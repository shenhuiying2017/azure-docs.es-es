---
title: "Guía de inicio rápido: Creación de un registro privado de Docker en Azure con PowerShell"
description: "Aprenda rápidamente a crear un registro de contenedor privado de Docker con PowerShell."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2bae45955cf3c2b157acce2544b1f35fbddd0170
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="create-an-azure-container-registry-using-powershell"></a>Creación de una instancia de Azure Container Registry mediante PowerShell

Azure Container Registry es un servicio de registro de contenedores de Docker administrado usado para almacenar imágenes de contenedor de Docker privadas. En esta guía se detalla la creación de una instancia de Azure Container Registry con PowerShell, la inserción de una imagen del contenedor en el registro y, por último, la implementación del contenedor desde el registro en Azure Container Instances (ACI).

Para realizar los pasos de esta guía, se requiere la versión 3.6 del módulo Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

También debe tener instalado Docker localmente. Docker proporciona paquetes que permiten configurar Docker fácilmente en cualquier sistema [Mac][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

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

El nombre del registro debe ser único dentro de Azure y contener entre 5 y 50 caracteres alfanuméricos. En el ejemplo siguiente, se usa *myContainerRegistry007*. Actualice este valor a uno único.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>Inicio de sesión en ACR

Antes de insertar y extraer imágenes de contenedor, debe iniciar sesión en la instancia de ACR. En primer lugar, use el comando [Get AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) para obtener las credenciales de administrador para la instancia ACR.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Después, use el comando [docker login][docker-login] para iniciar sesión en la instancia de ACR.

```powershell
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

El comando devolverá `Login Succeeded` una vez completado. También puede ver una advertencia de seguridad que recomienda el uso del parámetro `--password-stdin`. Cuando su uso esté fuera del ámbito de este artículo, recomendamos seguir este procedimiento recomendado. Para más información, vea la referencia del comando [docker login][docker-login].

## <a name="push-image-to-acr"></a>Inserción de una imagen en ACR

Para insertar una imagen en Azure Container Registry, primero debe tener una imagen. Si es necesario, ejecute el siguiente comando para extraer una imagen creada anteriormente de Docker Hub.

```powershell
docker pull microsoft/aci-helloworld
```

La imagen debe estar etiquetada con el nombre del servidor de inicio de sesión de ACR. Use el comando [docker tag][docker-tag] para realizarlo. 

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
docker tag microsoft/aci-helloworld $image
```

Por último, use [docker push][docker-push] para insertar la imagen en ACR.

```powershell
docker push $image
```

## <a name="deploy-image-to-aci"></a>Implementación de la imagen en ACI
Para implementar la imagen como una instancia de contenedor en Azure Container Instances (ACI), primero debe convertir la credencial del registro en una instancia de PSCredential.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

Para implementar la imagen del contenedor desde el registro de contenedores con un 1 núcleo de CPU y 1 GB de memoria, ejecute el comando siguiente:

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name mycontainer -Image $image -Cpu 1 -MemoryInGB 1 -IpAddressType public -Port 80 -RegistryCredential $pscred
```

Debería recibir una respuesta inicial de Azure Resource Manager con los detalles del contenedor. Para supervisar el estado del contenedor y comprobar si se está ejecutando, repita el comando [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]. Debería tardar menos de un minuto.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

Salida de ejemplo: `Succeeded`

## <a name="view-the-application"></a>Visualización de la aplicación
Cuando la implementación en ACI se realice correctamente, puede recuperar la dirección IP pública del contenedor con el comando [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).IpAddress
```

Salida de ejemplo: `"13.72.74.222"`

Para ver la aplicación en ejecución, vaya a la dirección IP pública en su explorador favorito. Debe tener un aspecto similar al siguiente:

![Aplicación Hola mundo en el explorador][qs-portal-15]

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, puede usar el comando [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] para eliminar el grupo de recursos, Azure Container Registry y todas las instancias de Azure Container Instances.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una instancia de Azure Container Registry con la CLI de Azure e iniciado dicha instancia en Azure Container Instances. Siga el tutorial de Azure Container Instances para información más detallada de ACI.

> [!div class="nextstepaction"]
> [Tutorial de Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png
