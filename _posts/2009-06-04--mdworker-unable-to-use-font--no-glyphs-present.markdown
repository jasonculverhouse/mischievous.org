---
layout: post
title:  " mdworker Unable to use font: no glyphs present"
date:   2009-06-04 14:15:33 +0000
categories: bugs
---
Running Apple Mac OSX, and your system.log is filling up with

mdworker[473]: Unable to use font: no glyphs present.

/System/Library/Frameworks/ApplicationServices.framework /Frameworks/ATS.framework/Support/ATSServer[474]: Serious problems were found in font data while activating it.

/System/Library/Frameworks/ApplicationServices.framework /Frameworks/ATS.framework/Support/ATSServer[474]: You may encounter drawing or printing problems.


Well, it could be Spotlight trying to index a bad PDF file.  To find the offending file with use lsof and the process id of the mdworker process

<pre>
  lsof -p 473
</pre>

In my case it was a PDF from the hadoop 20.0 release