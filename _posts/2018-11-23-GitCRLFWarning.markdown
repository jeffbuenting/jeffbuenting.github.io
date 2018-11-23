---
layout: post
title:  "LF Will be replaced by CRLF Warning"
date:   2018-11-23 08:38:00 -0400
categories: Git
---

I always forget this so I thought I would write a blog to help me remember.  If nothing else, it will be a reference for me.

When committing text files (usually powershell scripts for me) to git,  the default settings throw a warning about LF will be replaced by CRLF. The reason behind this is to allow developement of a project across platforms that use different line-ending styles without commits being very noisy.  By forcing the line-ending to consistently be LF, this prevents each editor from needing to change the endings.

Git has three modes on treating line-endings.  You can check the currently set mode using the following:

```git
git config core.autocrlf
```

   True = anytime git thinks you are committing a text file, it will convert the CRLF to LF.  And then when you check out the file, it will convert back from LF to CRLF.  The downside to this, is if a text file is authored using LF, it will be saved with LF when committed, but converted to CRLF when checked out again.

   False = no line-ending conversion is perfromed.

   Input = see <http://kernel.org/pub/software/scm/git/docs/git-config.html> for more information.


To disable the warning but not the actual conversion, use the following:

```git
git config --global core.safecrlf false
```

Enjoy 

Jeff