# The Basic Shit

If you’re starting to track an existing project in Git, you need to go to the project’s directory and type

	$ git init
	

If you want to start version-controlling existing files (as opposed to an empty directory), you should probably begin tracking those files and do an initial commit. You can accomplish that with a few `git add` commands that specify the files you want to track, followed by a commit:

	$ git add *.c
	$ git add README
	$ git commit -m 'initial project version'