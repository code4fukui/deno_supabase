# DenoでSupabaseを操作するサンプル

## 概要
- DBからデータを取得する
- DBにデータを追加する

## 準備

1. [Deno](https://deno.land/)をインストール
2. [GitHub](https://github.com/)のアカウントを作成
3. [Supabase](https://supabase.com/)にGitHubアカウントでログイン
4. project test1 を作成し、SUPABASE_URL と SUPABASE_KEY を設定
```sh
export SUPABASE_URL=***
export SUPABASE_KEY=***
```
5. table post を作成し、RLS(Row Level Security)はひとまずdisabledにする
6. table postに、下記の項目を追加
```
username :text
title :text
date :timestamptz
description :text
participants :numeric
```
7. サーバーを起動する
```sh
deno run server.js
```
8. ブラウザで [http://localhost:8000/](http://localhost:8000/) を開く

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

## Update
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
