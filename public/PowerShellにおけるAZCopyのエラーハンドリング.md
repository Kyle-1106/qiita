---
title: PowerShellにおけるAZCopyのエラーハンドリング
tags:
  - ''
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---
# 概要
PowerShellでazcopyを使用することでローカルのファイルを簡単にAzureBlobにコピーすることができます。
しかし、エラーが発生した場合のエラーハンドリングがうまくいかないことがあったのでそのエラーハンドリングについて記述します。

# エラーの内容
下のようなコードを実行しました。

```
try {
    $filePath = "C:\Users\user\Desktop\test.zip"
    $SASToken = "https://storageaccount.blob.core.windows.net/container/test.zip"
    azcopy copy $filePath $SASToken --recursive=true
} catch {
    Write-Error "AZCopyのエラーが発生しました。"
    Write-Error $_.Exception.Message
}

```
ここでSASTokenの権限を変更して、azcopyのエラーを意図的に発生させて、エラーハンドリングを正しく行うかのテストを行おうとしました。
エラーが発生するため、catchブロックが実行されるはずですが、エラーハンドリングが行われず、処理が最後まで行ってしまいました。

# 原因
PowerShell で azcopy コマンドを実行する際、azcopy は外部プログラムとして扱われるため、PowerShell の標準的なエラーハンドリング機構である try...catch ブロックではエラーを捕捉できないことがあるらしいです。

[text](https://github.com/Azure/azure-storage-azcopy/issues/1105?utm_source)

# 解決策

下記のコードで意図的に例外を発生させることでエラーハンドリングを行うことができました。
```
try {
    $filePath = "C:\Users\user\Desktop\test.zip"
    $SASToken = "https://storageaccount.blob.core.windows.net/container/test.zip"
    azcopy copy $filePath $SASToken --recursive=true
    $exitCode = $LASTEXITCODE
    if ($exitCode -ne 0) {
      throw "AzCopy failed with exit code $exitCode"
    }
} catch {
    Write-Error "AZCopyのエラーが発生しました。"
    Write-Error $_.Exception.Message
}

```
$LASTEXITCODEはPowerShellで実行した外部アプリの終了コードを取得するための変数です。
この処理を追加することで、azcopyのエラーが発生した場合に$exitCodeには0以外の値が入り、例外を発生させることができます。

# 参考
[text](https://github.com/Azure/azure-storage-azcopy/issues/1105?utm_source)
[text](https://tech.blog.aerie.jp/entry/2019/12/03/022650)
