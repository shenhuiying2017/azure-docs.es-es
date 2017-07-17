---
title: "Características y extensiones de las máquinas virtuales para Linux | Microsoft Docs"
description: "Obtenga información acerca de qué extensiones están disponibles para máquinas virtuales de Azure, agrupadas por lo que proporcionan o mejoran."
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 77f50a830c38b3a90110845e58ee7cbb28742c10
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017

---

<a id="virtual-machine-extensions-and-features-for-linux" class="xliff"></a>

# Características y extensiones de las máquinas virtuales para Linux

Las extensiones de máquina virtual de Azure son pequeñas aplicaciones que realizan tareas de automatización y configuración posterior a la implementación en máquinas virtuales de Azure. Por ejemplo, si una máquina virtual necesita que se instale software, protección antivirus o configuración de Docker, se puede usar una extensión de máquina virtual para completar estas tareas. Las extensiones de máquina virtual de Azure se pueden ejecutar con la CLI de Azure, PowerShell, plantillas de Azure Resource Manager y Azure Portal. Las extensiones pueden empaquetar con una nueva implementación de máquina virtual o se pueden ejecutar en cualquier sistema existente.

Este documento proporciona información general sobre las extensiones de máquina virtual, requisitos previos para usar las extensiones de máquina virtual de Azure e instrucciones sobre cómo detectar, administrar y quitar extensiones de máquina virtual. Este documento proporciona información generalizada porque muchas extensiones de máquina virtual están disponibles, cada una con una configuración potencialmente única. En cada documento específico de la extensión individual pueden encontrarse detalles específicos de extensión.

<a id="use-cases-and-samples" class="xliff"></a>

## Casos de uso y ejemplos

Varias extensiones de máquina virtual de Azure diferentes están disponibles, cada una con un caso de uso específico. A continuación, se indican algunos ejemplos:

- Aplique configuraciones de estado deseado de PowerShell a una máquina virtual mediante la extensión DSC para Linux. Para obtener más información, consulte la sección sobre la [extensión de configuración de estado deseado de Azure](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Configure la supervisión de una máquina virtual con la extensión de máquina virtual de Microsoft Monitoring Agent. Para obtener más información, consulte [Habilitación o deshabilitación de la supervisión de máquinas virtuales](vm-monitoring.md).
- Configure la supervisión de su infraestructura de Azure con la extensión de Datadog. Para obtener más información, consulte el [blog de Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- Configure un host de Docker en una máquina virtual de Azure mediante la extensión de máquina virtual de Docker. Para obtener más información, consulte [Extensión de máquina virtual de Docker](dockerextension.md).

Además de las extensiones específicas de proceso, una extensión de script personalizado está disponible tanto para máquinas virtuales Windows como para máquinas virtuales Linux. La extensión de script personalizado para Linux permite que se ejecute cualquier script de Bash en una máquina virtual. Los scripts personalizados resultan útiles para diseñar implementaciones de Azure que requieren una configuración más allá de lo que las herramientas de Azure nativas pueden proporcionar. Para obtener más información, consulte la sección sobre la [extensión de script personalizado de máquina virtual Linux](extensions-customscript.md).

Para trabajar a través de un ejemplo en el cual se usa una extensión de máquina virtual en una implementación completa de la aplicación, consulte [Automatización de implementaciones de aplicaciones en Azure Virtual Machines](../linux/dotnet-core-1-landing.md).

<a id="prerequisites" class="xliff"></a>

## Requisitos previos

Cada extensión de máquina virtual puede tener su propio conjunto de requisitos previos. Por ejemplo, la extensión de máquina virtual de Docker tiene un requisito previo de una distribución de Linux compatible. En la documentación específica de extensión se detallan los requisitos de extensiones individuales.

<a id="azure-vm-agent" class="xliff"></a>

### Agente de máquina virtual de Azure

El agente de máquina virtual de Azure administra las interacciones entre una máquina virtual de Azure y el controlador de tejido de Azure. El agente de máquina virtual es responsable de muchos aspectos funcionales de la implementación y administración de máquinas virtuales de Azure, incluida la ejecución de extensiones de máquina virtual. El agente de máquina virtual de Azure está preinstalado en imágenes de Azure Marketplace y se puede instalar manualmente en sistemas operativos compatibles.

Para obtener información sobre las instrucciones de instalación y los sistemas operativos compatibles, consulte [Agente de máquina virtual de Azure](../windows/classic/agents-and-extensions.md).

<a id="discover-vm-extensions" class="xliff"></a>

## Detección de extensiones de máquina virtual

Hay muchas extensiones de máquina virtual diferentes disponibles para su uso con máquinas virtuales de Azure. Para ver una lista completa, ejecute el comando siguiente con la CLI de Azure y reemplace la ubicación de ejemplo por la ubicación elegida.

```azurecli
az vm extension image list --location westus -o table
```

<a id="run-vm-extensions" class="xliff"></a>

## Ejecución de extensiones de máquina virtual

Las extensiones de máquina virtual de Azure pueden ejecutarse en máquinas virtuales existentes, lo que resulta útil si es preciso realizar cambios de configuración o recuperar la conectividad en una máquina virtual ya implementada. Las extensiones de máquina virtual también pueden agruparse con las implementaciones de plantilla de Azure Resource Manager. Al usar las extensiones con plantillas de Resource Manager, las máquinas virtuales de Azure se pueden implementar y configurar sin intervención alguna después de la implementación.

Los siguientes métodos pueden usarse para ejecutar una extensión en una máquina virtual existente.

<a id="azure-cli" class="xliff"></a>

### Azure CLI

Las extensiones de máquina virtual de Azure se pueden ejecutar en una máquina virtual existente mediante el comando `az vm extension set`. En este ejemplo se ejecuta la extensión de script personalizado en una máquina virtual.

```azurecli
az vm extension set `
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

El script genera una salida similar al texto siguiente:

```azurecli
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

<a id="azure-portal" class="xliff"></a>

### Portal de Azure

Las extensiones de máquina virtual se pueden aplicar a una máquina virtual existente a través de Azure Portal. Para ello, seleccione la máquina virtual, elija **Extensiones** y haga clic en **Agregar**. Seleccione la extensión que quiera en la lista de extensiones disponibles y siga las instrucciones del asistente.

En la siguiente imagen se muestra la instalación de la extensión de script personalizado de Linux desde Azure Portal.

![Instalar la extensión de script personalizado](./media/extensions-features/installscriptextensionlinux.png)

<a id="azure-resource-manager-templates" class="xliff"></a>

### Plantillas del Administrador de recursos de Azure

Las extensiones de máquina virtual se pueden agregar a una plantilla de Azure Resource Manager y ejecutar con la implementación de la plantilla. Al implementar una extensión con una plantilla, puede crear implementaciones de Azure completamente configuradas. Por ejemplo, el siguiente JSON procede de una plantilla de Resource Manager. La plantilla implementa un conjunto de máquinas virtuales de carga equilibrada y una base de datos de Azure SQL Database y, después, instala una aplicación de .NET Core en cada máquina virtual. La extensión de máquina virtual se encarga de la instalación de software.

Para obtener más información, consulte la [plantilla de Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux) completa.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Para obtener más información, consulte [Creación de plantillas de Azure Resource Manager con extensiones de máquina virtual Linux](../linux/extensions-authoring-templates.md).

<a id="secure-vm-extension-data" class="xliff"></a>

## Protección de datos de extensión de máquina virtual

Al ejecutar una extensión de máquina virtual, puede que sea necesario incluir información confidencial como credenciales, nombres de cuenta de almacenamiento y claves de acceso de cuenta de almacenamiento. Muchas extensiones de máquina virtual incluyen una configuración protegida que cifra datos y los descifra solo dentro de la máquina virtual de destino. Cada extensión tiene un esquema específico de configuración protegida que se detalla de forma individual en la documentación específica de extensión.

En el ejemplo siguiente se muestra una instancia de la extensión de script personalizado para Linux. Tenga en cuenta que el comando que se ejecutará incluye un conjunto de credenciales. En este ejemplo, el comando que se ejecutará no se cifrará.


```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Al moverse la propiedad **command to execute** a la configuración **protegida**, se protege la cadena de ejecución.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

<a id="troubleshoot-vm-extensions" class="xliff"></a>

## Solución de problemas de extensiones de máquina virtual

Cada extensión de máquina virtual puede tener unos pasos de solución de problemas específicos de la extensión. Por ejemplo, al usar la extensión de script personalizado, se pueden encontrar detalles de ejecución de script localmente en la máquina virtual donde se ejecutó la extensión. Todos los pasos de solución de problemas específicos de extensión aparecen detallados en la documentación específica de extensión.

Los pasos de solución de problemas siguientes se aplican a todas las extensiones de máquina virtual.

<a id="view-extension-status" class="xliff"></a>

### Consulta del estado de la extensión

Una vez que una extensión de máquina virtual se haya ejecutado en una máquina virtual, use el siguiente comando de la CLI de Azure para volver al estado de la extensión. Reemplace los nombres de parámetros de ejemplo por los suyos propios.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

La salida tendrá un aspecto similar al siguiente:

```azurecli
AutoUpgradeMinorVersion    Location    Name          ProvisioningState    Publisher                   ResourceGroup      TypeHandlerVersion  VirtualMachineExtensionType
-------------------------  ----------  ------------  -------------------  --------------------------  ---------------  --------------------  -----------------------------
True                       westus      customScript  Succeeded            Microsoft.Azure.Extensions  exttest                             2  customScript
```

El estado de ejecución de extensión también puede encontrarse en Azure Portal. Para ver el estado de una extensión, seleccione la máquina virtual, elija **Extensiones** y seleccione la extensión deseada.

<a id="rerun-a-vm-extension" class="xliff"></a>

### Repetición de ejecución de una extensión de máquina virtual

Puede haber casos en los que sea necesario volver a ejecutar una extensión de máquina virtual. Puede volver ejecutar la extensión si la quita y la vuelve a ejecutar con un método de ejecución de su elección. Para quitar una extensión, ejecute el siguiente comando con la CLI de Azure. Reemplace los nombres de parámetros de ejemplo por los suyos propios.

```azurecli
az vm extension delete --name customScript --resource-group myResourceGroup --vm-name myVM
```

Puede quitar una extensión mediante los siguientes pasos en Azure Portal:

1. Seleccione una máquina virtual.
2. Elija **Extensiones**.
3. Seleccione la extensión deseada.
4. Elija **Desinstalar**.

<a id="common-vm-extension-reference" class="xliff"></a>

## Referencia de extensión de máquina virtual común
| Nombre de la extensión | Descripción | Más información |
| --- | --- | --- |
| Extensión de script personalizado para Linux |Ejecución de scripts en una máquina virtual de Azure |[Extensión de script personalizado para Linux](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Extensión de Docker |Instale el demonio de Docker para admitir los comandos remotos de Docker. |[Extensión de máquina virtual de Docker](dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Extensión de acceso a máquina virtual |Repetición de obtención de acceso a una máquina virtual de Azure |[Extensión de acceso a máquina virtual](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Extensión de Diagnósticos de Azure |Administración de Diagnósticos de Azure |[Extensión de Diagnósticos de Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Extensión de acceso a máquina virtual de Azure |Administración de usuarios y credenciales |[Extensión de acceso a máquina virtual para Linux](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

