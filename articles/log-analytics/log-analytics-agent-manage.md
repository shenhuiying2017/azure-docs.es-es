---
title: Administrar el agente de Azure Log Analytics | Microsoft Docs
description: "En este artículo se describen las diferentes tareas de administración que normalmente realizará durante el ciclo de vida de Microsoft Monitoring Agent (MMA) implementado en una máquina."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2018
ms.author: magoedte
ms.openlocfilehash: 2e4daebf18d5edeba92bc14d5a4f699fbd2d94ce
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Administrar y mantener el agente de Log Analytics para Windows y Linux

Después de la implementación inicial del agente de Windows o Linux para Log Analytics, debe volver a configurar el agente o quitarlo del equipo si alcanzó la fase de retirada de su ciclo de vida.  Puede administrar fácilmente estas tareas de mantenimiento rutinarias manualmente o de manera automática, lo que reduce los errores de funcionamiento y los gastos.

## <a name="adding-or-removing-a-workspace"></a>Adición o eliminación de un área de trabajo 

### <a name="windows-agent"></a>Agente de Windows

#### <a name="update-settings-from-control-panel"></a>Actualizar la configuración del Panel de control

1. Inicie sesión en el equipo con una cuenta con derechos administrativos.
2. Abra el **Panel de control**.
3. Seleccione **Microsoft Monitoring Agent** y haga clic en la pestaña **Azure Log Analytics (OMS)**.
4. Si quiere eliminar un área de trabajo, selecciónela y, después, haga clic en **Quitar**. Repita este paso para cualquier otra área de trabajo de la que quiera que el agente deje de informar.
5. Si agrega un área de trabajo, haga clic en **Agregar** y en el cuadro de diálogo **Agregar área de trabajo de Log Analytics**, pegue el identificador del área de trabajo y la clave del área de trabajo (clave principal). Si el equipo tiene que notificar a un área de trabajo de Log Analytics en Azure Government Cloud, seleccione Azure Gobierno de EE.UU. de la lista desplegable Azure Cloud. 
6. Haga clic en **Aceptar** para guardar los cambios.

#### <a name="remove-a-workspace-using-powershell"></a>Quitar un área de trabajo con PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Agregar un área de trabajo en Azure Commercial con PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Agregar un área de trabajo en Azure for US Government con PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Si ha usado la línea de comandos o el script anteriormente para instalar o configurar el agente, `EnableAzureOperationalInsights` se ha reemplazado por `AddCloudWorkspace` y `RemoveCloudWorkspace`.
>

## <a name="update-proxy-settings"></a>Actualizar la configuración de proxy 
Para configurar el agente para comunicarse con el servicio a través de un servidor proxy o [puerta de enlace de OMS](log-analytics-oms-gateway.md) después de la implementación, use uno de los métodos siguientes para completar esta tarea.

### <a name="windows-agent"></a>Agente de Windows

#### <a name="update-settings-using-control-panel"></a>Actualizar la configuración con Panel de control

1. Inicie sesión en el equipo con una cuenta con derechos administrativos.
2. Abra el **Panel de control**.
3. Seleccione **Microsoft Monitoring Agent** y haga clic en la pestaña **Configuración de proxy**.
4. Haga clic en **Usar un servidor proxy** y proporcione la dirección URL y el número de puerto del servidor proxy o puerta de enlace. Si el servidor proxy o la puerta de enlace de OMS requiere autenticación, escriba el nombre de usuario y la contraseña para autenticar y, después, haga clic en **Aceptar**. 

#### <a name="update-settings-using-powershell"></a>Actualizar la configuración con PowerShell 

Copie el código de PowerShell de ejemplo siguiente, actualícelo con información específica de su entorno y guárdelo con una extensión de nombre de archivo PS1.  Ejecute el script en cada uno de los equipos que se conecta directamente con el servicio Log Analytics.

```PowerShell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object.  We need to determine if we
#have the right update rollup with the API we need.  If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
     Write-Output 'Health Service proxy API not present, will not update settings.'
     return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```  

### <a name="linux-agent"></a>Agente Linux
Siga estos pasos si los equipos Linux necesitan comunicarse a través de un servidor proxy o puerta de enlace de OMS con Log Analytics.  El valor de configuración de proxy tiene la siguiente sintaxis: `[protocol://][user:password@]proxyhost[:port]`.  La propiedad *proxyhost* acepta un nombre de dominio completo o la dirección IP del servidor proxy.

1. Edite el archivo `/etc/opt/microsoft/omsagent/proxy.conf`. Para ello, ejecute los comandos siguientes y cambie los valores según su configuración específica.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. Ejecute el comando siguiente para reiniciar el agente: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="uninstall-agent"></a>Desinstalar agente
Utilice uno de los procedimientos siguientes para desinstalar al agente de Windows o Linux mediante el asistente para instalación o la línea de comandos.

### <a name="windows-agent"></a>Agente de Windows

#### <a name="uninstall-from-control-panel"></a>Desinstalar desde el Panel de control
1. Inicie sesión en el equipo con una cuenta con derechos administrativos.  
2. En **Panel de control**, haga clic en **Programas y características**.
3. En **Programas y características**, haga clic en **Microsoft Monitoring Agent**, haga clic en **Desinstalar** y, después, haga clic en **Sí**.

>[!NOTE]
>También se puede ejecutar el asistente para instalación del agente haciendo doble clic en **MMASetup-<platform>.exe**, que está disponible para su descarga desde un área de trabajo en Azure Portal.

#### <a name="uninstall-from-the-command-line"></a>Desinstalar desde la línea de comandos
El archivo descargado para el agente es un paquete de instalación independiente creado con IExpress.  El programa de instalación para el agente y los archivos auxiliares se encuentran en el paquete y deben extraerse para realizar la desinstalación correctamente con la línea de comandos que se muestra en el ejemplo siguiente. 

1. Inicie sesión en el equipo con una cuenta con derechos administrativos.  
2. Para extraer los archivos de instalación del agente, desde un símbolo del sistema con privilegios elevados ejecute `extract MMASetup-<platform>.exe` y se le solicitará la ruta en la que extraer los archivos.  Como alternativa, puede especificar la ruta de acceso pasando los argumentos `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`.  Para obtener más información acerca de los modificadores de la línea de comandos compatibles con IExpress, consulte [Command-line switches for IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) (Modificadores de la línea de comandos para IExpress) y, después, actualice el ejemplo para adaptarlo a sus necesidades.
3. En el símbolo del sistema, escriba:`%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.  

### <a name="linux-agent"></a>Agente Linux
Para quitar el agente, ejecute el siguiente comando en el equipo Linux.  El argumento *--purge* quita completamente el agente y su configuración.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Configurar el agente para que envíe notificaciones a un grupo de administración de Operations Manager

### <a name="windows-agent"></a>Agente de Windows
Realice los pasos siguientes para configurar al agente OMS para Windows para informar a un grupo de administración de System Center Operations Manager. 

1. Inicie sesión en el equipo con una cuenta con derechos administrativos.
2. Abra el **Panel de control**. 
3. Haga clic en **Microsoft Monitoring Agent** y, después, en la pestaña **Operations Manager**.
4. Si los servidores de Operations Manager tienen integración con Active Directory, haga clic en **Actualizar automáticamente asignaciones de grupos de administración desde AD DS**.
5. Haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar un grupo de administración**.
6. En el campo **Nombre del grupo de administración**, especifique un nombre para el grupo de administración.
7. En el campo **Servidor de administración principal**, escriba el nombre de equipo del servidor de administración principal.
8. En el campo **Puerto de servidor de administración**, escriba el número de puerto TCP.
9. En **Cuenta de acción del agente**, seleccione la cuenta de sistema local o una cuenta de dominio local.
10. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Agregar un grupo de administración** y después haga clic en **Aceptar** para cerrar el cuadro de diálogo **Propiedades de Microsoft Monitoring Agent**.

### <a name="linux-agent"></a>Agente Linux
Realice los pasos siguientes para configurar al agente OMS para Linux para informar a un grupo de administración de System Center Operations Manager. 

1. Edite el archivo `/etc/opt/omi/conf/omiserver.conf`
2. Asegúrese de que la línea que comienza con `httpsport=` define el puerto 1270. Por ejemplo: `httpsport=1270`
3. Reinicie el servidor OMI: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>pasos siguientes

Consulte [Troubleshooting the Linux agent](log-analytics-agent-linux-support.md) (Solución de problemas del agente Linux) si encuentra problemas durante la instalación del agente o al administrarlo.  