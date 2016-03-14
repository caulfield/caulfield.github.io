---
layout: post
title: "Git message templates"
date: 2014-07-17 21:50:12
tags: git
---

When you work in big company with a lot of developers and long-term projects it's useful to follow conventions in any reports. And of course SCM message is a kind of reports. Projects in my company grow fast. Developers spend a lot of time for merging, reviewing, reverting, etc..

All tasks are created in tasks-tracking software. They, of course, have identical number and link. And we must add this reference information to commit messages and issue number in branch name.

* *git co feature-666-add-comments-to-posts*
* *git log ..master*
* [PRJCTABBRV-666] Create comment model
* [PRJCTABBRV-666] Create routes with controllers for comments


I hate typos. (I sure, [they][thingsfitin] hate too)
I don't like to double-check my messages. And I do rename and force-push very fast.

I found a good solution and I think it can be useful for you if you use git too.

> The prepare-commit-msg hook is run before the commit message editor is fired up but after the default message is created. It lets you edit the default message before the commit author sees it. This hook takes a few options: the path to the file that holds the commit message so far, the type of commit, and the commit SHA-1 if this is an amended commit. This hook generally isn’t useful for normal commits; rather, it’s good for commits where the default message is auto-generated, such as templated commit messages, merge commits, squashed commits, and amended commits. You may use it in conjunction with a commit template to programmatically insert information.
[Source][githelp]

Checkout my hook(for rules above).  This snippet extracts number from branch name following the format. E.g. - problem-12-add-missing-i18n and add your abbreviation with the number as a default message. Abbreviation will added even in merge commits. But I hope it's not a big deal to delete one line. Especially in vim, I recommend you to use it.

<script src="https://gist.github.com/caulfield/2f7fd952fa7ee48e3256.js"></script>

Just put this file into *.git/hooks* as *prepare-commit-msg* and make it executable.
Thanks for reading.

[thingsfitin]: http://thingsfittingperfectlyintothings.tumblr.com/
[githelp]: http://git-scm.com/book/en/Customizing-Git-Git-Hooks
