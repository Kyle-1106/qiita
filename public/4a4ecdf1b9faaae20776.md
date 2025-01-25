---
title: AzureBlobStorageへのファイルアップロードで詰まった
tags:
  - Azure
  - PowerShell
  - Storage
  - AzCopy
  - AzureBLOBStorage
private: false
updated_at: '2025-01-19T11:10:09+09:00'
id: 4a4ecdf1b9faaae20776
organization_url_name: null
slide: false
ignorePublish: false
---

# 概要
PowerShellを使用して、Azure Virtual MachinesからAzure Blob Storageへのファイルアップロードする機能の実装で詰まったので、その解決策を共有します。


# 詰まったこと

まずは以下のドキュメントを参考にして、ファイルをアップロードしようとした。
```
Set-AzStorageBlobContent -Container $containerName -File $zipPath -Blob $blobName -Context (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
```
https://learn.microsoft.com/ja-jp/azure/storage/blobs/storage-quickstart-blobs-powershell

## エラー発生
ただ、以下のようなエラーが出てしまった。
```
Get-AzStorageAccount : No subscription found in the context.  
Please ensure that the credentials you provided are authorized to access an Azure subscription, 
then run Connect-AzAccount to login.
```

## エラーの原因
このエラーの原因は、PowerShellセッションでAzureサブスクリプションにログインしていないか、ログイン情報が有効ではないためである。今回はログイン処理をPowershellで実装するなどは行なっていないためこのようなエラーが起きた。Get-AzStorageAccountコマンドは、Azureのサブスクリプションに接続されている必要があるが、環境が他社によって管理されており、ファイルをAzure Blob Storageにアップロードするだけなので連携コストやログインするための設定を追加する工数を考慮すると、他に解決策がないかを検討した。

# 解決策
以下の記事を参考にして、AZCopyを使用してファイルをアップロードすることで簡単に解決できた。ありがとうございます！

https://qiita.com/yuta1123/items/702eb7cab8479344fbc8

## なぜAZCopyで解決できたのか
PowerShellのコマンドは、Azureサブスクリプションの認証情報が必要だが、AZCopyでは、SASトークンやストレージアカウントのキーを使って直接認証を行うことができるため、Azureサブスクリプションに対するログインが不要で簡単に解決できた。これにより、適切なSASトークンやアクセスキーがあれば、スムーズにファイルのアップロードが可能になった。


# 参考

https://learn.microsoft.com/ja-jp/azure/storage/blobs/storage-quickstart-blobs-powershell

https://qiita.com/yuta1123/items/702eb7cab8479344fbc8
