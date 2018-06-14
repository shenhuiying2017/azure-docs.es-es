---
title: Configuración de PowerShell para crear una máquina virtual para Marketplace | Microsoft Docs
description: Instrucciones para configurar Azure PowerShell y usarlo como un flujo de proceso opcional para crear imágenes de máquina virtual en las que implementar y vender en Azure Marketplace
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: mbaldwin
ms.openlocfilehash: 72ee1ac612fc4c7191718009a78f55272f0a44cf
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
ms.locfileid: "29937406"
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Configurar Azure PowerShell para crear una oferta para Azure Marketplace
Para obtener información detallada sobre cómo configurar PowerShell en Azure, vea [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview). Un enfoque sencillo es usar el método de certificado, que descarga e importa un certificado necesario para la autenticación. Para obtener el certificado necesario, use el cmdlet **Get-AzurePublishSettingsFile** . Guarde el archivo cuando se le pida. Para importar el certificado en una sesión de PowerShell, use el cmdlet **Import-AzurePublishSettingsFile** .

Para configurar y almacenar la configuración común de suscripción de Microsoft Azure para la sesión de PowerShell, use los cmdlets **Set-AzureSubscription** y **Select-AzureSubscription**:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

El primer comando asocia una cuenta de almacenamiento predeterminada con la suscripción (necesaria para algunas operaciones de aprovisionamiento de máquinas virtuales).  El segundo convierte la suscripción en la actual (reconocida por otros cmdlets).

## <a name="see-also"></a>Otras referencias
* [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)
* [Creación de una imagen de máquina virtual para Azure Marketplace](marketplace-publishing-vm-image-creation.md)

