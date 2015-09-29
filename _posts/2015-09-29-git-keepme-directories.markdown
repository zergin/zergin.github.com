---
layout: post
title:  "GIT - keep empty directories"
date:   2015-09-28 22:25:10
categories: devops git
---

# Keeping empty directories in GIT

Many projects use empty ```.keepme``` files in directories that are empty but need to be commited. Just as a quick note I propose another way; one that has been working for me very well for couple years. Use .gitignore files ignoring everything but itself.

{% gist e6e64f83c058cd0edfe5 .gitignore %}

This way:

  * directory is not empty so git will add it to it's index
  * directory will be kept empty of any other then ```.gitignore``` files even if some are added
  * ```.gitignore``` will be added and updated when it changes due to exception for itself

