---
title: TypeORMでDate型使えない問題
tags:
  - TypeScript
  - TypeORM
private: false
updated_at: '2025-01-13T19:38:35+09:00'
id: f6bd1f8bbafe8f56bd5b
organization_url_name: null
slide: false
ignorePublish: false
---
## 環境
Typescript  4.5.2
Express    4.16.1
TypeORM 0.3.17
postgreSQL  15

## 状況

TypeORMのEntityでnullableなDate型を定義したいのですが、以下のコードでエラーが出ました。
```typescript:
@Column({ nullable: true, type: "Date" })
  time!: Date|null;
```

```text:エラー内容
TS2769: No overload matches this call.
  The last overload gave the following error.
    Argument of type '{ nullable: boolean; type: string; }' is not assignable to parameter of type '(type?: any) => Function'.
      Object literal may only specify known properties, and 'nullable' does not exist in type '(type?: any) => 
Function'.

20   @Column({ nullable: true, type: "Date" })
```

ここで調査してみると、TypeORMにはDate型はなく、string型で表現されるらしいです。
https://github.com/typeorm/typeorm/issues/2176

そこでstring型に変更しましたがまたエラーが出ます。
```text:
TypeORM connection error:  
DataTypeNotSupportedError: Data type "Object" in "Schedule.time" is not supported by "postgres" database.
```
これについてはこれと同じくtype:'varchar'を明示してあげることでうまくいきました。
https://stackoverflow.com/questions/64350794/typeorm-message-data-type-object-in-is-not-supported-by-postgr


