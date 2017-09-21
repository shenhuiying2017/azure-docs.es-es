---
title: "Configuración de MSI en una máquina virtual de Azure con una plantilla"
description: "Instrucciones paso a paso para configurar una identidad de servicio administrada (MSI) en una máquina virtual de Azure, mediante una plantilla de Azure Resource Manager."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 266458323ca54d9805aea12108faed79e69d30b0
ms.contentlocale: es-es
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-a-template"></a>Configuración de Managed Service Identity (MSI) en una máquina virtual con una plantilla

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Managed Service Identity proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, aprenderá a habilitar y quitar MSI para una máquina virtual Windows de Azure, mediante una plantilla de implementación de Azure Resource Manager.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Habilitación de MSI durante la creación de una máquina virtual de Azure o en una máquina virtual existente

Como con Azure Portal y los scripts, las plantillas de Azure Resource Manager proporcionan la capacidad de implementar recursos nuevos o modificados definidos con un grupo de recursos de Azure. Existen varias opciones para la edición e implementación de plantillas, tanto localmente como basados en web o en el portal. Algunas son:

   - Usar una [plantilla personalizada de Azure Marketplace](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), que permite crear una plantilla desde cero o basarla en una común existente en una [plantilla de inicio rápido](https://azure.microsoft.com/documentation/templates/).
   - Derivar a partir de un grupo de recursos existente, exportando una plantilla de [la implementación original](../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) o del [estado actual de la implementación](../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Usar una variable local del [editor de JSON (por ejemplo, Visual Studio Code)](../azure-resource-manager/resource-manager-create-first-template.md) y, a continuación, cargarla o implementarla con PowerShell o CLI.
   - Usar un [proyecto del grupo de recursos de Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) de Visual Studio tanto para crear como para implementar la plantilla.  

Independientemente de la forma que elija, la sintaxis de la plantilla es la misma durante la implementación inicial y la reimplementación, por lo que la habilitación de MSI en una máquina virtual nueva o existente se lleva a cabo de la misma manera. Asimismo, de forma predeterminada, Azure Resource Manager realiza una [actualización incremental](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) en las implementaciones:

1. Después de cargar la plantilla en un editor, busque el recurso de interés `Microsoft.Compute/virtualMachines` dentro de la sección `resources`. El suyo puede diferir ligeramente con respecto a esta captura de pantalla, según el editor que use y de si está editando una plantilla para una implementación nueva o una existente:

   >[!NOTE] 
   > En el paso 2 también se da por supuesto que las variables `vmName`, `storageAccountName` y `nicName` se definen en la plantilla.
   >

   ![Captura de pantalla de la plantilla antes: ubicar la máquina virtual](./media/msi-qs-configure-template-windows-vm/template-file-before.png) 

2. Agregue la propiedad `"identity"` en el mismo nivel que la propiedad `"type": "Microsoft.Compute/virtualMachines"` utilizando la sintaxis siguiente:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. A continuación, agregue la extensión MSI de máquina virtual como un elemento `resources` utilizando la sintaxis siguiente:

   >[!NOTE] 
   > En el ejemplo siguiente se da por hecho que se está implementando una extensión de máquina virtual Windows (`ManagedIdentityExtensionForWindows`). También puede configurarla para Linux mediante `ManagedIdentityExtensionForLinux` en su lugar.
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

4. Cuando haya terminado, la plantilla debería tener una apariencia similar a la del siguiente ejemplo:

   ![Captura de pantalla de la plantilla después](./media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Eliminación de MSI de una máquina virtual de Azure

Si tiene una máquina virtual que ya no necesita una MSI, basta con que quite los dos elementos que agregó en el ejemplo anterior: la propiedad `"identity"` de la máquina virtual y el recurso `"Microsoft.Compute/virtualMachines/extensions"`.

## <a name="related-content"></a>Contenido relacionado

- [Información general de Managed Service Identity](msi-overview.md)

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.


