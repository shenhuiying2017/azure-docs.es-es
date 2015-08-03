<properties
	pageTitle="Seguridad de Visión operativa"
	description="Obtenga información sobre cómo Visión operativa protege su privacidad y sus datos."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="dev-center-name"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="banders"/>

# Seguridad de Visión operativa

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft se compromete a proteger su privacidad y sus datos, al tiempo que ofrece software y servicios que le ayuden a administrar la infraestructura de TI de su organización. Reconocemos que cuando confía sus datos a otros usuarios, esa confianza requiere una seguridad rigurosa. Microsoft se adhiere a instrucciones estrictas de seguridad y cumplimiento de normas, desde la codificación hasta la operación de un servicio.

La seguridad y la protección de los datos son prioritarias en Microsoft. Póngase en contacto con nosotros si tiene preguntas, sugerencias o problemas acerca de la siguiente información, incluidas nuestras directivas de seguridad en [Opciones de soporte técnico de Azure](http://azure.microsoft.com/support/options/):

En este artículo se explica cómo se recopilan los datos, se procesan y se protegen en Visión operativa de Microsoft Azure. Puede usar cualquiera de los agentes para conectarse directamente con el servicio web o puede usar System Center Operations Manager para recopilar datos operativos para el servicio de Visión operativa. Los datos recopilados se envían a través de Internet para el servicio de Visión operativa, que se hospeda en Microsoft Azure.

El servicio de Visión operativa administra sus datos de forma segura mediante el uso de los siguientes métodos:

**Segregación de datos:** los datos de cliente se mantienen separados de forma lógica en cada componente en el servicio de Visión operativa. Todos los datos se etiquetan por organización. Este etiquetado persiste a lo largo del ciclo de vida de los datos y se aplica en cada nivel del servicio.

Cada cliente tiene un blob de Azure dedicado que aloja los datos a largo plazo. El blob se cifra con claves por cliente únicas, que se cambian cada 90 días.

**Retención de datos:** las métricas agregadas de algunas de las soluciones (llamadas anteriormente módulos de inteligencia), como Capacity Management, se almacenan en una Base de datos SQL hospedada por Microsoft Azure. Estos datos se almacenan durante 390 días. Los datos indizados de búsqueda de registro se almacenan y se conservan conforme al plan de precios. Para obtener más información, busque en la [página de precios](http://azure.microsoft.com/pricing/details/operational-insights/).

**Seguridad física:** el servicio de Visión operativa es atendido por el personal de Microsoft y todas las actividades se registran y se pueden auditar. El servicio de Visión operativa se ejecuta completamente en Azure y cumple los criterios de ingeniería comunes de Azure. Puede ver detalles acerca de la seguridad física de los recursos de Azure en la página 18 de la [Información general de la seguridad de Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf).

**Cumplimiento y certificaciones**: el equipo de servicio y desarrollo de software de Visión operativa trabaja de manera activa con los equipos y cumplimiento y servicio legal de Microsoft y otros socios del sector para adquirir una variedad de certificaciones, incluida ISO.

Actualmente cumplimos los siguientes estándares de seguridad:

- Criterios de ingeniería comunes de Windows
- Certificación de Microsoft Trustworthy Computing


## Seguridad del flujo de datos
El diagrama siguiente muestra el flujo de información proveniente de su empresa y cómo se protege a medida que se mueve al servicio de Visión operativa y al que, en última instancia, ve usted en Visión operativa. Después del diagrama aparece más información acerca de cada paso.

![Imagen de la seguridad y recopilación de datos de Visión operativa](./media/operational-insights-security/security.png)

### 1. Suscripción para Visión operativa y recopilación de datos

Para que su organización envíe datos al servicio de Visión operativa, deberá configurar agentes de Microsoft Monitoring cuando se conecte directamente al servicio web o usar un asistente de configuración en la consola de operaciones en Operations Manager. Los usuarios (que pueden ser usted, otros usuarios individuales o un grupo de personas) deben crear una o más cuentas de Visión operativa y registrar cada agente directamente conectado o su entorno de Operations Manager, mediante el uso de una de las siguientes cuentas:


- [Id. de organización](../sign-up-organization.md)

- [Cuenta de Microsoft: Outlook, Office Live, MSN](../sign-up-organization.md)

Una cuenta de Visión operativa es donde se recopilan, agregan, analizan y presentan los datos. Una cuenta de Visión operativa se usa principalmente como un medio para realizar la partición de datos y cada cuenta de Visión operativa es única. Por ejemplo, es posible que desee que sus datos de producción se administren con una cuenta de Visión operativa y que los datos de prueba se administren con otra cuenta. Las cuentas también ayudan a un administrador a controlar el acceso de los usuarios a los datos. Cada cuenta de Visión operativa puede tener varias cuentas de usuario asociadas y cada cuenta de usuario puede tener varias cuentas de Visión operativa.

Una vez que se finaliza el asistente de configuración, cada grupo de administración de Operations Manager establece una conexión con el servicio de Visión operativa. A continuación, use el asistente para agregar equipos para elegir qué equipos del grupo de administración pueden enviar datos al servicio.

Ambos tipos de agentes recopilan datos para Visión operativa. El tipo de datos recopilados depende de los tipos de soluciones usadas. Una solución es una agrupación de vistas predefinidas, consultas de búsqueda de registros, reglas de recopilación de datos y lógica de procesamiento. Solo los administradores de Visión operativa pueden usarla para importar una solución. Una vez importada la solución, se mueve a los servidores de administración de Operations Manager (si se utilizan) y, a continuación, a los agentes de Operations Manager que ha elegido. Posteriormente, los agentes recopilan los datos.

La tabla siguiente enumera las soluciones disponibles en Visión operativa y los tipos de datos que recopilan.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Solución</b></td>
		<td><b>Tipos de datos</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Evaluación de la configuración</td>
		<td>Datos de configuración, metadatos y datos de estado</td>
    </tr>
    <tr align="left" valign="top">
		<td>Planificación de capacidad</td>
		<td>Datos de rendimiento, metadatos y datos de estado</td>
    </tr>
	<tr align="left" valign="top">
		<td>Antimalware</td>
		<td>Datos de configuración, metadatos y datos de estado</td>
    </tr>
	    <tr align="left" valign="top">
		<td>Evaluación de la actualización del sistema</td>
		<td>Metadatos y datos de estado</td>
    </tr>
    <tr align="left" valign="top">
		<td>Administración de registros</td>
		<td>Registros de eventos definidos por el usuario</td>
    </tr>
    <tr align="left" valign="top">
		<td>Seguimiento de cambios</td>
		<td>Inventario de software y metadatos de servicio de Windows</td>
    </tr>
    <tr align="left" valign="top">
		<td>Evaluación de SQL y Active Directory</td>
		<td>Datos de WMI, datos de registro, datos de rendimiento y resultados de la vista de administración dinámica de SQL Server</td>
    </tr>
    </tbody>
    </table>


La tabla siguiente muestra ejemplos de los tipos de datos:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Tipo de datos</b></td>
		<td><b>Campos </b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Alerta</td>
		<td>Alert Name, Alert Description, BaseManagedEntityId, Problem ID, IsMonitorAlert, RuleId, ResolutionState, Priority, Severity, Category, Owner, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount</td>
    </tr>
    <tr align="left" valign="top">
		<td>Configuración</td>
		<td>CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate</td>
    </tr>
	<tr align="left" valign="top">
		<td>Evento</td>
		<td>EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded<p><b>Nota:</b> cuando registra eventos con campos personalizados en el registro de eventos de Windows, Visión operativa los recopila. </td>
    </tr>
	    <tr align="left" valign="top">
		<td>Metadatos</td>
		<td>BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime</td>
    </tr>
    <tr align="left" valign="top">
		<td>Rendimiento</td>
		<td>ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded</td>
    </tr>
    <tr align="left" valign="top">
		<td>Estado</td>
		<td>StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified</td>
    </tr>
    </tbody>
    </table>


### 2. Envío de datos desde agentes

Con agentes que se conectan directamente al servicio web, los registra con una clave y se establece una conexión segura entre el agente y el servicio de Visión operativa mediante el puerto 443.

Con Operations Manager, registra una cuenta con el servicio Visión operativa y se establece una conexión HTTPS entre el servidor de administración de Operations Manager y el servicio de Visión operativa mediante el puerto 443. Si por cualquier motivo Operations Manager no puede comunicarse con el servicio, los datos recopilados se almacenan en una caché temporal y el servidor de administración intenta volver a enviar los datos cada ocho minutos durante dos horas. Los datos recopilados se comprimen y se envían al servicio de Visión operativa, omitiendo las bases de datos locales, por lo que no se les agrega ninguna carga. Después de enviar los datos recopilados, se quitan de la memoria caché.

### 3. El servicio de Visión operativa recibe y procesa los datos

El servicio de Visión operativa asegura que los datos entrantes provienen de una fuente de confianza mediante la validación de certificados y la integridad de los datos. Los datos sin procesar se almacenan entonces como un blob en [Almacenamiento de Microsoft Azure](http://azure.microsoft.com/documentation/services/storage/). Cada usuario de Visión operativa tiene un blob de Azure dedicado, al que solo puede tener acceso ese usuario. El tipo de datos que se almacena depende de los tipos de soluciones que se importaron y se usaron para recopilar datos.

El servicio de Visión operativa procesa los datos sin procesar y los datos procesados agregados se almacenan en una base de datos SQL. La comunicación entre el servicio de Visión operativa y la base de datos SQL se basa en la autenticación de base de datos SQL.

### 4. Uso de Visión operativa para tener acceso a los datos

Puede iniciar sesión en Visión operativa con la cuenta que configuró anteriormente. Todo el tráfico entre Visión operativa y el servicio de Visión operativa se envía a través de un canal HTTPS seguro.

<!---HONumber=July15_HO4-->