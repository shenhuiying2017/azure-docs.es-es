<properties
	pageTitle="Conexión de equipos Windows a Log Analytics | Microsoft Azure"
	description="En este artículo, se explican los pasos necesarios para conectar los equipos Windows de la infraestructura local directamente a OMS a través de una versión personalizada de Microsoft Monitoring Agent (MMA)."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>


# Conexión de equipos Windows a Log Analytics

En este artículo, se explican los pasos necesarios para conectar los equipos Windows de la infraestructura local directamente a OMS a través de una versión personalizada de Microsoft Monitoring Agent (MMA). Deberá instalar y conectar agentes en todos los equipos que quiera incorporar a OMS para que puedan enviar datos a OMS y puedan ver esos datos y actuar sobre ellos en el portal de OMS.

Puede instalar los agentes a través del programa de instalación, a través de la línea de comandos o utilizando Configuración de estado deseado (DSC) en Automatización de Azure.

En los equipos que tengan conectividad a Internet, el agente utilizará esta conexión para enviar datos a OMS. En los equipos que no tengan conectividad a Internet, puede utilizar un servidor proxy o el reenviador de Log Analytics (OMS).

Para conectar equipos Windows a OMS, solo se requieren tres sencillos pasos:

1. Descarga del archivo de instalación del agente
2. Instalación del agente a través del método elegido
3. Configuración del agente, si es necesario

En el siguiente diagrama se muestra la relación entre los equipos Windows y OMS después de instalar y configurar los agentes.

![oms-direct-agent-diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)


## Requisitos del sistema y configuración necesaria
Antes de instalar o implementar agentes, consulte la siguiente información para asegurarse que cumple los requisitos necesarios.

- MMA de OMS solo puede instalarse en equipos con Windows Server 2008 SP 1 o versiones posteriores y en equipos con Windows 7 SP1 o versiones posteriores.
- Necesitará una suscripción de OMS. Para más información, consulte [Get started with Log Analytics](log-analytics-get-started.md) (Introducción a Log Analytics).
- Todos los equipos Windows deben tener conexión a Internet. Esta conexión puede ser directa, a través de un proxy o a través del reenviador de Log Analytics (OMS).
- Puede instalar MMA de OMS en equipos independientes, servidores y máquinas virtuales. Si quiere conectar a OMS máquinas virtuales hospedadas en Azure, consulte [Connect Azure storage to Log Analytics](log-analytics-azure-storage.md) (Conectar Almacenamiento de Azure a Log Analytics).
- El agente debe usar el puerto TCP 443 para diversos recursos. Para más información, consulte [Configure proxy and firewall settings in Log Analytics](log-analytics-proxy-firewall.md) (Configuración de servidores proxy y firewalls en Log Analytics).

## Descarga del archivo de instalación del agente desde OMS
1. En el portal de OMS, en la página **Overview** (Información general), haga clic en el icono **Settings** (Configuración). Haga clic en la pestaña **Orígenes conectados** en la parte superior. ![Pestaña Connected Sources (Orígenes conectados)](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. Para descargar el archivo de instalación, en **Attach Computers Directly** (Conectar equipos directamente), haga clic en la opción **Download Windows Agent** (Descargar agente de Windows) que corresponda al tipo de procesador de su equipo.
3. A la derecha de **Workspace ID** (Id. del área de trabajo), haga clic en el icono Copiar y pegue el identificador en el Bloc de notas.
4. A la derecha de **Primary Key** (Clave principal), haga clic en el icono Copiar y pegue la clave en el Bloc de notas.![copiar identificador del área de trabajo y clave principal](./media/log-analytics-windows-agents/oms-direct-agent-primary-key.png)

## Instalación del agente con el programa de instalación
1. Ejecute la configuración para instalar el agente en un equipo que desee administrar.
2. En la página de bienvenida, haga clic en **Siguiente**.
3. En la página Términos de licencia, lea la licencia y haga clic en **Acepto**.
4. En la página Carpeta de destino, cambie o mantenga la carpeta de instalación predeterminada y haga clic en **Siguiente**.
5. En la página Opciones de instalación del agente, puede conectar el agente a Visión operativa (OMS) o a Operations Manager. También puede dejar las opciones en blanco si prefiere configurar al agente más tarde. Haga clic en **Siguiente**.   
    - Si decide conectar el agente a Visión operativa (OMS), pegue el **identificador del área de trabajo** y la **clave del área de trabajo (clave principal)** que copió en el Bloc de notas durante el procedimiento anterior y haga clic en **Siguiente**. ![pegar identificador del área de trabajo y clave principal](./media/log-analytics-windows-agents/oms-mma-aoi-setup.png)
    - Si decide conectar el agente a Operations Manager, especifique el **nombre del grupo de administración**, el nombre del **servidor de administración** y el **puerto del servidor de administración**; después, haga clic en **Siguiente**. En la página Cuenta de acción del agente, seleccione la cuenta de sistema local o una cuenta de dominio local y haga clic en **Siguiente**. ![configuración del grupo de administración](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![cuenta de acción del agente](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. En la página Preparado para instalar, revise las opciones seleccionadas y haga clic en **Instalar**.
7. En la página La configuración finalizó correctamente, haga clic en **Finalizar**.
8. Una vez completado el proceso, el **Agente de administración de Microsoft** aparece en el **Panel de control**. Allí puede revisar la configuración y comprobar que el agente esté conectado a Visión operativa (OMS). Cuando se conecta a OMS, el agente muestra un mensaje similar al siguiente: **Microsoft Monitoring Agent se conectó correctamente al servicio Visión operativa de Azure (OMS).**

## Instalación del agente a través de la línea de comandos
- Modifique y, a continuación, use el siguiente ejemplo para instalar el agente con la línea de comandos.

    ```
    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

## Instalación del agente utilizando DSC en Automatización de Azure
1. Importe el módulo de DSC xPSDesiredStateConfiguration de [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) en Automatización de Azure.  

2.	En Automatización de Azure, cree los recursos de variable *OPSINSIGHTS\_WS\_ID* y *OPSINSIGHTS\_WS\_KEY*. Establezca *OPSINSIGHTS\_WS\_ID* en el identificador del área de trabajo de Log Analytics (OMS) y *OPSINSIGHTS\_WS\_KEY* en la clave principal del área de trabajo.
3.	Utilice el script siguiente y guárdelo como MMAgent.ps1.
4.	Después de modificar el ejemplo, úselo para instalar el agente utilizando DSC en Automatización de Azure. Importe MMAgent.ps1 en Automatización de Azure a través de la interfaz o el cmdlet de Automatización de Azure.
5.	Asigne un nodo a la configuración. Durante los 15 minutos siguientes, el nodo comprobará la configuración y MMA se insertará en el nodo.

```
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
        }

        xRemoteFile OIPackage {
            Uri = "https://opsinsight.blob.core.windows.net/publicfiles/MMASetup-AMD64.exe"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "E854571C-3C01-4128-99B8-52512F44E5E9"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}  


```


## Configuración manual de un agente
Si tiene instalados los agentes pero aún no los ha configurado o si ha realizado algún cambio en la configuración, puede usar la siguiente información para habilitarlos o volver a configurarlos. Una vez configurado el agente, se registrará con el servicio de agente y obtendrá la información de configuración necesaria y los paquetes de administración que contienen la información de la solución.

1. Una vez que Microsoft Monitoring Agent esté instalado, abra el **Panel de control**.
2. Abra **Microsoft Monitoring Agent** y después haga clic en **Connect to Azure Operational Insights (OMS)** (Conectar a Visión operativa de Azure (OMS)).   
3. Pegue el **identificador del área de trabajo** y la **clave del área de trabajo (clave principal)** que copió en el Bloc de notas durante un procedimiento anterior y haga clic en **Aceptar**. ![configurar Visión operativa](./media/log-analytics-windows-agents/oms-mma-aoi.png)

Una vez que los datos se hayan recopilado de los equipos supervisados por el agente, el número de equipos supervisados por OMS aparecerá como **Servers Connected** (Servidores conectados) en el portal de OMS, en la pestaña **Connected Sources** (Orígenes conectados) de **Settings** (Configuración).


### Para deshabilitar un agente
1. Después de instalar el agente, abra el **Panel de control**.
2. Abra Microsoft Monitoring Agent y haga clic en la pestaña **Azure Operational Insights (OMS)** (Visión operativa de Azure (OMS)).
3. Desactive **Conectarse a Visión operativa de Azure**.

## Configuración de un agente a través de la línea de comandos

- Puede usar Windows PowerShell con el ejemplo siguiente.
    ```
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableAzureOperationalInsights('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    ```

## (Opcional) Configurar los agentes para que envíen notificaciones a un grupo de administración de Operations Manager

Si utiliza Operations Manager en su infraestructura de TI, también puede usar el agente de MMA como agente de Operations Manager.

### Si desea configurar los agentes de MMA para que envíen notificaciones a un grupo de administración de Operations Manager
1.	En el equipo en el que está instalado el agente, abra el **Panel de control**.
2.	Abra **Microsoft Monitoring Agent** y haga clic en la pestaña **Operations Manager**. ![Pestaña Operations Manager de Microsoft Monitoring Agent](./media/log-analytics-windows-agents/oms-mma-om01.png)
3.	Si los servidores de Operations Manager tienen integración con Active Directory, haga clic en Actualizar automáticamente asignaciones de grupos de administración desde AD DS.
4.	Haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar un grupo de administración**. ![Agregar un grupo de administración de Microsoft Monitoring Agent](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.	En el cuadro **Nombre del grupo de administración**, especifique un nombre para el grupo de administración.
6.	En el cuadro **Servidor de administración principal**, escriba el nombre de equipo del servidor de administración principal.
7.	En el cuadro **Puerto de servidor de administración**, escriba el número de puerto TCP.
8.	En **Cuenta de acción del agente**, seleccione la cuenta de sistema local o una cuenta de dominio local.
9.	Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Agregar un grupo de administración** y después haga clic en **Aceptar** para cerrar el cuadro de diálogo **Propiedades de Microsoft Monitoring Agent**.

## (Opcional) Configurar agentes para que utilicen el reenviador de Log Analytics (OMS)

Aunque algunos de sus servidores o clientes no tengan conexión a Internet, podrán enviar datos a OMS a través del reenviador de Log Analytics (OMS). Si utiliza el reenviador, todos los datos de los agentes se envían a través de un único servidor que tiene acceso a Internet. El reenviador transfiere los datos de los agentes a OMS directamente, sin analizarlos.

Para más información sobre el reenviador, como su instalación y configuración, consulte [OMS Log Analytics Forwarder](https://blogs.technet.microsoft.com/msoms/2016/03/17/oms-log-analytics-forwarder) (Reenviador de Log Analytics de OMS).

Para más información acerca de cómo configurar los agentes para que usen un servidor proxy (que, en este caso, es el reenviador de OMS), consulte [Configure proxy and firewall settings in Log Analytics](log-analytics-proxy-firewall.md) (Configuración de servidores proxy y firewalls en Log Analytics).

## (Opcional) Configuración de servidores proxy y firewalls
Si en su entorno hay servidores proxy o firewalls que restringen el acceso a Internet, consulte [Configure proxy and firewall settings in Log Analytics](log-analytics-proxy-firewall.md) (Configuración de servidores proxy y firewalls en Log Analytics) para permitir que los agentes puedan comunicarse con el servicio de OMS.

## Pasos siguientes

- Consulte [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Incorporación de soluciones de Log Analytics desde la galería de soluciones) para agregar funcionalidad y recopilar información.
- Consulte [Configure proxy and firewall settings in Log Analytics](log-analytics-proxy-firewall.md) (Configuración del proxy y del firewall en Log Analytics) si la organización utiliza un servidor proxy o un firewall para que los agentes puedan comunicarse con el servicio Log Analytics.

<!---HONumber=AcomDC_0504_2016-->