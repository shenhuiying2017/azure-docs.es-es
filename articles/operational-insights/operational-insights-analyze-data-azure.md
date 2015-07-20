<properties
   pageTitle="Análisis de datos de servidores en Microsoft Azure"
   description="Registros de eventos de búsqueda y de IIS para servicios en la nube y máquinas virtuales mediante la habilitación de Diagnósticos de Azure."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders"/>
# Análisis de datos de servidores en Microsoft Azure

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Visión operativa usa datos de los servidores de la infraestructura local o en la nube. Puede recopilar datos de equipo del almacenamiento de Azure cuando son generados por Diagnósticos de Azure.

Con los datos recopilados del almacenamiento de Azure, puede buscar rápidamente en los registros de eventos y de IIS de servicios en la nube y máquinas virtuales mediante la habilitación de [Diagnósticos de Azure](operational-insights-log-collection.md). También puede obtener información adicional de las máquinas virtuales instalando el agente de supervisión de Microsoft.

Las soluciones de evaluación de actualizaciones, seguimiento de cambios y evaluación de SQL trabajan todas con Microsoft Monitoring Agent para proporcionar información más detallada sobre las máquinas virtuales. Si no lo ha hecho ya, puede [agregar soluciones](operational-insights-add-solution.md) cuando inicie sesión en el [portal de Visión operativa](https://preview.opinsights.azure.com/).

Para las máquinas virtuales de Azure, existen dos formas sencillas de habilitar la recopilación de datos basada en agente:

- En el Portal de administración de Microsoft Azure

- Uso de PowerShell

Cuando se usa una colección basada en agente para los datos de registro, debe configurar qué registros se van recopilar en la página de configuración de administración de registros del [portal de Visión operativa](https://preview.opinsights.azure.com/).

 >[AZURE.NOTE]Si ha configurado Visión operativa para indexar los datos de registro mediante Diagnósticos de Azure y configura el agente para recopilar registros, los mismos registros se indexarán dos veces. Ambos orígenes de datos se le cobrarán a la tarifa normal. Si tiene instalado el agente, debe recopilar datos de registro mediante el agente y no indexar los registros recopilados por Diagnósticos de Azure.

## Portal de administración de Microsoft Azure

Puede instalar el agente de Visión operativa desde el [Portal de Azure](https://manage.windowsazure.com/#Workspaces/OperationalInsightExtension/Workspaces).

### Para instalar al agente de Visión operativa
1. En el Portal de Azure, vaya al área de trabajo de Visión operativa y seleccione la pestaña **Servidores**.
2. En la pestaña, verá una lista de máquinas virtuales. Seleccione la máquina virtual en la que quiere instalar el agente y luego haga clic en **Habilitar visión operativa**.

El agente se instala y se configura automáticamente para el área de trabajo de Visión operativa.

![Imagen de la página de servidores de Vista operativa](./media/operational-insights-analyze-data-azure/servers.png)

 >[AZURE.NOTE]El [agente de VM de Azure](https://msdn.microsoft.com/library/azure/dn832621.aspx) debe estar instalado para instalar automáticamente el agente de Visión operativa.



## PowerShell

Si prefiere el scripting para realizar cambios en máquinas virtuales de Azure, puede habilitar al agente de supervisión de Microsoft mediante PowerShell.

Microsoft Monitoring Agent es una [extensión de máquina virtual de Azure](https://msdn.microsoft.com/library/azure/dn832621.aspx) y se puede administrar con PowerShell, tal y como se muestra en el ejemplo siguiente.

```powershell
Add-AzureAccount

$workspaceId="enter workspace here"
$workspaceKey="enter workspace key here"
$hostedService="enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService
Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

Al realizar la configuración con PowerShell, es necesario proporcionar el Id. del área de trabajo y la clave principal. Puede encontrar esta información en la página **Configuración** del portal de Visión operativa.

![sources](./media/operational-insights-analyze-data-azure/sources01.png)

## Recopilación de datos mediante Diagnósticos de Azure

Visión operativa puede analizar los datos escritos en el almacenamiento de Azure por Diagnósticos de Azure. Existen dos pasos principales que realizar:

- Configurar la recopilación de datos de diagnóstico para Almacenamiento de Azure
- Configurar Vista operativa para analizar los datos de la cuenta de almacenamiento

Los temas siguientes describen cómo habilitar la recopilación de datos de diagnóstico para Almacenamiento de Azure y, a continuación, cómo configurar Vista operativa para analizar los datos en Almacenamiento de Azure.

Diagnósticos de Azure es una extensión de Azure que le permiten recopilar datos de diagnóstico de un rol de trabajo, un rol web o una máquina virtual que se ejecuten en Azure. Los datos se almacenan en una cuenta de almacenamiento de Azure y luego se pueden usar en Visión operativa.

>[AZURE.NOTE]Se le cobrarán tarifas normales por el almacenamiento y las transacciones al enviar diagnósticos a una cuenta de almacenamiento y por las lecturas de Visión operativa de los datos de la cuenta de almacenamiento.

Diagnósticos de Azure puede recopilar los tipos de telemetría siguientes:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Origen de datos</b></td>
		<td><b>Descripción</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Registros IIS</td>
		<td>Información sobre los sitios web de IIS.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Registros de infraestructura de diagnóstico de Azure</td>
		<td>Información sobre los propios Diagnósticos.</td>
    </tr>
	<tr align="left" valign="top">
		<td>Registros de solicitudes con error de IIS </td>
		<td>Información sobre las solicitudes erróneas a un sitio o aplicación de IIS.</td>
    </tr>
	    <tr align="left" valign="top">
		<td>Registros de eventos de Windows</td>
		<td>Información enviada al sistema de registro de eventos de Windows.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Contadores de rendimiento</td>
		<td>Sistema operativo y contadores de rendimiento personalizados.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Volcados de memoria</td>
		<td>Información sobre el estado del proceso en caso de bloqueo de una aplicación.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Registros de errores personalizados</td>
		<td>Archivos creados por su aplicación o servicio.</td>
    </tr>
    <tr align="left" valign="top">
		<td>NET EventSource</td>
		<td>Eventos generados por su código mediante la clase <a href="https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">EventSource .NET</td>
    </tr>
    <tr align="left" valign="top">
		<td>ETW basado en manifiesto</td>
		<td>Eventos de ETW generados por cualquier proceso.</td>
    &lt;/tr
    <tr align="left" valign="top">
		<td>Syslog</td>
		<td>Eventos enviados a los demonios Syslog o Rsyslog</td>
    </tr>
    </tbody>
    </table>


Actualmente, Visión operativa es capaz de analizar:

- Registros IIS de roles web y máquinas virtuales
- Registros de eventos de Windows de roles web, roles de trabajo y máquinas virtuales de Azure que ejecutan un sistema operativo Windows
- Syslog desde máquinas virtuales de Azure que ejecutan un sistema operativo Linux

Los registros deben estar en las siguientes ubicaciones:

- WADWindowsEventLogsTable (Almacenamiento de tablas): contiene información de los registros de eventos de Windows.
- wad-iis-logfiles (Almacenamiento de blobs): contiene información acerca de los registros de IIS.
- LinuxsyslogVer2v0 (Almacenamiento de tablas): contiene eventos syslog de Linux.

 >[AZURE.NOTE]Los registros de IIS de Sitios web Azure no son compatibles actualmente.

Para máquinas virtuales, también tiene la opción de instalar el [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) en la máquina virtual para permitir información adicional. Además de poder analizar registros de IIS y registros de eventos, también podrá realizar análisis adicionales, como seguimiento de cambios de configuración, evaluación de SQL y evaluación de actualizaciones.

Puede ayudarnos a dar prioridad a otros registros para que los analice Visión operativa votando en nuestra [página de comentarios](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).

## Activación de Diagnósticos de Azure en un rol web para la recopilación de eventos y registros de IIS

Consulte [Habilitar diagnósticos en un servicio en la nube](https://msdn.microsoft.com/library/azure/dn482131.aspx). Utilizará la información básica de aquí y personalizará los pasos aquí para usarlos con Vista operativa de Microsoft Azure.

Con el diagnóstico de Azure habilitado:

- Los registros de IIS se almacenan de forma predeterminada, con los datos transferidos en el intervalo de transferencia de scheduledTransferPeriod.

- Los registros de eventos de Windows no se transfieren de forma predeterminada.

### Para habilitar diagnósticos

Para habilitar los registros de eventos de Windows, o para cambiar el valor de scheduledTransferPeriod, configure Diagnósticos de Azure mediante el archivo de configuración XML (diagnostics.wadcfg), como se muestra en [Paso 2: Agregar el archivo diagnostics.wadcfg a la solución de Visual Studio](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) y [Paso 3: Configurar diagnósticos para la aplicación](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) en el tema Habilitar diagnósticos en un servicio en la nube. En el ejemplo siguiente, el archivo de configuración recopila registros de IIS y todos los eventos de los registros de la aplicación y del sistema:

    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>


En [Paso 4: Configurar el almacenamiento de los datos de diagnóstico](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) del tema Habilitar diagnósticos en un servicio en la nube, asegúrese de que ConfigurationSettings especifica una cuenta de almacenamiento, como en el ejemplo siguiente:


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


Los valores de **AccountName** y **AccountKey** se encuentran en el Portal de administración de Microsoft Azure en el panel de cuentas de almacenamiento, debajo de Administrar claves de acceso. El protocolo de la cadena de conexión debe ser **https**.

Una vez que se aplica la configuración de diagnóstico actualizada al servicio en la nube y se escribe el diagnóstico en Almacenamiento de Azure, podrá configurar Vista operativa.

## Habilitación de Diagnósticos de Azure en una máquina virtual para la recopilación de registros de IIS y de eventos

Use el siguiente procedimiento para habilitar Diagnósticos de Azure en una máquina virtual para la recopilación de registros de IIS y de eventos mediante el Portal de administración de Microsoft Azure.

### Para habilitar Diagnósticos de Azure en una máquina virtual con el Portal de administración de Azure

1. Instale al agente de máquina virtual cuando cree una máquina virtual. Si la máquina virtual ya existe, compruebe que el agente de máquina virtual ya está instalado.
	- Si usa el Portal de administración de Azure predeterminado para crear la máquina virtual, lleve a cabo una **Creación personalizada** y seleccione **Instalar el agente de máquina virtual**.
	- Si usa el nuevo Portal de administración de Azure para crear una máquina virtual, seleccione **Configuración opcional**, luego **Diagnósticos** y establezca **Estado** en **Activado**.

	Tras la finalización, la máquina virtual tendrá automáticamente la extensión de Diagnósticos de Azure instalada y en ejecución que será la responsable de la recopilación de datos de diagnóstico.

2. Habilite la supervisión y configure el registro de eventos en una máquina virtual existente. Puede habilitar el diagnóstico en el nivel de máquina virtual. Para activar el diagnóstico y, a continuación, configurar el registro de eventos, realice los siguientes pasos:
	1. Seleccione la máquina virtual.
	2. Haga clic en **Supervisión**.
	3. Haga clic en **Diagnósticos**.
	4. Establezca el **Estado** en **Activado**.
	5. Seleccione las métricas de diagnóstico que desee utilizar. Vista operativa puede analizar los registros de sistema de eventos de Windows, los registros de aplicaciones de eventos de Windows y los registros de IIS.
	7. Haga clic en **Aceptar**.

Con PowerShell de Azure puede especificar con mayor precisión los eventos que se escriben en Almacenamiento de Azure. Consulte el esquema de configuración de Diagnósticos de Azure 1.2 para obtener un archivo de configuración de ejemplo y documentación detallada sobre su esquema. Asegúrese de instalar y configurar la versión 0.8.7 o posterior de Azure PowerShell siguiendo los pasos que se indica en [Instalación y configuración de Azure PowerShell](powershell-install-configure) Si tiene instalada una versión de Diagnósticos de Microsoft Azure que es anterior a la versión 1.2, no puede usar el nuevo portal para habilitar o configurar diagnósticos.

Puede habilitar y actualizar el agente mediante el siguiente script de PowerShell. También puede utilizar este script con la configuración de registro personalizada. Necesitará modificar el script para establecer la cuenta de almacenamiento, el nombre del servicio y el nombre de máquina virtual.

### Para habilitar diagnósticos en una máquina virtual con PowerShell de Azure

Revise el siguiente ejemplo de script, cópielo, modifíquelo según sea necesario, guarde el ejemplo como un archivo de script de PowerShell y, a continuación, ejecute el script.


	#Connect to Azure
	Add-AzureAccount

	# settings to change:
	$wad_storage_account_name = "myStorageAccount"
	$service_name = "myService"
	$vm_name = "myVM"

	#Construct Azure Diagnostics public config and convert to config format

	# Collect just system error events:
	$wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

	$wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
	$wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

	#Construct Azure diagnostics private config

	$wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
	$wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

	#Enable Diagnostics Extension for Virtual Machine

	$wad_extension_name = "IaaSDiagnostics"
	$wad_publisher = "Microsoft.Azure.Diagnostics"
	$wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

	(Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM


## Habilitación del análisis de Almacenamiento de Azure mediante Vista operativa

Use el siguiente procedimiento para permitir el análisis del almacenamiento y configurar Visión operativa para que lea desde la cuenta de almacenamiento de Azure con Diagnósticos de Azure.

### Para habilitar el análisis de Vista operativa

1. En el Portal de Azure predeterminado, vaya al área de trabajo de Visión operativa y seleccione la pestaña **Almacenamiento**. ![workspace storage tab](./media/operational-insights-analyze-data-azure/workspace-storage-tab.png)
2. Haga clic en **Agregar un cuenta de almacenamiento** para abrir el cuadro **Agregar cuenta de almacenamiento**.
3. Seleccione la cuenta de almacenamiento que quiera usar.
4. En la lista **Tipo de datos**, seleccione un tipo de datos: **Eventos**, **Registros de IIS** o **Syslog (Linux)**.
5. Haga clic en la imagen de **Aceptar**. ![storage account box](./media/operational-insights-analyze-data-azure/storage-account.png)
6. Repita los pasos anteriores para cada combinación de cuenta de almacenamiento y tipo de datos que quiera recopilar.

En aproximadamente 1 hora comenzará a ver los datos de la cuenta de almacenamiento disponible para el análisis de Vista operativa.

## Contenido relacionado

- [Conexión de equipos directamente en Visión operativa](operational-insights-direct-agent)
- [Entrada de blog: Habilitación de Visión operativa para máquinas virtuales de Azure](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## Pasos siguientes

[Configuración de los ajustes del proxy y del firewall (opcional)](../operational-insights-proxy-filewall.md)

<!---HONumber=July15_HO2-->