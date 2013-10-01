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

## Aggregation Framework

データ集計用のフレームワーク。SQLでいうGROUP BYのような機能を提供する。
内部ではMapReduceを使っている模様。

### 実行例サンプル

mongo シェルを起動して、DBを作成しでもデータを作成

    $ mongo
    MongoDB shell version: 2.2.3
    connecting to: test
    > use classdb
    switched to db classdb
    > year = ["freshman", "junior", "senior"];
    [ "freshman", "junior", "senior" ]
    > for(var i=1; i<=100; i++) db.scores.insert({"name":"quiz","score":Math.floor(Math.random()*100+1),"student":i,"year":year[Math.floor(Math.random()*year.length)]})

作成したテストDBの内容は以下のとおり。

データベース一覧

    > show dbs
    AMSLOG  11.9482421875GB
    classdb 0.203125GB
    local   (empty)
    tumblroll       0.203125GB

データ一覧

    > db.scores.find()
    { "_id" : ObjectId("515b9ade6124984d1083d823"), "name" : "quiz", "score" : 46, "student" : 1, "year" : "freshman" }
    { "_id" : ObjectId("515b9ade6124984d1083d824"), "name" : "quiz", "score" : 49, "student" : 2, "year" : "junior" }
    { "_id" : ObjectId("515b9ade6124984d1083d825"), "name" : "quiz", "score" : 62, "student" : 3, "year" : "junior" }
    { "_id" : ObjectId("515b9ade6124984d1083d826"), "name" : "quiz", "score" : 53, "student" : 4, "year" : "freshman" }
    { "_id" : ObjectId("515b9ade6124984d1083d827"), "name" : "quiz", "score" : 22, "student" : 5, "year" : "senior" }
    { "_id" : ObjectId("515b9ade6124984d1083d828"), "name" : "quiz", "score" : 15, "student" : 6, "year" : "freshman" }
    { "_id" : ObjectId("515b9ade6124984d1083d829"), "name" : "quiz", "score" : 17, "student" : 7, "year" : "freshman" }
    { "_id" : ObjectId("515b9ade6124984d1083d82a"), "name" : "quiz", "score" : 2, "student" : 8, "year" : "junior" }
    { "_id" : ObjectId("515b9ade6124984d1083d82b"), "name" : "quiz", "score" : 81, "student" : 9, "year" : "freshman" }
    { "_id" : ObjectId("515b9ade6124984d1083d82c"), "name" : "quiz", "score" : 47, "student" : 10, "year" : "junior" }
    { "_id" : ObjectId("515b9ade6124984d1083d82d"), "name" : "quiz", "score" : 7, "student" : 11, "year" : "senior" }
    { "_id" : ObjectId("515b9ade6124984d1083d82e"), "name" : "quiz", "score" : 16, "student" : 12, "year" : "freshman" }
    { "_id" : ObjectId("515b9ade6124984d1083d82f"), "name" : "quiz", "score" : 75, "student" : 13, "year" : "junior" }
    { "_id" : ObjectId("515b9ade6124984d1083d830"), "name" : "quiz", "score" : 42, "student" : 14, "year" : "senior" }
    { "_id" : ObjectId("515b9ade6124984d1083d831"), "name" : "quiz", "score" : 97, "student" : 15, "year" : "junior" }
    { "_id" : ObjectId("515b9ade6124984d1083d832"), "name" : "quiz", "score" : 52, "student" : 16, "year" : "junior" }
    { "_id" : ObjectId("515b9ade6124984d1083d833"), "name" : "quiz", "score" : 40, "student" : 17, "year" : "senior" }
    { "_id" : ObjectId("515b9ade6124984d1083d834"), "name" : "quiz", "score" : 44, "student" : 18, "year" : "senior" }
    { "_id" : ObjectId("515b9ade6124984d1083d835"), "name" : "quiz", "score" : 85, "student" : 19, "year" : "freshman" }
    { "_id" : ObjectId("515b9ade6124984d1083d836"), "name" : "quiz", "score" : 14, "student" : 20, "year" : "junior" }
    Type "it" for more

データ1つだけ取り出し。

    > db.scores.findOne()
    {
            "_id" : ObjectId("515b9ade6124984d1083d823"),
            "name" : "quiz",
            "score" : 46,
            "student" : 1,
            "year" : "freshman"
    }

いろいろな集約において、AggregationFrameworkを使うまでもないほど(結果が4MBを超えない場合)は集約のコマンド群(count, distinct, group)を使うことができる。

Count句を使うことでレコードを数えられる。全データ数は100。

    > db.scores.count()
    100

selector句を使用するとフィルタがかけられる。例えば'year'が'junior'なレコードの数を数えるには以下のようにする。

    > db.scores.count({'year':'junior'})
    35

Distict句で、特定のキーの全ての異なる値(uniqみたいな？)を表示する。

    > db.scores.distinct('year')
    [ "freshman", "junior", "senior" ]

もちろん、selector句でフィルタできる。

    > db.scores.distinct('score', {'year': 'junior'})
    [
            49,
            62,
            2,
            47,
            75,
            97,
            52,
            14,
            23,
            3,
            58,
            83,
            11,
            78,
            24,
            100,
            6,
            40,
            21,
            41,
            19,
            98,
            84,
            25,
            65,
            35,
            38,
            51
    ]

group句でSQLでいうGROUP BYを実施できる。

例)
年次(year)が'junior'な生徒の得点(score)の平均を集計し、名前(name)と平均得点(score)を抽出する。

SQLではこんなかんじになるはず。

    SELECT name, average(score) FROM scores WHERE year='junior' GROUP BY name, score;

これをgroup句で書くと、以下のようになる。
keyがGROUP BY句、condがWHERE句になる。
initialで出力結果で使用する変数を初期化、reduceでは各行毎に実行するfunctionを指定、finalizeで集計したoutに対する処理をしてできる。
reduceにはcur,out（名前は任意）の２つの引数が渡され、カレント行と今までの計算結果の２つ結果オブジェクトがそれぞれ渡される。結果オブジェクトに集計用の変数を設定したい場合にはinitialで指定する。
finalizeには今までの計算結果の結果オブジェクトが渡される。最終的にはこの結果オブジェクトはkey句で設定したカラムと同様に出力に含まれるので、結果オブジェクトに新しいプロパティを設定して計算結果を保存すればよい。

    > db.scores.group({
        key: {name: true},
        cond: {'year':'junior'},
        initial: {count: 0, total: 0},
        reduce: function(cur,out) {
            out.count+=1;
            out.total+=cur.score
        },
        finalize: function(out) {
            out.average = out.total/out.count
        }})

結果。initializeで初期化したキーやfinalizeで設定したaverage変数は出力に含まれている。

    [
            {
                    "name" : "quiz",
                    "count" : 35,
                    "total" : 1666,
                    "average" : 47.6
            }
    ]

これをAggregateで実施すると以下のようになる。上記のコマンドは結果に4MBまでの制限があるがaggregateにはない。
$matchでフィルタした上で、$projectに元レコードからの要素を1で指定、$groupに最終出力の内容を指定する。
$groupの_idにグループ化したい要素、それ以外のプロパティにExpressionを使った集約を指定する。
$group内で元データのキーを持ってくるには$keynameで指定する。
つまり $match が WHERE句、$projectがSELECT, $groupの_idがGROUP BY句に相当する

    > db.scores.aggregate(
        { $match:
            { 'year': 'junior' }
        },
        { $project:
            { 'name': 1, 'score': 1 }   // 1はtrueの代わり。0にすればfalseになる。
        },
        { $group:
            {
                '_id': '$name',
                'average': {
                    '$avg': '$score'
                }
            }
        })

結果

    { "result" : [ { "_id" : "quiz", "average" : 47.6 } ], "ok" : 1 }

他にも、Year毎にレコード数と平均値を計算できる。この場合は$avgと$sum Expressionを使用している。

    > db.scores.aggregate(
        {$project: {'year': true, 'score': true}},
        {$group: {
            '_id': '$year',
            'average': {$avg: '$score'},
            'count': {'$sum': 1}
        }})

結果

    {
            "result" : [
                    {
                            "_id" : "junior",
                            "average" : 47.6,
                            "count" : 35
                    },
                    {
                            "_id" : "senior",
                            "average" : 47.542857142857144,
                            "count" : 35
                    },
                    {
                            "_id" : "freshman",
                            "average" : 50.86666666666667,
                            "count" : 30
                    }
            ],
            "ok" : 1
    }

##### 参考
(http://docs.mongodb.org/manual/reference/method/db.collection.group/)

