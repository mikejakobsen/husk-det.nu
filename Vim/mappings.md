
### My mappings

| Hva' gør den?             |                 | Gentag | Modsat |
|---------------------------|-----------------|--------|--------|
| Lav en ændring            | {edit}          | .      | u      |
| Skan linje efter næste    | f{char}/t{chat} | ;      | ,      |
| Skan linje efter forrige  | F{char}/T{char} | ;      | ,      |
| Skan buffer efter næste   | /pattern<cr>    | n      | N      |
| Skan buffer efter forrige | ?pattern<cr>    | n      | N      |
| Replace                   | :s/før/nu       | &      | u      |
| Flere ændringer           | qx{changes}q    | @x     | u      |



### Vim-Commentary

Remember: *Go Comment*

| gc{motion}         | Comment or uncomment lines that {motion} moves over     |
|--------------------|---------------------------------------------------------|
| gcc                | Comment or uncomment [count] lines.                     |
| {Visual}gc         | Comment or uncomment the highlighted lines.             |
| gc                 | Text object for a comment (operator pending mode,only.) |
| gcgc gcu           | Uncomment the current and adjacent commented lines.     |
| :[range]Commentary | Comment or uncomment [range] lines                      |

### Vim Surround


| Input	 	     | Hva' gør den?					       |
|--------------------|---------------------------------------------------------|
| cs<fra><til>       | Ændre det surrounding tegn fra <fra> <til>              |
| cst<til>           | Ændre det surrounding tegn <til>		               |
| ds<tegn>  	     | Slet det surrounding tegn 			       |
| dst<tegn>          | Slet det surrounding tag 		               |

S in visual mode for at ændre surrounding.
