# aliasの登録と読み込み
gitを扱う上でaddしてcommitしてpushするってのが基本的な一連の流れですよね。
毎回`git add ~` `git commit -m "~"` `git push ~`てのは面倒でして。
aliasを使うことにしました。

## aliasとは
**別名**を意味する言葉でITの分野では**別の名前で参照するためのシンボル**といった意味で使われることが多い

aliasは`.bashrc`に記述します。
`alias ga='git add'`
などと記述すると`ga`が`git add`として使えます，便利！

`.bashrc`への記述後は`.bash_profile`に`.bashrc`を読み込む記述をしましょう。
`.bash_profile`に`source ~/.bashrc`の記述をするだけでOKです。

ターミナルの再起動か，`source ~/.bash_profile`を打ち込めばaliasが反映されます。
