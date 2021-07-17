# コンテナの実行
- `docker run [イメージに指定]`<br>
  > 詳細動作：dockerクライアント⇒dockerデーモン⇔DockerHub（PC上にイメージがない場合）<br>
  > docker runは下の３つのコマンドをまとめて実行している<br>
  > - `docker pull`：イメージ取得<br>
  > - `docker create`：コンテナ作成<br>
  > - `docker start`：コンテナの起動<br>