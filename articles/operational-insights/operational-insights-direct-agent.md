<properties
	pageTitle="Conexión de equipos directamente en Vista operativa"
	description="Puede conectar equipos directamente a Visión operativa instalando el agente de Visión operativa en cada equipo que desee incorporar."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/02/2015" 
	ms.author="banders"/>
# Conexión de equipos directamente en Vista operativa

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Puede conectar equipos directamente a Visión operativa instalando el agente de Visión operativa en cada equipo que desee incorporar.

> [AZURE.TIP]Para las máquinas virtuales que se ejecutan en Azure, instale el agente siguiendo los pasos descritos en [Análisis de datos de servidores de Microsoft Azure](operational-insights-analyze-data-azure.md).

## Descarga e instalación del agente
Utilice los procedimientos siguientes para descargar e instalar el agente de Visión operativa.

### Para descargar el archivo de configuración del agente
1. En el portal de Visión operativa, en la página **Información general**, haga clic en el icono **Configuración**. Haga clic en la pestaña **Orígenes conectados** en la parte superior. ![página de configuración](./media/operational-insights-direct-agent/direct-agent01.png)
2. En **Conectar servidores directamente (64 bits)**, haga clic en el botón Descargar agente para descargar el archivo de instalación.
3. A la derecha del **Id. de área de trabajo**, haga clic en el icono de copiar y pegue el identificador en el Bloc de notas.
4. A la derecha de la **Clave principal**, haga clic en el icono de copiar y pegue el identificador en el Bloc de notas. ![página de configuración](./media/operational-insights-direct-agent/direct-agent02.png)

### Para instalar el agente con la configuración
1. Ejecute la configuración para instalar el agente en un equipo que desee administrar.
2. Seleccione **Conectar el agente a Visión operativa de Microsoft Azure** y, a continuación, haga clic en **Siguiente**.
3. Cuando se le pida, escriba el **Id. de área de trabajo** y la **Clave principal** que ha copiado en el Bloc de notas en el procedimiento anterior.

4. Haga clic en **Siguiente**. El agente comprueba que se puede conectar a Visión operativa.
5. Cuando se complete, el **Agente de administración de Microsoft** aparece en **Panel de control**.

### Para instalar el agente con la línea de comandos
- Modifique y, a continuación, use el siguiente ejemplo para instalar el agente con la línea de comandos. ```MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"```

## Configuración del agente de supervisión de Microsoft (opcional)
Use la siguiente información para que un agente se pueda comunicar directamente con el servicio de Vista operativa de Microsoft Azure. Una vez configurado el agente, se registrará con el servicio de agente y obtendrá la información de configuración necesaria y los paquetes de administración que contienen la información de la solución.

Después de recopilar datos de los equipos supervisados por el agente, el número de equipos supervisados aparecerá en el portal de Visión operativa en la pestaña **Orígenes conectados** en **Configuración** bajo **Conectar servidores directamente (64 bits)**. Para cualquier equipo que envíe los datos, puede ver sus datos y la información de la evaluación en el portal de Vista operativa.

También puede deshabilitar al agente, si es necesario o habilitarlo mediante script o línea de comandos.

### Para configurar el agente
1. Una vez instalado el **Agente de supervisión de Microsoft**, abra el **Panel de control**.
2. Abra el agente de supervisión de Microsoft y, a continuación, haga clic en la pestaña Vista operativa de Azure.
3. Seleccione **Conectar con Visión operativa de Azure**.
4. En el cuadro **Id. del área de trabajo**, pegue el identificador del área de trabajo del portal de Visión operativa.
5. En el cuadro **Clave de cuenta**, pegue la **Clave principal** del portal de Visión operativa y, a continuación, haga clic en **Aceptar**.

### Para deshabilitar un agente
1. Después de instalar el agente, abra el **Panel de control**.
2. Abra el agente de supervisión de Microsoft y, a continuación, haga clic en la pestaña **Visión operativa de Azure**.
3. Desactive **Conectarse a Visión operativa de Azure**.

### Para habilitar el agente mediante script o línea de comandos
- Puede usar Windows PowerShell o un script de VB en el siguiente ejemplo.

```powershell
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
$healthServiceSettings.ReloadConfiguration()
```

## Configuración de los ajustes del proxy y del firewall (opcional)
Si dispone de servidores proxy o firewalls en el entorno que restrinjan el acceso a Internet, es posible que tenga que seguir los procedimientos siguientes para permitir que Operations Manager o los agentes se comuniquen con el servicio de Visión operativa.

- [Configuración de los ajustes del proxy y del firewall (opcional)](operational-insights-proxy-firewall.md)

<!---HONumber=July15_HO4-->