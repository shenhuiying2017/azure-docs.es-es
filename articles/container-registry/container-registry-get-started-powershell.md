---
title: 'Guía de inicio rápido: Creación de un registro privado de Docker en Azure con PowerShell'
description: Aprenda rápidamente a crear un registro de contenedor privado de Docker en Azure con PowerShell.
services: container-registry
author: marsma
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 05/08/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 282cd4bc9256fc483014b53626c02106d0de236a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885126"
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>Inicio rápido: Creación de una instancia de Azure Container Registry mediante PowerShell

Azure Container Registry es un servicio privado y administrado de registro de contenedores de Docker, que se usa para compilar, almacenar y proporcionar imágenes de contenedor de Docker. Gracias a esta guía de inicio rápido, aprenderá a crear un registro de contenedor de Azure mediante PowerShell. Después de crear el registro, inserte una imagen de contenedor en él e implemente el contenedor del registro en Azure Container Instances (ACI).

## <a name="prerequisites"></a>requisitos previos

Para realizar los pasos de esta guía, se requiere la versión 5.7.0 o posterior del módulo Azure PowerShell. Ejecute `Get-Module -ListAvailable AzureRM` para determinar la versión instalada. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

También debe tener instalado Docker localmente. Docker ofrece paquetes para los sistemas [macOS][docker-mac], [Windows][docker-windows] y [Linux][docker-linux].

Dado que Azure Cloud Shell no incluye todos los componentes necesarios de Docker (como por ejemplo el demonio `dockerd`), no se puede usar Cloud Shell en este tutorial de inicio rápido.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en la suscripción de Azure con el comando [Connect-AzureRmAccount][Connect-AzureRmAccount] y siga las instrucciones de la pantalla.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Creación de un grupo de recursos

Una vez se haya autenticado con Azure, debe crear un grupo de recursos con [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Crear un registro de contenedor

A continuación, cree un registro de contenedor en el nuevo grupo de recursos mediante el comando [New-AzureRMContainerRegistry][New-AzureRMContainerRegistry].

El nombre del registro debe ser único dentro de Azure y contener entre 5 y 50 caracteres alfanuméricos. En el ejemplo siguiente se crea un registro con el nombre "myContainerRegistry007". Reemplace *myContainerRegistry007* en el siguiente comando y, a continuación, ejecútelo para crear el registro:

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-registry"></a>Iniciar sesión en el registro

Antes de insertar y extraer imágenes de contenedor, debe iniciar sesión en el registro. Use el comando [Get AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential] para obtener las credenciales de administrador para el registro:

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

A continuación, ejecute el [inicio de sesión de Docker][docker-login] para iniciar sesión:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

Se devolverá un inicio de sesión correcto `Login Succeeded`:

```console
PS Azure:\> $creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
Login Succeeded
```

## <a name="push-image-to-registry"></a>Insertar la imagen en el registro

Ahora que ha iniciado sesión el registro, ya puede insertar imágenes de contenedor en él. Para obtener una imagen que se pueda insertar en el registro, extraiga la imagen pública [aci-helloworld][aci-helloworld-image] de Docker Hub. La imagen [aci-helloworld][aci-helloworld-github] es una pequeña aplicación de Node.js que actúa como una página HTML estática que muestra el logotipo de Azure Container Instances.

```powershell
docker pull microsoft/aci-helloworld
```

Al extraer la imagen, el resultado de salida debería tener este aspecto:

```console
PS Azure:\> docker pull microsoft/aci-helloworld
Using default tag: latest
latest: Pulling from microsoft/aci-helloworld
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
3be0618266da: Pull complete
9e232827e52f: Pull complete
b53c152f538f: Pull complete
Digest: sha256:a3b2eb140e6881ca2c4df4d9c97bedda7468a5c17240d7c5d30a32850a2bc573
Status: Downloaded newer image for microsoft/aci-helloworld:latest
```

Para poder insertar una imagen en el registro de contenedor de Azure, debe etiquetarla con el nombre de dominio completo (FQDN) del registro. El nombre de dominio completo de los registros de contenedor de Azure tienen el siguiente formato: *\<registry-name\>.azurecr.io*.

Rellene una variable con la etiqueta de la imagen completa. Incluya el servidor de inicio de sesión, el nombre del repositorio ("aci-helloworld") y la versión de la imagen ("v1"):

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
```

A continuación, etiquete la imagen con la [etiqueta de Docker][docker-tag]:

```powershell
docker tag microsoft/aci-helloworld $image
```

Finalmente, [insértela con Docker][docker-push] en el registro:

```powershell
docker push $image
```

A medida que el cliente de Docker inserta la imagen, el resultado de salida debería tener este aspecto:

```console
PS Azure:\> docker push $image
The push refers to repository [myContainerRegistry007.azurecr.io/aci-helloworld]
31ba1ebd9cf5: Pushed
cd07853fe8be: Pushed
73f25249687f: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576
```

Felicidades. Ya ha insertado su primera imagen de contenedor en el registro.

## <a name="deploy-image-to-aci"></a>Implementación de la imagen en ACI

Con la imagen ahora en el registro, implemente un contenedor directamente en Azure Container Instances para ver cómo se ejecuta en Azure.

En primer lugar, convierta las credenciales del registro en un valor *PSCredential*. El comando `New-AzureRmContainerGroup`, que se usa para crear la instancia del contenedor, debe tener este formato.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

Además, la etiqueta del nombre DNS para el contenedor debe ser única en la región de Azure donde la creó. Ejecute el comando siguiente para rellenar una variable con un nombre generado:

```powershell
$dnsname = "aci-demo-" + (Get-Random -Maximum 9999)
```

Por último, ejecute [New-AzureRmContainerGroup][New-AzureRmContainerGroup] para implementar un contenedor de la imagen en el registro con 1 núcleo de CPU y 1 GB de memoria:

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
```

Debería recibir una respuesta inicial de Azure con los detalles del contenedor, y el estado "Pendiente":

```console
PS Azure:\> New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials : {myContainerRegistry007}
RestartPolicy            : Always
IpAddress                : 40.117.255.198
DnsNameLabel             : aci-demo-8751
Fqdn                     : aci-demo-8751.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Linux
Volumes                  :
State                    : Pending
Events                   : {}
```

Para supervisar su estado y determinar cuándo se está ejecutando, ejecute el comando [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] varias veces. El contenedor se iniciará en menos de un minuto.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

En este caso, puede ver que el estado de aprovisionamiento del contenedor está establecido en *Crear* y, a continuación, se cambia al estado *Correcto* una vez está en funcionamiento:

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Creating
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Succeeded
```

## <a name="view-running-application"></a>Ver la aplicación en ejecución

Una vez que se ha realizado correctamente la implementación ACI y el contenedor está en funcionamiento, vaya al nombre de dominio completo (FQDN) en el explorador para ver la aplicación que se ejecuta en Azure.

Obtenga el FQDN para el contenedor con [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:


```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
```

El resultado del comando es el FQDN de la instancia del contenedor:

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
aci-demo-8571.eastus.azurecontainer.io
```

Con el FQDN a mano, vaya al lugar donde esté en el explorador:

![Aplicación Hola mundo en el explorador][qs-psh-01-running-app]

Felicidades. Ya tiene un contenedor que se ejecuta en una aplicación en Azure, y que está implementado directamente desde una imagen de contenedor del registro de contenedores privado de Azure.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado de trabajar con los recursos que creó gracias a este tutorial, use el comando [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] para quitar el grupo de recursos, el registro de contenedor y la instancia del contenedor:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una instancia de Azure Container Registry con la CLI de Azure e iniciado dicha instancia en Azure Container Instances. Siga el tutorial de Azure Container Instances para información más detallada de ACI.

> [!div class="nextstepaction"]
> [Tutorial de Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[aci-helloworld-github]: https://github.com/Azure-Samples/aci-helloworld
[aci-helloworld-image]: https://hub.docker.com/r/microsoft/aci-helloworld/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[New-AzureRMContainerRegistry]: /powershell/module/containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-psh-01-running-app]: ./media/container-registry-get-started-powershell/qs-psh-01-running-app.png
