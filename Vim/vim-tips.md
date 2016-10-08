#Personal Vim Tips

### Delete in insert mode

To delete a character, word or line while in Insert mode, Vim Command Line mode or Shell Command Line  we have the following shortcuts available:

	<C-h> " Backspace
	<C-w> " Delete back one word
	<C-u> " Delete back to start of line
	<C-k> " Delete forward to end of line

### Sessions

Everyone probably already knows, this, but you can do

      :mks ~/mysession.vim
      
      Then to open session
      
      vim -S ~/mysession.vim
    
When opening vim to load all your tabs, files and windows back just how they were.

### Sort properties in CSS

If we want to order the lines in the CSS selector alphabetically, we just select the lines and type :sort while in visual mode and then we have them sorted.

	h4 {
		font-size: 11px;
		margin: 15px;
		background: red;
		}

### Vim Easymotion
Configure Vim Easymotion to use **space** as trigger. [Vim Easymotion](https://github.com/easymotion/vim-easymotion)
Typing space and then typing, seems natural.

	let g:EasyMotion_leader_key = '<space>'

### Vim and system clipboard
Copy text to system clipboard. Maped to **leader + y**

	function! CopyText()
		normal gv"+y
		:call system('xsel -ib', getreg('+'))
	endfunction
	vmap <leader>y :call CopyText()<cr>

### Rename current file

Rename current file. Maped to **leader + rf**

	function! RenameFile()
	  let old_name = expand('%')
	  let new_name = input('New file name: ', expand('%'), 'file')
	  if new_name != '' && new_name != old_name
	    exec ':saveas ' . new_name
	    exec ':silent !rm ' . old_name
	    redraw!
	  endif
	endfunction
	map <leader>rf :call RenameFile()<cr>
