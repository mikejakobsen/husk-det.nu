#Netrw

One of the first plugins every VIM user install is NerdTree to get the IDE feeling.

But VIM has a very solid file explorer already build in.

I'm currently trying to minimize the amount of plugins i'm using, and make my vim seems less bloated.


| lazy 	| mnemonic  	| open file explorer                             	|
|------	|-----------	|------------------------------------------------	|
| :e   	| :edit     	| at current working directory                   	|
| :sp. 	| :split .  	| in splitat current working directory           	|
| :vs. 	| :vsplit . 	| in vertical split at current working directory 	|
| :E   	| :Explore  	| at directory of current file                   	|
| :Se  	| :Sexplore 	| in split at directory of current file          	|
| :Vex 	| :Vexplore 	| in split at directory of current file          	|



| Command 	| Action                    	|
|---------	|---------------------------	|
| %       	| Create a new file         	|
| d       	| Create a new directory    	|
| R       	| Rename the file/directory 	|
| D       	| Delete the file/directore 	|

#### My settings

    " Netrw options
    let g:netrw_altv = 1
    let g:netrw_banner = 0
    let g:netrw_browse_split = 4
    let g:netrw_liststyle = 3
    let g:netrw_winsize = -28
    let g:netrw_bufsettings = 'noma nomod nu nobl nowrap ro rnu'  " --> I want line numbers on the netrw buffer
    nnoremap <silent> <leader>ne :Lexplore<cr>
    nnoremap <silent> <leader>ns :Sexplore<cr>

Source: http://husk-det.nu/Vim/Netrw.html

