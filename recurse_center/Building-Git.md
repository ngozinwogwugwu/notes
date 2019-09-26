# Building Git
Joe wrote [zit](https://github.com/jmou/zit), or _zero git_, to illustrate how simple git really is. Here are my (annotated) notes to [his presentations](https://jmou.github.io/rc-git-storytime/#p1)

##  Time to look at some source code
- step 1: make an init file, make it executable, execute it `chmod +x init`
- step 2: make a zit file, make it executable
- step 3: make a hash-object file

blobs use the hash of the uncompressed file to get their names

## A Brief history of Version Control and Git
First Generation of Version Control
- You can use `diff` to represent the differences between two files in a very compact manner
- RCS: you can use this `diff` method to make a version control system

Second Generation of Version Control
- CVS: concurrent version system. Introduced the concept of a "change set". Downside: this was centralized. They included **network support** in 1995 (because the internet)
- SourceForge (1999) - built around the CVS ecosystem. First generation of open source stuff
- SubVersion (2004) - CVS done right

- Bitkeeper (predecessor to git, extremely fast) proprietary. Got into a fight with linux, so linux made their own thing
- Git (2009)

## Quick Notes
- git is a polyglot thing. It's written in multiple languages (bash, c, perl)
- git-hydra: git visualization tool
- git hashes are deterministic
- to display your index file: `hexdump -C .zit/index`
