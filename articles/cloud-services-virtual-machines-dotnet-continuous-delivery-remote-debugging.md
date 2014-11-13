<properties urlDisplayName="Enable remote debugging with continuous delivery" pageTitle="Habilitaci&oacute;n de la depuraci&oacute;n remota con entrega continua" metaKeywords="" description="Vea c&oacute;mo habilitar la depuraci&oacute;n remota cuando se usa entrega continua para implementar en Azure." metaCanonical="" services="cloud-services,virtual machines" documentationCenter=".NET" title="Habilitaci&oacute;n de la depuraci&oacute;n remota al usar la entrega continua para publicar en Azure" authors="kempb" solutions="" manager="douge" editor="" />

<tags ms.service="multiple" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="kempb" />

# Habilitación de la depuración remota al usar la entrega continua para publicar en Azure

Puede habilitar la depuración remota en Azure cuando usa la [entrega continua][entrega continua] para publicar en Azure; para ello, siga estos pasos.

En este tema:

[Habilitación de la depuración remota para los servicios en la nube][Habilitación de la depuración remota para los servicios en la nube]

[Habilitación de la depuración remota para las máquinas virtuales][Habilitación de la depuración remota para las máquinas virtuales]

## <a name="cloudservice"></a>Habilitación de la depuración remota para los servicios en la nube

1.  En el agente de compilación, configure el entorno inicial para Azure como se describe en [Compilación de línea de comandos para Azure][Compilación de línea de comandos para Azure].
2.  Como se requiere el tiempo de ejecución de depuración remota (msvsmon.exe) para el paquete, instale las [Herramientas remotas para Visual Studio 2013][Herramientas remotas para Visual Studio 2013] (o las [Herramientas remotas para Visual Studio 2012 Update 4][Herramientas remotas para Visual Studio 2012 Update 4] si usa Visual Studio 2012). También puede copiar los binarios de depuración remota desde un sistema que tenga instalado Visual Studio.
3.  Cree un certificado como se describe en [Crear un certificado del servicio para Azure][Crear un certificado del servicio para Azure]. Mantenga la huella digital del certificado de .pfx y RDP y cargue el certificado en el servicio en la nube de destino.
4.  Use las siguientes opciones de la línea de comandos MSBuild para compilarlo y empaquetarlo con la depuración remota habilitada. (Actualice las rutas de acceso de sus archivos de sistema y de proyecto.)

    /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="C:\\Remote Debugger\\x64\\";RemoteDebuggerConnectorCertificateThumbprint="56D7D1B25B472268E332F7FC0C87286458BFB6B2";RemoteDebuggerConnectorVersion="2.4" "C:\\Users\\yourusername\\Documents\\visual studio 2013\\Projects\\WindowsAzure1\\WindowsAzure1.sln"

5.  Publíquelo en el servicio en la nube de destino mediante el paquete y el archivo .cscfg generado en el paso anterior.
6.  Importe el certificado (archivo .pfx) en la máquina que tiene instalado Visual Studio con el SDK 2.4 de Azure.

## <a name="virtualmachine"></a>Habilitación de la depuración remota para las máquinas virtuales

1.  Cree una máquina virtual de Azure. Consulte [Creación de una máquina virtual que ejecuta Windows Server][Creación de una máquina virtual que ejecuta Windows Server] o [Crear y administrar máquinas virtuales de Azure en Visual Studio][Crear y administrar máquinas virtuales de Azure en Visual Studio].
2.  En la [página del portal de Azure][página del portal de Azure], examine el panel de máquinas virtuales para ver la "Huella digital del certificado RDP" de la máquina virtual. Esta se usa como valor de ServerThumbprint en la configuración de extensiones.
3.  Cree un certificado de cliente como se describe en [Crear un certificado del servicio para Azure][Crear un certificado del servicio para Azure] (mantenga la huella digital del certificado de .pfx y RDP).
4.  Instale [Azure Powershell][Azure Powershell] (versión 0.7.4 o posterior) desde el Centro de descarga de Microsoft.
5.  Ejecute el siguiente script para habilitar la extensión RemoteDebug. Sustituya los datos personales por los suyos propios, como el nombre de la suscripción, el nombre de servicio y la huella digital. (NOTA: Este script está configurado para Visual Studio 2013. Si utiliza Visual Studio 2012, use "RemoteDebugVS2013" para ReferenceName y ExtensionName.)

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

        $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2013"
        $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
        $extensionName = "RemoteDebugVS2013"
        $version = "1.*"
        $publicConfiguration = "true56D7D1B25B472268E332F7FC0C87286458BFB6B2E7DCB00CB916C468CC3228261D6E4EE45C8ED3C63039831398"

        $vm | Set-AzureVMExtension `
        -ReferenceName $referenceName `
        -Publisher $publisher `
        -ExtensionName $extensionName `
        -Version $version `
        -PublicConfiguration $publicConfiguration

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

6.  Importe el certificado (.pfx) en la máquina que tiene instalado Visual Studio con el SDK de Azure para .NET 2.4.

  [entrega continua]: http://azure.microsoft.com/es-es/documentation/articles/cloud-services-dotnet-continuous-delivery/
  [Habilitación de la depuración remota para los servicios en la nube]: #cloudservice
  [Habilitación de la depuración remota para las máquinas virtuales]: #virtualmachine
  [Compilación de línea de comandos para Azure]: http://msdn.microsoft.com/es-es/library/hh535755.aspx
  [Herramientas remotas para Visual Studio 2013]: http://www.microsoft.com/es-es/download/details.aspx?id=40781
  [Herramientas remotas para Visual Studio 2012 Update 4]: http://www.microsoft.com/es-es/download/details.aspx?id=38184
  [Crear un certificado del servicio para Azure]: http://msdn.microsoft.com/library/azure/gg432987.aspx
  [Creación de una máquina virtual que ejecuta Windows Server]: http://azure.microsoft.com/es-es/documentation/articles/virtual-machines-windows-tutorial/
  [Crear y administrar máquinas virtuales de Azure en Visual Studio]: http://msdn.microsoft.com/es-es/library/azure/dn569263.aspx
  [página del portal de Azure]: http://go.microsoft.com/fwlink/p/?LinkID=269851
  [Azure Powershell]: http://go.microsoft.com/?linkid=9811175&clcid=0x409
