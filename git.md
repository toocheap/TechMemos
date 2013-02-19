#git.md
## PowerShell上でgithub for windowsのgitをつかう

github for windowsをインストールするとPowerShellでも使えるようになるのだけど、デフォルトではパスに存在しない。

    PS C:\Users\tooch_000> git
    git : 用語 'git' は、コマンドレット、関数、スクリプト ファイル、または操作可能なプログラムの名前として認識されません。
    名前が正しく記述されていることを確認し、パスが含まれている場合はそのパスが正しいことを確認してから、再試行してください
    。
    発生場所 行:1 文字:1
    + git
    + ~~~
        + CategoryInfo          : ObjectNotFound: (git:String) [], CommandNotFoundException
        + FullyQualifiedErrorId : CommandNotFoundException

レポジトリ→ツールから起動できるGitシェルを起動したい場合は以下のPS1ファイルを実行すればよい。

    PS C:\Users\tooch_000> .\AppData\Local\GitHub\shell.ps1
    PS C:\Users\tooch_000> git
    usage: git [--version] [--exec-path[=<path>]] [--html-path] [--man-path] [--info-path]
               [-p|--paginate|--no-pager] [--no-replace-objects] [--bare]
               [--git-dir=<path>] [--work-tree=<path>] [--namespace=<name>]
               [-c name=value] [--help]
               <command> [<args>]

    The most commonly used git commands are:
       add        Add file contents to the index
       bisect     Find by binary search the change that introduced a bug
       branch     List, create, or delete branches
       checkout   Checkout a branch or paths to the working tree
       clone      Clone a repository into a new directory
       commit     Record changes to the repository
       diff       Show changes between commits, commit and working tree, etc
       fetch      Download objects and refs from another repository
       grep       Print lines matching a pattern
       init       Create an empty git repository or reinitialize an existing one
       log        Show commit logs
       merge      Join two or more development histories together
       mv         Move or rename a file, a directory, or a symlink
       pull       Fetch from and merge with another repository or a local branch
       push       Update remote refs along with associated objects
       rebase     Forward-port local commits to the updated upstream head
       reset      Reset current HEAD to the specified state
       rm         Remove files from the working tree and from the index
       show       Show various types of objects
       status     Show the working tree status
       tag        Create, list, delete or verify a tag object signed with GPG

    See 'git help <command>' for more information on a specific command.

## .gitignoreを自動で作成するgiboをインストールする

[gibo](https://github.com/simonwhitaker/gitignore-boilerplates)は[githubが公開しているgitignoreのtemplate](https://github.com/github/gitignore)から必要な設定を取得して.gitignoreを自動で生成する。

OSXなのでhomebrewでインストール。

    $ brew install gibo
    ==> Downloading https://github.com/simonwhitaker/gitignore-boilerplates/tarball/
    ######################################################################## 100.0%
    ==> Caveats
    Bash completion has been installed to:
      /usr/local/etc/bash_completion.d

    zsh completion has been installed to:
      /usr/local/share/zsh/site-functions
    ==> Summary
    🍺  /usr/local/Cellar/gibo/1.0.1: 5 files, 20K, built in 3 seconds

ヘルプ

     gibo
    gibo 1.0.1 by Simon Whitaker <simon@goosoftware.co.uk>
    https://github.com/simonwhitaker/gitignore-boilerplates

    Fetches gitignore boilerplates from github.com/github/gitignore

    Usage:
        gibo [options]
        gibo [boilerplate boilerplate...]

    Example:
        gibo Python TextMate >> .gitignore

    Options:
        -l, --list          List available boilerplates
        -u, --upgrade       Upgrade list of available boilerplates
        -h, --help          Display this help text
        -v, --version       Display current script version

~/.bash_profileにcompletionのために以下を追加

    # git completion
    source /usr/local/etc/bash_completion.d/git-completion.bash
    source /usr/local/etc/bash_completion.d/gibo-completion.bash

補完候補を表示

    $ gibo vim
    Display all 108 possibilities? (y or n)
    Actionscript          Java                  Redcar
    Android               Jboss                 RhodesRhomobile
    AppceleratorTitanium  Jekyll                Ruby
    Archives              Joomla                RubyMine
    Autotools             Jython                SASS
    Bancha                Kohana                SBT
    C                     LaTeX                 SVN
    C++                   Leiningen             Scala
    CFWheels              LemonStand            Sdcc
    CMake                 Lilypond              SeamGen
    CVS                   Linux                 SketchUp
    CakePHP               Lithium               SublimeText
    Clojure               Magento               SugarCRM
    CodeIgniter           Matlab                Symfony
    Compass               Maven                 Symfony2
    Concrete5             Mercurial             SymphonyCMS
    Coq                   ModelSim              Tags
    Dart                  MonoDevelop           Target3001
    Delphi                NetBeans              Tasm
    Django                Node                  TextMate
    Drupal                OCaml                 Textpattern
    Eagle                 OSX                   TurboGears2
    Eclipse               Objective-C           Typo3
    Emacs                 Opa                   Unity
    Erlang                OracleForms           VirtualEnv
    Espresso              Perl                  VisualStudio
    ExpressionEngine      PhPStorm              Waf
    Finale                PlayFramework         Windows
    FlexBuilder           Plone                 WordPress
    ForceDotCom           PyCharm               XilinxISE
    FuelPHP               Python                Yii
    GWT                   Qooxdoo               ZendFramework
    Go                    Qt                    gcov
    Grails                Quartus2              nanoc
    Haskell               R                     opencart
    IntelliJ              Rails                 vim

ありがちなのをグローバル設定に追加

    $ gibo vim Archives textmate node linux windows autotools CMake CVS eclipse emacs mercurial osx ruby python C C++ django espresso go haskell java jython objective-c perl R rails ruby scala sublimetext tags textpattern virtualenv visualstudio > ~/.global_ignore

グローバル設定を.gitconfigに追加

    $ git config --global core.excludesfile ~/.global_ignore
    $ cat ~/.gitconfig

追加内容。

    [core]
        excludesfile = /Users/too/.global_ignore

## 追加するときに変な名前がついちゃうかもなので、グローバル設定をちゃんとしとこうの巻

新しいマシンでcommitしたら....

    Committer: xxxxxx
    Your name and email address were configured automatically based
    on your username and hostname. Please check that they are accurate.
    You can suppress this message by setting them explicitly:

        git config --global user.name "Your Name"
        git config --global user.email you@example.com

    After doing this, you may fix the identity used for this commit with:

        git commit --amend --reset-author

oh... マシン名から自動生成されちゃった...。
これだと悲しいのでちゃんと設定しておこう。

        git config --global user.name "My Name"
        git config --global user.email me@example.com
        git commit --amend --reset-author -m 'Reset author'

gitconfigにいかが追加される。

    [use]
        name = My Name
        email = me@example.com


