# Vim Cheat Sheet
Just a list of the hard-to-remember but easy-to-love commands and shortcuts that I
find.

Plenty more useful Vim commands/tips [here](http://zzapper.co.uk/vimtips.html).

| Command            | What is happened                                |
|--------------------|-------------------------------------------------|
| `^w then ^w`       | Cycle through open splits                       |
| `cc`               | Delete line and enter insert mode               |
| `cw`               | Change word                                     |
| `^w then =`        | Make all splits equal in size                   |
| `gg=G`             | Reindent the file                               |
| `"+y`              | Copy to the system clipboard                    |
| `:%s/find/replace` | Global find and replace                         |
| `gd`               | Go to definition of the object under the cursor |
| `:e`               | Refresh the current buffer/file                 |
| `:e!`              | Same as above, but discards changes             |
| `ci"`              | Change content wrapped in quotes "              |
| `cit`              | Change text inside <tags>                       |
| `cst`              | Change surrounding <tag>                        |
| `p`                | Insert clipboard below current line             |
| `P`                | Insert clipboard above current line             |
| `gf`               | Open the file under the current cursor          |
| `*`                | Search for the current cursor in the buffer     |
| `ct_`              | Change until the next `_`                       |
| `c/foo<CR>`        | Change until the search term "foo"              |
| `^E`               | Scroll window down                              |
| `^Y`               | Scroll window up                                |
| `^F`               | Page down                                       |
| `^B`               | Page up                                         |


## Copy Paste

The default mapping is `cp`, and can be followed by any motion or text
object. For instance:

- `cpiw` => copy word into system clipboard
- `cpi'` => copy inside single quotes to system clipboard

In addition, `cP` is mapped to copy the current line directly.

The sequence `cv` is mapped to paste the content of system clipboard to the
next line.
