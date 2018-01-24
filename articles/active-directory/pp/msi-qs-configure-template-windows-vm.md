---
title: "Configuración de una MSI asignada por el usuario para una máquina virtual de Azure mediante una plantilla de Azure"
description: "Instrucciones detalladas para configurar una identidad de servicio administrada (MSI) asignada por el usuario para una máquina virtual de Azure, mediante una plantilla de Azure Resource Manager."
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
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d97f0fa2d6c1c92aaa3d5c74dd6715de00d32438
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-template"></a>Configuración de una identidad de servicio administrada (MSI) asignada por el usuario para una máquina virtual, mediante una plantilla de Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

La identidad de servicio administrada proporciona a los servicios de Azure una identidad administrada en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, aprenderá a habilitar y quitar una MSI asignada por el usuario de una máquina virtual de Azure mediante una plantilla de implementación de Azure Resource Manager.

## <a name="prerequisites"></a>requisitos previos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Habilitación de MSI durante la creación de una máquina virtual de Azure o en una máquina virtual existente

Como con Azure Portal y los scripts, las plantillas de Azure Resource Manager proporcionan la capacidad de implementar recursos nuevos o modificados definidos con un grupo de recursos de Azure. Existen varias opciones para la edición e implementación de plantillas, tanto localmente como basadas en el portal, incluidas:

   - Usar una [plantilla personalizada de Azure Marketplace](~/articles/azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), que permite crear una plantilla desde cero o basada en una común existente o [plantilla de inicio rápido](https://azure.microsoft.com/documentation/templates/).
   - Derivar a partir de un grupo de recursos existente, exportando una plantilla de [la implementación original](~/articles/azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) o del [estado actual de la implementación](~/articles/azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Usar un [editor de JSON (por ejemplo, VS Code)](~/articles/azure-resource-manager/resource-manager-create-first-template.md) local y, a continuación, cargarla e implementarla con PowerShell o la CLI.
   - Usar el [proyecto del grupo de recursos de Azure](~/articles/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) de Visual Studio tanto para crear como para implementar una plantilla.  

Independientemente de la opción que elija, la sintaxis de la plantilla es la misma durante la implementación inicial y posteriores implementaciones. La creación y asignación de una MSI asignada por el usuario en una máquina virtual existente o nueva se realiza de la misma forma. Asimismo, de forma predeterminada, Azure Resource Manager realiza una [actualización incremental](~/articles/azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) en las implementaciones:

1. Independientemente de que inicie sesión localmente en Azure o mediante Azure Portal, use una cuenta que esté asociada a la suscripción de Azure que contiene la máquina virtual y la MSI. Asegúrese también de que la cuenta pertenece a un rol que le conceda permisos de escritura en la suscripción o los recursos (por ejemplo, el rol de "propietario").

2. Después de cargar la plantilla en un editor, busque el recurso de interés `Microsoft.Compute/virtualMachines` dentro de la sección `resources`. El suyo puede diferir ligeramente con respecto a la siguiente captura de pantalla, en función del editor que use y de si está editando una plantilla para una implementación nueva o una existente.

   >[!NOTE] 
   > En este ejemplo, se asumen que se han definido variables como `vmName`, `storageAccountName` y `nicName` en la plantilla.
   >

   ![Captura de pantalla de la plantilla - localizar máquina virtual](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Agregue la propiedad `"identity"` en el mismo nivel que la propiedad `"type": "Microsoft.Compute/virtualMachines"`. Use la sintaxis siguiente:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. A continuación, agregue la extensión MSI de la máquina virtual como un elemento `resources`. Use la sintaxis siguiente:

   >[!NOTE] 
   > En el ejemplo siguiente se da por hecho que se está implementando una extensión (`ManagedIdentityExtensionForWindows`) de máquina virtual Windows. También puede configurarlo para Linux utilizando `ManagedIdentityExtensionForLinux` en su lugar, para los elementos `"name"` y `"type"`.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.ManagedIdentity",
           "type": "ManagedIdentityExtensionForWindows",
           "typeHandlerVersion": "1.0",
           "autoUpgradeMinorVersion": true,
           "settings": {
               "port": 50342
           },
           "protectedSettings": {}
       }
   }
   ```

5. Cuando haya terminado, la plantilla debería tener una apariencia similar a la siguiente:

   ![Captura de pantalla de la plantilla después de la actualización](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Eliminación de MSI de una máquina virtual de Azure

Si tiene una máquina virtual que ya no necesita MSI:

1. Independientemente de que inicie sesión localmente en Azure o mediante Azure Portal, use una cuenta que esté asociada a la suscripción de Azure que contiene la máquina virtual. Asegúrese también de que la cuenta pertenece a un rol que le conceda permisos de escritura en la máquina virtual (por ejemplo, el rol de "Colaborador de la máquina virtual").

2. Quite los dos elementos que se agregaron en la sección anterior: la propiedad `"identity"` de la máquina virtual y el recurso `"Microsoft.Compute/virtualMachines/extensions"`.

## <a name="related-content"></a>Contenido relacionado

- Para obtener una perspectiva más amplia sobre MSI, consulte la [Introducción a Managed Service Identity](msi-overview.md).

