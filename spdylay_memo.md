# Mac OSXでの[spdylay](http://sourceforge.net/projects/spdylay/files/stable/)のインストールメモ

	git clone https://
	autoreconf -i
	automake
	autoconf
	./configure
	make
	make install


## autoredonf -i を実行するとAC_PATH_XML2がないといわれる

	$ autoreconf -i
	configure.ac:146: warning: macro 'AM_PATH_XML2' not found in library
	configure.ac:146: warning: macro 'AM_PATH_XML2' not found in library
	configure.ac:146: error: possibly undefined macro: AM_PATH_XML2
	      If this token and others are legitimate, please use m4_pattern_allow.
	      See the Autoconf documentation.
	autoreconf: /usr/local/Cellar/autoconf/2.69/bin/autoconf failed with exit status: 1

勘違いしてLDFLAGやCPPFLAGを環境変数に定義したりしたけれど結局そこではなくて(それはそれで必要)、AM_PATH_XML2を定義しているm4マクロが足りなかった。
AC_PATH_XML2のm4マクロをぐぐって探して[ここ](http://keithr.org/eric/src/whoisserver-nightly/m4/am_path_xml2.m4)からダウンロードして、m4ディレクトリ以下に配置した。

## spdycatなどのコマンドが作られない。

OSデフォルトのOpenSSLが古かった。

	checking for OPENSSL... no
	configure: Requested 'openssl >= 1.0.1' but version of OpenSSL is 0.9.8j
	configure: The example programs will not be built.

SPDYで使用される[NPN](http://technotes.googlecode.com/git/nextprotoneg.html)は1.0.1以降で実装されているので最新(>1.0.1)のOpenSSLが必要。

	$ openssl
	OpenSSL> version
	OpenSSL 0.9.8r 8 Feb 2011

brewでインストールする。
brew search opensslだと

	$ brew search openssl
	openssl
	homebrew/versions/openssl098

と、0.98が入りそうだったけれど、brew editでフォーミュラを確認すると最新の1.0.1cだったのでbrew install opensslそのままインストールした。

brewでインストールしたライブラリを認識させるために~/.bash_profileに以下を追加して再読み込み。

export LDFLAGS="-L/usr/local/opt/sqlite/lib -L/usr/local/opt/libxml2/lib -L/usr/local/opt/openssl/lib"
export CPPFLAGS="-I/usr/local/opt/sqlite/include -I/usr/local/opt/libxml2/include -I/usr/local/opt/openssl/include"
export OPENSSL_CFLAGS=/usr/local/opt/openssl/include
export OPENSSL_LIBS=/usr/local/opt/openssl/lib
export CFLAGS=`xml2-config --cflags`
export LIBS=`xml2-config --libs`

再度configureしてbrewのライブラリを認識していることを確認。

		configure: summary of build options:

	    Version:        0.3.8-DEV shared 5:0:0
	    Host type:      x86_64-apple-darwin12.2.0
	    Install prefix: /usr/local
	    C compiler:     gcc
	    CFLAGS:         -I/usr/include/libxml2 -I/usr/local/Cellar/zlib/1.2.7/include
	    LDFLAGS:        -L/usr/local/opt/sqlite/lib -L/usr/local/opt/libxml2/lib -L/usr/local/opt/openssl/lib
	    LIBS:           -L/usr/local/Cellar/zlib/1.2.7/lib -lz   -lxml2
	    CPPFLAGS:       -I/usr/local/opt/sqlite/include -I/usr/local/opt/libxml2/include -I/usr/local/opt/openssl/include
	    C preprocessor: gcc -E
	    C++ compiler:   g++
	    CXXFLAGS:       -g -O2
	    CXXCPP:         g++ -E
	    Library types:  Shared=yes, Static=yes
	    CUnit:          no
	    OpenSSL:        yes
	    Libxml2:        yes
	    Libevent(SSL):  yes
	    Src:            yes
	    Examples:       yes

## ライブラリはmake installできるが、CLI関係がmake すると ld でエラー

	spdylay_ssl.cc: In function ‘void spdylay::print_timer()’:
	spdylay_ssl.cc:519: warning: format ‘%03ld’ expects type ‘long int’, but argument 4 has type ‘int’
	spdylay_ssl.cc:519: warning: format ‘%03ld’ expects type ‘long int’, but argument 4 has type ‘int’
	i686-apple-darwin11-llvm-g++-4.2: /usr/local/opt/openssl/include: linker input file unused because linking not done
	mv -f .deps/spdylay_ssl.Tpo .deps/spdylay_ssl.Po
	(snip)
	libtool: link: g++ -g -O2 /usr/local/opt/openssl/lib -pthread -o .libs/spdycat util.o spdylay_ssl.o spdycat.o HtmlParser.o http_parser.o -Wl,-bind_at_load  -L/usr/local/Cellar/libevent/2.0.21/lib -levent_openssl -levent -L/usr/local/opt/sqlite/lib -L/usr/local/opt/libxml2/lib -L/usr/local/opt/openssl/lib ../lib/.libs/libspdylay.dylib -L/usr/local/Cellar/zlib/1.2.7/lib -lz -lxml2 -pthread
	ld: can't map file, errno=22 for architecture x86_64
	collect2: ld returned 1 exit status
	make[2]: *** [spdycat] Error 1
	make[1]: *** [all-recursive] Error 1
	make: *** [all] Error 2

いろいろググると、CFLAGSが間違っていそうな感じ？だが、CFLAGSを変更してもだめっぽい。うーん、ここで心折れた。
