# DenoでSupabaseを操作するサンプル

## 概要
- DBからデータを取得する
- DBにデータを追加する
- DBのデータを更新する
（[デモ](https://code4fukui-deno-supabase.deno.dev/)）

## 準備

1. [Deno](https://deno.land/)をインストール
2. [GitHub](https://github.com/)のアカウントを作成
3. [Supabase](https://supabase.com/)にGitHubアカウントでログイン
4. project test1 を作成
![image](https://user-images.githubusercontent.com/1715217/227658661-8e05d64d-d6a6-46e0-b7d6-af9d40243355.png)

5. Project URL　を　SUPABASE_URL に、Project API Keys の public を SUPABASE_KEY に設定
![image](https://user-images.githubusercontent.com/1715217/227658705-e6c4c7bb-ad5c-4e6e-a991-8460488a4bb9.png)
```sh
export SUPABASE_URL=***
export SUPABASE_KEY=***
```
6. table post を作成
![image](https://user-images.githubusercontent.com/1715217/227658746-d5a5e028-afe5-4dd9-9c4a-9a5f964e0504.png)

7. RLS(Row Level Security)はひとまずdisabled
![image](https://user-images.githubusercontent.com/1715217/227658765-b0d865d6-9b06-4601-a0fd-2e6f313094be.png)

8. table postに、下記の項目を追加
```
username :text
title :text
date :timestamptz
description :text
participants :numeric
```
![image](https://user-images.githubusercontent.com/1715217/227658788-23a07a30-a03b-44dc-8ec2-959996330ef3.png)

9. サーバーを起動
```sh
deno run server.js
```
10. ブラウザで [http://localhost:8000/](http://localhost:8000/) を開く（ローカル環境動作完了！）

11. [Deno Deploy](https://deno.com/deploy) にGitHubでサインインする
12. New Project、Select GitHub Repository でこのリポジトリの公開したいブランチを選択
13. 起動するサーバープログラムとして server.jsを設定
14. 環境変数(Environment Variables)として、SUPABASE_URLとSUPABASE_KEYを設定し、Link
15. デプロイされたリンクをブラウザで開く（デプロイ完了！）

## 使い方
### fetch
```js
if (req.method === "GET" && pathname === "/fetch-posts") {
  const { data, error } = await supabase.from("post").select("*");
  if (error) return handleError(error);
  return resjson(data);
}
```

### insert
```js
if (req.method === "POST" && pathname === "/register-post") {
  const requestData = await req.json();
  const postData = {
    username: requestData.username,
    title: requestData.title,
    date: requestData.date,
    description: requestData.description,
    participants: 0
  };
  const { error } = await supabase.from("post").insert(postData);
  if (error) return handleError(error);
  return resjson(requestData);
}
```

### Update
```js
if (req.method === "POST" && pathname === "/add-participants") {
  const id = await req.json();
  const { data: participants, error: error1 } = await supabase.from("post")
    .select("participants")
    .eq("id", id);
  if (error1) return handleError(error1);

  const newParticipantCount = participants[0].participants + 1;
  const { error } = await supabase.from("post")
    .update({ participants: newParticipantCount })
    .eq("id", id);
  if (error) return handleError(error);
  return resjson(id);
}
```
