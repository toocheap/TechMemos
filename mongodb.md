#mongodb

## homebrewでインストール

 $ brew install mongodb
 ==> Downloading http://fastdl.mongodb.org/osx/mongodb-osx-x86_64-2.2.2.tgz
 ######################################################################## 100.0%
 ==> Caveats
 To have launchd start mongodb at login:
     ln -sfv /usr/local/opt/mongodb/*.plist ~/Library/LaunchAgents
 Then to load mongodb now:
     launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mongodb.plist
 Or, if you don't want/need launchctl, you can just run:
     mongod

 WARNING: launchctl will fail when run under tmux.

launchctl用のplistのsymlink作成

 $ ln -sfv /usr/local/opt/mongodb/*.plist ~/Library/LaunchAgents
 /Users/too/Library/LaunchAgents/homebrew.mxcl.mongodb.plist -> /usr/local/opt/mongodb/homebrew.mxcl.mongodb.plist

lanchctlで起動。

 $ launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mongodb.plist
 $ ps aux | grep mongo
 too            30673   0.0  0.0  2432768    628 s000  S+   11:39AM   0:00.00 grep mongo
 too            30651   0.0  0.3  2511112  29036   ??  S    11:38AM   0:00.10 /usr/local/opt/mongodb/mongod run --config /usr/local/etc/mongod.conf

mongoシェルの起動確認

 $ mongo
 MongoDB shell version: 2.2.2
 connecting to: test
 Welcome to the MongoDB shell.
 For interactive help, type "help".
 For more comprehensive documentation, see
  http://docs.mongodb.org/
 Questions? Try the support group
  http://groups.google.com/group/mongodb-user
 >
 bye

lanchctlで停止確認

 $ launchctl unload ~/Library/LaunchAgents/homebrew.mxcl.mongodb.plist
 $ ps axu | grep mongo
 too            30722   0.0  0.0  2432768    628 s000  S+   11:40AM   0:00.00 grep mongo

## Windows install

(http://docs.mongodb.org/manual/tutorial/install-mongodb-on-windows/)

まずはアーキテクチャの確認

    > wmic os get osarchitecture
    OSArchitecture
    64 ビット

アーキテクチャにあったファイルをダウンロードして展開。
c:\Program files\mongodb-xxx\bin にパスを通す。

デフォルトだと/data/dbがないとおこられる。

    > mongod
    mongod --help for help and startup options
    Tue Feb 05 21:27:38 [initandlisten] MongoDB starting : pid=3576 port=27017 dbpat
    h=\data\db\ 64-bit host=mohchan
    Tue Feb 05 21:27:38 [initandlisten] db version v2.2.3, pdfile version 4.5
    Tue Feb 05 21:27:38 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2
    f4a96bf08
    Tue Feb 05 21:27:38 [initandlisten] build info: windows sys.getwindowsversion(ma
    jor=6, minor=1, build=7601, platform=2, service_pack='Service Pack 1') BOOST_LIB
    _VERSION=1_49
    Tue Feb 05 21:27:38 [initandlisten] options: {}
    Tue Feb 05 21:27:38 [initandlisten] exception in initAndListen: 10296
    *********************************************************************
     ERROR: dbpath (\data\db\) does not exist.
     Create this directory or give existing directory in --dbpath.
     See http://dochub.mongodb.org/core/startingandstoppingmongo
    *********************************************************************
    , terminating
    Tue Feb 05 21:27:38 dbexit:
    Tue Feb 05 21:27:38 [initandlisten] shutdown: going to close listening sockets..
    .
    Tue Feb 05 21:27:38 [initandlisten] shutdown: going to flush diaglog...
    Tue Feb 05 21:27:38 [initandlisten] shutdown: going to close sockets...
    Tue Feb 05 21:27:38 [initandlisten] shutdown: waiting for fs preallocator...
    Tue Feb 05 21:27:38 [initandlisten] shutdown: lock for final commit...
    Tue Feb 05 21:27:38 [initandlisten] shutdown: final commit...
    Tue Feb 05 21:27:38 [initandlisten] shutdown: closing all files...
    Tue Feb 05 21:27:38 [initandlisten] closeAllFiles() finished
    Tue Feb 05 21:27:38 dbexit: really exiting now

ルートにディレクトリを掘るのは嫌なので、--dbpathオプションで指定する。

    > cd <mongo dir>
    > mkdir data
    > mkdir db
    > mongod --dbpath "C:\Program Files\apl\mongodb-win32-x86_64-2.2.3\db\data"
    Tue Feb 05 21:32:34 [initandlisten] MongoDB starting : pid=8984 port=27017 dbpat
    h=C:\Program Files\apl\mongodb-win32-x86_64-2.2.3\db\data 64-bit host=mohchan
    Tue Feb 05 21:32:34 [initandlisten] db version v2.2.3, pdfile version 4.5
    Tue Feb 05 21:32:34 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2
    f4a96bf08
    Tue Feb 05 21:32:34 [initandlisten] build info: windows sys.getwindowsversion(ma
    jor=6, minor=1, build=7601, platform=2, service_pack='Service Pack 1') BOOST_LIB
    _VERSION=1_49
    Tue Feb 05 21:32:34 [initandlisten] options: { dbpath: "C:\Program Files\apl\mon
    godb-win32-x86_64-2.2.3\db\data" }
    Tue Feb 05 21:32:34 [initandlisten] journal dir=C:/Program Files/apl/mongodb-win
    32-x86_64-2.2.3/db/data/journal
    Tue Feb 05 21:32:34 [initandlisten] recover : no journal files present, no recov
    ery needed
    Tue Feb 05 21:32:35 [initandlisten] waiting for connections on port 27017
    Tue Feb 05 21:32:35 [websvr] admin web console waiting for connections on port 2
    8017

windowsサービスに登録する前にログディレクトリを作成

    > cd <mongo dir>
    > mkdir log

ログファイルとdbパスを設定ファイル mongod.cfg に指定

    logpath=C:\Program Files\apl\mongodb-win32-x86_64-2.2.3\log\mongod.log
    dbpath=C:\Program Files\apl\mongodb-win32-x86_64-2.2.3\db\data

サービスインストールのためにコマンドプロンプトを管理者権限で起動して実行

    > mongod --config "c:\Program Files\apl\mongodb-win32-x86_64-2.2.3\mongod.cfg" --install
    all output going to: C:\Program Files\apl\mongodb-win32-x86_64-2.2.3\log\mongod.
    log

net startでサービスを起動

    > net start MongoDB

    Mongo DB サービスは正常に開始されました。

mongoシェルを起動して接続を確認

    > mongo
    MongoDB shell version: 2.2.3
    connecting to: test
    Welcome to the MongoDB shell.
    For interactive help, type "help".
    For more comprehensive documentation, see
            http://docs.mongodb.org/
    Questions? Try the support group
            http://groups.google.com/group/mongodb-user


