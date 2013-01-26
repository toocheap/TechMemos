# Node.js / Javascriptおぼえがき帳

##### 参考
- [Node.js v0.8.18 マニュアル & ドキュメンテーション](http://nodejs.jp/nodejs.org_ja/docs/v0.8/api/)

## 同期と非同期

- node.jsではノン・ブロッキング処理が基本。イベントドリブンかつシングルスレッドで動作する。そのためレースコンディションとは無縁。
- 関数の返り値などはコールバック関数を渡し呼び出しチェーンを作るか、返り値を複数にしたい場合はEventEmitterを継承する。
- 処理実行中は設定されたイベントごとにリスナーが呼び出される。
- 非同期なので処理の順序は保証されないことに注意。関数を呼び出すときにそれはブロックするかブロックしないかを気にすること。
- これらのイベントは EventEmitterを継承することで処理される。

## イベントドリブン処理の間違い例(結果不定)

	var fs = require('fs');
	fs.unlink('/tmp/hello', function (err) {
	  if (err) throw err;
	  console.log('successfully deleted /tmp/hello');
	});
	fs.stat('/tmp/world', function (err, stats) {
	  if (err) throw err;
	  console.log('stats: ' + JSON.stringify(stats));
	});

この場合にunlink()とstat()は非同期型のためstatが先に呼び出される場合がある。必要であれば同期型の関数を呼ぶかコールバックをチェーンする。

####コールバックチェーンの例

	fs.rename('/tmp/hello', '/tmp/world', function (err) { #完了コールバックの中で次を呼び出す
	  if (err) throw err;
	  fs.stat('/tmp/world', function (err, stats) {
	    if (err) throw err;
	    console.log('stats: ' + JSON.stringify(stats));
	  });
	});

####同期型の例

	var fs = require('fs');
	fs.unlinkSync('/tmp/hello');
	console.log('successfully deleted /tmp/hello');
	stats = fs.statSync('/tmp/world');
	console.log('stats: ' + JSON.stringify(stats));

## ファイル

###fsモジュールを使う。基本はPOSIX標準関数へのラッパーとして動作する。

	var fs = require("fs");

###ファイルの読み込み

	fs.readFile(ファイル名, [エンコード], [callback])を使う。

エンコーディングが指定されなければBufferオブジェクトがdataに入っている。

	fs.readFile("nick.txt", function(err, data) {
		 if (err) throw err;
		console.log(data);
	});

出力

	<Buffer 4c 69...>			# data は Bufferオブジェクト

エンコーディングを指定すると指定したエンコーディングでエンコードされる

	fs.readFile("nick.txt", 'utf8',function(err, data) {
		if (err) throw err;
		console.log(data);
	})

出力

	Lib=Bettie
	Reg=Reggie
	Larry=Lawrence
	Joann=Johanna
	Tessa=Terri
	.....

###ファイルの書き出し

	fs.writeFile(ファイル名, コンテンツ,[エンコード], [callback])を使う。
	fs.writeFile("write.txt", "Hello node.js!", function(err) {
		if (err) throw err;
		console.log("It has saved.");
	})

###同期で読み込み、書き込み

ブロッキングして書いて読む。

	> var fs = require('fs')
	> fs.writeFileSync("sync.txt", "this uses blocking functions");
	> var content = fs.readFileSync("sync.txt");
	> console.log(content)
	<Buffer 74 68 69 73 20 75 73 65 73 20 62 6c 6f 63 6b 69 6e 67 20 66 75 6e 63 74 69 6f 6e 73>
	> content.toString('utf8')				# Bufferをutf8でエンコード
	'this uses blocking functions'
	> var content = fs.readFileSync("sync.txt", "utf8");
	> console.log(content)
	this uses blocking functions

出力

	% cat write.txt
	It has saved.

### パスの連結

pathモジュールの[resolve()](http://nodejs.jp/nodejs.org_ja/docs/v0.8/api/path.html#path_path_resolve_from_to)を使う。これだと区切り文字もWindowsでもMacでもLinuxでも大丈夫。resolve()はrequireがモジュールを呼ぶときに毎回使用される。

	var jpgpath = path.resolve(curpath, "test.jpg");

### カレントディレクトリの取得

processモジュールのcwd()で取得できる

	> process.cwd()
	'/Users/too/Dropbox/apk/Node/NodeBegginerBook'

実行ファイル名から取得したい場合はpathモジュールのdirnameメソッドを使う。[require.main.filenameは実行しているスクリプトのファイル名](http://nodejs.jp/nodejs.org_ja/docs/v0.8/api/modules.html#modules_accessing_the_main_module)。

	var curpath = require("path").dirname(require.main.filename);

#####参考
- [How to get path to current script with node.js?](http://stackoverflow.com/questions/3133243/how-to-get-path-to-current-script-with-node-js)

### ディレクトリの変更

process.chdir()をつかう。

	> process.cwd()
	'/Users/too/Dropbox/apk/Node/NodeBegginerBook'
	> process.chdir("..")
	process.cwd()
	'/Users/too/Dropbox/apk/Node'

## 文字列

###標準出力はutilモジュールでできる。utilのメソッドは同期的に処理される点に注意。

#### 標準出力(改行なし)

	> util.print("aaa")
	aaa
	> util.print(a)
	1
	> util.print(b)
	sss
	> util.print(c)
	[object Object]

#### 標準出力(改行あり)

	> util.puts(a)
	1
	> util.puts(b)
	sss
	> util.puts(c)
	[object Object]

#### 標準エラー出力(改行あり)

	> util.error(a)
	1
	> util.error(b)
	sss
	> util.error(c)
	[object Object]

#### タイムスタンプ付き出力

	> util.log(a)
	23 Jan 16:40:02 - 1
	> util.log(b)
	23 Jan 16:40:04 - sss
	> util.log(c)
	23 Jan 16:40:05 - [object Object]

###出力はconsole.log()が簡単。フォーマット文字列も受け付ける。

	> console.log("Hello node.js");
	Hello node.js
	> console.log("%s", "aaaa")
	aaaa

###文字列操作

#### 文字列の連結は "+" でよい。

	> console.log("log" + "log");
	loglog

#### 前後のホワイトスペースを削除

	> '  Hello  '.trim()
	'Hello'

#### 色を付けて出力

colorsモジュールをインストールする(Stringを拡張するので注意)

	> require('colors');
	> console.log("red".red);
	red

### 文字列のフォーマット

utilモジュールを使う。%s,%dと%j(%jはJSONフォーマットで出力)がある。

	> require("util").format("%s:%d", "aaa","111")
	'aaa:111'
	> require("util").format("%j",c)
	'{"aaa":111}'

formatで出力されるとき変数はutil.inspect()で文字列化される

	> a = 1
	1
	> require("util").inspect(a)
	'1'
	> b = "sss"
	'sss'
	> require("util").inspect(b)
	'\'sss\''
	> c = {aaa:111}
	{ aaa: 111 }
	> require("util").inspect(c)
	'{ aaa: 111 }'

### Base64にエンコード/デコードする

Bufferをとおしてエンコードを指定する

	> var b = new Buffer("This is unicode string")	# 文字列からBufferオブジェクトを作成
	> typeof b										# たしかにBuffer
	'object'
	> require("util").inspect(b)
	'<Buffer 54 68 69 73 20 69 73 20 75 6e 69 63 6f 64 65 20 73 74 72 69 6e 67>'
	> b.toString()							# Bufferを文字列に変換
	'This is unicode string'
	> b.toString("base64")					# Base64にエンコード
	'VGhpcyBpcyB1bmljb2RlIHN0cmluZw=='
	> typeof s								# 文字列に変換されている
	'string'
	> Buffer(s,"base64").toString()			# Base64な文字列をデコード
	'This is unicode string'

## 配列

### 配列リテラル

	var a = [1,2,3]

### 配列に対する処理

配列の長さはlengthプロパティで取得できる

	> var a = ['john', 'michael', 'judy', 'mary']
	> a.length
	4

添字でアクセスした場合、あいだの要素が自動的に作られるので注意

	> myArray = new Array()
	[]
	> myArray[0] = 'start'
	'start'
	> myArray[99] = 'end'
	'end'
	> myArray.length
	100							# 100個できちゃった....

LILOなpush()/pop()

	> a.push(1)
	1					# aの個数を還す
	> a.push(2)
	2
	> a.push(23)
	3
	> a.push('aaa')
	4
	> a.push('bbb')
	5
	> a.push(54)
	6
	> a
	[ 1,
	  2,
	  23,
	  'aaa',
	  'bbb',
	  54 ]
	> a.pop()
	54				# pop()した値を返す
	> a.pop()
	'bbb'
	> a.pop()
	'aaa'
	> a.pop()
	23
	> a.pop()
	2
	> a.pop()
	1
	> a.pop()
	undefined
	> a
	[]

FIFOなshift()/unshift()

	> a.unshift(1)
	1				# a.lengthを返す
	> a.unshift(2)
	2
	> a.unshift(3)
	3
	> a.unshift(4)
	4
	> a
	[ 4, 3, 2, 1 ]
	> a.shift()
	4				#shift()された値を返す
	> a.shift()
	3
	> a.shift()
	2
	> a.shift()
	1
	> a.shift()
	undefined

配列の連結

	> [1,2,3].concat([4,5,6])
	[ 1,
	  2,
	  3,
	  4,
	  5,
	  6 ]

配列の文字列化

	> a
	[ 'michael', 'mary', 'JJ' ]
	> a.toString()
	'michael,mary,JJ'
	> a.join()
	'michael,mary,JJ'
	> a.join(" ")
	'michael mary JJ'

部分配列の取得

slice(begin,endをつかう。slice()は部分配列から新しい配列を作成する。

	> a
	[ 'john',
	  'michael',
	  'judy',
	  'mary' ]
	> a.slice(0)					# [0]から最後まで(endの省略時は最後を指す)
	[ 'michael',
	  'mary',
	  'judy',
	  'john' ]
	> a.slice(1)					# [1]から最後まで
	[ 'mary', 'judy', 'john' ]
	> a.slice(2)					# [2]から最後まで
	[ 'judy', 'john' ]
	> a.slice(3)					# [3]から最後まで
	[ 'john' ]
	> a.slice(4)					# [4]はないので、[]を返す
	[]
	> a.slice(1,2)					#[1]から[2]*の前*まで。endは含まない。
	[ 'mary' ]
	> a.slice(1,3)					#[1]から[3]*の前*まで。
	[ 'mary', 'judy' ]
	> a.slice(1,4)					#[1]から[4]*の前*まで。
	[ 'mary', 'judy', 'john' ]
	> a.slice(1,5)					#[1]から[5]*の前*まで。4はないので最後まで。
	[ 'mary', 'judy', 'john' ]
	> a.slice(-1)					#最後から数える
	[ 'john' ]
	> a.slice(a.length-1)			#つまりarray.length-endとおなじ。
	[ 'john' ]
	> a.slice(-2)					#[length-2=2]から最後まで
	[ 'judy', 'john' ]
	> a.slice(-3)					#[length-2=1]から最後まで
	[ 'mary', 'judy', 'john' ]
	> a.slice(-4)					#[length-4=0]から最後まで
	[ 'michael',
	  'mary',
	  'judy',
	  'john' ]
	> a.slice(2,-1)					#[2]から[length-1=2]まで。
	[ 'judy' ]
	> a								# aは変わってない。
	[ 'john',
	  'michael',
	  'judy',
	  'mary' ]

splice(index, howMany, elem1, elem2, ...)を使うと、元の配列を変更できる。

[2]から続く値を 0個 削除し、その場所に Aaron, JJ を追加

	> a.splice(2, 0, 'Aaron', 'JJ')
	[]
	> a
	[ 'michael',
	  'mary',
	  'Aaron',			#[2]から後ろに追加
	  'JJ',
	  'judy',			#元の[2]
	  'john',
	  'shade' ]

[2]を1個削除

	> a.splice(2, 1)
	[ 'Aaron' ]			# 削除した値を返す
	> a
	[ 'michael',
	  'mary',
	  'JJ',
	  'judy',
	  'john',
	  'shade' ]

[3]以降を削除

	> a.splice(3)
	[ 'judy', 'john', 'shade' ]	# howManyを省略するとそれ以降全部になる
	> a
	[ 'michael', 'mary', 'JJ' ]

ソートと反転

	> a
	[ 'john',
	  'michael',
	  'judy',
	  'mary' ]
	> a.sort()
	[ 'john',
	  'judy',
	  'mary',
	  'michael' ]
	> a.reverse()
	[ 'michael',
	  'mary',
	  'judy',
	  'john' ]

### 配列のイテレーション

forによるオブジェクトのループ。インデックス(キー)を使う場合。

- 配列

	> var foo = [4,5,6]
	foo
	[ 4, 5, 6 ]
	> for(var i=0;i<foo.length;i++) { console.log(foo[i]); }
	4
	5
	6
	undefined

- オブジェクトでも同じ

	> var bar = ['aaa', 'bbb', 'ccc']
	for(var i=0;i<bar.length;i++) { console.log(bar[i]); }
	aaa
	bbb
	ccc
	undefined

forによるオブジェクトのループ。オブジェクトを一個づつ取り出して処理

	> for (var i in a) { console.log("%d:%d", i, a[i]); }
	0:1
	1:2
	2:3

mapはオブジェクトを1個づつ取り出してcallback関数を適用する

	> [2,3,4].map(function(x) { return x * x })
	[ 4, 9, 16 ]

forEachとmapの違い

- forEachに返り値はない

	> a.forEach(function(v) { console.log(v); })
	1
	2
	3
	undefined

- mapは返り値が関数の返り値の配列になる。

	> a.map(function(v) { console.log(v); })
	1
	2
	3
	[ undefined, undefined, undefined ]

everyは要素をcallback関数に適用した結果が、*すべて true であれば*、true を返す。

	> var a = [1,2,3]
	> var b = [10,20,30]
	> var c = [5, 15, 25]
	> a.every(function over10(x) { return (x >= 10);})
	false
	> b.every(function over10(x) { return (x >= 10);})
	true
	> c.every(function over10(x) { return (x >= 10);})
	false

someは要素をcallback関数に適用した結果が、*少なくともひとつ true であれば*、true を返す。

	> a.some(function over10(x) { return (x >= 10);})
	false
	> b.some(function over10(x) { return (x >= 10);})
	true
	> c.some(function over10(x) { return (x >= 10);})
	true

filterは要素をcallback関数に適用した結果が true である結果を新規に配列を作成して返す

	> a.filter(function over10(x) { return (x >= 10);})
	[]
	> b.filter(function over10(x) { return (x >= 10);})
	[ 10, 20, 30 ]
	> c.filter(function over10(x) { return (x >= 10);})
	[ 15, 25 ]

畳み込みは reduceとreduceRightがある

	> [1,2,3,4,5,6,7,8,9].reduce(function(x,y) { return (x-y); })
	-43
	> [1,2,3,4,5,6,7,8,9].reduceRight(function(x,y) { return (x-y); })
	-27
	> 1-2-3-4-5-6-7-8-9
	-43
	> 9-8-7-6-5-4-3-2-1
	-27


##### 参考
- [MDN:配列](https://developer.mozilla.org/ja/docs/JavaScript/Reference/Global_Objects/Array)

## オブジェクト型

Javascriptに連想配列は存在しないが、Javascriptにおけるオブジェクトは*すべてが名前と値の組の集合*であるため、これを利用すれば実質的には連想配列が使用出来る。

	var handle = {};
	handle["/"] = requestHandler.start;
	handle["/start"] = requestHandler.start;
	handle["/upload"] = requestHandler.upload;

キーへのアクセスは[]も使えるが、"*.*"でもアクセスできる。

	> oa = {a:'aaa', b:'bbb'}
	{ a: 'aaa', b: 'bbb' }
	> oa['a']
	'aaa'
	> oa.a
	'aaa'

[]と*.key*の違いは、後者が静的なメンバーだけが指定できるのに対して、前者は実行時の式の結果を指定出来ます。

	> var love = { firstName: 'Élodie', lastName: 'Porteneuve' };
	> var useFirstName = true;
	> console.log(love[useFirstName ? 'firstName' : 'lastName']);
	Élodie

関数テーブルみたいな。

	> var i = {}
	> i.hello = function() { console.log("hello"); }
	[Function]
	> i.bye = function() { console.log("bye"); }
	[Function]
	> var sayHello = true;
	> i[sayHello ? 'hello' : 'bye']()
	hello
	> var sayHello = false;
	> i[sayHello ? 'hello' : 'bye']()
	bye

単純にkeyでアクセスすると自動的にキーが作成される。

	> var ooo = {}
	> ooo
	{}
	> ooo.a = 'aaa'
	'aaa'
	> ooo.b = 123
	123
	> ooo
	{ a: 'aaa', b: 123 }
	> ooo['ccc'] = {d:123,e:'eee'}
	{ d: 123, e: 'eee' }
	> ooo
	{ a: 'aaa',
	  b: 123,
	  ccc: { d: 123, e: 'eee' } }

オブジェクト型はすべてObjectを継承している。

	> Object.getOwnPropertyDescriptor(ooo,'a')
	{ value: 'aaa',
	  writable: true,
	  enumerable: true,
	  configurable: true }

object.__proto__は、そのobjectの継承元を指しており、代入することも可能

	> var foo = {}
	var bar = {hoge: 1}
	foo.__proto__ = bar				# 継承元を設定
	{ hoge: 1 }
	> Object.getPrototypeOf(foo) === bar
	true
	> foo.hoge							# bar.hogeを継承している
	1
	> var fuga = Object.create(bar)  	# 継承元からオブジェクトを生成
	Object.getPrototypeOf(fuga) === bar
	true

キーの取得は、Object.keys()をつかう。

	> Object.keys(oa)
	[ 'a', 'b' ]

## 型チェック

typeofを使って確認できるが、確認できるのはPrimitiveなものだけ。

	> function a() { console.log("a") };
	> a
	[Function: a]
	> typeof a
	'function'
	> b = 111
	111
	> typeof b
	'number'
	> c = "ccc"
	'ccc'
	> typeof c
	'string'
	> var d = {}
	d
	{}
	> typeof d
	'object'
	> typeof e
	'undefined'
	> oa = {a:'aaa', b:'bbb'}
	{ a: 'aaa', b: 'bbb' }
	> typeof oa		#配列もオブジェクトもオブジェクト
	'object'
	> typeof([1,2,3])
	'object'

配列かどうかはArray.isArray()をつかう

	> Array.isArray([1,2,3])
	true

## 関数

関数はfunctionで定義。引数に型はない。undefinedが変えるのは関数に返り値がないから。

	> function say(value) { console.log(value); };
	undefined

引数の数は少なく呼び出す文には*undefined*が入るだけ。多く呼び出す分には無視される。

	> function farg(a,b,c) { console.log(a,b,c) }
	farg("aaa", "bbb", "ccc")
	aaa bbb ccc
	farg("aaa", "bbb")
	aaa bbb defined
	> farg("aaa")
	aaa undefined defined
	> farg()							# なしでさえ呼べる
	undefined undefined farg("aaa", "bbb", "ccc", "ddd")	# 多い
	aaa bbb ccc
	undefined

関数に与えられた実引数は argumentsオブジェクトに入っている。
argumentsは仮引数の数とは全く関係ない模様。

	> function countArgs(a,b,c,d) { console.log(arguments.length) }
	> countArgs(1)
	1
	> countArgs(1,2)
	2
	> countArgs(1,2,3)
	3
	> countArgs(1,2,3,4)
	4
	> countArgs(1,2,3,4,5)			# あれ？
	5
	> countArgs(1,2,3,4,5,6)		# あれれ？
	6
	> countArgs(1,2,3,4,5,6,7)		# おおお
	7
	> function showArgs(a,b,c,d) { console.log(arguments) }
	> showArgs(1,2,3,4,5,6,7)
	{ '0': 1, '1': 2, '2': 3, '3': 4, '4': 5, '5': 6, '6': 7 }

型がないので呼び出し時に()をつけて呼び出せば関数になる？

	> function execute(someFunction, value) { someFunction(value); }
	undefined

呼び出してみる。

	> execute(say, "hello")
	hello
	undefined

匿名関数をその場で書いてもよい。

	> execute(function(value) { console.log(value); }, "hello")
	hello
	undefined

ただし匿名関数はスタックトレースに名前が表示されないので、できるだけ名前をつけておくのがtips

### 関数オブジェクト

関数は一級オブジェクトであり、Function型のオブジェクトである。

	> var f = new Function
	typeof f
	'function'

Function型のオブジェクトはprototypeというプロパティを持つが、これはObject.__proto__とは異なるものである。
prototypeプロパティはFunction.prototypeを指す。f.prototype.constructorがfにポイントされている

	> Object.getPrototypeOf(f) === f.prototype
	false
	> Object.getPrototypeOf(f) === Function.prototype
	true
	> f.prototype.constructor === f
	true

つまり親の代わりに原型（プロトタイプ）として使用されたオブジェクトが指定される。
これを利用してクラス定義を行うことができる。定義したクラスはnewでオブジェクトを作成でき、クラスメソッドはClass.prototype.classmethodへの代入で定義できる。

	> function ClassA() {}
	> ClassA
	[Function: ClassA]
	> ClassA.prototype.say = function(str) { console.log(str); }
	[Function]
	> a = new ClassA
	{}
	> var a = new ClassA
	> a.say("Hello prototype")
	Hello prototype

#### Function.prototypeの便利な関数

callは関数オブジェクトを第1引数をthis(実行コンテキスト）として呼び出す(でいいのかな？）。

	> var o = {a:1, b:2}
	> var func = function(x, y) { return this.a * x + this.b * y  }
	> func.call(o, 10, 20)
	50

applyはcallの引数を配列で渡す。

	> func.apply(o, [10, 20])
	50

## モジュール

###モジュールはrequireで呼び出して変数にbindして呼び出す。

	var http = require("http");
	http.createServer(.....)

###モジュール内で外部公開するメソッドをexports.外部公開名=内部関数名で指定

	exports.start = start;

###require()の読み込み順序

'/home/ry/projects/foo.js' の中で require('bar.js') を呼んでいた場合。
'./node_modules'にない場合は一つづつ上の階層のnode_modulesを探す。

1. /home/ry/projects/node_modules/bar.js
2. /home/ry/node_modules/bar.js
3. home/node_modules/bar.js
4. /node_modules/bar.js

## ノンブロッキング処理

### child_processモジュールを使って子プロセスを起動する。

	var exec = require("child_process").exec;
	#timeout(ms)がたつかmaxbufferを超える出力位があると子プロセスはkillされる
	exec("find /", {timeout:10000, maxBuffer:20000*1024},
		function(error, stdout, stderr){
			console.log('stdout:' + stdout);
			console.log('stderr:' + stdoerr);
			if(error != null) {
				console.log("exec error:" + error)
			}
	});

### sleep()の代わり

	function sleep(milliseconds){
		var startTime = new Date().getTime();
		while (new Date().getTime() < startTime + milliseconds);
	}
	sleep(10000);

### 引数を取得

process.argvをつかう

##### argv.js

	process.argv.map(function(av) { console.log(av); });

結果

	$ node argv.js 1 aaa bbb 4
	node
	/Users/too/Dropbox/apk/Node/tmp/argv.js
	1
	aaa
	bbb
	4

## HTTPクライアント

### URLを引数にとって、標準出力に表示

	var http = require("http"),
		util = require("util"),
		url = require("url");

	function download_one(urlstr) {
		var u = url.parse(urlstr);
		var options = {
			hostname: u.hostname,
			port: 80,
			path: u.pathname,
			method: 'GET',
			headers: {
				host: u.hostname
			}
		};

		var request = http.request(options, function(res){
			// ステータスコードとヘッダの出力
			console.log(res.statusCode);
			for (var i in res.headers) {
				console.log(i + ": ", res.headers[i]);
			}
			console.log("");

			// エンコードを指定
			res.setEncoding("utf8");
			// データ受信時
			res.on("data", function onRecv(chunk){
				// chunkは受信したデータ（全部とは限らない)
				util.print(chunk);
			});
			// データ受信終了時
			res.on("end", function onEnd(){
				console.log("");
			});
		});
		// リクエストの送信終了
		request.end();
	}

	download_one(process.argv[2]);

GETだけならメソッドをgetにすれば、ちょっとだけみじかく。

	var options = {
		hostname: u.hostname,
		port: 80,
		path: u.pathname,
	//	method: 'GET',
		headers: {
			host: u.hostname
		}
	};

	var request = http.get(options, function(res){

もっとみじかく。[superagentモジュール](https://github.com/visionmedia/superagent)を使う

	> require('superagent').get('http://graph.facebook.com/774635482').end(function(res){ console.log("Gottya!") })
	{ method: 'GET',
	  url: 'http://graph.facebook.com/774635482',
	  header: {},
	  writable: true,
	  _redirects: 0,
	  _maxRedirects: 5,
	  attachments: [],
	  cookies: '',
	  _redirectList: [],
	  _events: { end: [Function], response: [Function] },
	  req:
	   { domain: null,
	     _events:
	      { socket: [Object],
	        drain: [Function],
	        error: [Function],
	        response: [Function] },
	     _maxListeners: 10,
	     output: [ 'GET /774635482 HTTP/1.1\r\nHost: graph.facebook.com\r\nCookie: \r\nConnection: keep-alive\r\n\r\n' ],
	     outputEncodings: [ undefined ],
	     writable: true,
	     _last: true,
	     chunkedEncoding: false,
	     shouldKeepAlive: true,
	     useChunkedEncodingByDefault: false,
	     sendDate: false,
	     _hasBody: true,
	     _trailer: '',
	     finished: true,
	     agent:
	      { domain: null,
	        _events: [Object],
	        _maxListeners: 10,
	        options: {},
	        requests: {},
	        sockets: [Object],
	        maxSockets: 5,
	        createConnection: [Function] },
	     socketPath: undefined,
	     method: 'GET',
	     path: '/774635482',
	     _headers: { host: 'graph.facebook.com', cookie: '' },
	     _headerNames: { host: 'Host', cookie: 'Cookie' },
	     _header: 'GET /774635482 HTTP/1.1\r\nHost: graph.facebook.com\r\nCookie: \r\nConnection: keep-alive\r\n\r\n',
	     _headerSent: true },
	  protocol: 'http:',
	  host: 'graph.facebook.com',
	  _callback: [Function] }
	> Gottya!

## Stream

ファイルやネットワークを扱うときに流すデータが小さい時はいいけど、でかい時はリソースを食うのでStreamを間に介するといいよ！という話。

### Streamには標準で2+2種類ある

- Readable Stream
- Writable Stream
- Filter Stream
- Duplex Stream

### Readable Stream

process.stdin, fs.readStream, http.serverRequest なんかが ReadableStream。
Stream側のイベントで呼ぶためにブロックされない。

	require('http').createServer(function(req, res) {
	    res.writeHead(200, { 'Content-Type': 'image/jpeg' } );
	    var stream = require('fs').createReadStream('shiba.jpg');
	    stream.on('data', function(data){
	    	res.write(data);
	    });
	    stream.on('end', function(data){
	    	res.end();
	    });
	}).listen(3000);

pipeを使うとon, endなどのイベントをそれぞれ定義しなくても、ReadbleStreaam=>WritableStreamと流れるので超便利。

	require('http').createServer(function(req, res) {
	    res.writeHead(200, { 'Content-Type': 'image/jpeg' } );
	    require('fs').createReadStream('shiba.jpg').pipe(res);
	}).listen(3000);

##### 参考
- [Stream 公式ライブラリドキュメント](http://nodejs.jp/nodejs.org_ja/docs/v0.8/api/stream.html)
- [Stream Stream Stream](http://jxck.node-ninja.com/slides/nodeacademy7.html)
- [Stream-Handbook](https://github.com/substack/stream-handbook)
