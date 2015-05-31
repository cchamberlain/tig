tig (WIP)
=========

A CLI abstraction layer to git with strong integration to dev-ops platforms.

```sh
npm install -g tig
```

*Disclaimer: tig is a WIP.  Much of the tig functionality has been prototyped to varying levels, but it is still being very actively developed and not everything in this document is "done".  If you are interested in contributing to the goals of the platform, please shoot me an email at cole.chamberlain@gmail.com.*


background
----------

git has changed the way software development works.  Developers use many libraries, often on multiple computers and with all this code getting synced around, things can get messy.

tig is an extension to git and other web platforms that allows a developer to sync code to their local computers in a controlled fashion.  With tig, you, the developer, define what libraries you are interested in with a "track" function.  Once a library is tracked it can be orchestrated alongside your other git libraries via tig commands.

npm has shown how smart REST APIs can change the way we work with, package, and deploy code.  The days of tracking your local settings for each of your dev tools in rc files are dying.  tig has this covered with the ability to sync all of your "dotfiles".  This includes settings for git, GitHub, npm, AWS, and travis-ci and you can add your own.


roadmap
-------

tig attempts to make some smart assumptions to allow users to keep typing to a minimum.  The tig APIs use OAuth2 openid JWT bearer tokens to track who you are.  tig assumes that you are using GitHub for your source code hosting and npmjs.com for your npm hosting.  Once authorized with the tig CLI you will be able to access tig APIs in reference to your account without referencing your username in the URL.  As well when working in the command line, if you track a new repo, you will not need to reference the full GitHub url as you would in a normal clone.

URLs are case-insensitive.  Most operating systems are not.  Therefore, tig normalizes all data to lowercase unless otherwise expressed.  All tig commands and URLs are lowercase with hyphen word separators.


###tig.link

* User API: https://tig.link[/*username*|*organization*]

* Repository API: https://tig.link[/*username*|*organization*]/*repo-name*


###tig.sh

* Shell API: https://tig.sh[/*username*|*organization*]/*script-name*
** Store your scripts for setup and automation of various platforms and allow ease of execution.
** Operate as standalone or integrate with Gist / AWS S3

* Dot API: https://tig.sh[/*username*|*organization*]/./*dot-file-name*
** Store your dot files privately.  If you feel like it, make them public and share with others.

___

commands
--------

###tig install [-o]

tig has some dependencies.  They are very useful command line files and give tig a baseline for cross-platform scripting.  tig can also install optional portable applications that are extremely helpful in development.  If the application is already installed on your machine, it will be skipped.

* hard dependencies
** rimraf: small package for "rm -rf" in cross-platform context
** mkdirp: like rimraf, but "mkdir -p"
** google chrome: you should already have this but if you don't, it will be installed

* optional
** nodemon: run nodejs in a development fashion and reload as files change
** forever: run nodejs in production and it will be auto restarted if something happens
** gulp: nodejs build tool
** ack: development tool for quickly scanning files for specific words or regex
** ag: like ack, but faster
** (Windows-only) procexp/procmon: Tools from SysInternals for analyzing Windows system processes


###tig login [-n] [-a] [-t] *username*

tig is integrated with GitHub and your GitHub username / password has already been reserved for you on the tig network.  Support for other providers is in progress.  If you belong to multiple organization accounts, tig can handle that too.

tig is built to handle one or more npm, aws, and travis-ci accounts that you may use.


###tig config

configure your tig settings here.


###tig track [-g] [-s] [-d] [[username|organization/]repo|script|dot-file]

Adds something to the tig index that will be tracked on your tig account.  By default, tig assumes you want to track a GitHub repo but flags can be specified to track scripts and dot files.

* -g: the object should be meshed to all computers that you authenticate with via tig (defaults to just the computer you are on)
* -s: the object being tracked is a script file
* -d: the object being tracked is a dot file


###tig mesh [-s] [-r repo [-v version]]

Synchronizes everything you've tracked locally (or globally on other machines) to your machine.  By default, repositories will be synced down from the master branch.  This functionality can be overridden in your tig configuration settings.

* -s: mesh the target repo(s) to your sync folder instead of src folder
* -r: mesh a specific repository and optional version (annotated or hash)


###tig status [repo] [-v]

Gets the status of all repositories that have been synced to your local machine via the `tig mesh` command.


###tig publish [-n] [-p] [-m] [-M] [-b branch] [-R remote] repo-name "Commit and version message"

Git adds, commits, and pushes a repo to origin.  Flags can be provided to do a semver style annotated tag as well as publish the new package on npm.  You may also override the branch name and remote name that are being pushed to.

* -n: Publish on npm
* -p: Do a semver patch version bump
* -m: Do a semver minor version bump
* -M: Do a semver major version bump
* -b: Override the branch you want to push to on the remote
* -R: Push to a non-origin remote


###tig build [-v version] [repo]

Builds your code locally using the sync and build folders

* -v: build a specific version of the repository


###tig run [-s] [repo|script]

Build and execute one of your tracked repositories or scripts on the local machine.

* -s: The object to execute is a tracked script


###tig deploy [-a] [-f] [-u] [repo]

Builds code artifacts locally and optionally pushes them to FTP, UNC, or cloud service.

* -a: AWS deployment
* -f: FTP deployment
* -u: UNC deployment

