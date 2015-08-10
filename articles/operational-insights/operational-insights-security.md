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
	ms.date="07/22/2015"
	ms.author="banders"/>

# Seguridad de Visión operativa

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft se compromete a proteger su privacidad y sus datos, al tiempo que ofrece software y servicios que le ayuden a administrar la infraestructura de TI de su organización. Reconocemos que cuando confía sus datos a otros usuarios, esa confianza requiere una seguridad rigurosa. Microsoft se adhiere a instrucciones estrictas de seguridad y cumplimiento de normas, desde la codificación hasta la operación de un servicio.

La seguridad y la protección de los datos son prioritarias en Microsoft. Póngase en contacto con nosotros si tiene preguntas, sugerencias o problemas acerca de la siguiente información, incluidas nuestras directivas de seguridad en [Opciones de soporte técnico de Azure](http://azure.microsoft.com/support/options/):

En este artículo se explica cómo se recopilan los datos, se procesan y se protegen en Operations Management Suite (OMS). Se llamaba anteriormente Visión operativa de Microsoft Azure. Puede usar cualquiera de los agentes para conectarse directamente con el servicio web o puede usar System Center Operations Manager para recopilar datos operativos para el servicio de OMS. Los datos recopilados se envían a través de Internet para el servicio de OMS, que se hospeda en Microsoft Azure.

El servicio de OMS administra sus datos de forma segura mediante el uso de los siguientes métodos:

**Segregación de datos:** los datos de cliente se mantienen separados de forma lógica en cada componente en el servicio de OMS. Todos los datos se etiquetan por organización. Este etiquetado persiste a lo largo del ciclo de vida de los datos y se aplica en cada nivel del servicio.

Cada cliente tiene un blob de Azure dedicado que aloja los datos a largo plazo. El blob se cifra con claves por cliente únicas, que se cambian cada 90 días.

**Retención de datos:** las métricas agregadas de algunas de las soluciones (llamadas anteriormente módulos de inteligencia), como Capacity Management, se almacenan en una Base de datos SQL hospedada por Microsoft Azure. Estos datos se almacenan durante 390 días. Los datos indizados de búsqueda de registro se almacenan y se conservan conforme al plan de precios. Para obtener más información, busque en la [página de precios](http://azure.microsoft.com/pricing/details/operational-insights/).

**Seguridad física:** el servicio de OMS es atendido por el personal de Microsoft y todas las actividades se registran y se pueden auditar. El servicio de OMS se ejecuta completamente en Azure y cumple los criterios de ingeniería comunes de Azure. Puede ver detalles acerca de la seguridad física de los recursos de Azure en la página 18 de la [Información general de la seguridad de Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf).

**Cumplimiento y certificaciones**: el equipo de servicio y desarrollo de software de OMS trabaja de manera activa con los equipos y cumplimiento y servicio legal de Microsoft y otros socios del sector para adquirir una variedad de certificaciones, incluida ISO.

Actualmente cumplimos los siguientes estándares de seguridad:

- Criterios de ingeniería comunes de Windows
- Certificación de Microsoft Trustworthy Computing


## Seguridad del flujo de datos
El diagrama siguiente muestra el flujo de información proveniente de su empresa y cómo se protege a medida que se mueve al servicio de OMS y al que, en última instancia, ve usted en OMS. Después del diagrama aparece más información acerca de cada paso.

![Imagen de recopilación de datos de OMS y seguridad](./media/operational-insights-security/security.png)

### 1\. Suscríbase a OMS y recopile datos

Para que su organización envíe datos al servicio de OMS, deberá configurar agentes de Microsoft Monitoring cuando se conecte directamente al servicio web o usar un asistente de configuración en la consola de operaciones en Operations Manager. Los usuarios (que pueden ser usted, otros usuarios individuales o un grupo de personas) deben crear una o más cuentas de OMS y registrar cada agente directamente conectado o su entorno de Operations Manager, mediante el uso de una de las siguientes cuentas:


- [Id. de organización](../sign-up-organization.md)

- [Cuenta de Microsoft: Outlook, Office Live, MSN](../sign-up-organization.md)

Una cuenta de OMS es donde se recopilan, agregan, analizan y presentan los datos. Una cuenta de OMS se usa principalmente como un medio para realizar la partición de datos y cada cuenta de OMS es única. Por ejemplo, es posible que desee que sus datos de producción se administren con una cuenta de OMS y que los datos de prueba se administren con otra cuenta. Las cuentas también ayudan a un administrador a controlar el acceso de los usuarios a los datos. Cada cuenta de OMS puede tener varias cuentas de usuario asociadas y cada cuenta de usuario puede tener varias cuentas de OMS.

Una vez que se finaliza el asistente de configuración, cada grupo de administración de Operations Manager establece una conexión con el servicio de OMS. A continuación, use el asistente para agregar equipos para elegir qué equipos del grupo de administración pueden enviar datos al servicio.

Ambos tipos de agentes recopilan datos para OMS. El tipo de datos recopilados depende de los tipos de soluciones usadas. Una solución es una agrupación de vistas predefinidas, consultas de búsqueda de registros, reglas de recopilación de datos y lógica de procesamiento. Sólo los administradores de OMS pueden usar OMS para importar una solución. Una vez importada la solución, se mueve a los servidores de administración de Operations Manager (si se utilizan) y, a continuación, a los agentes de Operations Manager que ha elegido. Posteriormente, los agentes recopilan los datos.

La tabla siguiente enumera las soluciones disponibles en OMS y los tipos de datos que recopilan.


|**Solución**|**Tipos de datos**|
|---|---|
|Evaluación de la configuración|Datos de configuración, metadatos y datos de estado|
|Planificación de capacidad|Datos de rendimiento, metadatos y datos de estado|
|Antimalware|Datos de configuración, metadatos y datos de estado|
|Evaluación de la actualización del sistema|Metadatos y datos de estado|
|Administración de registros|Registros de eventos definidos por el usuario|
|Seguimiento de cambios|Inventario de software y metadatos de servicio de Windows|
|Evaluación de SQL y Active Directory|Datos de WMI, datos de registro, datos de rendimiento y resultados de la vista de administración dinámica de SQL Server|



La tabla siguiente muestra ejemplos de los tipos de datos:

|**Tipo de datos**|**Fields**|
|---|---|
|Alerta|Alert Name, Alert Description, BaseManagedEntityId, Problem ID, IsMonitorAlert, RuleId, ResolutionState, Priority, Severity, Category, Owner, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount|
|Configuración|CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate|
|Evento|EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded **Nota:** *cuando registra eventos con campos personalizados en el registro de eventos de Windows, OMS los recopila.*|
|Metadatos|BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime|
|Rendimiento|ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded|
|Estado|StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified|


### 2\. Envío de datos desde agentes

Con agentes que se conectan directamente al servicio web, los registra con una clave y se establece una conexión segura entre el agente y el servicio de OMS mediante el puerto 443.

Con Operations Manager, registra una cuenta con el servicio OMS y se establece una conexión HTTPS entre el servidor de administración de Operations Manager y el servicio de OMS mediante el puerto 443. Si por cualquier motivo Operations Manager no puede comunicarse con el servicio, los datos recopilados se almacenan en una caché temporal y el servidor de administración intenta volver a enviar los datos cada ocho minutos durante dos horas. Los datos recopilados se comprimen y se envían al servicio de OMS, omitiendo las bases de datos locales, por lo que no se les agrega ninguna carga. Después de enviar los datos recopilados, se quitan de la memoria caché.

### 3\. El servicio de OMS recibe y procesa los datos

El servicio de OMS asegura que los datos entrantes provienen de una fuente de confianza mediante la validación de certificados y la integridad de los datos. Los datos sin procesar se almacenan entonces como un blob en [Almacenamiento de Microsoft Azure](http://azure.microsoft.com/documentation/services/storage/). Cada usuario de OMS tiene un blob de Azure dedicado, al que solo puede tener acceso ese usuario. El tipo de datos que se almacena depende de los tipos de soluciones que se importaron y se usaron para recopilar datos.

El servicio de OMS procesa los datos sin procesar y los datos procesados agregados se almacenan en una base de datos SQL. La comunicación entre el servicio de OMS y la base de datos SQL se basa en la autenticación de base de datos SQL.

### 4\. Usar OMS para tener acceso a los datos

Puede iniciar sesión en OMS con la cuenta que configuró anteriormente. Todo el tráfico entre OMS y el servicio de OMS se envía por un canal HTTPS seguro.

<!---HONumber=July15_HO5-->