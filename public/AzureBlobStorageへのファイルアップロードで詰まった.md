---
title: AzureBlobStorageへのファイルアップロードで詰まった
tags:
  - 'Azure'
  - 'PowerShell'
  - 'AZCopy'
  - 'Azure Blob Storage'
private: false
updated_at: ''
id: null
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

ただ、以下のようなエラーが出てしまった。
```
Get-AzStorageAccount : No subscription found in the context.  
Please ensure that the credentials you provided are authorized to access an Azure subscription, 
then run Connect-AzAccount to login.
```
他社に準備していただいた環境であったため、アカウント管理は他社の方が行なっていた。
ファイルをAzure Blob Storageにアップロードするだけなので連携コストを考えて、他に解決策がないかを検討した。

# 解決策
以下の記事を参考にして、AZCopyを使用してファイルをアップロードすることで簡単に解決できた。ありがとうございます！

https://qiita.com/yuta1123/items/702eb7cab8479344fbc8



# 参考
https://qiita.com/yuta1123/items/702eb7cab8479344fbc8