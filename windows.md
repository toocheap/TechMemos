= Windows いろいろメモ

== 自動実行をOFFにする

1. gpedit.mscを開く
1. [コンピューターの構成]、[管理用テンプレート]、[Windows コンポーネント] [自動再生のポリシー]
1. [自動実行の既定の動作] →有効
1. [既定の自動実行の動作] ボックスの [自動実行コマンドを実行しない] を選択し、すべてのドライブを指定

http://support.microsoft.com/kb/967715/ja

== WindowsServer 2008R2でADを立てる

1. IP設定を変更。既存のDNSサーバのIPアドレスをセカンダリDNSに指定。プライマリDNSを127.0.0.1に指定
1. SeverManagerを起動
1. 役割→AD ディレクトリサービスを追加
1. dcpromo.exeを起動
1. 新規フォレストの作成を選択
1. フォレストのFQDNとしてsymmobile.localなどを指定
1. ADのバージョン指定ではWindows2008R2を選択
1. 追加サービスでDNSサーバを選択



