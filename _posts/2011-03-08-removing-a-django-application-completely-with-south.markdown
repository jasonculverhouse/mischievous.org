---
layout: post
title:  "Removing A Django Application Completely with South"
date:   2011-03-08 11:53:59 +0000
categories: python
---
Removing A Django Application Completely with South

Let's pretend that the application that you want remove contains the following model in myapp/models.py:
    class SomeModel(models.Model):
        data = models.TextField

Create the initial migration and apply it to the database:

<pre class="brush: python">
    ./manage.py schemamigration --initial myapp
    ./manage.py migrate
</pre>
    
To remove the Models edit myapp/models.py and remove all the model definitions

Create the deleting migration:
<pre class="brush: python">
    ./manage.py schemamigration myapp
</pre>

Edit myapp/migrations/0002_auto_del_somemodel.py to remove the related content types

<pre class="brush: python">
    from django.contrib.contenttypes.models import ContentType
    ...
    def forwards(self, orm):
        
        # Deleting model 'SomeModel'
        db.delete_table('myapp_somemodel')
        for content_type in ContentType.objects.filter(app_label='myapp'):
            content_type.delete()
</pre>

Migrate the App and remove the table, then fake a zero migration to clean out the south tables
<pre class="brush: python">
    ./manage.py migrate
    ./manage.py migrate myapp zero --fake
</pre>

Remove the app from your settings.py and it should now be fully gone....