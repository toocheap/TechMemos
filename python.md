# Python snipets

---

##### 参考
- [Python 2.7ja1 documentation](http://docs.python.jp/2.7/index.html)
- [Python 標準ライブラリ](http://docs.python.jp/2.7/library/index.html)
- [Python Tips Advent Calendar 2012](http://atnd.org/events/34762)

## ファイルエンコードを指定する

 #!/usr/bin/env
 # -*- coding: utf-8 -*-

vimなら

 #!/usr/bin/env
 # vim:fileencoding=utf-8

ファイルの記述エンコードをcodingに記述。基本UTF-8でいいはず。
#####参考
- [2.1.4 エンコード宣言 (encoding declaration)](http://docs.python.jp/2.5/ref/encodings.html)

---

## ファイル

### ファイルのオープンして1行ずつ読み込む

 from __future__ import with_statement
 with open("target.txt") as f:
  for line in f:
   print line.rstrip()

- withステートメントを使うことでcloseが要らない。
- 読み込んだ行端の文字を削除するには rstrip()を使う

### ファイルのオープンして1行ずつ読み込む

 from __future__ import with_statement
 with open("target.txt") as f:
  lines = f.readlines()


### 文字エンコーディングを指定してファイルをオープンする

 from __future__ import with_statement
 import codecs

 with codecs.open("target.txt", "r", "shift_jis") as f:
  for line in f:
   print line.rstrip()

codecsをimportし、文字エンコードを指定する。この場合オープンしたファイルのエンコードによらずfはshift_jisエンコードに自動変換される。"w"でオープンすれば書き込むファイルのエンコードを指定して自動変換する。

### ファイルを読み込んで配列に保存

####sampleファイル("="で分割して保存)

    Lib=Bettie
    Reg=Reggie
    Larry=Lawrence
    Joann=Johanna
    Tessa=Terri

#####ソース

    from __future__ import with_statement
    wordlist = []
    with open("sample.txt") as f:
        for line in f:
            a, b = line.rstrip().split('=') #行末の改行をとって"="で分割
            wordlist.append([a,b])

#####結果

    >>> wordlist
    [['Lib', 'Bettie'], ['Reg', 'Reggie'], ['Larry', 'Lawrence'], ['Joann', 'Johanna'], ['Tessa', 'Terri']]
---

## リスト(配列)

### リスト生成

リテラル
 >>> xs = [1,2,3]
 >>> xs
 [1, 2, 3]

### リスト処理

#### スライスで部分を指定
 >>> xs = range(10)
 >>> xs
 [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
 >>> xs[:]       # リストのコピー
 [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

部分取得

 >>> xs[0]     # 添字で指定
 0
 >>> xs[0:2]     # 0番目から、2個
 [0, 1]
 >>> xs[0:5]     # 0番目から、5個
 [0, 1, 2, 3, 4]
 >>> xs[2:5]     # 2番目から、5個
 [2, 3, 4]


xs[2:]とxs[:2]を連結するとxsになることに注意

 >>> xs[2:]     # 2番目から残り全部
 [2, 3, 4, 5, 6, 7, 8, 9]
 >>> xs[:2]     # 最初から2番目まで
 [0, 1]

マイナス指定で後から指定できる。xs[-num]はxs[len(list)-num]と同じ。配列数を数えなくても良い簡便な記法。

 >>> xs[:10-2]
 [0, 1, 2, 3, 4, 5, 6, 7]
 >>> xs[:-2]
 [0, 1, 2, 3, 4, 5, 6, 7]

 >>> xs[:-2]     # 最初から、後ろから2番目を除く(==xs[:10-2])
  [0, 1, 2, 3, 4, 5, 6, 7]
 >>> xs[-2:]     # 後ろから2番目、以降全部(==xs[10-2]==xs[8])
 [8, 9]
 >>> xs[-5:]     # 後ろから5番目、以降全部
 [5, 6, 7, 8, 9]
 >>> xs[-5:-2]    # 後ろから5番目までから、後ろ2個を除く
 [5, 6, 7]

ステップも指定可能

 >>> xs[::2]     # 最初から、2番目ずつ
 [0, 2, 4, 6, 8]
 >>> xs[1::2]    # １番目から、2番目ずつ
 [1, 3, 5, 7, 9]

ステップに"-"指定で逆方向に数える。これで配列を逆方向に走査できる。

 >>> xs[::-1]    # リストを反転(後ろから1個ずつ)
 [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
 >>> xs[-1::-2]    # 後ろから1番目から最後までで、後ろから2番目ずつ
 [9, 7, 5, 3, 1]
 >>> xs[-1:-5:-2]   # 後ろから1番目から、後ろから5番目までで、後ろから2番目ずつ
 [9, 7]
 >>> xs[-1:-3:-2]   # 後ろから1番目から、後ろから3番目までで、後ろから2番目ずつ
 [9]
 >>> xs[-1:-7:-2]   # # 後ろから1番目から、後ろから7番目までで、後ろから2番目ずつ
 [9, 7, 5]

#### スライスに代入

 >> xs =range(10)
 >>> xs[:5] = range(5)
 >>> xs
 [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
 >>> xs[5:] = range(5)
 >>> xs
 [0, 1, 2, 3, 4, 0, 1, 2, 3, 4]
 >>> xs[::-1]
 [4, 3, 2, 1, 0, 4, 3, 2, 1, 0]

ステップも指定可能

 >>> xs[::2] = xs[::-2]
 >>> xs
 [9, 1, 7, 3, 5, 5, 3, 7, 1, 9]

ただし要素数が同じでないと例外が投げられる。

 >>> xs[::2] = xs[::-3]
 Traceback (most recent call last):
   File "<stdin>", line 1, in <module>
 ValueError: attempt to assign sequence of size 4 to extended slice of size 5

##### 参考
[スライス操作あれこれ](http://d.hatena.ne.jp/melpon/20121203/1354542386)

### ループ

### ソート

#### リストのメソッドを使う

    >>> hoge = [['C', 0],['A',3],['D',2]]
    >>> hoge.sort()
    >>> hoge
    [['A', 3], ['C', 0], ['D', 2]]
    >>> hoge.reverse()                      #逆順
    >>> hoge
    [['D', 2], ['A', 3], ['C', 0]]
    >>> hoge = [['C', 0],['A',3],['D',2]]
    >>> hoge.reverse(key=lambda x: x[1])   # reverseにはkeyはない
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    TypeError: reverse() takes no keyword arguments
    [['C', 0], ['D', 2], ['A', 3]]
 >>> hoge = [['C', 0],['A',3],['D',2]]
 >>> hoge.sort(key=lambda x: x[1], reverse=True) #sortのreverseパラメータを使う
 >>> hoge
 [['A', 3], ['D', 2], ['C', 0]]

#### sorted()関数を使う

    >>> hoge = [['C', 0],['A',3],['D',2]]
    >>> sorted(hoge)
    [['A', 3], ['C', 0], ['D', 2]]

#### 配列の配列を特定のカラムでソート

#### メソッド

    >>> hoge = [['C', 0],['A',3],['D',2]]
    >>> hoge.sort(key=lambda x: x[1])
    >>> hoge
    [['C', 0], ['D', 2], ['A', 3]]

#### 関数

    >>> hoge = [['C', 0],['A',3],['D',2]]
    >>> sorted(hoge, key=lambda x: x[1])
    [['C', 0], ['D', 2], ['A', 3]]

#### クラスのインスタンスを特定の属性でソート

##### クラス定義

    class Student:
            def __init__(self, name, grade, age):
                self.name = name
                self.grade = grade
                self.age = age
            def __repr__(self):
                return repr((self.name, self.grade, self.age))

##### オブジェクト

    student_objects = [
        Student('john', 'A', 15),
        Student('jane', 'B', 12),
        Student('dave', 'B', 10),
    ]

##### ageのカラムでソート：

    >>> sorted(student_objects, key=lambda student: student.age)   # sort by age
    [('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]

### operatorを使うともっと早い

 >>> from operator import itemgetter, attrgetter
 >>> sorted(student_objects, key=attrgetter('age'))
 [('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
#### grade→ageの順でソートもできる
 >>> sorted(student_objects, key=attrgetter('grade', 'age'))
 [('john', 'A', 15), ('dave', 'B', 10), ('jane', 'B', 12)]

#####参考
[ソート HOWTO](http://docs.python.jp/2/howto/sorting.html)

---
## リスト

### rubyのflatten()みたいなリストのフラット化

リスト内包表記で複数回 for を回す。この場合生成されるリストが非常に大きい場合に問題。

 >>> xss
 [['C', 0], ['A', 3], ['D', 2]]
 >>> [x for xs in xss for x in xs]
 ['C', 0, 'A', 3, 'D', 2]

itertoolsのchainを使うほうがリソース消費が少ない（内部でリストを使用しない）

 >>> from itertools import chain
 >>> list(chain(*xss))
 ['C', 0, 'A', 3, 'D', 2]

#####参考
[データのフラット化](http://d.hatena.ne.jp/melpon/20121215/1355532721)

### 1から10までの数字のリストを作成する

#### range関数はリストを返す

 >>> range(1,10)
 [1, 2, 3, 4, 5, 6, 7, 8, 9]

リストを作成する分、リソースを食う

#### xrange関数はxrangeオブジェクトを返す

 >>> xrange(1,10)
 xrange(1, 10)
 >>> list(xrange(1,10))  #リストに変換
 [1, 2, 3, 4, 5, 6, 7, 8, 9]

一時的にしか使わないのであればxrange()を使うほうが良いかも。

#### 1から10までの奇数のリスト

 >>> range(1,10,2)
 [1, 3, 5, 7, 9]
 >>> list(xrange(1,10,2))
 [1, 3, 5, 7, 9]

ステップも刻める。

##### (参考)高階関数/リスト内包表記を使う

 >>> filter(lambda x: x % 2 != 0, xrange(1,10))
 [1, 3, 5, 7, 9]
 >>> [x for x in xrange(1,10) if x % 2 != 0]
 [1, 3, 5, 7, 9]

---
## 文字・文字列処理

### 文字列のエンコード変換
#### Unicodeリテラル

 s = u"こんにちは、世界"

 ファイルエンコーディングが使われる

### 文字列の部分とりだし

#### スライス

ほとんどスライスで処理できる

#### 文字列処理
#### 検索
 >>> s = "Hello world Hello"
 >>> s.find("Hello")
 0
 >>> s.rfind("Hello")
 12

返されるのは検索文字列が存在する最初のindex

 >>> s[s.index("Hello"):]
 'Hello world Hello'
 >>> s[s.rindex("Hello"):]
 'Hello'

見つからなければ -1 を返す

 >>> s.find("Good")
 -1
 >>> s.rfind("Good")
 -1

index(),rindex()はfind(),rfindと同じ

 >>> s.index("Hello")
 0
 >>> s.rindex("Hello")
 12

検索文字列が対象文字列に含まれる数を返す。

 >>> s.count("Hello")
 2

前方一致・後方一致

 >>> s = "Hello world Hello"
 >>> s.startswith("Hello")
 True
 >>> s.endswith("Hello")
 True
 >>> s.startswith("Hell")
 True
 >>> s.endswith("Hell")
 False

#### 置換

文字列は普遍(immutable)なので、変更はできない。

 >>> s.find('e')
 1
 >>> s[1:4] = "ELLO"
 Traceback (most recent call last):
   File "<stdin>", line 1, in <module>
 TypeError: 'str' object does not support item assignment

replace()は置換した文字列を(作成して）返す。

 >>> s.replace("ello", "ELLO")
 'HELLO world HELLO'
 >>> s
 'Hello world Hello'    # 元文字列は変更されていない。

stringモジュールのmaketrans()とtranslate()を組み合わせてtrコマンドと同様の処理を行う。

 >>> import string
 >>> s
 'Hello! How are you?'
 >>> s.translate(string.maketrans("?!","-="))
 'Hello= How are you-'
 >>> s
 'Hello! How are you?'

##### 参考

translate()の引数は変換テーブルでこのテーブルに従って文字を変換する。

 >>> string.maketrans("?!","-=")
 '\x00\x01\x02\x03\x04\x05\x06\x07\x08\t\n\x0b\x0c\r\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f ="#$%&\'()*+,-./0123456789:;<=>-@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\\]^_`abcdefghijklmnopqrstuvwxyz{|}~\x7f\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff'
 >>> print string.maketrans("?!","-=")
>="#$%&'()*+,-./0123456789:;<=>-@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????

#### 文字列の分割・結合

split()で分割

 >>> s = "twincle twincle little star"
 >>> s
 'twincle twincle little star'
 >>> s.split()
 ['twincle', 'twincle', 'little', 'star']
 >>> s = "twincle, twincle, little star"
 >>> s.split()        # 引数なしは空白文字列で分割
 ['twincle,', 'twincle,', 'little', 'star']
 >>> s.split(',')
 ['twincle', ' twincle', ' little star']
 >>> s.split(',',1)       # 1回だけ分割
 ['twincle', ' twincle, little star']

'<delimitar>'.join(list)で結合

    >>> s = "twincle twincle little star"
    >>> s
    'twincle twincle little star'
    >>> s.split()
    ['twincle', 'twincle', 'little', 'star']
    >>> ','.join(s.split())                         # ','で結合
    'twincle,twincle,little,star'
    >



### 正規表現
#### Base64のエンコード・デコート

encode(), decode()で簡単。

 >>> 'Hello World'.encode("base64")
 'SGVsbG8gV29ybGQ=\n'
 >>> 'Hello World'.encode("base64").decode("base64")
 'Hello World'

#####参考
[zlib, base64, rot13 エンコーディング](http://d.hatena.ne.jp/melpon/20121222/1356255226)
[文字列の操作](http://d.hatena.ne.jp/yumimue/20071223/1198407682)
---

## 高階関数

### リストを畳み込む(reduce, fold-left)

順番に要素を舐めていく。

(((0 + 1) + 2) + 3)

    >>> reduce(lambda acc, item: acc+item, [1, 2, 3], 0)
    6

(((7 - 1) - 2) - 3)

 >>> reduce(lambda acc, item: acc-item, [1, 2, 3], 7)
 1

((1 - 2) - 3)

 >>> reduce(lambda acc, item: acc-item, [1, 2, 3])
 -4

#### 参考
- [9.7. itertools — 効率的なループ実行のためのイテレータ生成関数](http://docs.python.jp/2.7/library/itertools.html)
- [itertoolsモジュールをひと通り眺めてみた](http://kk6.hateblo.jp/entry/20110521/1305984781)

## システム系

## 引数の処理


