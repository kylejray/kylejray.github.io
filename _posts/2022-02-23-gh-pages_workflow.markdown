---
layout: post
title:  "gh-pages workflow"
date:   2022-02-23-3:40
categories: blog testing
---

### First attempt at using a separate branch to track website progress
**Note: dont actually use the first attempt; the second attempt is better**{: style="color: red; opacity: 0.80;" }
1. Start with an extablished repositroy you want to build a website for. Build a website locally  in a subdirectory of the established repository. You do not want this to be tracked by the main repo, so adding it in .gitignore is probably wise.
- it's easy to make something bare-bones but can be as involved as you want. [Sam's cheatsheet](https://kylejray.github.io/assets/images/sam_cheatsheet_jekyll.png)
 gives the basic commands for making a skeleton-website that you can use to figure out how jekyll works. here is an excerpt from it:

- ![Sam's cheatsheet](https://kylejray.github.io/assets/images/sam_cheatsheet_jekyll.png)
    thank you [Sam](https://samlikesphysics.github.io/)! the cheatsheet is super useful

2. Navigate to the folder where you built the website, and do a `git init`

3. git add everything in the _site/ directory and do a commit

4. Now, run the following command `git subtree push --prefix _site/ origin gh-pages`

5. The hope is that the command above will create a new branch called gh-pages on the existing repository.

6. Go to the repo settings and under 'pages' tell it to use the root directory of the branch `gh-pages` as the source

7. Whenever you edit the site and want to publish a new version, repeat steps 3 and 4

One issue with this method, is that you haven't done version control on the actual directory you use to create the website-- but the generated site files themselves. So I am going back to the drawing board to see if there is a better way to do this. 

***hours later*** 

The attempt above represents my misunderstanding of how good github pages is at dealing with jekyll, it looks like github pages is smart enough to build the website itself with jekyll. You do not need to upload the local _site/ directory at all. See below for a second attempt that I think is more along the lines of what was intended.

### A second attempt at doing the above:

1. Start with an extablished repositroy you want to build a website for. Build a website locally using [jekyll](https://jekyllrb.com/) (this is the hard, involved part) for it, the website can be in a subdirectory of the established repository (but doesnt need to be); you do not want this to be tracked by the main repo branch, so adding it in .gitignore is probably wise if in the same directory.

2. Navigate to the folder where you built the website, and initialize a new local repository. git add and commit everything in the website directory. You can put _site/ in a .gitignore. You do not need it. It will also double down on assets unecesarially so its actually bad to have it tracked. If you use [jekyll](https://jekyllrb.com/) to make the website, the ignore will be auto-generated for you.

3. Now, add your established remote github repo as the upstream repository, but on a branch called 'gh-pages' instead of 'main'.
    - `git remote add origin <URL>`
    - `git branch -M gh-pages`
    - `git push -u origin gh-pages`

4. Go to the repo settings and under 'pages' tell is to use the root directory of the branch 'gh-pages' as the source

5. Now you can just add/commit/push like keeping a normal directory up to date






