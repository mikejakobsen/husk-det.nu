
# Cheat Sheet

* Text Objects
	* Say you have `def (arg1, arg2, arg3)`, where your cursor is somewhere in the middle of the parenthesis.
    * `di(` deletes everything between the parenthesis. That says "change everything inside the nearest parenthesis". Without text objects, you would need to do `T(dt)`.
	* Learn more [here](http://blog.carbonfive.com/2011/10/17/vim-text-objects-the-definitive-guide/)
* automatic marks
    * [http://vim.wikia.com/wiki/Using\_marks](http://vim.wikia.com/wiki/Using_marks) under "special marks"
* `%` - jumps between matching `()` or `{}`
* [jump list](http://vim.wikia.com/wiki/Jumping_to_previously_visited_locations)
	* `ctrl+o` - go backwards in the jump list
	* `ctrl+i` - go forwards in the jump list
* [change list](http://vim.wikia.com/wiki/Jumping_to_previously_visited_locations)
	* `g;` - go backwards in the change list
    * `g,` - go forwards in the change list
* `Ctrl+r + 0` in insert mode inserts the last yanked text (or in command mode)
* `gv` - reselect (select last selected block of text, from visual mode)

##Regex and Searching
* Searching in vim is very unintuitive. There are 4 modes of searching, ranging from "very magic" to "very no magic". They determine what needs to be escaped with a `\` in your search term. If you just want "regex on" or "regex off" then always search in Very Magic or Very No Magic mode.
* Check out the details by running `:h \\v`.

### Pearl style regexes (Very Magic Mode)

Start a search with `\v`. Everything else can act like a normal regex search, except you have to escape `/` and `\`. If you are searching backwards, you’ll also have to escape `?`.

* E.g. search for a url starting with `www` and ending with `.com`: `\vwww\..*\.com`

#### Differences from Pearl Regex
The `<` and `>` characters are special for start and end of word. Escape them to search for them literally.

###Literal Search (Very No Magic Mode)
Start a search with `\V`. Now you *only have to escape* `/` and `\`. It would be nice if you didn't have to escape *anything*, but alas vim is not like this.

###Advanced: Match a subsection of your search
Let's say you have this file

```
www.yahoo.com
blah some other stuff
www.google.com
www.ebay.org
```
And you want to change it to

```
www.chase.com
blah some other stuff
www.chase.com
www.chase.org
```
What you're doing is saying *find all domain names and change the inner part to `chase`*. You can do this by specifying the part of the search to *match*.
Put `\zs` before where you want to match and `\ze` after where you want to match.

So in this case: `\v` `www\.` **\zs** `.*` **\ze** `\.com`

* An extension to this is looking for the start of a word without matching it. For example looking for the variable `i`. We could then use this search: `\v\W\zsi\ze\W`. Note: Use `\W` matches everything except `[a-zA-Z0-9_]`
	* There’s a shortcut called word boundaries, with `<` and `>`:
		* `\v<i>` ([More info](http://vim.wikia.com/wiki/Search_patterns))
        * You can get this same behavior by pressing `*` in normal mode, while having your cursor over the word you want to search.

### Faster Search and Replace
* Vim 7.4 has an [amazing "gn" command](http://vimcasts.org/episodes/operating-on-search-matches-using-gn/) that allows you to search and replace faster. `gn` means "select the next search term".


# Vim Cheat Sheet

My collection of vim tips to make the best editor even better. This is by no means complete or a tutorial on how to use vim, but a set of commands I don't want to forget and need to write them down before they burn into memory.

See the resources section below for a more complete introduction and a set of in-depth tutorials.

### Navigation

    :nn            " Jump to line nn
    nn|            " Jump to column nn


#### Navigation Marks

    ma             " mark spot label it a
    'a             " jump to spot
    ''             " jump to last spot you were
    :marks         " show all marks


To create marks across files, use capital letters. I'll use this when working on an HTML View, CSS and Javascript. Mark the three spots H, C, J and easy to jump back and forth.

#### Copy and Paste Registers

Vim has a clipboard history stored in registers, you can also use these registers to cut and paste items to. Your past history of copies is also stored in these registers, use list regiter to find something you thought might be gone since not in clipboard

    "ad            " cut something to register a
    "ap            " paste something from register a
    :reg           " list registers


#### Deleting Lines

    S              " delete line and insert mode at start
    :g/regexp/d    " delete all lines that match regexp
    :v/regexp/d    " delete all lines that do NOT regexp
    :v/w{3,}/d   " delete all lines with less than 3-chars
    :15,20d        " delete lines 10-20


#### Buffer Management

    :ls             " list open buffers
    :b [num|name]   " switch to buffer
    :b#             " switch to last buffer
    :bdel #         " delete buffer


#### Record Macro

    qa              " start recording macro in buffer a
    [do stuff]
    q               " end recording


#### Playback Macro

    @a
    50@a  (50 times)


#### Map System Command to Key Stroke

Map ctrl-j d to run system command /tmp/x.py

    :imap <C-j>d <C-r>=system('/tmp/x.py')<CR>



#### Customizations

Here are a set of short cuts I have in my vimrc file that simplify some common operations. If I notice myself doing the same thing over and over, I try to add a shortcut when possible.

    " Add spaces inside parentheses, WordPress coding style
    map <Leader>o ci(hp

    " Surround word with quote
    map <Leader>' ysiw'
    map <Leader>" ysiw"

    " Add Trailing Semi-colon
    map <Leader>; g_a;<Esc>

    " Use :w!! to save with sudo
    ca w!! w !sudo tee >/dev/null "%"
