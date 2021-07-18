# コンテナの実行
- `docker run [イメージに指定]:[タグ(指定がない場合はlatastとなる)]`<br>
  > 詳細動作：dockerクライアント⇒dockerデーモン⇔DockerHub（PC上にイメージがない場合）<br>
  > docker runは下の３つのコマンドをまとめて実行している<br>
  > - `docker pull`：イメージ取得<br>
  > - `docker create`：コンテナ作成<br>
  > - `docker start`：コンテナの起動<br>

# Docker Hub
- [DokerHubサイト](https://hub.docker.com/)
  - officialなイメージや個人や組織のイメージが公開されている

# Dockerイメージのレイヤ
- 各レイヤは読み取り専用
- コンテナ起動後のコンテナレイヤは読み書き可能
- レイヤの例）<br>
  - CentOSのレイヤ
  - Rubyのruntimeのレイヤ
  - Railsのレイヤ
  ![Dockerレイヤ](https://camo.qiitausercontent.com/30b8872915b4437a88860d666329b2f7ebf21549/68747470733a2f2f71696974612d696d6167652d73746f72652e73332e616d617a6f6e6177732e636f6d2f302f3230393930392f37666266626538392d383162352d393165382d316566612d3331616134386163346531632e706e67)

# Dockerイメージのダウンロード
例）whalesayイメージの実行⇒クジラに任意の言葉を話させるイメージ<br>
>`docker run docker/whalesay cowsay YU!!!`<br>
ローカルにない場合はDockerHubからダウンロードされる
各レイヤ毎にダウンロードされ、ローカルにあるレイヤ情報はローカルのものが使用される。

# ローカル上のDockerイメージ管理
- ローカル上にダウンロード済みのイメージ一覧を表示
  > `docker images`

- イメージにタグづけする
  > `docker tag [元になるイメージ] [新しいイメージ:タグ名]`

- イメージの詳細情報表示
  > `docker inspect [対象イメージ]`

- イメージの削除
  > `docker rmi [対象イメージ]`
  > `docker rmi -f [対象イメージ]`（強制削除：コンテナ実行状態な場合など）

- イメージの取得（PULL）
  > `docker pull [取得したいイメージ]`

# Dockerfileを使用してイメージビルド方法と実行
1. ビルドコンテキストを作成する(フォルダを作成）<br>
2. ビルドコンテキスト内にDockerfileを作成する<br>
   > 注意：txtなど拡張子はつけない！
3. Dockerfileに命令を記述する<br>
   > `FROM [イメージ名:タグ名]`　#どのimageを元にimageを作成するか<br>
   > `RUN [パッケージ等インストールコマンド]`  #ここに記述したコマンドを実行してミドルウェアをインストールし、imageのレイヤーを重ねる<br>
   > `CMD [コマンド指定]`　#コンテナが作成された後で実行するコマンドを指定する
4. ビルド実行<br>
   > `docker build -t [ビルドしたイメージに付けるイメージ名] .(ビルドコンテキストの指定）`<br>
   >`docker build —no-cache -t docker-whale`(キャッシュを利用しない)
5. コンテナを実行する<br>
   > `docker run [イメージ]`

  