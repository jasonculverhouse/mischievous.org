---
layout: post
title:  "Cascading and Coroutines"
date:   2009-06-01 22:42:58 +0000
categories: python
---
[Cascading](http://www.cascading.org/) looks quite interesting.   Here is a python program that does something similar to the [Technical Overview](http://www.cascading.org/documentation/overview.html) seen **`main`** in the python program.  
<pre class="brush: python">
    #!/usr/bin/env python
    # encoding: utf-8
    import sys
    
    def input(theFile, pipe):
        """
        pushes a file a line at a time to a coroutine pipe
        """
        for line in theFile:
            pipe.send(line)
        pipe.close()
    
    @coroutine
    def extract(expression, pipe, group = 0):
        """
        extract the group from a regex
        """
        import re
        r = re.compile(expression)
        while True:
            line = (yield)
            match = r.search(line)
            if match:
                pipe.send(match.group(0))
    
    @coroutine
    def sort(pipe):
        """
        sort the input on a pipe
        """
        import heapq
        heap = []
        try:
            while True:
                line = (yield)
                heapq.heappush(heap, line)
        except GeneratorExit:
            while heap:
                pipe.send(heapq.heappop(heap))
    
    @coroutine
    def group(groupPipe, pipe):
        """
        sends consectutive matching lines from pipe to groupPipe
        """
        cur = None
        g = None
        while True:
            line = (yield)
            if cur is None:
                g = groupPipe(pipe)
            elif cur != line:
                g.close()
                g = groupPipe(pipe)
            
            g.send(line)
            cur = line
    
    @coroutine
    def uniq(pipe):
        """
        implements uniq -c
        """
        lines = 0
        try:
            while True:
                line = (yield)
                lines += 1
        except GeneratorExit:
            pipe.send('%s\t%s' % (lines, line))
            
    @coroutine
    def output(theFile):
        while True:
            line = (yield)
            theFile.write(line + '\n')
            
    def main():
        input(sys.stdin,
            extract( r'^([^ ]+)',
                sort(
                    group( uniq,
                        output(sys.stdout)
                    )
                )
            )
        )
    
    if __name__ == '__main__':
        main()
</pre>

You can achieve the same results with the unix command line:

    cat  access.log | cut -d ' ' -f 1 | sort | uniq -c