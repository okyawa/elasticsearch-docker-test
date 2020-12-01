
# CURDの作成例




## index作成


- `sample01` の名前でindexを作成する場合
```sh
curl -X PUT "localhost:9200/sample01?pretty"
```
> {  
>   "acknowledged" : true,  
>   "shards_acknowledged" : true,  
>   "index" : "sample01"  
> }  


### indexのステータス確認

```sh
curl -X GET "localhost:9200/_cat/indices?v&pretty"
```
> health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
> green  open   sample01 xxD5wNkSTzSSybv_EWkGFw   1   1          0            0       416b           208b

- `health`
  - `green`: 正常
  - `yellow`: Replicaシャード作成失敗
  - `red`: Primaryシャード作成失敗
- `pri`
  - Primaryシャード数
- `rep`
  - Replicaシャード数




## documentの読み書き


## document作成

```sh
curl -X PUT "localhost:9200/sample01/_doc/1?pretty" -H 'Content-Type: application/json' -d'
{
  "name": "Kobe Taro"
}
'
```
> {  
>   "_index" : "sample01",  
>   "_type" : "_doc",  
>   "_id" : "1", 
>   "_version" : 1,  
>   "result" : "created",  
>   "_shards" : {  
>     "total" : 2,  
>     "successful" : 2,  
>     "failed" : 0  
>   },  
>   "_seq_no" : 0,  
>   "_primary_term" : 1  
> }


### document取得

```sh
curl -X GET "localhost:9200/sample01/_doc/1?pretty"
```
> {  
>   "_index" : "sample01",  
>   "_type" : "_doc",  
>   "_id" : "1",  
>   "_version" : 1,  
>   "_seq_no" : 0,  
>   "_primary_term" : 1,  
>   "found" : true,  
>   "_source" : {  
>     "name" : "Kobe Taro"  
>   }  
> }

- `found`
  - 取得結果
- `_source`
  - 保存されているJSONデータ


### document削除

```sh
curl -X DELETE "localhost:9200/sample01/_doc/1?pretty"
```


## batch実行

```sh
curl -X POST "localhost:9200/sample01/_bulk?pretty" -H 'Content-Type: application/json' -d'
{"index":{"_id":"1"}}
{"name": "Kobe Taro" }
{"index":{"_id":"2"}}
{"name": "Kobe Jiro" }
'
```
> {  
>   "took" : 168,  
>   "errors" : false,  
>   "items" : [  
>     {  
>       "index" : {  
>         "_index" : "sample01",  
>         "_type" : "_doc",  
>         "_id" : "1",  
>         "_version" : 1,  
>         "result" : "created",  
>         "_shards" : {  
>           "total" : 2,  
>           "successful" : 2,  
>           "failed" : 0  
>         },  
>         "_seq_no" : 4,  
>         "_primary_term" : 1,  
>         "status" : 201  
>       }  
>     },  
>     {  
>       "index" : {  
>         "_index" : "sample01",  
>         "_type" : "_doc",  
>         "_id" : "2",  
>         "_version" : 1,  
>         "result" : "created",  
>         "_shards" : {  
>           "total" : 2,  
>           "successful" : 2,  
>           "failed" : 0  
>         },  
>         "_seq_no" : 5,  
>         "_primary_term" : 1,  
>         "status" : 201  
>       }  
>     }  
>   ]  
> }




## index削除

```sh
curl -X DELETE "localhost:9200/sample01?pretty"
```
> {
>   "acknowledged" : true
> }




## 参照

- https://qiita.com/kiyokiyo_kzsby/items/344fb2e9aead158a5545



