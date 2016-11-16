# Vim anti-patterns

One of the main reasons why VIM has been so popular through the years, is the amazing enhancement it levers to the users workflow, when he finally masters the editor to it's full potential.

For a developer, nearly everytime you reach for your mouse, that's an anti-pattern. Nearly every time, you take your hands off the home row, that's an anti pattern. It might only take a couple of second, but if you continue to do so through out the day, those seconds amounts to a lot off time. Here i collected, some of the best tips to enhance your VIM workflow. Some of them might be basic, but there all worth being mentioned.

This text is based on these two links.

Read: https://sanctum.geek.nz/arabesque/vim-anti-patterns/


[Moving](http://www.benorenstein.com/blog/jumping-long-distances-in-vim)

 I find that the two most useful are moving by paragraph and by screenful, but this depends on how far and how precisely you have to move.

```

{ — Move to start of previous paragraph or code block.
} — Move to end of next paragraph or code block.
Ctrl+F — Move forward one screenful.
Ctrl+B — Move backward one screenful.

nG - jump to line n
L - jump to the bottom of the screen ("low")
H - jump to the top of the screen ("high")
M - jump to the middle of the screen ("middle")
C-o - jump back
C-i - jump forward
zt - move this line to the top of the screen ("top")
zb - move this line to the bottom of the screen ("bottom")
zz - move this line to the middle of the screen ("ziddle?")

q: — Open with a command history from normal mode
q/ — Open with a search history from normal mode (to search forward)
q? — Open with a search history from normal mode (to search backward)
Ctrl+F — Open with a command history from command mode
Ctrl+w - Cycle open splits
Ctrl+w + = -Make splits equal size
gf - Go to definition

```

If you happen to know precisely where you want to go, navigating by searching is the way to go, searching forward with `/` and backward with `?`.

’s always useful to jump back to where you were, as well, which is easily enough done with two backticks, or gi to go to the last place you inserted text. If you like, you can even go back and forth through your entire change list of positions with g; and g,

gi - last insert


## Navigated the change list

g; - g,

## Commands and search

```
y/search<Enter>
y?search<Enter>
d/search<Enter>
d?search<Enter>
c/search<Enter>
c?search<Enter>
```

## Word under the cursor

```
* find forward
# find backward
```

## Deleting, then inserting. That's like 90% of the 21:07

Append i to the command mentally.

d2wi

Repeating substitutions

Just type `&` to repeat the last substitution on the current line. You can repeat it on all lines by typing `g&`.

## Current file name

`%` is always the current bufer
To echo it `"%P`

## Start with splits

-O — Open all buffers in vertically split windows, like :vsp
-o — Open all buffers in horizontally split windows, like :sp

## Format buffer

:%!column -t

:%!sort -k1

## Insert filename

Import etx

Ctrl+x Ctrl+f

Add `/` for absolute path

## Working with files


* `gof`: **Go** to the current file's directory in **Finder** 
    * `goF` (uppercase `F`) opens the current *working directory* (`:pwd`)
* `got`: **Go** to the current file's directory in a **Tmux Split**
    * `goT` (uppercase `T`) opens the current *working directory* (`:pwd`)

## Read More

Wanna read more?

http://vimuniversity.com/samples/jumping-long-distances
