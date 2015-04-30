<properties 
	pageTitle="connect-scom" 
	description="Conexión de equipos directamente en Vista operativa" 
	services="operational-insights" 
	documentationCenter="" 
	authors="lauracr" 
	manager="jwhit" 
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na" 
	ms.date="02/20/2015"
	ms.author="lauracr"/>

# Conexión de equipos directamente en Vista operativa 

Puede conectar equipos directamente a Vista operativa instalando el agente de Vista operativa en cada equipo que desee incorporar. 


##Descarga, instalación y configuración del agente
###Para descargar el archivo de configuración del agente
1. En el **portal de Vista operativa**, en la página **Información general**, haga clic en **Servidores y uso**.
1. En **Servidores conectados directamente**, haga clic en **configurar**.
1. Junto a **Agregar agentes**, haga clic en el enlace del agente para descargar el archivo de configuración.
1. En el cuadro **Clave de espacio de trabajo principal**, seleccione la clave y cópiela (CTRL+C).


### Para instalar el agente con la configuración
1. Ejecute la configuración para instalar el agente en un equipo que desee administrar.
1. Seleccione **Conectarse el agente a Vista operativa de Microsoft Azure** y, a continuación, haga clic en **siguiente**.
1. Cuando se le solicite, escriba la información que copió en el paso 4.
1. Cuando se complete, el **agente de administración de Microsoft** aparece en **Panel de control**.

### Para instalar el agente con la línea de comandos
Modifique y, a continuación, use el siguiente ejemplo para instalar el agente con la línea de comandos.

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

## Configuración de nuevo del agente de supervisión de Microsoft (opcional)
Use la siguiente información para que un agente se pueda comunicar directamente con el servicio de Vista operativa de Microsoft Azure. Una vez configurado el agente, se registrará con el servicio de agente y obtendrá la información de configuración necesaria y los paquetes de administración que contienen la información del paquete de inteligencia.

Después de recopilar datos de los equipos supervisados por el agente, el número de equipos supervisados aparecerá en el portal de Vista operativa en la página Uso en **Agentes conectados directamente**. Para cualquier equipo que envíe los datos, puede ver sus datos y la información de la evaluación en el portal de Vista operativa.

También puede deshabilitar al agente, si es necesario o habilitarlo mediante script o línea de comandos.

### Para configurar el agente
- Una vez instalado el **agente de supervisión de Microsoft**, abra el **Panel de control**.
- Abra el agente de supervisión de Microsoft y, a continuación, haga clic en la pestaña Vista operativa de Azure.
- Seleccione **Conectar con Vista operativa de Azure**.
- En el cuadro **Id. del área de trabajo**, escriba el Id. del área de trabajo en el portal de Vista operativa.
- En el cuadro Clave de cuenta, pegue la **clave de espacio de trabajo principal** que copió al instalar el agente y, a continuación, haga clic en **Aceptar**.

### Para deshabilitar un agente
- Después de instalar el agente, abra el **Panel de control**.
- Abra el agente de supervisión de Microsoft y, a continuación, haga clic en la pestaña **Vista operativa de Azure**.
- Desactive Conectarse a **Vista operativa de Azure**.

### Para habilitar el agente mediante script o línea de comandos
Puede usar Windows PowerShell o un script de VB en el siguiente ejemplo.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    



### Configuración de proxy y configuración de firewall (opcional)
Si dispone de servidores proxy o firewalls en el entorno que restrinjan el acceso a Internet, es posible que tenga que seguir los procedimientos siguientes para permitir que Operations Manager o los agentes se comuniquen con el servio de Vista previa operativa 



- [Configuración de los ajustes del proxy y del firewall (opcional)](https://msdn.microsoft.com/library/azure/dn884643.aspx) 


## Contenido relacionado

- [Publicación de blog: Conexión de servidores directamente a Vista operativa](http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [Publicación de blog: Habilitación de Vista operativa para Máquinas virtuales de Azure](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)


<!--HONumber=52-->