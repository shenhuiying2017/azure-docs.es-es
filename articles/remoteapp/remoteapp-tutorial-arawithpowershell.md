<properties
   pageTitle="Introducción a Azure RemoteApp con Powershell"
   description="Obtenga información sobre cómo comenzar a utilizar Azure RemoteApp con Powershell"
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/12/2015"
   ms.author="guscatal;spatnaik;elizapo"/>



# Introducción a Azure RemoteApp con Powershell
=====================================


## Obtención de los cmdlets 
-------------
En primer lugar, tenemos que descargar [aquí](http://go.microsoft.com/?linkid=9811175) los commandlets de Azure PowerShell, que incluyen los de RemoteApp.

Consulte la ayuda de cmdlets de Azure RemoteApp [aquí](https://msdn.microsoft.com/library/mt428031.aspx).

## Configuración de los cmdlets de Azure para usar su suscripción
------------------
Siga [esta guía](../powershell-install-configure.md) para poder usar los cmdlets en su suscripción de Azure.

## Creación de una colección en la nube
--------------------
Es sencillo, ejecute el siguiente comando:

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

El comando anterior publica automáticamente las aplicaciones de Microsoft Office 365 (Excel, OneNote, Outlook, PowerPoint, Visio y Word).

La creación de la colección puede tardar 30 minutos o más en completarse. Por lo tanto, este comando devuelve un identificador de seguimiento que se puede utilizar como sigue:


    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

Cuando la colección esté lista, puede agregar usuarios a la colección con el siguiente comando:

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

¡Y ya está! Ese usuario debería ser capaz de conectarse a la aplicación mediante el cliente de Azure RemoteApp que se encuentra [aquí](https://www.remoteapp.windowsazure.com/).

## Cmdlets disponibles
Tenemos muchos otros comandos, cuya información se pondrá pronto a disposición de los usuarios:

Cmdlets básicos de la colección de RemoteApp

- New-AzureRemoteAppCollection
- Get-AzureRemoteAppCollection
- Set-AzureRemoteAppCollection
- Update-AzureRemoteAppCollection
- Remove-AzureRemoteAppCollection
- Add-AzureRemoteAppUser
- Get-AzureRemoteAppUser
- Remove-AzureRemoteAppUser
- Get-AzureRemoteAppSession
- Disconnect-AzureRemoteAppSession
- Invoke-AzureRemoteAppSessionLogoff
- Send-AzureRemoteAppSessionMessage
- Get-AzureRemoteAppProgram
- Get-AzureRemoteAppStartMenuProgram
- Publish-AzureRemoteAppProgram
- Unpublish-AzureRemoteAppProgram
- Get-AzureRemoteAppCollectionUsageDetails
- Get-AzureRemoteAppCollectionUsageSummary
- Get-AzureRemoteAppPlan

Cmdlets de red virtual de RemoteApp:

- New-AzureRemoteAppVNet
- Get-AzureRemoteAppVNet
- Set-AzureRemoteAppVNet
- Remove-AzureRemoteAppVNet
- Get-AzureRemoteAppVpnDevice
- Get-- AzureRemoteAppVpnDeviceConfigScript
- Reset-AzureRemoteAppVpnSharedKey

Cmdlets de imagen de plantilla de RemoteApp:

- New-AzureRemoteAppTemplateImage
- Get-AzureRemoteAppTemplateImage
- Rename-AzureRemoteAppTemplateImage
- Remove-AzureRemoteAppTemplateImage

Otros cmdlets de RemoteApp:

- Get-AzureRemoteAppLocation
- Get-AzureRemoteAppWorkspace
- Set-AzureRemoteAppWorkspace
- Get-AzureRemoteAppOperationResult
 

<!---HONumber=Oct15_HO3-->