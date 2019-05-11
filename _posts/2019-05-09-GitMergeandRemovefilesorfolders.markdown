---
layout: post
title:  "Git Excluding files or folders from Merging"
date:   2019-05-09 21:38:00 -0400
categories: Git
---

In a large git project with multiple branches, you may have several files or folders that are not ready to merge.  Follow these steps to accomplish this:

1. from the branch you are mergin into

`
git merge --no-commit --no-ff branch_with_the_files_you_dont_want
`

2. run this command for each file or folder you want to remove

`
git reset HEAD -- path/to/yourfile
`

3.  once all have been removed run this to clean up

`git clean -fd`

4.  Then commit

`git commit -m "your message"`

For more information see https://github.com/RWTH-EBC/AixLib/wiki/How-to:-Exclude-files-or-folder-from-merge

That is all.

Enjoy

Jeff