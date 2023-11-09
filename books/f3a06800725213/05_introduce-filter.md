---
title: "フィルター機能を作成しよう！"
---

## フィルター機能を作成しよう

ここでは、取得するデータの絞り込みを行うフィルター機能を作成します。  
前までは無条件で全てのデータを取得していましたが、フィルター機能を作成することで、取得するデータを絞り込むことができます。  

## 使用するデータを外部化する

まずは、ソースコード内に下手書き指定たデータを外部化します。  
同時に使用するデータも変更します。  

`./data/pokemons.json`ファイルを作成して、以下の内容を保存します。  

![pokemon.json](./images/pokemon.json)  

このデータを最初に読み込みます。  
通常はデータベースから読み込むことになると思いますが、今回は簡単のためファイルロードとしています。  

```ts
const pokemons: Pokemon[] = await fs.readFile('./data/pokemons.json', 'utf-8').then((data) => JSON.parse(data))
```

次にフィルターを受け取って、当該フィルターに合致するポケモンのみを返すように修正してみましょう。  
リゾルバの引数には、第一引数に親オブジェクト、第二引数に引数、第三引数にコンテキスト、第四引数に情報が渡されます。  
※ <https://www.apollographql.com/docs/apollo-server/data/resolvers/>

第二引数のフィルタを取得して、これに沿ったポケモンのみを返すように修正してみましょう。  

```ts
const resolvers: Resolvers = {
  Query: {
    pokemons: (_parent, { filter }): Pokemon[] => {
      if (filter == null) {
        return pokemons
      }
      const {
        name,
        types,
        hpMin,
        hpMax,
        atkMin,
        atkMax,
        defMin,
        defMax,
        spatkMin,
        spatkMax,
        spdefMin,
        spdefMax,
        spdMin,
        spdMax
      } = filter
      return pokemons.filter((pokemon) => {
        if (name != null && pokemon.name.toLowerCase() !== name.toLowerCase()) {
          return false
        }
        if (types != null && !(types as string[]).every((type) => pokemon.types.includes(type))) {
          return false
        }
        if (hpMin != null && pokemon.hp < hpMin) {
          return false
        }
        if (hpMax != null && pokemon.hp > hpMax) {
          return false
        }
        if (atkMin != null && pokemon.atk < atkMin) {
          return false
        }
        if (atkMax != null && pokemon.atk > atkMax) {
          return false
        }
        if (defMin != null && pokemon.def < defMin) {
          return false
        }
        if (defMax != null && pokemon.def > defMax) {
          return false
        }
        if (spatkMin != null && pokemon.spatk < spatkMin) {
          return false
        }
        if (spatkMax != null && pokemon.spatk > spatkMax) {
          return false
        }
        if (spdefMin != null && pokemon.spdef < spdefMin) {
          return false
        }
        if (spdefMax != null && pokemon.spdef > spdefMax) {
          return false
        }
        if (spdMin != null && pokemon.spd < spdMin) {
          return false
        }
        if (spdMax != null && pokemon.spd > spdMax) {
          return false
        }
        return true
      })
    }
  }
}
```

では、クエリを実行してみましょう！  

```graphql
query ExampleQuery {
  pokemons(filter: {
    hpMin: 100
  }) {
    id
    name
    hp
  }
}
```

```result
{
  "data": {
    "pokemons": [
      {
        "id": "006",
        "name": "ラウドボーン",
        "hp": 104
      },
      {
        "id": "011",
        "name": "パフュートン",
        "hp": 110
      },
      {
        "id": "030",
        "name": "ヨクバリス",
        "hp": 120
      },
      {
        "id": "043",
        "name": "ピンプク",
        "hp": 100
      },
      {
        "id": "044",
        "name": "ラッキー",
        "hp": 250
      },
      {
        "id": "045",
        "name": "ハピナス",
        "hp": 255
      },
      {
        "id": "048",
        "name": "マリルリ",
        "hp": 100
      },
      {
        "id": "054",
        "name": "ドオー",
        "hp": 130
      },
      {
        "id": "060",
        "name": "プリン",
        "hp": 115
      },
      {
        "id": "061",
        "name": "プクリン",
        "hp": 140
      },
      {
        "id": "080",
        "name": "ケッキング",
        "hp": 150
      },
      {
        "id": "093",
        "name": "セキタンザン",
        "hp": 110
      },
      {
        "id": "110",
        "name": "タルップル",
        "hp": 110
      },
      {
        "id": "117",
        "name": "ハリテヤマ",
        "hp": 144
      },
      {
        "id": "125",
        "name": "ダイオウドウ",
        "hp": 122
      },
      {
        "id": "128",
        "name": "ガブリアス",
        "hp": 108
      },
      {
        "id": "131",
        "name": "キョジオーン",
        "hp": 100
      },
      {
        "id": "140",
        "name": "マルノーム",
        "hp": 100
      },
      {
        "id": "144",
        "name": "フワライド",
        "hp": 150
      },
      {
        "id": "160",
        "name": "コノヨザル",
        "hp": 110
      },
      {
        "id": "169",
        "name": "ナマズン",
        "hp": 110
      },
      {
        "id": "171",
        "name": "ハラバリー",
        "hp": 109
      },
      {
        "id": "180",
        "name": "シャワーズ",
        "hp": 130
      },
      {
        "id": "188",
        "name": "ノコッチ",
        "hp": 100
      },
      {
        "id": "189",
        "name": "ノココッチ",
        "hp": 125
      },
      {
        "id": "193",
        "name": "リキキリン",
        "hp": 120
      },
      {
        "id": "195",
        "name": "ベトベトン",
        "hp": 105
      },
      {
        "id": "206",
        "name": "モロバレル",
        "hp": 114
      },
      {
        "id": "222",
        "name": "ゴーゴート",
        "hp": 123
      },
      {
        "id": "227",
        "name": "スカタンク",
        "hp": 103
      },
      {
        "id": "233",
        "name": "ドンカラス",
        "hp": 100
      },
      {
        "id": "266",
        "name": "カバルドン",
        "hp": 108
      },
      {
        "id": "273",
        "name": "バンバドロ",
        "hp": 100
      },
      {
        "id": "292",
        "name": "イルカマン",
        "hp": 100
      },
      {
        "id": "314",
        "name": "ナゲツケサル",
        "hp": 100
      },
      {
        "id": "318",
        "name": "バンギラス",
        "hp": 100
      },
      {
        "id": "319",
        "name": "イシヘンジン",
        "hp": 100
      },
      {
        "id": "328",
        "name": "トリトドン",
        "hp": 111
      },
      {
        "id": "336",
        "name": "ママンボウ",
        "hp": 165
      },
      {
        "id": "361",
        "name": "アルクジラ",
        "hp": 108
      },
      {
        "id": "362",
        "name": "ハルクジラ",
        "hp": 170
      },
      {
        "id": "366",
        "name": "ウォーグル",
        "hp": 100
      },
      {
        "id": "369",
        "name": "ドドゲザン",
        "hp": 100
      },
      {
        "id": "374",
        "name": "ヘイラッシャ",
        "hp": 150
      },
      {
        "id": "376",
        "name": "イダイナキバ",
        "hp": 115
      },
      {
        "id": "377",
        "name": "サケブシッポ",
        "hp": 115
      },
      {
        "id": "378",
        "name": "アラブルタケ",
        "hp": 111
      },
      {
        "id": "384",
        "name": "テツノカイナ",
        "hp": 154
      },
      {
        "id": "387",
        "name": "テツノイバラ",
        "hp": 100
      },
      {
        "id": "390",
        "name": "セグレイブ",
        "hp": 115
      },
      {
        "id": "395",
        "name": "ディンルー",
        "hp": 155
      },
      {
        "id": "397",
        "name": "トドロクツキ",
        "hp": 105
      },
      {
        "id": "399",
        "name": "コライドン",
        "hp": 100
      },
      {
        "id": "400",
        "name": "ミライドン",
        "hp": 100
      }
    ]
  }
}
```

これで、HPが100以上のポケモンのみを取得することができました！  
