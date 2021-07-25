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

# DockerHubへのPUSH
- DockerHubへのログイン
  > `docker login`<br>
  > 明示的にログイン先の指定がない場合は、DockerHubとなる


- DockerHubへのPUSH
  > `docker push [DockerID]/[イメージ名]:[タグ名]`

# nginxコンテナの実行とデタッチドモード
  > `docker run --name [コンテナ名（自分でつける）] -d -p [ホスト側ポート番号]:[コンテナ側ポート番号] [イメージ名]`<br>
  > ※-d：デタッチドモードで起動（バックグラウンド実行）⇒常駐型のデーモンの場合はこれで立ち上げる。フォアグラウンドでも実行できるが、他の操作ができなくなる。（コンテナはホストの１プロセスとして実行されている。）<br>
  > ※-p：コンテナを外部に公開する設定⇒ホストのポート番号とコンテナのポート番号をマッピングする。
  > ※--rm：rmオプションを付与することでコンテナ停止時にコンテナを削除することができる。

# コンテナの停止、削除
- コンテナの停止
  > `docker stop [コンテナ名 or コンテナID]`
- コンテナの削除
  > `docker rm [コンテナ名 or コンテナID]`

# バインドマウント(-vオプション)
  > `docker run --name [コンテナ名（自分でつける）] -d -v [ホスト側ディレクトリ]:[コンテナ側のマウントポイント] -p [ホスト側ポート番号]:[コンテナ側ポート番号] [イメージ名]`
※Windowsでも区切り文字は`\`を使う<br>
例）`docker run --name test-nginx -d -v /C/Users/yutsu/OneDrive/デスクトップ/docker/html:/usr/share/nginx/html:ro -p 8080:80 nginx`<br>
  > ro:readonlyでコンテナから読み取り専用とする
  
# コンテナ上にファイルコピー
  >` docker cp [ホスト上のコピーしたいファイルパス] [コンテナID：コピー先パス]`
  > ※コンテナからコピーした持ってきたいときは逆にする。

# COPY命令とADD命令
- `COPY [ソース] [ターゲット]`
- ※ADD命令はコピー命令の機能に加えて、tarでアーカイブしたファイルを展開や、URLからダウンロードしてコピーなどの機能がある。基本はCOPY命令を使う

# Dockerコンテナのライフサイクル
- docker createの `-it`オプション：フォアグラウンドで実行状態にしておきたい場合につける（コンテナの永続化）
- `docker start [対象コンテナ]`：コンテナの実行（開始）
- `docker ps`：実行中のコンテナを表示する
- `docker ps -a`：全てのコンテナを表示する（実行中以外）
- `docker inspect [コンテナ名]`：対象コンテナの詳細を確認する
- `docker pause [コンテナ名]`：対象コンテナを一時停止（応答ができない状態:paused状態）
- `docker unpause [コンテナ名]`：対象コンテナの停止解除
- `docker stop [コンテナ名]`：対象コンテナの停止(Exited状態)
- `dockre rm [-f] [コンテナ名]`：対象コンテナの削除（-f：強制削除）
  
## コンテナのシェル接続
- `docker exec -it [コンテナ名] /bin/bash`<br>
  ※/bin/bashがなければ他のシェルで実行する<br>
  ※シェルを抜ける時はexitコマンドでぬける
- `docker attach [コンテナ名]`<br>
  ※exitで抜けた時、コンテナも停止させてしまうから注意！<br>
  ※CTRL+P、CTRL+Qで停止できるが、安全も考慮し、execコマンドを使う⇒pid１プロセスに接続してしまうから
  ※デーモンも起動していた場合、デーモンも標準入出力に接続されてしまう

## コンテナ状態のコミット（イメージとして保存）
- `docker commit [コンテナ名] [イメージ名：タグ名]`<br>
  ※ここで操作した変更は`docker history`コマンドでレイヤとして/bin/bashの情報しかなく、どういう操作をしたのかわからなくなる。Dockerfileを利用して管理するようにする。
