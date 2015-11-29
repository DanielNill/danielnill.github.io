---
title: Building Firefox on Mac OSX
layout: page
---
I was recently setting up a local build of the mozilla firefox browser and ran into a few issues that were not entirely straight forward to resolve, so I though I would run through them and how to resolve them for people who run into them in the future.

### The Definitive Guide
You will fine way more information by following the instructions at [https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Build_Instructions].  What follows is simply my documentation of issues I ran into during the process.

### Running the Environment Configuration Script
I ran into a few minor issues running the configuration script found (here)[https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Build_Instructions/Mac_OS_X_Prerequisites] mainly having to do with libraries not properly installing via homebrew.

It is worth it to run brew doctor and resolve any issues that are raised and additionally run brew update before running the script.  If you seen any `error` or `warning` tags thrown when running the python script it is worth reading through what those errors are as you will likely not be able to move forward without resolving them.

I had an error thrown installing and linking autoconf because I had a different version already installed via homebrew.  For my case this was resolved by running `brew install autoconf213` and then running `brew link --overwrite autoconf213` to link autoconf213 instead of the autoconf version I had installed.

After resolving this issue I was able to run the setup script successfuly.

### Cloning mozilla-central
At the time I was trying to get this set up, I was visiting my sister and so I was dealing with an excruciatingly slow internet connection.  As recommended by the site if you are concerned that it will take more that 10 minutes to clone the mozilla central repo, I would recommend downloading the repo via the mercurial bundle found (here)[https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Source_Code/Mercurial/Bundles] and following all instructions found on that page.

###  
