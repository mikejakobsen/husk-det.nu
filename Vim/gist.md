# Gist



- Post current buffer to gist, using default privacy option.

        :Gist

- Post selected text to gist, using default privacy option.
  This applies to all permutations listed below (except multi).

        :'<,'>Gist

- Create a private gist.

        :Gist -p

- Create a public gist.
  (Only relevant if you've set gists to be private by default.)

        :Gist -P

>  This is only relevant if you've set gists to be private by default;
> if you get an empty gist list, try ":Gist --abandon".

- Create a gist anonymously.

        :Gist -a

- Create a gist with all open buffers.

        :Gist -m

- Edit the gist (you need to have opened the gist buffer first).
  You can update the gist with the ":w" command within the gist buffer.

        :Gist -e

- Edit the gist with name 'foo.js' (you need to have opened the gist buffer
  first).

        :Gist -e foo.js

- Post/Edit with the description " (you need to have opened the gist buffer
  first). >

        :Gist -s something
        :Gist -e -s something

- Delete the gist (you need to have opened the gist buffer first).

        :Gist -d

- Fork the gist (you need to have opened the gist buffer first).

        :Gist -f

- Star the gist (you need to have opened the gist buffer first).

        :Gist +1

- Unstar the gist (you need to have opened the gist buffer first).

        :Gist -1

- Get gist XXXXX.

        :Gist XXXXX

- Get gist XXXXX and add to clipboard.

        :Gist -c XXXXX

- List your public gists.

        :Gist -l

- List gists from user "mattn".

        :Gist -l mattn

- List everyone's gists.

        :Gist -la

- List gists from your starred gists.

        :Gist -ls

- Open the gist on browser after you post or update it.

        :Gist -b
