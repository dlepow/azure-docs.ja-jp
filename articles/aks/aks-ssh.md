---
title: Azure Container Service (AKS) クラスター ノードへの SSH 接続
description: Azure Container Service (AKS) クラスター ノードとの SSH 接続を作成します
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 085a2976443db8ece7a36dbfc133b173432ce4c8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>Azure Container Service (AKS) クラスター ノードへの SSH 接続

場合によっては、メンテナンス、ログ収集、またはその他のトラブルシューティング操作のために、Azure Container Service (AKS) ノードにアクセスしなければならない場合があります。 Azure Container Service (AKS) ノードは、インターネットに公開されていません。 このドキュメントで説明されている手順を使用して、AKS ノードとの SSH 接続を作成してください。

## <a name="get-aks-node-address"></a>AKS ノード アドレスの取得

`az vm list-ip-addresses` コマンドを使用して、AKS クラスター ノードの IP アドレスを取得します。 リソース グループ名は AKS リソース グループの名前に置き換えます。

```console
$ az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table

VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-42032720-0  10.240.0.6
aks-nodepool1-42032720-1  10.240.0.5
aks-nodepool1-42032720-2  10.240.0.4
```

## <a name="create-ssh-connection"></a>SSH 接続の作成

`debian`コンテナー イメージを実行し、ターミナル セッションをそれにアタッチします。 コンテナーを使用して、AKS クラスター内の任意のノードで SSH セッションを作成できます。

```console
kubectl run -it --rm aks-ssh --image=debian
```

コンテナーに SSH クライアントをインストールします。

```console
apt-get update && apt-get install openssh-client -y
```

2 番目のターミナルを開き、すべてのポッドを一覧表示して新しく作成したポッド名を取得します。

```console
$ kubectl get pods

NAME                       READY     STATUS    RESTARTS   AGE
aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
```

ポッドに SSH キーをコピーし、ポッド名を適切な値に置き換えます。

```console
kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
```

ユーザーに対して読み取り専用となるように `id_rsa` ファイルを更新します。

```console
chmod 0600 id_rsa
```

ここで AKS ノードへの SSH 接続を作成します。 AKS クラスターの既定のユーザー名は、`azureuser` です。 クラスターの作成時にこのアカウントが変更された場合は、適切な管理者ユーザー名を使用してください。

```console
$ ssh -i id_rsa azureuser@10.240.0.6

Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

48 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Tue Feb 27 20:10:38 2018 from 167.220.99.8
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@aks-nodepool1-42032720-0:~$
```

## <a name="remove-ssh-access"></a>SSH アクセスの削除

完了したら、SSH セッションを終了し、対話型のコンテナー セッションを終了します。 このアクションにより、AKS クラスターからの SSH アクセスに使用されるポッドが削除されます。