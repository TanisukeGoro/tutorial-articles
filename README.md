# fish と Tmux を使って良い感じのターミナル環境を構築する  

お前らのターミナルはクソ雑魚なのでこれをみて悔い改めよ。  

#### やること  

- [ ] fishの導入  
  - [ ] fishのインストールとか  
  - [ ] fishプラグイン管理ツール`oh-my-fish`の導入  
  - [ ] fishレイアウトを変更, フォントの導入  
  - [ ] さらに良い感じのレイアウトにするために`starship`を導入  
  - [ ] VSCodeのターミナルのフォントを変更  
- [ ] tmuxの導入  
  - [ ] tmuxのインストールとか  
  - [ ] tmuxプラグイン管理ツールの導入  
  - [ ] tmuxの基本的な使い方
- [ ] fish や tmux を使いこなすためのプラグイン導入や設定の変更  
  - [ ] peco, cd, docker-machine, ghq, tigなどを入れる。
  - [ ] nodeやpyenvなどのpathが通らなくなっているはずなので再設定する

## fishとは?  

fish はzshやbashと同じシェルの一種  

`zsh`よりも軽量でかつ補完が強力(gitのブランチ名とかも予測変換に)
`fish`を使い始めたらもう戻れない。  

## tmuxとは?  

ターミナルを良い感じにするためのアプリ  
`iTerm`を使ってる奴は雑魚  

- ターミナルを複数開くことなく、一画面にいくつものペインを作成したりできる。  
- セッションを管理することでターミナルを消してしまっても元の状態に復元することが可能  

## インストール  

```bash  
# install fish   
brew install fish  

# install fish  
brew install tmux  
```  
入ったか確認  

```bash   
fish -v  
# fish, version 3.0.2  

tmux -V  
# tmux 3.0a  
```  
これでOK  


## ターミナルのデフォルトシェルを`bash`・`zsh`から`fish`に切り替える  

### シェルのパスを設定に追加する  

fishを追加してもTerminalはfishの起動アプリの場所を知らないので設定に追加する必要がある。  

まずは`fish`のインストールされたディレクトリを表示する  
```bash  
which fish  
# /usr/local/bin/fish  
```  

次に設定ファイルを開く  

```bash  
sudo vim /etc/shells  
```  
vimが開くので以下を追加  

```bash:etc/shells  
# List of acceptable shells for chpass(1).  
# Ftpd will not allow users to connect who are not using  
# one of these shells.  

/bin/bash  
/bin/csh  
/bin/ksh  
/bin/sh  
/bin/tcsh  
/bin/zsh  
/usr/local/bin/fish # <= これを追加する  
```  
`:wq`で保存して終了  

### デフォルトシェルを変更する  

```bash  
chsh -s /usr/local/bin/fish  
```  
これでターミナルを再起動すればOK  
再起動しら図のようになっているはず  

>  ref : [【2019年版】macのターミナルにFishとFishermanを導入する - Qiita](https://qiita.com/minako-ph/items/dba6d65b741e3a30ad16)  
> ![https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F468787%2F96fa9aa2-28c7-87d1-0314-76c6f79edc70.png?ixlib=rb-1.2.2&auto=format&gif-q=60&q=75&s=7cd1e6c7cc1c5841c3bb503b5b879aa5](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F468787%2F96fa9aa2-28c7-87d1-0314-76c6f79edc70.png?ixlib=rb-1.2.2&auto=format&gif-q=60&q=75&s=7cd1e6c7cc1c5841c3bb503b5b879aa5)  


## fishプラグイン管理ツール`oh-my-fish`を導入する  

fishを最大限に活用するためにプラグイン管理ツールを導入する  

`oh-my-fish`はfishのプラグイン管理用のソフトで、  

- nodeの`npm`  
- phpの`composer`  
- pythonの`php`  

と同じような立ち位置と考えて良さそう。  
[GitHub - oh-my-fish/oh-my-fish: The Fish Shell Framework](https://github.com/oh-my-fish/oh-my-fish)  

> 自分は基本的に`oh-my-fish`を使ってプラグインを導入しているけど、`Fisherman`(あるいは単に`fisher`)というのもある。  
> 以下にリポジトリを貼っておくので参照  
> [GitHub - jorgebucaran/fisher: A package manager for the fish shell.](https://github.com/jorgebucaran/fisher)  

### インストール  

```bash  
# oh-my-fish  
curl -L https://get.oh-my.fish | fish  

# ちなみにfishermanを使うならこちら  
curl https://git.io/fisher --create-dirs -sLo ~/.config/fish/functions/fisher.fish  
```  

### fishのテーマをインストールする  

ターミナルのレイアウトを良い感じにするためのテーマが大量にあるので、インストールして良い感じにする。  

[oh-my-fish/Themes.md at master · oh-my-fish/oh-my-fish · GitHub](https://github.com/oh-my-fish/oh-my-fish/blob/master/docs/Themes.md)  

ちなみにプラグインが管理されているリポジトリは`oh-my-fish`というリポジトリ  

テーマは好きなものを選択して入れれば良いと思うが、みんな`bobthefish`を使っているようなので、インストールする。  

```bash  
omf install bobthefish  
```  

インストールが完了したらターミナルを再起動するか`fish`とコマンドを叩く。  

するとレイアウトが変わっていると思うけど、文字化けも同時に起きているはず。  
この文字化けを解消するために、`powerline`というシェル用のフォントをインストールする。  

```bash  
git clone https://github.com/powerline/fonts.git  
cd fonts  
./install.sh  
```  

これでインストールは完了する。  

また以下を参考にターミナルのフォントを変更すること。  
フォントは`Roboto Mono Lght for Powerline`で良いと思う。  

> [【2019年版】macのターミナルにFishとFishermanを導入する - Qiita](https://qiita.com/minako-ph/items/dba6d65b741e3a30ad16#%E3%83%95%E3%82%A9%E3%83%B3%E3%83%88%E3%82%92%E5%A4%89%E6%9B%B4%E3%81%99%E3%82%8B)  


これでfishのインストール関連は終了。  

さらにfishを使いやすくするためにプラグインの導入や設定ファイルの変更を行う必要がある。  
これらは大体以下のようにして行う。  

```bash  
# 基本設定 (pathの追加など) や エイリアスの追加  
~/.config/fish/config.fish  

# 関数群の設定  
~/.config/fish/functions/functions.fish  

# プラグインのインストール  
omf install プラグイン名  
```  

## `Starship`の導入  

これは個人的な好みだが、ターミナルをよりインスタ映えさせるために`starship`を導入する。  

![34945188-1f6398be-fa0b-11e7-9845-a744bc3e148d.png](https://user-images.githubusercontent.com/3459374/34945188-1f6398be-fa0b-11e7-9845-a744bc3e148d.png)  

[Starship: Cross-Shell Prompt](https://starship.rs/)  

```bash  
# starshipのインストール  
brew install starship  
```  

fish で読み込めるようにするために、`~/.config/fish/config.fish`に以下の文を挿入する  

```bash  
# ~/.config/fish/config.fish  
 starship init fish | source  
```  

terminalで`fish`とコマンドを叩いて`fish`の設定を再読み込みさせればレイアウトがめっちゃ良くなっているはず。  

## Tmuxのプラグイン管理ツールを導入  

さっきは`fish`のプラグイン管理ツール`fisherman`を導入したので、次は`Tmux`のプラグイン管理ツールを導入する。  

`Tpm`を使おう。  

```  
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm  
```  

そして`tmux`の設定ファイルである、`.tmux.conf`を編集する  

```bash  
# tpm関連の導入  
set -g @plugin 'tmux-plugins/tpm'  
set -g @plugin 'tmux-plugins/tmux-sensible'  

## 中略  

# tpmを初期化して起動するために、最後にこの1行を挿入  
run '~/.tmux/plugins/tpm/tpm'  
```  

最後に次のコマンドをTerminalで叩くことで、tmuxをリロードする  

```  
tmux source ~/.tmux.conf  
```  

これでおけい。  
プラグインをインストールする際には、`.tmux.conf`に`set -g @plugin ~~~`と書けばOK  

> 詳細は以下のブログ記事が良いよ。  
> [tmuxメモ : tpmでtmuxのプラグインを管理 - もた日記](https://wonderwall.hatenablog.com/entry/2016/06/26/195329)  


## Tmuxの基本的な使い方

Tmuxをfishを活用するには、Terminal上で`tmux`を起動して、その上で`fish`を走らせるようにする。  
`tmux`はターミナルの機能を拡張し、`fish`は`bashやzsh`の機能を拡張している感じ。  

`tmux`には簡単に以下のような機能がある。

- Terminalのセッションを管理・保存
- 画面の分割(ペイン)
- コピー・ペースト(デフォルトで普通のcommand + c とかではできない) => 書くの面倒なのでわからなかったら聞いて

Tmuxを使うには`プレフィックス`という概念を覚える必要がある。  
`プレフィックス`と`キー`を組み合わせることでTmux自体を操作することが可能になる。  

実際に試してみるために、ターミナルを起動して`tmxu`と入力してみる。  
すると`tmux`が起動し、画面のしたにステータスが表示されるようになるはず。

この状態で

```
Ctrl + b (Ctrl は Macの ⌃ )
% (USキーボードのshift + 5)
```
を続けて入力します。すると、画面が縦に二分割されるはず。  
この時の`Ctrl + b`がプレフィックス。プレフィックス+キーでいろいろできるのでTmuxは便利〜

これ以外にも[キー操作](https://qiita.com/nmrmsys/items/03f97f5eabec18a3a18b#%E3%82%AD%E3%83%BC%E6%93%8D%E4%BD%9C)にいくつかコマンドが紹介されているので参照してみると良い。

基本的な操作はこんな感んじ。

## fishのおすすめのプラグイン導入

fishを使うなら`peco`や`ghq`などをインストールしてより使いやすくしたい。


### ghq => gitリポジトリ一元管理ツール

githubからリポジトリをクローンするとき、適当なディレクトリに移動して`git clone ~~~`してると思うけど、プロジェクトが多くなってくると管理が大変になってくるんだよね。

ディレクトリの移動とかわざわざFinderでフォルダを見に行ったりとか。

そんなの面倒なので、全てGHQの支配下に置いてしまえばリポジトリの管理が非常に楽になる。

#### インストール

```bash
brew install ghq
```

GithubリポジトリからのClone

```bash
# 単にcloneする
ghq get https://github.com/x-motemen/ghq

# あるいはsshなら
ghq get git@github.com:x-motemen/ghq.git

# ブランチを指定してclone
ghq get < repository > --branch < branch name >
```

ghqが真価を発揮するのは`peco`と`z`と連携したときであるので、次にpecoをインストールする

### peco => 履歴検索

え、まだailasを追加してドヤってんの？  
ailasは入力が固定され再現性に乏しいので、無闇に`ailas`を追加するのはナンセンス。  
`peco`を導入し、履歴から即座にコマンドを呼び起こす方針をとるのが吉。   

まあナンセンスは冗談だけど、`peco`を導入することでめちゃめちゃターミナルの操作が爆速になったのでおすすめ。  
ちなみにailas追加しまくってドヤってたのは俺。  


#### インストール

```bash
# Goで書かれたバイナリをインストールする
brew install peco

# fishで使えるようにする
omf install peco

# ついでにディレクトリ移動系のプラグインも導入する
omf install z
```

何も考えずに`~/.config/fish/functions/functions.fish`に以下のコードを挿入する。

```bash
# cd コマンドの履歴を表示して選択したらそのディレクトリに移動する
function peco_z
  set -l query (commandline)

  if test -n $query
    set peco_flags --query "$query"
  end

  z -l | peco $peco_flags --layout=bottom-up | awk '{ print $2 }' | read recent
  if [ $recent ]
      cd $recent
      commandline -r ''
      commandline -f repaint
  end
end

# ghq が管理しているGitリポジトリの一覧を表示して、選択したらそのディレクトリに移動する
function peco_ghq
    set -l query (commandline)

    if test -n $query
        set peco_flags --query "$query"
    end

    ghq list --full-path | peco $peco_flags --layout=bottom-up | read recent
    if [ $recent ]
        cd $recent
        commandline -r ''
        commandline -f repaint
    end
end

# キーバインドの追加
function fish_user_key_bindings
    bind \cr 'peco_select_history (commandline -b)' # プレフィックスの後に r キーを打つと履歴一覧
    bind \co peco_ghq # プレフィックスの後に o キーを打つとghq配下のgitリポジトリ一覧
    bind \cq peco_z # プレフィックスの後に q キーを打つと cd の履歴一覧
end
```
できたら`fish`コマンドを叩くか、再起動して設定をリロードする。

再起動したらコマンドが使えるようになっている

- `prefix` + r : コマンドの履歴を検索して呼び出す
- `prefix` + o : ghqで管理しているgitリポジトリを検索してディレクトリを移動
- `prefix` + q : cdコマンドで移動した履歴を検索してディレクトリを移動

こんな感じでめっちゃ使いやすくなったはず。

## 環境変数( PATH )が通らなくなっているので修正

nodeやpython, gemなどを使う際に、`~/.bash_profile`にプログラムのパスを書いているはずである。

例えば以下のように。

```bash
# .bash_profile
# Get aliases and a stack size from .bashrc
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi

# for pyenv
export PYENV_ROOT=${HOME}/.pyenv
if [ -d "${PYENV_ROOT}" ]; then
        export PATH=${PYENV_ROOT}/bin:$PATH
        eval "$(pyenv init -)"
        eval "$(pyenv virtualenv-init -)"
fi

# for gem
export GEM_HOME=$HOME/.gem
export PATH="$GEM_HOME/bin:$PATH"

# for rbenv
if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"

# for nodebrew
export PATH=$HOME/.nodebrew/current/bin:$PATH

LANG=ja_JP.UTF-8
ENV=$HOME/.bashrc

umask 022
# Prompt
PS1="\[\e[33m\]\W\[\e[36m\] > \[\e[m\]"
export LANG ENV PS1

export PATH="/usr/local/opt/icu4c/bin:$PATH"
export PATH="/usr/local/opt/icu4c/sbin:$PATH"

```

これを`~/.config/fish/config.fish`に書き換える必要がある。

パスの指定などをする際には`bash`の`exsport PATH`とかを`fish`の `set -x PATH`に置き換えていく。

大体以下のようにやればOK

```bash
set -x fish_greeting

# for pyenv

set -x PYENV_ROOT $HOME/.pyenv
set -x PATH $PATH $PYENV_ROOT/bin
source (pyenv init - | psub)
alias l 'ls'

# for gem
set -x GEM_HOME $HOME/.gem
set -x PATH $PATH $GEM_HOME/bin

# for rbenv
set -x PATH $PATH $HOME/.rbenv/bin
source (rbenv init - | psub)

# for nodebrew
set -x PATH $PATH $HOME/.nodebrew/current/bin
set -x theme_display_ruby no

# ~/.config/fish/config.fish
 starship init fish | source

set -x EDITOR /usr/local/bin/code

umask 022

# プロンプトの表示を書き換える
set -x fish_prompt_show_pwd_dir no
```



## Tmuxの設定変更  

自分は`tmux`を使いやすくするために`.tmux.conf`を以下のようにしている。  
まあこんな感じで良いと思う。  

```bash  
set -g @plugin 'tmux-plugins/tmux-open'  
set -g mouse on  
set -g terminal-overrides 'xterm*:smcup@:rmcup@'  
set -g @plugin 'tmux-plugins/tmux-urlview'  
set -g @plugin 'tmux-plugins/tmux-resurrect'  

# 新しいタブやペインを作成する際にカレントディレクトリを利用  
bind c new-window -c '#{pane_current_path}'  
bind '"' split-window -c '#{pane_current_path}'  
bind % split-window -h -c '#{pane_current_path}'  

# Mac OS X pasteboardを使用できるようにする  
set-option -g default-command "reattach-to-user-namespace -l fish"  

# コピーモードでvimキーバインドを使う  
setw -g mode-keys vi  

# 'v' で選択を始める  
bind-key -T copy-mode-vi v send-keys -X begin-selection  
bind-key -T copy-mode-vi y send-keys -X copy-pipe-and-cancel "reattach-to-user-namespace pbcopy"  

# `Enter` でもcopy-pipeを使う  
unbind -T copy-mode-vi Enter  
bind-key -T copy-mode-vi Enter send-keys -X copy-pipe-and-cancel "reattach-to-user-namespace pbcopy"  

# ']' でpbpasteを使う  
bind ] run "reattach-to-user-namespace pbpaste | tmux load-buffer - && tmux paste-buffer"  

# reload tmux => tmux source ~/.tmux.conf  
run '~/.tmux/plugins/tpm/tpm'  
```  

### 最後に
 
自分のdotfileはリポジトリにあるのでそれを参考にすれば大体大丈夫な気がする。

fishの設定などは以下を参考にすれば良い。  
https://github.com/TanisukeGoro/dotfiles/tree/master/modules/fish

oh-my-fishで入れているプラグイン類は以下を参考にすれば良い
https://github.com/TanisukeGoro/dotfiles/blob/master/modules/oh-my-fish/bundle

tmux関連の設定は上の`Tmuxの設定変更`のままなのでそれを見て欲しい

