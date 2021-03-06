---
title: Azure Resource Manager の vCPU クォータを増やす要求 | Microsoft Docs
description: Azure Resource Manager の vCPU クォータを増やす要求
author: ganganarayanan
ms.author: gangan
ms.date: 1/18/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c22a6dde0067385a1bf8d889cc76178bb44dd0ac
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Resource Manager の vCPU クォータを増やす要求

Resource Manager の vCPU クォータは、リージョン レベルおよび SKU のファミリ レベルで適用されます。
クォータを適用する方法について詳しくは、[Azure のサブスクリプションとサービスの制限](http://aka.ms/quotalimits)に関するページをご覧ください。
SKU のファミリについては、[Virtual Machines の料金](http://aka.ms/pricingcompute)ページでコストとパフォーマンスを比較できます。

vCPU 数の増加を要求するには、Azure Portal ([https://portal.azure.com](https://portal.azure.com)) で vCPU についてクォータのサポート ケースを作成します。

> [!NOTE]
> Azure Portal で[サポート要求を作成](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)する方法

1. [新しいサポート要求] ページで、[Issue type (問題の種類)] として [クォータ]、[Quota type (クォータの種類)] として [コア] を選択します。

    ![クォータの [基本] ブレード](./media/resource-manager-core-quotas-request/Basics-blade.png)

2. [デプロイ モデル] として [リソース マネージャー] を選択して [場所] を選択します。

    ![クォータの [問題] ブレード](./media/resource-manager-core-quotas-request/Problem-step.png)

3. 増加する必要がある SKU のファミリを選択します。

    ![選択した SKU シリーズ](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. サブスクリプションに対して必要な新しい制限を入力します。

    ![SKU の新しいクォータ要求](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- 行を削除するには、[SKU family (SKU ファミリ)] ドロップダウン リストで該当する SKU をオフにするか、破棄アイコン (x) をクリックします。
各 SKU ファミリに必要なクォータを入力した後に、[問題] ステップ ページの [次へ] をクリックしてサポート要求の作成を続行します。
