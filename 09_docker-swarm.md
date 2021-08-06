# 09_Docker-swarm
## Docker-swarm概要
### Swarm-Mode
- Docker Engineの動作モードのこと
- 複数のノード間でコンテナを管理する（クラスタ管理）
- オーケストレーションツール（他にも有名なのがKubernatesとか）
- ingressというノード間をまたがるオーバーレイネットワークが各ノードのコンテナに接続される
- マネージャノード（ワーカーノードと兼務できる）とワーカーノードで構成される
- 管理系のコマンドはマネージャノードで実行

## Swarmクラスタの作成
- `docker swarm init --advertiseaddr [マネージャノードのIP]`
- Swarmで利用されるポート番号：制限している場合は明示的に開放してあげる
  - クラスタ管理通信用：TCP2377
  - ノード間通信用：TCP/UDP7946
  - オーバーレイネットワークトラフィック用：UDP4789
- `docker swarm join --token [固有ID]`:これをワーカーノードで実行することでワーカーノードがSwarmクラスタに参加できる

## サービスとタスク
- サービス：マネージャノードやワーカーノードで実行するタスクの定義、サービス作成時にはイメージ指定やポートなどの設定を行う
- タスク：サービスで定義された内容をノードで実行する実行単一
- `docker service create --replicas 3 nginx`：nginxコンテナを３つのノードで実行するサービスを作成
-  `docker servise ps nginx`：サービスから作成した実行中のnginxコンテナ（タスク）を表示
-  `docker servise ls`：サービス一覧を表示

## サービス管理コマンド
- `docker servise update`：サービスを更新（新ポートの公開だと --publish-addr 8000:80など）
- `docker servise rollback`：サービスの更新を１つ前に戻す
- `docker servise remove [サービス名、ID]`：サービスの削除

## ルーティングメッシュ
- 通常は各ノードにSwarmロードバランサが存在し、各ノードで受け取ったリクエストはアクティブなコンテナに割りふられる
  - これは必ずしも同一ノード内のコンテナにリクエストが割り当てられるわけではない
- 上記が望ましくない場合は（外部LBを設定して、明確にノード内のコンテナに通信を割り振りたい）は、publishフラグにホストモードの指定をいれる
  - `docker servise create --name web --publish mode=host,published=8000,target=80 --replicas=2 nginx`

## レプリカモードとグローバルモード
- レプリカモード：デフォルトのモード、レプリカ数を指定して、ノード数より多い少ないタスクを実行できる
- グローバルモード：各ノードで１つのタスク（コンテナ）を起動（各ノード１つずつ欲しい監視エージェントのコンテナなどで利用される）

## Docker Swarmの可用性
- 1台だけのマネージャノードでダウンした場合、Swarmクラスタ機能は失われる
- 複数マネージャノードで構成する＝＞3台で1台ダウン、5台で2台ダウンを許容できる
- `docker node promote [ワーカーノード名]`：ワーカーノードをマネージャノードに昇格させる
- マネージャノードが復旧してもタスクの再配置は自動的に行われないので、再配置させる場合は下記を実行
  - `docker servise update --force [サービス名]`

## ローリングアップデート
- 各タスクを遅延時間を設定して、サービス全体に影響を与えないように１つずつ更新していく
- `docker servise create --replicas 3 --name redis --update-delay 10s redis:3.0.6`

## サービスのスケール
- `docker servise scale [サービス名]=[スケールする数]`
  
## ノードのアベイラビリティ
- Active：スケジューラサービスでノードにタスクを割り当てれる
- Pause：新規にタスクを割り当てることはできないが、既存のタスクは実行中のままである
- Drain：既存のタスクも停止し、新規に割り当てることもできない（メンテナンス作業）
- `docker node update --availability [モード] [ノード名]` 

## Docker-composeを使用したサービスの作成
- docker-composeで定義して、複数のサービスを各ノードへデプロイできる
  - ymlファイルのdeployタグでreplica数、constraints（ノード指定）を記述できる
- `docker stack deploy --compose-file docker-compose.yml [スタック名]`
- `docker stack ls`
- `docker stack remove [スタック名]`