---
layout: post
title:  "gh-pages workflow"
date:   2022-02-23-3:40
categories: blog testing
---

### First attempt at using a separate branch to track website progress

1. Start with an extablished repositroy you want to build a website for. Build a website locally  in a subdirectory of the established repository. You do not want this to be tracked by the main repo, so adding it in .gitignore is probably wise.
- it's easy to make something bare-bones but can be as involved as you want*. Sam's cheatsheet gives the basic commands for making a skeleton-website that you can use to figure out how jekyll works:
![Sam's cheatsheet](../assets/images/sam_cheatsheet_jekyll.png)

2. Navigate to the folder where you built the website, and do a `git init`

3. git add everything in the _site/ directory and do a commit

4. Now, run the following command `git subtree push --prefix _site/ origin gh-pages`

5. The hope is that the command above will create a new branch called gh-pages on the existing repository.

6. Go to the repo settings and under 'pages' tell is to use the root directory of the branch gh-pages as the source

7. Whenever you edit the site and want to publish a new version, repeat steps 3 and 4

One issue with this method, is that you haven't done version control on the actual directory you use to create the website-- but the generated site files themselves. So I am going back to the drawing board to see if there is a better way to do this. \\

The attempt above represents my misunderstanding how good github pages is at dealing with jekyll, it looks like github pages is smart enough to build the website itself with jekyll. You do not need to upload the _site/ directory at all. See below for a second attempt that I think works better.

### A second attempt at doing the above:

1. Start with an extablished repositroy you want to build a website for. Build a website locally (this is the hard part), in a subdirectory of the established repository; you do not want this to be tracked by the main repo, so adding it in .gitignore is probably wise.

2. Navigate to the folder where you built the website, and do a `git init`

3. `git checkout -b gh-pages`

4. git add anything and everything in the website directory that you need to build the website, but you can put _site/ in a .gitignore. You do not need it. It will also double down on assets unecesarially so its actual bad to have it tracked.

5. commit and do  `git push -u origin gh-pages`

6. Go to the repo settings and under 'pages' tell is to use the root directory of the branch gh-pages as the source

7. Now you can just add and push like keeping a normal directory up to date






