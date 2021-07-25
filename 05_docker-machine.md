# docker-machine
- ローカルPC、AWS、GCPにVMを作成し、その上でDockerコンテナを実行させるためのコマンド

- docker-machine ls：dockerホスト一覧
- docker-machine create --driver [virtualBox/amazonec2/google] [その他オプション（リージョン指定など）] [VM名]
- docker-machine env：Dockerホストに関する環境変数の設定
- docker-machine ssh [VM]
- docker-machine start/stop/rm [コンテナ]