---
title: Azure で Windows VM リソースを移動する | Microsoft Docs
description: Resource Manager デプロイメント モデルで Windows VM を他の Azure サブスクリプションまたはリソース グループに移動します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: cynthn
ms.openlocfilehash: b98b8c947fb34b60c7bd27b006672e0e9d923d3b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>他の Azure サブスクリプションまたはリソース グループへの Windows VM の移動
この記事では、リソース グループまたはサブスクリプション間で Windows VM を移動する方法について説明します。 サブスクリプション間での移動は、個人のサブスクリプションで作成した VM を、会社のサブスクリプションに移動して作業を続ける場合に便利です。

> [!IMPORTANT]
>現時点では Managed Disks を移動することはできません。 
>
>移動の一環として新しいリソース ID が作成されます。 VM を移動したら、この新しいリソース ID を使用するために、ツールやスクリプトを更新する必要があります。 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>PowerShell を使用した VM の移動

仮想マシンを他のリソース グループに移動するには、依存リソースも必ずすべて移動する必要があります。 Move-AzureRMResource コマンドレットを使用するには、各リソースの ResourceId が必要です。 [Find-AzureRMResource](/powershell/module/azurerm.resources/find-azurermresource) コマンドレットを使用して ResourceId のリストを取得できます。

```azurepowershell-interactive
 Find-AzureRMResource -ResourceGroupNameContains <sourceResourceGroupName> | Format-table -Property ResourceId 
```

VM を移動するには、複数のリソースを移動する必要があります。 Find-AzureRMResource の出力を使用して、ResourceId のコンマ区切りリストを作成し、そのリストを [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) に渡して、リソースを移動先に移動できます。 

```azurepowershell-interactive

Move-AzureRmResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
リソースを他のサブスクリプションに移動するには、 **-DestinationSubscriptionId** パラメーターを含めます。 

```azurepowershell-interactive
Move-AzureRmResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


指定したリソースを移動することの確認を求められます。 

## <a name="next-steps"></a>次の手順
リソース グループとサブスクリプションの間でさまざまな種類のリソースを移動できます。 詳細については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](../../resource-group-move-resources.md)」を参照してください。    

