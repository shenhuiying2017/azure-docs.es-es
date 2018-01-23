---
title: "Preguntas más frecuentes y problemas conocidos de Managed Service Identity (MSI) para Azure Active Directory"
description: Problemas conocidos con Managed Service Identity para Azure Active Directory.
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/12/2017
ms.author: bryanla
ms.openlocfilehash: 2c29e93c0978250281fa489e53dcdf25f890a0a7
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Preguntas más frecuentes y problemas conocidos de Managed Service Identity (MSI) para Azure Active Directory

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Preguntas más frecuentes (P+F)

### <a name="is-there-a-private-preview-available-for-additional-features"></a>¿Existe una vista versión preliminar disponible de las características adicionales?

Sí. Si desea que se le tenga en cuenta para la inscripción en la versión preliminar privada, [visite nuestra página de registro](https://aka.ms/azuremsiprivatepreview).

### <a name="does-msi-work-with-azure-cloud-services"></a>¿Funciona MSI con Azure Cloud Services?

No hay planes para admitir MSI en Azure Cloud Services.

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>¿Funciona MSI con la Biblioteca de autenticación de Active Directory (ADAL) o la Biblioteca de autenticación de Microsoft (MSAL)?

No, MSI no está integrado aún con ADAL ni con MSAL. Para obtener más información acerca de cómo obtener un token de MSI mediante el punto de conexión de REST de MSI, consulte [Uso de una identidad de servicio administrada de máquina virtual de Azure para obtener tokens](msi-how-to-use-vm-msi-token.md).

### <a name="what-are-the-supported-linux-distributions"></a>¿Qué distribuciones de Linux son compatibles?

Las siguientes distribuciones de Linux son compatibles con MSI: 

- CoreOS Stable
- CentOS 7.1
- RedHat 7.2
- Ubuntu 15.04

Actualmente, otras distribuciones de Linux no son compatibles, por lo que existe la posibilidad de que la extensión produzca errores en ellas.

La extensión funciona en CentOS 6.9. Sin embargo, dada a la falta de compatibilidad el sistema en la 6.9, si la extensión se bloquea o se detiene, no se reiniciará automáticamente. Se reinicia cuando se reinicia la máquina virtual. Para reiniciar la extensión manualmente, consulte [¿Cómo se reinicia la extensión MSI?](#how-do-you-restart-the-msi-extension)

### <a name="how-do-you-restart-the-msi-extension"></a>¿Cómo se reinicia la extensión MSI?
Tanto en Windows como en algunas versiones de Linux, si la extensión se detiene, se puede utilizar el siguiente cmdlet para reiniciarla manualmente:

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Donde: 
- El nombre y tipo de la extensión para Windows es: ManagedIdentityExtensionForWindows
- El nombre y tipo de la extensión para Linux es: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Problemas conocidos

### <a name="automation-script-fails-when-attempting-schema-export-for-msi-extension"></a>Se produce un error en el "Script de automatización" al intentar la exportación del esquema de la extensión MSI

Cuando se habilita Managed Service Identity en una máquina virtual, se muestra el siguiente error al intentar usar la característica de "Script de automatización" para la máquina virtual o su grupo de recursos:

![Error de exportación de script de automatización de MSI](media/msi-known-issues/automation-script-export-error.png)

La extensión Managed Service Identity de la máquina virtual no admite actualmente la posibilidad de exportar su esquema a una plantilla de grupo de recursos. Como resultado, la plantilla generada no muestra los parámetros de configuración para habilitar Managed Service Identity en el recurso. Estas secciones pueden agregarse manualmente siguiendo los ejemplos de [Configuración de Managed Service Identity de una máquina virtual mediante una plantilla](msi-qs-configure-template-windows-vm.md).

Cuando la funcionalidad de exportación de esquema está disponible para la extensión MSI de la máquina virtual, se enumerará en [Exportación de grupos de recursos que contienen extensiones de máquina virtual](../virtual-machines/windows/extensions-export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>La hoja de configuración no aparece en Azure Portal

Si la hoja de configuración de la máquina virtual no aparece en su máquina virtual, MSI todavía no se ha habilitado en el portal de su región.  Vuelva a comprobarlo más tarde.  También puede habilitar MSI para la máquina virtual con [PowerShell](msi-qs-configure-powershell-windows-vm.md) o la [CLI de Azure](msi-qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>No se puede asignar el acceso a las máquinas virtuales en la hoja Control de acceso (IAM)

Si **Virtual Machine** no aparece en Azure Portal como una opción para **Asignar acceso a** en **Control de acceso (IAM)** > **Agregar permisos**, Managed Service Identity todavía no se ha habilitado en el portal en su región. Vuelva a comprobarlo más tarde.  Aún puede seleccionar la identidad de servicio administrada para la asignación de roles; para ello, busque la entidad de servicio de MSI.  Escriba el nombre de la máquina virtual en el campo **Seleccionar** y la entidad de servicio aparece en el resultado de la búsqueda.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>La máquina virtual no puede iniciarse después de moverse del grupo de recursos o de la suscripción

Si mueve una máquina virtual en estado de ejecución, continúa ejecutándose durante el desplazamiento. Sin embargo, después, si la máquina virtual se detiene y se reinicia, no se podrá iniciar. Este problema se produce porque la máquina virtual no está actualizando la referencia a la identidad MSI y continúa apuntando a ella en el grupo de recursos anterior.

**Solución alternativa** 
 
Desencadene una actualización en la máquina virtual para que pueda obtener los valores correctos para la identidad MSI. Puede hacer un cambio de propiedad de máquina virtual para actualizar la referencia a la identidad MSI. Por ejemplo, puede establecer un nuevo valor de etiqueta en la máquina virtual con el siguiente comando:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Este comando establece una nueva etiqueta "fixVM" con un valor de 1 en la máquina virtual. 
 
Al establecer esta propiedad, la máquina virtual se actualiza con el URI del recurso correcto de MSI y, a continuación, debería poder iniciar la máquina virtual. 
 
Una vez que se inicia la máquina virtual, la etiqueta puede quitarse con el comando siguiente:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```
