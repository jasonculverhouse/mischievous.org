---
layout: post
title:  "Importing my old site from MovableType"
date:   2024-09-06 22:53:09 +0000
categories: jekyll update
---

I imported my site to Github Pages and Jekyll.

The result is what you see here.   You can use this as a template to import a MoveableType blog to Github Pages.

I still have some raw html formatting to deal with.

I used ChatGPT to write some scripts to import my old MoveableType blog.

I had it write 2 scripts

The first extracts the posts from the `mt.db`.

```
import sqlite3
import os
import re
from datetime import datetime

# Path to your SQLite database and Jekyll _posts directory
db_path = "mt.db"
posts_dir = "_posts/"

# Ensure the _posts directory exists
if not os.path.exists(posts_dir):
    os.makedirs(posts_dir)

# Function to sanitize the title for a valid file name
def sanitize_title(title):
    # Replace invalid characters (such as /, \, and other non-alphanumeric characters) with a dash
    return re.sub(r'[^a-zA-Z0-9\-_]', '-', title.lower())

# Connect to SQLite and fetch post data
conn = sqlite3.connect(db_path)
cursor = conn.cursor()

# SQL query to fetch post details
cursor.execute("""
    SELECT 
        e.entry_title AS title,
        e.entry_text AS content,
        e.entry_created_on AS date,
        c.category_label AS category
    FROM mt_entry e
    LEFT JOIN mt_placement p ON e.entry_id = p.placement_entry_id
    LEFT JOIN mt_category c ON p.placement_category_id = c.category_id
    WHERE e.entry_status = 2; -- Status 2 means published
""")

posts = cursor.fetchall()

# Loop through each post and create a Jekyll post file
for post in posts:
    title, content, post_date, category = post
    
    # Convert date to datetime object
    post_date = datetime.strptime(post_date, "%Y-%m-%d %H:%M:%S")
    
    # Sanitize title for file name
    sanitized_title = sanitize_title(title)
    
    # Generate the file name for Jekyll post
    file_name = f"{post_date.strftime('%Y-%m-%d')}-{sanitized_title}.markdown"
    
    # Create front matter for Jekyll post
    front_matter = f"""---
layout: post
title:  "{title}"
date:   {post_date.strftime('%Y-%m-%d %H:%M:%S')} +0000
categories: {category if category else 'uncategorized'}
---
"""
    
    # Write the post content to a Markdown file
    with open(os.path.join(posts_dir, file_name), 'w') as f:
        f.write(front_matter)
        f.write(content)

print("Posts successfully generated.")
```

The second fixes up the image paths to work with Jekyll

```
import os
import re

# Path to your markdown posts
posts_dir = "_posts/"

# Patterns to find old image src and href paths
old_src_pattern = re.compile(r'http://www\.mischievous\.org/(assets_c|images)/([\w%\-\/]+\.jpg|[\w%\-\/]+\.png)')
old_href_pattern = re.compile(r'http://www\.mischievous\.org/images/([\w%\d\-_\.]+\.pdf)')

# New paths with Jekyll variables
new_src_path = "{{ site.url }}{{ site.baseurl }}/assets/images/"
new_href_path = "{{ site.url }}{{ site.baseurl }}/assets/images/"

# Loop through all markdown files
for post_file in os.listdir(posts_dir):
    if post_file.endswith(".markdown"):
        file_path = os.path.join(posts_dir, post_file)

        # Read file content
        with open(file_path, 'r') as file:
            content = file.read()

        # Replace old image src paths (both assets_c and images) with new ones using Jekyll variables
        updated_content = re.sub(old_src_pattern, new_src_path + r'\2', content)
        
        # Replace old href paths with new ones using Jekyll variables
        updated_content = re.sub(old_href_pattern, new_href_path + r'\1', updated_content)

        # Write the updated content back to the file
        with open(file_path, 'w') as file:
            file.write(updated_content)

print("Image src and href paths updated with Jekyll variables.")
```