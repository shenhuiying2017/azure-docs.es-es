---
title: Ejecución de scripts personalizados en VM de Linux en Azure | Microsoft Docs
description: Automatización de tareas de configuración de máquinas virtuales Linux mediante la extensión de script personalizado v2
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: danis
ms.openlocfilehash: 3977aa16878ea1e2d808376165f551ce5fb8d00f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944987"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Uso de la extensión de script personalizado de Azure versión 2 con máquinas virtuales Linux
La extensión de script personalizado versión 2 descarga y ejecuta scripts en máquinas virtuales de Azure. Esta extensión es útil para la configuración posterior a la implementación, la instalación de software o cualquier otra tarea de configuración o administración. Los scripts se pueden descargar desde Azure Storage u otra ubicación de Internet accesible, o se pueden proporcionar al tiempo de ejecución de la extensión. 

La extensión de script personalizado se integra con las plantillas de Azure Resource Manager. También puede ejecutarla mediante la CLI de Azure, PowerShell, Azure Portal o la API de REST de Azure Virtual Machines.

En este artículo se detalla cómo utilizar la extensión de script personalizado desde la CLI de Azure, y cómo ejecutar la extensión mediante una plantilla de Azure Resource Manager. En este artículo se proporcionan también los pasos para la solución de problemas para los sistemas Linux.


Hay dos extensiones de script personalizado para Linux:
* Versión 1: Microsoft.OSTCExtensions.CustomScriptForLinux
* Versión 2: Microsoft.Azure.Extensions.CustomScript

Cambie las implementaciones nuevas y existentes para que usen la nueva versión 2. La nueva versión está diseñada para que sea un sustituto directo. Por lo tanto, la migración es tan sencilla como cambiar el nombre y la versión; no necesita cambiar la configuración de la extensión.


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
* Si tiene un script que provocará un reinicio, instale las aplicaciones y ejecute los scripts, etc. Debe programar el reinicio de un trabajo de Cron o usar herramientas como DSC, o extensiones de Chef o Puppet.
* La extensión solo ejecutará un script una vez, si desea ejecutar un script en cada inicio, puede usar [cloud-init image](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/using-cloud-init) y usar un módulo [Scripts Per Boot](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot). Como alternativa, puede usar el script para crear una unidad de servicio de Systemd.
* Si desea programar cuándo se ejecutará un script, debe utilizar la extensión para crear un trabajo de Cron. 
* Cuando el script se esté ejecutando, solo verá un estado de extensión 'en transición' desde Azure Portal o la CLI. Si desea actualizaciones de estado más frecuentes de un script en ejecución, debe crear su propia solución.
* La extensión de script personalizada no admite de forma nativa servidores proxy, pero puede usar una herramienta de transferencia de archivos que admita servidores proxy en el script, como *Curl*. 
* Tenga en cuenta las ubicaciones de directorio no predeterminadas en las que se puedan basar los scripts o comandos y aplique lógica para controlarlas.



## <a name="extension-schema"></a>Esquema de extensión

La configuración de la extensión de script personalizado especifica aspectos como la ubicación del script y el comando que se ejecutará. Esta configuración se puede almacenar en archivos de configuración, o se puede especificar en la línea de comandos o en una plantilla de Azure Resource Manager. 

Los datos confidenciales se pueden almacenar en una configuración protegida, que se cifra y se descifra solo dentro de la máquina virtual. La configuración protegida es útil cuando el comando de ejecución incluye secretos tales como una contraseña.

Estos elementos se deben tratar como datos confidenciales y se deben especificar en la configuración protegida de las extensiones. Los datos de configuración protegida de la extensión de VM de Azure están cifrados y solo se descifran en la máquina virtual de destino.

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
      "skipDos2Unix":false,
      "timestamp":"123456789",          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."]  
    }
  }
}
```

### <a name="property-values"></a>Valores de propiedad

| NOMBRE | Valor / ejemplo | Tipo de datos | 
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | fecha |
| publisher | Microsoft.Compute.Extensions | string |
| Tipo | CustomScript | string |
| typeHandlerVersion | 2.0 | int |
| fileUris (p. ej.) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute (p. ej.) | python MyPythonScript.py <my-param1> | string |
| script | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | string |
| skipDos2Unix (p. ej.) | false | boolean |
| timestamp (p. ej.) | 123456789 | Entero de 32 bits |
| storageAccountName (p. ej.) | examplestorageacct | string |
| storageAccountKey (p. ej.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

### <a name="property-value-details"></a>Detalles del valor de propiedad
* `skipDos2Unix`: (opcional, booleano) omita la conversión dos2unix del script y las direcciones URL del archivo basado en script.
* `timestamp` (opcional, entero de 32 bits) use este campo solo para desencadenar una nueva ejecución del script; para ello, cambie el valor de este campo.  Se acepta cualquier valor entero; solo debe ser distinto del valor anterior.
 * `commandToExecute`: (**obligatorio** si hay script establecido, cadena) script de punto de entrada que se va a ejecutar. Use este campo si el comando contiene secretos tales como contraseñas.
* `script`: (**obligatorio** si no hay commandToExecute establecido, cadena) script codificado en Base64 (y opcionalmente como archivo) ejecutado mediante /bin/sh.
* `fileUris` (opcional, matriz de cadenas): direcciones URL de los archivos que se van a descargar.
* `storageAccountName` (opcional, cadena): nombre de la cuenta de almacenamiento. Si especifica credenciales de almacenamiento, todos los valores de `fileUris` deben ser direcciones URL de blobs de Azure.
* `storageAccountKey` (opcional, cadena): clave de acceso de la cuenta de almacenamiento.


Los valores siguientes se pueden establecer en la configuración pública o protegida. La extensión rechazará una configuración si los valores siguientes están establecidos en la configuración tanto pública como protegida.
* `commandToExecute`
* `script`
* `fileUris`

Usar la configuración pública puede resultar útil para la depuración, pero se recomienda encarecidamente usar la configuración protegida.

La configuración pública se envía en texto no cifrado a la máquina virtual donde se ejecutará el script.  La configuración protegida se cifra con una clave que solo conocen Azure y la máquina virtual. La configuración se guarda en la máquina virtual tal cual se envió; es decir, si la configuración estaba cifrada, se guarda cifrada en la máquina virtual. El certificado usado para descifrar los valores cifrados se almacena en la máquina virtual y se usa para descifrar la configuración (si es necesario) en tiempo de ejecución.

#### <a name="property-skipdos2unix"></a>Propiedad: skipDos2Unix

El valor predeterminado es false, lo que significa que la conversión dos2unix **sí** se ejecuta.

La versión anterior de CustomScript, Microsoft.OSTCExtensions.CustomScriptForLinux, convertiría automáticamente los archivos DOS en archivos UNIX mediante la traducción de `\r\n` en `\n`. Esta traducción todavía existe y está habilitada de manera predeterminada. Esta conversión se aplica a todos los archivos que se descargan de fileUris o la configuración del script según cualquiera de los criterios siguientes.

* Si la extensión es una de `.sh`, `.txt`, `.py` o `.pl`, se convertirá. La configuración del script siempre coincidirá con este criterio porque se supone que es un script que se ejecuta con /bin/sh y se guarda como script.sh en la máquina virtual.
* Si el archivo empieza con `#!`.

Se puede omitir la conversión dos2unix si skipDos2Unix se establece en true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Propiedad: script

CustomScript admite la ejecución de un script definido por el usuario. La configuración del script para combinar commandToExecute y fileUris en una sola configuración. En lugar de tener que configurar un archivo para descarga desde Azure Storage o GitHub GiST, simplemente codifique el script como una configuración. El script se puede usar para reemplazar commandToExecute y fileUris.

El script se **debe** codificar en Base64.  El script **se puede** comprimir mediante gzip. La configuración del script se puede usar en configuraciones públicas o protegidas. El tamaño máximo de los datos del parámetro del script es 256 KB. No se ejecutará el script si excede este tamaño.

Por ejemplo, supongamos que el script siguiente se guardó en el archivo /script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

La configuración correcta del script CustomScript se construiría tomando la salida del comando siguiente.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

De manera opcional, el script se puede comprimir mediante gzip para reducir más el tamaño (en la mayoría de los casos). (CustomScript detecta automáticamente el uso de la compresión gzip).

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript usa el algoritmo siguiente para ejecutar un script.

 1. Garantizar que la longitud del valor del script no excede los 256 KB.
 1. Descodificar el valor del script en Base64.
 1. _Intente_ ejecutar gunzip en el valor decodificado en Base64.
 1. Escriba el valor descodificado (y opcionalmente descomprimido) en el disco (/var/lib/waagent/custom-script/#/script.sh)
 1. Ejecute el script con _/bin/sh -c /var/lib/waagent/custom-script/#/script.sh.


## <a name="template-deployment"></a>Implementación de plantilla
Las extensiones de VM de Azure pueden implementarse con plantillas de Azure Resource Manager. El esquema JSON detallado en la sección anterior se puede usar en una plantilla de Azure Resource Manager para ejecutar la extensión de script personalizado durante la implementación de una plantilla de Azure Resource Manager. Aquí ([GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)) puede encontrar una plantilla de ejemplo que incluye la extensión de script personalizado.


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
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/MyPythonScript.py"
      ]  
    }
  }
}
```

>[!NOTE]
>Los nombres de propiedad distinguen entre mayúsculas y minúsculas. Para evitar problemas de implementación, use los nombres como se muestran aquí.

## <a name="azure-cli"></a>Azure CLI
Cuando se usa la CLI de Azure para ejecutar la extensión de script personalizado, cree un archivo o archivos de configuración. Como mínimo, debe tener "commandToExecute".

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Opcionalmente, la configuración puede especificarse en el comando como una cadena con formato JSON. Esto permite especificar la configuración durante la ejecución sin un archivo de configuración independiente.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Ejemplos de la CLI de Azure

#### <a name="public-configuration-with-script-file"></a>Configuración pública con archivo de script

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Comando de la CLI de Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Configuración pública sin archivo de script

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Comando de la CLI de Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Archivos de configuración pública y protegida

Use un archivo de configuración pública para especificar el URI del archivo de script. Use un archivo de configuración protegida para especificar el comando que se ejecutará.

Archivo de configuración pública:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Archivo de configuración protegida:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Comando de la CLI de Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>solución de problemas
Cuando la extensión de script personalizado se ejecuta, el script se crea o se descarga en un directorio similar al del ejemplo siguiente. La salida del comando se guarda también en este directorio, en los archivos `stdout` y `stderr`.

```bash
/var/lib/waagent/custom-script/download/0/
```

Para solucionar problemas, primero compruebe el registro del agente de Linux, asegúrese de que se ejecutó la extensión, consulte:

```bash
/var/log/waagent.log 
```

Debe buscar la ejecución de la extensión, tendrá un aspecto parecido al siguiente:
```text
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```
Algunos puntos a tener en cuenta:
1. Enable es cuando el comando empieza a ejecutarse.
2. Download se relaciona con la descarga del paquete de extensiones de CustomScript de Azure, no los archivos de script especificados en fileUris.


La extensión de script de Azure genera un registro, que se encuentra aquí:

```bash
/var/log/azure/custom-script/handler.log
```

Debe buscar la ejecución individual. Tendrá un aspecto parecido al siguiente:
```text
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
```
Aquí puede ver:
* El comando Enable que se inicia es este registro.
* La configuración que se pasó a la extensión.
* El archivo de descarga de la extensión y el resultado.
* El comando que se ejecuta y el resultado.

El estado de ejecución de la extensión de script personalizado también se puede recuperar con la CLI de Azure:

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
Para ver el código, los problemas actuales y las versiones, consulte el [repositorio Linux de extensiones de custom-script](https://github.com/Azure/custom-script-extension-linux).

