<properties 
   pageTitle="Recopilación de datos del equipo" 
   description="Recopilación de los datos del equipo de servidores en la infraestructura local o en la nube" 
   services="operational-insights" 
   documentationCenter="" 
   authors="bandersmsft" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/20/2015"
   ms.author="banders"/>
#Recopilación de datos del equipo

Vista operativa usa datos de los servidores en la infraestructura local o en la nube. Puede recopilar datos del equipo de los orígenes siguientes:

- Agentes de Operations Manager
- Equipos conectados directamente a Vista operativa
- Datos de diagnóstico de máquina virtual en servicios de Almacenamiento de Azure

Una vez recopilados los datos, se envían al servicio de Vista operativa.

## Conexión a Vista operativa desde System Center Operations Manager 

Puede conectar Vista operativa a un entorno de System Center Operations Manager existente. Esto le permitirá utilizar los agentes de Operations Manager existentes como agentes de Vista operativa. Para obtener información adicional sobre el uso de Operations Manager con Vista operativa, consulte [Consideraciones de Operations Manager en relación con Vista operativa](operational-insights-operations-manager.md).

Si usa Operations Manager para supervisar cualquiera de las siguientes cargas de trabajo, deberá establecer las cuentas de ejecución de Operations Manager para ellas. Consulte [Cuentas de ejecución de Operations Manager para Vista operativa](operational-insights-run-as.md) para obtener más información sobre la configuración de cuentas.

- Evaluación de SQL
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE] La compatibilidad con Vista operativa está disponible a partir de Operations Manager 2012 SP1 UR6 y Operations Manager 2012 R2 UR2. Se agregó compatibilidad con proxy en UR7 de System Center Operations Manager 2012 SP1 y System Center Operations Manager 2012 R2 UR3.

#### Para conectar Operations Manager a Vista operativa y agregar agentes

1. En la consola de Operations Manager, haga clic en **Administración**.

2. Expanda el nodo **Vista operativa** y haga clic en **Conexión a Vista operativa**.

3. Haga clic en el vínculo **Registrar en Vista operativa** y siga las instrucciones en pantalla. 

4. Después de completar el asistente de registro, haga clic en **Agregar un equipo/grupo**.

5. En el cuadro de diálogo **Búsqueda de equipo** puede seleccionar los equipos o grupos supervisados por Operations Manager. Seleccione los equipos o grupos que incorporar a Vista operativa, haga clic en **Agregar** y, a continuación, haga clic en **Aceptar**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Conexión de equipos directamente en Vista operativa 

Puede conectar equipos directamente a Vista operativa instalando el agente de Vista operativa en cada equipo que desee incorporar. 


###Descarga e instalación del agente

####Para descargar el archivo de configuración del agente
1. En el **portal de Vista operativa**, en la página **Información general**, haga clic en **Servidores y uso**.
1. En **Servidores conectados directamente**, haga clic en **configurar**.
1. Junto a **Agregar agentes**, haga clic en el enlace del agente para descargar el archivo de configuración.
1. En el cuadro **Clave de espacio de trabajo principal**, seleccione la clave y cópiela (CTRL+C).


#### Para instalar el agente con la configuración
1. Ejecute la configuración para instalar el agente en un equipo que desee administrar.
1. Seleccione **Conectarse el agente a Vista operativa de Microsoft Azure** y, a continuación, haga clic en **Siguiente**.
1. Cuando se le solicite, escriba la información que copió en el paso 4.
1. Cuando se complete, el **agente de administración de Microsoft** aparece en **Panel de control**.

#### Para instalar el agente con la línea de comandos
Modifique y, a continuación, use el siguiente ejemplo para instalar el agente con la línea de comandos.

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

### Configuración del agente de supervisión de Microsoft (opcional)
Use la siguiente información para que un agente se pueda comunicar directamente con el servicio de Vista operativa de Microsoft Azure. Una vez configurado el agente, se registrará con el servicio de agente y obtendrá la información de configuración necesaria y los paquetes de administración que contienen la información del paquete de inteligencia.

Después de recopilar datos de los equipos supervisados por el agente, el número de equipos supervisados aparecerá en el portal de Vista operativa en la página Uso en **Agentes conectados directamente**. Para cualquier equipo que envíe los datos, puede ver sus datos y la información de la evaluación en el portal de Vista operativa.

También puede deshabilitar al agente, si es necesario o habilitarlo mediante script o línea de comandos.

#### Para configurar el agente
- Una vez instalado el **agente de supervisión de Microsoft**, abra el **Panel de control**.
- Abra el agente de supervisión de Microsoft y, a continuación, haga clic en la pestaña Vista operativa de Azure.
- Seleccione **Conectar con Vista operativa de Azure**.
- En el cuadro **Id. del área de trabajo**, escriba el Id. del área de trabajo en el portal de Vista operativa.
- En el cuadro Clave de cuenta, pegue la **clave de espacio de trabajo principal** que copió al instalar el agente y, a continuación, haga clic en **Aceptar**.

#### Para deshabilitar un agente
- Después de instalar el agente, abra el **Panel de control**.
- Abra el agente de supervisión de Microsoft y, a continuación, haga clic en la pestaña **Vista operativa de Azure**.
- Desactive Conectarse a **Vista operativa de Azure**.

#### Para habilitar el agente mediante script o línea de comandos
Puede usar Windows PowerShell o un script de VB en el siguiente ejemplo.

    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    



### Configuración de los ajustes del proxy y del firewall (opcional)
Si dispone de servidores proxy o firewalls en el entorno que restrinjan el acceso a Internet, es posible que tenga que seguir los procedimientos siguientes para permitir que Operations Manager o los agentes se comuniquen con el servio de Vista previa operativa 



- [Configuración de los ajustes del proxy y del firewall (opcional)](operational-insights-proxy-filewall.md) 

## Análisis de datos de servidores en Microsoft Azure

Con Vista operativa puede buscar rápidamente registros de IIS y de eventos para servicios en la nube y máquinas virtuales habilitando [Diagnósticos en Servicios en la nube de Azure](operational-insights-log-collection.md). También puede obtener información adicional de las máquinas virtuales instalando el agente de supervisión de Microsoft.

Los paquetes de inteligencia de evaluación de SQL, seguimiento de cambios y evaluación de actualizaciones funcionan con el agente de supervisión de Microsoft para ofrecer información más detallada sobre las máquinas virtuales. Si no lo ha hecho ya, puede habilitar estos paquetes de inteligencia cuando haya iniciado sesión en el [portal de Visión operativa](https://preview.opinsights.azure.com/).

Para las máquinas virtuales de Azure, existen dos formas sencillas de habilitar la recopilación de datos basada en agente:

- En el Portal de administración de Microsoft Azure

- Uso de PowerShell

Cuando se utiliza una colección basada en agente para los datos de registro, debe configurar qué registros recopilar en la página de configuración de administración de registros del [portal de Vista operativa](https://preview.opinsights.azure.com/).

Si ha configurado Vista operativa para indexar los datos de registro mediante diagnósticos en los Servicios en la nube de Azure y configura el agente para recopilar registros, los mismos registros de eventos tendrán dos copias de los datos indexados. Si tiene instalado el agente, debe recopilar datos de registro mediante el agente y no indexar los registros recopilados por diagnósticos en Servicios en la nube de Azure.

### Portal de administración de Microsoft Azure

En el portal de [Vista operativa](https://preview.opinsights.azure.com/), diríjase al espacio de trabajo de Vista operativa y seleccione la pestaña Servidores. En la pestaña, verá una lista de máquinas virtuales. Seleccione la máquina virtual en la que desee instalar el agente y, a continuación, haga clic en **Habilitar Vista operativa**.

El agente se instala y configura para el espacio de trabajo de Vista operativa.

![Imagen de la página de servidores de Vista operativa](./media/operational-insights-collect-data/servers.png)

### PowerShell

Si prefiere el scripting para realizar cambios en máquinas virtuales de Azure, puede habilitar al agente de supervisión de Microsoft mediante PowerShell.

El agente de supervisión de Microsoft es una [extensión de máquina virtual de Azure](https://msdn.microsoft.com/library/azure/dn832621.aspx) y puede administrarlo con PowerShell, como se muestra en el siguiente ejemplo.

    Add-AzureAccount
    
    $workspaceId="enter workspace here"
    $workspaceKey="enter workspace key here"
    $hostedService="enter hosted service here"
    
    $vm = Get-AzureVM -ServiceName $hostedService
    Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

Cuando se realice la configuración mediante PowerShell, deberá proporcionar el Id. del área de trabajo y la clave de área de trabajo. Puede encontrar el Id. del área de trabajo y la clave de área de trabajo en la página Servidores y uso del portal de Vista operativa.

![Imagen de la configuración del agente directo de Vista operativa](./media/operational-insights-collect-data/direct-agent-cfg.png)

### Recopilación de datos con el diagnóstico en Servicios en la nube de Azure

Vista operativa puede analizar los datos escritos por el diagnóstico de Servicios en la nube de Azure a servicios de Almacenamiento de Azure. Existen dos pasos principales que realizar:

- Configurar la recopilación de datos de diagnóstico para Almacenamiento de Azure
- Configurar Vista operativa para analizar los datos de la cuenta de almacenamiento

Los temas siguientes describen cómo habilitar la recopilación de datos de diagnóstico para Almacenamiento de Azure y, a continuación, cómo configurar Vista operativa para analizar los datos en Almacenamiento de Azure.

Diagnósticos de Azure son extensiones de Azure que le permiten recopilar datos de telemetría de diagnóstico desde un rol de trabajo, un rol web o una máquina virtual que se ejecute en Azure. Los datos de telemetría se almacenan en una cuenta de Almacenamiento de Azure y, a continuación, Vista operativa puede utilizarlos.

>[AZURE.NOTE] Se le cobrarán velocidades de datos normales para el almacenamiento y las transacciones cuando envíe diagnósticos a una cuenta de almacenamiento.

Diagnósticos de Azure puede recopilar los tipos de telemetría siguientes:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Origen de datos</b></td>
		<td><b>

Descripción </b></td>
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
		<td>Eventos generados por el código con la clase .NET <a href="https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">EventSource/a></td>
    </tr>
    <tr align="left" valign="top">
		<td>ETW basado en manifiesto</td>
		<td>Eventos de ETW generados por cualquier proceso.</td>
    </tr>
    </tbody>
    </table>


Actualmente, Vista operativa es capaz de analizar los registros de IIS de los roles web y los registros de eventos de Windows de los roles web y de trabajo y máquinas virtuales de Azure. Los registros deben estar en las siguientes ubicaciones:

- WADWindowsEventLogsTable (Almacenamiento de tablas): contiene información de los registros de eventos de Windows.

- wad-iis-logfiles (Almacenamiento de blobs): contiene información acerca de los registros de IIS.

Para las máquinas virtuales, también tiene la opción de instalar el [agente de supervisión de Microsoft](http://go.microsoft.com/fwlink/?LinkId=517269) en la máquina virtual. Esto le permitirá analizar registros de IIS, registros de eventos y realizar un análisis adicional, incluido el seguimiento de cambios de configuración y la evaluación de la actualización. Habilite la información adicional mediante la instalación de del [agente de supervisión de Microsoft](http://go.microsoft.com/fwlink/?LinkId=517269).

>[AZURE.NOTE] Los registros de IIS de Sitios web Azure no son compatibles actualmente.

Puede ayudarnos a dar prioridad a los registros adicionales para el análisis de Vista operativa mediante un sistema de votación en la [página de comentarios](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).

### Activación de Diagnósticos de Azure en un rol web para la recopilación de eventos y registros de IIS

Consulte [Activación de diagnósticos en un servicio en la nube](https://msdn.microsoft.com/library/azure/dn482131.aspx). Utilizará la información básica de aquí y personalizará los pasos aquí para usarlos con Vista operativa de Microsoft Azure.

Con el diagnóstico de Azure habilitado:

- Los registros de IIS se almacenan de forma predeterminada, con los datos transferidos en el intervalo de transferencia de scheduledTransferPeriod.

- Los registros de eventos de Windows no se transfieren de forma predeterminada.

#### Para habilitar diagnósticos

Para habilitar los registros de eventos de Windows, o para cambiar scheduledTransferPeriod, configure Diagnósticos de Azure con el archivo de configuración XML (diagnostics.wadcfg) como se muestra en el [Paso 2: Adición de un archivo diagnostics.wadcfg a la solución de Visual Studio](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) y [Paso 3: Configuración de diagnóstico para la aplicación](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) en el tema Habilitación de diagnósticos en un servicio en la nube. El siguiente archivo de configuración de ejemplo recopila los registros de IIS y todos los eventos de los registros del sistema y aplicaciones:

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


En el [Paso 4: Configuración del almacenamiento de los datos de diagnóstico](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) del tema Habilitación de diagnósticos en un servicio en la nube, asegúrese de que ConfigurationSettings especifica una cuenta de almacenamiento como ocurre en el siguiente ejemplo:


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


Los valores **AccountName** y **AccountKey** se encuentran en el Portal de administración de Microsoft Azure en el panel de la cuenta de almacenamiento, en Administrar claves de acceso. El protocolo para la cadena de conexión debe ser **https**.

Una vez que se aplica la configuración de diagnóstico actualizada al servicio en la nube y se escribe el diagnóstico en Almacenamiento de Azure, podrá configurar Vista operativa.

### Habilitación del diagnóstico de Servicios en la nube de Azure en una máquina virtual para la recopilación de registros de IIS y de eventos

Use el siguiente procedimiento para habilitar el diagnóstico de Servicios en la nube de Azure en una máquina virtual para la recopilación de registros de IIS y de eventos mediante el Portal de administración de Microsoft Azure.

#### Para habilitar el diagnóstico de Servicios en la nube de Azure en una máquina virtual con el Portal de administración de Azure

1. Instale al agente de máquina virtual cuando cree una máquina virtual. Si la máquina virtual ya existe, compruebe que el agente de máquina virtual ya está instalado.
	- Si utiliza el Portal de administración de Azure predeterminado para crear la máquina virtual, lleve a cabo una **creación personalizada** y seleccione **Instalar el agente de máquina virtual**.
	- Si usa el nuevo Portal de administración de Azure para crear una máquina virtual, seleccione **Configuración opcional**, a continuación, **Diagnósticos** y establezca **Estado** en **Activado**.
	
	Tras la finalización, la máquina virtual tendrá automáticamente la extensión de Diagnósticos de Azure instalada y en ejecución que será la responsable de la recopilación de datos de diagnóstico.

2. Habilite la supervisión y configure el registro de eventos en una máquina virtual existente. Puede habilitar el diagnóstico en el nivel de máquina virtual. Para activar el diagnóstico y, a continuación, configurar el registro de eventos, realice los siguientes pasos:
	1. Seleccione la máquina virtual.
	2. Haga clic en **Supervisión**.
	3. Haga clic en **Diagnóstico**.
	4. Establezca **Estado** en **Activado**.
	5. Seleccione las métricas de diagnóstico que desee utilizar. Vista operativa puede analizar los registros de sistema de eventos de Windows, los registros de aplicaciones de eventos de Windows y los registros de IIS.
	7. Haga clic en **Aceptar**.

Con PowerShell de Azure puede especificar con mayor precisión los eventos que se escriben en Almacenamiento de Azure. Consulte el esquema de configuración de Diagnósticos de Azure 1.2 para obtener un archivo de configuración de ejemplo y documentación detallada sobre su esquema. Asegúrese de instalar y configurar la versión 0.8.7 o posterior de PowerShell de Azure desde [Instalación y configuración de PowerShell de Azure](install-configure-powershell/). Si tiene instalada una versión de Diagnósticos de Microsoft Azure que es anterior a la versión 1.2, no puede usar el nuevo portal para habilitar o configurar diagnósticos.

Puede habilitar y actualizar el agente mediante el siguiente script de PowerShell. También puede utilizar este script con la configuración de registro personalizada. Necesitará modificar el script para establecer la cuenta de almacenamiento, el nombre del servicio y el nombre de máquina virtual.

#### Para habilitar diagnósticos en una máquina virtual con PowerShell de Azure

Revise el siguiente ejemplo de script, cópielo, modifíquelo según sea necesario, guarde el ejemplo como un archivo de script de PowerShell y, a continuación, ejecute el script.

    # Conexión a Azure
    Add-AzureAccount 
     
    # configuración que cambiar:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"
     
    #Creación de configuración pública de Diagnósticos de Azure y conversión a formato de configuración 
    
    # Recopilación solo de los eventos de errores del sistema:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"
    
    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)
    
    #Creación de una configuración privada de Diagnósticos de Azure
    
    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)
    
    #Habilitación de la extensión de diagnóstico para la máquina virtual
    
    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Versión # Obtiene la última versión de la extensión
    
    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
    

### Habilitación del análisis de Almacenamiento de Azure mediante Vista operativa

En el portal de Azure predeterminado, diríjase al espacio de trabajo de Vista operativa y seleccione la pestaña **Almacenamiento**. Use la siguiente información para configurar Vista operativa para que lea a partir de la cuenta de Almacenamiento de Azure con Diagnósticos de Azure.

#### Para habilitar el análisis de Vista operativa

1. Haga clic en **Agregar** para abrir el cuadro **Agregar cuenta de almacenamiento**.

2. Seleccione la **cuenta de almacenamiento**.

3. Seleccione un tipo de datos, ya sea **Eventos** o **Registros de IIS**.

4. Haga clic en **Aceptar**.

5. Repita los pasos anteriores para cada combinación de cuenta de almacenamiento y tipo de datos que desee recopilar.

En aproximadamente 1 hora comenzará a ver los datos de la cuenta de almacenamiento disponible para el análisis de Vista operativa.




## Contenido relacionado

- [Publicación de blog: Conexión de servidores directamente a Vista operativa](http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [Publicación de blog: Habilitación de Vista operativa para Máquinas virtuales de Azure](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## Pasos siguientes

[Configuración de los ajustes del proxy y del firewall (opcional)](operational-insights-proxy-filewall.md)




<!--HONumber=52-->