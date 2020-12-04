# 検索




## 基本構文


Key       | 説明
--------- | -----------------------------
`query`   | 検索の条件（≒where句）
`_source` | 取得するフィールド名（≒select句）
`from`    | 取得開始位置（≒offset句）※0スタート
`size`    | 取得件数（≒limit句）
`sort`    | ソート条件（≒order by句）




## サンプルデータの流し込み


- `sample-data/accounts.json` のサンプルデータをbatch処理で登録

```sh
curl -H "Content-Type: application/json" -X POST "localhost:9200/bank/_bulk?pretty&refresh" --data-binary "@sample-data/accounts.json"
```

### リクエスト例

```sh
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} },
  "_source": ["account_number", "balance"],
  "from": 10,
  "size": 10,
  "sort": { "balance": { "order": "desc" } }
}
'
```




## 流し込んだサンプルデータを削除

```sh
curl -X DELETE "localhost:9200/bank?pretty"
```




## インデックスのステータス表示

```sh
curl -X GET "localhost:9200/bank/_stats?pretty"
```




## 検索クエリ


### match_all

- 全件取得

```sh
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} }
}
'
```


### match

- 指定したワードが含まれているdocumentを返す
- スペース区切りを入れると複数ワード指定となり、デフォルトではor検索

```sh
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "address": "Holmes Madison"
    }
  }
}
'
```


### match_phrase 

- 指定したフレーズが含まれているdocumentを返す
- `match`と違い、複数の単語をひとまとまりとして認識



### range

- 範囲指定
- `gte`
  - 以上
  - greater than or equal
- `lte`
  - 以下
  - less than or equal

```sh
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "range": {
      "age" : { "gte" : 20, "lte" : 29 }
    }
  }
}
'
```


### bool句 (複数条件の組み合わせ)


- 条件とdocumentの適合度は検索結果の `_score` で確認可能


#### must

- 必ず満たされる条件
- 条件とdocumentの適合度が高ければ高いほど検索結果の上位に表示される
- 複数条件を書く場合は配列表記

```sh
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool" : {
      "must" : [
        { "match" : { "address" : "mill" } },
        { "range" : { "age" : { "gte" : 21, "lte" : 30 }}}
      ]
    }
  }
}
'
```


#### should

- `must`と違って、プラスアルファな要素
- 条件とdocumentの適合度が高ければ高いほど検索結果の上位に表示される

```sh
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool" : {
      "should" : [
        { "match" : { "address" : "mill" } },
        { "range" : { "age" : { "gte" : 21, "lte" : 30 }}}
      ]
    }
  }
}
'
```


#### must_not

- 条件を満たすdocumentは必ず出力されなくなる

```sh
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool" : {
      "must_not" : [
        { "match" : { "address" : "mill" } },
        { "range" : { "age" : { "gte" : 21, "lte" : 30 }}}
      ]
    }
  }
}
'
```


#### filter

- `must`と同様、書いた条件が必ず満たされる
- `must`との違いは、条件とdocumentの適合度算出には使われない点
- 単純なフィルタリングを行いたい場合は`must`よりも`filter`の方がベター

```sh
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool" : {
      "filter" : [
        { "match" : { "address" : "mill" } },
        { "range" : { "age" : { "gte" : 21, "lte" : 30 }}}
      ]
    }
  }
}
'
```


#### bool句で各条件を組み合わせた例 

- `filter`: ageが20以上、30以下 (※最初に`filter`が実行される)
- `must`: addressにroadが含まれている
- `must_not`: addressにmillが含まれていない
- `should`: balanceが30000以上だと望ましい
- `should`:` genderがFだと望ましい

```sh
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool" : {
      "must" : {
        "match" : { "address" : "road" }
      },
      "filter": {
        "range" : { "age" : { "gte" : 20, "lte" : 30 } }
      },
      "must_not" : {
        "match" : { "address" : "mill" }
      },
      "should" : [
        { "range" : { "balance" : { "gte" : 30000 } } },
        { "match" : { "gender" : "F" } }
      ]
    }
  }
}
'
```




## indexの詳細情報を表示

- mappingsやsettingsなど (スキーマ的なもの)

```sh
curl -X GET "localhost:9200/bank?pretty" 
```




## 配列データに含んでいるものがあるもを抽出 (array contains的なやつ)


### 保存された値が文字列配列の場合

- ダブルクオーテーションで値の文字列を囲う必要なし

```sh
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool" : {
      "filter": {
        "query_string" : { "query" : "hobbies:(music)" }
      }
    }
  },
  "_source": ["account_number", "balance", "hobbies"],
  "size": 10,
  "sort": { "balance": { "order": "desc" } }
}
'
```


### 保存された値が数値配列の場合

```sh
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool" : {
      "filter": {
        "query_string" : { "query" : "groupId:(11)" }
      }
    }
  },
  "_source": ["account_number", "balance", "groupId"],
  "size": 10,
  "sort": { "balance": { "order": "desc" } }
}
'
```


### 配列に含まれないものだけを抽出 (not array contains的なやつ)

- `NOT ()` もしくは `!()`

```sh
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool" : {
      "filter": {
        "query_string" : { "query" : "NOT (groupId:(11))" }
      }
    }
  },
  "_source": ["account_number", "balance", "groupId"],
  "size": 5,
  "sort": { "balance": { "order": "desc" } }
}
'
```




## aggregations (集約クエリ)


- ElasticSearchの集計機能


### aggregationsのサンプル

- 自動mappingの場合、数値型のみ動作を確認
  - 文字列をAggregationの引数に指定するとエラーになる

```sh
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} },
  "_source": ["account_number", "balance", "name", "firstname"],
  "aggs": {
    "types": {
      "terms": {
        "field": "balance"
      }
    }
  },
  "size": 10,
  "sort": { "balance": { "order": "desc" } }
}'
```




## 参照

- [公式サンプルデータ - elastic elasticsearch - GitHub](https://github.com/elastic/elasticsearch/blob/master/docs/src/test/resources/accounts.json)
- https://qiita.com/kiyokiyo_kzsby/items/344fb2e9aead158a5545
- https://qiita.com/NAO_MK2/items/630f2c4caa0e8a42407c



