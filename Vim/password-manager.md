# Password manager

In my pursuit of a secure computer, i'm hugh fan of 1Password for Mac. 


Remember, Vim has an option that enables the encryption of plain text files. But its default encryption mode pkzip is not that secure and can be easily bruteforced. So the first thing you need to do is to set the crypto algorithm to something more secure. Add this to your .vimrc :

	set cm=blowfish
	

So, now create a text file, say .password and open it with Vim. Store your usernames, passwords, sites as tuples in the text file. To set a password type :X , Vim will prompt you for a passphrase, once you enter one and save the file. An encrypted version of the text file will be stored on disk. Every subsequent time you open the file, it will ask you for the pass phrase and then decrypt the file, but it will always save the encrypted version. If you need to change the passphrase, type :X

Then store it on Dropbox, or a host of your choice.