# Ruby

## インストールしたgemがどこにあるか探す

rbenvなどでローカルに環境を作っていると、コマンドとしてインストールされるはずのファイルがパスがキレるところに置かれないこともしばしば。実際インストール先ディレクトリさえわからないことが多い。

で、そんなときは gem にきこう。

    $ gem which redcarpet
    /Users/too/.rbenv/versions/1.9.3-p194/lib/ruby/gems/1.9.1/gems/redcarpet-2.2.2/lib/redcarpet.rb


