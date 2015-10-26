<properties
   pageTitle="Configurar PowerShell para crear una máquina virtual para Marketplace | Microsoft Azure"
   description="Instrucciones para configurar Azure PowerShell y usarlo como un flujo de proceso opcional para crear imágenes de máquina virtual en las que implementar y vender en Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/08/2015"
   ms.author="hascipio"/>

# Configuración de Azure PowerShell para crear una oferta para Azure Marketplace
Para obtener información detallada sobre cómo configurar PowerShell en Azure, visite [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md). Un enfoque sencillo es usar el método de certificado, que descarga e importa un certificado necesario para autenticar. Para obtener el certificado necesario, use el cmdlet *Get-AzurePublishSettingsFile*. Guarde el archivo cuando se le solicite. Para importar el certificado en una sesión de PowerShell, use el *Import-AzurePublishSettingsFile*.

Para configurar y almacenar la configuración común de la suscripción de Microsoft Azure para la sesión de PowerShell, use los cmdlets *Set-AzureSubscription* y *Select-AzureSubscription*:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

El primer comando asocia una cuenta de almacenamiento predeterminada con la suscripción (necesaria para algunas operaciones de aprovisionamiento de máquinas virtuales). El segundo convierte la suscripción en la actual (reconocida por otros cmdlets).

## Consulte también
- [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)
- [Creación de una imagen de máquina virtual para Marketplace](marketplace-publishing-vm-image-creation.md)

<!---HONumber=Oct15_HO3-->