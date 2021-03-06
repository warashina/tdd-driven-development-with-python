* ユーザー入力の保存
たくさんのモデルを作成して、これを違ったURLで追加していく方法もあるが今回はそうしない。
ここでは単純なPOSTリクエストを使う。これにはDjangoの簡潔である利点を学べる利点がある。

  * 私たちのフォームをPOSTリクエスト送信ができるようにする

    * POSTに失敗する。失敗の原因を特定するためfunctional_test.pyにtime.sleep()を追加する。これによって画面が一瞬で閉じてしまうのを防ぐ。
    * csrfトークンをテンプレートに追加するとエラーは出なくなる。
    * 不要になればtime.sleep()は除去する。

  * サーバーでのPOSTリクエストの処理

    * POSTリクエストに対応した新しいテストメソッドをtest.pyに追加する。
    * requestオブジェクトの属性に.methodや.POSTを追加することでPOSTリクエストが作れる。
    * テストメソッドのなかで、セットアップ、実行、アサートを空行で分けるとよいだろう。
    * response = home_page(request)のみが実行部となる。
    * views.pyにはまず愚直にテストに通る戻りつを追加する。

  * Pythonの変数をテンプレートの中でレンダリングされるよう渡す

    * {{ ... }}の表記でPythonオブジェクトをテンプレートに文字列として表示する。
    * ユニットテストではrender_to_string関数をつかうことでテンプレートに手動で変数をセットできる。こうやって作ったhtmlとviewが返すhtmlを比較できる。(P.56)
    * render_to_string()関数は第1引数にテンプレート、第2引数に変数名と変数のマッピングをとる。
    * views.pyのrequest.POSTにdict.getでパラメーターを与える。
    * functional_test.pyで表の中をアサーションする。混乱しないよう簡潔にかけるとよい。
  リストの項目番号となる数字を文字列として確認している。最初はこれでよい。

  * スリーストライクでリファクタリングする

    * テストコードに重複していることろがある、マントラとして「スリーストライクでリファクタリングする」を適用する。１回はコピーしていいが、３回繰り返しになったら重複をとりのぞくのである。
    * リファクタリングの前にはコミットを行う。
    * ヘルパーメソッドを使う。test_で始まるメソッドだけがテストとして実行される。自分の目的に合わせて他のメソッドを使える。
    * 著者はtearDownと最初のテストの間にヘルパーメソッドを置くのが好みである。
    * リファクタリング後もFTが同じように動作することを確認してコミット。

  * DjangoのORMと私たちの最初のモデル

    * Object-Relational Mapperは、データベースのテーブル、行と列にデータを格納するための抽象化レイヤーである。これらは私たちに使い慣れたオブジェクト指向のメタファーをデータベースで使えるようにしてくれます。クラスはデータベースのテーブルに、属性は列に、そして個々のインスタンスは行にあたるというわけです。
    * Djangoは素晴らしいORMとともにあり、これを使用するユニットテストを書くことはORMを学ぶのに最適な方法です。ORMを私たちが望むように使用することに特化した演習をコーディングするのだから。
    * tests.pyに新しいクラスを追加します。
    * テストコードをみると、新しいオブジェクトを作るようにデータベースに新しいレコードが作れることがわかる。属性をアサインして.save()関数を呼べば良い。
    * Djangoはクラス属性として.objectsというAPIをクエリのために提供している。もっともシンプルなクエリとして.all()を使用する。テーブルのすべてのレコードを取得する。
    * これはQuerySetというリストのようなオブジェクトを返す。ここから個別のオブジェクトを取り出すことができるし、.count()のような関数を呼ぶこともできる。
    * ここで、私たちは正しい情報が格納されているかを確認する。
    * DjangoのORMには役立つ機能が多くある。すばらしいイントロとなるDjango tutorialをさらってみるのにはいいタイミングである。
    * このテストはもっと短く描ける。Chapter 11で説明する。
    * 牧師たちは「本物」のユニットテストはデータベースに触るべきでないといういうでしょう。これらはインテグレーテッドテストと呼ぶべきと。
    * 今はアプリをテストするハイレベルのfunctional testとプログラマー視点のローレベルなunit testのみ区別します。続きはChapter 19で。
    * test.py実行。Imteのインポートエラー。Item = None のステップはスキップして、クラスをmodels.pyに作りましょう。
    * エラー、Itemクラスに.save属性がないので、これを与えるために本物のDjangoのmodelにしてあげましょう。Modelクラスを継承します

  * 私たちの最初のデータベースマイグレーション

    * 次に起こるのはデータベースエラーです。
    * Djangoでは、ORMの仕事はデータベースを形作ることです。データベースを構築する実際の作業はマイグレーションと呼ばれます。この作業はあなたにテーブルや列の追加や削除をmodels.pyにしたがって変更することを提供します。
    * データベースのためのバージョンコントロールシステムのようなものです。後ほど、実際に動作しているサーバーのデータベースのアップデートなどに便利なことをお見せします。
    * 今は最初のデータベースを作成するためのマイグレーションを知らなければなりません。
    * makemigrationsコマンドを使います
    ```
    $ python3 manage.py makemigrations
    ```
    * migrationsファイルにmodels.pyで作成したものが反映されていることを確認できる。

  * テストは続くよどこまでも(The Test Gets Surprisingly Far)

    * テストを実行すると多くのエラーが出る。Itemに.text属性がないからである。
    * もしあなたがPythonに慣れていないのなら、.text属性を指定できることに驚くかもしれません。Javaではコンパイルエラーになりますが、Pythonでは大丈夫です。リラックスできます。
    * models.Modelを継承したクラスはデータベースにマップされます。デフォルトでは自動生成されるid属性があり、プライマリキーの列になります。しかし、他の列はあなたが自分で定義しなくてはなりません。これが文字列フィールドの定義方法です。
    * Djangoは多数のフィールド型をもっています。IntegerField, CharFiled, DateFieldなどなど。私はCharFieldよりTextFieldを選びます、長さの制約のためです。フィールド型についてもっとしりがければチュートリアルとドキュメントを見ましょう。

  * 新しいフィールドがあれば新しいマイグレーションがある

    * 再度テストを走らせるとエラーが表示される。列がないよというもの。マイグレーションをしていないからである。
    * デフォルト値が設定されていないのでmakemigrationsで警告がでる。2で中断して、models.pyで明示する
    * UTが通ることを確認する。(私はUTのfirst_saved_itemに.textを付け忘れてエラーを踏んだ)
    * git status # test.py, models.py 2つの追跡されていないmigrations
    * git diff # test.py, models.py
    * git add lists
    * git commit -m "Model for list Items and associated migration"

  * POSTをデータベースに保存する

    * 私たちのhome pageのテストをPOSTリクエスト対応のものに調節し、そして「新しいアイテムを出たベースに保存できるようviewがレスポンスしたらな」と言いましょう。既存のテストに3行加えるだけでできます。
    * このテストはいろいろやりすぎじゃないか？複雑なので分割すべきではないか、そいういうことは紙片にかきとめておきましょう。
      * POSTのテストが長すぎるのでは？
    * 紙片に書いたら一旦忘れt、心地よく作業を続けましょう。テストを実行します。
    * モデルがないエラーが出ます（カウントが0)。views.pyを調整(実装)しましょう。
    * 慎重にコーディングを進めているので、あなたはもしかしたら大きな問題にすでに気がついているかもしれません。home pageにリクエストがあるたびに空のitemが保存されてしまうのです。これはいま無視します。
    * 別にいつでも問題を無視しろというっているわけではないからね。
      * ブランクのitemをリクエストのたびに保存しない
      * 複数のアイテムをテーブルに表示する
      * 複数のリストをサポートする
    * 最初の1つめからとりかかりましょう。test.pyにtest_home_page_only_saves_items_when_nessesaryを追加。1 != 0で失敗するのでviews.pyを実装する。
    * .objects.create はItemを作成するショートハンドです。.save()を呼ぶ必要はありません。

  * POSTの後のリダイレクト

    * "Always redirect after a POST"というように、POSTのあとはリダイレクトをしよう。
    * 302のステータスコードを返すようにテストを変更、テストを走らせ200 != 302を確認、viewにリダイレクトを追加。importにもredirectの追加が必要。

  * ユニットテストのよりよい実践: 一つのテストは一つの物事をテストする

    * POSTとリダイレクトをテストするようにした代わりに、テストは短くなった（レスポンスをアサートしなくなった）。だが、もっとよくできる。
    * バグの追跡を容易にするためには、各テストは1つのことだけをテストするようにしたほうがよい。
    * ひとつのアサート分にまで煮つまりはしないが、今回はPOSTリクエストの確認とリダイレクトのテストを分離することで懸念を払拭しよう。

  * テンプレートの中にItemたちをレンダリングする

    * To-Doリストにもどる。2つ残っている
      * 複数のitemを表に表示する
      * 複数のlistをサポートする
    * 簡単な方からやろう。テンプレートに複数のitemを表示できるようにする。テストを書く、（予想通り)失敗する。
    * Djangoのテンプレート記法である{% for .. in .. %}を使用する。
    * Djangoのテンプレート記法の一つで、これでtrの行は複数生成される。他にもテンプレートの魔法を紹介したいが、Django docsを確認して欲しい。
    * まだテストは通らない、実際にitemをviewから渡してやる必要がある。
    * ユニットテストは通った。ファンクショナルテストを確認する。(runserverしていなくてミスした、runserverして再度)エラーが表示されるのでブラウザで手動で開いて見てみる。デバッグのために必要なメッセージが表示されている。

  * マイグレートによる我々のプロダクション・データベースの作成
    * ユニットテストでは何のエラーもないのに、データベースがないというような基本的なエラーが表示されている。
    * 実は、DjangoはユニットテストにおいてTestCase向けに特別なテスト用データベースを作成している。
    * 「本物の」データベースをセットアップするには、それを作成する必要があります。SQLite databaseはディスク上のファイル一つで、Djangoのsettings.pyにデフォルトで書かれているのを見つけられます。これがプロジェクトのディレクトリにdb.sqlite3というファイルを一つ置いてくれるという寸法です。
    ```
    $ python3 manage.py migrate
    ```
    * migrate実行後、ブラウザをリフレッシュするとエラーは表示されない。ファンクショナルテストを走らせよう
    * 惜しい！ナンバリングする項目が1のまま固定だった。テンプレートタグのforloop.counterを追加しよう
    * 今度は、(明示的に失敗するようファンクショナルテストの最後にあるアサート分まで)進むだろう
    * しかし問題がある、ファンクショナルテストを走らせるたびに、項目が繰り返し増えてしまう。やるたびに悪くなる
    * テスト実行後、自動でデータを削除する必要があるが、今のところは手動でデータベースを削除し、migrateコマンドで再び作成してリフレッシュすることにしょう。その後、FTを通ることを確認する。
    * git add lists
    * git commit -m "Redirect after POST, and show all items in template"
    * git tag end-of-chapter-05
    * やったこと
      * POSTで新しいitemを追加できるようformを設定した
      * リストitemをデータベースに保存できるシンプルなmodelを設定した
      * 少なくとも3つのFTデバッグ技術を使った
    * しかし、FTのあとにクリーンアップをすることと、リストを複数にするという課題が残ってていて、後者は重大である。ユーザー全員で同じリストを使うわけにはいかない。

  * TDDの便利なコンセプト

    * Regression
      動いていたコードを新しいコードがある見方から壊してしまうこと

    * Unexpected failure
      予期しないテスト失敗は、テストの間違いか、regressionの発生発見を助けてくれたり、コードの修正を助けてくれる。

    * Red/Green/Refactor
      TDDの別の説明方法。テストを書いて失敗(Red)、テストをパスするように書いて(Green)、よりより実装にするようRefactor。

    * Triangulation
      すでにあるテストケースに新しい新しい仕様を追加して、コードの現状にあわせること。ちょっとズルい

    * Three strikes and refactor
      コード重複を取り除くときのルール。

    * The scratchpad to-do list
      コーディング中に起きたことを書いておく場所。やっていることが終わったら戻ってくる。