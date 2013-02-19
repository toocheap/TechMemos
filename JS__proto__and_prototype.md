# __proto__ と prototype

## オブジェクト型が持つプロパティ __proto__, prototype, constructorの関係

- オブジェクト型は（拡張された）連想配列にすぎない。
- 生成されたオブジェクトは、**プロトタイプオブジェクト**を持つ。

例

	> var a = new Object()

生成されたオブジェクトaのprototypeプロパティa.prototypeはnullである。

	> a.prototype
	undefined
	> a.prototype == null
	true

生成されたオブジェクトaの__proto__プロパティとa.prototypeは別物である。

	> a.__proto__ !== a.prototype
	true

生成されたオブジェクトaの__proto__プロパティは、親オブジェクトObjectのprototypeプロパティと同じオブジェクトを指している。

	> a.__proto__ === Object.prototype
	true

生成されたオブジェクトaのconstructorプロパティは、コンストラクタ関数であった関数オブジェクトObject()だが、これはプロトタイプチェーンを辿って得たものである。

	> a.constructor
	[Function: Object]
	> a.constructor.name
	'Object'
	> a.constructor === Object			#同じObjectを指している。
	true
	> a.hasOwnProperty('constuctor')	#自身のプロパティではない。
	false

## プロトタイプチェーン

上のconstructorのように、呼び出されたメソッドやプロパティが自オブジェクトにない場合は__proto__, __proto__.__proto__というように親オブジェクとをたどって呼び出される。これをプロパティチェーンと呼ぶ。

__proto__などはget/set可能な単なるプロパティであり、代入によってプロパティチェーンを変更できる。


	> var a = {}
	> var b = {}
	> var c = {}
	> b.__proto__ = a
	{}
	> c.__proto__ = b
	{}
	> printArray(protoChain(c))
	0 : [object Object]
	1 : [object Object]
	2 : [object Object]
	3 : [object Object]

ちなみに循環したチェーンは追加時に検出される。

	> c.__proto__ = b
	{}
	> b.__proto__ = a
	{}
	> a.__proto__ = c
	Error: Cyclic __proto__ value		# 循環する所でエラー

プロパティチェーンは返される親オブジェクトがnullになるまで辿られる。少なくともObject.prototype.__proto__はnullである(Object.__proto__は[Function Empty]である)。

> Object.prototype.__proto__
null
> Object.__proto__
[Function: Empty]

## コンストラクタと__proto_、prototypeの関係

コンストラクタ関数が呼ばれて、元オブジェクトから新オブジェクトが生成されるとき、以下のようにプロパティがセットされる。

	a.prototype = new Object;				# prototypeには元オブジェクト関数
	a.__proto__ = Constructor.prototype;

このおかげでプロトタイプチェーンを使って、生成したオブジェクトに対して後からメソッドを追加することができる。
次オブジェクトではなく、prototypeプロパティが指す元オブジェクトに対してメソッドを追加することで、次オブジェクトだけではなく元オブジェクトを同じとするすべてのオブジェクトに新メソッドが追加される。

	> var ar = new Array()
	> ar
	[]
	> ar.fill()									# この時点ではfill()の呼び出しはエラーとなる。
	TypeError: Object  has no method 'fill'
	> Array.prototype.fill = function() {}		# Array.prototype.fillに 空関数をセット
	[Function]									# これはObjectに追加することになる。
	> ar.fill()									# エラーにならなくなった。
	undefined
	> ar.hasOwnProperty('fill')					# 自オブジェクトが持つ関数ではなく
	false
	> ar.__proto__.hasOwnProperty('fill')		# 元オブジェクトが持つ関数が呼ばれている。
	true
	> ar2 = new Array()							# もちろん新しく生成しても
	[]
	> ar2.fill()								# 元オブジェクトに追加されているので呼び出しが可能
	undefined

## 結局どういうふうに使うのさ

- constuctor = 初期化に使われた関数（であることが期待される）。
- prototype  = コンストラクタで初期化されたオブジェクトを指すので、これでクラスメソッドのようにメソッドやプロパティを追加できる。ここでついかしなければRubyの得意メソッドのようにオブジェクトに限定されたメソッドになってしまう。
- __proto__  = プロトタイプオブジェクト。プロトタイプチェーンを構成するために存在する。

クラスや継承といった概念が(実装上は)ないため、継承で期待される親メソッドやプロパティの呼び出しを、プロトタイプチェーンとconstructorを自分で設定することでオブジェクト同士のメソッドをミックスさせることができてしまう。特にJavascriptではメソッドはオブジェクトとは結びついていない。メソッドにコンテキストとして他のオブジェクトを与えることで、実行コンテキストを変更することさえできる。これは引数であるargumentsオブジェクトを配列オブジェクトに変換するイディオム Array.prototype.slice.call(arguments) でよくわかる。

	> function A() {}
	undefined
	> function B() {}
	undefined
	> B.prototype
	{}
	> B.prototype.b = function() { return 'b'; }
	[Function]
	> A.prototype = new B();
	{}
	> A.prototype.__proto__ === B.prototype
	true
	> A.prototype.a = function() { return 'a'; }
	[Function]
	> var a = new A();
	undefined
	> console.log(a)
	{}
	undefined
	> console.log(a instanceof B )
	true
	undefined
	> console.log(a.__proto__.__proto__ === B.prototype)
	true
	undefined

## __proto__とutil.inherit()の違い

__proto__の場合

	> function Animal() {}
	undefined
	> function Ferret() {}
	undefined
	> Ferret.prototype.eat = true;						# 親オブジェクト.eat == true
	true
	> Ferret.prototype.__proto__ = Animal.prototype;	# プロトタイプチェーンを設定
	{}
	> Ferret.prototype.bark = false;					# 親オブジェクト.bark == false
	false
	> var ferret = new Ferret();						# 新オブジェクトを作って
	undefined
	> console.log(ferret.eat, ferret.bark);				# eatとbarkを呼び出すと、もともと設定した値になる。
	true false
	undefined

util.inheritの場合

	> function Animal() {}
	undefined
	> function Ferret() {}
	undefined
	> Ferret.prototype.eat = true;					# ここでのFerret.prototypeはAnimalではない
	true
	> require('util').inherits(Ferret, Animal);		# inheritでAnimalから継承してFerret生成
	undefined
	> Ferret.prototype.bark = false;				# Ferret.prototypeであろうAnimal.barkをfalseにした（つもり）
	false
	> var ferret = new Ferret();					# オブジェクト生成
	undefined
	> console.log(ferret.eat, ferret.bark);			# prototypeが上書きされたからか、ferret.eatがundefinedになる。
	undefined false
	undefined

##### 参考
http://emaame.com/20080121.html





メソッドは自オブジェクト、__proto__に設定されたオブジェクト、__protp__.__proto__に設定されたオブジェクト...というふうに検索される。この__proto__のチェーンをプロトタイプチェーンと呼ぶ。
プロトタイプチェーンを検索する以下のメソッドを用意して、いろんな型やオブジェクトを見てみる。

	function protoChain(x) {
		var chain = [];
		for (var p = x; p != null; p = p.__proto__) {
			chain.push(p);
		}
		return chain;
	}
	function printArray(a) {
		for (var i = 0; i < a.length && i < 10; i++) {
			console.log(i + " : " + a[i]);
		}
		if ( i < a.length) { console.log("...(mode)"); }
	}

	> printArray(protoChain(1))
	0 : 1
	1 : 0
	2 : [object Object]
	> printArray(protoChain("str"))
	0 : str
	1 :
	2 : [object Object]
	> printArray(protoChain([1,2]))
	0 : 1,2
	1 :
	2 : [object Object]
	> printArray(protoChain({}))
	0 : [object Object]
	1 : [object Object]
	> printArray(protoChain(Number))
	0 : function Number() { [native code] }
	1 : function Empty() {}
	2 : [object Object]
	> printArray(protoChain(c))
	0 : [object Object]
	1 : [object Object]
	2 : [object Object]
	3 : [object Object]

プロトコルチェーンを結んでみる。

a<-b<-cの場合

	> var a = {}
	> var b = {}
	> var c = {}
	> b.__proto__ = a
	{}
	> c.__proto__ = b
	{}
	> printArray(protoChain(c))
	0 : [object Object]
	1 : [object Object]
	2 : [object Object]
	3 : [object Object]

a<-b, a<-cの場合

	> var a = {}
	> var b = {}
	> var c = {}
	> b.__proto__ = a
	{}
	> c.__proto__ = a
	{}
	> printArray(protoChain(c))
	0 : [object Object]
	1 : [object Object]
	2 : [object Object]
	> printArray(protoChain(b))
	0 : [object Object]
	1 : [object Object]
	2 : [object Object]
	> printArray(protoChain(a))
	0 : [object Object]
	1 : [object Object]

a<-b<-c<-aの場合は？

	> c.__proto__ = b
	{}
	> b.__proto__ = a
	{}
	> a.__proto__ = c
	Error: Cyclic __proto__ value		# 循環する所でエラー

__proto__をつかうと、プロトタイプチェーンを辿ってプロパティをnullになるまでたどり、あればそのプロパティが使われる。

	> var point = {x:20, y:30}
	> point.x
	20
	> point.y
	30
	> point.color						# colorはない
	undefined
	> typeof point.color				# color自体が undefined.
	'undefined'
	> var colored = {color: 'red'}		# 別のcolored オブジェクトを作る
	undefined
	> point.__proto__ = colored			# チェーンを繋ぐ
	{ color: 'red' }					# point.__proto__にはcolorプロパティがある
	> point.color						# pointになくてもpoint.__proto__.colorが呼ばれる
	'red'

すべてのオブジェクト型はObjectから繋がっているので、Objectの__proto__チェーンがnullになっているのでチェーンは必ず終端する。

> Object.prototype.__proto__
null

##### 参考
[プログラマのためのJavaScript (7)：プロトタイプ継承の正体](http://d.hatena.ne.jp/m-hiyama/20050908/1126154117)

オブジェクトのコンストラクタは単なる関数

	> var x = 10
	> var y = 20
	> function point(x,y) { this.x = x; this.y = y; }
	> point(70,80)	# このコンテキストでのthisはグローバル変数
	> x
	70				# globalのxとyがPointの中で変更されてる....
	> y
	80

関数オブジェクトのprototypeプロパティを見てみる。__proto__とは関係がないことに注意。

	> typeof sum
	'function'					# 関数オブジェクト
	> typeof point
	'function'					# コンストラクタのつもりだけど、これも関数オブジェクト
	> typeof point.prototype
	'object'					# 関数オブジェクトのprototypeプロパティは Object
	> point.__proto__
	[Function: Empty]			# 関数オブジェクトの__proto__プロパティは 関数オブジェクト Empty() ???

コンストラクタはただの関数。関数オブジェクトのconstructorプロパティにセットされる。

	> function sum(x,y) { return x+y }
	undefined
	> sum(1,2)
	3
	> var z = new sum
	undefined
	> var z = new sum(3,4)		# コンストラクタ形式(new Object)でも呼べちゃう...
	undefined
	> z
	{}
	> typeof z
	'object'
	> typeof z.prototype
	'undefined'
	> typeof z.__proto__
	'object'
	> sum.constructor.name
	'Function'

オブジェクトを生成(new)すると、生成したオブジェクトの__proto__プロパティに、コンストラクタ関数のprototypeオブジェクトがセットされる。

	> var pt = new point(10,20)
	undefined
	> pt.__proto__ === point.prototype	#　同じ物を指している
	true
	> var d = new Date()
	undefined
	> d.__proto__ === Date.prototype
	true

オブジェクトは自分を生成したコンストラクタメソッドはconstructorプロパティにセットされる？？

	> pt.constructor
	[Function: point]
	> pt.constructor.name
	'point'
	> d.constructor
	[Function: Date]
	> d.constructor.name
	'Date'

以下の関数で確認してみる。

	function hasProperProp(a, p) {
		var result;
		var orig = a.__proto__;					// いったん待避
		a.__proto__ = null;						// __proto__チェーンを断ち切る
		result = (typeof a[p] != 'undefined');	// 調べる
		a.__proto__ = orig;						// もとに戻す
		return result;
	}

と思ったら、__proto__チェーンをたどっていた。
生成されたオブジェクトのconstructorプロパティは親プロジェクトの__proto__に記録された情報が提供されている。

生成したオブジェクトの__proto__プロパティ === 親オブジェクトのprototypeプロパティ（が指すオブジェクト)
生成したオブジェクトのconstructorプロパティ === 親オブジェクトの__proto__.constructor

となる。

> hasProperProp(d, "constructor")
false
> hasProperProp(d.__proto__, "constructor")
true
> hasProperProp(pt, "constructor")
false
> hasProperProp(pt.__proto__, "constructor")
true
