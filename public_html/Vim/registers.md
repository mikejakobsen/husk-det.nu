#Vim Registers

To record macro `a` -> `qa` -> to stop -> `q;`

Run macro `@a`

Similarly, you can store text from the buffer itself rather than commands in these registers, by prepending `"a` to any command which uses a register, such as the c, d, and y commands

"ayy — Read current line into register a.
"bP — Paste contents of register b above current line.
"cc3w — Change three words, putting the previous three words into register c.

### Append to clipboard

Use capital letters, when defining the register.

"ayy — Replace the contents of register a with the current line.
"Ayy — Append the current line to register a.


:registers to show all registers


### Numbered registers

The read-only registers 0 through 9 are your “historical record” registers. The register 0 will always contain the most recently yanked text, but never deleted text; this is handy for performing a yank operation, at least one delete operation, and then pasting the text originally yanked with "0p.

### Smal deletes

`"-`

The last small delete - usually one Character.

Paste: `"-P` 

### Last inserted

The read-only register ". contains the text that you last inserted. Don’t make the mistake of using this to repeat an insert operation, though; just tap . for that after you leave insert mode, or have the foresight to prepend a number to your insert operation; for example, 6i.


