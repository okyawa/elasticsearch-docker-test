# dockerでElasticsearchを起動


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


## 参照

- https://qiita.com/kiyokiyo_kzsby/items/344fb2e9aead158a5545
