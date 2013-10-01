# Chef

## 方針

何もない環境から始めることを前提とする（Chefをつかいたいのはそういうときなので）。
ChefやRubyやGCCもCLIで入れる方法があるけれど、いろいろとあとで困るのが面倒なので、GCCはXCodeのCommand line tool、Rubyもbrewではなくてrbenvでいれる方針とする。Rubyは1.9台と2.0台の両方を入れるけれど、2.0を使ってみよう。

なので、順番としては、

1. AppStoreで入れられるものを入れる。
2. Chefをインストール前の準備
2. Chefで入らないけど入れるのが面倒なアプリを手動でインストールする。要するにDropboxと1passwordがないとパスワード関連が何も使えないのでこれを。
2. Chefをインストール
2. Chefでもろもろインストール

## Chefをインストールする前の準備

Chefをインストールする前の準備としてhomebrewをインストール。そしてrbenvでrubyをインストールする。

### homebrew

[homebrew](http://mxcl.github.io/homebrew/)からインストール

    % ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"

brew doctorで環境を確認。

    % brew doctor

brewのレシピをアップデートしておく

    % brew update

### rbenv

ruby インストール前に必要なライブラリをインストール。

    % brew install openssl
    % brew install readline
    % brew install libyaml
    % brew install autoconf
    % brew install rbenv
    % brew install ruby-build

シェルにrbenvの設定をロードしておく。

    % eval "$(rbenv init -)"

インストールできるrubyのリストから1.9系と2.0系の最新をインストール。
ここでは1.9.3-p392と2.0.0-p0をインストール

    % rbenv install -l
    % RUBY_CONFIGURE_OPTS="--enable-shared --with-readline-dir=$(brew --prefix readline) --with-openssl-dir=$(brew --prefix openssl) --with-libyaml-dir=$(brew --prefix libyaml)"  rbenv install 1.9.3-p395
    % RUBY_CONFIGURE_OPTS="--enable-shared --with-readline-dir=$(brew --prefix readline) --with-openssl-dir=$(brew --prefix openssl)"  rbenv install 2.0.0-p0
    % rbenv shell 1.9.3-p395
    % rbenv versions
      system
      * 1.9.3-p392 (set by /usr/local/opt/rbenv/version)
        2.0.0-p0

rehashしてコマンドを認識しておく。これgemをインストールしたりしたら必要。

    % rbenv rehash

#### 参考URL
http://qiita.com/items/9dd797f42e7bea674705

## Chefをインストール

RubyはChefでは1.9系でインストールしておく。
Chefなど必要なgemはbundlerで管理するのがはやりのようなので、そっちで。

    % gem install bundler

さて、レポジトリのディレクトリを用意して開始。Rubyのバージョンをローカルだけ1.9.3-p392にする。

    % mkdir repo
    % cd repo
    % rbenv local 1.9.3-p392
    % touch .gitignore
    % bundle --path vendor/bundle
    % bundle install --binstubs

knifeの設定ファイルを作成してレポジトリの場所を追記。すでに存在している場合は手動で書き換えたほうが良いかも。

    % bin/knife configure

レポジトリの場所は以下のとおりで追記する。

    % cat ~/.chef/knife.rb
    log_level                :info
    log_location             STDOUT
    node_name                'too'
    client_key               '/Users/too/.chef/too.pem'
    validation_client_name   'chef-validator'
    validation_key           '/etc/chef/validation.pem'
    chef_server_url          'http://ns-pc226.tokyo.netstar-inc.com:4000'
    syntax_check_cache_path  '/Users/too/.chef/syntax_check_cache'
    solo_path                '/Users/too/Dropbox/apk/Chef/repo' # <<< ADD

一旦ひな形を作って移動
    % ./bin/knife solo init ./tmprepo
    Creating kitchen...
    Creating knife.rb in kitchen...
    Creating cupboards...
    $ mv tmprepo/* .

knife solo用の設定ファイルを作成

    $ cat solo.rb
    base_dir = "/Users/too"
    repo_dir = "#{base_dir}/Dropbox/apk/Chef/repo"

    file_cache_path             "/tmp/var"
    data_bag_path               "#{repo_dir}/data_bags"
    encrypted_data_bag_key      "#{repo_dir}/data-bag_key"
    cookbook_path   [
        "#{repo_dir}/cookbooks",
        "#{repo_dir}/site-cookbooks",
        "#{repo_dir}/vendor/cookbooks"
    ]
    role_path                   "#{repo_dir}/roles"

あわせてfile_cache用のディレクトリも作成

    % mkdir /tmp/var

今の状況はこんな感じ。

    $ ls -F
    Berksfile       bin/            nodes/          solo.rb
    Gemfile         cookbooks/      roles/          vendor/
    Gemfile.lock    data_bags/      site-cookbooks/


必要なcookbookをberkshelfで管理する。

    $ bin/berks init
    $ cat Berksfile
    site :opscode

    cookbook 'apt'
    cookbook 'yum'
    cookbook 'homebrew'
    cookbook 'windows'
    cookbook 'dmg'
    cookbook 'osxzip'
    cookbook 'mac_os_x'
    cookbook '1password'
    cookbook 'ghmac'
    cookbook 'xquartz'
    cookbook 'build-essential'
    cookbook 'virtualbox'
    cookbook 'iterm2'
    $ bin/berks install --path vendor/cookbooks
    $ ls vendor/cookbooks/
    1password       chef_handler    homebrew        osxzip          xquartz
    apt             dmg             iterm2          virtualbox      yum
    build-essential ghmac           mac_os_x        windows

dropboxのインストールのためにBerksfileに以下を追加。githubからも取得することができる。

    cookbook 'chef-dropbox', git: 'https://github.com/laces/chef-dropbox'
    t

Berksfileをアップデートしたのでberkshelfをアップデートする。

    $ bin/berks update
    Using apt (1.9.2)
    Using yum (2.2.0)
    Using homebrew (1.3.2)
    Using windows (1.8.10)
    Using dmg (1.1.0)
    Using osxzip (0.1.0)
    Using mac_os_x (1.4.2)
    Using 1password (1.0.6)
    Using ghmac (1.3.4)
    Using xquartz (1.0.0)
    Using build-essential (1.4.0)
    Using virtualbox (1.0.0)
    Using iterm2 (1.3.0)
    Installing chef-dropbox (0.1.0) from git: 'https://github.com/laces/chef-dropbox' with branch: 'a65d82a60903b3aae9ff70743f7d2c18b39af05f'
    Using chef_handler (1.1.4)
    $ bin/berks install --path vendor/cookbooks

使用するcookbookとgemが一旦落ち着いたここで一旦コミット。
管理すべきなのはGemfiles,Berksfileとsite-cookbookおよびrole, data_bagsなどのローカルファイルのみ。vendorやbin以下などのGemやberkshelfで管理されるファイルはgitの管理外にするようにgitignoreに記述する。

    $ cat .gitignore
    /.ruby-version
    /vendor
    /cookbooks
    *.dmg
    *.zip
    *.gz
    .vagrant
    Berksfile.lock
    *~
    *#
    .#*
    \#*#
    .*.sw[a-z]
    *.un~

    # Bundler
    Gemfile.lock
    bin/*
    .bundle/*


    $ git init
    Initialized empty Git repository in /Users/too/Dropbox/apk/Chef/repo/.git/
    $ git status  # gitignoreがきいているかどうか確認
    $ git add .
    $ git commit -m 'Initial commit'
    [master (root-commit) 44fe879] Initial commit
     5 files changed, 156 insertions(+)
     create mode 100644 .gitignore
     create mode 100644 Berksfile
     create mode 100644 Gemfile
     create mode 100644 Gemfile.lock
     create mode 100644 Vagrantfile
     create mode 100644 solo.rb

cookbookを作成。opscodeなど他のところから取ってきたものはvendor/cookbooks、ローカルのcookbookはsite-cookbookにがルールっぽい。じゃあ直下のcookbooksは何に使うのだろう^^;

    $ bin/knife cookbook create osxbase -o site-cookbooks
    $ ls site-cookbooks/osxbase/
    CHANGELOG.md    definitions/    metadata.rb     resources/
    README.md       files/          providers/      templates/
    attributes/     libraries/      recipes/

recipeをそのまま実行してもいいが、roleによってrecipeをまとめて実行させたいのと、homebrewやdmgなどの便利クックブックを読み込ませたいので、roleを設定スル。

role:mac_os_xには基本となるcookbookを、role:workstationには基本アプリをそれぞれ記述した。

    $ cat roles/mac_os_x.rb
    name "mac_os_x"
    description "Role applied to all Mac OS X systems."

    run_list(
        "recipe[dmg]",
        "recipe[osxzip]",
        "recipe[mac_os_x]",
        "recipe[homebrew]"
    )

    $ cat roles/workstation.rb
    name "workstation"
    description "Mac OS X worksations"
    run_list(
      "recipe[osxbase]"
    )

osxbaseのrecipeにテスト用のlogを追加

    % vim site-cookbooks/osxbase/recipes/default.rb
    log "Hello Chef!"

ふたつのroleを実行するjsonを作成

    $ cat osxbase.json
    {
        "run_list": [
            "role[mac_os_x]",
            "role[workstation]"
        ]
    }

テスト実行して動作を確認。

    $ bin/chef-solo -c solo.rb -j osxbase.json
    Starting Chef Client, version 11.4.4
    Compiling Cookbooks...
    Converging 5 resources
    Recipe: homebrew::default
      * remote_file[/tmp/var/homebrew_go] action create (up to date)
      * execute[/tmp/var/homebrew_go] action run (skipped due to not_if)
      * package[git] action install (skipped due to not_if)
      * execute[update homebrew from github] action run
        - execute /usr/local/bin/brew update || true

    Recipe: osxbase::default
      * log[Hello Chef!] action write

    Chef Client finished, 2 resources updated

## Chefでもろもろインストール

### berkshelfのbash completionファイルをインストールするrecipe

リモートからファイルを取ってきて、ファイルを設置場所にコピーするだけの簡単なcookbookを作成してみる。この用途にはremote_file DSLをつかう。

まず knifeで空のcookbookを作成。

    $ bin/knife cookbook create berkshelf-completion -o site-cookbooks/

attiributeに:barkshelf_compl以下に使用する変数を設定

    $ cat site-cookbooks/berkshelf-completion/attributes/default.rb
    default[:berkshelf_compl][:bash_completion_d]  = "/usr/local/etc/bash_completion.d/"
    default[:berkshelf_compl][:src_filename]       = "berkshelf-complete.sh"
    default[:berkshelf_compl][:remote_uri]         = "https://raw.github.com/RiotGames/berkshelf/master/berkshelf-complete.sh"
    default[:berkshelf_compl][:file_checksum]      = "3c4ad2a8bc88885eb2e9c580e9f6d0e41ba556b8b2abc80f1ab64f36af489e09"

recipeにremote_fileを設定

    $ cat site-cookbooks/berkshelf-completion/recipes/default.rb
    remote_file "#{node[:berkshelf_compl][:bash_completion_d]}#{node[:berkshelf_compl][:src_filename]}" do
        source "#{node[:berkshelf_compl][:remote_uri]}"
        checksum "#{node[:berkshelf_compl][:file_checksum]}"
    end

remote_file 設置場所 do でパラメータとしてsourceとchecksumをあたえる。
パラメータに設定する変数はattributeで設定された値を使う。またこれらはデフォルト値として使われる。recipeではnode変数でアクセスできる。

これでsourceからファイルを取得して、設置場所にコピーされる。必要ならばuserやgroup, modeなどを設定する。

role:workstationにrecipeを追加。

    $ cat roles/workstation.rb
    name "workstation"
    description "Mac OS X worksations"
    run_list(
            "recipe[osxbase]",
            "recipe[chef-dropbox]",
            "recipe[1password]",
            "recipe[berkshelf-completion]"      # <<< ADD
    )

chef-soloを実行。

    $ bin/chef-solo -c solo.rb -j osxbase.json

最後にgitに追加。

    $ git add .
    $ git status
    # On branch master
    # Changes to be committed:
    #   (use "git reset HEAD <file>..." to unstage)
    #
    #       modified:   roles/workstation.rb
    #       new file:   site-cookbooks/berkshelf-completion/CHANGELOG.md
    #       new file:   site-cookbooks/berkshelf-completion/README.md
    #       new file:   site-cookbooks/berkshelf-completion/attributes/default.rb
    #       new file:   site-cookbooks/berkshelf-completion/metadata.rb
    #       new file:   site-cookbooks/berkshelf-completion/recipes/default.rb
    #
    $ git commit -m 'Add berkshelf-completion cookbook'
    [master 91dd53e] Add berkshelf-completion cookbook
     6 files changed, 107 insertions(+), 1 deletion(-)
        create mode 100644 site-cookbooks/berkshelf-completion/CHANGELOG.md
        create mode 100644 site-cookbooks/berkshelf-completion/README.md
        create mode 100644 site-cookbooks/berkshelf-completion/attributes/default.rb
        create mode 100644 site-cookbooks/berkshelf-completion/metadata.rb
        create mode 100644 site-cookbooks/berkshelf-completion/recipes/default.rb

##### 参考URL
http://qiita.com/items/48b2c1c5f1161001c94e
http://docs.opscode.com/chef/resources.html#remote-file

### dotfilesをインストール

自分用のdotfileをホームディレクトリにsymlinkをはる。
Dropboxがインストールされていれば所定の場所にレポジトリはあるはずなので、そのチェックを行う。

attribute/default.rb

    default[:dotfiles][:dropbox_path] = "#{ENV['HOME']}/Dropbox/apk/dotfiles"
    default[:dotfiles][:install_path] = "#{ENV['HOME']}"
    default[:dotfiles][:dotfiles]     = %w{
      dot.Rprofile
      dot.bash_profile
      dot.bashrc
      dot.emacs.el
      dot.gdbinit
      dot.gitconfig
      dot.global_ignore
      dot.gvimrc
      dot.inputrc
      dot.jshintrc
      dot.pystartup
      dot.tmux.conf
      dot.vimrc
    }

recipe/default.rb

    dotfiles = node[:dotfiles][:dotfiles]

    dotfiles.each do |dropbox_dotfile|
      dot_itself, dotfile = dropbox_dotfile.split('.')
      if dotfile.nil? then
        log "Skipped for #{dotfile}"
        next
      end

      src = "#{node[:dotfiles][:dropbox_path]}/#{dropbox_dotfile}"
      dst = "#{node[:dotfiles][:install_path]}/.#{dotfile}"

      # 元ファイルは削除
      File dst do
        action :delete
        not_if do
          !File.exists?(dst)
        end
      end

      # リンクファイルを作成
      link dst do
        to src
        owner 'too'
        group 'staff'
      end
    end

linkのdstとsrcが逆に思えてはまったくらいか。
修正点としてはownerの自動取得とdotfilesのリストをdata_bagsに移すとかかな。

## gem_packageをつかうとNoMethodErrorがでる

こんなかんじ。

[ここ](https://github.com/rubygems/rubygems/issues/502)で議論されている通り、wontfixとなっており、RubyGemsの2.0とのコンパチビリティ問題。RubyGemsの1.x系をつかうようにいわれる。

    Recipe: osxbase::default
      * gem_package[rake] action install
      ================================================================================
      Error executing action `install` on resource 'gem_package[rake]'
      ================================================================================


      NoMethodError
      -------------
      undefined method `last' for #<Gem::AvailableSet:0x007fc0639f57b0>

RubyGemsのversionが...

    $ gem -v
    2.0.3

1.8.3へダウングレードする。

    $ gem uninstall rubygems-update  # 全部消す
    Remove executables:
    update_rubygems

    in addition to the gem? [Yn]  gem install rubygems-update --version=1.8.24
        update_rubygemsRemove executables:
        update_rubygems

        in addition to the gem? [Yn]
        Remove executables:
        update_rubygems

        in addition to the gem? [Yn]  y
        Removing update_rubygems
        Successfully uninstalled rubygems-update-2.0.3

    $ gem install rubygems-update --version=1.8.24
    Fetching: rubygems-update-1.8.24.gem (100%)
    Successfully installed rubygems-update-1.8.24
    Installing ri documentation for rubygems-update-1.8.24
    1 gem installed

    $ update_rubygems
    RubyGems 1.8.24 installed

    == 1.8.24 / 2012-04-27

    * 1 bug fix:

    * Install the .pem files properly. Fixes #320
    * Remove OpenSSL dependency from the http code path


    ------------------------------------------------------------------------------

    RubyGems installed the following executables:
    /usr/local/opt/rbenv/versions/1.9.3-p392/bin/gem

    $ gem -v
    1.8.24

しかしオフィシャルでは1.9最新版をつかうことになってるんだよなあ...。

