---
title: 'Stockage Premium Azure : Concevoir pour des performances élevées sur les machines virtuelles Linux | Microsoft Docs'
description: Concevez des applications hautes performances avec les disques managés SSD Azure Premium. Premium Storage offre une prise en charge très performante et à faible latence des disques pour les charges de travail utilisant beaucoup d'E/S exécutées sur les machines virtuelles Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9940ee4cfce9721ac65f2b3cf1469e180adfa098
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79231981"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Stockage Azure Premium : conception sous le signe de la haute performance
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Parfois, ce qui semble être un problème de performances disque est en fait un goulot d’étranglement sur le réseau. Dans ces situations, vous devez optimiser vos [performances réseau](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Si vous souhaitez évaluer votre disque, consultez notre article sur le [Benchmarking d’un disque](disks-benchmarks.md).
>
> Si votre machine virtuelle prend en charge la mise en réseau accélérée, vous devez vous assurer qu’elle est activée. Si elle n’est pas activée, vous pouvez l’activer sur les machines virtuelles déjà déployées sur [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) et [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Avant de commencer, si vous débutez avec le Stockage Premium, commencez par lire les articles [Sélectionner un type de disque Azure pour les machines virtuelles IaaS](disks-types.md) et [Cibles de scalabilité pour les comptes de stockage d’objets blob de pages Premium](../../storage/blobs/scalability-targets-premium-page-blobs.md).


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Si vous souhaitez évaluer votre disque, consultez notre article sur le [Benchmarking d’un disque](disks-benchmarks.md).

En savoir plus sur les types de disque disponibles : [Sélectionner un type de disque](disks-types.md)  

Pour les utilisateurs de SQL Server, consultez les articles relatifs aux meilleures pratiques de performances de SQL Server :

* [Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](../windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Storage provides highest performance for SQL Server in Azure VM (en anglais)](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)