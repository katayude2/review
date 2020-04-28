# CodeReview
- 2020年4月28日(火)10時30分〜12時
- Reviewer(SG)：株式会社ソニックガーデン 西見さん・川村さん
- Reviewee(DIC)：2020年1月期 松浦さん

## Q&A
1. パフォーマンスの改善
- 本番環境のUserインデックス、Postsインデックスなどのページの読み込みが遅い(10秒ほど)時があります。どのように読み込み速度を改善するべきでしょうか？ (DIC)
  - フロント(画面の表示速度)＞　Chrome Developer Console　＞　Performance (SG)
  - ネットワーク ＞　Chrome Developer Console　＞　Network　＞　サーバの応答速度、ネットワーク待ち　＞　サーバのロケーションが遠い、ネットワークが弱い (SG)
  - サーバ　＞　アセット配信(画像、CSS、JSなど)　＞　どこから配信するか？　＞　Railsサーバ(サーバ負荷の影響を受ける)、S3のようなストレージサーバ(サーバ負荷の影響を受けない)、CDN(一番早い・サーバ負荷の影響を受けない・ロケーションとして一番近い場所に配置されるためネットワークが速い) (SG)
  - サーバ　＞　ビューのレンダリング　＞　Railsログ(View/DB)、NewRelic・Skylightのような計測ツール (SG)
  - サーバ　＞　DBへのアクセス　＞　インデックスが活用されているか？(クエリをpsqlにコピペして、実行計画を取得する)、N+1が発生していないか？　＞　クエリの回数を減らすことがパフォーマンスチューニングのポイント(クエリの回数分SQLが実行され、通信が行われる・クエリ実行数が多すぎるとコネクションプールを圧迫する)　＞　bullet gemを活用すると分析しやすい (SG)

2. ユーザー情報(User Model)のバリデーションの設計について
- DB、モデルでemail、password以外の項目にnot null制約やpresenceのバリデーションをかけるとその他の項目を入力せずにemail、passwordを変更する場合にバリデーションエラーが発生してしまうので、今回はDB,モデルでのバリデーションはかけずにビューでのバリデーションをかけました。実務的にはどのように設計するのが良いのでしょうか。 (DIC)
  - validation context　＞　contextを指定することで、context毎にバリデーションを切り替えられる (SG)
  - Formオブジェクト　＞　登録するフォーム毎にクラスを用意する (SG)
  - https://speakerdeck.com/hotatekaoru/fat-modelnidui-chu-suru-6tufalserihuakutaringupatan (SG)
  - テーブルの切り分け　＞　DBの制約が最強、users、user_basic_informations、user_xxxx (SG)

3. ユーザー情報の編集フォームについて
- 編集ページは1ページに集約する一方で、ユーザーに小分けで情報入力を求めるために下記要件で編集フォームを実装しました。より良い実装の方法はあるでしょうか？ (DIC)
  - uses/show.html.erbで自分のアカウントの場合であれば各項目（基礎情報、職歴、学歴）ごとに編集ボタンが表示される (DIC)
  - 編集ボタンクリックでusers/show.html.erbに埋め込んだ編集用のモーダルが現れる (DIC)
  - 編集用モーダルはrender partialでformにローカル変数を渡し（例：users/show.html.erb 395行目）、form.html.erbのif文（例：users/form.html.erb 156行目）によってローカル変数ごとに表示されるフォームが変わっている (DIC)
  - (例)Users::WorkExperiencesController　＞　GET /users/:user_id/work_experiences/:id/edit　＞　XHRでmodalのHTMLを返す　＞　フロントでHTMLをJSでモーダルとしてレンダリング (SG)
  - PUT or PATCH /users/:user_id/work_experiences/:id　＞　職歴だけ更新できる (SG)

4. 他受講生から
- 自分でWebアプリを製作したら、簡単そうなこと(例えば動画投稿機能)が面倒で、面倒そうなこと(例えばJavaScriptでアニメーション)が意外と簡単でした。お客さん(非エンジニア)と打ち合わせをするときに、同じような行き違いが発生しそうな気がしましたが、エンジニアのリアルというか、説得の仕方みたいなのありましたら、技術的な話じゃないので恐縮ですが、お伺いできたら幸いです。 (DIC)
  - 要件の本質を理解する (SG)
- 初めてWebアプリを製作しましたが、もっとレベルアップしたいというときに、次のステップのおすすめを経験ベースで教えていただけたら。個人差あると思いますので、一般論的な話になるかもですけど、アドバイスを頂戴できたら幸いです（やっぱり働くのが一番だとは思いますが）。 (DIC)
  - 技術要素を絞って、やれることの引き出しを増やす（手を早くする） (SG)
- 今後、就職活動をするにあたり、採用されるかという問題もありますが、正直、できるだけ良い会社、エンジニアを大事にする会社に就職したいです。で、大学生の就活とかも同じですが、就活生に人気のある会社と、転職者に人気のある会社は往々にして食い違いがあるように思います。なので、未経験者がエンジニアとして就職したいというときに、持っておくべき観点みたいなのをお伺いできたら幸いです。 (DIC)
  - 実際にスパイしよう。 (SG)
- プログラミングスクールを卒業したことを就職活動でどういった形でPR出来ると思いますか？卒業課題をポートフォリオとする、スクールで頑張ったことや苦労したことを伝える、興味のある分野を磨きたいなど意思を表明するなどが求職者側では思いつきますが、企業側から見るとどういったことをPRして欲しいなどあるでしょうか？ (DIC)
  - コードを見せよう (SG)
  - 趣味サイトだとしても本番運用経験があると、より良い (SG)