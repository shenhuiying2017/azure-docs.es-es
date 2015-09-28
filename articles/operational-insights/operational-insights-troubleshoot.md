<properties
   pageTitle="Solución de problemas de Operational Insights"
   description="Conozca los procedimientos de solución de problemas de Operational Insights"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/10/2015"
   ms.author="banders" />

# Solución de problemas de Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Puede usar la información de las siguientes secciones para ayudarle a resolver problemas. Si el problema que tiene no se describe en este artículo, puede probar con el [blog del equipo de Visión operativa](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx).

## Solucionar problemas de permisos con la evaluación de SQL
Operations Management Suite (OMS) usa los grupos de administración de Microsoft Monitoring Agent y Operations Manager para recopilar y enviar datos al servicio OMS. Determinadas cargas de trabajo requieren privilegios específicos de carga de trabajo para ejecutar colecciones de datos en un contexto de seguridad diferente como, por ejemplo, una cuenta de dominio. Si Microsoft Monitoring Agent se conecta a través de System Center Operations Manager y se experimentan problemas con los permisos al recopilar datos, habrá que proporcionar información de credenciales mediante la configuración de una cuenta de ejecución.

Si ya usa el módulo de administración de SQL Server, debe utilizar esa cuenta de Windows como cuenta de ejecución.

### Para configurar la cuenta de ejecución de SQL en la consola de Operations, siga estos pasos:

1. En Operations Manager, abra la Consola del operador y haga clic en **Administración**.

2. En **Configuración de ejecución**, haga clic en **Perfiles** y abra el **Perfil de identificación de Microsoft System Center Advisor**.

3. En la página **Cuentas de ejecución**, haga clic en **Agregar**.

4. Seleccione una cuenta de ejecución de Windows que contenga las credenciales necesarias para SQL Server o haga clic en **Nuevo** para crear una.

    >[AZURE.NOTE]El tipo de cuenta de ejecución debe ser Windows. La cuenta de ejecución también debe ser parte del grupo de administradores locales en todos los servidores de Windows que hospedan instancias de SQL Server.

5. Haga clic en **Guardar**.

6. Modifique y luego ejecute el siguiente ejemplo de T-SQL en cada instancia de SQL Server para conceder los permisos mínimos que necesita la cuenta de ejecución para realizar la evaluación de SQL. Sin embargo, este paso no es necesario si una cuenta de ejecución ya forma parte del rol de servidor sysadmin en las instancias de SQL Server.

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

### To configure the SQL Run As account using Windows PowerShell
Alternatively, you can use the following PowerShell script to set the SQL Run As account. Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```
After the PowerShell Script finishes executing, perform the T-SQL commands provided above.

## Diagnose connection issues for Operational Insights

Because Microsoft Azure Operational Insights relies on data that is moved to and from the cloud, connection issues can be crippling. Use the following information to understand and solve your connection issues.


**Error message:** The Internet connectivity was verified, but connection to Operational Insights service could not be established. Please try again later.

**Possible causes:**
- The Operational Insights service is under maintenance. Wait until the Operational Insights maintenance is done.
- Your network has blocked Operational Insights. Contact your network administrator and request access to Operational Insights, or use another server as your gateway.

**Error message:** Internet connection could not be established. Please check your proxy settings.

**Possible causes:**
- This server is not connected to the Internet. Check the Internet connectivity status, and connect the server to the Internet.
- The proxy setting is not correct. See [Configure proxy and firewall settings](operational-insights-proxy-firewall.md) for information about how to set or change your proxy settings.
- The proxy server requires authentication. See [Configure proxy and firewall settings](operational-insights-proxy-firewall.md) to learn about how to configure Operations Manager to use a proxy server.


## Troubleshoot SQL Server discovery

If you are running Microsoft SQL Server 2008 R2, and despite deploying the Operations Manager agent, you do not see alerts for this server, you might have a discovery issue.

To confirm if this is the source of your trouble, check for the following two issues:

- In the Operations Manager event log, you see Event ID 4001. This event indicates that there is an invalid class.

- In SQL Server Configuration Manager, when you view SQL Server Services, you see the error message, “The remote procedure call failed. [0x0800706be]”

If both issues are true, you need to install SQL Server 2008 R2 Service Pack 2. To download this service pack, see [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) in the Microsoft Download Center.

After you install the service pack, you should see Operational Insights data for the server within 24 hours.

## Troubleshoot agents or Operations Manager data flow to Operational Insights

The following set of procedures is meant as a guide to help you troubleshoot your directly-connected agents or Operations Manager deployments configured to report data to Azure Operational Insights.

### Procedure 1: Validate if the right Management Packs get downloaded to your Operations Manager Environment
>[AZURE.NOTE] If you only use Direct Agent, you can skip to the next procedure.

Depending on which solutions (previously called intelligence packs) you have enabled from the OpInsights Portal will you see more or less of these MPs. Search for keyword ‘Advisor’ or ‘Intelligence’ in their name.
You can check for these MPs using OpsMgr PowerShell:

```Powershell
Get-SCOMManagementPack | donde {$\_.DisplayName -match 'Advisor'} | Seleccionar Name,Sealed,Version Obtener -SCOMManagementPack | donde {$\_.Name -match 'IntelligencePacks'} | Seleccionar Name,Sealed,Version ```

>[AZURE.NOTE]Si soluciona problemas con la solución de capacidad, compruebe *cuántos* módulos de administración tiene con la palabra ’capacity’ en el nombre: existen dos módulos de administración con el mismo nombre para mostrar (pero con distintos identificadores internos) que vienen en el mismo paquete de módulos de administración; si no es posible importar alguno de los dos (a menudo debido a una dependencia de VMM que falta), el otro módulo de administración no se importa y la operación no se vuelve a intentar.

Debe ver los tres módulos de administración siguientes relacionados con 'capacity' - Microsoft System Center Advisor Capacity Intelligence Pack - Microsoft System Center Advisor Capacity Intelligence Pack - Microsoft System Center Advisor Capacity Storage Data

Si solo ve uno o dos de ellos, pero no los tres, quítelo y espere entre cinco y diez minutos para que Operations Manager vuelva a descargarlo e importarlo; revise los registros de eventos para consultar los errores durante este periodo.

### Procedimiento 2: Validar que las soluciones correctas se descargaron en Direct Agent
>[AZURE.NOTE]Si solo usa Operations Manager, puede omitir este procedimiento.

En Direct Agent, debe ver la directiva de colección de soluciones que se almacena en caché en **C:\\Archivos de programa\\Microsoft Monitoring Agent\\Agent\\Health Service State\\Management Packs**


### Procedimiento 3: Validar que se enviaron datos al servicio Advisor (o que se intentó hacerlo)
Dependiendo de si usa Operations Manager o agentes con conexión directa, puede realizar el siguiente procedimiento en la máquina del agente directo o en el servidor de administración de Operations Manager:

1. - Abrir el "Monitor de rendimiento"
2. - Seleccionar "Grupos de administración de servicio de mantenimiento"
3. - Agregar todos los contadores que empiezan por "HTTP"

Si todo está configurado correctamente, debe ver actividad en estos contadores, a medida que se cargan eventos y otros elementos de datos (según las soluciones incorporadas al portal y la directiva de colección de registros configurada). Esos contadores no necesariamente deben estar continuamente ocupados: si ve poca o nula actividad, es posible que no esté incorporado a muchas soluciones o que tenga una directiva de colección ligera.

### Procedimiento 4: Comprobar si hay errores en los registros de eventos del servidor de administración o de Direct Agent
Como paso final, si ninguno de los pasos anteriores funciona y sigue sin ver datos recibidos por el servicio, revise si ve algún error en el **Visor de eventos**.

Abra **Visor de eventos** –> **Aplicaciones y servicios** –> **Operations Manager** y filtre por orígenes de eventos: **Advisor**, **Módulos de servicio de mantenimiento **, **HealthService** y **Conector de servicios** (este último solo se aplica a Direct Agent).

La mayoría de estos eventos sería similar en Operations Manager y en Direct Agent y los pasos para solucionar problemas también serían similares. Lo único que difiere entre Operations Manager y Direct Agent es el proceso de registro (GUI en Operations Manager, combinación de identificador/clave de área de trabajo en Direct Agent) pero, después del registro inicial, los certificados que se intercambian y usan y todo lo relacionado con la comunicación con el servicio es igual.

Por lo tanto, muchos de estos eventos se aplican a ambos tipos de infraestructura de informes. La siguiente es una lista de los eventos más comunes que podría encontrar.

### Eventos de los "Módulos de servicio de mantenimiento" de origen
##### Id. de evento 2138
Esto significa que el proxy requiere autenticación. Siga los pasos para [configurar los servidores proxy](https://msdn.microsoft.com/library/azure/dn884643.aspx).

##### Id. de evento 2137
Operations Manager no puede leer el certificado de autenticación. Volver a ejecutar el asistente para registro del asesor corregirá las cuentas RunAs/certificados

##### Id. de evento 2132
Significa **No autorizado**. Podría tratarse de un problema con el certificado y/o el registro en el servicio; intente volver a ejecutar el asistente de registro que corregirá los certificados y las cuentas RunAs. Además, compruebe que el proxy está definido para permitir exclusiones. Para obtener más información, vea [Configuración de servidores proxy](https://msdn.microsoft.com/library/azure/dn884643.aspx).

##### Id. de evento 2129
Esta es una conexión con errores debido a una negociación SSL errónea. Compruebe que los sistemas están configurados para usar TLS y no SSL. Puede que haya algunos ajustes SSL extraños en este servidor con respecto a los cifrados, ya sea en las opciones **Avanzadas** de Internet Explorer o en la clave del Registro de Windows.

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL

##### Id. de evento 2127
Código de error recibido al fallar el envío de datos. Si solo se produce de vez en cuando, podría ser simplemente un problema técnico. Esté atento para ver si se produce con frecuencia. Si el mismo error ocurre frecuentemente (cada diez minutos o más durante un periodo extendido), podría tratarse de un problema real: revise la configuración de la red, los ajustes del servidor proxy descritos anteriormente y vuelva a ejecutar el asistente de registro. Pero si solo se produce de manera esporádica (es decir, un par de veces al día), todo debiera estar bien, puesto que los datos se pondrán en cola y se volverán a transmitir. Si ocurre con esa frecuencia, probablemente no sea más que solo un tiempo de espera de la red.

Algunos de los códigos de error HTTP tienen ciertos significados especiales, por ejemplo:

- la PRIMERA vez que un servidor de administración o un agente directo de MMA intenta enviar datos a nuestro servicio, recibirá un error 500 con un código de error 404 interno (404 significa "No encontrado"). Este error indica que el área de almacenamiento que usaremos para esta área de trabajo todavía no está lista, sino que se está aprovisionando. Sin embargo, en el siguiente reintento estará lista y el flujo comenzará a funcionar (en condiciones normales).
- El código 403 podría indicar un problema de permisos/credenciales, etc.

##### Id. de evento 2128
Error en la resolución de nombres DNS. El servidor no puede resolver nuestra dirección de Internet a la que debe enviar datos. Podría tener que ver con la configuración de la resolución de DNS en su máquina, con la configuración incorrecta del proxy o con un problema (temporal) con el DNS en el proveedor. Al igual que ocurre con el evento anterior, podría indicar que se trata de un problema real o no, dependiendo de si se produce de manera constante o de vez en cuando.

##### Id. de evento 2130
Tiempo de espera. Al igual que ocurre con el evento anterior, podría indicar que se trata de un problema o no, dependiendo de si se produce de manera constante o "de vez en cuando".

### Eventos del "HealthService" de origen
##### Id. de evento 4511
No se puede cargar el módulo "System.PublishDataToEndPoint": no se encuentra el archivo. Error de inicialización de un módulo de tipo "System.PublishDataToEndPoint" (CLSID "{D407D659-65E4-4476-BF40-924E56841465}") con el código de error El sistema no encuentra el archivo especificado. Este error indica que tiene DLL antiguas en su equipo, las que no contienen los módulos requeridos. Para corregir este error, actualice los servidores de administración al paquete acumulativo de actualizaciones más reciente.

##### Id. de evento 4502
Bloqueo de módulo. Si este error aparece en flujos de trabajo con nombres tales como **CollectInstanceSpace** o **CollectTypeSpace**, puede indicar que el servidor tiene problemas para enviar algunos datos de configuración. Podría indicar que se trata de un problema o no, dependiendo de la frecuencia con que se produce: de manera constante o "de vez en cuando". Si se produce con una frecuencia mayor a una hora, definitivamente se trata de un problema. Si esta operación presenta un error solo una o dos veces al día, no existen problemas para que se realice una recuperación. Según la manera en que el módulo realmente presenta el error (la descripción mostrará más detalles), podría tratarse de un problema local (es decir, para coleccionar en la base de datos) o un problema en el envío a la nube. Compruebe la configuración de la red y del proxy y, en el peor de los casos, intente reiniciar el Servicio de mantenimiento.

##### Id. de evento 4501
Bloqueo del módulo "System.PublishDataToEndPoint". Un módulo del tipo "System.PublishDataToEndPoint" informó de un error 87L que se ejecutaba como parte de la regla "Microsoft.SystemCenter.CollectAlertChangeDataToCloud" en ejecución para la instancia "Grupo de administración de Operations Manager" con el identificador:"{6B1D1BE8-EBB4-B425-08DC-2385C5930B04}" en el grupo de administración "SCOMTEST". Ya *no* debería verlo con este flujo de trabajo, módulo y error exactos; era un [error que *ahora está corregido*](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6714689-alert-management-intelligence-pack-not-sending-ale). Si vuelve a verlo, infórmelo a través del canal de soporte técnico de Microsoft de su preferencia.


### Evento del "Conector de servicios" de origen
##### Id. de evento 4002
El servicio devolvió un código de estado HTTP 403 en respuesta a una consulta. Compruebe el estado del servicio con el administrador de servicios. La consulta se volverá a intentar más tarde. Puede recibir un código 403 durante la fase de registro inicial del agente; verá una dirección URL como, por ejemplo, https://<YourWorkspaceID>.oms.opinsights.azure.com/ AgentService.svc/AgentTopologyRequest. El código de error 403 significa "Prohibido". Normalmente se trata de una clave o identificador de área de trabajo que se copió de manera incorrecta, o bien el reloj no se encuentra sincronizado con la máquina. Intente sincronizar con un origen de hora confiable y use la comprobación de conectividad en el applet del Panel de control para que Microsoft Monitoring Agent compruebe que posee la clave y el identificador de área de trabajo correctos.


### Procedimiento 5: Comprobar que los agentes envían sus datos y están indexados en el Portal
Regístrese en el Portal de OpInsights, desde la página Información general, navegue hasta el icono pequeño **Servidores y uso**; esto mostrará si los grupos de administración (y sus agentes) y los agentes directos notifican datos en la búsqueda de registros. El número de agentes que aparece en el mosaico deriva de los datos: si las máquinas no informan durante dos semanas, dejarán el radar.

Las exploraciones en profundidad le llevarán a la búsqueda de registros y le mostrarán la marca de tiempo de los últimos datos indexados para cada máquina. Desde ahí puede explorar cuáles son los datos. Dependiendo de la cantidad configurada de recopilaciones de datos y de cuáles sean las soluciones, la programación de carga de datos y la velocidad pueden variar.

Esta página también presenta información de medición (esta no usa el índice de búsqueda de registros, sino el sistema de facturación y se actualiza cada dos horas) sobre las cantidades de datos enviados al servicio desglosado por la solución.

<!---HONumber=Sept15_HO3-->