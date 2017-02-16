---
title: "Configuración de PowerShell para crear una máquina virtual para Marketplace | Microsoft Docs"
description: "Instrucciones para configurar Azure PowerShell y usarlo como un flujo de proceso opcional para crear imágenes de máquina virtual en las que implementar y vender en Azure Marketplace"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 68be118bc40e3a62aad73cb43119f49415f5b6a9


---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Configurar Azure PowerShell para crear una oferta para Azure Marketplace
Para obtener información detallada sobre cómo configurar PowerShell en Azure, vea [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs). Un enfoque sencillo es usar el método de certificado, que descarga e importa un certificado necesario para la autenticación. Para obtener el certificado necesario, use el cmdlet **Get-AzurePublishSettingsFile** . Guarde el archivo cuando se le pida. Para importar el certificado en una sesión de PowerShell, use el cmdlet **Import-AzurePublishSettingsFile** .

Para configurar y almacenar la configuración común de suscripción de Microsoft Azure para la sesión de PowerShell, use los cmdlets **Set-AzureSubscription** y **Select-AzureSubscription**:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

El primer comando asocia una cuenta de almacenamiento predeterminada con la suscripción (necesaria para algunas operaciones de aprovisionamiento de máquinas virtuales).  El segundo convierte la suscripción en la actual (reconocida por otros cmdlets).

## <a name="see-also"></a>Consulte también
* [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)
* [Creación de una imagen de máquina virtual para Marketplace](marketplace-publishing-vm-image-creation.md)




<!--HONumber=Feb17_HO3-->


