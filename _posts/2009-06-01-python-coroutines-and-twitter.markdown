---
layout: post
title:  "Python Coroutines and Twitter"
date:   2009-06-01 16:04:57 +0000
categories: python
---
Reading [http://www.dabeaz.com/coroutines/](http://www.dabeaz.com/coroutines/) and thought this was a natural for a twitter client. Here is a pretty simple version that just prints the public timeline every 60 seconds.  Next, up removing the time.sleep and scheduling the followStatus function as a task so I can follow more than one stream at a time.
<pre class="brush: python">
    #!/usr/bin/env python
    # encoding: utf-8
    import time
    import twitter
    
    def coroutine(func):
        """
        A decorator function that takes care of starting a coroutine
        automatically on call.
    
        see: http://www.dabeaz.com/coroutines/
        """
        def start(*args,**kwargs):
            cr = func(*args,**kwargs)
            cr.next()
            return cr
        return start
    
    @coroutine
    def statusPrinter():
        """
        Just prints twitter status messages to the screen
        """
        while True:
             status = (yield)
             print status.id, status.user.name, status.text
    
    def followStatus(twitterGetter, target, timeout = 60):
        """
        Follows a twitter status message that takes a since_id
        """
        since_id = None
        while True:
            statuses = twitterGetter(since_id=since_id)
            if statuses:
                # pretty sure these are always in order
                since_id = statuses[0]
                for status in statuses:
                    target.send(status)
            # twitter caches for 60 seconds anyway
            time.sleep(timeout)
    
    def main():
        api = twitter.Api()
        followStatus(api.GetPublicTimeline, statusPrinter())
    
    if __name__ == '__main__':
        main()
 </pre>   