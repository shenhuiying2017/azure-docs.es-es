---
title: Seguridad de datos de Log Analytics | Microsoft Docs
description: "Obtenga información sobre cómo Log Analytics protege su privacidad y sus datos."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: magoedte
ms.openlocfilehash: 91af774560860b35913e57b49fb7a1dd59f5640f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-data-security"></a>Seguridad de datos de Log Analytics
Microsoft se compromete a proteger su privacidad y sus datos, al tiempo que ofrece software y servicios que le ayuden a administrar la infraestructura de TI de su organización. Reconocemos que cuando confía sus datos a otros usuarios, esa confianza requiere una seguridad rigurosa. Microsoft se adhiere a instrucciones estrictas de seguridad y cumplimiento de normas, desde la codificación hasta la operación de un servicio.

 La seguridad y la protección de los datos son prioritarias en Microsoft. Póngase en contacto con nosotros si tiene preguntas, sugerencias o problemas acerca de la siguiente información, incluidas nuestras directivas de seguridad en [Opciones de soporte técnico de Azure](http://azure.microsoft.com/support/options/).

En este artículo, se explica cómo Log Analytics recopila, procesa y protege los datos en Operations Management Suite (OMS). Puede usar a agentes para conectarse al servicio web, utilizar System Center Operations Manager con el fin de recopilar datos operativos o recuperar datos de Diagnósticos de Azure para que Log Analytics los utilice. Los datos recopilados se envían a través de Internet mediante la autenticación basada en certificados y SSL 3 al servicio Log Analytics, que se hospeda en Microsoft Azure. El agente comprime los datos antes de enviarlos.

El servicio Log Analytics administra sus datos basados en la nube de forma segura mediante los siguientes métodos:

* Segregación de datos
* Retención de datos
* Seguridad física
* Administración de incidentes
* Cumplimiento normativo
* Certificaciones de estándares de seguridad

## <a name="data-segregation"></a>Segregación de datos
Los datos de cliente se mantienen separados de forma lógica en cada componente a través del servicio de OMS. Todos los datos se etiquetan por organización. Este etiquetado persiste a lo largo del ciclo de vida de los datos y se aplica en cada nivel del servicio. Cada cliente tiene un blob de Azure específico que aloja los datos a largo plazo.

## <a name="data-retention"></a>Retención de datos
Los datos indexados de búsqueda de registros se almacenan y se conservan conforme al plan de precios. Para obtener más información, consulte [Precios de Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

Microsoft elimina los datos de cliente 30 días después de cerrar el área de trabajo de OMS. Asimismo, suprime la cuenta de Azure Storage donde residen los datos. Cuando se eliminan los datos de cliente, no se destruyen unidades físicas.

En la tabla siguiente se muestran las soluciones disponibles en OMS y ejemplos de los tipos de datos que recopilan.

| **Solución** | **Tipos de datos** |
| --- | --- |
| Evaluación de la configuración |Datos de configuración, metadatos y datos de estado |
| Planificación de capacidad |Datos de rendimiento y metadatos |
| Antimalware |Datos de configuración y metadatos |
| Evaluación de la actualización del sistema |Metadatos y datos de estado |
| Administración de registros |Registros IIS, de eventos de Windows o de eventos definidos por el usuario |
| Seguimiento de cambios |Inventario de software y metadatos de servicio de Windows |
| Evaluación de SQL y Active Directory |Datos de WMI, datos de registro, datos de rendimiento y resultados de la vista de administración dinámica de SQL Server |

La tabla siguiente muestra ejemplos de los tipos de datos:

| **Tipo de datos** | **Fields** |
| --- | --- |
| Alerta |Alert Name, Alert Description, BaseManagedEntityId, Problem ID, IsMonitorAlert, RuleId, ResolutionState, Priority, Severity, Category, Owner, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Configuración |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Evento |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Nota**: Cuando se escriben eventos con campos personalizados en el registro de eventos de Windows, OMS los recopila. |
| Metadatos |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Rendimiento |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Estado |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Seguridad física
El servicio Log Analytics de OMS está a cargo de personal de Microsoft y todas las actividades se registran y se pueden auditar. El servicio se ejecuta completamente en Azure y cumple los criterios de ingeniería comunes de Azure. Puede ver detalles acerca de la seguridad física de los recursos de Azure en la página 18 de la [Información general de la seguridad de Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Los derechos de acceso físico para proteger áreas de las personas que ya no tienen ninguna responsabilidad en el servicio de OMS se cambian en un plazo de un día hábil, incluidas la transferencia y finalización. Puede consultar información sobre la infraestructura física global que se utiliza en los [centros de datos de Microsoft](https://www.microsoft.com/en-us/server-cloud/cloud-os/global-datacenters.aspx).

## <a name="incident-management"></a>Administración de incidentes
OMS incluye un proceso de administración de incidentes que cumplen todos los servicios de Microsoft. Resumiendo:

* Usamos un modelo de responsabilidad compartida donde una parte de la responsabilidad de seguridad reside en Microsoft y otra parte pertenece al cliente.
* Administramos los incidentes de seguridad de Azure.
  * Iniciamos una investigación tras la detección de un incidente.
  * Un miembro de guardia del equipo de respuesta a incidentes evalúa el impacto y la gravedad de un incidente. En función de las pruebas, la evaluación puede dar lugar a que el incidente se remita al equipo de respuesta de seguridad.
  * Diagnostique un incidente gracias a los expertos de respuesta de seguridad para realizar la investigación forense o técnica, e identificar las estrategias de contención, mitigación y solución. Si el equipo de seguridad considera que los datos de cliente pueden quedar expuestos a una persona no autorizada, la ejecución en paralelo del proceso de notificación de incidentes de cliente.  
  * Estabilice y recupere la infraestructura después del incidente. El equipo de respuesta a incidentes crea un plan de recuperación para mitigar el problema. Pueden llevarse a cabo procesos de gestión de crisis, como poner en cuarentena sistemas afectados, de inmediato y en paralelo al diagnóstico. Pueden planearse más mitigaciones a largo plazo para realizarse después de que ha pasado el riesgo inmediato.  
  * Cierre el incidente y realice un análisis final. El equipo de respuesta a incidentes lleva a cabo un análisis final donde se describen los detalles del incidente con el objetivo de revisar las directivas, los procedimientos y los procesos para evitar que se vuelva a producir el evento.
* Notificamos a los clientes de los incidentes de seguridad.
  * Determine el alcance de los incidentes y notifique de la forma más detallada posible a todos las personas afectadas.
  * Cree un aviso para los clientes con la información detallada suficiente para que puedan realizar una investigación y cumplir los compromisos con sus usuarios finales sin retrasar indebidamente el proceso de notificación.
  * Confirme y declare el incidente según sea necesario.
  * Envíe a los clientes una notificación de incidentes puntual y de acuerdo con cualquier compromiso contractual o jurídico. La notificación de incidentes de seguridad se entrega a uno o varios de los administradores de un cliente por los medios que Microsoft elija, entre ellos el correo electrónico.
* Formamos y preparamos al equipo.
  * El personal de Microsoft tiene que finalizar el curso sobre seguridad y concienciación, para que puedan identificar y notificar los posibles problemas de seguridad.  
  * Los operadores que trabajan en el servicio de Microsoft Azure tienen más obligaciones de aprendizaje relacionadas con el acceso a los sistemas confidenciales que hospedan los datos de cliente.
  * El personal de respuesta de seguridad de Microsoft recibe cursos especializados para los roles que desempeñan.

En caso de pérdida de datos de cualquier cliente, se notificará a cada cliente en el plazo de un día. Sin embargo, con OMS nunca se han perdido datos de cliente. Además, mantenemos copias de los datos que creamos y los distribuimos geográficamente.

Para obtener más información sobre cómo Microsoft responde a los incidentes de seguridad, consulte [Microsoft Azure Security Response in the Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf) (Respuesta de seguridad de Microsoft Azure en la nube).

## <a name="compliance"></a>Cumplimiento normativo
El programa de regulación y seguridad de la información del equipo de servicio y desarrollo de software de OMS respalda sus requisitos empresariales y cumple las leyes y normativas, tal y como se describe en [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/support/trust-center/) y [Microsoft Trust Center Compliance](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx) (Cumplimiento normativo del Centro de confianza de Microsoft Azure). En estas páginas también se describe cómo OMS establece los requisitos de seguridad identifica los controles de seguridad, y administra y supervisa los riesgos. Cada año, revisamos las directivas, los estándares, los procedimientos y las instrucciones.

Cada miembro del equipo de desarrollo de OMS recibe cursos formales sobre seguridad de aplicaciones. Internamente, utilizamos un sistema de control de versiones para el desarrollo de software. Cada proyecto de software se protege mediante el sistema de control de versiones.

Microsoft cuenta con un equipo de seguridad y cumplimiento normativo que supervisa y evalúa todos los servicios de Microsoft. Los responsables de seguridad de la información conforman el equipo y no están vinculados con los departamentos de ingeniería que desarrollan OMS. Los responsables de seguridad tienen su propia cadena de administración y llevan a cabo evaluaciones independientes de los productos y servicios para garantizar la seguridad y el cumplimiento normativo.

Al comité de dirección de Microsoft se le notifica mediante informes anuales sobre todos los programas de información de seguridad de Microsoft.

El equipo de servicio y desarrollo de software de OMS trabaja de manera activa con los equipos de cumplimiento y asuntos jurídicos de Microsoft, así como con otros asociados del sector, para adquirir diversas certificaciones.

## <a name="certifications-and-attestations"></a>Certificaciones y atestaciones
OMS Log Analytics cumple los requisitos siguientes:

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/en-us/blog/iso22301/)
* [Conformidad con la norma Data Security Standard de PCI (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) del PCI Security Standards Council.
* [Conformidad con Service Organization Control (SOC) 1, tipo 1 y SOC 2, tipo 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2)
* [HIPAA y HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA) para aquellas empresas que posean un contrato de asociación comercial según las normas HIPAA.
* Criterios de ingeniería comunes de Windows
* Microsoft Trustworthy Computing
* Como se trata de un servicio de Azure, los componentes que usa OMS cumplen los requisitos de cumplimiento normativo de Azure. Puede obtener más información en el artículo [Microsoft Trust Center Compliance](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx) (Cumplimiento normativo del Centro de confianza de Microsoft Azure).

> [!NOTE]
> En algunas certificaciones o atestaciones, Log Analytics aparece con su nombre anterior, *Operational Insights*.
>
>


## <a name="cloud-computing-security-data-flow"></a>Flujo de datos de seguridad de informática en la nube
El diagrama siguiente muestra una arquitectura de seguridad en la nube como el flujo de información proveniente de su empresa y cómo se protege a medida que se mueve al servicio Log Analytics y al que, en última instancia, ve usted en el portal de OMS. Después del diagrama aparece más información acerca de cada paso.

![Imagen de recopilación de datos de OMS y seguridad](./media/log-analytics-security/log-analytics-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Suscripción a Log Analytics y recopilación de datos
Para que su organización envíe datos a Log Analytics, tendrá que configurar agentes de Windows, otros que se ejecuten en máquinas virtuales de Azure o agentes de OMS para Linux. Si utiliza agentes de Operations Manager, se configuran mediante un Asistente para configuración de la consola de Operations. Los usuarios (que pueden ser usted, otros usuarios individuales o un grupo de personas) deben crear una o más cuentas de OMS (espacios de trabajo de OMS) y registrar los agentes mediante una de las siguientes cuentas:

* [Id. de organización](../active-directory/sign-up-organization.md)
* [Cuenta de Microsoft: Outlook, Office Live, MSN](http://www.microsoft.com/account/default.aspx)

Un espacio de trabajo de OMS es donde se recopilan, agregan, analizan y presentan los datos. Se usa principalmente como un medio para realizar la partición de datos y cada uno de ellos es único. Por ejemplo, recomendamos que los datos de producción se administren con un espacio de trabajo de OMS, y los datos de prueba, con otro diferente. Los espacios de trabajo también ayudan a un administrador a controlar el acceso de los usuarios a los datos. Cada espacio de trabajo puede tener, a su vez, varias cuentas de usuario asociada; y viceversa: cada cuenta de usuario puede tener varios espacios de OMS. Las áreas de trabajo se crean en función de la región del centro de datos. Cada una de ellas se replica en otros centros de datos de la región, principalmente para la disponibilidad del servicio de OMS.

En lo que respecta a Operations Manager, una vez completado el Asistente para configuración, cada grupo de administración de Operations Manager establece una conexión con el servicio Log Analytics. A continuación, use el asistente para agregar equipos para elegir qué equipos del grupo de administración pueden enviar datos al servicio. En el caso de otros tipos de agentes, cada uno de ellos se conecta al servicio de OMS de forma segura.

Toda la comunicación se cifra entre sistemas conectados y el servicio Log Analytics.  El protocolo TLS (HTTPS) se utiliza para el cifrado.  Se sigue el proceso del SDL de Microsoft para garantizar que el servicio Log Analytics está actualizado con los últimos avances en protocolos criptográficos.

Cada tipo de agente recopila datos para Log Analytics. El tipo de datos recopilados depende de los tipos de soluciones usadas. Puede ver un resumen del proceso de recolección de datos en el artículo [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md). Además, hay información más detallada disponible sobre este tema para la mayoría de las soluciones. Una solución es una agrupación de vistas predefinidas, consultas de búsqueda de registros, reglas de recopilación de datos y lógica de procesamiento. Solo los administradores pueden usar Log Analytics para importar una solución. Una vez importada, la solución se transfiere a los servidores de administración de Operations Manager (en caso de utilizarse) y, después, a los agentes que haya elegido. Posteriormente, los agentes recopilan los datos.

## <a name="2-send-data-from-agents"></a>2. Envío de datos desde agentes
El registro de todos los tipos de agentes se efectúa con una clave de inscripción y se establece una conexión segura entre el agente en cuestión y el servicio Log Analytics mediante la autenticación basada en certificados y SSL con el puerto 443. OMS utiliza un almacén secreto para generar y mantener las claves. Las claves privadas se alternan cada 90 días, y se almacenan en Azure y administran a través de las operaciones de Azure que siguen las estrictas prácticas regulatorias y de cumplimiento normativo.

Con Operations Manager, registra un área de trabajo con el servicio Log Analytics y se establece una conexión segura HTTPS con el servidor de administración de Operations Manager.

En lo que respecta a los agentes de Windows que se ejecutan en máquinas virtuales de Azure, se utiliza una clave de almacenamiento de solo lectura para leer los eventos de diagnóstico en las tablas de Azure.

Si, por cualquier motivo, un agente no puede comunicarse con el servicio, los datos recopilados se almacenan de manera local en una caché temporal y el servidor de administración trata de volver a enviar los datos cada ocho minutos durante dos horas. Los datos almacenados del agente se protegen mediante el almacén de credenciales del sistema operativo. Si el servicio no puede procesar los datos después de dos horas, los agentes pondrán en cola los datos. Si la cola se llena, OMS empieza a quitar tipos de datos, empezando por los datos de rendimiento. El límite de cola de los agentes es una clave de registro que puede modificar si es necesario. Los datos recopilados se comprimen y se envían al servicio, omitiendo las bases de datos locales, por lo que no se les agrega ninguna carga. Después de enviar los datos recopilados, se quitan de la memoria caché.

Como se describió anteriormente, los datos de los agentes se envían por SSL a los centros de datos de Microsoft Azure. También puede usar ExpressRoute para proporcionar más seguridad a los datos. Con ExpressRoute puede conectarse directamente a Azure desde una red WAN, como una VPN de conmutación por etiquetas multiprotocolo (MPLS) que proporcione un proveedor de servicios de red. Para obtener más información, consulte el artículo sobre [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Recepción y procesamiento de los datos por parte del servicio Log Analytics
El servicio Log Analytics asegura que los datos entrantes provienen de una fuente de confianza mediante la validación de certificados y la integridad de los datos con la autenticación de Azure. Los datos sin procesar se almacenan como un blob en [Microsoft Azure Storage](../storage/common/storage-introduction.md) y no se cifran. Sin embargo, cada blob de almacenamiento de Azure tiene un solo conjunto de claves al que solo puede acceder ese usuario. El tipo de datos que se almacena depende de los tipos de soluciones que se importaron y se usaron para recopilar datos. Después, el servicio Log Analytics procesa los datos sin procesar del blob de almacenamiento de Azure.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Uso de Log Analytics para acceder a los datos
Puede iniciar sesión en Log Analytics mediante el portal de OMS utilizando la cuenta de la organización o la cuenta de Microsoft configurada anteriormente. Todo el tráfico que circule entre el portal de OMS y Log Analytics de OMS se envía por un canal seguro HTTPS. Al usar el portal de OMS, se genera un identificador de sesión en el cliente del usuario (explorador web) y los datos se almacenan en una caché local hasta que finalice la sesión. Cuando termina, se elimina la memoria caché. Las cookies del cliente, que no contienen información de identificación personal, no se quitan automáticamente. Las de sesión se marcan con HTTPOnly y se protegen. Después de un periodo de inactividad predeterminado, se termina la sesión del portal de OMS.

Mediante el portal de OMS, puede exportar datos a un archivo CSV y acceder a la información mediante las API de búsqueda. La exportación a CSV está limitada a 50 000 filas por exportación y los datos de API tienen una limitación de 5000 filas por búsqueda.

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Log Analytics](log-analytics-get-started.md) , encontrará más información sobre esta solución y cómo empezar a utilizarla en cuestión de minutos.
