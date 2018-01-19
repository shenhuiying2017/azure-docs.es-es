---
title: "Guía de inicio rápido: Creación del primer contenedor de Azure Container Instances con PowerShell"
description: Empiece a trabajar con Azure Container Instances creando una instancia del contenedor de Windows con PowerShell.
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 01/02/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 18a342fed7e99e82082764d6f5a3429a3ce794b7
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Creación del primer contenedor en Azure Container Instances

Con Azure Container Instances es muy fácil crear y administrar contenedores de Docker en Azure sin tener que aprovisionar máquinas virtuales ni recurrir a un servicio de nivel superior.

En esta guía de inicio rápido crearemos un contenedor de Windows en Azure y lo expondremos en Internet con una dirección IP pública. Esta operación se completa en un solo comando. En unos instantes, puede ver la aplicación que se ejecuta en su explorador:

![Aplicación implementada mediante Azure Container Instances vista en el explorador][qs-powershell-01]

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell localmente, para este tutorial se requiere la versión 3.6 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos de Azure con [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Crear un contenedor

Para crear un contenedor debe especificar un nombre, una imagen de Docker y un grupo de recursos de Azure para el cmdlet [New-AzureRmContainerGroup][New-AzureRmContainerGroup]. Dicho contenedor se puede exponer opcionalmente a Internet con una dirección IP pública. En este caso usaremos un contenedor de Nano Server en el que se ejecuta Internet Information Services (IIS).

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
```

En pocos segundos, obtendrá una respuesta a su solicitud. Inicialmente, el contenedor está en el estado **Creando**, pero debería iniciarse en uno o dos minutos. Puede comprobar el estado mediante el cmdlet [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] cmdlet:

 ```azurepowershell-interactive
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

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>pasos siguientes

En esta guía de inicio rápido ha iniciado un contenedor de Windows pregenerado en Azure Container Instances. Si quiere intentar compilar un contenedor usted mismo e implementarlo en Azure Container Instances mediante Azure Container Registry, vaya al tutorial de Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutorial de Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
