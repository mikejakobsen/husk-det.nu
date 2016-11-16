# Git and FZF

One of the main reasons, why i use VIM on a daily basis. Is the connectivity with the command-line.

Many Vim users, use a GUI version of Vim like MacVim or NeoVim. Mainly because the interface has all the similarities
you'll normally get from a Text editor like Sublime Text ore Visual Studio Code.

And off cause the ability to save, just by using Ctrl+S instead of :w all the time.

But the main reason why i use Vim from the command-line, is the ability to use FZF.

FZF is a file explorer based on ACK/Grep depending on your setup.


Type "fe" to get a fuzzy finder searcher directly from the command-line.


# fe [FUZZY PATTERN] - Open the selected file with the default editor
#   - Bypass fuzzy finder if there's only one match (--select-1)
#   - Exit if there's no match (--exit-0)
fe() {
  local files
  IFS=$'\n' files=($(fzf-tmux --query="$1" --multi --select-1 --exit-0))
  [[ -n "$files" ]] && ${EDITOR:-vim} "${files[@]}"
}

And fshow will let you search though all the git commit in the branch.

In the image below, you'll see my dotfiles. With nearly 200 commits, the git history becomes difficult to manage.
That's where the 'fshow' command really shines.

# fshow - git commit browser
fshow() {
  git log --graph --color=always \
    --format="%C(auto)%h%d %s %C(black)%C(bold)%cr" "$@" |
  fzf --ansi --no-sort --reverse --tiebreak=index --bind=ctrl-s:toggle-sort \
    --bind "ctrl-m:execute:
  (grep -o '[a-f0-9]\{7\}' | head -1 |
  xargs -I % sh -c 'git show --color=always % | less -R') << 'FZF-EOF'
  {}
  FZF-EOF"
}


FZF https://github.com/junegunn/fzf
