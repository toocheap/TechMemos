# vim:ff=unix

## Tips

(https://github.com/guyon/configs/blob/master/.vim/doc/best_tips1.txt)
(https://github.com/guyon/configs/blob/master/.vim/doc/best_tips2.txt)


# まずは.vimディレクトリを作成

    mkdir ~/.vim
    mkdir ~/.vim/vim_swap
    mkdir ~/.vim/vim_backup

OSごとにパスが違うのでOSで切り替えて設定

    " Set different path by OS
    if has('mac')
        set backupdir=~/.vim/vim_backup
        set directory=~/.vim/vim_swap
    elseif has('win32')
        set backupdir=C:\Users\tooch_000\tmp\vim_backup
        set directory=C:\Users\tooch_000\tmp\vim_swap
    endif

# Windowsのクリップボードのコピペのショートカット

| CTRL+INS | コピー|
| CTRL+DEL | カット|
| SHIFT+INS | ペースト|

## 開いているファイルの文字コードを変更する

    set fileencoding=utf8
    set fileencoding=cp932

## 開いているファイルの改行コードを変更する

    set fileformat=dos
    set fileformat=mac
    set fileformat=unix

## メニューが文字化けしたら治す

    " menu 文字化け対応
    source $VIMRUNTIME/delmenu.vim
    set langmenu=ja_jp.utf-8
    source $VIMRUNTIME/menu.vim

##### 参考

[vimエディタで「文字コード、改行コードを変更して保存する。」](http://advweb.seesaa.net/article/3074705.html)

## Markdownのファイルタイプを追加

autocmdでファイル読み込みと書き込み時にファイル・タイプを自動設定する

    autocmd BufRead,BufNewFile *.mkd  setfiletype mkd
    autocmd BufRead,BufNewFile *.md  setfiletype mkd

(ちなみにauはautocmdの省略形)

以下の二つのpluginを入れるとsyntax highlightが有効化する。

    https://github.com/hallison/vim-markdown
    https://github.com/msanders/snipmate.vim

## NeoBundle

(http://vim-users.jp/2011/10/hack238/)
(http://qiita.com/items/1c32d3f24cc2919203eb)

    mkdir -p ~/.vim/bundle
    git clone https://github.com/Shougo/neobundle.vim ~/.vim/bundle/neobundle.vim
    git clone https://github.com/Shougo/vimproc ~/.vim/bundle/vimproc

以下を\_vimrcに追加した。

    "--------------------------------------------------------------------------
    """ neobundle
    filetype off                   " Required!

    if has('vim_starting')
        set runtimepath+=~/.vim/bundle/neobundle.vim
    endif

    call neobundle#rc(expand('~/.vim/bundle/'))

    NeoBundle 'Shougo/neobundle.vim'
    NeoBundle 'Shougo/vimproc'
    NeoBundle 'Shougo/unite.vim'
    NeoBundle 'Shougo/neocomplcache'
    NeoBundle 'Shougo/neosnippet'

    " for Surround.vim
    NeoBundle "tpope/vim-surround"
    " for Markdown syntax highlight
    NeoBundle "hallison/vim-markdown"
    NeoBundle "msanders/snipmate.vim"
    " for Gist access tool
    NeoBundle "mattn/gist-vim"
    " syntax + 自動compile
    NeoBundle 'kchmck/vim-coffee-script'
    " js BDDツール
    NeoBundle 'claco/jasmine.vim'
    " indentの深さに色を付ける
    NeoBundle 'nathanaelkane/vim-indent-guides'

    filetype plugin indent on     " Required!

    " Installation check.
    if neobundle#exists_not_installed_bundles()
      echomsg 'Not installed bundles : ' .
            \ string(neobundle#get_not_installed_bundle_names())
      echomsg 'Please execute ":NeoBundleInstall" command.'
      "finish
    endif

## Gist

https://github.com/mattn/gist-vim/

## ペーストするときにインデントさせない。

:a!してCTRL+Vするというのがよくあるけど

    " ペーストするときにインデントさせない
    " http://qiita.com/items/019250dbca167985fe32
    imap <F5> <nop>
    set pastetoggle=<F5>

[VimでインデントさせずにC-vで貼り付けを行う方法](http://mba-hack.blogspot.jp/2013/01/vimc-v.html)

## Ricty font

WindowsだとFontForgeのインストールだけでCygwin使ったりと面倒なので、結局はフォントファイルを作るだけだからVM上のUbuntuでサクッと作成。

    $ cd ~/Dropbox/apk
    $ mkdir font
    $ mkdir Ricty
    $ cd font

###Download inconsolata Opentype

    http://levien.com/type/myfonts/inconsolata.html
    http://levien.com/type/myfonts/Inconsolata.otf

###Download Migu-1M

    http://mix-mplus-ipa.sourceforge.jp/migu/
    http://sourceforge.jp/projects/mix-mplus-ipa/downloads/57240/migu-1m-20121030.zip/
    http://sourceforge.jp/frs/redir.php?m=jaist&f=%2Fmix-mplus-ipa%2F57240%2Fmigu-1m-20121030.zip

    $ unzip migu-1m-20121030.zip

###Install FontForge

    $ sudo apt-get install fontforge

###Download Ricty shell script

    https://github.com/yascentur/Ricty/tree/3.2.1

    $ git clone https://github.com/yascentur/Ricty.git

###Create Ricty font

    $ cp migu-1m-20121030/migu-1m-* .
    $ cp Ricty/ricty_generator.sh .
    $ sh ricty_generator.sh auto

###Install fonts

- Ubuntu

    ディレクトリを作成してそこにほおり込むだけ

        $ mkdir ~/.fonts
        $ cp -f Ricty*.ttf ~/.fonts/

- Windows

    Explorerでダブルクリックしてインストール

###_gvimrcに設定。Rictyを設定すると全角表示されるので、InconsolataとMigu 1Mを直接設定することで逃げた^^;

    " UTF-8 環境でないとうまく表示されない
    set encoding=utf-8
    " フォントサイズはお好みで
    "set guifont=Migu\ 1M:h12
    set guifont=Inconsolata:h12
    " こっちは日本語フォント
    set guifontwide=Migu\ 1M:h12
    set ambiwidth=double
    " menu 文字化け対応
    source $VIMRUNTIME/delmenu.vim
    set langmenu=ja_jp.utf-8
    source $VIMRUNTIME/menu.vim

## Unite

NeoBundleに以下を設定してインストール

    NeoBundle 'Shougo/vimfiler'
    NeoBundle 'Shougo/vimproc'
    NeoBundle 'Shougo/unite.vim'

vimrcに以下を追加

    "------------------------------------------------------------------------
    """ unite.vim
    " http://blog.remora.cx/2010/12/vim-ref-with-unite.html
    "
    " 入力モードで開始する
    let g:unite_enable_start_insert=1
    " バッファ一覧
    noremap <C-P> :Unite buffer<CR>
    " ファイル一覧
    noremap <C-N> :Unite -buffer-name=file file<CR>
    " 最近使ったファイルの一覧
    noremap <C-Z> :Unite file_mru<CR>

    " ウィンドウを分割して開く
    au FileType unite nnoremap <silent> <buffer> <expr> <C-J> unite#do_action('split')
    au FileType unite inoremap <silent> <buffer> <expr> <C-J> unite#do_action('split')
    " ウィンドウを縦に分割して開く
    au FileType unite nnoremap <silent> <buffer> <expr> <C-K> unite#do_action('vsplit')
    au FileType unite inoremap <silent> <buffer> <expr> <C-K> unite#do_action('vsplit')
    " ESCキーを2回押すと終了する
    au FileType unite nnoremap <silent> <buffer> <ESC><ESC> :q<CR>
    au FileType unite inoremap <silent> <buffer> <ESC><ESC> <ESC>:q<CR>
    " 初期設定関数を起動する
    au FileType unite call s:unite_my_settings()
        function! s:unite_my_settings()
        " Overwrite settings.
    endfunction

    " 様々なショートカット
    call unite#set_substitute_pattern('file', '\$\w\+', '\=eval(submatch(0))', 200)
    call unite#set_substitute_pattern('file', '^@@', '\=fnamemodify(expand("#"), ":p:h")."/"', 2)
    call unite#set_substitute_pattern('file', '^@', '\=getcwd()."/*"', 1)
    call unite#set_substitute_pattern('file', '^;r', '\=$VIMRUNTIME."/"')
    call unite#set_substitute_pattern('file', '^\~', escape($HOME, '\'), -2)
    call unite#set_substitute_pattern('file', '\\\@<! ', '\\ ', -20)
    call unite#set_substitute_pattern('file', '\\ \@!', '/', -30)

    if has('win32') || has('win64')
        call unite#set_substitute_pattern('file', '^;p', 'C:/Program Files/')
        call unite#set_substitute_pattern('file', '^;v', '~/vimfiles/')
    else
        call unite#set_substitute_pattern('file', '^;v', '~/.vim/')
    endif

CTRL+Pでバッファ一覧、CTRL+Nでカレントディレクトリ一覧を表示。[ESC][ESC]でUniteを終了する。
ショートカットの使い方がよくわからんなあ。

## VimFiler

(http://www.karakaram.com/vimfiler)

    :VimFilerBufferDir

で開く

hjklで移動 qで終了


## 色変更いろいろ

    " 色変更

    colorscheme solarized
    set background=dark
    " IMEの状態でカーソルの色を変更 (colorschemeのあとで設定)
    "https://sites.google.com/site/fudist/Home/vim-nihongo-ban/vim-color#color-ime
    if has('multi_byte_ime')
      highlight Cursor guifg=NONE guibg=Green
      highlight CursorIM guifg=NONE guibg=Purple
    endi
    "挿入モード時、ステータスラインの色を変更
    let g:hi_insert = 'highlight StatusLine guifg=darkblue guibg=darkyellow gui=none ctermfg=blue ctermbg=yellow cterm=none'
    if has('syntax')
      augroup InsertHook
        autocmd!
        autocmd InsertEnter * call s:StatusLine('Enter')
        autocmd InsertLeave * call s:StatusLine('Leave')
      augroup END
    endif

    let s:slhlcmd = ''
    function! s:StatusLine(mode)
      if a:mode == 'Enter'
        silent! let s:slhlcmd = 'highlight ' . s:GetHighlight('StatusLine')
        silent exec g:hi_insert
      else
        highlight clear StatusLine
        silent exec s:slhlcmd
      endif
    endfunction

    function! s:GetHighlight(hi)
      redir => hl
      exec 'highlight '.a:hi
      redir END
      let hl = substitute(hl, '[\r\n]', '', 'g')
      let hl = substitute(hl, 'xxx', '', '')
      return hl
    endfunction
    if has('unix') && !has('gui_running')
      " ESC後にすぐ反映されない対策
      inoremap <silent> <ESC> <ESC>
    endif

gvimrcの読み込みとバッティングして設定の上書きが発生する場合の対処を入れた

    " カラースキーム
    " GUIとTerminalで色が変わる問題の対処
    " http://d.hatena.ne.jp/yayugu/20110918/1316363220
    " http://d.hatena.ne.jp/acotie/20100123/1264271335
    if has('gui_running')
        set background=dark
        let scheme = 'solarized'
        augroup guicolorscheme
          autocmd!
          execute 'autocmd GUIEnter * colorscheme' scheme
        augroup END
        execute 'colorscheme' scheme
    endif

## Surround.vim

( http://d.hatena.ne.jp/zentoo/20090815/1250325935 )
( https://github.com/tpope/vim-surround )

とりあえず、おぼえるべきは y,c,d の3コマンド。これにs<範囲指定もしくはカーソル移動>を組み合わせる。

|abc*d*ef|       abc(def)|       ys$)|
|aabc*d*ef|       abc( def )|      ys$( abc(def)        dにカーソルがあるとすると|
|aabc*d*ef|       abc'def'|        cs('|
|aabc*d*ef|       abc"def"|        cs("|
|abc'def'|        cs'"|    abc"def"|
|abc(def)|       ds(|      abcdef|
|aabc(def)|      ds)|      dbcdef|

上は文字選択の場合だけど、下は行選択の場合。インデントも有効化できる。
Visualで行選択してS{(大文字Sでインデント有効)

    function some() {
        do somthing;
    }
↓
    {
        function some() {
            do somthing;
        }
    }
## キーマップの変更コマンドの違い

(http://whileimautomaton.net/2008/07/20150335)

## map系とnoremap系の違い

mapの場合、BをおすとXが実行される

    map {A} {X}
    map {B} {A}

noremapの場合は、BをおしてもXは実行されない

    map {A} {X}
    noremap {B} {A}

なので、以下をnoremapではなく、mapにすると延々とlをllに変換し続けてしまう。

    map l ll

## prefixのnvoicとpostfixの!

|n|ノーマルモード|map,noremap|機能へのショートカット, カーソル移動|
|v|ビジュアルモード|vmap, vnoremap|範囲選択, カーソル移動|
|o|オペレーション待ちモード|omap, onoremap|範囲選択, カーソル移動|
|i|挿入モード|imap, inoremap|入力補助|
|c|コマンドラインモード||
|!|指定したモード以外||

## <silent><plug><unique>

- <unique>

    他にマップされて入れば、マップしない。

- <silent>

    マップ時のエラーを表示しない

- <plug>(xxx)

    プラグイン用の名前xxxをつける。<Plug>(xxx)で参照できる

- <expr>

##### 参考
[勝手に添削ftpluginのマナー](http://mattn.kaoriya.net/software/vim/20121015165001.htm)


## 文字をうっていると自動で改行が挿入される

ん？？と思っていたら、TABの設定時にtwに2とか4を設定してた。

    'textwidth' 'tw' 数値 (既定では 0)
                バッファについてローカル
                {Vi にはない}
        入力されているテキストの最大幅。行がそれより長くなると、この幅を超えな
        いように空白の後で改行される。値を 0 に設定すると無効になる。

要するに tw=2 とかではワードを打ち込んだ所で自動改行が働いて打ち込んだ文字を2桁に整形してしまう。ゆえに縦書モードのような状態にww。tw=0 で自動改行を無効に設定。

## VimScriptのデバッグに使えそうなコマンド

関数が定義されているファイルを調べる

    :verbose function /FunctionName/

スクリプトが実行される様子を詳しく眺める

    :99verbose YourCommandHere

デバッグする (ステップ実行、ブレークポイントなど)

    :debug YourCommandHere


## 起動時、ファイルオープン時にオプションを自動設定する方法

ヘルプによると3つある。

    2. オプションの自動設定     *auto-setting*

    コマンド ":set" によるオプションの設定の他に、3通りの方法で、1つまたは複数のファ
    イルに自動的にオプションを設定できる。

    1. Vimを起動したとき、様々な場所から初期化設定を読み込ませることができる。
       |initialization| を参照。多くの設定は編集セッション全てに適用されるが、いく
       つかはVimを起動したディレクトリによって異なる。初期化設定ファイルはコマンド
       |:mkvimrc|, |:mkview|, |:mksession| で生成できる。
    2. 新しいファイルの編集を始めたとき、自動的に実行されるコマンドがある。
       これを使うと、特定のパターンにマッチするファイルに対してオプションを設定し
       たり、様々なことが可能である。|autocommand| を参照。
    3. 新しいファイルの編集を始めたときオプション 'modeline' がオンなら、ファイル
       の先頭と末尾の数行ずつがモードラインとして読み込まれる。それをここで説明す
       る。

1はvimrc、2はvimrcなどで設定するauXXX、3がmodeline。
mkXXXコマンドは初めて知った。

モードラインの指定はよく間違うのだけれど、実は空白が意味を持つらしい。

     [text]{white}{vi:|vim:|ex:}[white]{options}

    [text]  任意のテキスト、なくても良い
    {white}  1個以上の余白 (<Space> または <Tab>)
    {vi:|vim:|ex:} "vi:" か "vim:" か "ex:" という文字列
    [white]  空白、なくても良い
    se[t]  "set " または "se " という文字列 ( NOTE 終わりの空白に注意)
    {options} オプション設定が空白で区切られて並んだもので、コマンド ":set"
            の引数である
    :  コロン
    [text]  任意のテキスト、なくても良い
    modline

vim:の前後に空白が必要。ここがミソかな。

## TABの設定

いくつもオプションがあって悩むTABの指定は以下の3つのオプションが基本。

    shiftwidth  sw
    tabstop     ts
    softtabstop sts

よく一緒にtwが設定されていて勘違いするけれど、こちらはtextwidth。エディタのカラム数を指定してそれを超えると自動改行するオプションなので、間違うと縦書モードｗになってしまうので注意すること。副作用を起こさないために規約などがなければtw=0にし、自動改行オプションをオフにしておくほうがいいだろう。

基本的にはTAB幅を2,4,8などにするにはshiftwidthとtabstopに同じ値を指定して、softtabstopに0を指定しておけばよい。

TAB幅を4にするモードラインの例。

    vim: sw=4 ts=4 sts=0

ファイルタイプが python だったら、TAB幅を4に設定するautocmdの例。ここではexpandtabでTABを空白に置き換えるオプションも設定している。
    autocmd FileType python setl tabstop=4 expandtab shiftwidth=4 softtabstop=0 tw=0

##### 参考
[vim-users.jp](http://vim-users.jp/2010/04/hack137/)

## Syntasticでjshintrcにパラメータを渡す

jshintrcのひな形は[ここ](https://github.com/jshint/node-jshint/blob/master/.jshintrc)から取ってきた。

    let g:syntastic_javascript_checker = "jshint"

このようにcheckerだけだとパラメータを使用しないようなので、exeとargsに分ける。

    " Javascript
    let g:syntastic_javascript_checker_exe = "jshint"
    let g:syntastic_javascrupt_checker_args='--config=~/.jshintrc'

## Syntastic: Javascript: Express.staticがjshintでエラーになる。

staticが予約語で引っかかる。

    app.use(express.static(path.join(__dirname, 'public')));

これはjshintrcに es5: true を追加することで抑制できる(はずだった)

    // EcmaScript 5.
    "es5"           : true,   // Allow EcmaScript 5 syntax.

でも、だめぽ。
作者はjs(h|l)int suicksだそうなので、

    app.use(express['static'](path.join(__dirname, 'public')));

に書き換えるくらいしか方法がないかなー。だせぇ。


## Syntasticでmongooseのスキーマ設定時にjshintが予約後のエラーを出す

    var userSchema = new mongoose.Schema({
        blogs_length:   {type: Number, default: 0},
        created_at:     {type: Date, default: Date.now()}
    });

とすると、jshintrcでes5: trueとしていてもエラーが出る。

    error| Expected an identifier and instead saw 'default' (a reserved word).

これはes5:trueでsuppressできる範囲を超えて、Javascriptとしてinvalidであるため。エラーを無くすにはquoteするしかない。


    var userSchema = new mongoose.Schema({
        blogs_length:   {type: Number, 'default': 0},
        created_at:     {type: Date, 'default': Date.now()}
    });

## MacVimでバックスラッシュを入力する

option + \ で **"\"** を入力できる

## vimrc内でだけ使う関数の定義

functionのあと関数名に必ずs:をつけること。これでスクリプトローカル関数になるので、他の関数と当たらない。
function!の!は既存の関数の上書きをする指定。


    function! s:StatusLine(mode)
        if a:mode == 'Enter'
            silent! let s:slhlcmd = 'highlight ' . s:GetHighlight('StatusLine')
            silent exec g:hi_insert
        else
            highlight clear StatusLine
            silent exec s:slhlcmd
        endif
    endfunction

## キーマップのCTRL+uはどういう意味？

exコマンド(:), インサートモードでのCTRL+uはカレントラインの削除なので、途中で入力中の状態でもそれを削除した上でコマンドを入力するために設定される。

    autocmd FileType vimfiler
            \ nnoremap <buffer><silent>/
            \ :<C-u>Unite file -default-action=vimfiler<CR>

## autocmd登録時の注意点

(http://whileimautomaton.net/2008/08/vimworkshop3-kana-presentation)

autocommandは定義すればするほど追加されていくため、vimrcをリロードするとどんどんと追加され、結果的に動作が不定になってしまう可能性がある。そのためリロード時対策としてautocmd!でリセットを行う。

    autocmd BufEnter *  ...
    autocmd BufLeave *  ...

ではなく

    augroup MyAutoCmd           " 自分の作ったauをまとめる
      autocmd!                  " 今までのauをリセットする
      autocmd BufEnter *  ...
      autocmd BufLeave *  ...
      ...
    augroup

がよい

## プラグインの自動読み込みを行う(NeoBundleの場合)

NeoBundleによって登録したプラグインは、遅延読み込みが可能。またその場合プラグインの設定部分を読み込んだかどうかの判定をNeoBundleのon_sourceフックで行うことができるため、プラグインがない場合には実行しないようにすることができる。

遅延読み込みは NeoBundleLazyを使い、autoloadプロパティにTriggerを設定する。

該当関数が実行された時に読み込み

    " OpenBrowser 関数
    " :OpenBrowser, :OpenBrowserSearch コマンド
    " <Plug>(openbrowser-smart-search) キーマッピング
    " のいずれかが呼ばれた場合に読み込まれる
    NeoBundleLazy "tyru/open-browser.vim", {
    \   'autoload' : {
    \       'functions' : "OpenBrowser",
    \       'commands'  : ["OpenBrowser", "OpenBrowserSearch"],
    \       'mappings'  : "<Plug>(openbrowser-smart-search)"
    \   },
    \}

該当ファイルタイプの時に読み込み

    " set filetype=cpp
    " された時に読み込まれる
    NeoBundleLazy "vim-jp/cpp-vim", {
    \   "autoload" : { "filetypes" : ["cpp"] }
    \}

初期設定をNeoBundleのhookに登録。neobundle#get()のプラグイン名は~/.vim/bundle(デフォルトでは）以下のディレクトリ名を指定する。

    " 各プラグインが読み込まれている場合に処理される
    let s:bundle = neobundle#get("unite.vim")
    function! s:bundle.hooks.on_source(bundle)
        ....
    endfunction
    unlet s:bundle

## レジスタとマーク

viのときは範囲指定によく使っていたけど、vimになってからビジュアルモードがあることで全く使わなくなったマークとレジスタ。
例えばmkでマークkにマークして移動してからy'kでマークkまでヤンクとかよく使ってた。

一覧表示のキーマップを設定したので、使いやすくなった。

    " マークとレジスタを表示
    nnoremap <Space>m  :<C-u>marks<CR>
    nnoremap <Space>r  :<C-u>registers<CR>

レジスタを指定してコピーは、たとえばレジスタwなら、"wy
レジスタを指定してペーストは、たとえばレジスタwなら、"wp

## vimrc内でオプションを変数として扱いたい

backupdirなどのオプションの値を変数として取り出したい場合は&をつける。

    set backupdir=C:\Users\tooch_000\tmp\vim_backup
    if (!isdirectory(&backupdir))
        call mkdir(&backupdir, 'p')
    endif

(http://d.hatena.ne.jp/thinca/20100205/1265307642)

## 起動時に vimrc などの起動スクリプトを読んだ順番を表示

    :scriptnames

で表示される。読み込まれるファイルは runtimepathに登録されるので、

    :set runtimepath

で表示される。

## 消えたメッセージの再表示

    :message
    :mes

で表示される。


