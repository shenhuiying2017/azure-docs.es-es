---
title: "Administración después de la migración: Azure SQL Database | Microsoft Docs"
description: "Obtenga información acerca de cómo administrar la base de datos tras la migración a Azure SQL Database."
services: sql-database
documentationcenter: 
author: joesackmsft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 10/14/2016
ms.author: Joe.Sack
ms.suite: sql
ms.prod_service: sql-database
ms.component: migration
ms.openlocfilehash: e562c33cabc7d39d1f6a911c21343f85da205c0b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="how-should-i-manage-my-azure-sql-database-after-migration"></a>¿Cómo debo administrar mi base de datos SQL de Azure después de la migración?

*Preguntas más frecuentes acerca de la administración de sus inversiones en Azure SQL Database* 

Supongamos que recientemente ha movido las bases de datos de SQL Server a Azure SQL Database o quizás piensa hacerlo a corto plazo. Una vez que las haya movido, ¿cuál es el siguiente paso? Dado que SQL Database es una *plataforma como servicio*, Microsoft se encarga de varias áreas en su nombre. ¿Cómo cambia esto exactamente las prácticas de su empresa en áreas clave como la seguridad, la continuidad del negocio, el mantenimiento de base de datos, el ajuste del rendimiento, la supervisión y otras áreas? 

El propósito de este artículo es organizar brevemente los recursos y la orientación que necesitará para realizar el cambio en la administración de sus inversiones en SQL Database. Las áreas principales en este artículo tratan la continuidad del negocio, la seguridad, el mantenimiento y supervisión de base de datos, el rendimiento y el movimiento de datos. Hablaremos sobre las áreas clave que son diferentes entre SQL Server y SQL Database, y reseñaremos prácticas recomendadas que le ayudarán a maximizar los beneficios y minimizar el riesgo. 

## <a name="manage-business-continuity-after-migration"></a>Administración de la continuidad del negocio después de la migración

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>¿Cómo crear y administrar copias de seguridad en SQL Database? 
SQL Database realiza una copia de seguridad de las bases de datos automáticamente y proporciona la capacidad de restaurar a un momento dado en el tiempo dentro del período de retención. El período de retención es de 35 días para las bases de datos Standard y Premium y 7 días para las bases de datos Basic. Además, la característica de retención a largo plazo permite almacenar archivos de copia de seguridad durante un periodo de tiempo mayor (hasta 10 años) y restaurar a partir de estas copias de seguridad en cualquier momento. Además, las copias de seguridad de base de datos tienen replicación geográfica para garantizar la capacidad de su restauración geográfica en cualquier región en el caso de un desastre o una catástrofe regional. Consulte [Introducción a la continuidad del negocio](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-a-regional-catastrophe"></a>¿Cómo asegurar la continuidad del negocio en caso de un desastre en el nivel de centro de datos o una catástrofe regional? 

Las copias de seguridad de base de datos tienen replicación geográfica para garantizar la capacidad de su restauración geográfica en cualquier región en el caso de un desastre o una catástrofe regional. Consulte [Introducción a la continuidad del negocio](sql-database-business-continuity.md). Además, SQL Database proporciona la funcionalidad de mantener bases de datos secundarias replicadas geográficamente de un modo activo en otra región. Al configurarlas en un grupo de conmutación por error automática, se asegurará de que las bases de datos realizarán la conmutación por error automáticamente a la base de datos secundaria en un escenario de desastre. Si no se configura un grupo de conmutación por error automática, la aplicación debe supervisar activamente la existencia de un desastre e iniciar una conmutación por error a la base de datos secundaria. 
### <a name="sql-server-provided-me-readable-secondary-replicas-can-i-access-the-secondaries-on-sql-database"></a>SQL Server proporciona réplicas secundarias de lectura, ¿es posible acceder a las secundarias en SQL Database? 

Sí, la característica de "Replicación geográfica activa" se utiliza para crear réplicas secundarias de lectura. 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premise-to-sql-database"></a>¿Cómo cambia mi plan de recuperación ante desastres en relación a la base de datos SQL local? 
Las implementaciones de SQL Server requieren que administre de un modo activo las copias de seguridad con características como la agrupación en clústeres de conmutación por error, la creación de reflejo de la base de datos, la replicación, el trasvase de registros o simplemente las copias de seguridad BACPAC. Sin embargo, en SQL Database, las copias de seguridad están totalmente administradas por Microsoft y simplemente puede tener planes de recuperación ante desastres y de copia de seguridad configurados y en funcionamiento con unos pocos clics en Azure Portal (o algunos comandos de PowerShell). ‌
### <a name="in-the-event-of-disaster-how-do-i-recover-my-databases"></a>En caso de desastre, ¿cómo recupero mis bases de datos? 
SQL Database le permite restaurar automáticamente las bases de datos a cualquier punto en el tiempo en los últimos 35 días. Se trata de una opción si pierde datos o se enfrentan a un desastre relacionado con la aplicación. 

En caso de enfrentarse a un desastre regional, si se configuran las bases de datos secundarias de replicación geográfica, es posible recuperar desde las bases de datos de replicación geográfica secundaria en otra región. Para obtener acceso en tiempo real a las aplicaciones, puede conmutar por error manualmente a la replicación geográfica secundaria en otra región. O bien, si tiene configurado el grupo de conmutación por error automática, esta conmutación por error a una réplica secundaria geográfica se realiza automáticamente en un escenario de desastre. Si no tiene la replicación geográfica secundaria de base de datos configurada, todavía puede recuperar las bases de datos desde archivos de copia de seguridad replicados automáticamente (funcionalidad integrada, sin configuración), con mayor tiempo de recuperación (RTO de 12 horas) y hasta una hora de pérdida de datos. 

### <a name="are-the-failovers-to-secondary-transparent-how-does-my-application-handle-database-failovers"></a>¿Son transparentes las conmutaciones por error a una réplica secundaria? ¿Cómo controla mi aplicación las conmutaciones por error de base de datos? 
Si tiene grupos de conmutación por error automática configurados, la conmutación por error a una réplica secundaria es transparente. Si no los tiene, la aplicación debe incorporar la lógica para supervisar la disponibilidad de la principal y, a continuación, realizar la conmutación por error manual a la base de datos secundaria. 
 
## <a name="manage-security-after-migration"></a>Administración de la seguridad después de la migración

### <a name="how-can-i-restrict-access-to-my-sql-database"></a>¿Cómo puedo restringir el acceso a mi base de datos en SQL Database? 
 
Hay varias maneras de bloquear el acceso de conectividad a las bases de datos SQL. 
1. Limitar el tráfico a través de Internet Express Route proporciona fibra dedicada a la red de Azure para que los datos no viajen a través de Internet. También es posible configurar conectividad entre regiones mediante Express Route. Los vínculos siguientes describen Express Route con más detalle: 
 - [Introducción a Express Route](../expressroute/expressroute-introduction.md)
 - [Requisitos previos](../expressroute/expressroute-prerequisites.md) 
 - [Flujos de trabajo](../expressroute/expressroute-workflows.md) 
 
2. Seleccionar qué recursos se conectan a SQL Database: 

   De forma predeterminada, la base de datos SQL está configurada para "Permitir todos los servicios de Azure", lo que significa que todas las máquinas virtuales de Azure pueden intentar conectarse a la base de datos.  Todavía debe producirse la autenticación de todos los inicios de sesión. Si no desea que la base de datos sea accesible para todas las direcciones IP de Azure, puede deshabilitar "Permitir todos los servicios de Azure" y usar [puntos de conexión de servicio de red virtual](sql-database-vnet-service-endpoint-rule-overview.md) para restringir el acceso entrante a la base de datos solamente a los recursos de Azure que están dentro de una subred determinada de una red virtual de Azure. 

   ![Puntos de conexión de servicio de red virtual](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

   Una opción alternativa es aprovisionar [direcciones IP reservadas](../virtual-network/virtual-networks-reserved-public-ip.md) para las máquinas virtuales e incluir en la lista de permitidas esas direcciones IP específicas de las máquinas virtuales en la configuración de firewall del servidor. (Observe la captura de pantalla siguiente como ejemplo en Azure Portal.) Mediante la asignación de direcciones IP reservadas, se evita el problema de tener que actualizar las reglas del firewall con el cambio de direcciones IP. 

3. Evitar exponer el puerto 1433 fuera de Azure

   Ejecute SSMS en Azure mediante [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). Esto no requiere abrir conexiones salientes para el puerto 1433, la dirección IP es estática, por lo que la base de datos puede estar abierta solo para RemoteApp, que admite Multi-Factor Authentication (MFA) y es multiusuario. 

### <a name="what-authentication-methods-are-offered-in-sql-database"></a>¿Qué métodos de autenticación se ofrecen en SQL Database?

Los métodos de autenticación principales que ofrecen SQL Database y SQL Data Warehouse son la autenticación de Azure Active Directory y la autenticación de SQL. Azure Active Directory (AD) es un servicio de administración de identidades y acceso centralizado y SQL es solo uno de los muchos servicios de Azure que se integran con Azure AD. La ventaja de un servicio administrado centralizado es que las credenciales del usuario se comparten entre todos los servicios de Azure que utiliza para una autenticación más sencilla. Esto también permite a SQL Database y SQL Data Warehouse ofrecer autenticación multifactor y cuentas de usuario de invitado dentro de un dominio de Azure AD. 

Si ya tiene una instancia de Active Directory local, puede federar el directorio con Azure Active Directory para extender su directorio a Azure. 


|||
|---|---|
| Si usted...|Azure SQL Database / Azure SQL Data Warehouse|
| No desea usar Azure Active Directory (AD) en Azure|Use [autenticación de SQL](sql-database-security-overview.md)|
| Ha utilizado AD en SQL Server local|[Federe AD con Azure AD](../active-directory/connect/active-directory-aadconnect.md) y use la autenticación de Azure AD. De este modo, puede usar el inicio de sesión único.|
| Necesita aplicar autenticación multifactor (MFA)|Exija MFA como una directiva mediante el [acceso condicional de Microsoft](sql-database-conditional-access.md) y utilice la [autenticación universal de Azure AD con compatibilidad con MFA](sql-database-ssms-mfa-authentication.md).|
| Tiene cuentas de invitado procedentes de cuentas de Microsoft (live.com, outlook.com) o de otros dominios (gmail.com)|Use la [autenticación universal de Azure AD](sql-database-ssms-mfa-authentication.md) en SQL Database y Data Warehouse, que aprovecha la [colaboración B2B de Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
| Ha iniciado sesión en Windows con sus credenciales de Azure AD desde un dominio federado|Use la [autenticación integrada de Azure AD](sql-database-aad-authentication-configure.md).|
| Ha iniciado sesión en Windows con sus credenciales desde un dominio no federado con Azure|Use la [autenticación de contraseña de Azure AD](sql-database-aad-authentication-configure.md).|
| Tiene servicios de nivel intermedio que necesitan conectarse a Azure SQL Database o Data Warehouse|Use la [autenticación de token de Azure AD](sql-database-aad-authentication-configure.md).
|||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-databases-from-the-application-side"></a>¿Cómo puedo limitar el acceso a la información confidencial en mis bases de datos desde el lado de la aplicación? 

Para evitar que los usuarios no autorizados puedan ver datos confidenciales, hay algunas opciones disponibles en SQL Database: 

- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) es una forma de cifrado en el cliente que cifra las columnas confidenciales de la base de datos (para lo que están en texto cifrado para los administradores de la base de datos y los usuarios no autorizados). La clave de Always Encrypted se almacena en el lado del cliente, de modo que solo los clientes autorizados pueden descifrar las columnas confidenciales. Always Encrypted admite comparaciones de igualdad en la actualidad, por lo que los DBA pueden seguir consultando las columnas cifradas como parte de los comandos SQL. Always Encrypted puede utilizarse con diversos almacenes de claves, como [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), el almacén de certificados de Windows y módulos de seguridad de hardware local.
- [Enmascaramiento dinámico de datos](sql-database-dynamic-data-masking-get-started.md) es una característica de enmascaramiento de datos que limita la exposición de información confidencial ocultándola a los usuarios sin privilegios en el nivel de aplicación. Se define una regla de enmascaramiento que puede crear un patrón de enmascaramiento (por ejemplo, para mostrar solo los últimos 4 dígitos de un documento nacional de identidad y marcar el resto con X) y se identifica qué usuarios pueden excluirse de la regla de enmascaramiento.
- [Seguridad en el nivel de fila](/sql/relational-databases/security/row-level-security) permite controlar el acceso a las filas de una tabla de la base de datos en función del usuario que ejecuta la consulta (pertenencia a un grupo o contexto de ejecución). La restricción de acceso se realiza en el nivel de base de datos en lugar de en una capa de aplicación para simplificar la lógica de la aplicación. 

### <a name="what-encryption-options-do-i-have-in-sql-database-and-what-actors-does-the-encryption-protect-from"></a>¿Qué opciones de cifrado tengo en SQL Database y frente a qué actores protege el cifrado?
Hay tres tecnologías de cifrado principales que están disponibles en SQL Database: 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) (que se menciona en la pregunta anterior): cifra columnas confidenciales de la tabla de extremo a extremo, desde los clientes no autorizados hasta el disco físico. Los administradores del servidor y de los datos no pueden ver los datos confidenciales, ya que las claves de cifrado se almacenan en el cliente. 
- [Cifrado de datos transparente](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) (TDE): Cifrado en reposo, el cual cifra en el nivel de base de datos y protege los archivos de datos, archivos de registro y copias de seguridad asociadas del robo de medios físicos. TDE está habilitado de forma predeterminada en todas las bases de datos de nueva creación.
 
  El siguiente diagrama muestra una visión general de las opciones de tecnología de cifrado.

   ![Información general del cifrado](./media/sql-database-manage-after-migration/overview-encryption.png)

### <a name="how-should-i-manage-encryption-keys-in-the-cloud"></a>¿Cómo debo administrar claves de cifrado en la nube? 
Hay opciones de administración de claves para Always Encrypted (cifrado de cliente) y el Cifrado de datos transparente (cifrado en reposo). Se recomienda rotar periódicamente las claves de cifrado con una frecuencia alineada con las normativas internas y los requisitos de cumplimiento de normas.

- **Always Encrypted**: hay una [jerarquía de dos calves](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) en Always Encrypted: una columna de datos confidenciales se cifra mediante una clave de cifrado de columna AES 256 (CEK), que a su vez se cifra con una clave maestra de columna (CMK). Los controladores de cliente proporcionados para Always Encrypted no tienen limitaciones en la longitud de las claves CMK.

  El valor cifrado de la CEK se almacena en la base de datos y la CMK se almacena en un almacén de claves de confianza, como el almacén de certificados de Windows, Azure Key Vault o un módulo de seguridad de hardware. 
  
  Tanto la [CEK como la CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) se pueden rotar. La rotación de la CEK puede llevar mucho tiempo en función del tamaño de las tablas que contienen las columnas cifradas. Por lo tanto, planee cuidadosamente las rotaciones de CEK. La rotación de CMK, por otro lado, no interfiere con el rendimiento de la base de datos y puede hacerse con roles separados.

  El diagrama siguiente muestra las opciones de almacén de claves para las claves maestras de columna en Always Encrypted 

   ![Proveedores de almacén de CMK de Always Encrypted](./media/sql-database-manage-after-migration/always-encrypted.png)

- **Cifrado de datos transparente (TDE)**: hay una jerarquía de dos claves en TDE: los datos de cada base de datos del usuario se cifran mediante una clave de cifrado única para la base de datos AES-256 simétrica (DEK), que a su vez se cifra con una clave maestra RSA 2048 asimétrica única para el servidor. 

  De forma predeterminada, el servicio SQL Database administra la clave maestra de Cifrado de datos transparente para su comodidad. Si la organización quiere tener control sobre la clave maestra, existe la opción de usar [Azure Key Vault](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql) como el almacén de claves. 

  Al usar Azure Key Vault, su organización asume el control sobre el aprovisionamiento de claves, la rotación y el control de permisos. La [rotación o cambio del tipo de una clave maestra TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) es rápida, ya que solo se vuelve a cifrar la DEK. 

  Para organizaciones con separación de roles entre la seguridad y la administración de datos, un administrador de seguridad puede aprovisionar el material de clave para la clave maestra TDE en Azure Key Vault y proporcionar un identificador de Azure Key Vault al administrador de base de datos que se usará para el cifrado en reposo en un servidor. 

## <a name="monitoring-and-compliance-after-migration"></a>Supervisión y cumplimiento después de la migración

### <a name="how-do-i-monitor-database-activities-in-sql-database"></a>¿Cómo puedo supervisar las actividades de la base de datos en SQL Database?
Hay algunas funcionalidades de supervisión integradas en SQL Database que realizan un seguimiento de la seguridad y otros eventos en la base de datos:
- [Auditoría de SQL](sql-database-auditing.md) permite recopilar los registros de auditoría de eventos de base de datos en su propia cuenta de Azure Storage.
- [Detección de amenazas de SQL](sql-database-threat-detection.md) permite detectar actividades sospechosas que indiquen un posible intento malintencionado de acceso, infracción o vulnerabilidad de seguridad de datos en la base de datos. Detección de amenazas de SQL Database ejecuta varios conjuntos de algoritmos que detectan posibles vulnerabilidades y ataques de inyección de SQL, así como patrones de acceso de base de datos anómalos (por ejemplo, el acceso desde una ubicación inusual o una entidad de seguridad desconocida). Los responsables de seguridad u otros administradores designados reciben una notificación por correo electrónico si se detecta una amenaza en la base de datos. Cada notificación proporciona detalles de la actividad sospechosa y recomendaciones acerca de cómo investigar más y mitigar la amenaza. 
- [Evaluación de vulnerabilidad de SQL](sql-vulnerability-assessment.md) es un servicio de examen e informes de la base de datos que le permite supervisar el estado de seguridad de las bases de datos a escala e identificar los riesgos de seguridad y desfase respecto a una línea de base de seguridad definida por el usuario. Después de cada examen, se proporciona una lista personalizada de pasos procesables y scripts de corrección, así como un informe de evaluación que puede usarse para ayudar a cumplir con las normas. 
- La [Aplicación de sincronización de SQL y OMS Security](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) utiliza las API públicas de Operations Management Suite (OMS) para insertar los registros de auditoría de SQL en OMS para el análisis de registros y la posibilidad de definir alertas de detección personalizada incluidas: 
 - El icono y el panel de Auditoría de SQL Database, que proporcionan informes claros y coherentes de las actividades de la base de datos. 
 - SQL Log Analytics para analizar la actividad de la base de datos e investigar las discrepancias y las anomalías que podrían indicar infracciones de seguridad sospechosas.
 - Reglas específicas de alertas avanzadas sobre los eventos observados que desencadenan alertas por correo electrónico, Webhook y runbook de Azure Automation (por ejemplo, cambios de contraseña, después del horario comercial, comandos SQL específicos).
- [Azure Security Center](../security-center/security-center-intro.md) ofrece administración de seguridad centralizada de todas las cargas de trabajo que se ejecutan en Azure, de forma local y en otras nubes. Puede ver si se han configurado en todos los recursos elementos de protección esenciales de SQL Database como la auditoría y el Cifrado de datos transparente y crear directivas basadas en sus propios requisitos. 

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>¿Cumple SQL Database con los requisitos de regulación y cómo ayuda con el cumplimiento de mi propia organización? 
Azure SQL Database cumple con una variedad de regulaciones de cumplimiento. Para ver el conjunto más reciente de cumplimientos que se han alcanzado, visite [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings) para profundizar en los cumplimientos que son importantes para su organización para ver si Azure SQL Database se incluye en los servicios de Azure compatibles. Es importante tener en cuenta que aunque Azure SQL Database puede estar certificada como un servicio conforme, contribuye al cumplimiento del servicio en la organización pero no lo garantiza automáticamente. 

## <a name="database-maintenance-and-monitoring-after-migration"></a>Mantenimiento y supervisión de la base de datos después de la migración

### <a name="how-do-i-monitor-growth-in-data-size-and-resource-utilization"></a>¿Cómo superviso el crecimiento del tamaño de los datos y la utilización de recursos?

- Puede ver métricas de supervisión sobre la utilización de recursos y el tamaño de la base de datos en el gráfico "Supervisión" en Azure Portal. 

  ![Gráfico de supervisión](./media/sql-database-manage-after-migration/monitoring-chart.png)

- Para obtener un conocimiento más profundo y profundizar en los detalles de las consultas, puede usar "Información de rendimiento de consultas" en Azure Portal. Esto requerirá que "Almacén de consultas" esté activo en la base de datos.

  ![Información de rendimiento de consultas](./media/sql-database-manage-after-migration/query-performance-insight.png)

- Como alternativa, también puede ver las métricas mediante Vistas de administración dinámica (DMV): mediante el uso de [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) y [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database). 

### <a name="how-often-do-i-need-to-run-consistency-checks-like-dbcccheckdb"></a>¿Con qué frecuencia es necesario ejecutar comprobaciones de coherencia como DBCC_CHECKDB?
DBCC_CHECKDB comprueba la integridad lógica y física de todos los objetos de la base de datos. Ya no necesita realizar estas comprobaciones, puesto que estas las administra Microsoft en Azure. Para más información, consulte [Integridad de los datos en Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="monitor-performance-and-resource-utilization-after-migration"></a>Supervisión del rendimiento y utilización de recursos después de la migración

### <a name="how-do-i-monitor-performance-and-resource-utilization-in-azure-sql-database"></a>¿Cómo se supervisa el rendimiento y la utilización de recursos en Azure SQL Database?
Puede supervisar el rendimiento y la utilización de recursos en Azure SQL Database utilizando los métodos siguientes:

- **Azure Portal**: Azure Portal muestra la utilización de una sola base de datos al seleccionar la base de datos y hacer clic en el gráfico en el panel Información general. Puede modificar el gráfico para mostrar varias métricas, incluido el porcentaje de CPU, el porcentaje de DTU, el porcentaje de E/S de datos, el porcentaje de las sesiones y el porcentaje de tamaño de base de datos. 
  ![utilización de recursos](./media/sql-database-manage-after-migration/resource-utilization.png)

  En este gráfico también puede configurar alertas por recurso. Estas alertas le permiten responder a condiciones de los recursos con un correo electrónico, escribir a un punto de conexión HTTP/HTTPS o realizar una acción. Consulte [Supervisión del rendimiento de la base de datos en Azure SQL Database](sql-database-single-database-monitor.md) para obtener instrucciones detalladas.

- **Vistas**: puede consultar la vista de administración dinámica [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) para devolver el historial de estadísticas de consumo de recursos de la última hora y la vista de catálogo del sistema [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) para devolver el historial de los últimos 14 días. 

- **Información de rendimiento de consultas**: [Información de rendimiento de consultas](sql-database-query-performance.md) le permite ver un historial de las consultas de consumo de recursos principales y las consultas de larga ejecución para una base de datos específica. Esta característica requiere que [Almacén de consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) esté habilitado y activo para la base de datos.

- **Azure SQL Analytics (versión preliminar) en Log Analytics**: [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md) le permite recopilar y visualizar las métricas clave de rendimiento de Azure SQL, permitiendo hasta 150.000 bases de datos de Azure SQL y 5.000 grupos elásticos de SQL por área de trabajo. Puede utilizarlo para supervisar y recibir notificaciones. Puede supervisar Azure SQL Database y las métricas de los grupos elásticos de varias suscripciones de Azure y puede utilizar los grupos elásticos para identificar problemas en cada capa de un pila de aplicación. 

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>¿Cómo me aseguro de que utilizo el nivel de rendimiento y el nivel de servicio adecuados?
Supervise las vistas de administración dinámica [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) y [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) para conocer el consumo de CPU, E/S y memoria. También puede utilizar [Información de rendimiento de consultas](sql-database-query-performance.md) de SQL Database para ver el consumo de recursos. Si tiene una ejecución continuada en un alto porcentaje de los recursos disponibles, debe considerar la posibilidad de mover a un nivel más alto de rendimiento en el nivel de servicio existente o mover a un mayor nivel de servicio. Por el contrario, si utiliza sistemáticamente un porcentaje bajo de los recursos disponibles, puede considerar la posibilidad de mover a un nivel de rendimiento o nivel de servicio inferiores.

### <a name="i-am-seeing-performance-issues-how-does-my-azure-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Estoy observando problemas de rendimiento. ¿Cómo se diferencia la metodología para solucionar problemas de Azure SQL Database frente a la de SQL Server?
Muchos aspectos de la metodología de solución de problemas de rendimiento seguirá siendo el mismo en Azure SQL Database, pero habrá algunas diferencias. Por ejemplo, si observa una degradación del rendimiento general, supervise las vistas de administración dinámica [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) y [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) para conocer el consumo de CPU, E/S y memoria. Puede necesitar cambiar el nivel de rendimiento o el nivel de servicio en función de las peticiones de carga de trabajo.
Para obtener un conjunto completo de recomendaciones para optimizar los problemas de rendimiento, consulte [Ajuste del rendimiento en Azure SQL Database](sql-database-performance-guidance.md). 

### <a name="do-i-need-to-maintain-indexes-and-statistics"></a>¿Debo mantener índices y estadísticas?
Azure SQL Database no mantiene índices y estadísticas automáticamente como parte del servicio. Usted es responsable de programar el mantenimiento de índices y estadísticas. El siguiente artículo, métodos de Azure Automation, detalla varias opciones para la programación de trabajos de mantenimiento en Azure SQL Database.

## <a name="data-movement-after-migration"></a>Movimiento de datos después de la migración

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-azure-sql-database"></a>¿Cómo exportar e importar datos como archivos BACPAC desde Azure SQL Database? 

- **Exportar**: puede exportar la base de datos de Azure SQL como un archivo BACPAC desde Azure Portal.

  ![Exportar como un archivo BACPAC](./media/sql-database-export/database-export.png)

- **Importar**: puede importar como archivo BACPAC en una base de datos mediante Azure Portal.

  ![Importar un archivo BACPAC](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-azure-sql-database-sql-server-2016--2012"></a>¿Cómo se pueden sincronizar los datos entre Azure SQL Database y SQL Server 2016 y 2012?
La característica [Sincronización de datos](sql-database-sync-data.md) ayuda a sincronizar los datos bidireccionalmente entre varias bases de datos de SQL Server local y Azure SQL Database. Sin embargo, dado que se basa en un desencadenador, se garantiza la coherencia final (sin pérdida de datos), pero no se garantiza la coherencia de la transacción. 

## <a name="next-steps"></a>Pasos siguientes
Obtenga información acerca de [Azure SQL Database](sql-database-technical-overview.md).
