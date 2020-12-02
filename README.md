# DockerでElasticsearchを試用




## 初期設定


### ビルド

```sh
docker-compose build
```

### 起動

```sh
docker-compose up -d
```

- 以下のメッセージが出ればelasticsearchコンテナの起動完了
  - `localhost:9200` で起動

> Creating es01 ... done  
> Creating es02 ... done


### elasticsearchクラスタの停止

```sh
docker-compose down
```

- Dockerイメージの削除まで同時に行いたい場合

```sh
docker-compose down --rmi all
```




## コマンドラインからcurlで状態確認


### クラスのヘルスチェック

```sh
curl -X GET "localhost:9200/_cat/health?v&pretty"
```


### 各ノードのステータスを取得

```sh
curl -X GET "localhost:9200/_cat/nodes?v&pretty"
```




## Windows10でDockerを使う場合の注意点


- `Windows10 (Docker on windows use WSL2)` で `docker-compose up` を実行するとメモリ不足のエラーが出る場合がある
- [ElasticSearch in Windows docker image vm max map count - StackOverflow](https://stackoverflow.com/questions/42111566/elasticsearch-in-windows-docker-image-vm-max-map-count)
- [Windows10対応(Docker on windows use WSL2) #1](https://github.com/okyawa/elasticsearch-docker-test/issues/1)


### Windows10環境(WSL2)でバーチャルメモリの容量が足りないというエラーが出る場合の手順

- `wsl -d docker-desktop` としてDocker用のディストリビューションに入る
- `sysctl -w vm.max_map_count=262144` としてバーチャルメモリの値を上げる
- `echo "vm.max_map_count = 262144" > /etc/sysctl.d/99-docker-desktop.conf` として起動時から有効になるように設定




## 参照

- https://qiita.com/kiyokiyo_kzsby/items/344fb2e9aead158a5545
