<properties
	pageTitle="Habilitación de la depuración remota con entrega continua"
	description="Sepa cómo habilitar la depuración remota cuando se usa entrega continua para implementar en Azure."
	services="cloud-services"
	documentationCenter=".net"
	authors="kempb"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="cloud-services"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="kempb"/>
# Habilitación de la depuración remota al usar la entrega continua para publicar en Azure

Puede habilitar la depuración remota en Azure cuando usa la [entrega continua](cloud-services-dotnet-continuous-delivery.md) para publicar en Azure; para ello, siga estos pasos.

En este tema:

[Habilitación de la depuración remota para los servicios en la nube](#cloudservice)

[Habilitación de la depuración remota para las máquinas virtuales](#virtualmachine)

## <a name="cloudservice"></a>Habilitación de la depuración remota para los servicios en la nube

1. En el agente de compilación, configure el entorno inicial para Azure como se describe en [Compilación de línea de comandos para Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Como se requiere el tiempo de ejecución de depuración remota (msvsmon.exe) para el paquete, instale las [Herramientas remotas para Visual Studio 2015 RC](http://www.microsoft.com/download/details.aspx?id=46874) (o las [Herramientas remotas para Visual Studio 2013 Update 5 RC](https://www.microsoft.com/es-es/download/details.aspx?id=46870) si usa Visual Studio 2013). También puede copiar los binarios de depuración remota desde un sistema que tenga instalado Visual Studio.
3. Cree un certificado como se describe en [Creación de un certificado de servicio para Azure](http://msdn.microsoft.com/library/azure/gg432987.aspx). Mantenga la huella digital del certificado de .pfx y RDP y cargue el certificado en el servicio en la nube de destino.
4. Use las siguientes opciones de la línea de comandos MSBuild para compilarlo y empaquetarlo con la depuración remota habilitada. (Actualice las rutas de acceso de sus archivos de sistema y de proyecto.)

		msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.6" "<path to your VS solution file>"

	`VSX64RemoteDebuggerPath` es la ruta de acceso a la carpeta que contiene msvsmon.exe en las Herramientas para Visual Studio.

5. Publíquelo en el servicio en la nube de destino mediante el paquete y el archivo .cscfg generado en el paso anterior.
6. Importe el certificado (archivo .pfx) en la máquina que tiene instalado Visual Studio con el SDK de Azure para .NET.

## <a name="virtualmachine"></a>Habilitación de la depuración remota para las máquinas virtuales

1. Cree una máquina virtual de Azure. Consulte [Creación de una máquina virtual que ejecuta Windows Server](../virtual-machines-windows-tutorial.md) o [Creación de máquinas virtuales de Azure en Visual Studio](http://msdn.microsoft.com/library/azure/dn569263.aspx).
2. En la [página del portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=269851), examine el panel de máquinas virtuales para ver la "Huella digital del certificado RDP" de la máquina virtual. Esta se usa como valor de `ServerThumbprint` en la configuración de extensiones.
3. Cree un certificado de cliente como se describe en [Creación de un certificado de servicio para Azure](http://msdn.microsoft.com/library/azure/gg432987.aspx) (mantenga la huella digital del certificado de .pfx y RDP).
4. Instale [PowerShell de Azure ](http://go.microsoft.com/?linkid=9811175&clcid=0x409) (versión 0.7.4 o posterior) desde el Centro de descarga de Microsoft.
5. Ejecute el siguiente script para habilitar la extensión RemoteDebug. Sustituya los datos personales por los suyos propios, como el nombre de la suscripción, el nombre de servicio y la huella digital. (NOTA: este script está configurado para Visual Studio 2015. Si utiliza Visual Studio 2013, use "RemoteDebugVS2013" para ReferenceName y ExtensionName).

	<pre>
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
</pre>

6. Importe el certificado (.pfx) en la máquina que tiene instalado Visual Studio con el SDK de Azure para .NET.
 

<!---HONumber=July15_HO5-->