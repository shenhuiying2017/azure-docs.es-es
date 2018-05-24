---
title: Introducción a Instancia administrada de Azure SQL Database | Microsoft Docs
description: En este tema se describe una instancia de Instancia administrada de Azure SQL Database y se explica cómo funciona y cómo difiere de una base de datos única de Azure SQL Database.
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/10/2018
ms.author: bonova
ms.openlocfilehash: 85a0157751a1c26fb7f37152d7c12f56b1c423d1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32193291"
---
# <a name="what-is-a-managed-instance-preview"></a>¿Qué es Instancia administrada de SQL Database (versión preliminar)?

Instancia administrada de Azure SQL Database (versión preliminar) es una nueva funcionalidad de Azure SQL Database que proporciona casi un 100 % de compatibilidad con instancias locales de SQL Server (Enterprise Edition), una implementación nativa de [red virtual (VNet)](../virtual-network/virtual-networks-overview.md) que permite solucionar problemas de seguridad habituales y un [modelo de negocio](https://azure.microsoft.com/pricing/details/sql-database/) favorable para los clientes locales de SQL Server. Instancia administrada permite a los clientes existentes de SQL Server migrar mediante lift-and-shift sus aplicaciones locales a la nube con cambios mínimos en la aplicación y la base de datos. Al mismo tiempo, Instancia administrada conserva todas las funcionalidades de PaaS (revisiones y actualizaciones de versión automáticas, copia de seguridad, alta disponibilidad), lo cual reduce drásticamente la sobrecarga de administración y el costo total de propiedad.

> [!IMPORTANT]
> Para una lista de las regiones en las que está actualmente disponible Instancia administrada, consulte [Migrate your databases to a fully managed service with Azure SQL Database Managed Instance](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/) (Migración de las bases de datos a un servicio completamente administrado con Instancia administrada de Azure SQL Database).
 
En el diagrama siguiente se describen las características principales de Instancia administrada:

![principales características](./media/sql-database-managed-instance/key-features.png)

Instancia administrada está pensado para convertirse en la plataforma preferida en los escenarios siguientes: 

- Clientes de SQL Server local o de IaaS que desean migrar sus aplicaciones a un servicio totalmente administrado con unos cambios de diseño mínimos.
- Fabricantes de software independientes que confían en bases de datos SQL, y que desean permitir a sus clientes migrar a la nube y conseguir así una ventaja competitiva sustancial o una cobertura de mercado global. 

En cuanto a la disponibilidad general, Instancia administrada quiere proporcionar una compatibilidad de área expuesta cercana al 100% con la última versión de SQL Server local mediante un plan de lanzamiento gradual. 

En la siguiente tabla se describen las principales diferencias y escenarios de uso previstos para SQL IaaS, Azure SQL Database e Instancia administrada de SQL Database:

| | Escenario de uso | 
| --- | --- | 
|Instancia administrada de SQL Database |Para aquellos clientes que desean migrar un gran número de aplicaciones desde ubicaciones locales o de IaaS, compiladas automáticamente o proporcionadas por fabricantes de software independientes, con el menor esfuerzo de migración posible, se recomienda Instancia administrada. Mediante el uso totalmente automatizado de [Azure Database Migration Service (DMS)](/sql/dma/dma-overview), los clientes pueden migrar mediante lift-and-shift sus instancias locales de SQL Server a una instancia administrada que ofrezca compatibilidad con SQL Server local y aislamiento completo de las instancias de cliente con compatibilidad nativa con redes virtuales.  Con Software Assurance, puede intercambiar sus licencias existentes para obtener descuentos en Instancia administrada de SQL Database mediante las [ventajas para uso híbrido de Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  Instancia administrada de SQL Database es el mejor destino de migración en la nube para instancias de SQL Server que requieren alta seguridad y una completa superficie de programación. |
|Azure SQL Database (única o grupal) |**Grupos elásticos**: para los clientes que desarrollan nuevas aplicaciones SaaS multiinquilino o que transforman intencionadamente sus aplicaciones locales existentes en una aplicación SaaS multiinquilino, se recomiendan los grupos elásticos. Las ventajas de este modelo son: <br><ul><li>Conversión del modelo de negocio de venta de licencias a la venta de suscripciones de servicio (para fabricantes de software independientes)</li></ul><ul><li>Aislamiento de inquilinos fácil y a toda prueba</li></ul><ul><li>Un modelo de programación simplificado centrado en una base de datos</li></ul><ul><li>La posibilidad de escalar horizontalmente sin alcanzar un límite estricto</li></ul>**Bases de datos únicas**: para aquellos clientes que desean desarrollar nuevas aplicaciones que no sean aplicaciones SaaS multiinquilino, se recomienda usar bases de datos únicas. Las ventajas de este modelo son:<ul><li>Un modelo de programación simplificado centrado en una base de datos</li></ul>  <ul><li>Rendimiento predecible para cada base de datos</li></ul>|
|Máquina virtual IaaS de SQL|Para los clientes que necesitan personalizar el sistema operativo o el servidor de bases de datos, así como los clientes que tienen requisitos específicos en cuanto a la ejecución de aplicaciones de terceros en paralelo con SQL Server (en la misma máquina virtual), se recomiendan las máquinas virtuales de SQL o IaaS como la mejor solución|
|||

## <a name="how-to-programmatically-identify-a-managed-instance"></a>Cómo identificar mediante programación una instancia administrada

En la tabla siguiente se muestran varias propiedades, accesibles mediante Transact SQL, que puede utilizar para detectar si la aplicación funciona con Instancia administrada y recuperar propiedades importantes.

|Propiedad|Valor|Comentario|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Este valor es igual que en SQL Database.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Este valor es igual que en SQL Database.|
|`SERVERPROPERTY('EngineEdition')`|8|Este valor identifica la instancia administrada de forma única.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nombre DNS completo de la instancia en el siguiente formato:<instanceName>.<dnsPrefix>.database.windows.net, donde <instanceName> es el nombre proporcionado por el cliente, mientras que <dnsPrefix> es la parte que se genera automáticamente del nombre, lo cual permite garantizar la exclusividad del nombre DNS global (por ejemplo, "wcus17662feb9ce98")|Por ejemplo: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="key-features-and-capabilities-of-a-managed-instance"></a>Principales características y funcionalidades de Instancia administrada 

> [!IMPORTANT]
> Una instancia administrada se ejecuta con todas las características de la versión más reciente de SQL Server, incluidas las operaciones en línea, las correcciones automáticas del plan y otras mejoras de rendimiento empresarial. 

| **Ventajas de PaaS** | **Continuidad del negocio** |
| --- | --- |
|No hay compras de hardware ni administración <br>Ninguna sobrecarga de administración a la hora de administrar la infraestructura subyacente <br>Aprovisionamiento y escalado de servicio rápidos <br>Aplicación de revisiones y actualización de versiones automatizadas <br>Integración con otros servicios de datos de PaaS |Acuerdo de Nivel de Servicio de tiempo de actividad del 99,99 %  <br>Alta disponibilidad integrada <br>Datos protegidos con copias de seguridad automatizadas <br>Período de retención de copia de seguridad configurable por el cliente (fijado en 7 días en la versión preliminar pública) <br>Copias de seguridad iniciadas por el usuario <br>Funcionalidad de restauración de base de datos a un momento dado |
|**Seguridad y cumplimiento normativo** | **Administración**|
|Entorno aislado (integración con red virtual, servicio de inquilino único y procesos y almacenamiento dedicados <br>Cifrado de los datos en tránsito <br>Autenticación de Azure AD, compatibilidad con el inicio de sesión único <br>Se adhiere a las mismas normas de cumplimiento estándar que Azure SQL Database <br>Auditoría de SQL <br>Detección de amenazas |API de Azure Resource Manager para automatizar el aprovisionamiento y escalado del servicio <br>Funcionalidad de Azure Portal para el aprovisionamiento y escalado manuales del servicio <br>Data Migration Service 

![Inicio de sesión único](./media/sql-database-managed-instance/sso.png) 

## <a name="vcore-based-purchasing-model-preview"></a>Modelo de compra basado en núcleos virtuales (versión preliminar)

El modelo de compra basado en núcleos virtuales (versión preliminar) le ofrece flexibilidad, control, transparencia y facilidad para trasladar las necesidades de carga de trabajo del entorno local a la nube. Este modelo le permite escalar los recursos de proceso, memoria y almacenamiento en función de las necesidades de carga de trabajo. El modelo de núcleos virtuales también permite disfrutar de hasta un 30 por ciento de ahorro con la [Ventaja de uso híbrido de Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

Un núcleo virtual representa una CPU lógica que cuenta con una opción para elegir entre varias generaciones de hardware.
- Las CPU lógicas Gen 4 se basan en procesadores Intel E5-2673 v3 (Haswell) de 2.4 GHz.
- Las CPU lógicas Gen 5 se basan en procesadores Intel E5-2673 v4 (Broadwell) de 2.3 GHz.

La tabla siguiente le servirá de ayuda para determinar cuál es la configuración óptima que debe seleccionar en función de los recursos de proceso, memoria, almacenamiento y E/S.

||Gen 4|Gen 5|
|----|------|-----|
|Hardware|Procesadores Intel E5-2673 v3 (Haswell) de 2.4 GHz, núcleo virtual SSD conectado equivalente a 1 PP (núcleo físico)|Procesadores Intel E5-2673 v4 (Broadwell) de 2.3 GHz, SSD eNVM rápido, núcleo virtual equivalente a 1 LP (Hyper-Threading)|
|Niveles de rendimiento|8, 16, 24 núcleos virtuales|8, 16, 24, 32, 40 núcleos virtuales|
|Memoria|7 GB por núcleo virtual|5,5 GB por núcleo virtual|
||||

## <a name="managed-instance-service-tier"></a>Nivel de servicio de Instancia administrada

Instancia administrada está inicialmente disponible en un único nivel de servicio, el nivel de uso general, que está diseñado para aplicaciones con los requisitos típicos de disponibilidad y latencia de E/S.

La siguiente lista describe las principales características del nivel de servicio de uso general: 

- Diseño de la mayoría de las aplicaciones empresariales con requisitos típicos de alta disponibilidad y rendimiento 
- Premium Storage de alto rendimiento (8 TB) 
- 100 bases de datos o instancias 

En este nivel, puede seleccionar almacenamiento y capacidad de proceso de forma independiente. 

El siguiente diagrama muestra los procesos activos y los nodos redundantes de este nivel de servicio.
 
![Nivel de servicio de uso general](./media/sql-database-managed-instance/general-purpose-service-tier.png) 

La tabla siguiente describe las principales características del nivel de servicio de uso general:

|Característica | DESCRIPCIÓN|
|---|---|
| Número de núcleos virtuales* | 8, 16, 24 (Gen 4)<br>8, 16, 24, 32, 40 (Gen5)|
| Versión de SQL Server | SQL Server (la versión más reciente disponible) |
| Tamaño de almacenamiento mínimo | 32 GB |
| Tamaño de almacenamiento máximo | 8 TB |
| Almacenamiento máximo por base de datos | 8 TB |
| IOPS de almacenamiento esperadas | 500-7500 IOPS por cada archivo de datos (depende del archivo de datos). Consulte, [Premium Storage](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes) |
| Número de archivos de datos (ROWS) por base de datos | Múltiple | 
| Número de archivos de registro (LOG) por base de datos | 1 | 
| Administración de copias de seguridad automatizadas | Sí |
| Alta disponibilidad | Según el almacenamiento remoto y [Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| Supervisión y métricas integradas de instancias y bases de datos | Sí |
| Aplicación automática de revisiones de software | Sí |
| Redes virtuales: implementación de Azure Resource Manager | Sí |
| Redes virtuales: modelo de implementación clásica de Azure | Sin  |
| Soporte técnico del portal | Sí|
|||

\* Un núcleo virtual representa la CPU lógica que se ofrece con la posibilidad de elegir entre varias generaciones de hardware. Las CPU lógicas Gen 4 se basan en procesadores Intel E5-2673 v3 (Haswell) de 2,4 GHz y las CPU lógicas Gen 5 se basan en procesadores Intel E5-2673 v4 (Broadwell) de 2,3 GHz. 

## <a name="advanced-security-and-compliance"></a>Conformidad y seguridad avanzada 

### <a name="managed-instance-security-isolation"></a>Aislamiento de seguridad de Instancia administrada 

Instancia administrada proporciona un aislamiento de seguridad adicional a partir de otros inquilinos en la nube de Azure. El aislamiento de seguridad incluye: 

- [Implementación nativa de redes virtuales](sql-database-managed-instance-vnet-configuration.md) y conectividad al entorno local mediante Azure ExpressRoute o VPN Gateway 
- El punto de conexión de SQL se expone solo mediante una dirección IP privada, lo que permite una conectividad segura desde una nube privada de Azure o redes híbridas
- Inquilino único con infraestructura subyacente dedicada (proceso, almacenamiento)

El siguiente diagrama describe el diseño del aislamiento: 

![alta disponibilidad](./media/sql-database-managed-instance/application-deployment-topologies.png)  

### <a name="auditing-for-compliance-and-security"></a>Auditoría de seguridad y cumplimiento 

La [auditoría de Instancia administrada](sql-database-managed-instance-auditing.md) realiza un seguimiento de eventos de bases de datos y los escribe en un registro de auditoría de su cuenta de Azure Storage. La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas. 

### <a name="data-encryption-in-motion"></a>Cifrado de datos en movimiento 

Instancia administrada protege los datos con cifrado de datos en movimiento mediante Seguridad de la capa de transporte.

Además de la seguridad de la capa de transporte, Instancia administrada de SQL Database ofrece la protección de la información confidencial en tránsito, en reposo y durante el procesamiento de consultas con [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted es una tecnología totalmente novedosa en el sector que ofrece una seguridad de datos sin parangón frente a las infracciones que implican el robo de datos críticos. Por ejemplo, con Always Encrypted, los números de las tarjetas de crédito siempre se almacenan cifrados en la base de datos, incluso durante el procesamiento de las consultas, lo que permite que solo los descifren personal autorizado o las aplicaciones que los necesitan para procesar los datos en el lugar en que se van a usar. 

### <a name="dynamic-data-masking"></a>Enmascaramiento de datos dinámicos 

El [enmascaramiento dinámico de datos](/sql/relational-databases/security/dynamic-data-masking) de SQL Database limita la exposición de información confidencial mediante su enmascaramiento a los usuarios sin privilegios. El enmascaramiento dinámico de datos ayuda a impedir el acceso no autorizado a datos confidenciales permitiéndole designar la cantidad de los datos confidenciales que se revelarán con un impacto mínimo en el nivel de aplicación. Es una característica de seguridad basada en directivas que oculta los datos confidenciales en el conjunto de resultados de una consulta sobre los campos designados de la base de datos, aunque que los datos de la base de datos no cambian. 

### <a name="row-level-security"></a>Seguridad de nivel de fila 

La [seguridad de nivel de fila](/sql/relational-databases/security/row-level-security) le permite controlar el acceso a las filas de una tabla de base de datos en función de las características del usuario que ejecuta una consulta (por ejemplo, la pertenencia a un grupo o el contexto de ejecución). La seguridad de nivel de fila (RLS) simplifica el diseño y la codificación de la seguridad de la aplicación. RLS permite implementar restricciones de acceso a filas de datos. Por ejemplo, garantiza que los empleados únicamente puedan acceder a aquellas filas de datos necesarios para su departamento o restringe el acceso solo a los datos relevantes. 

### <a name="threat-detection"></a>Detección de amenazas 

La [detección de amenazas de Instancia administrada](sql-database-managed-instance-threat-detection.md) complementa la [auditoría de Instancia administrada](sql-database-managed-instance-auditing.md), ya que proporciona una capa adicional de inteligencia de seguridad integrada en el servicio que detecta intentos inusuales y potencialmente dañinos para obtener acceso a las bases de datos o vulnerarlas. Recibirá alertas de actividades sospechosas, vulnerabilidades potenciales y ataques por inyección de código SQL, así como patrones anómalos de acceso a bases de datos. Las alertas de Detección de amenazas pueden verse en [Azure Security Center](https://azure.microsoft.com/services/security-center/) y proporcionar detalles de actividad sospechosa y la acción recomendada sobre cómo investigar y mitigar la amenaza.  

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integración de Azure Active Directory y autenticación multifactor 

SQL Database permite administrar centralmente las identidades de usuario de base de datos y otros servicios de Microsoft con la [integración de Azure Active Directory](sql-database-aad-authentication.md). Esta funcionalidad simplifica la administración de permisos y mejora la seguridad. Azure Active Directory admite la [autenticación multifactor](sql-database-ssms-mfa-authentication-configure.md) (MFA) para aumentar la seguridad tanto de los datos como de las aplicaciones y admite un proceso de inicio de sesión único. 

### <a name="authentication"></a>Autenticación 
La autenticación de SQL Database indica cómo los usuarios prueban su identidad al conectarse a la base de datos. SQL Database admite dos tipos de autenticación:  

- Autenticación de SQL, que usa un nombre de usuario y una contraseña.
- Autenticación de Azure Active Directory, la cual usa las identidades administradas por Azure Active Directory y es compatible con los dominios administrados e integrados. 

### <a name="authorization"></a>Autorización

La autorización indica las acciones que pueden llevar a cabo los usuarios en Azure SQL Database, algo que controlan los permisos de nivel de objeto y las pertenencias a roles de bases de datos de la cuenta de usuario. Instancia administrada tiene las mismas funcionalidades de autorización que SQL Server 2017. 

## <a name="database-migration"></a>Migración de bases de datos 

Instancia administrada está diseñado para escenarios de usuario con migración masiva de bases de datos desde implementaciones locales o de base de datos de IaaS. Instancia administrada admite varias opciones de migración de base de datos: 

### <a name="data-migration-service"></a>Data Migration Service

Azure Database Migration Service es un servicio totalmente administrado diseñado para permitir migraciones completas desde varios orígenes de base de datos hasta las plataformas de datos de Azure con un tiempo de inactividad mínimo. Este servicio simplifica las tareas necesarias para mover las bases de datos existentes de SQL Server y de terceros a Azure. Las opciones de implementación incluyen Azure SQL Database, Instancia administrada y SQL Server en una máquina virtual de Azure en versión preliminar pública. Consulte cómo [migrar su base de datos local a Instancia administrada mediante DMS](https://aka.ms/migratetoMIusingDMS). 

### <a name="backup-and-restore"></a>Copia de seguridad y restauración  

El enfoque de migración aprovecha las copias de seguridad de SQL en Azure Blob Storage. Las copias de seguridad almacenadas en Azure Blob Storage se pueden restaurar directamente en Instancia administrada. Para restaurar una base de datos SQL existente a una instancia administrada, haga lo siguiente:

- Use [Data Migration Service (DMS)](/sql/dma/dma-overview). Para ver un tutorial, consulte [Migración de SQL Server a Instancia administrada de Azure SQL Database](../dms/tutorial-sql-server-to-managed-instance.md) para restaurar desde un archivo de copia de seguridad de base de datos.
- Use el [comando RESTORE de T-SQL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql). 
  - Para ver un tutorial que muestra cómo restaurar el archivo de copia de seguridad de base de datos de Wide World Importers - Standard, consulte [Restauración de una copia de seguridad de datos a una instancia administrada de Azure SQL Database](sql-database-managed-instance-restore-from-backup-tutorial.md). Este tutorial muestra que tiene que cargar un archivo de copia de seguridad en el almacenamiento de blobs de Azure y protegerlo utilizando una clave de firma de acceso compartido (SAS).
  - Para obtener información sobre la restauración desde una URL, vea [Restauración de copias de seguridad nativas desde la dirección URL](sql-database-managed-instance-migrate.md#native-restore-from-url).
- [Importación desde un archivo BACPAC](sql-database-import.md)

## <a name="sql-features-supported"></a>Características de SQL admitidas 

El objetivo de Instancia administrada es proporcionar una compatibilidad de área expuesta cercana al 100% con un entorno local de SQL Server, con una incorporación gradual hasta alcanzar la disponibilidad general del servicio. Para obtener una lista de características y una comparación, consulte [Características comunes de SQL](sql-database-features.md).
 
Instancia administrada admite versiones anteriores hasta bases de datos de SQL 2008. Se admite la migración directa desde los servidores de bases de datos de SQL 2005, el nivel de compatibilidad para las bases de datos de SQL 2005 migradas se ha actualizado a SQL 2008. 
 
El siguiente diagrama describe la compatibilidad de área expuesta en Instancia administrada:  

![migración](./media/sql-database-managed-instance/migration.png) 

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>Diferencias principales entre SQL Server local e Instancia administrada 

Instancia administrada tiene la ventaja de estar siempre actualizada en la nube, lo cual significa que algunas características de la instancia local de SQL Server pueden ser obsoletas, estar retiradas o presentar alternativas. Hay casos específicos en los que las herramientas deben reconocer que una característica determinada funciona de forma ligeramente diferente o que el servicio no se está ejecutando en un entorno que no controla totalmente: 

- La alta disponibilidad está integrada y configurada previamente. Las características de alta disponibilidad de Always On no se exponen de la misma manera que en las implementaciones de SQL IaaS 
- Copias de seguridad automatizadas y restauración a un momento dado. El cliente puede iniciar copias de seguridad `copy-only` que no interfieren con la cadena de copias de seguridad automáticas. 
- Instancia administrada no permite especificar las rutas de acceso físicas completas por lo que todos los escenarios correspondientes se admiten de manera diferente: RESTORE DB no admite WITH MOVE, CREATE DB no permite rutas de acceso físicas, BULK INSERT funciona solo con Blobs de Azure, etc. 
- Instancia administrada admite la [autenticación de Azure AD](sql-database-aad-authentication.md) como alternativa en la nube a la autenticación de Windows. 
- Instancia administrada administra automáticamente el grupo de archivos XTP y los archivos de bases de datos que contienen objetos de OLTP en memoria
 
### <a name="managed-instance-administration-features"></a>Características de administración de Instancia administrada  

Instancia administrada permite al administrador del sistema centrarse en lo más importante para el negocio. Muchas actividades del administrador del sistema o DBA no son necesarias, o son sencillas. Por ejemplo, la instalación y aplicación de revisiones de SO o RDBMS, el cambio de tamaño y configuración de instancias dinámicos, las copias de seguridad, la replicación de bases de datos (incluidas las bases de datos del sistema), la configuración de la alta disponibilidad y los flujos de datos de configuración del estado y supervisión del rendimiento. 

> [!IMPORTANT]
> Para obtener una lista de las características compatibles, parcialmente compatibles o no compatibles, consulte [Características de SQL Database](sql-database-features.md). Para obtener una lista de diferencias de T-SQL en Instancia administrada en comparación con SQL Server, consulte [Managed Instance T-SQL Differences from SQL Server](sql-database-managed-instance-transact-sql-information.md) (Diferencias de T-SQL entre Instancia administrada y SQL Server)
 
## <a name="next-steps"></a>Pasos siguientes

- Para obtener una lista de características y una comparación, consulte [Características comunes de SQL](sql-database-features.md).
- Para más información acerca de la configuración de redes virtuales, consulte [Configuración de una red virtual de instancia administrada](sql-database-managed-instance-vnet-configuration.md).
- Para ver un tutorial que crea una instancia administrada y restaura una base de datos desde un archivo de copia de seguridad, consulte [Creación de una instancia administrada](sql-database-managed-instance-create-tutorial-portal.md).
- Para consultar un tutorial con Azure Database Migration Service (DMS) para la migración, consulte [Migración a Instancia administrada con DMS](../dms/tutorial-sql-server-to-managed-instance.md).
