#PKI

電子証明書関係の整理

非常にテクニカルタームが多いので混乱しがちなPKI周りを整理する。

公開鍵暗号方式と共通鍵暗号方式
	公開鍵暗号
		公開鍵、秘密鍵
	共通鍵暗号
	ハッシュや暗号方式の整理
		ブロック暗号とストリーム暗号
		DESやAESの種類
	電子証明書によってできること
		電子署名
		暗号化
ルート証明書の役割とSSLサーバ証明書の仕組み
	X.509? CRT? CRL? CSR? DER? PEM? PKCS7? PKCS#10?, PKCS12? pfx?ってなに？

	http://www.ipa.go.jp/security/pki/index.html
	http://www.ipa.go.jp/security/pki/033.html
	http://www.ipa.go.jp/security/rfc/RFC2459JA.html

	SSL証明書の発行方法
	CA証明書, 中間証明書, クロスルート証明書とはなにか？
OpenSSLをつかって作ってみる


暗号2010年問題の課題と解決策
SSL接続にまつわるトラブルの回避策
	携帯での問題
