---
title: "Uso de una identidad de servicio administrada en una máquina virtual de Azure para el inicio de sesión"
description: "Instrucciones paso a paso y ejemplos de uso de una entidad de servicio de MSI en una máquina virtual de Azure para el acceso a los recursos y el inicio de sesión de cliente mediante script."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/05/2018
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c5f71d27a9e07cc6d6a260b809e91aaa2a50270c
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="sign-in-using-a-vm-user-assigned-managed-service-identity-msi"></a>Inicio de sesión con una Identidad de servicio administrada (MSI) asignada por el usuario de la máquina virtual

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)] En este artículo se proporcionan ejemplos de script de la CLI para el inicio de sesión mediante una entidad de servicio de MSI asignada por el usuario, así como instrucciones sobre temas importantes como el tratamiento de errores.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Si tiene previsto usar los ejemplos de la CLI de Azure de este artículo, asegúrese de instalar la versión más reciente de la [CLI 2.0 de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - En todo el script de ejemplo de este artículo se da por supuesto que el cliente de la línea de comandos se ejecuta en una máquina virtual con MSI habilitado. Use la característica "Conectar" de la máquina virtual en Azure Portal para conectarse a la máquina virtual de forma remota. Para más información sobre la habilitación de MSI en una máquina virtual, consulte [Configuración de Managed Service Identity (MSI) con una máquina virtual con la CLI de Azure](msi-qs-configure-cli-windows-vm.md) o uno de los artículos variantes (mediante PowerShell, la CLI, una plantilla o un SDK de Azure). 
> - Para evitar errores durante el acceso a los recursos y el inicio de sesión, a la MSI se le debe dar al menos el acceso de "Lector" en el ámbito adecuado (la máquina virtual u otro superior) para permitir las operaciones de Azure Resource Manager en la máquina. Consulte [Asignación de un acceso de Managed Service Identity (MSI) a un recurso mediante CLI de Azure](msi-howto-assign-access-cli.md) para obtener más información.

## <a name="overview"></a>Información general

Una MSI proporciona una [entidad de servicio](~/articles/active-directory/develop/active-directory-dev-glossary.md#service-principal-object), que se [crea tras la habilitación de MSI](msi-overview.md#how-does-it-work) en la máquina virtual. A la entidad de servicio se le puede dar acceso a los recursos de Azure y los clientes de script o de línea de comandos pueden usar esta entidad para iniciar sesión y acceder a los recursos. Tradicionalmente, para acceder a los recursos protegidos con su propia identidad, un cliente de script debía hacer lo siguiente:  

   - Registrarse y ser aceptado en Azure AD como una aplicación cliente confidencial/web
   - Iniciar sesión con su entidad de servicio mediante las credenciales de la aplicación (que probablemente estaban insertadas en el script)

Con MSI, el cliente de script ya no necesita hacer nada de esto, ya que puede iniciar sesión con la entidad de servicio de MSI. 

## <a name="azure-cli"></a>Azure CLI

El script siguiente muestra cómo:

1. Inicie sesión en Azure AD con la entidad de servicio de MSI asignada por el usuario.  
2. Llame a Azure Resource Manager y obtenga la ubicación de la región de Azure para una máquina virtual. La CLI se ocupa de administrar la adquisición y el uso del token automáticamente. Asegúrese de sustituir el nombre de máquina virtual por `<VM NAME>` y el identificador de recurso MSI asignado por el usuario para `<MSI ID>`. Se devuelve el identificador del recurso MSI en la propiedad `id` durante la creación de un archivo MSI asignado por el usuario. Consulte [Configure a user-assigned Managed Service Identity ](msi-qs-configure-cli-windows-vm.md) (Configuración de una identidad de servicio administrada (MSI) asignada por el usuario) para obtener ejemplos del comando `az identity create`.

    ```azurecli
    az login --msi –u <MSI ID>
   
    vmLocation=$(az resource list -n <VM NAME> --query [*].location --out tsv)
    echo The VM region location is $vmLocation
    ```

    Respuestas de ejemplo:
   
    ```bash
    user@vmLinux:~$ az login --msi -u /subscriptions/80c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName
    [
      {
        "environmentName": "AzureCloud",
        "id": "90c696ff-5efa-4909-a64d-z1b616f423bl",
        "isDefault": true,
        "name": "MSIResource-/subscriptions/90c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName@50342",
        "state": "Enabled",
        "tenantId": "933a8f0e-ec41-4e69-8ad8-971zc4b533ll",
        "user": {
          "name": "userAssignedIdentity",
          "type": "servicePrincipal"
        }
      }
    ]  
    user@vmLinux:~$ vmLocation=$(az resource list -n vmLinux --query [*].location --out tsv)
    user@vmLinux:~$ echo The VM region location is $vmLocation
    The VM region location is westcentralus
    ```

## <a name="resource-ids-for-azure-services"></a>Identificadores de recurso para los servicios de Azure

Consulte [Servicios de Azure que admiten la autenticación de Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) para ver una lista de recursos que admite Azure AD y que se han probado con la identidad de servicio administrada, y sus respectivos identificadores de recurso.

## <a name="error-handling-guidance"></a>Instrucciones de control de errores 

Las siguientes respuestas pueden indicar que la MSI no se ha configurado correctamente:

- CLI: *MSI: No se pudo recuperar un token de "http://localhost:50342/oauth2/token" con un error de "HTTPConnectionPool(host='localhost", puerto=50342)* 

Si recibe uno de estos errores, vuelva a la máquina virtual de Azure en [Azure Portal](https://portal.azure.com) y:

- Vaya a la página **Configuración** y asegúrese de que "Identidad de servicio administrada" esté establecida en "Sí".
- Vaya a la página **Extensiones** y asegúrese de que MSI se haya implementado correctamente.

Si alguna de las opciones no es correcta, puede que tenga que volver a asignar de nuevo la identidad de servicio administrada al recurso o solucionar el error de implementación. Consulte [Configuración de la identidad de servicio administrada (MSI) de máquina virtual con la CLI de Azure](msi-qs-configure-cli-windows-vm.md), si necesita ayuda con la configuración de la máquina virtual.

## <a name="next-steps"></a>pasos siguientes

- Para habilitar MSI en una máquina virtual de Azure, consulte [Configuración de la identidad de servicio administrada (MSI) de una máquina virtual con la CLI de Azure](msi-qs-configure-cli-windows-vm.md).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.








