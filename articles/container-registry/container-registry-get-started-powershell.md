---
title: "Guía de inicio rápido: Creación de un registro privado de Docker en Azure con PowerShell"
description: "Aprenda rápidamente a crear un registro de contenedor privado de Docker con PowerShell."
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2017
ms.author: nepeters
ms.openlocfilehash: b58b10e644e934cc38a6e0512ba7642ab8bf27c4
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2017
---
# <a name="create-an-azure-container-registry-using-powershell"></a>Creación de una instancia de Azure Container Registry mediante PowerShell

Azure Container Registry es un servicio de registro de contenedores de Docker administrado usado para almacenar imágenes de contenedor de Docker privadas. En esta guía se detalla la creación de una instancia de Azure Container Registry mediante PowerShell.

Para realizar los pasos de esta guía, se requiere la versión 3.6 del módulo Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

También debe tener instalado Docker localmente. Docker proporciona paquetes que permiten configurar Docker fácilmente en cualquier sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) o [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando `Login-AzureRmAccount` y siga las instrucciones de la pantalla.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Creación de un grupo de recursos

Cree un grupo de recursos de Azure con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container-registry"></a>Creación de un registro de contenedor

Cree una instancia de ACR mediante el comando [New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry).

El nombre del registro **debe ser único**. En el ejemplo siguiente, se usa *myContainerRegistry007*. Actualice este valor a uno único.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>Inicio de sesión en ACR

Antes de insertar y extraer imágenes de contenedor, debe iniciar sesión en la instancia de ACR. En primer lugar, use el comando [Get AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) para obtener las credenciales de administrador para la instancia ACR.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Después, use el comando [docker login](https://docs.docker.com/engine/reference/commandline/login/) para iniciar sesión en la instancia de ACR.

```bash
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

Al finalizar, el comando devuelve un mensaje que indica que el inicio de sesión se ha realizado correctamente.

## <a name="push-image-to-acr"></a>Inserción de una imagen en ACR

Para insertar una imagen en Azure Container Registry, primero debe tener una imagen. Si es necesario, ejecute el siguiente comando para extraer una imagen creada anteriormente de Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

La imagen debe estar etiquetada con el nombre del servidor de inicio de sesión de ACR. Ejecute el comando [Get-AzureRmContainerRegistry](/powershell/module/containerregistry/Get-AzureRmContainerRegistry) para devolver el nombre del servidor de inicio de sesión de la instancia de ACR.

```powershell
Get-AzureRmContainerRegistry | Select Loginserver
```

Etiquete la imagen mediante el comando [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). Reemplace *acrLoginServer* por el nombre del servidor de inicio de sesión de su instancia de ACR.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Por último, use el comando [docker push](https://docs.docker.com/engine/reference/commandline/push/) para insertar la imagen en la instancia de ACR. Reemplace *acrLoginServer* por el nombre del servidor de inicio de sesión de su instancia de ACR.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, puede usar el comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos, la instancia de ACR y todas las imágenes de contenedor.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, creará una instancia de Azure Container Registry con la CLI de Azure. Si quiere usar Azure Container Registry con Azure Container Instances, continúe con el tutorial de Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutorial de Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)