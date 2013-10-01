# CSVなログをmongodbに読み込む

## 複数のログファイルをインポート

mongodbはUTF-8なので、文字コードを確認

    nkf -g *log

ShiftJISのようなので、nkfで変換

変換後ログの文字コードを一応確認

    for i in *.log ; do nkf -g u_$i ; done

mongodbはCSVの一括インポートコマンド*mongoimport*があるので、これをつかう。

- db: AMSLOG
- collection: alpine
- type: csv
- file: ログ
- fieldfile: カラム名を保存したファイル

    mongoimport -d AMSLOG -c alpine --type csv --file u_intersafe_http_20111002_001.log --fieldFile logcolumn.txt

fieldファイルはカラムを1行毎にカイたファイル

    -- logcolumn.txt
    Date
    Time
    Protocol
    SrcIP
    GroupName
    UserName
    UA
    State
    ServerIP
    StatusCode
    DstHostname
    RcvTime
    RcvSize
    FileType
    Category
    RequestURL

## forでログごとにインポート

    for i in u_* ; do mongoimport -d AMSLOG -c alpine --type csv --file $i --fieldFile logcolumn.txt ;done

## インポートしたログの日付をDate型に変換

mongoシェルでレコードに元のカラムにあるデータを元に新しいカラムを追加する

DateとTimeは文字列として入っているので、これを取り出してISODate型に変換する。
ISODate型は*"1970-01-01T00:00:00+0900"*というフォーマット。
元のDateが*-*ではなく*/*区切りなのでシェルで置換。

    var dstr = doc.Date.split('/').join('-') + 'T' + doc.Time + '+09:00:00'
    var d = new ISODate(dstr)

各行に適用するので、カーソルをforEachで取り出して適用。
元データにエラーが有ったりするので、カーソル取り出し時にエラー無しデータでフィルタしてカーソルを取得。

    var cur = db.alpine.find( {'Date': /\d{4}\/\d{2}\/\d{2}/} )

テストの時はlimit()をつかって取り出す行数を少なくして試していくと良い。

    var cur = db.alpine.find( {'Date': /\d{4}\/\d{2}\/\d{2}/} ).limit(5)

さて、カーソルを取り出したので、各行に適用していく。
各行取り出しているので、updateもidでできる。複数行適用したいので multi オプションをtrueにする。テスト時は外しておけば1行目だけ適用されるので、カーソルを再取り出しすれば、動くまで繰り返しできる。

    db.alpine.update(
        {'_id': doc._id},
        {$set:
            {'ISODate': d}
        },
        {multi: true}
    )

## 集計

Aggregate Frameworkをつかうとmongo上でMapReduceで集計ができる。
先ほどのデータを日付順にカウントして、上位5日間を集計

グループ化用のExpressionは(ここ)[http://docs.mongodb.org/manual/reference/aggregation/#aggregation-expression-operators]参照。

    > var r = db.alpine.aggregate([
        {$group:
            {'_id': {                           // _idにグループ化したいカラムを指定
                'year': {$year: '$ISODate'},    // $yearなどはgroup化のためのExpression
                'month': {$month: '$ISODate'},
                'day': {$dayOfMonth: '$ISODate'},
                'week': {$dayOfWeek: '$ISODate'}
            },
                count: { $sum:1 },
            }
        },
        {$sort: {count: -1} },  //逆順
        {$limit: 5},            // 逆順なので上位5レコード
        {$project: {            // 出力形式
            _id: 0,
            year: '$_id.year',
            month: '$_id.month',
            day: '$_id.day',
            week: '$_id.week',
            count:1, }
        }
    ])

実行結果。

    > r
    {
      "result" : [
            {
                "count" : 182700,
                "year" : 2011,
        "month" : 10,
        "day" : 10,
        "week" : 2
    },
    {
        "count" : 171775,
        "year" : 2011,
        "month" : 10,
        "day" : 13,
        "week" : 5
    },
    {
        "count" : 171406,
        "year" : 2011,
        "month" : 10,
        "day" : 12,
        "week" : 4
    },
    {
        "count" : 170227,
        "year" : 2011,
        "month" : 10,
        "day" : 20,
        "week" : 5
    },
    {
        "count" : 169988,
        "year" : 2011,
        "month" : 10,
        "day" : 11,
        "week" : 3
    }
    ],
        "ok" : 1
    }

ちなみに失敗の場合。$dayが$dayOfMonthに名称変更していた。

    > var r = db.alpine.aggregate([ {$group: {'_id': { 'year': {$year: '$ISODate'}, 'month': {$month: '$ISODate'}, 'day': {$day: '$ISODate'}, 'week': {$dayOfWeek: '$ISODate'} }, count: { $sum:1 }, }}, {$sort: {count: -1}}, {$limit: 5}, {$project: { _id: 0, year: '$_id.year', month: '$_id.month', day: '$_id.day', week: '$_id.week', count:1, }} ])
    > r
    {
        "errmsg" : "exception: invalid operator '$day'",
        "code" : 15999,
        "ok" : 0
    }
    >


