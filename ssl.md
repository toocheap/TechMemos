# オレオレCAたてて、オレオレserver ssl証明書作って、 クライアント認証用のクライアント用証明書を作成するまで

## 事前準備

CA.shがあることを確認。

	/usr/lib/ssl/misc/CA.sh

作業ディレクトリを作成(~/Dropbox/apk/ssl/)

	cd ~/Dropbox/apk
	mkdir ssl
	cd ssl

## CA作成

	$ CA.sh -newca
	CA certificate filename (or enter to create)        # Enter を入力

	Making CA certificate ...
	Generating a 1024 bit RSA private key
	..++++++
	..++++++
	writing new private key to './demoCA/private/./cakey.pem'
	Enter PEM pass phrase:                              # パスフレーズを入力
	Verifying - Enter PEM pass phrase:                  # 上と同じ物を入力
	-----
	You are about to be asked to enter information that will be incorporated
	into your certificate request.
	What you are about to enter is what is called a Distinguished Name or a DN.
	There are quite a few fields but you can leave some blank
	For some fields there will be a default value,
	If you enter '.', the field will be left blank.
	-----
	Country Name (2 letter code) [AU]:
	State or Province Name (full name) [Some-State]:
	Locality Name (eg, city) []:
	Organization Name (eg, company) [Internet Widgits Pty Ltd]:
	Organizational Unit Name (eg, section) []:
	Common Name (eg, YOUR name) []:webos-goodies.jp     # サーバー名を入力
	Email Address []:

	Please enter the following 'extra' attributes
	to be sent with your certificate request
	A challenge password []:
	An optional company name []:
	Using configuration from /System/Library/OpenSSL/openssl.cnf
	Enter pass phrase for ./demoCA/private/./cakey.pem: # 再び同じパスフレーズを入力
	Check that the request matches the signature
	Signature ok

## サーバーSSL証明書作成

### 秘密鍵の作成

	$ openssl genrsa -aes128 -out server.key 1024

### 証明書の作成

まずはCSRを作成

	$ openssl req -new -days 365 -key server.key -out server.csr
	You are about to be asked to enter information that will be incorporated
	into your certificate request.
	What you are about to enter is what is called a Distinguished Name or a DN.
	There are quite a few fields but you can leave some blank
	For some fields there will be a default value,
	If you enter '.', the field will be left blank.
	-----
	Country Name (2 letter code) [AU]:JA
	State or Province Name (full name) [Some-State]:Saitama
	Locality Name (eg, city) []:Shiki
	Organization Name (eg, company) [Internet Widgits Pty Ltd]:toocheap.jp
	Organizational Unit Name (eg, section) []:ubuntu
	Common Name (e.g. server FQDN or YOUR name) []:ubuntu
	Email Address []:toocheap@gmail.com

	Please enter the following 'extra' attributes
	to be sent with your certificate request
	A challenge password []:
	An optional company name []:

CSRをCAに渡して、証明書を発行

	$ openssl ca -in server.csr -keyfile demoCA/private/cakey.pem -out server.crt
	Using configuration from /usr/lib/ssl/openssl.cnf
	Enter pass phrase for demoCA/private/cakey.pem:
	Check that the request matches the signature
	Signature ok
	Certificate Details:
	        Serial Number:
	            d9:6f:e1:3a:33:3c:46:29
	        Validity
	            Not Before: Jan 25 16:47:15 2013 GMT
	            Not After : Jan 25 16:47:15 2014 GMT
	        Subject:
	            countryName               = JA
	            stateOrProvinceName       = Saitama
	            organizationName          = toocheap.jp
	            organizationalUnitName    = ubuntu
	            commonName                = ubuntu
	            emailAddress              = toocheap@gmail.com
	        X509v3 extensions:
	            X509v3 Basic Constraints:
	                CA:FALSE
	            Netscape Comment:
	                OpenSSL Generated Certificate
	            X509v3 Subject Key Identifier:
	                96:78:2D:C8:75:2F:3C:AC:07:84:64:78:2E:7B:16:57:4A:E5:DD:6E
	            X509v3 Authority Key Identifier:
	                keyid:7A:95:56:09:97:48:46:84:5C:60:4B:8F:02:E2:D7:82:43:64:CB:C7

	Certificate is to be certified until Jan 25 16:47:15 2014 GMT (365 days)
	Sign the certificate? [y/n]:y


	1 out of 1 certificate requests certified, commit? [y/n]y
	Write out database with 1 new entries
	Data Base Updated

こんなかんじでできる。

	$ more server.crt
	Certificate:
	    Data:
	        Version: 3 (0x2)
	        Serial Number:
	            d9:6f:e1:3a:33:3c:46:29
	    Signature Algorithm: sha1WithRSAEncryption
	        Issuer: C=JA, ST=Saitama, O=toocheap.jp, CN=ubuntu
	        Validity
	            Not Before: Jan 25 16:47:15 2013 GMT
	            Not After : Jan 25 16:47:15 2014 GMT
	        Subject: C=JA, ST=Saitama, O=toocheap.jp, OU=ubuntu, CN=ubuntu/emailAddress=toocheap@gmail.com
	        Subject Public Key Info:
	            Public Key Algorithm: rsaEncryption
	                Public-Key: (1024 bit)
	                Modulus:
	                    00:c7:b3:dd:a2:e9:68:68:d9:33:2d:70:4d:b1:45:
	                    51:1a:8a:06:8a:4f:84:bf:e7:76:96:4c:53:49:98:
	                    e9:9f:36:12:4f:97:28:c7:2c:ff:8d:a3:ab:e4:df:
	                    9b:13:21:0d:e9:89:40:f7:07:57:96:a3:34:6c:e0:
	                    6e:7d:32:6c:51:a5:d0:40:e1:08:f5:f3:89:fa:80:
	                    2a:81:fa:07:64:45:ae:4d:de:d8:13:e5:de:64:52:
	                    e7:52:cb:1c:45:46:ff:b9:04:2c:d1:83:f0:17:47:
	                    65:0d:ea:19:d0:9a:9c:16:03:90:52:9b:1e:83:bc:
	                    c1:52:00:a4:7b:db:35:f7:25
	                Exponent: 65537 (0x10001)
	        X509v3 extensions:
	            X509v3 Basic Constraints:
	                CA:FALSE
	            Netscape Comment:
	                OpenSSL Generated Certificate
	            X509v3 Subject Key Identifier:
	                96:78:2D:C8:75:2F:3C:AC:07:84:64:78:2E:7B:16:57:4A:E5:DD:6E
	            X509v3 Authority Key Identifier:
	                keyid:7A:95:56:09:97:48:46:84:5C:60:4B:8F:02:E2:D7:82:43:64:CB:C7

	    Signature Algorithm: sha1WithRSAEncryption
	         a1:46:9d:ce:e8:ba:8d:7e:1e:95:8e:7e:60:5a:c7:aa:ea:94:
	         db:30:58:32:ce:ec:17:85:5e:65:71:33:5a:27:a0:6b:8e:67:
	         99:4d:e1:68:eb:96:a9:54:28:b4:2c:51:f8:3b:4c:22:ef:f3:
	         bd:bb:79:de:90:84:f0:5c:da:29:60:a4:f3:50:76:54:c6:d4:
	         de:38:69:25:67:d4:24:b5:2f:1b:ba:e0:55:85:42:da:35:26:
	         46:ee:14:0e:ad:8e:15:b1:00:f9:14:ce:a2:58:dd:a5:a0:ac:
	         df:f2:27:a3:8a:7e:4b:7a:6b:c3:ef:29:37:36:7c:f6:5c:fb:
	         b9:82:86:78:8a:89:ec:a3:9c:31:45:fe:2d:8e:58:38:5b:46:
	         00:cc:71:aa:5a:1f:78:ca:91:2e:d3:5d:b3:ae:e4:60:4a:01:
	         54:1e:7c:c0:fc:45:0e:d8:fc:42:04:bd:fd:27:b8:02:13:e5:
	         9c:b5:fc:9b:5e:03:ac:f5:12:bd:d5:82:ae:b3:68:c8:ed:17:
	         dd:b3:b5:77:eb:c4:01:20:7a:95:08:7e:a7:c0:96:63:a2:80:
	         d7:ab:93:ec:63:93:12:01:51:1a:23:38:88:b4:b7:c7:b7:cd:
	         18:c9:77:ed:e3:00:da:8f:a3:e7:a1:4b:97:15:e7:35:45:e5:
	         5a:5e:d0:0a
	-----BEGIN CERTIFICATE-----
	MIIDOjCCAiKgAwIBAgIJANlv4TozPEYpMA0GCSqGSIb3DQEBBQUAMEYxCzAJBgNV
	BAYTAkpBMRAwDgYDVQQIDAdTYWl0YW1hMRQwEgYDVQQKDAt0b29jaGVhcC5qcDEP
	MA0GA1UEAwwGdWJ1bnR1MB4XDTEzMDEyNTE2NDcxNVoXDTE0MDEyNTE2NDcxNVow
	ejELMAkGA1UEBhMCSkExEDAOBgNVBAgMB1NhaXRhbWExFDASBgNVBAoMC3Rvb2No
	ZWFwLmpwMQ8wDQYDVQQLDAZ1YnVudHUxDzANBgNVBAMMBnVidW50dTEhMB8GCSqG
	SIb3DQEJARYSdG9vY2hlYXBAZ21haWwuY29tMIGfMA0GCSqGSIb3DQEBAQUAA4GN
	ADCBiQKBgQDHs92i6Who2TMtcE2xRVEaigaKT4S/53aWTFNJmOmfNhJPlyjHLP+N
	o6vk35sTIQ3piUD3B1eWozRs4G59MmxRpdBA4Qj184n6gCqB+gdkRa5N3tgT5d5k
	UudSyxxFRv+5BCzRg/AXR2UN6hnQmpwWA5BSmx6DvMFSAKR72zX3JQIDAQABo3sw
	eTAJBgNVHRMEAjAAMCwGCWCGSAGG+EIBDQQfFh1PcGVuU1NMIEdlbmVyYXRlZCBD
	ZXJ0aWZpY2F0ZTAdBgNVHQ4EFgQUlngtyHUvPKwHhGR4LnsWV0rl3W4wHwYDVR0j
	BBgwFoAUepVWCZdIRoRcYEuPAuLXgkNky8cwDQYJKoZIhvcNAQEFBQADggEBAKFG
	nc7ouo1+HpWOfmBax6rqlNswWDLO7BeFXmVxM1onoGuOZ5lN4WjrlqlUKLQsUfg7
	TCLv8727ed6QhPBc2ilgpPNQdlTG1N44aSVn1CS1Lxu64FWFQto1JkbuFA6tjhWx
	APkUzqJY3aWgrN/yJ6OKfkt6a8PvKTc2fPZc+7mChniKieyjnDFF/i2OWDhbRgDM
	capaH3jKkS7TXbOu5GBKAVQefMD8RQ7Y/EIEvf0nuAIT5Zy1/JteA6z1Er3Vgq6z
	aMjtF92ztXfrxAEgepUIfqfAlmOigNerk+xjkxIBURojOIi0t8e3zRjJd+3jANqP
	o+ehS5cV5zVF5Vpe0Ao=
	-----END CERTIFICATE-----
	too@ubuntu:~/Dropbox/apk/ssl$ more server.key
	-----BEGIN RSA PRIVATE KEY-----
	MIICXQIBAAKBgQDHs92i6Who2TMtcE2xRVEaigaKT4S/53aWTFNJmOmfNhJPlyjH
	LP+No6vk35sTIQ3piUD3B1eWozRs4G59MmxRpdBA4Qj184n6gCqB+gdkRa5N3tgT
	5d5kUudSyxxFRv+5BCzRg/AXR2UN6hnQmpwWA5BSmx6DvMFSAKR72zX3JQIDAQAB
	AoGBAICt2HGh/qIY2o474AQLC0CTkbVLmdliFxqvobc5rcfmOpRIbYEx8JVe0mNO
	5gjEcsd5pn/Gnly1WxGQ6AEKHZq1JeSh/SW7pGMJK/STOM1w8/JDxIjJMz1AmV1i
	gR4NKtn3GTTdS0eW8aAzeg07Li7ZQ9OeN4+uiBbuju8DJu2hAkEA/8+MQXPucmJ1
	4Tyvy013GPNopShVeHjymULhYvyIqodHFp4yVUBS5z5ybf3tWuxpdFSWuKxsk+h2
	jTL2/dVXrQJBAMfZsM+brNb52oM06GGUZDaY0YyZp+0VezUYUl6AD78MvlLr/3Pw
	q2dyyK2CFfUhhxxVKNDQ1LYdlmSGdE7z7FkCQG7kkA6HrSRU3nkHj8V4DVr5mbGO
	7I5PEAu0XHRGuRADmKOLbJJcUhQAlCZuX4h817IgQT5JMMBlk47eOwgKhfkCQB08
	5/U3nU3GOAXCE81S3GZwbRfY0wyIfAIEkOhqa+NThfSzuifIKgt0a4+W3IeEZDPs
	8Y+7PaN3KK2ETjfOz+ECQQDlsqPzmKUQ1CPTwixPWxs9ic/gwknQ8oi0onCV/Afs
	0dJyCEBvy8m/deoogKV+kvIyc5SO6GgmnsiqsJ9MIT2v
	-----END RSA PRIVATE KEY-----

## クライントの証明書発行

### 事前に作業ディレクトリを作成

	mkdir clients
	cd clients

#クライアントの秘密鍵を作成

	$ openssl genrsa -aes128 -out toocheap.key 1024
	Generating RSA private key, 1024 bit long modulus
	.......................++++++
	..................++++++
	e is 65537 (0x10001)
	Enter pass phrase for toocheap.key:
	Verifying - Enter pass phrase for toocheap.key:

	CSRを秘密鍵から作成

	$ openssl req -new -days 365 -key toocheap.key -out toocheap.crt
	Enter pass phrase for toocheap.key:
	You are about to be asked to enter information that will be incorporated
	into your certificate request.
	What you are about to enter is what is called a Distinguished Name or a DN.
	There are quite a few fields but you can leave some blank
	For some fields there will be a default value,
	If you enter '.', the field will be left blank.
	-----
	Country Name (2 letter code) [AU]:JA
	State or Province Name (full name) [Some-State]:Saitama
	Locality Name (eg, city) []:Shiki
	Organization Name (eg, company) [Internet Widgits Pty Ltd]:toocheap.jp
	Organizational Unit Name (eg, section) []:
	Common Name (e.g. server FQDN or YOUR name) []:toocheap
	Email Address []:

	Please enter the following 'extra' attributes
	to be sent with your certificate request
	A challenge password []:
	An optional company name []:

クライアント証明書発酵用にopenssl.cnfを修正

	$ cp /usr/lib/ssl/openssl.cnf .
	$ mv openssl.cnf openssl-client.cnf
	$ vim openssl-client.cnf

作業ディレクトリを掘ったのでCAの場所を再指定

	[ CA_default ]

	dir             = ../demoCA             # Where everything is kept


以下のコメントアウトを外す

	[usr_cert]
	nsCertType = client,email

CSRから証明書を作成

	$ openssl ca -config openssl-client.cnf -in toocheap.csr -out toocheap.crt
	Using configuration from openssl-client.cnf
	Enter pass phrase for ../demoCA/private/cakey.pem:
	Check that the request matches the signature
	Signature ok
	Certificate Details:
	        Serial Number:
	            d9:6f:e1:3a:33:3c:46:2a
	        Validity
	            Not Before: Jan 25 17:22:34 2013 GMT
	            Not After : Jan 25 17:22:34 2014 GMT
	        Subject:
	            countryName               = JA
	            stateOrProvinceName       = Saitama
	            organizationName          = toocheap.jp
	            commonName                = toocheap
	        X509v3 extensions:
	            X509v3 Basic Constraints:
	                CA:FALSE
	            Netscape Cert Type:
	                SSL Client, S/MIME
	            Netscape Comment:
	                OpenSSL Generated Certificate
	            X509v3 Subject Key Identifier:
	                BB:9C:C1:38:BC:E1:D3:E8:17:DA:37:60:E2:48:FB:AA:75:78:95:83
	            X509v3 Authority Key Identifier:
	                keyid:7A:95:56:09:97:48:46:84:5C:60:4B:8F:02:E2:D7:82:43:64:CB:C7

	Certificate is to be certified until Jan 25 17:22:34 2014 GMT (365 days)
	Sign the certificate? [y/n]:y


	1 out of 1 certificate requests certified, commit? [y/n]y
	Write out database with 1 new entries
	Data Base Updated

# こんな感じで終わる。

	$ ls
	openssl-client.cnf  toocheap.crt  toocheap.csr  toocheap.key

証明書

	$ cat toocheap.crt
	Certificate:
	    Data:
	        Version: 3 (0x2)
	        Serial Number:
	            d9:6f:e1:3a:33:3c:46:2a
	    Signature Algorithm: sha1WithRSAEncryption
	        Issuer: C=JA, ST=Saitama, O=toocheap.jp, CN=ubuntu
	        Validity
	            Not Before: Jan 25 17:22:34 2013 GMT
	            Not After : Jan 25 17:22:34 2014 GMT
	        Subject: C=JA, ST=Saitama, O=toocheap.jp, CN=toocheap
	        Subject Public Key Info:
	            Public Key Algorithm: rsaEncryption
	                Public-Key: (1024 bit)
	                Modulus:
	                    00:a1:ec:c7:72:56:c1:24:92:34:06:6a:17:9a:d1:
	                    59:ae:cf:48:52:05:83:9a:fb:ae:3e:bb:2a:43:ed:
	                    b3:23:8b:19:16:ea:ec:44:ae:fc:86:6b:d0:ef:0a:
	                    92:72:7a:72:e0:1a:30:b9:fd:b1:eb:c4:63:cc:bd:
	                    dd:3b:24:b5:68:f2:ca:93:e4:e1:c6:01:04:cb:49:
	                    24:d9:3c:e7:19:1f:ea:87:34:98:87:12:e6:63:47:
	                    dc:77:54:dc:7c:e7:b4:3f:8f:57:88:ec:92:ff:2c:
	                    d1:40:7c:2d:25:5f:2c:be:b4:73:d2:f6:84:9f:eb:
	                    34:10:5a:68:d8:7b:ae:ac:e3
	                Exponent: 65537 (0x10001)
	        X509v3 extensions:
	            X509v3 Basic Constraints:
	                CA:FALSE
	            Netscape Cert Type:
	                SSL Client, S/MIME
	            Netscape Comment:
	                OpenSSL Generated Certificate
	            X509v3 Subject Key Identifier:
	                BB:9C:C1:38:BC:E1:D3:E8:17:DA:37:60:E2:48:FB:AA:75:78:95:83
	            X509v3 Authority Key Identifier:
	                keyid:7A:95:56:09:97:48:46:84:5C:60:4B:8F:02:E2:D7:82:43:64:CB:C7

	    Signature Algorithm: sha1WithRSAEncryption
	         37:0a:02:83:c4:09:1c:f9:26:ba:36:a3:3a:2e:80:bb:14:53:
	         89:75:e5:ec:4e:1c:61:8a:36:72:bc:d8:7d:0e:e0:3d:24:4c:
	         6f:e0:58:3b:7e:c5:d0:bd:70:2b:ee:ef:d2:cd:4c:f4:9d:85:
	         af:72:5a:c8:41:12:ce:b1:9e:d5:b9:42:2d:cd:d3:df:d6:43:
	         cb:d8:77:27:99:a2:4f:56:69:d7:48:6b:8b:0e:c9:ce:86:12:
	         ff:62:1b:66:29:d5:8b:8e:6b:d0:bf:0f:d1:0a:3b:0a:bb:d5:
	         89:fc:e3:27:61:81:8d:b7:1e:3a:f9:53:88:45:be:f4:72:26:
	         fc:d1:f1:1a:c3:78:2f:ca:ed:86:57:d4:b1:47:5b:7a:60:6f:
	         bd:94:1d:3a:31:c6:20:c3:c8:bd:77:5f:12:1d:8e:93:33:30:
	         98:1d:6c:08:ba:74:78:6a:e0:e3:37:ac:95:6a:3d:99:bd:91:
	         9d:81:4b:eb:17:6d:6f:8e:62:ac:2b:50:19:59:71:b0:fa:4e:
	         83:4c:13:bb:27:91:84:9a:78:ad:19:73:94:78:45:e0:eb:9c:
	         ce:49:85:38:55:28:e3:db:b2:93:5d:54:02:9e:10:45:a2:62:
	         f2:f2:a3:6e:76:d4:f9:6a:d5:19:be:40:c8:f9:ab:94:9d:e9:
	         20:33:1a:86
	-----BEGIN CERTIFICATE-----
	MIIDHTCCAgWgAwIBAgIJANlv4TozPEYqMA0GCSqGSIb3DQEBBQUAMEYxCzAJBgNV
	BAYTAkpBMRAwDgYDVQQIDAdTYWl0YW1hMRQwEgYDVQQKDAt0b29jaGVhcC5qcDEP
	MA0GA1UEAwwGdWJ1bnR1MB4XDTEzMDEyNTE3MjIzNFoXDTE0MDEyNTE3MjIzNFow
	SDELMAkGA1UEBhMCSkExEDAOBgNVBAgMB1NhaXRhbWExFDASBgNVBAoMC3Rvb2No
	ZWFwLmpwMREwDwYDVQQDDAh0b29jaGVhcDCBnzANBgkqhkiG9w0BAQEFAAOBjQAw
	gYkCgYEAoezHclbBJJI0BmoXmtFZrs9IUgWDmvuuPrsqQ+2zI4sZFursRK78hmvQ
	7wqScnpy4Bowuf2x68RjzL3dOyS1aPLKk+ThxgEEy0kk2TznGR/qhzSYhxLmY0fc
	d1TcfOe0P49XiOyS/yzRQHwtJV8svrRz0vaEn+s0EFpo2HuurOMCAwEAAaOBjzCB
	jDAJBgNVHRMEAjAAMBEGCWCGSAGG+EIBAQQEAwIFoDAsBglghkgBhvhCAQ0EHxYd
	T3BlblNTTCBHZW5lcmF0ZWQgQ2VydGlmaWNhdGUwHQYDVR0OBBYEFLucwTi84dPo
	F9o3YOJI+6p1eJWDMB8GA1UdIwQYMBaAFHqVVgmXSEaEXGBLjwLi14JDZMvHMA0G
	CSqGSIb3DQEBBQUAA4IBAQA3CgKDxAkc+Sa6NqM6LoC7FFOJdeXsThxhijZyvNh9
	DuA9JExv4Fg7fsXQvXAr7u/SzUz0nYWvclrIQRLOsZ7VuUItzdPf1kPL2HcnmaJP
	VmnXSGuLDsnOhhL/YhtmKdWLjmvQvw/RCjsKu9WJ/OMnYYGNtx46+VOIRb70cib8
	0fEaw3gvyu2GV9SxR1t6YG+9lB06McYgw8i9d18SHY6TMzCYHWwIunR4auDjN6yV
	aj2ZvZGdgUvrF21vjmKsK1AZWXGw+k6DTBO7J5GEmnitGXOUeEXg65zOSYU4VSjj
	27KTXVQCnhBFomLy8qNudtT5atUZvkDI+auUnekgMxqG
	-----END CERTIFICATE-----

秘密鍵

	$ cat toocheap.key
	-----BEGIN RSA PRIVATE KEY-----
	Proc-Type: 4,ENCRYPTED
	DEK-Info: AES-128-CBC,FA350BD5C92C25CB18F263023CB9F7D8

	ERxzxfCllftDfOPu5NNVvPixgoYXZaQ5NtRbandpuWBB0Jk3uA8eVP+fsylFlqCs
	F6FV4VlynMrlXIdicHYFT/O6X8xLLgwP7KKFNuq0roV4VWt3rmZnDNM8nJ6aHVax
	mXvgUhm9NbOLYR7LXNWe686nGqmIIuwV6MSCEVpBQ1ISFwFB09Nm6Z44ARX/mfXW
	lDl0bL32n2zLIy0mrhSmwwSeVIJxsgM8/E8Ma2ZebgmqidCUDpWQpcvTd1tMvfE9
	oW1Co6YH1XmLgZIYhG0OEa4AqI4nMvq8byli2R880nAQGqcpT5Pdx4IYA5iSickz
	TUNBJZfMX5V6ajrllfqmjj+3G5lsQfteyEGm4+JQrh48U5FySz6Y3dS6fP375tgi
	qvugpduyt36fnllHQ8CAUqqKHGtAzi7Z+byQeOZrOgIeSJiAmKe5PyfoUAFYSDYA
	lq6GvSCIlSVzexmy33VIEtknpyLYxe0j3cF8KOKe4jekAb18YdObcX3Ol/6Yh9hs
	AHLQ34YsHYIxUYSnmY7haZAjh0cVoePuZASa3OHrzAsGm8ZUPYUArpSo5DxSaxlA
	EeeLePTq8QsL0krmUaFrMTdXDGGj9OlgAeVmq+EUpkSpu4PDZ2x7mgBqR6YYnSiG
	yFWpCTj+bJ6bMTdvjEoMD59MZEDvVPXIsNqcOXvuPW5g41Rczh0eg6f4QIQXdzCo
	uuyNB+G0vhUWBIJeuadN+iRuj0Lhhrm1qTE3JekuXRDbkMbgP+ODp/FfMhdEU5dS
	wC8VhdGHvi5dTmcBkmDvfXyE/czp6qf5KjmuwkD+KMp72/I6S0b8ZcjoLOdn0ao4
	-----END RSA PRIVATE KEY-----

### PKCS#12形式に変換

	$ openssl pkcs12 -export -in toocheap.crt -inkey toocheap.key -certfile ../demoCA/cacert.pem -out toocheap.p12
	Enter pass phrase for toocheap.key:
	Enter Export Password:
	Verifying - Enter Export Password:

### CAの中身

$ find ../demoCA/
../demoCA/
../demoCA/cacert.pem
../demoCA/newcerts
../demoCA/newcerts/D96FE13A333C462A.pem
../demoCA/newcerts/D96FE13A333C4629.pem
../demoCA/newcerts/D96FE13A333C4628.pem
../demoCA/crl
../demoCA/private
../demoCA/private/cakey.pem
../demoCA/certs
../demoCA/serial.old
../demoCA/index.txt.attr
../demoCA/serial
../demoCA/index.txt.attr.old
../demoCA/index.txt.old
../demoCA/index.txt
../demoCA/careq.pem

index.txtには発行した証明書がリストされている。

$ more ../demoCA/index.txt
V	160125164240Z		D96FE13A333C4628	unknown	/C=JA/ST=Saitama/O=toocheap.jp/CN=ubuntu
V	140125164715Z		D96FE13A333C4629	unknown	/C=JA/ST=Saitama/O=toocheap.jp/OU=ubuntu/CN=ub
untu/emailAddress=toocheap@gmail.com
V	140125172234Z		D96FE13A333C462A	unknown	/C=JA/ST=Saitama/O=toocheap.jp/CN=toocheap

\newcert以下には発行した証明書がそのまま入っている(MD5ハッシュが同じ

$ md5sum toocheap.crt
e947e3fe752ff645a4c551a518eb9b3a  toocheap.crt
$ md5sum ../demoCA/newcerts/D96FE13A333C462A.pem
e947e3fe752ff645a4c551a518eb9b3a  ../demoCA/newcerts/D96FE13A333C462A.pem

serialには次のシリアル値が。

$ cat ../demoCA/serial
D96FE13A333C462B

