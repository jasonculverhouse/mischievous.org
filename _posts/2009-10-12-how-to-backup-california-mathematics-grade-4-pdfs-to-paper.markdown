---
layout: post
title:  "How to Backup California Mathematics Grade 4 PDFs to Paper"
date:   2009-10-12 21:05:29 +0000
categories: education
---
My school sent home a set of 3 CD's with circa Glencoe StudentWorks software from 2007.
This software consists of a flash application that launches Adobe Acrobat version 7 to display
PDF the PDF content of the California Mathematics Grade 4 workbooks.  Children are 
expected to complete homework assignments using this software.  My primary operating system
is Mac OS X v10.6 Snow Leopard, the application "Student Works OSX" is a PowerPC application 
and would require Rosetta to be installed to run.

Thankfully, the PDFs are on the CD 
and you do not need to run the application to see the content, just navigate to 
**/Volumes/CA Math 4/support/PDF/docs** and you will find the PDF for each individual chapter.  
On Mac or Windows, there is no need to install or run any application from the disc, If you
are running Windows, download the latest Acrobat, don't install the antiquated version on the disc.

Printing is another matter, you can't.  The chapters are password print protected. I'm not
a lawyer but I read the standard shrink wrap licensing agreement that came with the software
and here is what I found:

> COPIES. Copies can be made only as authorized above
> in machine readable form. Print copies of Software code
> are not authorized. All copyright and trademark notices
> must remain on all copies. All copies must be faithful
> reproductions. You are solely responsible for the content,
> quality and operation of all Software copies. Certain
> Software programs may be "copy protected" by special
> encryption coding that prevents copying or 
> printing-out content

And

> You may also make one (1) back- up copy of the Software
> for archival purpose

Great, I choose to backup the PDF portions of the "software" by printing on
paper.  Here is my "backup" program, you just need GhostScript from macports.

<pre class="brush: python">

    #!/bin/bash -x
    for i in "$@"; do
        NAME=`basename "$i"`
        gs -q -dNOPAUSE -dBATCH -sDEVICE=pdfwrite -sOutputFile="$NAME" -c .setpdfwrite -f "$i"
        lpd -d "SCX_4500" "$NAME"
        rm $NAME
    done
    
</pre>