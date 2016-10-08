# Working with Git in Vim

http://media.vimcasts.org/videos/32/fugitive_2.m4v

### Git status

Mapped to **leader+gs**

The :Gstatus command opens a status window. The contents closely resemble the output from running git status in the shell, but fugitive makes the window interactive. You can jump directly between files with ctrl-n and ctrl-p.

	| Command | Effect                                |
	|---------|---------------------------------------|
	| -       | Add/reset file                        |
	| Enter   | Open current file                     |
	| p       | run `git add –patch` for current file |
	| c       | invoke :Gcommit                       |

### Git index

The git index is where you put changes that you want to be included in the next commit.

To see the **Git saved** version of curent file

	leader+ge


### Git diff

To see changes to current file, compared to Gits version.

	leader+gd


### Mappings

		<leader>dg :diffget<CR>
		<leader>dp :diffput<CR>

		Git add current file

		<leader>ga :Git add %:p<CR><CR>
		<leader>gs :Gstatus<CR>
		<leader>gc :Gcommit -v -q<CR>
		<leader>gd :Gdiff<CR>
		<leader>ge :Gedit :%<CR>

