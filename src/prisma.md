# prismaでDB管理

[document](https://www.prisma.io/)

目的、 Webで簡単にDB管理をする

### 環境

MAMP

PHPMyadomin

### DB管理用のプロジェクトを作成する

[document](https://www.prisma.io/docs/getting-started/setup-prisma/start-from-scratch/relational-databases-typescript-postgres)に沿ってプロジェクトを作成する。※必要なさそうなところは省略していくスタイル。


プロジェクト ディレクトリを作成し、そこに移動します。

```bush
mkdir hello-prisma 
cd hello-prisma
```

TypeScript プロジェクトを初期化し、Prisma CLI を開発依存関係として追加

```bush
npm init -y 
npm install prisma typescript ts-node @types/node --save-dev
```

Prisma CLI を起動する。(いらないかも)

```bush
npx prisma
```

次のコマンドを使用してPrismaスキーマファイルを作成し、Prismaプロジェクトをセットアップします。

```bush
npx prisma init
```

### DBと接続するための設定をする

`hello-prisma/prisma/schema.prisma`の内容を変更する

今回は`mysql`を使用するため、テキストを変更

```diff prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
-  provider = "postgresql"
+  provider = "mysql"
  url      = env("DATABASE_URL")
}
```

.envファイルの内容を変更する

[参考資料](https://www.prisma.io/docs/concepts/database-connectors/mysql)

```diff .env
- DATABASE_URL="postgresql://johndoe:randompassword@localhost:5432/mydb?schema=public"
+ DATABASE_URL="mysql://masa:masafumidb@localhost:8889/kainusikentei001"
```

### 既存のDB情報がない場合⇨DBに格納するデータを設定

既存のDB情報がない場合

[参考資料](https://www.prisma.io/docs/getting-started/setup-prisma/start-from-scratch/relational-databases/using-prisma-migrate-typescript-postgres)

`hello-prisma/prisma/schema.prisma`に追記する

<img src="./img/schema001.png">

`schema`作成後は下記のコマンドを実行するとDBのテーブルが自動で作成される

```bush
npx prisma migrate dev --name init
```

`/example-pj/prisma/migrations`ディレクトリが作成され、`PHPMyadomin`にも自動で更新がかかる

Webブラウザでtableが作成されているか確認する

```bush
npx prisma studio
```

### 既存のDB情報がある場合⇨DBから自動作成

⚠️⚠️`migrate`コマンドを使用するときは注意してください⚠️⚠️

> `/Desktop/example-pj/prisma/migrations`フォルダの内容を確認し、データが上書きされる事を認識してから作業してください。

[参考資料](https://www.keisuke69.net/entry/2022/08/17/143620)

DB情報をあらかじめエクスポートしてください。

`schema`を既存のDBから読み取る方法は、`hello-prisma/prisma/schema.prisma`ファイルと`.env`ファイルを既存のDBに接続する設定にし、下記のコマンドを実行。

```bush
npx prisma db pull
```

すると`schema.prisma`が更新される

<img src="./img/schema001.png">

次に、`hello-prisma/prisma/schema.prisma`ファイルと`.env`ファイルを新しく作成するDBの接続設定にする。

変更を確認したら以下のコマンドを実行し、テーブルを自動で作成する

```bush
npx prisma migrate dev --name init
```

`/hello-prisma/prisma/migrations/20220911165042_init/migration.sql`が自動で作成されるので、事前にエクスポートした`sql`ファイルの`INSERT文`をコピペして追加で貼り付ける

<img src="./img/insert.png">

最後に、データベースをリセットし、編集した移行を含むすべての移行を再生します。

上記の作業でデータを復元し、移行が完了します。下記のコマンドを実行

```bush
npx prisma migrate dev
```

Webブラウザでtableが作成されているか確認する

```bush
npx prisma studio
```




