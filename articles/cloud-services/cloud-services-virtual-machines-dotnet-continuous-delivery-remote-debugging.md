---
title: "Habilitación de la depuración remota con entrega continua | Microsoft Docs"
description: "Sepa cómo habilitar la depuración remota cuando se usa entrega continua para implementar en Azure."
services: cloud-services
documentationcenter: .net
author: mikejo
manager: ghogen
editor: 
ms.assetid: 7d423639-3b2f-4ca5-ac5a-9ac19a217c29
ms.service: cloud-services
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: 1a30b42e6e84edf9a7cef861aaf6a60e87c473d0
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>Habilitación de la depuración remota al usar la entrega continua para publicar en Azure
Puede habilitar la depuración remota en Azure, para servicios en la nube o máquinas virtuales, cuando use la [entrega continua](cloud-services-dotnet-continuous-delivery.md) para publicar en Azure; para ello, siga estos pasos.

## <a name="enabling-remote-debugging-for-cloud-services"></a>Habilitación de la depuración remota para los servicios en la nube
1. En el agente de compilación, configure el entorno inicial para Azure como se describe en [Compilación de línea de comandos para Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Como se requiere el tiempo de ejecución de depuración remota (msvsmon.exe) para el paquete, instale las **Herramientas remotas para Visual Studio**.

   * [Herramientas remotas para Visual Studio 2017](https://go.microsoft.com/fwlink/?LinkId=746570)
   * [Herramientas remotas para Visual Studio 2015](https://go.microsoft.com/fwlink/?LinkId=615470)
   * [Herramientas remotas para Visual Studio 2013 Update 5](https://www.microsoft.com/download/details.aspx?id=48156)
    
   También puede copiar los binarios de depuración remota desde un sistema que tenga instalado Visual Studio.

3. Cree un certificado como se describe en [Información general de los certificados para Azure Cloud Services](cloud-services-certs-create.md). Mantenga la huella digital del certificado de .pfx y RDP y cargue el certificado en el servicio en la nube de destino.
4. Use las siguientes opciones de la línea de comandos MSBuild para compilarlo y empaquetarlo con la depuración remota habilitada. (Actualice las rutas de acceso de sus archivos de sistema y de proyecto.)
   
   ```cmd
   msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"
   ```
   
   `VSX64RemoteDebuggerPath` es la ruta de acceso a la carpeta que contiene msvsmon.exe en las Herramientas para Visual Studio.
   `RemoteDebuggerConnectorVersion` es la versión de Azure SDK del servicio en la nube. También debe coincidir con la versión instalada con Visual Studio.

5. Publíquelo en el servicio en la nube de destino mediante el paquete y el archivo .cscfg generado en el paso anterior.
6. Importe el certificado (archivo .pfx) en la máquina que tiene instalado Visual Studio con el SDK de Azure para .NET. No se olvide de importar el almacén de certificados `CurrentUser\My` ; de lo contrario, no se podrá asociar al depurador de Visual Studio.

## <a name="enabling-remote-debugging-for-virtual-machines"></a>Habilitación de la depuración remota para las máquinas virtuales
1. Cree una máquina virtual de Azure. Consulte [Creación de una máquina virtual que ejecuta Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [Creación y administración de Azure Virtual Machines en Visual Studio](../virtual-machines/windows/classic/manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
2. En Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=269851), navegue a la máquina virtual y a **RDP CERTIFICATE THUMBPRINT** de la máquina virtual. Este valor se usa para el valor de `ServerThumbprint` en la configuración de la extensión.
3. Cree un certificado de cliente como se describe en [Información general sobre certificados para Azure Cloud Services](cloud-services-certs-create.md) (mantenga la huella digital del certificado de .pfx y RDP).
4. Instale Azure Powershell (versión 0.7.4 o posterior) como se describe en [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).
5. Ejecute el siguiente script para habilitar la extensión RemoteDebug. Sustituya las rutas de acceso y los datos personales por los suyos propios, como el nombre de la suscripción, el nombre de servicio y la huella digital.
   
   > [!NOTE]
   > Este script está configurado para Visual Studio 2015. Si usa Visual Studio 2013 o Visual Studio 2017, cambie las asignaciones `$referenceName` y `$extensionName` siguientes por `RemoteDebugVS2013` o `RemoteDebugVS2017`.

    ```powershell   
    Add-AzureAccount

    Select-AzureSubscription "My Microsoft Subscription"

    $vm = Get-AzureVM -ServiceName "mytestvm1" -Name "mytestvm1"

    $endpoints = @(
                    ,@{Name="RDConnVS2013"; PublicPort=30400; PrivatePort=30398}
                    ,@{Name="RDFwdrVS2013"; PublicPort=31400; PrivatePort=31398}
                )

    foreach($endpoint in $endpoints)
    {
        Add-AzureEndpoint -VM $vm -Name $endpoint.Name -Protocol tcp -PublicPort $endpoint.PublicPort -LocalPort $endpoint.PrivatePort
    }

    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015"
    $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
    $extensionName = "RemoteDebugVS2015"
    $version = "1.*"
    $publicConfiguration = "<PublicConfig><Connector.Enabled>true</Connector.Enabled><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

    $vm | Set-AzureVMExtension -ReferenceName $referenceName -Publisher $publisher -ExtensionName $extensionName -Version $version -PublicConfiguration $publicConfiguration

    foreach($extension in $vm.VM.ResourceExtensionReferences)
    {
        if(($extension.ReferenceName -eq $referenceName) `
        -and ($extension.Publisher -eq $publisher) `
        -and ($extension.Name -eq $extensionName) `
        -and ($extension.Version -eq $version))
        {
            $extension.ResourceExtensionParameterValues[0].Key = 'config.txt'
            break
        }
    }

    $vm | Update-AzureVM
    ```

6. Importe el certificado (.pfx) en la máquina que tiene instalado Visual Studio con el SDK de Azure para .NET.

