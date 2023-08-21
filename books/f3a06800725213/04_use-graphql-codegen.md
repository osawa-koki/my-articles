---
title: "GraphQLのスキーマ定義からTypeScriptの型定義を自動生成しよう！"
---

## GraphQLのスキーマ定義からTypeScriptの型定義を自動生成しよう

今までは、GraphQLのスキーマ(型定義)とTypeScriptの型定義を別々に生成していましたが、GraphQL Code Generatorでは、GraphQLのスキーマからTypeScriptの型定義を生成することができます。  

## 必要なパッケージをインストールする

まずは、必要なパッケージをインストールします。

```bash
yarn add -D @graphql-codegen/cli @graphql-codegen/typescript @graphql-codegen/typescript-operations @graphql-codegen/typescript-resolvers
```

## GraphQL Code Generatorの設定ファイルを作成する

次に、GraphQL Code Generatorの設定ファイルを作成します。

```bash
yarn graphql-codegen init
```

イロイロ聞かれるので、以下のように答えます。  

```bash
? What type of application are you building? (Use arrow keys)
❯ Backend - API or server 
  Application built with Angular 
  Application built with React 
  Application built with Stencil 
  Application built with Vue 
  Application using graphql-request 
  Application built with other framework or vanilla JS

? Where is your schema?: (path or url) ./schemas/**/*.graphql

? Pick plugins: (Press <space> to select, <a> to toggle all, <i> to invert selection, and <enter> to proceed)
❯◉ TypeScript (required by other typescript plugins)
 ◉ TypeScript Resolvers (strongly typed resolve functions)
 ◯ TypeScript MongoDB (typed MongoDB objects)
 ◯ TypeScript GraphQL document nodes (embedded GraphQL document)

? Where to write the output: (src/generated/graphql.ts) 

? Do you want to generate an introspection file? Yes

? How to name the config file? codegen.ts

? What script in package.json should run the codegen? codegen
```

これで、`yarn codegen`を実行すると、GraphQLのスキーマからTypeScriptの型定義を生成することができます。  
実際に、`yarn codegen`を実行してみましょう。  

```bash
$ graphql-codegen --config codegen.ts
✔ Parse Configuration
⚠ Generate outputs
  ✔ Generate to src/generated/graphql.ts
  ❯ Generate to ./graphql.schema.json
    ✔ Load GraphQL schemas
    ✔ Load GraphQL documents
    ✖
      Unable to find template plugin matching 'introspection'
      Install one of the following packages:
      - @graphql-codegen/introspection
      - @graphql-codegen/introspection-template
      - @graphql-codegen/introspection-plugin
      - graphql-codegen-introspection
      - graphql-codegen-introspection-template
      - graphql-codegen-introspection-plugin
      - codegen-introspection
      - codegen-introspection-template
      - introspection
error Command failed with exit code 1.
info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
```

上記のエラーが発生する場合には、`yarn install`を実行し直してください。  

---

以下のファイルが生成されていることを確認してください。  

- `./src/generated/graphql.ts`
- `graphql.schema.json` (Introspectionをオンにした場合のみ)

## 自動生成されたデータ型を使用する

ソースコード内で定義したデータ型を削除して、自動生成されたデータ型を使用するように変更します。  

```diff ts
import { ApolloServer } from '@apollo/server';
import { startStandaloneServer } from '@apollo/server/standalone';

import { GraphQLFileLoader } from '@graphql-tools/graphql-file-loader';
import { loadSchemaSync } from '@graphql-tools/load';
import { addResolversToSchema } from '@graphql-tools/schema';
+ import { Book, Resolvers } from './src/generated/graphql';

(async () => {
- interface Book {
-   title: string;
-   author: string;
- }

  const schema = loadSchemaSync('./schemas/**/*.graphql', {
    loaders: [new GraphQLFileLoader()],
  });

 const books: Book[] = [
    {
      title: 'The Awakening',
      author: 'Kate Chopin',
    },
    {
      title: 'City of Glass',
      author: 'Paul Auster',
    },
  ];

- const resolvers = {
+ const resolvers: Resolvers = {
    Query: {
      books: (): Book[] => books,
    },
  };

  const schemaWithResolvers = addResolversToSchema({ schema, resolvers });
  const server = new ApolloServer({ schema: schemaWithResolvers });

  const { url } = await startStandaloneServer(server, {
    listen: { port: 4000 },
  });

  console.log(`🚀  Server ready at: ${url}`);
})()
  .then(() => console.log('Server started...'))
  .catch((e) => console.error(e));
```
