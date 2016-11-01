<properties
   pageTitle="Trabajos híbridos de runbook de Automatización de Azure | Microsoft Azure"
   description="Este artículo brinda información sobre cómo instalar y usar Trabajo híbrido de runbook, una característica de Automatización de Azure que le permite ejecutar runbooks en máquinas de su centro de datos local."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="bwren" />


# <a name="azure-automation-hybrid-runbook-workers"></a>Trabajos híbridos de runbook de Automatización de Azure

Los runbooks de Automatización de Azure no pueden tener acceso a los recursos de su centro de datos local, debido a que se ejecutan en la nube de Azure.  La característica Trabajo híbrido de runbook de Automatización de Azure permite ejecutar runbooks en máquinas ubicadas en su centro datos, para así poder administrar los recursos locales. Los runbooks se almacenan y administran en Automatización de Azure y después se entregan a una o más máquinas locales.  

Esta funcionalidad se ilustra en la imagen siguiente.<br>  

![Información general de Trabajo híbrido de runbook](media/automation-hybrid-runbook-worker/automation.png)

Puede designar uno o más equipos del centro de datos para que actúen como un trabajo híbrido de runbook y ejecuten runbooks desde Automatización de Azure.  Cada trabajo requiere el agente de administración de Microsoft con una conexión a Microsoft Operations Management Suite y al entorno de runbooks de Automatización de Azure.  Operations Management Suite solo se usa para instalar y mantener el agente de administración y para supervisar la funcionalidad del trabajo.  Automatización de Azure realiza la entrega de los runbooks y la instrucción para ejecutarlos.

No hay ningún requisito de firewall de entrada para admitir Trabajos híbridos de runbook. El agente que se encuentra en el equipo local inicia toda la comunicación con Automatización de Azure en la nube. Cuando se inicia un runbook, Automatización de Azure crea una instrucción que el agente recupera. El agente baja el runbook y todos los parámetros antes de ejecutarlo.  También recuperará todos los [recursos](http://msdn.microsoft.com/library/dn939988.aspx) que usa el runbook desde Automatización de Azure.

>[AZURE.NOTE] Los trabajo híbridos de runbooks no admiten actualmente [configuraciones de DSC](automation-dsc-overview.md).


## <a name="hybrid-runbook-worker-groups"></a>Grupos de Trabajos híbridos de runbook

Cada Trabajo híbrido de runbook es miembro de un grupo de Trabajos híbridos de runbook que especifica cuando instala el agente.  Un grupo puede incluir solo un agente, pero puede instalar varios agentes en un grupo para contar con alta disponibilidad.

Cuando se inicia un runbook en un Trabajo híbrido de runbook, se especifica el grupo en el que se ejecutará.  Los miembros del grupo determinarán qué trabajo no atiende a la solicitud.  No es posible especificar un trabajo determinado.

## <a name="hybrid-runbook-worker-requirements"></a>Requisitos de Hybrid Runbook Worker

Debe designar al menos un equipo local para ejecutar trabajos híbridos de Runbook.  Este equipo debe tener lo siguiente:

- Windows Server 2012 o superior
- Windows PowerShell 4.0 o posterior.
- Mínimo de dos núcleos y 4 GB de RAM

Tenga en cuenta las siguientes recomendaciones para los Hybrid Worker: 

- Designe varios Hybrid Worker en cada grupo para una alta disponibilidad.  
- Los Hybrid Worker pueden coexistir con servidores de runbooks de Service Management Automation o System Center Orchestrator.
- Considere la posibilidad de usar un equipo ubicado físicamente en la región de su cuenta de Automation, o cerca de ella, puesto que los datos del trabajo se envían a Azure Automation cuando se completa un trabajo.


>[AZURE.NOTE] Actualmente la versión 7.2.11136.0 de Hybrid Runbook Worker solo admite la comunicación mediante un servidor proxy con scripts de PowerShell.  La compatibilidad con scripts de flujo de trabajo de PowerShell estará disponible en una futura versión.  

### <a name="configure-proxy-and-firewall-settings"></a>Configuración del proxy y el firewall

Para que la instancia de Hybrid Runbook Worker local se conecte al servicio Microsoft Operations Management Suite (OMS) y se registre en él, debe tener acceso al número de puerto y a las direcciones URL que se describen a continuación.  Y esto aparte de los [puertos y las direcciones URL necesarios para que Microsoft Monitoring Agent](../log-analytics/log-analytics-proxy-firewall.md#configure-proxy-and-firewall-settings-with-the-microsoft-monitoring-agent) se conecte a OMS. Si se usa un servidor proxy para realizar la comunicación entre el agente y el servicio de OMS, deberá asegurarse de que es posible tener acceso a los recursos adecuados. Si usa un firewall para restringir el acceso a Internet, deberá configurarlo para que permita el acceso. 

A continuación se indican los puertos y las direcciones URL que son necesarios para que Hybrid Runbook Worker se comunique con Automatización.

- Puerto: solo se requiere el puerto TCP 443 para el acceso a Internet
- URL global: *.azure-automation.net

Si tiene una cuenta de Automatización definida para una región específica y quiere restringir la comunicación con ese dentro de datos regional, en la tabla siguiente se proporciona el registro DNS para cada región.

|**Región**|**Registro DNS**|
|--------------|--------------|
|Centro-Sur de EE. UU|scus-jobruntimedata-prod-su1.azure-automation.net|
|Este de EE. UU. 2|eus2-jobruntimedata-prod-su1.azure-automation.net|
|Europa occidental|we-jobruntimedata-prod-su1.azure-automation.net|
|Europa del Norte|ne-jobruntimedata-prod-su1.azure-automation.net|
|Centro de Canadá|cc-jobruntimedata-prod-su1.azure-automation.net|
|Sudeste de Asia|sea-jobruntimedata-prod-su1.azure-automation.net|
|India Central|cid-jobruntimedata-prod-su1.azure-automation.net|
|Este de Japón|jpe-jobruntimedata-prod-su1.azure-automation.net|
|Sudeste de Australia|ase-jobruntimedata-prod-su1.azure-automation.net|


## <a name="installing-hybrid-runbook-worker"></a>Instalación de Trabajo híbrido de runbook

El siguiente procedimiento describe cómo instalar y configurar Hybrid Runbook Worker.  Realice los dos primeros pasos una vez para su entorno de Automatización y después repita los pasos restantes en cada equipo de trabajo.

### <a name="1.-create-operations-management-suite-workspace"></a>1. Creación del área de trabajo de Operations Management Suite

Si todavía no tiene un espacio de trabajo de Operations Management Suite, puede crear uno siguiendo las instrucciones que aparecen en [Configuración del área de trabajo](https://technet.microsoft.com/library/mt484119.aspx). Si cuenta con un área de trabajo existente, puede usarla.

### <a name="2.-add-automation-solution-to-operations-management-suite-workspace"></a>2. Adición de soluciones de Automatización de área de trabajo de Operations Management Suite

Las soluciones agregan funcionalidad a Operations Management Suite.  La solución de Automatización agrega funcionalidad a Automatización de Azure, incluida la compatibilidad con Hybrid Runbook Worker.  Cuando se agrega la solución al área de trabajo, se insertarán automáticamente los componentes de trabajo al equipo del agente que va a instalar en el paso siguiente.

Siga las instrucciones de [Para agregar una solución mediante la Galería de soluciones](../log-analytics/log-analytics-add-solutions.md) para agregar la solución de **Automatización** al área de trabajo de Operations Management Suite.

### <a name="3.-install-the-microsoft-monitoring-agent"></a>3. Instalación de Microsoft Monitoring Agent

El agente de Microsoft Monitoring Agent conecta los equipos a Operations Management Suite.  Cuando instala el agente en el equipo local y se conecte al espacio de trabajo, se descargarán automáticamente los componentes necesarios para Hybrid Runbook Worker.

Siga las instrucciones que se encuentran en [Conexión de equipos Windows a Log Analytics](../log-analytics/log-analytics-windows-agents.md) para instalar el agente en el equipo local.  Puede repetir este proceso para varios equipos para agregar varios trabajos a su entorno.

Cuando el agente se conecta correctamente a Operations Management Suite, se enumerará en la pestaña **Orígenes conectados** del panel **Configuración** de Operations Management Suite.  Puede comprobar que el agente ha descargado correctamente la solución Automation cuando tiene una carpeta llamada **AzureAutomationFiles** en C:\Program Files\Microsoft Monitoring Agent\Agent.  Para confirmar la versión de Hybrid Runbook Worker, puede ir a C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ y examinar la subcarpeta \\*version*.   


### <a name="4.-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Instalación del entorno de runbook y conexión con Automatización de Azure

Cuando agrega un agente a Operations Management Suite, la solución Automation inserta el módulo **HybridRegistration** de PowerShell que contiene el cmdlet **Add-HybridRunbookWorker**.  Este cmdlet se usa para instalar el entorno de runbook en el equipo y registrarlo con Automatización de Azure.

Abra una sesión de PowerShell en modo de Administrador y ejecute el comando siguientes para importar el módulo.

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

A continuación, ejecute el cmdlet **Add-HybridRunbookWorker** con la siguiente sintaxis:

    Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>

Puede obtener la información necesaria para este cmdlet en la hoja **Administrar claves** de Azure Portal.  Puede abrir esta hoja con un clic en el icono de clave en el panel Elementos de la cuenta de Automatización.

![Información general de Trabajo híbrido de runbook](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

- **Name** es el nombre del grupo de Trabajos híbridos de runbook. Si este grupo ya existe en la cuenta de automatización, se le agregará el equipo actual.  Si todavía no existe, se agregará.
- **EndPoint** es el campo **URL** de la hoja **Administrar claves**.
- **Token** es la **Clave de acceso primaria** en la hoja **Administrar claves**.  

Use el modificador **-Verbose** con **Add-HybridRunbookWorker** para recibir información detallada sobre la instalación.

### <a name="5.-install-powershell-modules"></a>5. Instalación de módulos PowerShell

Los runbooks pueden usar cualquiera de las actividades y los cmdlets definidos en los módulos instalados en el entorno de Automatización de Azure.  Estos módulos no se implementan automáticamente en equipos locales, por lo que debe instalarlos de forma manual.  La excepción es el módulo de Azure que se instala de manera predeterminada y brinda acceso a los cmdlets para todos los servicios y actividades de Azure para Automatización de Azure.

Debido a que el propósito principal de la característica Trabajo híbrido de runbook es administrar recursos locales, es muy probable que deba instalar los módulos que admiten estos recursos.  Puede consultar [Instalación de módulos](http://msdn.microsoft.com/library/dd878350.aspx) para más información sobre cómo instalar módulos de Windows PowerShell.

## <a name="removing-hybrid-runbook-worker"></a>Eliminación de Hybrid Runbook Worker

Puede quitar uno o más Hybrid Runbook Worker de un grupo o puede quitar el grupo, dependiendo de sus requisitos.  Para quitar un Hybrid Runbook Worker de un equipo local, abra una sesión de PowerShell en modo de administrador y ejecute el comando siguiente: **Remove-HybridRunbookWorker**.  Use el modificador **-Verbose** para ver un registro detallado del proceso de eliminación. 

Con esto no se quita Microsoft Monitoring Agent del equipo, sino solo la funcionalidad y la configuración del rol Hybrid Runbook Worker.  

Para quitar un grupo, primero debe quitar el Hybrid Runbook Worker de todos los equipos que sean miembro del grupo mediante el comando mostrado anteriormente y luego realizar los pasos siguientes para quitar el grupo.  

1. Abra la cuenta de Automatización en el Portal de Azure.
2. Seleccione el icono **Grupos de Hybrid Worker** y en la hoja **Grupos de Hybrid Worker**, seleccione el grupo que quiere eliminar.  Tras seleccionar el grupo específico, se muestra la hoja de propiedades de **Grupo de Hybrid Worker**.<br> ![Hoja Grupo de Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. En la hoja de propiedades del grupo seleccionado, haga clic en **Eliminar**.  Aparecerá un mensaje solicitándole que confirme esta acción. Si está seguro de que quiere continuar, seleccione **Sí**.<br> ![Cuadro de diálogo de confirmación de eliminación de grupo](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> Este proceso puede tardar varios segundos en completarse y puede realizar el seguimiento de su progreso en **Notificaciones** en el menú.  

## <a name="starting-runbooks-on-hybrid-runbook-worker"></a>Inicio de runbooks en Trabajo híbrido de runbook

[Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md) describe los distintos métodos para iniciar un runbook.  Trabajo híbrido de runbook agrega una opción **Ejecutar en** donde puede especificar el nombre de un grupo de Trabajos híbridos de runbook.  Si se especifica un grupo, los trabajos de ese grupo recuperan y ejecutan el runbook.  Si no se especifica esta opción, se ejecuta en Automatización de Azure de la manera normal.

Cuando inicia un Runbook en Azure Portal, verá una opción **Ejecutar en** donde puede seleccionar **Azure** o **Hybrid Worker**.  Si selecciona **Trabajo híbrido**, puede seleccionar el grupo en una lista desplegable.

Use el parámetro **RunOn**. Podría usar el comando siguiente para iniciar un Runbook llamado Test-Runbook en un grupo de Hybrid Runbook Worker denominado MyHybridGroup con Windows PowerShell.

    Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

>[AZURE.NOTE] El parámetro **RunOn** se agregó al cmdlet **Start-AzureAutomationRunbook** en la versión 0.9.1 de Microsoft Azure PowerShell.  Si tiene instalada una versión anterior, deberá [descargar la versión más reciente](https://azure.microsoft.com/downloads/) .  Solo necesita instalar esta versión en una estación de trabajo donde iniciará el runbook desde Windows PowerShell.  No es necesario que la instale en el equipo de trabajo, a menos que tenga la intención de iniciar runbooks desde ese equipo.  Actualmente no puede iniciar un runbook en un Trabajo híbrido de runbook desde otro runbook, debido a que esto requeriría que la versión más reciente de PowerShell de Azure esté instalada en su cuenta de Automatización.  La versión más reciente se actualizará automáticamente en Automatización de Azure y pronto se insertará de manera automática en los trabajos.

## <a name="runbook-permissions"></a>Permisos de runbooks

Los runbooks que se ejecutan en una instancia de Hybrid Runbook Worker no pueden usar el mismo [método que se usa normalmente para los runbooks que se autentican en los recursos de Azure](automation-configuring.md#configuring-authentication-to-azure-resources) , debido a que tendrán acceso a recursos fuera de Azure.  El runbook puede proporcionar su propia autenticación a los recursos locales, o puede especificar una cuenta RunAs para proporcionar un contexto de usuario para todos los runbooks.

### <a name="runbook-authentication"></a>Autenticación de runbook

De forma predeterminada, los Runbooks se ejecutarán en el contexto de la cuenta de sistema local en el equipo local, por lo que deben proporcionar su propia autenticación a los recursos a los que tienen acceso.  

Puede usar los recursos [redencial](http://msdn.microsoft.com/library/dn940015.aspx) y [ertificado](http://msdn.microsoft.com/library/dn940013.aspx) en el Runbook con cmdlets que le permitan especificar las credenciales, para que así pueda autenticarse en distintos recursos.  El ejemplo siguiente muestra una porción de un runbook que reinicia un equipo.  Recupera las credenciales desde un recurso de Credencial y el nombre del equipo desde un recurso de Variable y, a continuación, usa estos valores con el cmdlet Restart-Computer.

    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
    $Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

    Restart-Computer -ComputerName $Computer -Credential $Cred

También puede aprovechar [InlineScript](automation-powershell-workflow.md#inline-script), que le permitirá ejecutar bloques de código en otro equipo con credenciales especificadas por el parámetro común [PSCredential](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="runas-account"></a>Cuenta RunAs

En lugar de hacer que los runbooks proporcionen su propia autenticación a los recursos locales, puede especificar una cuenta **RunAs** para un grupo de Hybrid Worker.  Especifique un [recurso de credencial](automation-credentials.md) que tenga acceso a los recursos locales y todos los runbooks se ejecutarán con estas credenciales cuando se ejecuten en una instancia de Hybrid Runbook Worker del grupo.  

El nombre de usuario de la credencial debe tener uno de los siguientes formatos:

- dominio\nombre de usuario 
- username@domain
- nombre de usuario (para cuentas locales en el equipo local)


Utilice el procedimiento siguiente para especificar una cuenta RunAs de un grupo de Hybrid Worker:

1. Cree un [recurso de credencial](automation-credentials.md) con acceso a los recursos locales.
2. Abra la cuenta de Automatización en el Portal de Azure.
2. Seleccione el icono **Grupos de Hybrid Worker** y luego seleccione el grupo.
3. Seleccione **Toda la configuración** y luego **Configuración del grupo de Hybrid Worker**.
4. Cambie **Ejecutar como** de **Predeterminado** a **Personalizado**.
5. Seleccione la credencial y haga clic en **Guardar**.


## <a name="creating-runbooks-for-hybrid-runbook-worker"></a>Creación de runbooks para Trabajo híbrido de runbook

No hay ninguna diferencia en la estructura de runbooks que se ejecutan en Automatización de Azure y los que se ejecutan en un Trabajo híbrido de runbook. Los runbooks que usa con cada uno de ellos probablemente sean muy distintos entre sí, debido a que los runbooks para Trabajo híbrido de runbook normalmente administrarán los recursos locales de su centro de datos, mientras que los runbooks en Automatización de Azure normalmente administrarán recursos en la nube de Azure. 

Puede editar un runbook para Trabajo híbrido de runbook en Automatización de Azure, pero es probable que tenga dificultades si intenta probar el runbook en el editor.  Es posible que los módulos de PowerShell que tienen acceso a los recursos locales no estén instalados en el entorno de Automatización de Azure; si este es el caso, la prueba presentará errores.  Si instala los módulos necesarios, se ejecutará el runbook, pero no podrá tener acceso a los recursos locales para una prueba completa.

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Solución de problemas de runbooks en Hybrid Runbook Worker

[La salida y los mensajes de runbooks](automation-runbook-output-and-messages.md) se envían a Automatización de Azure desde los trabajos híbridos igual que los trabajos de runbook que se ejecutan en la nube.  También puede habilitar los flujos Detallado y Progreso de la misma manera que haría para otros runbooks.  

Los registros se almacenan localmente en cada Hybrid Worker en C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.

Si sus runbooks no están finalizando correctamente y el resumen del trabajo muestra el estado **Suspendido**, consulte el artículo de solución de problemas [Hybrid Runbook Worker: un trabajo de Runbook finaliza con el estado Suspendido](automation-troubleshooting-hrw-runbook-terminates-suspended.md).   

## <a name="relationship-to-service-management-automation"></a>Relación con Service Management Automation

[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx)permite ejecutar los mismos Runbooks compatibles con Azure Automation en su centro de datos local. SMA normalmente se implementa junto con Microsoft Azure Pack, dado que Microsoft Azure Pack contiene una interfaz gráfica para la administración de SMA. A diferencia de Azure Automation, SMA requiere una instalación local que incluye servidores web para hospedar la API, una base de datos para que contenga la configuración de SMA y de los Runbooks y Runbook Workers para ejecutar los trabajos de Runbook. Automatización de Azure proporciona estos servicios en la nube y solo requiere que mantenga los Trabajos híbridos de runbook en su entorno local.

Si es un usuario existente de SMA, puede mover los runbooks a Automatización de Azure para usarlos con Trabajo híbrido de runbook sin cambios, suponiendo que realizan su propia autenticación a recursos, tal como se describe en [Creación de runbooks para Trabajo híbrido de runbook](#creating-runbooks-for-hybrid-runbook-worker).  Los runbooks en SMA se ejecutan en el contexto de la cuenta de servicio en el servidor de trabajo, lo que puede proporciona esa autenticación para los runbooks.

Puede usar los criterios siguientes para determinar si Automatización de Azure con Trabajo híbrido de runbook o Service Management Automation es más adecuado para sus requisitos.

- SMA requiere la instalación local de sus componentes subyacentes que están conectados a Microsoft Azure Pack, en caso de que sea necesaria una interfaz gráfica de administración. Se requerirán más recursos locales con costos de mantenimiento más elevados que Azure Automation, que solo necesita un agente instalado en Runbooks Workers locales. Operations Management Suite administra los agentes, con lo que disminuyen los costos de mantenimiento.
- Azure Automation almacena sus Runbooks en la nube y los entrega a Hybrid Runbook Worker locales. Si su directiva de seguridad no permite este comportamiento, deberá usar SMA.
- SMA se incluye con System Center; y por tanto, requiere una licencia de System Center 2012 R2. Azure Automation se basa en un modelo de suscripción en niveles.
- Presenta características avanzadas, como Runbooks gráficos que no están disponibles en SMA.


## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre los distintos métodos que se pueden utilizar para iniciar un runbook, consulte [Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md)
- Para entender los diferentes procedimientos para trabajar con runbooks de PowerShell y de flujo de trabajo de PowerShell en Automatización de Azure mediante el editor de texto, consulte [Edición de runbooks de texto en Automatización de Azure](automation-edit-textual-runbook.md)

 



<!--HONumber=Oct16_HO2-->


