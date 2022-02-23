---
layout: post
title:  "gh-pages workflow"
date:   2022-02-23-3:40
categories: blog testing
---

### Attempting to document the workflow for using a separate branch to track website progress

1. Start with an extablished repositroy you want to build a website for. Build a website locally (this is the hard part), in a subdirectory of the established repository; you do not want this to be tracked by the main repo, so adding it in .gitignore is probably wise.

2. Navigate to the folder where you built the website, and do a git init

3. git add everything in the _site/ directory and do a commit

4. Now, run the following command git subtree push --prefix _site/ origin gh-pages

5. The hope is that the command above will create a new branch called gh-pages on the existing repository.

6. Go to the repo settings and under 'pages' tell is to use the root directory of the branch gh-pages as the source

7. Whenever you edit the site and want to publish a new version, repeat steps 3 and 4

One issue with this method, is that you havent done version control on the actual directory you use to create the website-- but the generated site files themselves. Perhaps it is better to push the entire direcotry where you built the website and then tell github to use _site/ as the directory.


