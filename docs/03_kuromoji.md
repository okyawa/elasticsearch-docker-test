# kuromojiプラグインを使った形態素解析




## kuromojiを使用してインデックスを作成


```sh
curl -X PUT 'localhost:9200/my_index' -H 'Content-Type: application/json' -d'
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_kuromoji_analyzer": {
          "type": "custom",
          "mode": "search",
          "tokenizer": "kuromoji_tokenizer"
        }
      }
    }
  }
}
'
```




## どのように形態素解析されるかを確認


```sh
curl -X GET "localhost:9200/my_index/_analyze?pretty" -H 'Content-Type: application/json' -d'
{
  "analyzer": search",
  "text": "検索したい文字"
}
'
```





## 参照

- [ElasticsearchでKuromoji Tokenizerを試す](http://pppurple.hatenablog.com/entry/2017/05/28/141143)


