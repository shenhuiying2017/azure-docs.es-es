---
title: Extensión de script personalizado de Azure para Windows | Microsoft Docs
description: Automatización de tareas de configuración de máquinas virtuales Windows mediante la extensión de script personalizado
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/24/2018
ms.author: danis
ms.openlocfilehash: 34c16b686a50994862bef14cefec1a4799a343c4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944947"
---
# <a name="custom-script-extension-for-windows"></a>Extensión de la secuencia de comandos personalizada para Windows

La extensión de script personalizado descarga y ejecuta scripts en máquinas virtuales de Azure. Esta extensión es útil para la configuración posterior a la implementación, la instalación de software o cualquier otra tarea de configuración o administración. Los scripts se pueden descargar desde Azure Storage o GitHub, o se pueden proporcionar a Azure Portal en el tiempo de ejecución de la extensión. La extensión de script personalizado se integra con las plantillas de Azure Resource Manager y también se puede ejecutar mediante la CLI de Azure, PowerShell, Azure Portal o la API de REST de máquina virtual de Azure.

En este documento se detalla cómo usar la extensión de script personalizado mediante el módulo de Azure PowerShell y plantillas de Azure Resource Manager, y se detallan también los pasos para solucionar problemas en los sistemas Windows.

## <a name="prerequisites"></a>requisitos previos

> [!NOTE]  
> No use la extensión de script personalizado para ejecutar Update-AzureRmVM con la misma máquina virtual como su parámetro, ya tendrá que hacerlo ella misma.  
>   
> 

### <a name="operating-system"></a>Sistema operativo

La extensión de script personalizado para Linux se ejecutará en los sistemas operativos admitidos de la extensión. Para más información, vea este [artículo](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Ubicación del script

Puede emplear la extensión para usar las credenciales de Azure Blob Storage, a fin de tener acceso Azure Blob Storage. Como alternativa, la ubicación del script puede ser cualquier lugar, siempre y cuando la máquina virtual pueda enrutarse a ese punto de conexión, como GitHub, un servidor de archivos internos, etc.


### <a name="internet-connectivity"></a>Conectividad de Internet
Si necesita descargar un script externamente, como GitHub o Azure Storage, deben abrirse puertos adicionales de firewall/grupo de seguridad de red. Por ejemplo, si el script se encuentra en Azure Storage, puede permitir el acceso mediante las etiquetas del servicio NSG de Azure para [Storage](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags).

Si el script se encuentra en un servidor local, puede que aún necesite que haya abiertos puertos adicionales de firewall/grupo de seguridad de red.

### <a name="tips-and-tricks"></a>Trucos y sugerencias
* La mayor tasa de errores para esta extensión se debe a errores de sintaxis en el script. Compruebe que el script se ejecute sin errores y también establezca registros adicionales en el script para facilitar la búsqueda de dónde se produjo el error.
* Escriba scripts que sean idempotentes, para que, si se ejecutan más de una vez por accidente, no provoquen cambios en el sistema.
* Asegúrese de que los scripts no requieran intervención del usuario cuando se ejecutan.
* Los scripts tienen permitido un plazo de 90 minutos para ejecutarse; todo lo que dure más provocará un error de aprovisionamiento de la extensión.
* No coloque reinicios dentro del script, ya que esto provocará problemas con otras extensiones que se estén instalando y, tras reiniciar el equipo, la extensión no continuará. 
* Si tiene un script que provocará un reinicio, instale las aplicaciones y ejecute los scripts, etc. Debe programar el reinicio mediante una tarea programada de Windows o usar herramientas como DSC, o extensiones de Chef o Puppet.
* La extensión solo ejecutará un script una vez. Si quiere ejecutar un script en cada inicio, debe usar la extensión para crear una tarea programada de Windows.
* Si quiere programar cuándo se ejecutará un script, debe usar la extensión para crear una tarea programada de Windows. 
* Cuando el script se esté ejecutando, solo verá un estado de extensión "en transición" desde Azure Portal o la CLI. Si quiere recibir actualizaciones de estado más frecuentes de un script en ejecución, debe crear su propia solución.
* La extensión de script personalizada no admite de forma nativa servidores proxy, pero puede usar una herramienta de transferencia de archivos que admita servidores proxy en el script, como *Curl*. 
* Tenga en cuenta las ubicaciones de directorio no predeterminadas en las que se puedan basar los scripts o comandos y aplique lógica para controlarlas.


## <a name="extension-schema"></a>Esquema de extensión

La configuración de la extensión de script personalizado especifica aspectos como la ubicación del script y el comando que se ejecutará. Esta configuración se puede almacenar en archivos de configuración, o se puede especificar en la línea de comandos o en una plantilla de Azure Resource Manager. 

Los datos confidenciales se pueden almacenar en una configuración protegida, que se cifra y se descifra solo dentro de la máquina virtual. La configuración protegida es útil cuando el comando de ejecución incluye secretos tales como una contraseña.

Estos elementos se deben tratar como datos confidenciales y se deben especificar en la configuración protegida de las extensiones. Los datos de configuración protegida de la extensión de VM de Azure están cifrados y solo se descifran en la máquina virtual de destino.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```
**Nota**: Solo se puede instalar una versión de una extensión en una máquina virtual en un momento dado. Se producirá un error si especifica el script personalizado dos veces en la misma plantilla de Resource Manager para la misma máquina virtual. 

### <a name="property-values"></a>Valores de propiedad

| NOMBRE | Valor / ejemplo | Tipo de datos |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | fecha |
| publisher | Microsoft.Compute | string |
| Tipo | CustomScriptExtension | string |
| typeHandlerVersion | 1.9 | int |
| fileUris (p. ej.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| commandToExecute (p. ej.) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | string |
| storageAccountName (p. ej.) | examplestorageacct | string |
| storageAccountKey (p. ej.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

>[!NOTE]
>Los nombres de propiedad distinguen entre mayúsculas y minúsculas. Para evitar problemas de implementación, use los nombres como se muestran aquí.

#### <a name="property-value-details"></a>Detalles del valor de propiedad
 * `commandToExecute` (**necesario**, cadena): script de punto de entrada que se va a ejecutar. Use este campo si el comando contiene secretos, como contraseñas, o si los fileUris son confidenciales.
* `fileUris` (opcional, matriz de cadenas): direcciones URL de los archivos que se van a descargar.
* `storageAccountName` (opcional, cadena): nombre de la cuenta de almacenamiento. Si especifica credenciales de almacenamiento, todos los valores de `fileUris` deben ser direcciones URL de blobs de Azure.
* `storageAccountKey` (opcional, cadena): clave de acceso de la cuenta de almacenamiento.

Los valores siguientes se pueden establecer en la configuración pública o protegida. La extensión rechazará una configuración si los valores siguientes están establecidos en la configuración tanto pública como protegida.
* `commandToExecute`

Usar la configuración pública puede resultar útil para la depuración, pero se recomienda encarecidamente usar la configuración protegida.

La configuración pública se envía en texto no cifrado a la máquina virtual donde se ejecutará el script.  La configuración protegida se cifra con una clave que solo conocen Azure y la máquina virtual. La configuración se guarda en la máquina virtual tal cual se envió; es decir, si la configuración estaba cifrada, se guarda cifrada en la máquina virtual. El certificado usado para descifrar los valores cifrados se almacena en la máquina virtual y se usa para descifrar la configuración (si es necesario) en tiempo de ejecución.

## <a name="template-deployment"></a>Implementación de plantilla

Las extensiones de VM de Azure pueden implementarse con plantillas de Azure Resource Manager. El esquema JSON detallado en la sección anterior se puede usar en una plantilla de Azure Resource Manager para ejecutar la extensión de script personalizado durante la implementación de una plantilla de Azure Resource Manager. Aquí ([GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)) puede encontrar una plantilla de ejemplo que incluye la extensión de script personalizado.

## <a name="powershell-deployment"></a>Implementación de PowerShell

El comando `Set-AzureRmVMCustomScriptExtension` se puede usar para agregar la extensión de script personalizado a una máquina virtual existente. Para más información, consulte [Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```
## <a name="further-examples"></a>Otros ejemplos

### <a name="using-multiple-script"></a>Usar varios scripts
En este ejemplo, hay tres scripts que se usan para crear el servidor. "commandToExecute" llama al primer script. Después, dispone varias opciones para llamar a los demás. Por ejemplo, puede tener un script maestro que controla la ejecución, con el control de errores, la administración de estados y el registro correctos.

```powershell

$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$Settings = @{"fileUris" = $fileUri};

$storageaccname = "xxxxxxx"
$storagekey = "1234ABCD"
$ProtectedSettings = @{"storageAccountName" = $storageaccname; "storageAccountKey" = $storagekey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzureRmVMExtension -ResourceGroupName myRG `
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "buildserver1" ` 
    -Publisher "Microsoft.Compute" ` 
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" ` 
    -Settings $Settings ` 
    -ProtectedSettings $ProtectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Ejecutar scripts desde un recurso compartido local
En este ejemplo tal vez le interese usar un servidor SMB local para la ubicación de los scripts. Tenga en cuenta que no es necesario pasar otros valores de configuración, excepto *commandToExecute*.

```powershell
$ProtectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};
 
Set-AzureRmVMExtension -ResourceGroupName myRG 
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "serverUpdate" 
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $ProtectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Cómo ejecutar un script personalizado más de una vez con la CLI
Si quiere ejecutar la extensión de script personalizado más de una vez, solo puede hacerlo si se cumplen las condiciones siguientes:
1. El parámetro "Name" de la extensión coincide con la implementación anterior de la extensión.
2. Debe actualizar la configuración para que el comando se vuelva a ejecutar. Por ejemplo, podría agregar una propiedad dinámica al comando, como una marca de tiempo. 

## <a name="troubleshoot-and-support"></a>Solución de problemas y asistencia

### <a name="troubleshoot"></a>Solución de problemas

Los datos sobre el estado de las implementaciones de extensiones pueden recuperarse desde Azure Portal y mediante el módulo Azure PowerShell. Para ver el estado de implementación de las extensiones de una máquina virtual determinada, ejecute el comando siguiente:

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

La salida de la ejecución de las extensiones se registra en los archivos que se encuentran en el siguiente directorio de la máquina virtual de destino.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Los archivos especificados se descargan en el siguiente directorio de la máquina virtual de destino.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
donde `<n>` es un entero decimal que puede variar entre las ejecuciones de la extensión.  El valor `1.*` coincide con el valor `typeHandlerVersion` actual y real de la extensión.  Por ejemplo, el directorio real podría ser `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Cuando se ejecute el comando `commandToExecute`, la extensión establece este directorio (por ejemplo, `...\Downloads\2`) como directorio de trabajo actual. Esto permite el uso de rutas de acceso relativas para buscar los archivos descargados a través de la propiedad `fileURIs`. En la tabla siguiente se muestran algunos ejemplos.

Dado que la ruta de acceso absoluta de descarga puede variar con el paso del tiempo, es mejor optar por rutas de acceso relativas de archivo o script en la cadena `commandToExecute`, siempre que sea posible. Por ejemplo: 
```json
    "commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

La información de ruta de acceso después del primer segmento de URI se conserva para los archivos descargados a través de la lista de propiedades `fileUris`.  Como se muestra en la tabla siguiente, los archivos descargados se asignan en los subdirectorios de descarga para reflejar la estructura de los valores `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Ejemplos de archivos descargados

| URI en fileUris | Ubicación descargada relativa | Ubicación descargada absoluta * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* Como anteriormente, las rutas de acceso absolutas al directorio cambian a lo largo del ciclo de vida de la máquina virtual, pero no dentro de una ejecución única de la extensión CustomScript.

### <a name="support"></a>Soporte técnico

Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en los [foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione Obtener soporte. Para obtener información sobre el uso del soporte técnico, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](https://azure.microsoft.com/support/faq/).
