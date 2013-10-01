# Ruby

## インストールしたgemがどこにあるか探す

rbenvなどでローカルに環境を作っていると、コマンドとしてインストールされるはずのファイルがパスがキレるところに置かれないこともしばしば。実際インストール先ディレクトリさえわからないことが多い。

で、そんなときは gem にきこう。

    $ gem which redcarpet
    /Users/too/.rbenv/versions/1.9.3-p194/lib/ruby/gems/1.9.1/gems/redcarpet-2.2.2/lib/redcarpet.rb

## rbenvでrubyをインストールする

インストール対象のRubyのバージョンを確認

    $ rbenv install -l
    Available versions:
      1.8.6-p383
      1.8.6-p420
      1.8.7-p249
      1.8.7-p302
      1.8.7-p334
      1.8.7-p352
      1.8.7-p357
      1.8.7-p358
      1.8.7-p370
      1.8.7-p371
      1.9.1-p378
      1.9.2-p180
      1.9.2-p290
      1.9.2-p318
      1.9.2-p320
      1.9.3-dev
      1.9.3-p0
      1.9.3-p125
      1.9.3-p194
      1.9.3-p286
      1.9.3-p327
      1.9.3-p362
      1.9.3-p374
      1.9.3-p385
      1.9.3-p392
      1.9.3-preview1
      1.9.3-rc1
      2.0.0-dev
      2.0.0-p0
      2.0.0-preview1
      2.0.0-preview2
      2.0.0-rc1
      2.0.0-rc2
      2.1.0-dev
      jruby-1.5.6
      jruby-1.6.3
      jruby-1.6.4
      jruby-1.6.5
      jruby-1.6.5.1
      jruby-1.6.6
      jruby-1.6.7
      jruby-1.6.7.2
      jruby-1.6.8
      jruby-1.7.0
      jruby-1.7.0-preview1
      jruby-1.7.0-preview2
      jruby-1.7.0-rc1
      jruby-1.7.0-rc2
      jruby-1.7.1
      jruby-1.7.2
      jruby-1.7.3
      maglev-1.0.0
      maglev-1.1.0-dev
      rbx-1.2.4
      rbx-2.0.0-dev
      rbx-2.0.0-rc1
      ree-1.8.6-2009.06
      ree-1.8.7-2009.09
      ree-1.8.7-2009.10
      ree-1.8.7-2010.01
      ree-1.8.7-2010.02
      ree-1.8.7-2011.03
      ree-1.8.7-2011.12
      ree-1.8.7-2012.01
      ree-1.8.7-2012.02

brewでインストールされているライブラリのパスをRUBY_CONFIGURE_OPTSで指定して、rbenv install

    $ RUBY_CONFIGURE_OPTS="--enable-shared --with-readline-dir=$(brew --prefix readline) --with-openssl-dir=$(brew --prefix openssl) --with-libyaml-dir=$(brew --prefix libyaml)"  rbenv install 1.9.3-p392
    Downloading yaml-0.1.4.tar.gz...
    -> http://dqw8nmjcqpjn7.cloudfront.net/36c852831d02cf90508c29852361d01b
    Installing yaml-0.1.4...
    Installed yaml-0.1.4 to /usr/local/opt/rbenv/versions/1.9.3-p392

    Downloading ruby-1.9.3-p392.tar.gz...
    -> http://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.3-p392.tar.gz
    Installing ruby-1.9.3-p392...
    Installed ruby-1.9.3-p392 to /usr/local/opt/rbenv/versions/1.9.3-p392

インストールされたversionを確認

    $ ruby -v
    ruby 1.8.7 (2012-02-08 patchlevel 358) [universal-darwin12.0]

Ruby v2.0.0-p0をインストール

    $ rbenv install 2.0.0-p0
    Downloading openssl-1.0.1e.tar.gz...
    -> https://www.openssl.org/source/openssl-1.0.1e.tar.gz
    Installing openssl-1.0.1e...

RUBY_CONFIGURE_OPTSを指定していなかったので、再度。

    $ RUBY_CONFIGURE_OPTS="--enable-shared --with-readline-dir=$(brew --prefix readline) --with-openssl-dir=$(brew --prefix openssl)"  rbenv install 2.0.0-p0
    Downloading ruby-2.0.0-p0.tar.gz...
    -> http://ftp.ruby-lang.org/pub/ruby/2.0/ruby-2.0.0-p0.tar.gz
    Installing ruby-2.0.0-p0...
    Installed ruby-2.0.0-p0 to /usr/local/opt/rbenv/versions/2.0.0-p0

インストールされたバージョンを確認して、1.9.3に使用バージョンを変更

    !796-j:0 $ rbenv versions
    * system (set by /usr/local/opt/rbenv/version)
      1.9.3-p392
      2.0.0-p0
    !797-j:0 $ rbenv global 1.9.3-p392
    !798-j:0 $ ruby -v
    ruby 1.9.3p392 (2013-02-22 revision 39386) [x86_64-darwin12.3.0]
    !

## rbenv-gemset

rbenvでインストールした特定のRuby versionにたいしてgemのセットを定義して切り替えることができる。rbenvのように特定のディレクトリ以下だけ特定のバージョンとgemsetを使う、といった切り替えが自動的にできるので便利。

事前準備で現時点でインストールされているgemを全部削除する

```shell
for gem in `gem list | cut -d " " -f 1`; do yes | gem uninstall $gem -a; done
```

rbenv と rbenv-gemset を brew でインストールする。

システム全体で使うruby-versionを指定

```shell
$ rbenv versions
* system (set by /Users/too/.rbenv/version)
  1.9.3-p448
  2.0.0-p247

$ rbenv-gemset list
2.0.0-p247:
  defaultgems
    pop3gems
```


例)POP3のスクリプトを作る

POP3のスクリプトを作るために、そのディレクトリにversionとgemsetを指定する
```shell
$ mkdit test
$ cd test
```

versionを指定

```shell
$ rbenv versions
  system
  1.9.3-p448
* 2.0.0-p247 (set by /Users/too/Dropbox/apk/ruby/.ruby-version)
$ rbenv version
2.0.0-p247 (set by /Users/too/Dropbox/apk/ruby/.ruby-version)

$ rbenv local 1.9.3-p448
$ rbenv version
1.9.3-p448 (set by /Users/too/Dropbox/apk/ruby/test/.ruby-version)
$ ruby --version
ruby 1.9.3p448 (2013-06-27 revision 41675) [x86_64-darwin12.5.0]
```

gemset を指定

```
$ rbenv gemset list
no acrive gemsets

$ rbenv gemset create 2.0.0-p247 defaultgems

$ rbenv-gemset active
default global

$ rbenv-gemset list
2.0.0-p247:
defaultgems
pop3gems

$ rbenv-gemset create 1.9.3-p448 defaultgems
created defaultgems for 1.9.3-p448

$ rbenv-gemset list
1.9.3-p448:
defaultgems
2.0.0-p247:
defaultgems
pop3gems

$ rbenv gemset file
/Users/too/Dropbox/apk/ruby/test/.rbenv-gemsets

$ rbenv gemset active
defaultgems global

$ rbenv gemset create 1.9.3-p448 testgems
created testgems for 1.9.3-p448

$ rbenv gemset list
1.9.3-p448:
defaultgems
testgems
2.0.0-p247:
defaultgems
pop3gems

$ echo testgems > .rbenv-gemsets
$ rbenv gemset active
testgems global

$ rbenv gemset file
/Users/too/Dropbox/apk/ruby/.rbenv-gemsets

$ echo testgems > .rbenv-gemsets
$ rbenv gemset file
/Users/too/Dropbox/apk/ruby/test/.rbenv-gemsets

$ rbenv gemset active
testgems global
```

## Chef

gemでインストール

    $ gem install chef


Last login: Fri Apr 19 14:30:30 on ttys001
ns-nt078:~ too$
ns-nt078:~ too$
ns-nt078:~ too$
ns-nt078:~ too$
ns-nt078:~ too$ ls
Desktop         Downloads       Library         Music           Public
Documents       Dropbox         Movies          Pictures        setup.log
ns-nt078:~ too$ bundle
-bash: bundle: command not found
ns-nt078:~ too$ rbenv
rbenv 0.4.0
Usage: rbenv <command> [<args>]

Some useful rbenv commands are:
   commands    List all available rbenv commands
   local       Set or show the local application-specific Ruby version
   global      Set or show the global Ruby version
   shell       Set or show the shell-specific Ruby version
   install     Install a Ruby version using the ruby-build plugin
   uninstall   Uninstall a specific Ruby version
   rehash      Rehash rbenv shims (run this after installing executables)
   version     Show the current Ruby version and its origin
   versions    List all Ruby versions available to rbenv
   which       Display the full path to an executable
   whence      List all Ruby versions that contain the given executable

See `rbenv help <command>' for information on a specific command.
For full documentation, see: https://github.com/sstephenson/rbenv#readme
ns-nt078:~ too$ rbenv rehash
ns-nt078:~ too$ rbenv versions
  system
* 1.9.3-p392 (set by /Users/too/.rbenv/version)
  2.0.0-p0
ns-nt078:~ too$ gem list --local

*** LOCAL GEMS ***


ns-nt078:~ too$ gem install bundle
^CERROR:  Interrupted
ns-nt078:~ too$ gem install bundler
ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions into the /Library/Ruby/Gems/1.8 directory.
ns-nt078:~ too$ which ruby
/usr/bin/ruby
ns-nt078:~ too$ eval `rbenv init -`
-bash: syntax error near unexpected token `('
ns-nt078:~ too$ rbenv init
# Load rbenv automatically by adding
# the following to ~/.bash_profile:

eval "$(rbenv init -)"

ns-nt078:~ too$ eval "$(rbenv init -)"
ns-nt078:~ too$ which ruby
/Users/too/.rbenv/shims/ruby
ns-nt078:~ too$ gem install bundler
Successfully installed bundler-1.3.5
1 gem installed
Installing ri documentation for bundler-1.3.5...
Installing RDoc documentation for bundler-1.3.5...
ns-nt078:~ too$ rbenv rehash
ns-nt078:~ too$ bundle
Bundler::GemfileNotFound
ns-nt078:~ too$ cd ~/Dropbox/apk/Chef/chef-repo
ns-nt078:chef-repo too$ ls
Gemfile
ns-nt078:chef-repo too$ bundle --path vendor/bundle
Fetching git://github.com/matschaffer/knife-solo.git
remote: Counting objects: 4123, done.
remote: Compressing objects: 100% (2147/2147), done.
remote: Total 4123 (delta 1862), reused 3917 (delta 1679)
Receiving objects: 100% (4123/4123), 815.98 KiB | 458 KiB/s, done.
Resolving deltas: 100% (1862/1862), done.
Fetching gem metadata from https://rubygems.org/........
Fetching gem metadata from https://rubygems.org/..
Resolving dependencies...
Installing i18n (0.6.1)
Installing multi_json (1.7.2)
Installing activesupport (3.2.13)
Installing addressable (2.3.4)
Installing timers (1.1.0)
Installing celluloid (0.13.0)
Installing hashie (2.0.3)
Installing chozo (0.6.1)
Installing multipart-post (1.2.0)
Installing faraday (0.8.7)
Installing json (1.7.7)
Installing minitar (0.5.4)
Installing mixlib-config (1.1.2)
Installing mixlib-shellout (1.1.0)
Installing retryable (1.3.2)
Installing erubis (2.7.0)
Installing mixlib-log (1.6.0)
Installing mixlib-authentication (1.3.0)
Installing net-http-persistent (2.8)
Installing net-ssh (2.6.7)
Installing solve (0.4.2)
Installing ridley (0.9.0)
Installing thor (0.18.1)
Installing yajl-ruby (1.1.0)
Installing berkshelf (1.4.0)
Installing highline (1.6.18)
Installing mixlib-cli (1.3.0)
Installing net-ssh-gateway (1.2.0)
Installing net-ssh-multi (1.1)
Installing ipaddress (0.8.0)
Installing systemu (2.5.2)
Installing ohai (6.16.0)
Installing mime-types (1.22)
Installing rest-client (1.6.7)
Installing chef (11.4.0)
Using knife-solo (0.3.0.pre4) from git://github.com/matschaffer/knife-solo.git (at master)
knife-solo at /Users/too/Dropbox/apk/Chef/chef-repo/vendor/bundle/ruby/1.9.1/bundler/gems/knife-solo-2e4adde48f07 did not have a valid gemspec.
This prevents bundler from installing bins or native extensions, but that may not affect its functionality.
The validation message from Rubygems was:

## brew cleanupで残ったkegの削除

依存関係で残ってしまうkegがあるので、その場合は入れなおしを行う。

    $ brew cleanup
    Removing: /usr/local/Cellar/autojump/21.1.4...
    Removing: /usr/local/Cellar/autojump/21.3.0...
    (snip)
    Removing: /usr/local/Cellar/freetype/2.4.10...
    Warning: Skipping (old) keg-only: /usr/local/Cellar/gettext/0.18.1.1
    Removing: /usr/local/Cellar/ghostscript/9.06...
    Warning: Skipping git: most recent version 1.8.2.2 not installed
    Removing: /usr/local/Cellar/glib/2.30.2...

大きく分けると上記の通りkegが残る場合とバージョンが古いのでcleanupできない場合がある。
前者は依存関係のあるkegの入れ替え、後者はupgrade後にcleanupのやりなおしになる。

        $ brew upgrade
        $ brew uses gettext
        apt-dater                               gts

(とやると、パッケージすべてを見に行ってしまうので)

        $ brew uses --installed gettext
        fontforge  git        glib

とする。ではアンインストールしてインストール。

        $ brew uninstall fontforge git glib
        Uninstalling /usr/local/Cellar/fontforge/20120731...
        Uninstalling /usr/local/Cellar/git/1.8.2.2...
        Uninstalling /usr/local/Cellar/glib/2.36.1...
        $ brew install fontforge git glib

同じ手順でreadlineも入れなおして、でcleanupのやりなおし。

        $ brew cleanup
        Removing: /usr/local/Cellar/ack/2.02...
        Warning: Skipping (old) keg-only: /usr/local/Cellar/gettext/0.18.1.1
        Removing: /usr/local/Cellar/git/1.7.10...
        Removing: /usr/local/Cellar/git/1.7.9.3...
        Removing: /usr/local/Cellar/git/1.8.0.2...
        Removing: /usr/local/Cellar/git/1.8.1.1...
        Removing: /usr/local/Cellar/git/1.8.1.4...
        Removing: /usr/local/Cellar/git/1.8.1.5...
        Removing: /usr/local/Cellar/git/1.8.2...
        Removing: /usr/local/Cellar/git/1.8.2.1...
        Removing: /usr/local/Cellar/readline/6.2.2...
        Removing: /usr/local/Cellar/ruby-build/20120815...
        Removing: /usr/local/Cellar/ruby-build/20121204...
        Removing: /usr/local/Cellar/ruby-build/20130118...
        Removing: /usr/local/Cellar/ruby-build/20130224...
        Removing: /usr/local/Cellar/ruby-build/20130227...
        Removing: /usr/local/Cellar/ruby-build/20130408...
        Pruned 0 dead formula
        Pruned 0 symbolic links and 2 directories from /usr/local

gettextだけ残っているけれどこれはもう一度やると消えた。

    $ brew cleanup
    Removing: /usr/local/Cellar/gettext/0.18.1.1...
    Pruned 0 dead formula


