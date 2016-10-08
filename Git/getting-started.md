# Getting Started

Use Homebrew (`http://brew.sh/`) to install Git. If you have Homebrew installed, install Git via

	$ brew install git

	## First-Time Git Setup ##

Now that you have Git on your system, you’ll want to do a few things to customize your Git environment. You should have to do these things only once; they’ll stick around between upgrades. You can also change them at any time by running through the commands again.

Git comes with a tool called `git config` that lets you get and set configuration variables that control all aspects of how Git looks and operates. These variables can be stored in three different places:

*`/etc/gitconfig`	 file: Contains values for every user on the system and all their repositories. If you pass the option` --system` to `git config`, it reads and writes from this file specifically.
*`~/.gitconfig` f	ile: Specific to your user. You can make Git read and write to this file specifically by passing the `--global` option.
*config file in the Git di	rectory (that is, `.git/config`) of whatever repository you’re currently using: Specific to that single repository. Each level overrides values in the previous level, so values in `.git/config` trump those in `/etc/gitconfig`.


## Define your identity


	$ git config --global user.name "Mike Jakobsen"
	$ git config --global user.email "mike@jakobsen.dk"
	
	$ git config --global core.editor VIM	
	$ git config --global merge.tool vimdiff
	
Check the setup

	$ git config --list	
