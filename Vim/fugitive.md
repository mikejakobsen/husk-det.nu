# Working with Git in Vim

http://media.vimcasts.org/videos/32/fugitive_2.m4v

comma + g? to show all git commands
```
nnoremap <localleader>g? :map <localleader>g<cr>
```

## Mappings

| Mapping  |  Function    | Description         |
|----------|--------------|----------------------|
| ga       | Git add      | Add the file         |
| gs       | Git status   | Opens git status     |
| gc       | Git commit   | Git commit basic     |
| gt       | Git commit   | Git commit with diff |
| gd       | Git diff     | Git diff             |
| ge       | Git edit     |                      |
| gr       | Git read     |                      |
| gw       | Git write    |                      |
| gl       | Git log      | See the file in log  |
| gg       | Git gutter   | Toggle gitgutter     |
| gb       | Git branch   |                      |
| gp       | Git grep     |                      |
| go       | Git checkout |                      |
| gps      | Git push     |                      |
| gpl      | Git pull     |                      |


## Normal mode

| Mapping | Function | Description |
|---------|----------|-------------|
| dp      | :diffput | Diff put    |
| do      | :diffget | Diff get    |

## Fugitive Mappings

| Mapping | Function   |
|---------|------------|
| gsl     | Git log    |
| gsd     | Git diff   |
| gsb     | Git blame  |
| gsw     | Git write  |
| gsc     | Git commit |
| gst     | git status |
| gscd    |            |
| gsld    |            |
