---
title: Repositorios de Azure Container Registry | Microsoft Docs
description: "Uso de los repositorios de Azure Container Registry para imágenes de Docker"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: cristyg
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 1e5d5ea5b1ec121fe008abc48178b1d58f540ce1
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-powershell"></a>Creación de un registro de contenedor privado de Docker con Azure PowerShell
Use los comandos de la [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview) para crear un registro de contenedor y administrar su configuración desde un equipo Windows. También puede crear y administrar registros de contenedor mediante [Azure Portal](container-registry-get-started-portal.md), la [CLI de Azure](container-registry-get-started-azure-cli.md) o mediante programación con la [API de REST](https://go.microsoft.com/fwlink/p/?linkid=834376) de Container Registry.


* Para más información sobre el entorno y los conceptos, consulte [la información general](container-registry-intro.md)
* Para una lista de los cmdlets compatibles, consulte el artículo sobre los [Cmdlets de administración de Azure Container Registry](https://docs.microsoft.com/en-us/powershell/module/azurerm.containerregistry/).


## <a name="prerequisites"></a>Requisitos previos
* **Azure PowerShell**: para instalar y empezar a trabajar con PowerShell, consulte las [instrucciones de instalación](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps). Inicie sesión en la suscripción de Azure mediante la ejecución de `Login-AzureRMAccount`. Para más información, consulte el artículo de [introducción a Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/get-started-azurep).
* **Grupo de recursos**: cree un [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) antes de crear un registro de contenedor o utilice un grupo de recursos ya existente. Asegúrese de que el grupo de recursos está en una ubicación donde el servicio Container Registry está [disponible](https://azure.microsoft.com/regions/services/). Para crear un grupo de recursos mediante Azure PowerShell, consulte la [referencia de PowerShell](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps#create-a-resource-group).
* **Cuenta de almacenamiento** (opcional): cree una [cuenta de almacenamiento](../storage/common/storage-introduction.md) estándar de Azure para realizar copias del registro de contenedor en la misma ubicación. Si no especifica una cuenta de almacenamiento al crear un registro con `New-AzureRMContainerRegistry`, el comando creará una automáticamente. Para crear una cuenta de almacenamiento mediante PowerShell, consulte la [referencia de PowerShell](https://docs.microsoft.com/en-us/powershell/module/azure/new-azurestorageaccount). Premium Storage no se admite actualmente.
* **Entidad de servicio** (opcional): cuando crea un registro con PowerShell, el acceso a este no está configurado de forma predeterminada. Según sus necesidades, puede asignar una entidad de servicio de Azure Active Directory existente a un registro o crear y asignar una nueva. Como alternativa, puede habilitar la cuenta de usuario de administrador del registro. Consulte las secciones más adelante en este artículo. Para más información acerca del acceso al registro, consulte [Authenticate with the container registry](container-registry-authentication.md) (Autenticación con el registro de contenedor).

## <a name="create-a-container-registry"></a>Creación de un registro de contenedor
Ejecute el comando `New-AzureRMContainerRegistry` para crear un registro de contenedor.

> [!TIP]
> Cuando cree un registro, especifique un nombre de dominio de nivel superior único global que contenga solo letras y números. El nombre del registro en los ejemplos es `MyRegistry`, pero puede sustituirlo por un nombre único de su elección.
>
>

El siguiente comando utiliza los parámetros mínimos necesarios para crear el registro de contenedor `MyRegistry` en el grupo de recursos `MyResourceGroup` en la ubicación centro-sur de EE. UU.:

```PowerShell
$Registry = New-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

* `-StorageAccountName` es opcional. Si no se especifica, se crea una cuenta de almacenamiento con un nombre formado por el nombre del registro y una marca de tiempo del grupo de recursos especificado.

## <a name="assign-a-service-principal"></a>Asignación de una entidad de servicio
Use los comandos de PowerShell para asignar una [entidad de servicio](../azure-resource-manager/resource-group-authenticate-service-principal.md) de Azure Active Directory a un registro. A la entidad de servicio en estos ejemplos se le asigna el rol de propietario, pero puede asignar [otros roles](../active-directory/role-based-access-control-configure.md) si lo desea.

### <a name="create-a-service-principal"></a>Creación de una entidad de servicio
En el siguiente comando se crea una nueva entidad de servicio. Especifique una contraseña segura con el parámetro `-Password`.

```PowerShell
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName ApplicationDisplayName -Password "MyPassword"
```

### <a name="assign-a-new-or-existing-service-principal"></a>Asignación de una entidad de servicio nueva o existente
A un registro puede asignar una entidad de servicio nueva o existente. Para asignarle acceso de rol de propietario al registro, ejecute un comando similar al del ejemplo siguiente:

```PowerShell
New-AzureRMRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Registry.Id
```

##<a name="sign-in-to-the-registry-with-the-service-principal"></a>Inicie sesión en el registro con la entidad de servicio
Después de asignar la entidad de servicio al registro, puede iniciar sesión con el comando siguiente:

```PowerShell
docker login -u $ServicePrincipal.ApplicationId -p myPassword
```

## <a name="manage-admin-credentials"></a>Administración de las credenciales de administrador
Se crea automáticamente una cuenta de administrador para cada registro de contenedor, que estará deshabilitada de forma predeterminada. Los ejemplos siguientes muestran los comandos de PowerShell para administrar las credenciales de administrador del registro de contenedor.

### <a name="obtain-admin-user-credentials"></a>Obtención de las credenciales de administrador
```PowerShell
Get-AzureRMContainerRegistryCredential -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

### <a name="enable-admin-user-for-an-existing-registry"></a>Habilitación del usuario de administrador para un registro existente
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -EnableAdminUser
```

### <a name="disable-admin-user-for-an-existing-registry"></a>Deshabilitación del usuario de administrador para un registro existente
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -DisableAdminUser
```

## <a name="next-steps"></a>Pasos siguientes
* [Insertar la primera imagen mediante la CLI de Docker](container-registry-get-started-docker-cli.md)

