---
title: 'Guía de inicio rápido: Creación del primer contenedor de Azure Container Instances con PowerShell'
description: En esta guía de inicio rápido, se usa Azure PowerShell para implementar un contenedor Windows en Azure Container Instances.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 4a1d338304dbd5e2845768b7bf0273eed23af0ec
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Guía de inicio rápido: Creación del primer contenedor en Azure Container Instances

Con Azure Container Instances es muy fácil crear y administrar contenedores de Docker en Azure sin tener que aprovisionar máquinas virtuales ni recurrir a un servicio de nivel superior. En esta guía de inicio rápido, crearemos un contenedor Windows en Azure y lo expondremos en Internet con un nombre de dominio completo (FQDN). Esta operación se completa en un solo comando. En unos instantes, puede ver la aplicación que se ejecuta en su explorador:

![Aplicación implementada mediante Azure Container Instances vista en el explorador][qs-powershell-01]

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para este tutorial se requiere la versión 5.5 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzureRmAccount` para crear una conexión con Azure.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos de Azure con [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Crear un contenedor

Para crear un contenedor debe especificar un nombre, una imagen de Docker y un grupo de recursos de Azure para el cmdlet [New-AzureRmContainerGroup][New-AzureRmContainerGroup]. Este contenedor se puede exponer opcionalmente a Internet con una etiqueta de nombre DNS.

Ejecute el siguiente comando para iniciar un contenedor de Nano Server en el que se ejecuta Internet Information Services (IIS). El valor `-DnsNameLabel` debe ser único dentro de la región de Azure en la que crea la instancia, por lo que puede que tenga que modificar este valor para garantizar la exclusividad.

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

En pocos segundos, recibirá una respuesta a su solicitud. Inicialmente, el contenedor está en el estado **Creando**, pero debería iniciarse en uno o dos minutos. Puede comprobar el estado de la implementación mediante el cmdlet [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

El estado de aprovisionamiento, el nombre de dominio completo (FQDN) y la dirección IP del contenedor aparecen en la salida del cmdlet:

```console
PS Azure:\> Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

Cuando el contenedor **ProvisioningState** pasa a `Succeeded`, vaya a su `Fqdn` en el explorador:

![IIS implementado mediante Azure Container Instances visualizado en el explorador][qs-powershell-01]

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado con el contenedor, elimínelo con el cmdlet [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una instancia de contenedor de Azure a partir de una imagen en un repositorio público de Docker Hub. Si quiere compilar una imagen de contenedor por su cuenta e implementarla en Azure Container Instances desde un registro de contenedor privado de Azure, vaya al tutorial de Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutorial de Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
