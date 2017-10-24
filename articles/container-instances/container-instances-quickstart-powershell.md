---
title: "Guía de inicio rápido: Creación del primer contenedor de Azure Container Instances con PowerShell"
description: Empiece a trabajar con Azure Container Instances creando una instancia del contenedor de Windows con PowerShell.
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: fbd1bee04c5180beda23c04607b313eec9edcab4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Creación del primer contenedor en Azure Container Instances

Con Azure Container Instances es muy fácil crear y administrar contenedores de Docker en Azure sin tener que aprovisionar máquinas virtuales ni recurrir a un servicio de nivel superior.

En esta guía de inicio rápido crearemos un contenedor de Windows en Azure y lo expondremos en Internet con una dirección IP pública. Esta operación se completa en un solo comando. En pocos segundos verá lo siguiente en el explorador:

![Aplicación implementada mediante Azure Container Instances vista en el explorador][qs-powershell-01]

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Para realizar los pasos de esta guía, se requiere la versión 4.4 del módulo Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando `Login-AzureRmAccount` y siga las instrucciones de la pantalla.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Creación de un grupo de recursos

Cree un grupo de recursos de Azure con [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Crear un contenedor

Para crear un contenedor debe especificar un nombre, una imagen de Docker y un grupo de recursos de Azure para el cmdlet [New-AzureRmContainerGroup][New-AzureRmContainerGroup]. Dicho contenedor se puede exponer opcionalmente a Internet con una dirección IP pública. En este caso usaremos un contenedor de Nano Server de Windows en el que se ejecuta Internet Information Services (IIS).

```powershell
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
```

En pocos segundos obtendrá una respuesta a su solicitud. Inicialmente, el contenedor estará en un estado de **creación**, pero debería comenzar dentro de un minuto o dos. Puede comprobar el estado mediante el cmdlet [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] cmdlet:

```powershell
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

El estado de aprovisionamiento y la dirección IP del contenedor aparecen en la salida del cmdlet:

```
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/12345678-1234-1234-1234-12345678abcd/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            :
IpAddress                : 40.71.248.73
Ports                    : {80}
OsType                   : Windows
Volumes                  :
```

Una vez que el contenedor **ProvisioningState** pasa a tener el estado `Succeeded`, puede obtener acceso a él en el explorador con la dirección IP proporcionada.

![IIS implementado mediante Azure Container Instances visualizado en el explorador][qs-powershell-01]

## <a name="delete-the-container"></a>Eliminación del contenedor

Cuando haya terminado con el contenedor, puede eliminarlo con el cmdlet [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup]:

```powershell
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido ha iniciado un contenedor de Windows pregenerado en Azure Container Instances. Si quiere intentar compilar un contenedor usted mismo e implementarlo en Azure Container Instances mediante Azure Container Registry, vaya al tutorial de Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutoriales de Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png