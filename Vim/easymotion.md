# EasyMotion

Mappings

    Mapping              | Details
    ---------------------|----------------------------------------------
    f				     | Find {char} to the right. See |f|.
    F				     | Find {char} to the left. See |F|.
    t					 | Till before the {char} to the right. See |t|.
    T					 | Till after the {char} to the left. See |T|.
    <Leader>w            | Beginning of word forward. See |w|.
    <Leader>W            | Beginning of WORD forward. See |W|.
    <Leader>b            | Beginning of word backward. See |b|.
    <Leader>B            | Beginning of WORD backward. See |B|.
    <Leader>e            | End of word forward. See |e|.
    <Leader>E            | End of WORD forward. See |E|.
    <Leader>ge           | End of word backward. See |ge|.
    <Leader>gE           | End of WORD backward. See |gE|.
    <Leader>j            | Line downward. See |j|.
    <Leader>k            | Line upward. See |k|.
    <Leader>n            | Jump to latest "/" or "?" forward. See |n|.
    <Leader>N            | Jump to latest "/" or "?" backward. See |N|.
    s		             | Find(Search) {char} forward and backward.


```
	Move to word

	map  <Leader>w <Plug>(easymotion-bd-w)
	nmap <Leader>w <Plug>(easymotion-overwin-w)

	" s{char}{char} to move to {char}{char}
	nmap s <Plug>(easymotion-overwin-f2)

	nmap s <Plug>(easymotion-s2)
	nmap t <Plug>(easymotion-t2)

	" JK motions: Line motions
	map <Leader>j <Plug>(easymotion-j)
	map <Leader>k <Plug>(easymotion-k)

	" }}
```
