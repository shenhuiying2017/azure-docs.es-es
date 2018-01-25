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
ms.date: 12/01/2017
ms.author: bryanla
ms.openlocfilehash: 2e4da8cd02a1d07a3225a0c1fda4c60928dba8a4
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in"></a>Uso de una identidad de servicio administrada (MSI) en una máquina virtual de Azure para el inicio de sesión 

[!INCLUDE [preview-notice](../../includes/active-directory-msi-preview-notice.md)]  
En este artículo se proporcionan ejemplos de script de PowerShell y de la CLI para el inicio de sesión mediante una entidad de servicio de MSI, así como instrucciones sobre temas importantes como el tratamiento de errores.

## <a name="prerequisites"></a>requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Si tiene previsto usar los ejemplos de Azure PowerShell o de la CLI de Azure en este artículo, asegúrese de instalar la versión más reciente de [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) o de la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - En todo el script de ejemplo de este artículo se da por supuesto que el cliente de la línea de comandos se ejecuta en una máquina virtual con MSI habilitado. Use la característica "Conectar" de la máquina virtual en Azure Portal para conectarse a la máquina virtual de forma remota. Para más información sobre la habilitación de MSI en una máquina virtual, consulte [Configuración de Managed Service Identity (MSI) en una máquina virtual mediante Azure Portal](msi-qs-configure-portal-windows-vm.md) o uno de los artículos variantes (mediante PowerShell, la CLI, una plantilla o un SDK de Azure). 
> - Para evitar errores durante el acceso a los recursos, a la MSI de la máquina virtual se le debe dar al menos el acceso de "Lector" en el ámbito adecuado (la máquina virtual u otro superior) para permitir las operaciones de Azure Resource Manager en esta máquina. Consulte [Asignación de un acceso de Managed Service Identity (MSI) a un recurso mediante Azure Portal](msi-howto-assign-access-portal.md) para más detalles.

## <a name="overview"></a>Información general

Una MSI proporciona una [entidad de servicio](develop/active-directory-dev-glossary.md#service-principal-object), que se [crea tras la habilitación de MSI](msi-overview.md#how-does-it-work) en la máquina virtual. Se le puede dar a la entidad de servicio acceso a los recursos de Azure, y los clientes de script o de línea de comandos pueden usar esta entidad como identidad para iniciar sesión y acceder a los recursos. Tradicionalmente, para acceder a los recursos protegidos con su propia identidad, un cliente de script debía hacer lo siguiente:  

   - Registrarse y ser aceptado en Azure AD como una aplicación cliente confidencial/web
   - Iniciar sesión con su entidad de servicio mediante las credenciales de la aplicación (que probablemente estaban insertadas en el script)

Con MSI, el cliente de script ya no necesita hacer nada de esto, ya que puede iniciar sesión con la entidad de servicio de MSI. 

## <a name="azure-cli"></a>Azure CLI

El script siguiente muestra cómo:

1. Iniciar sesión en Azure AD con la entidad de servicio de MSI de la máquina virtual  
2. Llamar a Azure Resource Manager y obtener el identificador de entidad de servicio de la máquina virtual. La CLI se ocupa de administrar la adquisición y el uso del token automáticamente. Asegúrese de sustituir el nombre de máquina virtual por `<VM-NAME>`.  

   ```azurecli
   az login --msi
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The MSI service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

El script siguiente muestra cómo:

1. Adquirir un token de acceso de MSI para la máquina virtual  
2. Usar el token de acceso para iniciar sesión en Azure AD con la entidad de servicio de MSI correspondiente   
3. Llamar a un cmdlet de Azure Resource Manager para obtener información acerca de la máquina virtual PowerShell se encarga de administrar el uso del token automáticamente.  

   ```azurepowershell
   # Get an access token for the MSI
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                                 -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
   $content =$response.Content | ConvertFrom-Json
   $access_token = $content.access_token
   echo "The MSI access token is $access_token"

   # Use the access token to sign in under the MSI service principal. -AccountID can be any string to identify the session.
   Login-AzureRmAccount -AccessToken $access_token -AccountId "MSI@50342"

   # Call Azure Resource Manager to get the service principal ID for the VM's MSI. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The MSI service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Identificadores de recurso para los servicios de Azure

Consulte [Servicios de Azure que admiten la autenticación de Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) para ver una lista de recursos que admite Azure AD y que se han probado con la identidad de servicio administrada, y sus respectivos identificadores de recurso.

## <a name="error-handling-guidance"></a>Instrucciones de control de errores 

Respuestas como las siguientes pueden indicar que la MSI en la máquina virtual no se ha configurado correctamente:

- PowerShell: *Invoke-WebRequest: no se puede conectar con el servidor remoto*
- CLI: *MSI: No se pudo recuperar un token de "http://localhost:50342/oauth2/token" con un error de "HTTPConnectionPool(host='localhost", puerto=50342)* 

Si recibe uno de estos errores, vuelva a la máquina virtual de Azure en [Azure Portal](https://portal.azure.com) y:

- Vaya a la página **Configuración** y asegúrese de que "Identidad de servicio administrada" esté establecida en "Sí".
- Vaya a la página **Extensiones** y asegúrese de que MSI se haya implementado correctamente.

Si alguna de las opciones no es correcta, puede que tenga que volver a implementar la identidad de servicio administrada en el recurso nuevo o solucionar el error de implementación. Consulte [Configuración de la identidad de servicio administrada (MSI) de máquina virtual con Azure Portal](msi-qs-configure-portal-windows-vm.md) si necesita ayuda con la configuración de la máquina virtual.

## <a name="related-content"></a>Contenido relacionado

- Para habilitar la MSI en una máquina virtual de Azure, consulte [Configuración de una identidad de servicio administrada (MSI) en una máquina virtual con PowerShell](msi-qs-configure-powershell-windows-vm.md) o [Configuración de una identidad de servicio administrada (MSI) en una máquina virtual con la CLI de Azure](msi-qs-configure-cli-windows-vm.md)

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.








