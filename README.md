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




## 参照

- https://qiita.com/kiyokiyo_kzsby/items/344fb2e9aead158a5545
