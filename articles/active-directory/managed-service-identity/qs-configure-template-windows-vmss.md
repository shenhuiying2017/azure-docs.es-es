---
title: Configuración de MSI en un conjunto de escalado de máquinas virtuales de Azure mediante una plantilla
description: Instrucciones paso a paso para configurar Managed Service Identity (MSI) en un conjunto de escalado de máquinas virtuales de Azure, mediante una plantilla de Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: f7c5d063bfb287de9afe808395b951ecb161da69
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930620"
---
# <a name="configure-a-vmss-managed-service-identity-by-using-a-template"></a>Configuración de Managed Service Identity (MSI) en un VMSS mediante una plantilla

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Managed Service Identity proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, aprenderá a realizar las siguientes operaciones de Managed Service Identity en un VMSS de Azure mediante la plantilla de implementación de Azure Resource Manager:
- Habilitar y deshabilitar la identidad asignada por el sistema en un VMSS de Azure
- Agregar y quitar una identidad asignada por el usuario en un VMSS de Azure

## <a name="prerequisites"></a>requisitos previos

- Si no está familiarizado con Managed Service Identity, consulte la [sección de introducción](overview.md). **No olvide revisar la [diferencia entre una identidad asignada por el sistema y una asignada por el usuario](overview.md#how-does-it-work)**.
- Si aún no tiene una cuenta de Azure, [regístrese para una cuenta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="azure-resource-manager-templates"></a>Plantillas del Administrador de recursos de Azure

Al igual que con Azure Portal y los scripts, las plantillas de [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) proporcionan la capacidad de implementar recursos nuevos o modificados definidos por un grupo de recursos de Azure. Existen varias opciones para la edición e implementación de plantillas, tanto localmente como basadas en el portal, incluidas:

   - Usar una [plantilla personalizada de Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), que permite crear una plantilla desde cero o basada en una común existente o [plantilla de inicio rápido](https://azure.microsoft.com/documentation/templates/).
   - Derivar a partir de un grupo de recursos existente, exportando una plantilla de [la implementación original](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) o del [estado actual de la implementación](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Usar un [editor de JSON (por ejemplo, VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md) local y, a continuación, cargarla e implementarla con PowerShell o la CLI.
   - Usar el [proyecto del grupo de recursos de Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) de Visual Studio tanto para crear como para implementar una plantilla.  

Independientemente de la opción que elija, la sintaxis de la plantilla es la misma durante la implementación inicial y posteriores implementaciones. La habilitación de MSI en una máquina virtual nueva o existente se realiza de la misma manera. Además, de forma predeterminada, Azure Resource Manager realiza una [actualización incremental](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) en las implementaciones.

## <a name="system-assigned-identity"></a>Identidad asignada por el sistema

En esta sección, se habilita y deshabilita la identidad asignada por el sistema con una plantilla de Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>Habilitación de una identidad asignada por el sistema durante la creación de un VMSS de Azure o en uno existente

1. Cargue la plantilla en un editor, busque el recurso de interés `Microsoft.Compute/virtualMachineScaleSets` dentro de la sección `resources`. El suyo puede diferir ligeramente con respecto a la siguiente captura de pantalla, en función del editor que use y de si está editando una plantilla para una implementación nueva o una existente.
   
   ![Captura de pantalla de la plantilla - localizar máquina virtual](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. Para habilitar la identidad asignada por el sistema, agregue la propiedad `"identity"` en el mismo nivel que la propiedad `"type": "Microsoft.Compute/virtualMachineScaleSets"`. Use la sintaxis siguiente:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. (Opcional) Agregue la extensión de MSI del conjunto de escalado de máquinas virtuales como un elemento `extensionsProfile`. Este paso es opcional, ya que puede usar la identidad de Azure Instance Metadata Service (IMDS) para recuperar tokens.  Use la sintaxis siguiente:

   >[!NOTE] 
   > En el ejemplo siguiente se da por hecho que se está implementando una extensión (`ManagedIdentityExtensionForWindows`) de un conjunto de escalado de máquinas virtuales Windows. También puede configurarlo para Linux utilizando `ManagedIdentityExtensionForLinux` en su lugar, para los elementos `"name"` y `"type"`.
   >

   ```JSON
   "extensionProfile": {
        "extensions": [
            {
                "name": "MSIWindowsExtension",
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

4. Cuando haya terminado, la plantilla debería tener una apariencia similar a la siguiente:

   ![Captura de pantalla de la plantilla después de la actualización](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Deshabilitar una identidad asignada por el sistema de un conjunto de escalado de máquinas virtuales de Azure

> [!NOTE]
> Actualmente no se puede deshabilitar Managed Service Identity de una máquina virtual. Mientras tanto, puede cambiar entre el uso de identidades asignadas por el sistema y por el usuario.

Si tiene un conjunto de escalado de máquinas virtuales que ya no necesita una identidad asignada por el sistema, pero aún necesita identidades asignadas por el usuario:

- Cargue la plantilla en un editor y cambie el tipo de identidad a `'UserAssigned'`.

## <a name="user-assigned-identity"></a>Identidad asignada por el usuario

En esta sección, asignará una identidad asignada por el usuario a un VMSS de Azure mediante la plantilla de Azure Resource Manager.

> [!Note]
> Para crear una identidad asignada por el usuario mediante una plantilla de Azure Resource Manager, consulte [Create a user assigned identity](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity) (Creación de una identidad asignada por el usuario).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Asignación de una identidad asignada por el usuario a un VMSS de Azure

1. En el elemento `resources`, agregue la siguiente entrada para asignar una identidad asignada por el usuario al VMSS.  No olvide reemplazar `<USERASSIGNEDIDENTITY>` con el nombre de la identidad asignada por el usuario que ha creado.

    ```json
    {
        "name": "[variables('vmssName')]",
        "apiVersion": "2017-03-30",
        "location": "[parameters(Location')]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/<USERASSIGNEDIDENTITY>)']"
            ]
        }

    }
    ```
2. (Opcional) Agregue la siguiente entrada en el elemento `extensionProfile` para asignar la extensión de identidad administrada al VMSS. Este paso es opcional, ya que puede usar el punto de conexión de identidad de Azure Instance Metadata Service (IMDS) para recuperar tokens. Use la sintaxis siguiente:
   
    ```JSON
       "extensionProfile": {
            "extensions": [
                {
                    "name": "MSIWindowsExtension",
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
3.  Cuando haya terminado, la plantilla debería tener un aspecto similar al siguiente:
   
      ![Captura de pantalla de la identidad asignada por el usuario](./media/qs-configure-template-windows-vmss/qs-configure-template-windows-final.PNG)

## <a name="next-steps"></a>Pasos siguientes

- Para obtener una perspectiva más amplia sobre MSI, consulte la [Introducción a Managed Service Identity](overview.md).

