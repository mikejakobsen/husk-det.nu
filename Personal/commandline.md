## Functions

Some of these functions assume that command line utilities are GNU versions, that filenames do not contain linefeeds, that IFS has not been changed, or that the contents of the input files or other pieces of text do not contain null bytes.

Print each argument followed by a linefeed, run GNU `xargs` with the input delimiter set to linefeed, or get input either as arguments or from STDIN. These functions are also used by later functions.

```bash
    p(){ printf %s\\n "$@"; }
    e(){ xargs -rd\\n "$@"; }
    ai(){ if(($#>0));then p "$@";else cat;fi; }
```

For example `z :2` is the same as `cut -d: -f2` and `zr :2` is the same as `rev|cut -d: -f2|rev`.

```bash
    z(){ cut -d"${1:0:1}" -f"${1:1}"; }
    zr(){ rev|z "$1"|rev; }
```

Remove duplicate lines.

```bash
    u(){ awk '!a[$0]++' -- "$@"; }
```

Consume the whole STDIN before subsequent parts of a pipeline.

```bash
    ~~(){ sponge /dev/stdout; }
```

Display the number of lines in the middle of a pipeline. Without `sponge /dev/stdout` for example `seq 100000|wt|head` would print the number of lines read by the `head` command before the `head` command exits.

```bash
    wt(){ tee >(wc -l>/dev/tty)|sponge /dev/stdout; }
``
For example `s aa bb` replaces `aa` with `bb`, `s aa` deletes `aa`, and `s aa bb cc dd` replaces `aa` with `bb` and `cc` with `dd`. `r` is a similar function for regular expressions.

```bash
    s(){ local s=$(cat) x i;for((i=1;i<=$#;i++));do x=${!i};let i++;s=${s//"$x"/${!i}};done;p "$s"; }
    r(){ ruby -e's=STDIN.read;ARGV.each_slice(2){|x,y|s.gsub!(Regexp.new(x),y||"")};puts s' -- "$@"; }
```

For example `a0 '$2 in a' file1 file2` prints the lines of `file2` whose second field is a chomped line of `file1`. `-e` and `ARGIND` are specific to `gawk`.

    a0(){ awk -e'ARGIND==1{a[$0];next}' -e"$@"; }

Print lines that are only in the first file or in both files. `o2` and `o3` print only unique lines but `r2` and `r3` include repetitions. For example `r2 file[12]` is like `comm -13 <(sort file1) <(sort file2)` but it does not require sorting.

    r2(){ awk 'ARGIND==1{a[$0];next}!($0 in a)' -- "$1" "${2--}"; }
    r3(){ awk 'ARGIND==1{a[$0];next}$0 in a' -- "$1" "${2--}"; }
    o2(){ r2 "$1" "${2--}"|awk '!a[$0]++'; }
    o3(){ r3 "$1" "${2--}"|awk '!a[$0]++'; }

Search in history. I now use `fzf` instead of this function most of the time.

    h(){ if [ $# = 0 ];then history 50;else history|grep -iFe "$1";fi|sed 's/ *[^ ]*  //;/^h /d'; }

Search in the output of `ps`.

    pg(){ local x ps=$(ps -ef);for x;do grep -iF -- "$x"<<<"$ps";done; }

Print three lines, where the first line contains the number of unique lines only in the first file, only in the second file, and in both files, the second line contains the total number of lines only in the first file and only in the second file, and the third line contains the total number of lines in the first file and in the second file.

    inter(){ awk 'ARGIND==1{a[$0];next}{b[$0];if($0 in a&&$0 in b)c[$0]}END{la=length(a);lb=length(b);lc=length(c);print la-lc,lb-lc,lc"\n"la,lb"\n"NR-FNR,FNR}' -- "$1" "${2--}"; }

Move or copy files from STDIN to the directory specified as the first argument. `-t` specifies a target directory in GNU `mv` and `cp`. These use `$@` instead of `$1` so that options can be placed after the first argument.

    mt(){ e mv -t"$@"; }
    ct(){ e cp -Rt"$@"; }

Delete the lines from STDIN from the file specified as an argument.

    /(){ awk 'ARGIND==1{a[$0];next}!($0 in a)' - "$1"|sponge "$1"; }

Prepend STDIN to a file.

    prep(){ touch -- "$1";cat - "$1"|sponge "$1"; }

For example `n 51,100` is the same as `sed 51,100\!d`, `n 4,` is the same as `sed 1,3d`, and `rn 2` prints the second last line.

    n(){ local x=${1/#,/1,};sed -n "${x/%,/,$}"p; }
    rn(){ tac|n "$1"|tac; }

Append the lines from STDIN to the file specified as an argument and remove duplicate lines.

    ua(){ touch -- "$1";awk '!a[$0]++' -- "$1" -|sponge "$1"; }

Resolve symlinks and print the absolute paths of files. `readlink -f` canonicalizes paths in coreutils and OpenBSD but it has a different meaning in OS X and FreeBSD.

    rf(){ ai "$@"|e readlink -f --; }

Print the absolute paths or basenames of files in the directories specified as arguments or read from STDIN.

    l(){ local l;ai "$@"|while IFS= read -r l;do p "$l"/*;done|e readlink -f --; }
    l2(){ local l;ai "$@"|while IFS= read -r l;do ls -- "$l";done; }

Print the recursive contents of the directories specified as arguments or read from STDIN.

    l0(){ local l;ai "$@"|while IFS= read -r l;do p "${l%/}"/**/*;done; }

Run `find` with the `-mindepth 1` and `-maxdepth 1` options.

    f1(){ local n=$(($#+1)) i;for((i=1;i<=$#;i++));do if [[ ${!i} = -* ]];then n=$i;break;fi;done;find "${@:1:n-1}" -mindepth 1 -maxdepth 1 "${@:n}"; }

Filter a list of filenames to only print the names of files that either exist or that do not exist.

    exi(){ e ls -1d -- 2>/dev/null; }
    nexi(){ local x=$(cat);p "$x"|exi|awk 'ARGIND==1{a[$0];next}!($0 in a)' - <(p "$x"); }

Print lines whose number of occurrences in the input is `$1` or more or `$1` or less.

    mi(){ awk '++a[$0]=='$1; }
    ma(){ local x=$(cat);p "$x"|awk 'ARGIND==1{a[$0];next}!($0 in a)&&!b[$0]++' <(p "$x"|awk '++a[$0]>'$1) -; }

Print the number of files in each directory whose path is specified as an argument or is read from STDIN. The `-f` option which disables sorting can make `ls` faster, but it implies including `.` and `..` even if `-A` is specified.

    nf(){ local l;ai "$@"|while IFS= read -r l;do p "$(ls -f -- "$l"|wc -l|awk '{$0-=2}1') $l";done; }

Remove duplicate lines from files in place.

    iu(){ local f;for f;do awk '!a[$0]++' -- "$f"|sponge "$f";done; }

Run `disown` with STDOUT and STDERR redirected to `/dev/null`.

    down(){ "$@"&>/dev/null&disown; }

Open an HTML file for STDIN in a browser or open an HTML file for viewing images in a browser. `date +%s%3N` prints milliseconds since epoch in GNU `date`. Add `jq -Rr @uri|sed s,%2F,/,g` before `im` to escape spaces or other characters in local paths.

    html(){ local f=/tmp/$(date +%s%3N).html;cat>$f;open $f; }
    im(){ sed 's,^/,file:///,;s,.*,<a href=&><img src=&></a>,'|tr -d \\n|cat <(p "<style>body{text-align:center}img{height:${1-calc(50% - 20px)}}</style>") -|html; }

Delete the last `$1` lines from the files specified as the remaining arguments.

    dl(){ local f;for f in "${@:2}";do head -n-$1 -- "$f"|sponge "$f";done; }

For example `aa string` is like `sed s/^/string/` and `ee string` is like `sed 's/$/string/'`. These use an environment variable instead of `-v` or a variable placed after the command because `awk` interprets escape sequences in variables passed with `-v` and in variables placed after a command.

    aa(){ x="$1" awk '{print ENVIRON["x"]$0}'; }
    ee(){ x="$1" awk '{print$0 ENVIRON["x"]}'; }

For example `f '<a href=",">,</a>'` is like `sed 's,.*,<a href="&">&</a>,'`.

    f(){ local l;while IFS= read -r l;do p "${1//"${2-,}"/$l}";done; }

Encode or unencode parts of a URL.

    eu(){ jq -Rr @uri; }
    uu(){ local l;while IFS= read -r l;do printf %b\\n "${l//%/\\x}";done; }

Escape XML or HTML. You can also use `xml esc`.

    amp(){ local x=$(cat);x=${x//&/&amp;};x=${x//</&lt;};x=${x//>/&gt;};p "${x//\"/&quot;}"; }

Convert text to lowercase or uppercase. These apply to non-ASCII characters in a UTF-8 locale with OS X's `tr` but not with GNU `tr`.

    lc(){ tr '[:upper:]' '[:lower:]'; }
    uc(){ tr '[:lower:]' '[:upper:]'; }

Sort by count, like `sort|uniq -c|sort -n|sed 's/^ *[^ ]* //'`.

    sbc(){ awk '{++a[$0]}END{PROCINFO["sorted_in"]="@val_num_asc";for(i in a)print i}'; }

Like `sort|uniq -c|sort -n|sed 's/^ *//'`.

    co(){ awk '{++a[$0]}END{PROCINFO["sorted_in"]="@val_num_asc";for(i in a)print a[i],i}'; }

Like `sort|uniq -c|awk '{print$1}'|sort|uniq -c|sort -n|sed 's/^ *//'`.

    cco(){ awk '{++a[$0]}END{for(i in a)++b[a[i]];for(i in b)print b[i],i}'|sort -n; }

Sort by length.

    sbl(){ awk '{print length,$0}'|sort -n|cut -d\  -f2-; }

Append the contents of the files specified as the second and remaining arguments to the file specified as the first argument and remove the files specified as the second and remaining arguments.

    apd(){ cat "${@:2}">>"$1";rm "${@:2}"; }

Print the number of lines and the number of unique lines.

    wcu(){ awk '{a[$0]}END{print NR,length(a)}' "${@--}"; }

Replace URLs with the final URL after redirections. `res2` uses the `--tag` option of `parallel` to prepend the original URL to each line.

    res(){ ai "$@"|parallel -j10 -k curl -ILso /dev/null -w '%{url_effective}\\n'; }
    res2(){ ai "$@"|parallel --tag -j10 -k curl -ILso /dev/null -w '%{url_effective}\\n'|tr \\t \ ; }

Sort characters, remove duplicate characters, shuffle characters, sort words, remove duplicate words, shuffle words, or reverse words.

    sortc(){ grep -o .|sort|paste -sd\\0 -; }
    uniqc(){ grep -o .|awk '!a[$0]++'|paste -sd\\0 -; }
    shufc(){ grep -o .|shuf|paste -sd\\0 -; }
    sortw(){ grep -Eo \\S+|sort|paste -sd\  -; }
    uniqw(){ grep -Eo \\S+|awk '!a[$0]++'|paste -sd\  -; }
    shufw(){ grep -Eo \\S+|shuf|paste -sd\  -; }
    revw(){ grep -Eo \\S+|tac|paste -sd\  -; }

Print the first line and the total number of lines.

    hw(){ sed -n '1p;$=' "${1--}"; }

Sleep for `$1` seconds after reading each line in a pipeline.

    es(){ local l;while IFS= read -r l;do p "$l";sleep $1;done; }

For example `cr 3-5` prints characters -5 to -3.

    cr(){ rev|cut -c"$1"|rev; }

For example `rr /` is like `sed 's,.*/,,'` and `rl /` is like `sed 's,/[^/]*$,,'`. `NF--` does not work with `nawk`.

    rr(){ awk -F"$1" '{print$NF}'; }
    rl(){ awk -F"$1" NF-- OFS="$1"; }

Print lines that match a pattern or that do not match a pattern before other lines.

    part(){ x="$1" awk '{if($0~ENVIRON["x"])print;else x=x$0"\n"}END{printf"%s",x}'; }
    partn(){ x="$1" awk '{if($0!~ENVIRON["x"])print;else x=x$0"\n"}END{printf"%s",x}'; }

Print lines that start with one of the specified tags before other lines sorted by the tags.

    study(){ awk '{if(/^([sonchjbe$S]|en|ei|el|er|ec) /){print|c}else{x=x$0"\n"}}END{close(c);printf"%s",x}' c='sort -sk1,1'; }

Convert audio files to V2 MP3.

    mp3(){ parallel ffmpeg -i {} -aq 2 {.}.mp3; }

Open and delete the last ten lines in a text file with one URL per line. `head -n-10` removes the last ten lines in GNU `head`.

    h10(){ tail ~/n/htoread|e open;head -n-10 ~/n/htoread|sponge ~/n/htoread; }

Generate an HTML file for reading text in a browser.

    scroll(){ { echo '<style>body{font:20px Times;width:25em;margin:0 auto;line-height:1.2}</style>';cat; }>/tmp/scroll.html;open /tmp/scroll.html; }

Search in a text file that contains lines like `example.com username password` and set the clipboard to the last field of the last matching line.

    pass(){ grep -iFe "$1" ~/n/pass|tee >(awk 'END{printf"%s",$NF}'|pbcopy); }

Search for Unicode characters. `~/f/unicodedata` contains the output of `curl www.unicode.org/Public/UNIDATA/UnicodeData.txt|while read l;do [[ ${l#*;} != @(<|SEMICOLON;)* ]]&&printf \\U$(printf %08d $((0x${l%%;*})));printf ';%s\n' "$l";done`.

    ug(){ grep -ie "$*" ~/f/unicodedata; }

Convert Markdown to HTML or HTML to Markdown. `markdown_strict` uses Markdown.pl syntax without Pandoc extensions.

    mh(){ pandoc -f markdown_strict; }
    hm(){ pandoc -f html -t markdown_strict --no-wrap --atx-headers; }

Open the first Google Scholar result for each argument or line from STDIN.

    scho(){ local l;ai "$@"|while IFS= read -r l;do curl -sG -A mozilla --data-urlencode q="$l" https://scholar.google.com/scholar|pup '.gs_rt a attr{href}'|head -n1;done|e open; }

Strip spaces and tabs from the start, end, or start and end of lines.

    sl(){ sed 's/^[ \t]*//'; }
    sr(){ sed 's/[ \t]*$//'; }
    sb(){ sed 's/^[ \t]*//;s/[ \t]*$//'; }

Join lines from STDIN by `$1`.

    j(){ local x=$(cat);p "${x//$'\n'/$1}"; }

Print the text specified as an argument before or after the lines from STDIN.

    pre(){ p "$1";cat; }
    app(){ cat;p "$1"; }

Print the width, height, and filename of image files. `%i` is absolute path and `%f` is basename in `identify`.

    iwh(){ ai "$@"|e identify -format '%w %h %i\n' --; }

If for example `~/n/urls` contains the line `ru http://rutracker.org/forum/tracker.php?nm=%s`, then `url ru query` opens `http://rutracker.org/forum/tracker.php?nm=query`.

    url(){ local x y;while read x y;do if [[ $x = "$1" ]];then open "${y//\%s/$(jq -Rr @uri "${*:2}")}";break;fi;done<~/n/urls; }

Print the size in bytes or modification time of files followed by the filename, sort files by size in bytes or modification time, or display how many seconds ago files were modified. `nawk` and old versions of `mawk` do not have `systime`.

    sn(){ ai "$@"|e stat -c'%s %n' --; }
    tn(){ ai "$@"|e stat -c'%Y %n' --; }
    sbs(){ sn "$@"|sort -n|cut -d\  -f2-; }
    sbt(){ tn "$@"|sort -n|cut -d\  -f2-; }
    age(){ tn "$@"|awk 'BEGIN{d=systime()}{print d-$1,$0}'|cut -d\  -f1,3-; }

Print the values of XML or HTML attributes. This assumes that there are double quotes around the values of attributes and that comments or CDATA sections do not match the regular expression, and this does not unescape character entity references. There is a space after `@` because `@()` would be treated as an extglob pattern when extglob is enabled.

    @ (){ grep -Pio $1'="\K[^"]+'; }

Print unique lines by field `$1` using `$2` as an optional field separator.

    uf(){ awk "!a[\$$1]++" "${2+FS=}$2"; }

Add field count or sort by field count. `sfc 2` sorts lines by the number of lines of the input whose second field the second field is.

    afc(){ a "{a[\$$1]++;b[\$$1][NR]=\$0}END{for(i in a)for(j in b[i])print a[i],b[i][j]}" "${2+FS=}$2"; }
    sfc(){ afc "$@"|sort -sn|z \ 2-; }

Reverse fields on each line using `$1` as a field delimiter when `$1` is specified or space otherwise.

    rw(){ jq --arg x "${1- }" -Rr './$x|reverse|join($x)'; }

Like `sed 's/[ \t]\{1,\}/ /g;s/^ //;s/ $//'`, but this was faster in my testing.

    sp(){ awk '{$1=$1}1'; }

Download YouTube videos using `-x` to extract audio. `--audio-format aac` converts audio in formats like opus and ogg to aac.

    yx()(cd ~/Music/youtube;ai "$@"|parallel youtube-dl -x --audio-format aac)

Round each number on a list of numbers up to an integer multiple of `$1` when `$1` is a positive number or down to an integer multiple of `$1` when `$1` is a negative number. This also works when `$1` has a fractional part.

    rmu(){ awk '{print$0+(n-$0%n)%n}' n=$1; }

Filter a list of filenames from STDIN to only print the names of files whose size in bytes is `$1` or larger or `$1` or smaller.

    s+ (){ e stat -c'%s %n' --|awk '$1>='$1|cut -d\  -f2-; }
    s-(){ e stat -c'%s %n' --|awk '$1<='$1|cut -d\  -f2-; }

Display lines in the middle of a pipeline.

    tt(){ tee /dev/tty|sponge /dev/stdout; }

Filter a list of filenames to only print the names of files that exist and are either regular files or directories.

    regs(){ e stat -c%F,%n --|awk -F, '$1=="regular file"||$1=="empty regular file"'|cut -d, -f2-; }
    dirs(){ e stat -c%F,%n --|sed -n s/^directory,//p; }

Print the text specified as an argument if STDIN is empty but print the whole input otherwise.

    ie(){ x="$1" awk '1;END{if(NR==0)print ENVIRON["x"]}'; }

For example `= +1` increments each number on a list of numbers by 1.

    =(){ awk "{\$0${1:0:1}=${1:1}}1"; }

Indent lines. `printf %4s` prints four spaces.

    ind(){ sed s/^/"$(printf %${1-4}s)"/; }

Attempt to fetch a URL until the response is not empty.

    ce(){ local x=;while [[ -z $x ]];do x=$(curl -Ls "$@");done;p "$x"; }

## Differences from Bash in Zsh

Word splitting is not performed after expanding unquoted parameters without `setopt shwordsplit`, so that `x='a a';set $x;echo $#` prints `1` instead of `2`. Like in Bash, empty removal is however performed after expanding unquoted parameters, so that `x=;set -- $x;echo $#` prints `0`.

Filename expansion is not performed after expanding unquoted parameters without `setopt globsubst`, so that `x=*;echo $x` always prints `*`.

Globs without matches are treated as errors without `setopt nonomatch`, like in Bash with `shopt -s failglob`.

`echo` interprets escape sequences by default, like in Bash when invoked as `sh` or with `shopt -s xpg_echo`, so that for example `echo \\t` prints a tab.

Array indexes start at one, so that `a=(a b);echo ${a[1]}` prints `a` instead of `b`.

Parameter expansion is performed before brace expansion so that `x=3;echo {1..$x}` prints `1 2 3`.

Functions cannot be exported.

Strings can contain null bytes.

The last command of a pipeline is ran in the current shell, like in Bash with `set +m;shopt -s lastpipe`, so that `x=1;:|x=2;echo $x` prints `2` instead of `1`.

Comments are disabled in interactive shells without `setopt interactivecomments`, so that for example `echo a # print a` prints `a # print a`.

Aliases are enabled in non-interactive shells by default, like in Bash with `shopt -s expand_aliases`.

There is no `help` builtin. Run `man zshbuiltins` to see documentation for builtins.

`history -10` instead of `history 10` prints the last ten history entries, and `history 1` instead of `history` prints all history entries.

`printf %s\\n **` does not recurse into directories. `printf %s\\n **/*` recurses into directories in both Bash and Zsh.

The option in `read` for assigning the results to an array is `-A` instead of `-a`.

The option in `read` for reading a certain number of characters is `-k` instead of `-n`. `-k` also implies reading from a terminal, so `-u0` is needed to read from STDIN.

Numbers that start with zero are not treated as octal numbers without `setopt octalzeroes`.

`for((i=1;i<=10;i++));do echo $i;done` and `i=1;while((i<=10));do let i++;done` result in an error because there is no space before the first opening parenthesis.

`for _ in {1..10};do :;done` and `while read _ x;do :;done` result in an error. In Bash they change `$_`.

`x=a*;[[ aa = $x ]];echo $?` prints `1` instead of `0`, and `x=a*;case aa in $x)echo true;;*)echo false;;esac` prints `false` instead of `true`, `a=abc;b=b*;echo "${a//$b}"` prints `abc` instead of `a`, and `a=aa;b=?;printf %s\\n "${a%$b}"` prints `aa` instead of `a`.

`x=a;printf %s\\n "${x/a/\'}"` prints `\'` instead of `'`, and `x=a;printf %s\\n "${x/a/'}"` prints `'` instead of being blocked because of an unpaired quote. `x=a;q=\';printf %s\\n "${x/a/$q}"` prints `'` in both Bash and Zsh.

`x=\';echo ${x/\'/a}` prints `a` instead of `'`. `x=\';echo ${x/"'"/a}` prints `a` in both Bash and Zsh.

`x=%;echo ${x/%/a}` prints `%a` instead of `a`. `x=%;echo ${x/\%/a}` prints `a` in both Bash and Zsh. `x=#;echo ${x/#/a}` prints `a#` in both Bash and Zsh.

The variable corresponding to `HISTFILESIZE` is called `SAVEHIST`.

If the output of a command does not end with a newline, a percent sign in reverse video followed by spaces and a carriage return is printed after the output without `setopt nopromptcr nopromptsp`.

Sequences of digits are valid function names.

`fc -nl` does not include a tab followed by a space before commands.

`x=abc;i=1;echo ${x:i}` and `x=abc;i=1;echo ${x:i:1}` result in an error. Add a dollar sign or a space before `i`.

`>file` cannot be used to make `file` empty. Use `:>file` instead. `>file` can be used instead of `cat>file` to write STDIN to `file`. `<file` can be used instead of `cat file` to show the contents of a file.

The syntax for indirect expansion is `${(P)n}` instead of `${!n}`.

When there are no positional parameters, `${@:$#}` is treated as `$0` and not as empty.

`printf \\u0025` results in an error.

`[a-z]` matches only the 26 lowercase ASCII letters even in a locale where other characters sort between `a` and `z`, like in Bash with `shopt -s globasciiranges`.

A command that starts with a semicolon does not result in an error.

`$COLUMNS` is set in non-interactive shells.

`set -e;i=1;let i++` and `set -e;f(){ [ -n '' ]&&echo a; };f` do not make the shell exit. `set -e;(false)` makes the shell exit.

`set -- -1;echo $((10-$1))` results in an error because there is no space before `$1`.

Using brace expansion to expand a range of characters works with characters other than ASCII alphanumeric characters.

Increments like `{a..z..7}` are not supported when brace expansion is used to expand a range of characters.

`${x::-1}` results in an error. Use `${x:0:-1}` or `${x: :-1}` instead.

Process substitution is not disabled when the shell is invoked as `sh`.

`a=(x y z);echo $a` prints `x y z` instead of `x`.

`(seq 2 >&2;echo 3)>/dev/null|&grep 1` and `(seq 2 >&2;echo 3)>/dev/null 2>&1|grep 1` print `1` instead of nothing. `(seq 2 >&2;echo 3)2>&1 >/dev/null|grep 1` prints `1` in both Zsh and Bash.

The `time` builtin prints statistics for each part of a pipeline, it prints to STDOUT instead of STDERR, and the parameter corresponding to `TIMEFORMAT` is called `TIMEFMT`.

`printf %04s\\n ff` prints `  ff` instead of `00ff`. Use `printf %04x\\n 0xff` instead.

`echo .*` does not include `.` or `..`.

`echo <()` results in an error instead of printing an empty line.

## Differences from GNU/Linux in OS X

Some of these points do not apply to all GNU/Linux distributions or to all of their versions, to all versions of OS X, or to all versions of GNU command line utilities. Many of the points that are about differences between GNU and non-GNU command line utilities do not apply to Linux distributions such as BusyBox that do not come with GNU command line utilities or to OS X when GNU command line utilities are used in OS X.

OS X is intended to conform to SUS, and all major versions of OS X starting from 10.5 have been certified as conforming to the UNIX 03 product standard, which is the product standard for SUSv3. As of 2016, no platform has been certified as conforming to the UNIX V7 product standard, which is the product standard for SUSv4. Linux distributions are intended mostly but not fully to conform to POSIX, and none have been certified as conforming to a SUS product standard, which is partially because they intentionally do not conform to POSIX in some respects, partially because some follow or mostly follow LSB instead, and partially because the certification process is expensive and so far only commercial Unix-like operating systems have been certified.

`/bin/sh` is Bash instead of Dash like in Debian. `/bin/sh` is also Bash in at least Arch, Gentoo, Red Hat, and SUSE.

OS X comes with Bash 3.2, which is the last version of Bash licensed under GPLv2.

Unlike most Linux distributions, OS X comes with Zsh. Zsh has an MIT-like license, so OS X comes with a new version of Zsh.

`/usr/bin/awk` is `nawk`, also known as BWK `awk`, an old version of which is described in the Awk Programming Language book, and which is still maintained by Brian Kerningham. Debian comes with both `mawk` 1.2 and `gawk` even though `/usr/bin/awk` is `mawk` 1.2. `/usr/bin/awk` is `gawk` in Arch.

OS X no longer comes with `wget` because `wget` is licensed under GPLv3. You can use `curl -O` to download a single URL to the current directory.

OS X uses code point collation order in UTF-8 locales, so that in a UTF-8 locale, `printf %s\\n A B a b|sort|tr -d \\n` prints `ABab` instead of `aAbB`, `grep '[a-z]'` does not match Latin letters with diacritics, `printf %b\\n \\u0d4c \\u0d57 \\u0d46\\u0d57|sort -u` prints three lines instead of one line, and `printf '\u2461\n\u2460\n'|sort` prints U+2460 first instead of printing U+2461 first. Also `shopt -s globasciiranges` has no effect in Bash because its behavior is the default behavior in OS X.

For example `[[:alpha:]]` does not match Arabic or Japanese characters in a UTF-8 locale like it does in Debian.

`cp -r` is not equivalent to `cp -R`, but it follows symlinks and reads the contents of special files and FIFOs, like `cp -RL --copy-contents` in coreutils. `-r` has the OS X behavior in old versions of FreeBSD but it is equivalent to `-RL` in new versions of FreeBSD. POSIX allows `-r` to have implementation-defined behavior but has made it obsolete.

OS X comes with `rsync` 2 because `rsync` 3 is licensed under GPLv3. The main implementation of `rsync` 2 does not support extended attributes, but the version of `rsync` 2 that comes with OS X uses AppleDouble files to add support for extended attributes.

`cut -c` counts multibyte characters in a UTF-8 locale as required by POSIX instead of being equivalent to `cut -b`. `cut -d` accepts a multibyte character as a delimiter in a UTF-8 locale. An illegal byte sequence causes `cut` to exit with an error in a UTF-8 locale.

`tr` supports Unicode in a UTF-8 locale, so that for example `tr '[:upper:]' '[:lower:]'` applies to non-ASCII characters and `tr $'[\u30a0-\u30ff]' $'[\u3040-\u309f]'` converts katakana to hiragana. `tr -dc 0-9</dev/random` results in an illegal byte sequence error in a UTF-8 locale.

`fold` applies to multibyte characters instead of bytes in a UTF-8 locale.

OS X comes with BSD command line utilities such as `jot`, `rs`, and `mkfile`.

`xargs` has no `-r` option, but the behavior of `-r` in GNU `xargs` is the default, so that for example `printf ''|xargs date` prints nothing.

`xargs` has no `-d` option for changing the input delimiter. You can use `tr \\n \\0|xargs -0` instead of `xargs -d\\n`, even though that uses both linefeed and null as input delimiters.

The `-I` option in `xargs` does not imply changing the input delimiter to only linefeed. Use `tr \\n \\0|xargs -0I` instead.

The `-f` option in `readlink` changes the output format instead of canonicalizing filenames. There is also no `realpath` command.

`wc -l`, `wc -w`, `wc -c`, and `wc -m` include one or more spaces at the start of the output line when a single file is given as an argument or when input is read from STDIN.

Control-o is used to toggle discarding pending output without `stty discard undef`.

OS X does not come with the procps implementations of `ps`, `top`, `pgrep`, or `pkill`. `ps` has no `-C` option for matching command names. The `comm` column in `ps` contains the full command line instead of the command name without the `-c` option, which is not included in the procps implementation. `pgrep` and `pkill` do not include ancestor processes without the `-a` option, which is not included in the procps implementations.

Process names are not truncated to 15 characters like in Linux.

HFS+ stores many non-ASCII characters in filenames in decomposed form instead of precomposed form. If the current directory is empty, `touch $'\u00e4';ls|tr -d \\n|xxd -p` prints `61cc88` instead of `c3a4`. HFS+ does not use the standard NFD (normal form decomposed) but a variant of NFD where the characters from 2000 to 2FFF, from F900 to FAFF, and from 2F800 to 2FAFF are not decomposed. `iconv -f utf-8-mac -t utf-8` converts text from the decomposed form used by HFS+ to precomposed form. At least Bash 4.1 and later and new versions of Zsh treat the decomposed and precomposed forms of characters as equivalent in contexts such as filename completion and globbing.

The short option for the `--mime` option in `file` is `-I` instead of `-i` like in Debian.

`stat` uses different format specifier characters and the option for specifying a format is `-f` instead of `-c`.

`find` does not have the `-printf` option.

`find` does not allow omitting a period as the first argument.

`find` uses POSIX BRE instead of Emacs regular expressions by default. The option in `find` for using ERE is `-E` instead of `-regextype posix-extended`.

`find` does not display a warning when an option primary like `-mindepth` is placed after a non-option primary like `-type`.

`find` does not display a warning when `-d` is used instead of `-depth` or when `-ipath` is used instead of `-iwholename`, but like in GNU `find`, `-d` is equivalent to `-depth` and `-ipath` is equivalent to `-iwholename`.

`find` does not have the `-executable` primary. You can use `-perm +100` instead, even though that does not consider ACLs.

The option for using ERE in `sed` is `-E` instead of `-r`. GNU `sed` also supports `-E` as an undocumented alias for `-r`.

`sed -i s/a/b/ file` does not work but `sed -i '' s/a/b/ file` does, which is the opposite of GNU `sed`.

`sed` does not support other escape sequences than `\n`, and even `\n` is not supported in the replacement part of the `s` function. Use `sed $'s/\t/aa/'` instead of `sed 's/\t/aa/'` or use `sed $'s/aa/\\\n/'` instead of `sed 's/aa/\n/'`.

`sed` does not support the `\|`, `\+`, `\?`, ``` \` ```, `\'`, `\<`, `\>`, `\b`, `\B`, `\w`, or `\W` escape sequences in basic regular expressions. POSIX leaves the behavior of those escape sequences unspecified in basic regular expressions.

Attempting to match an illegal byte sequence results in a fatal error in `sed`. In GNU `sed` that does not even result in a warning, even though a period does not match an illegal byte sequence as required by POSIX.

`sed` does not print the pattern space before it exits when the `N` function is used on the last line, so that `sed N<<<a` does not print anything. You can often rewrite a `sed` command to use `$!N` instead of `N` to get the same behavior with GNU `sed` and BSD `sed`.

`sed` does not have the `e`, `v`, `z`, `F`, `L`, `Q`, `R`, `T`, or `W` functions.

`sed` does not support the `x~y` address type for matching every `y`-th line starting from `x`.

`sed` does not support 0 as a line number in addresses.

`sed` does not allow omitting a semicolon before a closing brace.

The `a`, `i`, and `c` functions in `sed` have to be followed by a backslash and a linefeed as specified by POSIX, and they do not add a linefeed if the text to be inserted does not end with a linefeed. Use `sed $'/pattern/a\\\ntext to insert\n'` instead of `sed '/pattern/atext to insert'`.

`printf a|sed -n p` adds a linefeed.

The `l` function in `sed` does not escape printable non-ASCII characters in a UTF-8 locale, even though it does escape non-printable non-ASCII characters.

The `-l` option in `sed` makes output line-buffered instead of specifying a line length for the `l` function.

`grep` no longer has the `-P` option for using PCRE in 10.8 and later.

`grep -z` and `grep -Z` are both equivalent to `zgrep` instead of `-z` using null as the input delimiter and `-Z` using null as the output delimiter.

`sort` does not have the `-z` option for using null as the input and output delimiter.

`date` does not have the `-d` option. Use `date -r1234567890` instead of `date -d@1234567890`, or use `date -jf%FT%T 2013-12-31T23:59:59 +%s` instead of `date -d2013-12-31T23:59:59 +%s`.

`date` does not have the `%N` specifier for nanoseconds.

The `date` man page does not include documentation for the format specifier characters. See the `strftime` man page instead.

`touch` does not have the `-d` option for specifying the time in the formats understood by `-d` in GNU `date`. Use `touch -t$(date -r1234567890 +%Y%m%d%H%M.%S) file` instead of `touch -d@1234567890 file`.

`du` and `df` use 512 instead of 1024 as the default block size as required by POSIX.

`du` does not have the `-b` option for displaying sizes in bytes.

`paste` does not allow using `-d ''` instead of `-d\\0` and does not allow omitting the dash for STDIN.

`cp` and `mv` do not have the `-t` option for specifying a target directory.

`seq` uses scientific notation by default for 1,000,000 and larger numbers and -1,000,000 and smaller numbers. Add `-f %.0f` to format all numbers as integers.

OS X comes with `md5` but not `md5sum`. Use `md5 -r` instead of `md5sum` or use `md5 -q file` instead of `md5sum file|cut -d' ' -f1`.

OS X comes with `shasum` but not `sha256sum`. Use `shasum -a 256` instead of `sha256sum`.

`fmt -80` and `fmt -w80` set both the maximum and goal width to 80 instead of using 93% of 80 as the goal width.

`fmt` does not reformat lines that start with a period without the `-n` option, which is not included in GNU `fmt` which always reformats lines that start with a period.

`fmt` does not exit with an error when the width specified as an argument is 2501 or larger.

OS X does not come with `tac`. Use `tail -r` instead.

OS X does not come with `shuf` and `sort` does not have the `-R` option.

`head -n` and `head -c` do not support a negative offset. You can use `tail -r|tail -rn+6` instead of `head -n-5`.

In `mktemp` the `-t` option cannot be omitted, a period followed by random characters is always appended to the template specified with `-t`, and `X` characters are not replaced with random characters in the template specified with `-t`. Instead of `mktemp` or `mktemp -t tmp.XXXXXXXX`, use `mktemp -t tmp`.

OS X comes with Emacs 22.1.1, which is the last version of Emacs licensed under GPLv2.

`-E` preserves extended attributes and ACLs in `rsync`, when in the main `rsync` implementation `-X` preserves extended attributes and `-A` preserves ACLs.

`cp -R dir/ /tmp` copies each file inside `dir` instead of `dir` itself because of the forward slash after `dir`. Like in coreutils, `mv dir/ /tmp` moves `dir` itself however.

The environment variable for changing the colors used by `ls` is named `LSCOLORS` instead of `LS_COLORS` and has a different format, and `ls` does not use colors by default unless the `CLICOLOR` environment variable is set.

Terminal.app and iTerm 2 execute a command like `login -pf username` in new shells by default, so that shells are invoked as login shells and for example Bash reads `~/.bash_profile` but not `~/.bashrc`, whereas the terminal applications on other platforms often invoke shells as non-login shells, so that for example Bash reads `~/.bashrc` but not `~/.bash_profile`.

`ls` does not have the `--time-style` option. The `-T` option in `ls` always includes years and seconds in times but it has a different meaning in GNU `ls`.

OSXFUSE does not come with an `fusermount` command and OSXFUSE volumes can be unmounted with `umount` instead of `fusermount -u`.

OS X comes with `dtrace` instead of `strace`.

`/bin/echo` only has the `-n` option instead of `-n`, `-e`, `-E`, `--help`, and `--version`.

`od` uses a different output format where there are multiple spaces instead of one space after the address space, two spaces instead of one space between columns, and spaces at the end of lines that are shorter than the width of the default output.

`od` stops parsing options after a non-option argument, so that for example `od /dev/random -N6 -tx1 -An` reads from `/dev/random` indefinitely.

Unlike many Linux distributions, OS X comes with `pax` and `uuencode`, which are required by POSIX but not by LSB.

`uniq` compares collation orders as required by POSIX instead of comparing byte values, but it does not make a difference in what lines are considered unique since different sequences of characters always have a different collation order in OS X, even though it does make an illegal byte sequence result in an error in a UTF-8 locale.

The default `PATH` is `/usr/bin:/bin:/usr/sbin:/sbin` instead of `/bin:/usr/bin:/sbin:/usr/sbin` like in Ubuntu.

`ifconfig` without arguments is synonymous to `ifconfig -a` instead of `ifconfig -u` so that it lists all interfaces instead of only interfaces that are up.

Some binaries that are in `/bin` in Ubuntu are in `/usr/bin`, such as `bzip2`, `false`, `grep`, `gzip`, `less`, `login`, `more`, `sed`, `tar`, `touch`, `true`, and `which`. Some binaries that are in `/usr/bin` in Ubuntu are in `/bin`, such as `expr`, `rcp`, `test`, and `unlink`.

`/etc`, `/tmp`, and `/var` are symlinks to directories in `/private` instead of directories.

When `tar` creates an archive, it includes an AppleDouble file in the archive for each file that has extended attributes, file flags, an ACL, or a resource fork. `COPYFILE_DISABLE= tar` disables that.

In `tar` `-s/aa/bb/` instead of `--transform s/aa/bb/` replaces `aa` with `bb` in filenames.

`sort` has no `-V` option for sorting version numbers.

Most HFS+ volumes are case-insensitive, so that `cd ~/desktop` changes the directory to `~/Desktop` and `eyed3` runs an executable on the path whose name is `eyeD3`. Using `mv` to rename a file so that only case is changed results in an error on a case-insensitive volume when GNU `mv` is used in OS X but not with OS X's `mv`.

`iconv -f utf-8 -t ascii//TRANSLIT<<<$'\u00c4'` prints `"a` instead of `a`. You can use `unaccent utf-8` instead.

`sleep` does not support the `m`, `h`, or `d` suffixes.

The `-U` option in `ls` makes `-t` and `-l` use creation times instead of modification times, when in GNU `ls` it is like `-f` but disables `-l`, `-s`, and `--color`.

## Differences between gawk, nawk, and mawk

Some of these points do not apply to all versions of `gawk`, `nawk`, or `mawk`, or to all builds of `mawk` 1.3.4, and I have only tested them with `gawk` 4.1.1, `nawk` version 20070501, builds of `mawk` 1.3.4 from 2016, and with `mawk` 1.2.

See `man gawk|less +/^GNU\ EXTENSIONS` for a more complete list of differences between `gawk` and POSIX `awk`.

`nawk` is also known as BWK `awk` or Brian Kerningham's `awk`. An old version of `nawk` is described in the Awk Programming Language book, the first and only edition of which was published in 1988. `/usr/bin/awk` is `nawk` in OS X and BSDs.

The original author of `mawk`, Mike Brennan, stopped maintaining `mawk` in 1996 at version 1.3.3, but starting from 2010, another maintainer, Thomas Dickey, has relesed a few builds of `mawk` with the version number 1.3.4, which have addressed changes to POSIX, fixed bugs, and added a few features. Starting from 2016, Mike Brennan has released builds of `mawk` that are called beta releases of `mawk` 2.0, which are based on `mawk` 1.3.3 and not on `mawk` 1.3.4, but they are not covered on this list.

As of 2016, the `/usr/bin/awk` in Debian is version 1.2 of `mawk`, which was released in 1994 and which does not conform to newer versions of POSIX in some respects. Debian also comes with `gawk` however, and in my Ubuntu VM at DigitalOcean `/usr/bin/awk` was `gawk` by default. `/usr/bin/awk` is `gawk` in Arch.

`mawk` 1.2 does not support character classes or range expressions in regular expressions even though support for both is required by POSIX, so that `mawk '/[[:lower:]]/'<<<a` and `mawk '/a{1,}/'<<<a` do not print anything with `mawk` 1.2. `mawk` 1.3.4 supports character classes, but the built-in regular expression engine in `mawk` 1.3.4 does not support range expressions, which is a documented violation of POSIX.

`gawk` and `mawk` 1.3.4 support null bytes in strings and in input data but `nawk` and `mawk` 1.2 do not.

`gawk` and `mawk` treat a two character or longer `RS` as a regular expression but `nawk` does not.

`nawk` and `mawk` treat hexadecimal numbers that start with `0x` as numbers but `gawk` does not without `-P` or `-n` (`--non-decimal-data`). `gawk -n` also treats octal numbers that start with zero as numbers but `gawk -P`, `nawk`, `mawk` 1.3.4, or `mawk` 1.2 does not.

`LC_ALL=de_DE.UTF-8 awk '{print$1*2,$2*2}'<<<1.5\ 1,5` prints `2 3` in `nawk`, `mawk` 1.3.4, and in `gawk` with `-P` or `--lc-numeric`, `3 2` in `gawk` by default, and `3 3` in `mawk` 1.2.

`LC_ALL=de_DE.UTF-8 awk 'BEGIN{print 1.5}'` prints `1,5` in `mawk` 1.3.4.

`nawk` requires adding a space after `-v`.

`print>$1".txt"` is equivalent to `print>($1".txt")` in `gawk` and `mawk` 1.3.4 but results in an error in `nawk` and `mawk` 1.2.

`nawk` and `mawk` do not support arrays of arrays.

`nawk` and `mawk` do not have the `IGNORECASE` variable, but they do have the `tolower` and `toupper` functions which are in POSIX.

Splitting on each character by setting `FS` to an empty string or by using an empty string as the third argument for `split` is not specified by POSIX, but both are supported by `gawk`, `nawk`, `mawk` 1.3.4, and `mawk` 1.2.

`nawk` does not support `-F ''` as an alternative to `-v FS=`.

`nawk` and `mawk` do not support the `BEGINFILE` or `ENDFILE` patterns of `gawk`. `BEGINFILE` can be replaced with `FNR==1` most of the time, even though inside a `BEGINFILE` rule the `ERRNO` variable can be used to check if there was an error reading a file.

`nawk` and `gawk` support `**` as an alias to the `^` operator but `mawk` does not. `nawk` and `gawk` support `func` as an alias to the `function` keyword but `mawk` does not.

`tolower` and `toupper` apply to non-ASCII characters in a UTF-8 locale in `gawk` but not in `nawk` or `mawk`.

When the operands are strings, `==` and `!=` compare collation orders in `nawk` and in `gawk` in POSIX mode, but they compare byte values in `gawk` by default and in `mawk`.

`mawk` 1.3.4, `mawk` 1.2, `gawk -P`, and `gawk -c` exit when they encounter an input file argument for a directory, but `gawk` by default and `nawk` do not.

Using the `length` function to get the length of an array is not specified by POSIX and it is not supported by `mawk` 1.2, but it is supported by `gawk`, `nawk`, and `mawk` 1.3.4.

`nawk` and `mawk` 1.2 do not have the `mktime`, `strftime`, or `systime` functions, but `gawk` and `mawk` 1.3.4 do.

`nawk` and `mawk` do not have the `asort` or `asorti` functions.

`nawk` and `mawk` do not have the `-e` option of `gawk`.

Octal constants in strings are specified by POSIX but hexadecimal constants are not, even though both are supported by `gawk`, `nawk`, `mawk` 1.3.4, and `mawk` 1.2.

`nawk` and `mawk` do not have `RT`.

`awk NF--<<<a\ b` prints `a` in `gawk`, `mawk` 1.3.4, and `mawk` 1.2, but `a b` in `nawk`.

`nawk` and `mawk` do not support `\s`, `\S`, `\w`, `\W`, `\<`, `\>`, `\y`, `\B`, ``` \` ```, or `\'` in regular expressions.

`printf %s\\n a 'b c'|awk '{print$2}' RS=x` prints `c` in `gawk` in POSIX mode because a linefeed is not treated as a field separator when `FS` has not been changed.

POSIX and `mawk` 1.2 do not have `nextfile`.

`gawk`, `nawk`, `mawk` 1.3.4, and `mawk` 1.2 all use different hashing functions for arrays, so that they go through the keys of an array in a for loop in a different order.

`awk '{sub("a","\\\\")}1'<<<a` prints `\\` in `gawk` and `nawk` but `\` in `mawk` 1.2 and `mawk` 1.3.4.

`mawk` 1.3.4 goes through the keys of an array in a for loop in a sorted order when the `WHINY_USERS` environment variable is set, which was an undocumented feature in `gawk` that stopped working in `gawk` 4.0.

## jq examples

Pretty-print JSON, like `python -mjson.tool`.

    jq

Print JSON in a compact format.

    jq -c

Print the sum of a list of numbers. `-s` (`--slurp`) creates an array for the input lines after parsing each line as JSON, or as a number in this case.

    jq -s add

Print the maximum of a list of numbers.

    jq -s max

Print the average of a list of numbers.

    jq -s add/length

Print the RMS of a list of numbers, like `awk '{x+=$0*$0}END{print sqrt(x/NR)}'`.

    jq -s 'map(.*.)|add/length|sqrt'

Print the length of the longest line, like `wc -L` in GNU `wc`. `-R` (`--raw-input`) treats each input line as a string.

    jq -R length|jq -s max

Apply percent encoding. `-sR` reads the input into a single string. `-r` (`--raw-output`) prints the contents of strings instead of JSON string literals.

    jq -sRr @uri

Escape HTML or XML, like `xml esc` or `recode ..xml`.

    jq -sRr @html

Print the number nearest to 50 on a list of numbers.

    jq -s 'min_by(.-50*.-50)'

Like `awk '$0<100{exit 1}'`. `-e` sets the exit status to 1 if the last output value is false or null.

    jq -es 'map(.>=100)|all'>/dev/null

Round each number on a list of numbers down to the nearest multiple of 20.

    jq -R 'tonumber/20|floor*20'

Like `awk 'length<=80'`.

    jq -Rr 'select(length<=80)'

Sort a single line list where items are delimited by a comma followed by a space, like `gsed 's/, /\n/g'|sort|sed '$!s/$/, /'|paste -sd\\0 -`. The `/` operator is overloaded to split strings.

    jq -Rr './", "|sort|join(", ")'

Convert a list of lines to a JSON array of strings. `jq -R .` prints each input line as a JSON string so that the input can be slurped with `-s`.

    jq -R .|jq -s .

Sort by length, like `awk '{print length,$0}'|sort -n|cut -d' ' -f2-`.

    jq -R .|jq -sr 'sort_by(length)[]'

Like `sort -u`. `unique` also sorts items.

    jq -R .|jq -rs 'unique[]'

Like `sort -t\; -uk3,3`. `unique_by` also sorts items.

    jq -R .|jq -sr 'unique_by(split(";")[2])[]'

Like `x="$x" awk '{print ENVIRON["x"]$0}'`.

    jq --arg x "$x" -Rr '$x+.'

Transpose a matrix, like `rs -c' ' -C' ' -T`.

    jq -R .|jq -sr 'map(./" ")|transpose|map(join(" "))[]'

Print lines that end with the specified string.

    jq --arg x "$string" -Rr 'select(endswith($x))'

Print the product of a list of numbers.

    jq -s 'reduce.[]as$x(1;.*$x)'

Print the factorial of 6. `-n` is `--null-input`. The end point for `range` is exclusive like in Python.

    jq -n 'reduce range(1;7)as$x(1;.*$x)'

Merge JSON files.

    jq -s add *.json

Merge two JSON files recursively. The `*` operator is overloaded to merge objects recursively.

    jq -s '.[0]*.[1]' [12].json

Merge multiple JSON files recursively.

    jq -s 'reduce.[]as$x({};.*$x)' *.json

Print 2-tuples (2-permutations with repetition) of the letters a, b, and c, like `printf %s\\n {a,b,c}{a,b,c}`.

    jq -nr '"abc"/""|combinations(2)|join("")'

Subtract the minimum from a list of numbers.

    jq -s 'min as$x|map(.-$x)[]'

Like `awk '{if(/pattern/)print;else x=x$0"\n"}END{printf"%s",x}'`. `test` is like `match` but it only returns true or false instead of a match object.

    jq -R .|jq -sr 'group_by(test("pattern")|not)[][]'

Like `while IFS= read -r l;do printf %s\\n "${x//aa/$l}";done`.

    jq --arg x "$x" -Rr '. as$in|$x|gsub("aa";$in)'

Like `pcregrep -M -o1 -o2 --om-separator=' ' '(?s)<a href="([^"]*)">MP3 Download.*?Rated <strong>([^<]*)'`.

    jq -Rsr 'scan("(?s)<a href=\"([^\"]*)\">MP3 Download.*?Rated <strong>([^<]*)")|join(" ")'

Like `grep -Ff file1 file2`.

    jq --slurpfile a <(jq -R . file1) -Rr '. as$in|select($a|map(inside($in))|any)' file2

Like `awk '{split($3,a,"/");$3=a[5]}1' FS=\; OFS=\;`. `|=` is the update operator.

    jq -Rr './";"|.[2]|=(./"/")[4]|join(";")'

Print the geometric mean of a list of numbers, like `awk '{x*=$0}END{print x^(1/NR)}' x=1`.

    jq -s 'length as$l|reduce.[]as$x(1;.*$x)|pow(.;1/$l)'

Like `awk '{if(length<=80)print;else x=x$0"\n"}END{printf"%s",x}'`.

    jq -R .|jq -sr 'group_by(length>80)[][]'

Like `awk -F\; 'NR==FNR{a[$0]=NR;next}{print a[$3],$0}' file1 file2|sort -n|cut -d' ' -f2-`, assuming that `file1` contains each line only once.

    jq -R . file2|jq --slurpfile a <(jq -R . file1) -s 'sort_by((./";")[2]as$x|$a|indices($x))'

Print the standard deviation of a list of numbers, like `awk '{x+=$0;y+=$0^2}END{print sqrt(y/NR-(x/NR)^2)}'`.

    jq -s '(map(.*.)|add/length)-pow(add/length;2)|sqrt'

Like `grep -Fxf file1 file2;grep -Fxvf file1 file2`.

    jq -R . file2|jq --slurpfile a <(jq -R . file1) -rs 'group_by([.]|inside($a))[][]'

Scale a list of numbers so the smallest number becomes 0.0 and the largest becomes 1.0.

    jq -rs 'min as$min|max as$max|map((.-$min)/($max-$min))[]'

Floor each number on a list of numbers, like `awk '{x=int($0);print(x==$0||$0>0)?x:x-1}'`.

    jq -s 'map(floor)[]'

Round each number on a list of numbers down to a multiple of 100.

    jq -s 'map(./100|floor|.*100)[]'

Count how many numbers are between each multiple of 100 on a list of numbers, where the start of each block is inclusive and the end is exclusive.

    jq -sr 'map(./100|floor|.*100)|group_by(.)[]|(length|tostring)+" "+(.[0]|tostring)'

Print the sum of numbers in each column in a table of numbers where columns are separated by tabs, assuming that each row has the same number of columns.

    jq -R .|jq -sr 'map(./"\t"|map(tonumber))|transpose|map(add|tostring)|join("\t")'

Sort a Markdown file that consists of sections that start with second level ATX headings by the headings. `(?s)` makes period match a newline and `(?m)` makes `^` match the start of a line and `$` match the end of a line.

    jq -Rsr '[scan("(?sm)^## .*?(?=\n\n## |\\Z)")]|sort|join("\n\n")'

Print the lines of the input that contain all substrings in `substrings.txt`.

    jq --slurpfile a <(jq -R . substrings.txt) -Rr 'select(. as$in|$a|map(inside($in))|all)'

Reverse words on each line, like `awk -F'[ ]' '{for(i=NF;i>=1;i--)printf"%s",$i (i==1?"\n":" ")}'`.

    jq -Rr './" "|reverse|join(" ")'

Sort lines by the number of lines of the input whose second field the second field is.

    jq -R .|jq -sr 'group_by((./" ")[1])|sort_by(length)[][]'

Like `sort|uniq -c|awk '$1<=2'|sed 's/^ *[^ ]* //'` but without sorting.

    jq -R .|jq -sr 'group_by(.)[]|select(length<=2)[0]'

Like `gawk 'ARGIND==1{a[$0];next}!($0 in a)' file[12]`.

    jq --slurpfile a <(jq -R . file1|sed 's/$/:1/;$!s/$/,/;1s/^/{/;$s/$/}/') -Rr 'select(in($a[0])|not)' file2

Print the lines of `file2` that start with one of the lines of `file1`, like `grep -Ff <(sed 's/[[^.\$*]/\\&/g;s/^/^/' file1) file2`.

    jq --slurpfile a <(jq -R . file1) -Rr '. as$in|select($a|map(. as$x|$in|startswith($x))|any)' file2

Generate an HTML file for reading answers posted by a Stack Exchange user.

    curl -s 'https://api.stackexchange.com/2.2/users/22565/answers?site=unix&pagesize=100&page=1&filter=!-.mgWMP7sO7J'|gzip -d|jq -r '.items[]|"<h2><a href="+.link+">"+.title+"</a></h2>"+.body'>/tmp/a.html

Generate an HTML file for reading answers to questions tagged with `jq` from Stack Overflow.

    api=https://api.stackexchange.com/2.2;site=stackoverflow;curl -s "$api/questions/$(curl -s "$api/search?site=$site&sort=votes&tagged=jq&pagesize=100&page=1"|gzip -d|jq -r '.items[].question_id'|paste -sd\; -)/answers?site=$site&sort=votes&pagesize=100&page=1&filter="\!--btTLjIVxsJ|gzip -d|jq -r '.items[]|"<h2><a href="+.link+">"+.title+"</a></h2>"+.body'>/tmp/a.html

List commands that have a score of 2 or higher out of the 1000 newest commands at commandlinefu.

    curl -s http://www.commandlinefu.com/commands/browse/sort-by-date/json/{0..975..25}|jq -r '.[]|select(.votes|tonumber>=2)|"# "+.summary+"\n"+.command+"\n"'

Print the URLs of links that have a score of 5 or higher posted to a subreddit.

    a=;for i in {1..10};do r=$(curl "https://api.reddit.com/r/emacs/new?limit=100&after=$a");jq -r '.data.children[].data|select(.is_self==false and .score>=5).url'<<<"$r";a=$(jq -r .data.after<<<"$r");done

Open a `spotify:` URL for the first Spotify search result. Without `//empty` the `jq` command would print `null` when the result of the filter is null.

    curl -sG --data-urlencode q='Artist - Title' 'https://api.spotify.com/v1/search?type=track'|jq -r '.tracks.items[0].uri//empty'|xargs open -g

Print the WikiText source of a Wikipedia article. `."*"` selects a key named `*`.

    curl -s 'https://en.wikipedia.org/w/api.php?action=query&prop=revisions&rvprop=content&format=json&titles=Example'|jq -r '.query.pages[].revisions[]."*"'

Get similar artists for an artist from Last.fm.

    curl -s "http://ws.audioscrobbler.com/2.0/?api_key=$lastfmapi&format=json&method=artist.getsimilar&artist=$artist"|jq -r '.similarartists.artist[].name'

Make a list of the URLs of all images posted to a Tumblr blog.

    api="http://api.tumblr.com/v2/blog/$tumblr.tumblr.com/posts?type=photo&api_key=$tumblrapi";for n in $(seq 0 20 $(curl -s "$api"|jq .response.total_posts));do curl -s "$api&offset=$n"|jq -r '.response.posts[].photos[].original_size.url';done

Make a list of the URLs of all images uploaded by a Flickr user. `-e` causes `jq` to exit with a nonzero status after the last page when the result of the filter is empty.

    page=1;url="https://api.flickr.com/services/rest/?api_key=$flickrapi&format=json&method=flickr.people.getPublicPhotos&user_id=$userid&extras=url_o,url_l&per_page=500&page=";while :;do curl "$url$page"|sed 's/jsonFlickrApi(\(.*\))/\1/'|jq -er '.photos.photo[].url_o'||break;let page++;done

Make a list of the URLs of photos posted by an Instagram user.

    max=;while :;do c=$(curl -s "https://www.instagram.com/username/?__a=1&max_id=$max");jq -r '.media.nodes[]?|.display_src'<<<"$c";max=$(jq -r .user.media.page_info.end_cursor<<<"$c");jq -e .user.media.page_info.has_next_page<<<"$c">/dev/null||break;done

## Single line notes

    #unsorted
    `pkill -f` matches the full command line and `pgrep -fl` matches and prints the full command line
    `env` and `printenv` print exported variables and functions and `set` and `declare` print all variables and functions
    brace expansion is performed before all other expansions in bash but between process substitution and word splitting in ksh
    in `en_US.UTF-8`, `en` is the language, `US` is the territory, and `UTF-8` is the codeset
    `LC_CTYPE` can be set to just `UTF-8` but `LANG` and `LC_ALL` have to be set to a value like `en_US.UTF-8` that includes the language and territory parts
    if you run `mkfifo p;cat p` in one shell and `echo z>p` in a second shell, `z` is printed in the first shell
    if you run `mkfifo p;echo x>p` in one shell, `cat p` prints `x` in another shell
    the Bourne shell supported `var=value;export var` but not `export var=value`
    you can run the current command on the background by pressing `C-z` and running `bg`
    you can disown the current command by pressing `C-z` and running `bg;disown`
    `SIGXCPU` is sent when `ulimit -t` is exceeded and `SIGXFSZ` is sent when `ulimit -f` is exceeded
    a process receives a `SIGPIPE` when it tries to write to a pipe with no reader left
    `%%` and `%+` are the current job and `%-` is the previous job
    `atq` is equivalent to `at -l` and `atrm` is equivalent to `at -d`
    `NUL` is `^@` in caret notation because `@` is before `A` and `DEL` is `^?` because `?` is before `@`
    `grep -E '.{1,256}'` results in an error if `RE_DUP_MAX` is `255`
    `sha224sum`, `sha256sum`, `sha384sum`, and `sha512sum` compute SHA-2 hashes
    `declare` and `typeset` are identical in both bash and zsh but `typeset` is obsolete in bash
    the default `ARG_MAX` is one fourth of the stack size in recent versions of Linux, so `ulimit -s 4000;getconf ARG_MAX` prints `1024000`
    `-z` sets the input and output separators to null in GNU `sort`, GNU `uniq`, and GNU `sed`, and `-zZ` sets the input and output separators to null in GNU `grep`
    `\e[H` goes to the beginning of a line and `\e[F` goes to the end of the line
    `x==;[ -n $x -a -n $x ]` results in an error in bash
    the Thompson shell was released as part of the first version of Unix in 1971 and the Bourne shell was released in 1979 as part of Version 7 Unix
    the last version of the Bourne shell was released as part of SVR4.2
    `csh` was released in 1979 as part of 2BSD
    `/var/run/screen/S-$USER/$STY` is a domain socket used by screen
    `[[` is not supported by dash, yash, or posh
    POSIX does not specify local variables but LSB does
    `tput init` outputs initialization strings and `tput reset` outputs reset strings
    single user mode is meant to work without files in `/usr`
    `/usr/share` is used for files that can be shared by computers with different architectures
    `/boot` is meant for static files used by the boot loader
    `/lib` contains shared library images that are needed to boot the system and to run commands in the root filesystem
    `/opt` is used for static files and `/var/opt` is used of variable files
    `/usr` is meant for read-only data that can be shared between hosts
    `/var/run` contains run-time variable data
    `/var/tmp` is used for temporary files preserved between reboots
    Linux ignores the setuid bit in executables that start with `#!`
    `ctime` is updated when metadata or contents change and `mtime` is updated when contents change
    `el` erases to the end of a line, `el1` erases to the start of a line, and `ed` erases to end of display
    `smul` starts underline and `rmul` removes underline
    `cnorm` shows the cursor and `cvis` hides the cursor
    `smcup` starts alt display and `rmcup` ends alt display
    `sc` saves the cursor and `rc` loads the cursor
    `cmd&disown` is equivalent to `cmd&disown $!`
    creation time is called `crtime` in EXT4 and `otime` in BTRFS
    at least GNU `ls` and OS X's `ls` do not display linefeeds in filenames as question marks if the output is not to a terminal
    busybox `ls` replaces non-printable characters with question marks even if the output is not a terminal
    if `C-\` does not work, you can try to press `C-z` and run `kill %1` or `kill -9 %1`
    LSB specifies `at -r` but not `at -d` and POSIX specifies `at -d` but not `at -r`
    `/etc/localtime` is often a symlink to a file in `/usr/share/zoneinfo`
    bash, csh, and nksh emulate cooked mode keybindings but zsh, tcsh, and fish do not
    `7zr` only supports 7z, `7z` also supports other archive formats but not rar, and `7za` also supports rar
    `TERM=xterm tput el1|cat -v` prints `^[[1K` and `TERM=xterm infocmp -1|grep 1K` prints `el1=\E[1K,`
    `7z a` creates an archive or adds files to an archive, `7z e` extracts an archive, and `7z l` lists the contents of an archive
    Linux blocks reads from `/dev/random` when the entropy pool is empty but FreeBSD does not
    `1000` or `+t` is sticky, `2000` or `+g` is setgid, and `4000` or `+s` is setuid
    `MACHTYPE` (like `x86_64-apple-darwin13.1.0`) starts with `HOSTTYPE` (like `x86_64`) and ends with `OSTYPE` (like `darwin13.1.0`)
    `3>&4` and `3<&4` both duplicate fd 4 to fd 3
    `<&` duplicates input file descriptors and `>&` duplicates output file descriptors
    bash and zsh use the term word splitting but POSIX uses the term field splitting
    `gunzip` is equivalent to `gzip -d` and `zcat` is equivalent to `gzip -dc`
    in `top` `ocpu\r` orders by CPU use, `ca\r` sets the counting mode to accumulative, and `S\r1234\r` sends SIGINT to PID 1234
    `jobs -l` includes PIDs and `jobs -p` only prints PIDs
    `man -k` is equivalent to `apropos` and `man -w` is equivalent to `whereis`
    `trap '' INT` ignores SIGINT, `trap : INT` sets a signal handler that does nothing for SIGINT, and `trap INT` removes traps for SIGINT
    `/private/tmp` usually has the sticky bit set so users cannot delete files owned by other users
    you can make `sudo` not require a password by running `sudo visudo` and changing `%admin ALL=(ALL) ALL` to `%admin ALL=(ALL) NOPASSWD: ALL`
    `curl -O` creates a file for a URL to the current directory and `wget -O-` outputs to STDOUT
    `-O` only applies to the next argument in `curl`, so to download two URLs you can use `curl -O "$url1" -O "$url2"` or `printf %s\\n "$url1" "$url2"|xargs -n1 curl -O`
    `curl -R` enables preserving timestamps and `wget --no-use-server-timestamps` disables preserving timestamps
    `x=aa;y=bb;echo "${x/aa/"$y"}"` prints `bb` in bash 4.3 and later and in zsh but `"bb"` in older versions of bash
    `a=\\;b=\\;c=x;echo "${a//"$b"/"$c"}"` prints `x` in bash 4.3 and later and in zsh and `"x"` in older versions of bash
    `read x< <(printf x\\0x);echo $x` prints `xx` in bash 4.3 and later and in zsh but `x` in older versions of bash
    in shells other than zsh `IFS=0;set {1..101};echo $#` prints `1 1` but `IFS=0;set {1..101};echo "$#"` prints `101`
    at least in bash and zsh `x=0;f(){ x=1; };f;echo $x` prints `1` but `x=0;f(){ local x=1; };f;echo $x` prints `0`
    at least in bash and zsh `x=0;f(){ for x in 1;do :;done; };f;echo $x` prints `1` but `x=0;f(){ local x;for x in 1;do :;done; };f;echo $x` prints `0`
    SUSv2 included `tar` and `cpio` but SUSv3 only includes `pax`
    `shopt -s failglob` enables treating non-matching globs as errors in bash and `setopt nonomatch` disables treating them as errors in zsh
    `setopt interactivecomments` allows interactive comments in zsh and `shopt -u interactive-comments` disallows interactive comments in bash
    `tr -d \\` displays a warning like `an unescaped backslash at end of string is not portable` in GNU `tr`
    GNU `find` displays a warning when an option argument like `-mindepth` is placed after a non-option argument like `-type`
    `echo` interprets escape sequences by default in zsh but not in bash, ksh, or fish
    `fc -nl -1` includes a tab followed by a space at the start of the output in bash, a tab in ksh93, and nothing in zsh
    `id3v2` and `id3tag` do not support Unicode and `id3tool` does not support ID3v2 but `eyeD3` and `mid3v2` support both
    zsh, csh, tcsh, and fish treat globs without matches as errors by default
    the `-a` option in `cp` is equivalent to `-pRP` in coreutils and OS X but it is not specified by POSIX
    `od -x` is equivalent to `od -t x2` and `od -X` is equivalent to `od -t x4`
    if `file1` does not exist, `cat file1|cmd` runs `cmd` but `<file1 cmd` does not, and `cat file1|cmd>file2` clobbers `file2` but `<file1 cmd>file2` does not
    the option for setting a user agent is `-A` in `curl` and `-U` in `wget`
    `echo $'\cc'` prints `\003` in bash and ksh but not in zsh
    `curl $url|gzip -d` or `curl --compressed $url` decompresses a gzipped response
    the default `PATH` is `/usr/bin:/bin:/usr/sbin:/sbin` in OS X and `/bin:/usr/bin:/sbin:/usr/sbin` in Debian
    at least in OS X's `sort` and GNU `sort`, `sort` is often much faster the C locale than in a UTF-8 locale but `sort -n` is usually as fast in a UTF-8 locale as in the C locale
    `find . -mtime +0` finds files that were modified 24 hours ago or earlier and `find . -mtime -1` finds files that were not modified 24 hours ago or earlier
    array indexing starts at zero in ksh and bash and at one in zsh, csh, tcsh, fish, rc, es, and yash
    the man page of GNU `ls` describes `-f` as `do not sort, enable -aU, disable -ls --color` and `-U` as `do not sort; list entries in directory order`
    `ls -U` is not in POSIX and in OS X's `ls` it makes `-t` and `-l` use creation times instead of modification times, but `ls -f` is in POSIX as an XSI extension
    in Android there is no `/bin` directory and `sh` is in `/system/bin/sh`
    POSIX:2008 (The Base Specifications, Issue 7) is a subset of SUSv4 (Single UNIX Specification, Version 4)
    SUS includes the extensions to POSIX that are identified by the XSI (X/Open System Interfaces) option, and SUS also requires support for certain features such as file synchronization that are optional in POSIX
    Debian previously attempted to conform to the LSB with several limitations, but in 2015 the Debian project announced that it no longer intends to conform to the LSB
    at least GNU `xargs` and OS X's `xargs` skip empty lines by default, but `xargs -d\\n` does not skip empty lines in GNU `xargs`
    old versions of POSIX required `sort +1` to be equivalent to `sort -k2` but POSIX.1-2001 and later require it to sort a file named `+1`
    `wget --content-disposition` and `curl -OJ` use filenames specified in Content-Disposition headers
    `local` and `declare` accept the same options in bash but in zsh `local` does not accept `-f` or `-g` but `declare` does
    `set -e;f(){ local x=$(exit 1); };f` does not make the shell exit but `set -e;f(){ local x;x=$(exit 1); };f` does in zsh and bash
    in GNU `stat` `stat -c%F` displays the type of a regular file that is not empty as `regular file` and of a regular file that is empty as `empty regular file`
    in GNU `sed` a period in a regular expression does not match an illegal byte sequence, and in OS X's `sed` an illegal byte sequence in the input results in a fatal error
    adding `-X post` or adding a `-d` or `--data-urlencode` option makes `curl` send a POST request
    `[[:blank:]]` is equivalent to `[ \t]` in the POSIX locale but it matches characters like ideographic space in UTF-8 locales in at least OS X
    
    #bash
    invoking bash as `sh` enables `xpg_echo` but only invoking bash in POSIX mode does not
    invoking bash as `sh` makes `FCEDIT` default to `ed` instead of `EDITOR` or `ed` but only invoking bash in POSIX mode does not
    process substitution is not available when bash is invoked as `sh`
    `f(){ for x;do :;done; };type f` shows `for x` as `for x in "$@"`
    if both `~/.profile` and `~/.bash_profile` exist, bash only reads `~/.bash_profile` when it is invoked as a login shell
    `set -e;i=0;let i++` makes the shell exit but `set -e;i=-1;let i++` does not
    `source` is a special builtin but not specified by POSIX
    `printf '%()T'` prints the current time in bash 4.3 and later and `printf '%()T' -1` prints the current time in bash 4.2 and later
    `BASHOPTS` includes options settable with `shopt` and `SHELLOPTS` includes options settable with `set -o`
    you can use `type` to show the definition of a function or an alias and `declare -p` to show the definition of a variable
    `shopt -s cdable_vars;export sp=/some/path` makes `cd sp` change the directory to `/some/path`
    `\C-x\C-r` (`re-read-init-file`) or `bind -f ~/.inputrc` reloads `.inputrc`
    `\eg` (`glob-complete-word`) completes a glob, `\C-x*` (`glob-expand-word`) inserts the results of a glob expression, and `\e=` (`possible-completions`) lists completions
    you can complete a filename that contains a substring by typing `*substring\eg` or `*substring*\t`
    you can use `\C-x\C-x` (`exchange-point-and-mark`) to move the point back to where it was before text was yanked
    `\e\C-y` is `yank-nth-arg`
    `\e2\e\C-y` or `\e2\e.` inserts the second argument of the previous command but `\e2\e.` also cycles through previous history entries when it is pressed repeatedly
    `\e0\e.` inserts the zeroth argument of the previous command and `\e-\e.` inserts the second last argument of the previous command
    `\C-w` deletes a whitespace-delimited word backward
    `\e\C-e` (`shell-expand-line`) can be used to replace a command substitution with the result
    `\e/` (`complete-filename`) can be used to complete filenames when tab completes something else or to list files in the current directory when the current word is empty
    `3>&4-` is short for `3>&4 4>&-` or it makes FD 4 a copy of FD 3 and closes FD 4
    `compgen -b` or `enable` lists builtins, `compgen -A function` or `declare -F` lists functions, `compgen -a` or `alias` lists aliases, and `compgen -e` or `export` lists exported variables
    the bash manual defines a subroutine as a shell function or a shell script executed with `source`
    each compound command begins and ends with a reserved word or a control operator
    `{` and `}` are called reserved words and `(` and `)` are called control operators
    a non-interactive login shell whose parent process is `sshd` or `rshd` sources `~/.bashrc` but not `~/.bash_profile`
    the default value for `TIMEFORMAT` corresponds to `$'\nreal\t%E\nuser\t%U\nsys\t%S'`
    `COMP_LINE`, `COMP_POINT`, `COMP_KEY`, and `COMP_TYPE` are set for commands and functions but `COMP_WORDS` and `COMP_CWORD` are only set for functions
    `set +m` disables job control, `set +H` disables history expansion, and `set +B` disables brace expansion
    `kill` is a builtin so it recognizes job designators and it can be run even when the maximum number of processes has been reached
    `{ cmd1;cmd2; }|cmd3` is equivalent to `(cmd1;cmd2)|cmd3` because each part of a pipeline is run in a subshell
    `!!:*` removes argument 0, `!!:2*` removes arguments 0 and 1, and `!!:2-` removes arguments 0, 1, and -1
    `~+` is the same as `$PWD`, `~-` is the same as `$OLDPWD`, and `~2` is the same as `dirs +2` or `${DIRSTACK[2]}`
    you can add history entries from one shell to another shell by running `history -w` in the first shell and running `history -r` in the other shell
    `help` shows usage for all builtins, `help \*` prints all help messages, and `help variables` describes some shell variables
    `HISTFILESIZE= HISTSIZE= HISTFILE=~/.h` enables unlimited history and uses a `HISTFILE` that does not get truncated by a shell with the default `HISTFILESIZE`
    you can make a script source `~/.bashrc` by adding `-i` to the interpreter line
    `declare -A a[x]=;[[ ${a[x]} ]];echo $?` prints `1` but `declare -A a[x]=;[[ ${a[x]+_} ]];echo $?` prints `0`
    in an arithmetic context for example `${a[1]}` can be replaced with `a[1]`
    when `dotglob` is enabled, `*` does not include `.` or `..` but `.*` does
    when `GLOBIGNORE` is set to a non-empty value, `.*` does not include `.` or `..` but `dir/.*` includes `dir/.` and `dir/..`
    `shopt -s checkwinsize` is included in `/etc/bash.bashrc` in Ubuntu and in `/etc/bashrc` in OS X, neither of which is a standard bash configuration file but is sourced from other configuration files
    you can run a function defined in `~/.bashrc` with cron by adding a line like `* * * * * bash -ic cmd` to crontab
    when `HISTFILE` is large enough, `PROMPT_COMMAND='history -a;history -c;history -r'` may add a noticeable delay before the prompt is displayed but `PROMPT_COMMAND='history -a;history -n'` usually does not
    
    #POSIX
    a collating element is defined as a character, a multi-character sequence, or a collating sequence name
    a background process is defined as a process that is the leader of a background process group
    an equivalence class is defined as a set of collating elements with the same primary collation weight
    `.` is specified but `source` is not
    `select` and `PS3` are not specified
    `=` is specified in `test` expressions but `==` is not
    `-a`, `-o`, and parentheses are deprecated in `test` expressions
    the result of a `test` expression with four or more arguments is said to be unspecified
    an interactive shell expands `a*` in `cat foo>a*` if the expansion is a single word
    `$x` is not expanded in `true||$x`
    the `sh` binary is not required to be in `/bin/sh`
    `(` and `)` are called control operators and `{` and `}` are called reserved words
    in the POSIX locale, `[[:graph:]]` is equivalent to `[!-~]`, `[[:print:]]` to `[ -~]`, `[[:space:]]` to `[ \f\n\r\t\v]`, and `[[:blank:]]` to `[ \t]`
    `xxd` and `hexdump` are not specified but `od` is
    the only keywords specified for `tput` are `clear`, `init`, and `reset`
    `-r` is the only option specified for `read`, `-n` for `head`, and `-u` for `date`
    both `TZ=xxx-1` and `TZ=UTC-1` define a new timezone whose offset to UTC is -1, where the strings `xxx` and `UTC` are meaningless, but the string cannot be two or one characters long or omitted
    the `%s` format specifier for `date` is not specified
    `sed` is required to support line lengths of at least 8192 bytes for the pattern space and hold space
    `tail -c` is specified but `head -c` is not
    `++` and `--` are not specified in an arithmetic context
    
    #awk
    `\n` is always a field separator when `RS` is set to an empty string
    an `END` block is ran even after an `exit` statement
    variables placed after a command are not available in a `BEGIN` block but variables passed with `-v` are
    `system` is called a function in the manuals of `nawk` and `mawk` and a statement in the manual of `gawk`
    
    #jq
    `.array[-1]` selects the last element of an array in new versions of jq but `.array[length-1]` also works in old versions of jq
    `.aa//empty` returns nothing instead of `null` if `.aa` does not exist
    `.items[]|select(.tags-["aa","bb"]==.tags)` selects items whose tags do not include `aa` or `bb`
    `not .aa` is not true if `.aa` is null but `.aa==null` is
    `[]?` does not result in a `Cannot iterate over null` error when `[]` does
    `.[1]` cannot be used to get the first character of a string but `.[:1]` can
    you can add `sed '1{/^[^{[]/Q}'` before a jq command to skip non-JSON input without an error message

## Unsorted new single line commands

    pr -t -w $(tput cols) -4 # print lines in four columns (-t disables printing header and footer lines and does not add empty lines to the end)
    d=2014-12-01;while [[ ! $d > 2015-02-28 ]];do echo $d;d=$(gdate -d$d+1day +%F);done # print a range of dates
    xml sel -t -m //xx -v child1 -o , -v child2 -n # print child1 and child2 subelements of xx elements separated by a comma (-n prints a newline)
    xml sel -t -v '//li[@id="someid"]/a/@href' # print a list of attributes
    xml ed -u //xx -v newvalue file.xml # set the value of xx elements to newvalue (-u is update)
    xml ed -u '//xx[@value="yes"]/@value' -v no # change the value of attributes
    a2p # translate awk to perl
    toe # list terminal types in the terminfo database
    curl -s 'https://en.wikipedia.org/w/api.php?action=query&prop=revisions&rvprop=content&format=json&titles=Example'|jq -r '.query.pages[].revisions[]."*"' # get the WikiText source of a Wikipedia article
    curl -s 'https://en.wikipedia.org/wiki/Example?action=render'|pandoc -f html -t markdown_strict --no-wrap --atx-headers # convert a Wikipedia article to Markdown (action=render removes all parts of the layout except the main part for the article)
    grep '[^ -~]' -r # find files that include characters other than printable ASCII characters and linefeed
    tr -d \\0-\\177|recode ..dump # show the code points and Unicode names of non-ASCII characters
    smartypants|recode html # convert straight quotes to smart quotes (`recode html` unescapes HTML escape sequences)
    awk '{if(x&&/./)$0="  "$0;x=/./}1' # indent all lines except the first line in each paragraph
    gfind dir1 -mindepth 1 -maxdepth 1 -printf '%TY%Tm%Td%TH%TM%TS %f\n'|while read x y;do touch -t ${x%.*} dir2/$y;done # set the modification time of each file in dir2 to the modification time of the file with the same basename in dir1
    lynx -dump https://www.youtube.com/user/username/videos|grep -ao 'http.*watch.*'|awk '!a[$0]++'|vlc - # create a VLC playlist for recent videos uploaded by a YouTube user
    cat input.md|curl -s -HContent-Type:text/plain -d@- https://api.github.com/markdown/raw # convert GitHub Flavored Markdown to HTML
    nice bash -ic somefunction # use nice to run a function defined in ~/.bashrc
    awk 'gsub(/pattern/,"&")==1' RS= # print paragraphs that match a pattern once but not more times
    <urls.txt parallel -j100 wget -P/tmp/a;grep substring -r /tmp/a # download a list of URLs and search for a substring in their contents
    while read l;do open 'https://www.google.com/search?q='$(printf %s "$l"|xxd -p|tr -d \\n|sed 's/../%&/g');sleep 1;done # open a Google search page for each line from STDIN
    awk 'NR==FNR{a[$0];next}$0 in a{print FNR}' file1 file2|tail -n1 # print the line number of the last line of file2 that is also a line of file1
    awk 'NR==FNR{a[tolower($0)];next}!(tolower($0)in a)' <(look .) wordlist.txt # find words that are not in the dictionary file
    awk '!($0==prev&&/pattern/);{prev=$0}' # remove consecutive duplicate lines that match a pattern
    fmt -w2500 # revert the effects of fold (GNU fmt exits with an error for values of 2501 and higher but OS X's fmt doesn't)
    awk -v RS= -F\\n '{if(NF==1)x=x$0"\n\n";else print$0"\n"}END{print x}'|sed \$d # move paragraphs that contain a single line after paragraphs that contain two or more lines
    man zshall # show all sections from the zsh manual
    man zshbuiltins # show the section for builtins from the zsh manual (zsh has no help builtin)
    awk 'NR>1&&$0<=x{exit 1}{x=$0}';echo $? # test if a list of numbers is strictly increasing
    perldoc perlrun # see documentation for perl options
    grep -f- file.txt # grep lines from STDIN
    stty -a # display all settings
    man termios # show documentation for the flags for stty
    stty -ixon # do not use control-q and control-s for flow control so that control-s can be used for forward incremental search
    stty -echoctl # do not for example print ^C when pressing control-c and do not display control characters in typeahead text
    jq -s add *.json # merge JSON files
    jq -s '.[0]*.[1]' [12].json # merge two JSON files recursively (the * operator is overloaded to merge objects recursively)
    jq -s 'reduce.[]as$x({};.*$x)' *.json # merge multiple JSON files recursively
    cat *.jpg|ffmpeg -f image2pipe -r 1 -c:v mjpeg -i - -c:v libx264 output.mp4 # create a video where each image is displayed for one second
    fold -sw60|sed "s/^/$(printf %$((COLUMNS/2-30))s)/" # fold text and center the block of text
    xpstopdf input.xps output.pdf # convert xps to pdf (xpstopdf is part of libgxps)
    (gsed -u 10q|tac;cat) # reverse the first ten lines (-u is unbuffered in GNU sed)
    awk -F\` NF%2==0 # print lines that contain an odd number of backticks
    curl https://tiswww.case.edu/php/chet/bash/CHANGES|less # read the bash changelist
    metaflac input.flac --show-tag=ARTIST|sed 's/.*=//' # display the artist tag of a FLAC file
    ogginfo file.ogg # display the metadata of an OGG file
    awk NF==2 RS= FS=\\n # print the lines of paragraphs that contain two lines
    parallel --tag 'curl -Ls {}|tr -d \\n|grep -Pio "<title>\K[^<]*"' # add titles to a list of URLs (--tag prepends the argument to each output line)
    pdfimages -j file.pdf nameprefix # extract images from a PDF (pdfimages is part of poppler)
    aria2c file.torrent # download a torrent
    awk 'NR==FNR{a[$0];next}$0 in a{exit}1' file1 file2 # print the lines of file2 up to but not including the first line that is also a line of file1
    curl -LH 'Accept:text/bibliography;style=bibtex' https://dx.doi.org/$doiname # convert a DOI name to BibTeX
    gsed 's/Example/\L&/g' # convert all instances of a substring to lowercase
    printf %s\\n "The current time is $(tput sc)$(date +%T).";while sleep 1;do printf %s "$(tput rc)$(date +%T)";done # use the save cursor and restore cursor escape sequences to change a part of previously printed text
    tput cup $(tput lines) 0 # move the cursor to the last line
    curl ringtail.its.monash.edu.au/pub/nihongo/edict.gz|gzip -d|iconv -f euc-jp -t utf-8|grep example # search in a Japanese dictionary
    shellcheck file.sh # check for common mistakes and pitfalls in a bash, POSIX shell, or ksh script
    youtube-dl --default-search=ytsearch -x 'Artist - Title' # download the first YouTube search result and use -x to extract audio
    xml el # display the element structure of an XML document (add -u to print sorted and unique lines or add -a to include attributes)
    read -d '' -t0.001 -n99999;printf '\e[2K\r' # discard the typeahead buffer and clear the current line in bash
    awk 'NR>1{print$0-x}{x=$0}' # print the difference from the previous line on each line except the first line on a list of numbers
    gawk -vRS= -F\\n -vOFS=\\n 'NR==1{x=$NF;NF--}NR==2{$(NF+1)=x}{printf"%s",$0 RT}' # move the last line of the first paragraph to the end of the second paragraph
    awk -v RS= -F\\n '{for(i=NF-4;i<=NF;i++)print$i}' # print the last five lines of each paragraph
    awk '{if(length<=80)print;else x=x$0"\n"}END{printf"%s",x}' # print lines whose length is 81 characters or more after other lines
    awk 'NR==FNR{++a[$2];next}a[$2]==1' file{,} # print the lines whose second field is the second field of only one line in a file
    awk '/pattern/&&++i==3{getline;print;exit}' # print the line after the third line that matches a pattern
    uawk '/^$/{if(++n<=2)print;next}{n=0}1' # replace three or more consecutive empty lines with two empty lines
    sort -n|awk '{a[NR]=$0}END{print(NR%2==1)?a[int(NR/2)+1]:(a[NR/2]+a[NR/2+1])/2}' # print the median of a list of numbers
    awk '!($0 in a)||a[$0]<NR-11;{a[$0]=NR}' # remove lines that appeared before within the previous ten lines
    awk -v size=5 '{mod=NR%size;if(NR<=size){count++}else{sum-=a[mod]};sum+=$1;a[mod]=$1;print sum/count}' # display the moving average of a list of numbers
    gawk '$1 in a&&!($2 in a[$1]);{a[$1][$2]}' # print lines whose first field is the first field of one or more previous lines but whose second field is not the second field of any of those lines (nawk and mawk do not support arrays of arrays)
    awk '$2 in a;{a[NR%3]=$2}' # print lines whose second field was the second field of one of the three previous lines
    curl www.unicode.org/Public/UCD/latest/ucd/Blocks.txt # look up the names and the first and last code points of Unicode blocks
    echo a>/dev/ttys002 # display text in another terminal
    ffmpeg -ss 0:12:34 -t 5 -i input.mp4 -f image2 /tmp/%04d.png;convert -delay 3 -fuzz 1.5% -layers optimize /tmp/*.png output.gif # convert a part of a video to a gif
    eyeD3 --remove-image file.mp3;eyeD3 --add-image=cover.jpg:FRONT_COVER file.mp3 # replace the cover art of an mp3 file
    mp4info file.m4a # show information about an aac file (mp4info is part of mp4v2)
    mp4art --overwrite --add cover.jpg file.m4a # replace the cover of an aac file (mp4art is part of mp4v2)
    find . -name \*.flac|parallel ffmpeg -i {} -aq 2 {.}.mp3\;rm {};find . -name \*.cue|parallel mp3splt -c {} {.}.mp3 # convert flac to mp3 and split files with cue files
    youtube-dl $playlist_id_or_url # download all videos on a YouTube playlist
    youtube-dl https://www.youtube.com/user/username # download all videos uploaded by a YouTube user
    curl -sG --data-urlencode artist='Some Artist' "ws.audioscrobbler.com/2.0/?api_key=$apikey&format=json&method=artist.getsimilar"|jq -r '.similarartists.artist[].name' # get similar artists for an artist from Last.fm
    curl -sG --data-urlencode artist='Some Artist' "ws.audioscrobbler.com/2.0/?api_key=$apikey&format=json&method=artist.getTopTracks"|jq -r '.toptracks.track[].name' # list the top tracks of an artist from Last.fm
    curl -L 'someblog.blogspot.com/atom.xml?start-index=1&max-results=150'|grep -Pio 'href=&quot;\K[^&]*'|egrep '\.(jpg|jpeg|png)' # list the URLs of images posted to a Blogger blog
    zip directory.zip -x \*.DS_Store -r directory # create a zip archive and exclude .DS_Store files (`-x .DS_Store` does not exclude .DS_Store files inside directories but `-x \*.DS_Store` does)
    7z e *.7z # extract 7z archives (7z is part of p7zip)
    lame --preset help # show help about lame presets (-V2 or -V0 is recommended over -b320)
    for p in insane extreme standard medium;do flac -cd input.flac|lame --preset $p - $p.mp3;done # test lame presets
    flac *.wav # convert wav to flac
    sox input.wav output.wav reverse # reverse audio
    sox input.wav output.wav repeat 3 # loop audio four times
    sox input.wav output.wav pitch 1200 # transpose audio one octave up
    flac -cd file.flac|sox - -n spectrogram # create a spectrogram image
    comm -13 <(ssh user@host 'ls /{usr/,}{s,}bin'|sort -u) <(ls /{usr/,}{s,}bin|sort -u)|while read l;do apropos $l|grep -v 'nothing appropriate'|grep ^"$l("|sed 's/([^ ]* *-/:/';done # print the apropos descriptions of commands that are in /{usr/,}{s,}bin on the current system but not on another system
    seq 0 20 $((x+19)) # print each multiple of 20 from 0 to $x rounded up to a multiple of 20
    sort -n -sf # use both numeric sorting and case-insensitive sorting (-f has no effect in `sort -nf`)
    man pcre # list man pages related to PCRE
    convert -size 1280x720 xc:black black.png;ffmpeg -loop 1 -i black.png -i audio.m4a -c:a copy -shortest output.mp4 # create a black video for an audio file
    node # start a JavaScript REPL
    gsed '$!axx' # insert xx between each line
    ffmpeg -i input.mp4 -f lavfi -i anullsrc=channel_layout=stereo:sample_rate=44100 -shortest -c:a libfdk_aac -c:v copy output.mp4 # add a silent audio track to a video (=channel_layout=stereo:sample_rate=44100 can be omitted)
    ffmpeg -i input.mp4 -r 0.05 %06d.png # create an image for a frame of a video every 20 seconds
    mkvmerge output.mkv file1.mkv + file2.mkv + file3.mkv # concatenate mkv files losslessly (mkvmerge is part of mkvtoolnix)
    ruby -pe'$_.gsub!(/\\u(....)/){[$1.hex].pack("U")}' # replace \u escape sequences with literal characters
    grep -Po '\p{Han}|\p{Katakana}|\p{Hiragana}|\p{Hangul}' # search for CJK characters (the script names are listed in `man pcrepattern`)
    convert *.jpg -adjoin output.pdf # create a PDF where each page consists of one image
    ffmpeg -activation_bytes $activationkey -i input.aax -c:a copy -vn output.m4a # remove encryption from an Audible audiobook after getting an activation key with audible-activator
    mkvmerge -o prefix.mkv --split chapters:all input.mkv # split an mkv file into chapters
    mpv https://www.youtube.com/watch?v=$videoid # watch a YouTube video
    ffmpeg -i input.mp4 -filter:a volume=0.5 -c:v copy output.mp4 # reduce volume

## Notes

    ### Differences between function syntaxes
    
    In POSIX, the `function` keyword is not specified and it is not included on the list of reserved words, but it is however included on a list of a few words that "may be recognized as reserved words on some implementations".
    
    POSIX specifies the `f(){ :; }` syntax where `{ :; }` can be any compound command, so that `f()(echo a)` and `f()for x;do echo $x;done` are also valid.
    
    The `function` keyword was introduced by Ksh and the `f(){ :; }` syntax was introduced by the Bourne shell. Ksh supported functions before the Bourne shell.
    
    The syntax that includes both the `function` keyword ant the parentheses (such as `function f(){ :; }`) results in an error in at least Ksh93, Dash, Csh, Tcsh, and in old versions of Zsh.
    
    The `f()echo a` syntax where `echo a` is a simple command is supported by the Bourne shell and by most Bourne-like shells but not by Bash.
    
    In Ksh `typeset` makes a variable local inside a function defined using the `function` keyword but not inside a function defined without the `function` keyword:
    
        $ ksh -c 'x=a;function f { typeset x=b; };f;echo $x'
        a
        $ ksh -c 'x=a;f(){ typeset x=b; };f;echo $x'
        b
    
    In Ksh a function defined using the `function` keyword has its own trap context, so that traps defined outside the function are ignored when the function is executed, and a fatal error during the execution of the function causes only the function and not the whole script to exit.
    
    In Ksh `$0` is the name of the function in a function defined using the `function` keyword but the name of the script in a function defined without the `function` keyword, so that `ksh -c 'function f { echo $0; };f'` prints `f` but `ksh -c 'f(){ echo $0; };f'` prints `ksh`.
    
    With `shopt -s extglob` in Bash, `@(){ :; }` and `a+(){ :; }` result in a syntax error but `function @ { :; }` or `function a+ { :; }` does not, even though `@ (){ :; }` or `a+ (){ :; }` does not either. `@(){ :; }` or `a+(){ :; }` does not result in an error with `setopt ksh_glob` in Zsh.
    
    ### Grepping STDERR
    
    Grep STDERR and preserve STDOUT:
    
        $ (seq 2 >&2;echo 3)2> >(grep 1)
        1
        3
        $ (seq 2 >&2;echo 3)3>&1 1>&2 2>&3|grep 1 3>&2 2>&1 1>&3
        1
        3
    
    Discard STDOUT, redirect STDERR to STDOUT, and grep STDIN:
    
        $ (seq 2 >&2;echo 3)2>&1 >/dev/null|grep 1
        1
    
    In the command above `2>&1 >/dev/null|` can be replaced with `>/dev/null 2>&1|` or `>/dev/null|&` in Zsh but not in Bash.
    
    Redirect STDERR to STDOUT and grep STDIN:
    
       $ (seq 2 >&2;echo 3)|&egrep 2\|3
       2
       3
    
    `|&` is a shorthand for `2>&1|` in at least Zsh and Bash.
    
    ### Using fzf to search Bash history
    
        bind -x '"\er":history -n;READLINE_LINE=$(history|sed "s/ *[^ ]  //"|fzf -e +s --tac);READLINE_POINT=${#READLINE_LINE}'
    
    `-e` (`--exact`) includes only non-fuzzy matches. `+s` disables sorting the results, because the default behavior is to sort the results primarily by a fuzzy matching score and secondarily by length, and when `-e` is specified, the results are simply sorted by length.
    
    ### Concatenating videos
    
    If all of the input videos have the same dimensions and have been encoded with the same settings, you may be able to use a command like this:
    
        ffmpeg -f concat -safe 0 -i <(printf "file '%s'\n" ~/dir/*.mp4) -c:v libx264 -preset slow -crf 22 -pix_fmt yuv420p -c:a libfdk_aac -vbr 3 output.mp4
    
    Without `-safe 0`, new versions of `ffmpeg` exit with an error when the path of some input file is absolute or when some filename component of a path contains characters other than ASCII alphanumeric characters, period, underscore, or hyphen.
    
    To resize the input videos to 1280 by 720 pixels when all input videos do not have the same dimensions, add this option:
    
        -vf 'scale=iw*min(1280/iw\,720/ih):ih*min(1280/iw\,720/ih),pad=1280:720:(1280-iw*min(1280/iw\,720/ih))/2:(720-ih*min(1280/iw\,720/ih))/2'
    
    When I tried using the `ffmpeg` command with the option above to concatenate videos from different sources that were encoded with different settings, the audio in the output files was sometimes out of sync and sometimes missing completely. I therefore used `mkvmerge` from mkvtoolnix instead. I first used `ffmpeg` to resize and re-encode the input videos:
    
        for f in *.mp4;do width=1280;height=720;ffmpeg -y -i $f -filter:v "scale=iw*min($width/iw\,$height/ih):ih*min($width/iw\,$height/ih), pad=$width:$height:($width-iw*min($width/iw\,$height/ih))/2:($height-ih*min($width/iw\,$height/ih))/2" -c:v libx264 -crf 22 -preset slow -pix_fmt yuv420p -c:a libfdk_aac -vbr 3 -ac 2 -ar 44100 ${f%mp4}mkv;done
    
    Some of my input videos did not have an audio channel, so I had to use a command like this to add a silent audio channel to them:
    
        for f in *.mkv;do ffprobe $f|&grep -q '1: Audio'||{ ffmpeg -i $f -f lavfi -i anullsrc -c:a libfdk_aac -shortest -c:v copy temp-$f;mv temp-$f $f; };done
    
    I then concatenated the videos using a command like this:
    
        mkvmerge -o /tmp/output.mkv $(printf %s\\n *.mkv|sed '1!s/^/+/')