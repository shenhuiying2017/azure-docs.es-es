---
title: "Ejecución de scripts personalizados en VM de Linux en Azure | Microsoft Docs"
description: "Automatización de tareas de configuración de máquinas virtuales Linux mediante la extensión de script personalizado"
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: nepeters
ms.openlocfilehash: 1dde64aac72c11ccfccf4fdb676279692befaadd
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="using-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Uso de la extensión de script personalizado de Azure con máquinas virtuales Linux
La extensión de script personalizado descarga y ejecuta scripts en máquinas virtuales de Azure. Esta extensión es útil para la configuración posterior a la implementación, la instalación de software o cualquier otra tarea de configuración o administración. Los scripts se pueden descargar desde Azure Storage u otra ubicación de Internet accesible, o se puede proporcionar a la extensión en tiempo de ejecución. La extensión de script personalizado se integra con las plantillas de Azure Resource Manager y también se puede ejecutar mediante la CLI de Azure, PowerShell, Azure Portal o la API de REST de máquina virtual de Azure.

En este documento se detalla cómo usar la extensión de script personalizado desde la CLI de Azure y una plantilla de Azure Resource Manager, y también detalla los pasos para solucionar problemas en los sistemas Linux.

## <a name="extension-configuration"></a>Configuración de la extensión
La configuración de la extensión de script personalizado especifica aspectos como la ubicación del script y el comando que se ejecutará. Esta configuración se puede almacenar en archivos de configuración o se puede especificar en la línea de comandos o en una plantilla de Azure Resource Manager. Los datos confidenciales se pueden almacenar en una configuración protegida, que se cifra y se descifra solo dentro de la máquina virtual. La configuración protegida es útil cuando el comando de ejecución incluye secretos tales como una contraseña.

### <a name="public-configuration"></a>Configuración pública
Esquema:

**Nota**: Los nombres de propiedad distinguen entre mayúsculas y minúsculas. Use los nombres tal y como se muestra a continuación para evitar problemas de implementación.

* **commandToExecute**: (necesario, cadena) script de punto de entrada que se va a ejecutar.
* **fileUris**: (opcional, matriz de cadenas) direcciones URL de los archivos que se van a descargar.
* **timestamp** (opcional, entero) use este campo solo para desencadenar una nueva ejecución del script; para ello, cambie el valor de este campo.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Configuración protegida
Esquema:

**Nota**: Los nombres de propiedad distinguen entre mayúsculas y minúsculas. Use los nombres tal y como se muestra a continuación para evitar problemas de implementación.

* **commandToExecute**: (opcional, cadena) script de punto de entrada que se va a ejecutar. Use este campo si el comando contiene secretos tales como contraseñas.
* **storageAccountName**: (opcional, string) nombre de la cuenta de almacenamiento. Si especifica credenciales de almacenamiento, todos los valores de fileUri deben ser direcciones URL de blobs de Azure.
* **storageAccountName**: (opcional, cadena) clave de acceso de la cuenta de almacenamiento.

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Azure CLI
Cuando use la CLI de Azure para ejecutar la extensión de script personalizado, cree uno o varios archivos de configuración que contengan como mínimo el URI de archivo y el comando de ejecución del script.

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

Opcionalmente, la configuración puede especificarse en el comando como una cadena con formato JSON. Esto permite especificar la configuración durante la ejecución sin un archivo de configuración independiente.

```azurecli
az vm extension set '
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/neilpeterson/test-extension/master/test.sh"],"commandToExecute": "./test.sh"}'
```

### <a name="azure-cli-examples"></a>Ejemplos de la CLI de Azure

**Ejemplo 1** : configuración pública con archivo de script.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/neilpeterson/test-extension/master/test.sh"],
  "commandToExecute": "./test.sh"
}
```

Comando de la CLI de Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

**Ejemplo 2** : configuración pública sin archivo de script.

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Comando de la CLI de Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

**Ejemplo 3** : se usa un archivo de configuración pública para especificar el URI del archivo de script y un archivo de configuración protegida para especificar el comando que se ejecutará.

Archivo de configuración pública:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"]
}
```

Archivo de configuración protegida:  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Comando de la CLI de Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json --protected-settings ./protected-config.json
```

## <a name="resource-manager-template"></a>Plantilla de Resource Manager
La extensión de script personalizado de Azure se puede ejecutar en tiempo de implementación de la máquina virtual mediante una plantilla de Resource Manager. Para ello, agregue código JSON con el formato correcto a la plantilla de implementación.

### <a name="resource-manager-examples"></a>Ejemplos de Resource Manager
**Ejemplo 1** : configuración pública.

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

**Ejemplo 2** : comando de ejecución en la configuración protegida.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
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
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Vea el ejemplo completo de Music Store de .NET Core: [Music Store Demo](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db)(Ejemplo de Music Store).

## <a name="troubleshooting"></a>Solución de problemas
Cuando la extensión de script personalizado se ejecuta, el script se crea o se descarga en un directorio similar al del ejemplo siguiente. La salida del comando se guarda también en este directorio, en los archivos `stdout` y `stderr`.

```bash
/var/lib/waagent/custom-script/download/0/
```

La extensión de script de Azure genera un registro, que se encuentra aquí.

```bash
/var/log/azure/custom-script/handler.log
```

El estado de ejecución de la extensión de script personalizado también se puede recuperar con la CLI de Azure.

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

La salida tendrá un aspecto similar al siguiente:

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre otras extensiones de script de máquina virtual, consulte [Introducción a la extensión de script de Azure para Linux](extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

