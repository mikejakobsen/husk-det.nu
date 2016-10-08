# ACK - The Silver Searcher

Mapped to `<Leader>a`

## Usage

    :Ack [options] {pattern} [{directories}]

Search recursively in `{directories}` (which defaults to the current directory)
for the `{pattern}`.

Files containing the search term will be listed in the quickfix window, along
with the line number of the occurrence, once for each occurrence. `<Enter>` on
a line in this window will open the file, and place the cursor on the matching
line.

Just like where you use `:grep`, `:grepadd`, `:lgrep`, and :`lgrepadd`, you can
use `:Ack`, `:AckAdd`, `:LAck`, and `:LAckAdd` respectively. (See `:help Ack`

For more ack help see [ack documentation](http://beyondgrep.com/documentation/).

[doc]: https://github.com/mileszs/ack.vim/blob/master/doc/ack.txt

### Keyboard Shortcuts

The quickfix results window is augmented with these convenience mappings:

    ?    a quick summary of these keys, repeat to close
    o    to open (same as Enter)
    O    to open and close the quickfix window
    go   to preview file, open but maintain focus on ack.vim results
    t    to open in new tab
    T    to open in new tab without moving to it
    h    to open in horizontal split
    H    to open in horizontal split, keeping focus on the results
    v    to open in vertical split
    gv   to open in vertical split, keeping focus on the results
    q    to close the quickfix window

# Append to search result

`:AckAdd [options] {pattern} [{directory}] :AckAdd`
	
	Appends the result to the previous search.

`:LAck [options] {pattern} [{directory}]                                *:LAck*`

    Just like |:Ack| but instead of the |quickfix| list, matches are placed in
    the current |location-list|.

`:AckWindow[!] [options] {pattern}                                 *:AckWindow*`

    Search all buffers visible in the screen (current tab page only) files for
    the {pattern}.

### Remember

#### I don't want to jump to the first result automatically. ####

I really hate that. When searching, my mindset is not focused on the first match.

Use `:Ack!`, with bang. If you want this behavior most of the time, you might
like an abbreviation or mapping in your personal config, something like these:

```vim
cnoreabbrev Ack Ack!
nnoremap <Leader>a :Ack<Space>

let g:ackprg = 'ag --nogroup --nocolor --column'

let g:ackhighlight = 1
```
