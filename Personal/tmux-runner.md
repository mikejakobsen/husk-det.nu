# VTR [Vim Tmux Runner]


Watch [Vim London Video](https://vimeo.com/126420226)

VTR is a great way to work with Tmux panes from within Vim.

VTR provides a collection of commands and functions that allow Vim to interact with tmux. The primary command is VtrSendCommandToRunner. (Leader+sc) This allows for any command string to be passed to tmux for execution. The command VtrSendLinesToRunner allow for either the current visually selected region or the current line to be sent to the tmux runner
pane for execution.

It's like a Shell below the command-line.

*<leader+sc> Send command is extemely useful.*

## Learn

Mapping      |   Think
-----------------------------
<leader>rr   |   ResizeRunner
<leader>ror  |   ReorientRunner
<leader>sc   |   SendCommand
<leader>sl   |   SendLines
<leader>or   |   OpenRunner
<leader>kr   |   KillRunner
<leader>fr   |   FocusRunner
<leader>dr   |   DetachRunner
<leader>ar   |   attachRunner
<leader>cr   |   ClearRunner
<leader>fc   |   FlushCommand

------------------------------------------------------------------------------

## Mappings 

Add to your Vimrc

  let g:VtrUseVtrMaps = 1

The following normal mode maps are provided when g:VtrUseVtrMaps is set to 1:

        Mapping      |   Command
        -----------------------------
        <leader>rr   |   VtrResizeRunner<cr>
        <leader>ror  |   VtrReorientRunner<cr>
        <leader>sc   |   VtrSendCommandToRunner<cr>
        <leader>sl   |   VtrSendLinesToRunner<cr>
        <leader>or   |   VtrOpenRunner<cr>
        <leader>kr   |   VtrKillRunner<cr>
        <leader>fr   |   VtrFocusRunner<cr>
        <leader>dr   |   VtrDetachRunner<cr>
        <leader>ar   |   VtrReattachRunner<cr>
        <leader>cr   |   VtrClearRunner<cr>
        <leader>fc   |   VtrFlushCommand<cr>

Visual


        Mapping      |   Command
        -----------------------------
        <leader>sl   |   VtrSendLinesToRunner<cr>


