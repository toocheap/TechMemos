homebrew のインストール

ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"

too$ ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"
==> This script will install:
/usr/local/bin/brew
/usr/local/Library/...
/usr/local/share/man/man1/brew.1
==> The following directories will be made group writable:
/usr/local/.
/usr/local/bin
==> The following directories will have their group set to admin:
/usr/local/.
/usr/local/bin

Press ENTER to continue or any other key to abort
==> /usr/bin/sudo /bin/chmod g+rwx /usr/local/. /usr/local/bin

WARNING: Improper use of the sudo command could lead to data loss
or the deletion of important system files. Please double-check your
typing when using sudo. Type "man sudo" for more information.

To proceed, enter your password, or type Ctrl-C to abort.

Password:
==> /usr/bin/sudo /usr/bin/chgrp admin /usr/local/. /usr/local/bin
==> Downloading and Installing Homebrew...
remote: Counting objects: 109011, done.
remote: Compressing objects: 100% (44663/44663), done.
remote: Total 109011 (delta 77603), reused 91941 (delta 63419)
Receiving objects: 100% (109011/109011), 16.44 MiB | 465 KiB/s, done.
Resolving deltas: 100% (77603/77603), done.
From https://github.com/mxcl/homebrew
 * [new branch]      master     -> origin/master
 HEAD is now at 920e164 Sonar 3.5.1
 ==> Installation successful!
 You should run `brew doctor' *before* you install anything.
 Now type: brew help
 NS-NT078:~ too$ brew doctor
 Your system is ready to brew.
 NS-NT078:~ too$

brew update
brew install openssl
brew install readline
brew install --HEAD ruby-build
brew install rbenv
brew install libyaml
brew install autoconf
RUBY_CONFIGURE_OPTS="--with-readline-dir=`brew --prefix readline` --with-openssl-dir=`brew --prefix openssl` --with-libyaml-dir=`brew --prefix libyaml`" rbenv install 1.9.3-p392
 RUBY_CONFIGURE_OPTS="--with-readline-dir=`brew --prefix readline` --with-openssl-dir=`brew --prefix openssl` --with-libyaml-dir=`brew --prefix libyaml`" rbenv install 2.0.0-p0
 rbenv global 1.9.3-p392
 rbenv rehash

!1-j:0 $ mkdir chef-repo
 [14:12:42]: ~/Dropbox/apk/Chef
 !1-j:0 $ cd chef-repo/
  [14:12:44]: ~/Dropbox/apk/Chef/chef-repo

rbenv versions

eval "$(rbenv init -)"
gem install bundler
rbenv rehash
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
(snip)

$ bundle install --binstubs
$ cat Berksfile
site :opscode

cookbook 'homebrew'
cookbook 'dmg'
cookbook 'osxzip'

ns-nt078:chef-repo too$ bundle exec berks install --path vendor/cookbooks
Installing homebrew (1.3.2) from site: 'http://cookbooks.opscode.com/api/v1/cookbooks'
Installing dmg (1.1.0) from site: 'http://cookbooks.opscode.com/api/v1/cookbooks'
Installing osxzip (0.1.0) from site: 'http://cookbooks.opscode.com/api/v1/cookbooks'
Installing mac_os_x (1.4.2) from site: 'http://cookbooks.opscode.com/api/v1/cookbooks'

}z@R2j9GWNoQr
ns-nt078:chef-repo too$ git commit -m 'Andler::GemfileNotFound
ns-nt078:~ too$ cd ~/Dropbox/apk/Chef/chef-repo
onf-repo
ns-nt078:chef-repo too$ ls
Gemfile
ns-nt078:chef-repo t add .
ns-nt078:chef-repo too$ git commit -m 'Add OS X cookbooks'
[master 33d783d] Add OS X cookbooks

$ bin/knife cookbook create base -o site-cookbooks/
** Creating cookbook base
** Creating 23 (delta 1862), reused 3917 (delta 1679)
(snip)

$ bundle in ball --binstubs
$ cat Berksfile
site :opscode

cookbook 'homebo too$ vim site-cookbooks/base/
CHANGELOG.md  attributes/   files/        metadata.rb   recipes/      templates/
README.md     definitions/  libraries/    providers/    resources/
ns-nt078:chef-repo too$ vim site-cookbooks/base/re
recipes/   resources/
ns-nt078:chef-repo too$ vim site-cookbooks/base/re
recipes/   resources/
ns-nt078:chef-repo too$ vim site-cookbooks/base/recipes/default.rb


