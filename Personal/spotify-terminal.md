### Control Spotify in the terminal


Install Shpotify (http://brewformulas.org/Shpotify)

	$ brew install shpotify
	
	
### The Aliases
	
	alias p="spotify play"
	alias pause="spotify pause"
	alias next="spotify next"
	alias prev="spotify prev"
	alias song="spotify status"
	alias repeat="spotify toggle repeat"
	
	alias spil="spotify play artist"
	alias playlist="spotify play list"
	
	
	
	
	
 
To play Lukas Graham
 
     spil Lukas Graham

To play the playlist Top 25
 
     playlist top 25
	
	
### Control the volume

	alias vol+="spotify vol up"
	alias vol-="spotify vol down"
	alias mute="osascript -e 'set volume output muted true'"
	alias vol="spotify vol"
	
	
	
All of my aliases (Gist)

(https://gist.github.com/mikejakobsen/463b25ba3107d080e529b011e36b616b)