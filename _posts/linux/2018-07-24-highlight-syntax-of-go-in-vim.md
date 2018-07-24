### vim-go

Get https://github.com/fatih/vim-go and follow instruction on it.

For vim lower than 7.5 pathogen is needed.

### pathogen

Installation
Install to ~/.vim/autoload/pathogen.vim. Or copy and paste the following into your terminal/shell:
``` shell
mkdir -p ~/.vim/autoload ~/.vim/bundle && \
curl -LSso ~/.vim/autoload/pathogen.vim https://tpo.pe/pathogen.vim
```

If you're using Windows, change all occurrences of ~/.vim to ~\vimfiles.

Runtime Path Manipulation
Add this to your vimrc:
``` shell
execute pathogen#infect()
```
If you're brand new to Vim and lacking a vimrc, vim ~/.vimrc and paste in the following super-minimal example:
```
execute pathogen#infect()
syntax on
filetype plugin indent on
```

Then:
```
 git clone https://github.com/fatih/vim-go.git ~/.vim/bundle/vim-go
```

### more resources
https://github.com/golang/go/wiki/IDEsAndTextEditorPlugins
