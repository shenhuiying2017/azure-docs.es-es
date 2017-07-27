---
title: "Conexión de equipos Windows a Azure Log Analytics | Microsoft Docs"
description: "En este artículo, se explican los pasos necesarios para conectar los equipos Windows de la infraestructura local directamente al servicio Log Analytics a través de una versión personalizada de Microsoft Monitoring Agent (MMA)."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 932f7b8c-485c-40c1-98e3-7d4c560876d2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 48a0eaeb10d406d551c9e5870edde06809bd7544
ms.contentlocale: es-es
ms.lasthandoff: 07/04/2017


---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Conexión de equipos Windows al servicio Log Analytics de Azure

En este artículo, se explican los pasos necesarios para conectar los equipos Windows de la infraestructura local a áreas de trabajo de OMS a través de una versión personalizada de Microsoft Monitoring Agent (MMA). Deberá instalar y conectar agentes en todos los equipos que quiera incorporar para que puedan enviar datos al servicio Log Analytics, así como ver esos datos y actuar sobre ellos. Cada agente puede informar a varias áreas de trabajo.

Puede instalar los agentes a través del programa de instalación, a través de la línea de comandos o utilizando Configuración de estado deseado (DSC) en Automatización de Azure.  

>[!NOTE]
Para las máquinas virtuales que se ejecutan en Azure, puede simplificar la instalación con la [extensión de máquina virtual](log-analytics-azure-vm-extension.md).

En los equipos que tengan conectividad a Internet, el agente usa esta conexión para enviar datos a OMS. En los equipos que no tengan conectividad a Internet, puede usar un proxy o la [puerta de enlace de OMS](log-analytics-oms-gateway.md).

Para conectar equipos Windows a OMS, solo se necesitan tres sencillos pasos:

1. Descargar el archivo del programa de instalación del agente desde el portal de OMS
2. Instalación del agente a través del método elegido
3. Configuración del agente o incorporación de áreas de trabajo adicionales, si es necesario

En el siguiente diagrama se muestra la relación entre los equipos Windows y OMS después de instalar y configurar los agentes.

![oms-direct-agent-diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)

Si las directivas de seguridad de TI no permiten que los equipos de la red se conecten a Internet, puede configurarlos para que se conecten a la puerta de enlace de OMS. Para obtener más información y pasos sobre cómo configurar los servidores para que se comuniquen por una puerta de enlace de OMS con el servicio OMS, consulte [Conexión de equipos sin acceso a OMS mediante la puerta de enlace de OMS](log-analytics-oms-gateway.md).

## <a name="system-requirements-and-required-configuration"></a>Requisitos del sistema y configuración necesaria
Antes de instalar o implementar agentes, consulte la siguiente información para asegurarse de que cumple los requisitos.

- MMA de OMS solo puede instalarse en equipos con Windows Server 2008 SP 1 o versiones posteriores y en equipos con Windows 7 SP1 o versiones posteriores.
- Necesita una suscripción de Azure.  Para más información, consulte [Introducción a Log Analytics](log-analytics-get-started.md).
- Todos los equipos Windows necesitan tener conexión a Internet con HTTPS o la puerta de enlace de OMS. Esta conexión puede ser directa, a través de un proxy o a través de la puerta de enlace de OMS.
- Puede instalar MMA de OMS en equipos independientes, servidores y máquinas virtuales. Si quiere conectar a OMS máquinas virtuales hospedadas en Azure, consulte [Connect Azure storage to Log Analytics](log-analytics-azure-vm-extension.md)(Conectar Azure Storage a Log Analytics).
- El agente debe usar el puerto TCP 443 para diversos recursos.

### <a name="network"></a>Red

Para que los agentes de Windows se conecten y se registren con el servicio OMS, deben tener acceso a los recursos de red, incluidos los números de puerto y las direcciones URL de dominio.

- Para los servidores proxy, debe asegurarse de que los recursos de servidor proxy adecuados están configurados en la configuración del agente.
- Para firewalls que restringen el acceso a Internet, usted o el ingeniero de redes deberán configurarlo para que permita el acceso a OMS. No es necesario realizar ninguna acción en la configuración del agente.

En la siguiente tabla se muestran los recursos necesarios para la comunicación.

>[!NOTE]
>Algunos de los siguientes recursos mencionan Operational Insights, que era el nombre anterior de Log Analytics.

| Recurso del agente | Puertos | Omitir inspección de HTTPS |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Sí |
| *.oms.opinsights.azure.com | 443 | Sí |
| *.blob.core.windows.net | 443 | Sí |
| *.azure-automation.net | 443 | Sí |



## <a name="download-the-agent-setup-file-from-oms"></a>Descarga del archivo de instalación del agente desde OMS
1. En el portal de OMS, en la página **Overview** (Información general), haga clic en el icono **Settings** (Configuración).  Haga clic en la pestaña **Orígenes conectados** en la parte superior.  
    ![Pestaña Connected Sources (Orígenes conectados)](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. Haga clic en **Servidores de Windows** y, después, haga clic en **Descargar el agente de Windows** (según el tipo de procesador del equipo) para descargar el archivo del programa de instalación.
3. A la derecha de **Id. del área de trabajo**, haga clic en el icono Copiar y pegue el id. en el Bloc de notas.
4. A la derecha de **Clave principal**, haga clic en el icono Copiar y pegue la clave en el Bloc de notas.     

## <a name="install-the-agent-using-setup"></a>Instalación del agente con el programa de instalación
1. Ejecute la configuración para instalar el agente en un equipo que desee administrar.
2. En la página de bienvenida, haga clic en **Siguiente**.
3. En la página Términos de licencia, lea la licencia y haga clic en **Acepto**.
4. En la página Carpeta de destino, cambie o mantenga la carpeta de instalación predeterminada y haga clic en **Siguiente**.
5. En la página Opciones de instalación del agente, puede conectar el agente a Azure Log Analytics (OMS), Operations Manager. También puede dejar las opciones en blanco si prefiere configurar al agente más tarde. Haga clic en **Siguiente**.   
    - Si decide conectar el agente a Azure Log Analytics (OMS), pegue el **identificador del área de trabajo** y la **clave del área de trabajo (clave principal)** que copió en el Bloc de notas durante el procedimiento anterior y haga clic en **Siguiente**.  
        ![pegar identificador del área de trabajo y clave principal](./media/log-analytics-windows-agents/connect-workspace.png)
    - Si decide conectar el agente a Operations Manager, especifique el **nombre del grupo de administración**, el nombre del **servidor de administración** y el **puerto del servidor de administración**; después, haga clic en **Siguiente**. En la página Cuenta de acción del agente, seleccione la cuenta de sistema local o una cuenta de dominio local y haga clic en **Siguiente**.  
        ![configuración del grupo de administración](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![agent action account](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. En la página Preparado para instalar, revise las opciones seleccionadas y haga clic en **Instalar**.
7. En la página La configuración finalizó correctamente, haga clic en **Finalizar**.
8. Una vez completado el proceso, el **Agente de administración de Microsoft** aparece en el **Panel de control**. Allí puede revisar la configuración y comprobar que el agente esté conectado a Visión operativa (OMS). Cuando se conecta a OMS, el agente muestra un mensaje similar al siguiente: **Microsoft Monitoring Agent se conectó correctamente al servicio Microsoft Operations Management Suite.**

## <a name="configure-proxy-settings"></a>Configuración de los valores de proxy

Puede utilizar el procedimiento siguiente para configurar el proxy para Microsoft Monitoring Agent mediante el Panel de Control. Deberá usar este procedimiento para cada servidor. Si tiene muchos servidores que necesite configurar, le resultará más fácil usar un script para automatizar este proceso. Puede utilizar el procedimiento siguiente para [configurar el proxy para Microsoft Monitoring Agent mediante el Panel de Control](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script).

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-control-panel"></a>Para configurar el proxy para Microsoft Monitoring Agent mediante el Panel de control
1. Abra el **Panel de control**.
2. Abra **Microsoft Monitoring Agent**.
3. Haga clic en la pestaña **Configuración de proxy**.  
    ![Pestaña Configuración de proxy](./media/log-analytics-windows-agents/proxy-direct-agent-proxy.png)
4. Seleccione **Usar un servidor proxy** y escriba la dirección URL y el número de puerto, si uno es necesario, de forma similar al ejemplo mostrado. Si el servidor proxy requiere autenticación, escriba el nombre de usuario y la contraseña para acceder al servidor proxy.


### <a name="verify-agent-connectivity-to-oms"></a>Comprobar la conectividad del agente a OMS

Puede comprobar fácilmente si los agentes se comunican con OMS con el procedimiento siguiente:

1.  En el equipo con el agente de Windows, abra el Panel de Control.
2.  Abra Microsoft Monitoring Agent.
3.  Haga clic en la pestaña Azure Log Analytics (OMS).
4.  En la columna Estado, verá que el agente se conectó correctamente al servicio Operations Management Suite.

![agente conectado](./media/log-analytics-windows-agents/mma-connected.png)


### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script"></a>configurar el proxy para Microsoft Monitoring Agent mediante el Panel de Control
Copie el ejemplo siguiente, actualícelo con la información específica para su entorno, guárdelo con una extensión de nombre de archivo PS1 y luego ejecute el script en cada equipo que se conecta directamente al servicio de OMS.

    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

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



## <a name="install-the-agent-using-the-command-line"></a>Instalación del agente a través de la línea de comandos
- Modifique y, a continuación, use el siguiente ejemplo para instalar el agente con la línea de comandos. En el ejemplo se realiza una instalación silenciosa completa.

    >[!NOTE]
    Si desea actualizar a un agente, debe utilizar la API de scripting de Log Analytics. Consulte la sección siguiente para actualizar un agente.

    ```dos
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

El agente usa IExpress como autoextractor mediante el comando `/c`. Puede ver los modificadores de la línea de comandos en [Modificadores de la línea de comandos para los paquetes de actualización de software de IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) y, a continuación, actualizar el ejemplo para adaptarlo a sus necesidades.

|Opciones específicas de MMA                   |Notas         |
|---------------------------------------|--------------|
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Configurar el agente para informar a un área de trabajo                |
|OPINSIGHTS_WORKSPACE_ID                | Id. de área de trabajo (guid) para el área de trabajo que se agregará                    |
|OPINSIGHTS_WORKSPACE_KEY               | Clave del área de trabajo que se usa para autenticar inicialmente con el área de trabajo |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Especificar el entorno en la nube donde se encuentra el área de trabajo <br> 0 = nube comercial de Azure (valor predeterminado) <br> 1= Azure Government |
|OPINSIGHTS_PROXY_URL               | Identificador URI del proxy que se va a usar |
|OPINSIGHTS_PROXY_USERNAME               | Nombre de usuario para acceder a un proxy autenticado |
|OPINSIGHTS_PROXY_PASSWORD               | Contraseña para acceder a un proxy autenticado |

>[!NOTE]
Para evitar llegar al límite de longitud de la línea de comandos de IExpress, instale el agente sin ningún área de trabajo configurada y, después, use un script para configurar dicha área.

>[!NOTE]
Si obtiene `Command line option syntax error.` al usar el parámetro `OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE`, puede usar la siguiente solución alternativa:
```dos
MMASetup-AMD64.exe /C /T:.\MMAExtract
cd .\MMAExtract
setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
```

## <a name="add-a-workspace-using-a-script"></a>Incorporación de un área de trabajo mediante un script
Agregue un área de trabajo mediante la API de scripting del agente de Log Analytics siguiendo este ejemplo:

```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

Para agregar un área de trabajo en Azure para el gobierno de EE. UU., use el siguiente ejemplo de secuencia de comandos:
```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
Si ha usado la línea de comandos o el script anteriormente para instalar o configurar el agente, `EnableAzureOperationalInsights` se ha reemplazado por `AddCloudWorkspace`.

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Instalación del agente utilizando DSC en Automatización de Azure

Puede usar el siguiente script de ejemplo para instalar el agente mediante DSC en Azure Automation. En el ejemplo se instala el agente de 64 bits, identificado por el valor `URI`. También puede utilizar la versión de 32 bits si se reemplaza el valor del identificador URI. Estos son los identificadores URI para ambas versiones:

- Agente de Windows de 64 bits: https://go.microsoft.com/fwlink/?LinkId=828603
- Agente de Windows de 32 bits: https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
Este ejemplo de script y procedimiento no actualizará un agente existente.

1. Importe el módulo de DSC xPSDesiredStateConfiguration desde [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) en Automatización de Azure.  
2.  En Azure Automation, cree los recursos de variable *OPSINSIGHTS_WS_ID* y *OPSINSIGHTS_WS_KEY*. Establezca *OPSINSIGHTS_WS_ID* en el identificador del área de trabajo de Log Analytics de OMS y *OPSINSIGHTS_WS_KEY* en la clave principal del área de trabajo.
3.  Use el siguiente script y guárdelo como MMAgent.ps1.
4.  Después de modificar el ejemplo, úselo para instalar el agente utilizando DSC en Automatización de Azure. Importe MMAgent.ps1 en Automatización de Azure a través de la interfaz o el cmdlet de Automatización de Azure.
5.  Asigne un nodo a la configuración. Durante los 15 minutos siguientes, el nodo comprueba la configuración y MMA se inserta en el nodo.

```PowerShell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"


    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}


```

### <a name="get-the-latest-productid-value"></a>Obtención del valor de ProductId más reciente

El valor `ProductId value` del script MMAgent.ps1 es exclusivo de cada versión del agente. Cuando se publica una versión actualizada de cada agente, el valor de ProductId cambia. Por lo tanto, cuando ProductId cambie en el futuro, podrá encontrar la versión del agente mediante un sencillo script. Una vez que haya instalado la última versión del agente en un servidor de prueba, puede usar el siguiente script para obtener el valor de ProductId instalado. Con el último valor de ProductId, puede actualizar el del script MMAgent.ps1.

```PowerShell
$InstalledApplications  = Get-ChildItem hklm:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall


foreach ($Application in $InstalledApplications)

{

     $Key = Get-ItemProperty $Application.PSPath

     if ($Key.DisplayName -eq "Microsoft Monitoring Agent")

     {

        $Key.DisplayName

        Write-Output ("Product ID is: " + $Key.PSChildName.Substring(1,$Key.PSChildName.Length -2))

     }

}  
```

## <a name="configure-an-agent-manually-or-add-additional-workspaces"></a>Configuración de un agente de forma manual o incorporación de áreas de trabajo adicionales
Si tiene instalados los agentes pero aún no los ha configurado o si quiere que el agente informe a varias áreas de trabajo, puede usar la siguiente información para habilitarlos o volver a configurarlos. Una vez configurado el agente, se registrará con el servicio de agente y obtendrá la información de configuración necesaria y los paquetes de administración que contienen la información de la solución.

1. Una vez que Microsoft Monitoring Agent esté instalado, abra el **Panel de control**.
2. **Abra Microsoft Monitoring Agent** y haga clic en la pestaña **Azure Log Analytics (OMS)**.   
3. Haga clic en **Agregar** para abrir el cuadro **Add a Log Analytics Workspace** (Agregar un área de trabajo de Log Analytics).
4. Pegue el **identificador del área de trabajo** y la **clave del área de trabajo (clave principal)** que copió en el Bloc de notas durante un procedimiento anterior para el área de trabajo que desee agregar y luego haga clic en **Aceptar**.  
    ![configurar Operational Insights](./media/log-analytics-windows-agents/add-workspace.png)

Una vez que los datos se hayan recopilado de los equipos supervisados por el agente, el número de equipos supervisados por OMS aparecerá como **Servers Connected** (Servidores conectados) en el portal de OMS, en la pestaña **Connected Sources** (Orígenes conectados) de **Configuración**.


## <a name="to-disable-an-agent"></a>Para deshabilitar un agente
1. Después de instalar el agente, abra el **Panel de control**.
2. Abra Microsoft Monitoring Agent y haga clic en la pestaña **Azure Log Analytics (OMS)** .
3. Seleccione un área de trabajo y haga clic en **Quitar**. Repita este paso para el resto de áreas de trabajo.


## <a name="optionally-configure-agents-to-report-to-an-operations-manager-management-group"></a>(Opcional) Configurar los agentes para que envíen notificaciones a un grupo de administración de Operations Manager

Si utiliza Operations Manager en su infraestructura de TI, también puede usar el agente de MMA como agente de Operations Manager.

### <a name="to-configure-mma-agents-to-report-to-an-operations-manager-management-group"></a>Si desea configurar los agentes de MMA para que envíen notificaciones a un grupo de administración de Operations Manager
1.  En el equipo en el que está instalado el agente, abra el **Panel de control**.  
2.  Abra **Microsoft Monitoring Agent** y haga clic en la pestaña **Operations Manager**.  
    ![Pestaña Operations Manager de Microsoft Monitoring Agent](./media/log-analytics-windows-agents/om-mg01.png)
3.  Si los servidores de Operations Manager tienen integración con Active Directory, haga clic en **Actualizar automáticamente asignaciones de grupos de administración desde AD DS**.
4.  Haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar un grupo de administración**.  
    ![Agregar un grupo de administración de Microsoft Monitoring Agent](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.  En el cuadro **Nombre del grupo de administración** , especifique un nombre para el grupo de administración.
6.  En el cuadro **Servidor de administración principal** , escriba el nombre de equipo del servidor de administración principal.
7.  En el cuadro **Puerto de servidor de administración** , escriba el número de puerto TCP.
8.  En **Cuenta de acción del agente**, seleccione la cuenta de sistema local o una cuenta de dominio local.
9.  Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Agregar un grupo de administración** y después haga clic en **Aceptar** para cerrar el cuadro de diálogo **Propiedades de Microsoft Monitoring Agent**.


## <a name="next-steps"></a>Pasos siguientes

- [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md) para agregar funcionalidad y recopilar información.

