---
title: "Automatización de la implementación de aplicaciones con extensiones de máquina virtual | Microsoft Docs"
description: "Tutorial de DotNet Core para máquinas virtuales de Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 79c91304-6c1b-4354-a185-fecc129b139d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 2d1860a16405646452f6448c26bf995473ac04b3
ms.contentlocale: es-es
ms.lasthandoff: 03/31/2017


---
# <a name="application-deployment-with-azure-resource-manager-templates-for-windows-vms"></a>Implementación de aplicaciones con plantillas de Azure Resource Manager para máquinas virtuales Windows

Una vez identificados todos los requisitos de infraestructura de Azure y traducidos en una plantilla de implementación, debe abordar la implementación de aplicaciones reales. Por implementación de la aplicación, se entiende la instalación de los archivos binarios de aplicación reales en recursos de Azure. En el ejemplo de Music Store, es necesario instalar y configurar .NET Core e IIS en cada máquina virtual. Los archivos binarios de Music Store deben instalarse en la máquina virtual y la base de datos de Music Store debe crearse previamente.

En este documento se detalla cómo las extensiones de máquina virtual pueden automatizar la implementación y configuración de aplicaciones en máquinas virtuales de Azure. Se resaltan todas las dependencias y configuraciones únicas. Para obtener la mejor experiencia, realice una implementación previa de una instancia de la solución en su suscripción de Azure y trabaje con la plantilla de Azure Resource Manager. La plantilla completa se puede encontrar aquí: [Music Store Deployment on Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)(Implementación de Music Store en Windows).

## <a name="configuration-script"></a>Script de configuración
Las extensiones de máquina virtual son programas especializados que se ejecutan en máquinas virtuales para automatizar la configuración. Hay extensiones disponibles para muchos fines específicos, como antivirus, configuración de registro y configuración de Docker. Se puede usar una extensión de script personalizado para ejecutar cualquier script en una máquina virtual. En el ejemplo de Music Store, la configuración de las máquinas virtuales Windows y la instalación de la aplicación Music Store depende de la extensión de script personalizado.

Antes de detallar cómo se declaran las extensiones de máquina virtual en una plantilla de Azure Resource Manager, examine el script que se ejecuta. Este script configura la máquina virtual Windows para hospedar la aplicación Music Store. Cuando se ejecuta, el script instala todo el software necesario, instala la aplicación Music Store desde el control de código fuente y prepara la base de datos. 

> Este ejemplo es para fines de demostración.

```PowerShell
<#
    .SYNOPSIS
        Downloads and configures .Net Core Music Store application sample across IIS and Azure SQL DB.
#>

Param (
    [string]$user,
    [string]$password,
    [string]$sqlserver
)

# Firewall
netsh advfirewall firewall add rule name="http" dir=in action=allow protocol=TCP localport=80

# Folders
New-Item -ItemType Directory c:\temp
New-Item -ItemType Directory c:\music

# Install iis
Install-WindowsFeature web-server -IncludeManagementTools

# Install dot.net core sdk
Invoke-WebRequest http://go.microsoft.com/fwlink/?LinkID=615460 -outfile c:\temp\vc_redistx64.exe
Start-Process c:\temp\vc_redistx64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkID=809122 -outfile c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe
Start-Process c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkId=817246 -outfile c:\temp\DotNetCore.WindowsHosting.exe
Start-Process c:\temp\DotNetCore.WindowsHosting.exe -ArgumentList '/quiet' -Wait

# Download music app
Invoke-WebRequest  https://github.com/Microsoft/dotnet-core-sample-templates/raw/master/dotnet-core-music-windows/music-app/music-store-azure-demo-pub.zip -OutFile c:\temp\musicstore.zip
Expand-Archive C:\temp\musicstore.zip c:\music

# Azure SQL connection sting in environment variable
[Environment]::SetEnvironmentVariable("Data:DefaultConnection:ConnectionString", "Server=$sqlserver;Database=MusicStore;Integrated Security=False;User Id=$user;Password=$password;MultipleActiveResultSets=True;Connect Timeout=30",[EnvironmentVariableTarget]::Machine)

# Pre-create database
$env:Data:DefaultConnection:ConnectionString = "Server=$sqlserver;Database=MusicStore;Integrated Security=False;User Id=$user;Password=$password;MultipleActiveResultSets=True;Connect Timeout=30"
Start-Process 'C:\Program Files\dotnet\dotnet.exe' -ArgumentList 'c:\music\MusicStore.dll'

# Configure iis
Remove-WebSite -Name "Default Web Site"
Set-ItemProperty IIS:\AppPools\DefaultAppPool\ managedRuntimeVersion ""
New-Website -Name "MusicStore" -Port 80 -PhysicalPath C:\music\ -ApplicationPool DefaultAppPool
& iisreset
```

## <a name="vm-script-extension"></a>Extensión de script de máquina virtual
Para poder ejecutar extensiones de máquina virtual en una máquina virtual en tiempo de compilación, incluya los recursos de extensión en la plantilla de Azure Resource Manager. La extensión se puede agregar con el asistente Agregar nuevo recurso de Visual Studio, o insertando un recurso JSON válido en la plantilla. El recurso de extensión de script se anida dentro del recurso de máquina virtual. Lo podemos ver en el ejemplo siguiente.

Siga este vínculo para ver el ejemplo de JSON en la plantilla de Resource Manager: [VM Script Extension](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L339)(Extensión de script de máquina virtual). 

En el siguiente código JSON, observe que el script se almacena en GitHub. Este script también podría almacenarse en Azure Blob Storage. Además, plantillas de Azure Resource Manager permiten construir la cadena de ejecución de script de modo que los valores de los parámetros de la plantilla se puedan usar como parámetros para la ejecución del script. En este caso, los datos se proporcionan al implementar las plantillas, y estos valores pueden usarse después al ejecutar el script.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
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
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
  }
}
```

Como se mencionó anteriormente, también es posible almacenar los scripts personalizados en Azure Blob Storage. Hay dos opciones para almacenar los recursos de script en Blob Storage; ya sea hacer que el script o el contenedor sean públicos y sigan el mismo enfoque anterior, o que se guarde en Blob Storage privado que requiere que proporcione storageAccountName y storageAccountKey a la definición de recursos de CustomScriptExtension.

En el ejemplo siguiente hemos realizado un paso adicional. Aunque es posible proporcionar el nombre de la cuenta de almacenamiento y la clave como un parámetro o variable durante la implementación, las plantillas de Resource Manager proporcionan la función `listKeys` que puede obtener la clave de cuenta de almacenamiento mediante programación e insertarla en la plantilla automáticamente durante la implementación.

En la definición de recursos CustomScriptExtension de ejemplo, nuestro script personalizado ya se ha cargado en una cuenta de Azure Storage denominada `mystorageaccount9999`, que existe en otro grupo de recursos denominado `mysa999rgname`. Cuando se implementa una plantilla que contiene este recurso, la función `listKeys` mediante programación obtiene la clave de cuenta de almacenamiento para la cuenta de almacenamiento `mystorageaccount9999` en el grupo de recursos `mysa999rgname` y realiza la inserción en la plantilla automáticamente.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
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
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://mystorageaccount9999.blob.core.windows.net/container/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]",
      "storageAccountName": "mystorageaccount9999",
      "storageAccountKey": "[listKeys(resourceId('mysa999rgname','Microsoft.Storage/storageAccounts', mystorageaccount9999), '2015-06-15').key1]"
    }
  }
}
```

La principal ventaja de este enfoque es que no requiere que cambie los parámetros de plantilla o la implementación en el caso de cambios de claves de cuentas de almacenamiento.

Para más información sobre el uso de la extensión de script personalizado, consulte [Uso de la extensión de script personalizado para máquinas virtuales de Linux con plantillas de Azure Resource Manager](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-step"></a>Paso siguiente
<hr>

[Más plantillas de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)


