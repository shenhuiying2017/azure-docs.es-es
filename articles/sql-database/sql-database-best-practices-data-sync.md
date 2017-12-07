---
title: "Procedimientos recomendados para Azure SQL Data Sync (versión preliminar) | Microsoft Docs"
description: "Conozca los procedimientos recomendados para configurar y ejecutar Azure SQL Data Sync (versión preliminar)."
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 1c8ad4b318d52b5cb6af284b3304cfa7ad35522b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="best-practices-for-sql-data-sync-preview"></a>Procedimientos recomendados para SQL Data Sync (versión preliminar) 

En este artículo se describen los procedimientos recomendados para Azure SQL Data Sync (versión preliminar).

Para obtener información general sobre SQL Data Sync (versión preliminar), vea [Sincronización de datos entre varias bases de datos locales y de la nube con SQL Data Sync (versión preliminar)](sql-database-sync-data.md).

## <a name="security-and-reliability"></a>Seguridad y fiabilidad

### <a name="client-agent"></a>Agente cliente

-   Instale el agente cliente con la cuenta de usuario con menos privilegios que tenga acceso al servicio de red.  
-   Instale el agente cliente en un equipo que no sea el equipo de SQL Server local.  
-   No registre ninguna base de datos local con más de un agente.    
    -   Evite esto aunque vaya a sincronizar tablas diferentes para distintos grupos de sincronización.  
    -   Registrar una base de datos local con varios agentes cliente puede causar problemas al eliminar uno de los grupos de sincronización.

### <a name="database-accounts-with-least-required-privileges"></a>Cuentas de base de datos con los mínimos privilegios requeridos

-   **Para la configuración de la sincronización**. Crear/modificar tabla, modificar base de datos, crear procedimiento, seleccionar/modificar esquema, crear el tipo definido por el usuario.

-   **Para la sincronización en curso**. Seleccionar/insertar/actualizar/eliminar en tablas seleccionadas para la sincronización y en tablas de seguimiento y metadatos de la sincronización, ejecutar un permiso en procedimientos almacenados creados por el servicio, ejecutar un permiso en tipos de tablas definidas por el usuario.

-   **Para el desaprovisionamiento**. Modificar en tablas que formen parte de la sincronización, seleccionar/eliminar en tablas de metadatos de la sincronización, controlar en tablas de seguimiento de la sincronización, procedimientos almacenados y tipos definidos por el usuario.

Azure SQL Database solo admite un único conjunto de credenciales. Para realizar estas tareas dentro de esta restricción, tenga en cuenta las siguientes opciones:

-   Cambie las credenciales para las distintas fases (por ejemplo, utilice *credencial1* para la configuración y *credencial2* para la sincronización en curso).  
-   Cambie el permiso de las credenciales (es decir, cambie el permiso una vez configurada la sincronización).

## <a name="setup"></a>Configuración

### <a name="database-considerations-and-constraints"></a> Restricciones y consideraciones de la base de datos

#### <a name="sql-database-instance-size"></a>Tamaño de la instancia de SQL Database

Al crear una nueva instancia de SQL Database, establezca el tamaño máximo de forma que siempre sea mayor que la base de datos que vaya a implementar. Si no lo hace así, se producirá un error de sincronización. Aunque SQL Data Sync (versión preliminar) no ofrecen un crecimiento automático, puede ejecutar el comando `ALTER DATABASE` para aumentar el tamaño de la base de datos una vez creada. Asegúrese de que no se sale de los límites de tamaño de la instancia de SQL Database.

> [!IMPORTANT]
> SQL Data Sync (versión preliminar) almacena metadatos adicionales con cada base de datos. No olvide tener en cuenta estos metadatos al calcular el espacio necesario. La cantidad de sobrecarga adicional guarda relación con la cantidad de tráfico y con el ancho de las tablas (por ejemplo, las tablas estrechas necesitan más sobrecarga).

### <a name="table-considerations-and-constraints"></a> Restricciones y consideraciones de la tabla

#### <a name="selecting-tables"></a>Selección de tablas

No deben incluir en un grupo de sincronización todas las tablas que se encuentran en una base de datos. Las tablas que se incluyen en un grupo de sincronización afectan a la eficiencia y los costos. Incluya tablas y también las tablas que de ellas dependan en un grupo de sincronización solo si así lo requieren las necesidades empresariales.

#### <a name="primary-keys"></a>Claves principales

Cada tabla de un grupo de sincronización debe tener una clave principal. El servicio SQL Data Sync (versión preliminar) no puede sincronizar una tabla que no tiene una clave principal.

Antes de usar SQL Data Sync (versión preliminar) en producción, pruebe el rendimiento de la sincronización inicial y en curso.

### <a name="provisioning-destination-databases"></a> Aprovisionamiento de bases de datos de destino

La versión preliminar de SQL Data Sync ofrece un aprovisionamiento automático de la base de datos básica.

En esta sección se describen los límites del aprovisionamiento de SQL Data Sync (versión preliminar).

#### <a name="autoprovisioning-limitations"></a>Limitaciones del aprovisionamiento automático

SQL Data Sync (versión preliminar) tiene las siguientes limitaciones de aprovisionamiento automático:

-   Seleccione solo las columnas creadas en la tabla de destino.  
    Todas las columnas que no forman parte del grupo de sincronización no se aprovisionan en las tablas de destino.
-   Los índices se crean únicamente para las columnas seleccionadas.  
    Si el índice de la tabla de origen tiene columnas que no forman parte del grupo de sincronización, estos índices no se aprovisionan en las tablas de destino.  
-   Los índices de las columnas de tipo XML no se aprovisionan.  
-   Las restricciones CHECK no se aprovisionan.  
-   Los desencadenadores existentes en las tablas de origen no se aprovisionan.  
-   No se crean vistas ni procedimientos almacenados en la base de datos de destino.

#### <a name="recommendations"></a>Recomendaciones

-   Use la funcionalidad de aprovisionamiento automático de SQL Data Sync (versión preliminar) solo cuando vaya a probar el servicio.  
-   Para entornos de producción, aprovisione el esquema de base de datos.

### <a name="locate-hub"></a> Dónde encontrar la base de datos central

#### <a name="enterprise-to-cloud-scenario"></a>Escenario de empresa a la nube

Para minimizar la latencia, mantenga la base de datos central cerca de la mayor concentración del tráfico de la base de datos del grupo de sincronización.

#### <a name="cloud-to-cloud-scenario"></a>Escenario de nube a nube

-   Cuando todas las bases de datos de un grupo de sincronización están en un centro de datos, el concentrador debe estar en el mismo centro de datos. Esta configuración reduce la latencia y el costo de la transferencia de datos entre centros de datos.
-   Cuando las bases de datos de un grupo de sincronización están en varios centros de datos, el concentrador debe estar en el mismo centro de datos que la mayoría de las bases de datos y del tráfico de la base de datos.

#### <a name="mixed-scenarios"></a>Escenarios combinados

Aplique las instrucciones anteriores a configuraciones de grupos de sincronización complejas, como aquellas en las que existe una combinación de escenarios de la empresa a la nube y de la nube a la nube.

## <a name="sync"></a>Sync

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a> Evitar una sincronización inicial lenta y costosa

En esta sección se describe la sincronización inicial de un grupo de sincronización. Obtenga información sobre cómo ayudar a evitar que una sincronización inicial tarde más tiempo y sea más costosa de lo necesario.

#### <a name="how-initial-sync-works"></a>Funcionamiento de la sincronización inicial

Al crear un grupo de sincronización, comience con los datos en una sola base de datos. Si tiene datos en varias bases de datos, SQL Data Sync (versión preliminar) trata cada fila como un conflicto que hay que resolver. La resolución de conflictos hace que la sincronización inicial sea lenta. Si tiene datos en varias bases de datos, la sincronización inicial puede tardar entre varios días y varios meses, según el tamaño de la base de datos.

Si las bases de datos están en distintos centros de datos, cada fila debe desplazarse entre ellos. Esto aumenta el costo de una sincronización inicial.

#### <a name="recommendation"></a>Recomendación

Si es posible, comience con datos en una sola base de datos del grupo de sincronización.

### <a name="design-to-avoid-synchronization-loops"></a> Diseño para evitar bucles de sincronización

Un bucle de sincronización se produce cuando hay referencias circulares dentro de un grupo de sincronización. En ese caso, cada cambio realizado en una base de datos resulta interminable y se replica de forma circular a través de las bases de datos del grupo de sincronización.   

Asegúrese de evitar los bucles de sincronización, ya que causan una degradación del rendimiento y podrían aumentar significativamente los costos.

### <a name="handling-changes-that-fail-to-propagate"></a> Cambios que no se propagan

#### <a name="reasons-that-changes-fail-to-propagate"></a>Razones por las que los cambios no se propagan

Puede que los cambios no se propaguen por alguno de los siguientes motivos:

-   Incompatibilidad de esquemas o tipos de datos.
-   Insertar valores NULL en columnas que no aceptan valores NULL.
-   Infringir restricciones de claves principales.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>¿Qué ocurre cuando los cambios no se propagan?

-   Se muestra un estado de **advertencia** en el grupo de sincronización.
-   Los detalles se encuentran en el visor de registro de la interfaz de usuario del portal.
-   Si el problema no se resuelve en 45 días, la base de datos se convierte en obsoleta.

> [!NOTE]
> Estos cambios no se propagan nunca. La única manera de recuperarse en este caso es volver a crear el grupo de sincronización.

#### <a name="recommendation"></a>Recomendación

Supervise el estado del grupo de sincronización y la base de datos con regularidad desde la interfaz del portal y de registro.


## <a name="maintenance"></a>Mantenimiento

### <a name="avoid-out-of-date-databases-and-sync-groups"></a> Evitar bases de datos y grupos de sincronización obsoletos

Un grupo de sincronización o una base de datos de un grupo de sincronización pueden llegar a estar obsoletos. Cuando el estado de un grupo de sincronización es **obsoleto**, deja de funcionar. Cuando el estado de la base de datos es **obsoleto**, se pueden perder datos. Es mejor evitar esta situación en lugar de intentar recuperarse de este error.

#### <a name="avoid-out-of-date-databases"></a>Evitar bases de datos obsoletas

El estado de la base de datos se establece en **obsoleto** cuando ha estado sin conexión durante cuarenta y cinco días o más. Para evitar el estado **obsoleto** en una base de datos, asegúrese de que ninguna de las bases de datos esté sin conexión durante cuarenta y cinco días o más.

#### <a name="avoid-out-of-date-sync-groups"></a>Evitar grupos de sincronización obsoletos

El estado de un grupo de sincronización se establece en **obsoleto** cuando cualquier cambio en el grupo de sincronización no puede propagarse al resto del grupo de sincronización durante cuarenta y cinco días o más. Para evitar el estado **obsoleto** de un grupo de sincronización, compruebe periódicamente el registro de historial del grupo de sincronización. Asegúrese de que todos los conflictos se resuelvan y los cambios se propaguen correctamente en las bases de datos del grupo de sincronización.

Un grupo de sincronización puede producir un error al aplicar un cambio por alguno de estos motivos:

-   Incompatibilidad de esquemas entre tablas.
-   Incompatibilidad de datos entre tablas.
-   Insertar una fila con un valor NULL en una columna que no admite valores NULL.
-   Actualizar una fila con un valor que infringe una restricción de clave externa.

Para impedir grupos de sincronización obsoletos, haga lo siguiente:

-   Actualice el esquema para permitir los valores incluidos en las filas con errores.
-   Actualice los valores de la clave externa para incluir los valores incluidos en las filas con errores.
-   Actualice los valores de datos en la fila con errores para que sean compatibles con el esquema o con las claves externas de la base de datos de destino.

### <a name="avoid-deprovisioning-issues"></a> Evitar problemas de cancelación del aprovisionamiento

En determinadas circunstancias, anular el registro de una base de datos con un agente cliente puede provocar errores de sincronización.

#### <a name="scenario"></a>Escenario

1. El grupo de sincronización A se creó con una instancia de SQL Database y una base de datos de SQL Server local, asociada al agente local 1.
2. La misma base de datos local está registrada con el agente local 2 (este agente no está asociado a ningún grupo de sincronización).
3. Al anular el registro de la base de datos local del agente local 2, se eliminan las tablas de seguimiento y de metadatos del grupo de sincronización A en la base de datos local.
4. En las operaciones del grupo de sincronización A, se producirá el error: "La operación actual no se pudo completar porque la base de datos no está aprovisionada para la sincronización o no tiene permisos para las tablas de configuración de sincronización".

#### <a name="solution"></a>Solución

Para evitar esta situación, no registre una base de datos con más de un agente.

Para recuperarse de esta situación:

1. Quite la base de datos de todos los grupos de sincronización a los que pertenezca.  
2. Vuelva a agregar la base de datos a cada uno de los grupos de sincronización de los que la ha quitado.  
3. Implemente cada grupo de sincronización afectado (esta acción aprovisiona la base de datos).  

### <a name="modifying-your-sync-group"></a> Modificación de un grupo de sincronización

No intente quitar una base de datos de un grupo de sincronización para después editar el grupo de sincronización sin implementar primero uno de los cambios.

En su lugar, primero quite una base de datos de un grupo de sincronización. A continuación, implemente el cambio y espere a que el desaprovisionamiento finalice. Una vez finalizado el desaprovisionamiento, puede editar el grupo de sincronización e implementar los cambios.

Si intenta quitar una base de datos para después modificar un grupo de sincronización sin implementar primero uno de los cambios, se produce un error en una de las dos operaciones. La interfaz del portal puede quedar en un estado incoherente. En este caso, actualice la página para restaurar el estado correcto.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre SQL Data Sync (versión preliminar), vea:

-   [Sincronización de datos entre varias bases de datos locales y de la nube con SQL Data Sync (versión preliminar)](sql-database-sync-data.md)
-   [Configuración de Azure SQL Data Sync (versión preliminar)](sql-database-get-started-sql-data-sync.md)
-   [Supervisar SQL Data Sync (versión preliminar) con Log Analytics de OMS](sql-database-sync-monitor-oms.md)
-   [Solución de problemas de SQL Data Sync (versión preliminar)](sql-database-troubleshoot-data-sync.md)  
-   Para obtener ejemplos completos de PowerShell que muestren cómo configurar SQL Data Sync (versión preliminar):  
    -   [Uso de PowerShell para sincronizar entre varias bases de datos SQL de Azure.](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Uso de PowerShell para realizar la sincronización entre Azure SQL Database y una base de datos de SQL Server local](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Descarga de la documentación de la API de REST de SQL Data Sync (versión preliminar)](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)  

Para obtener más información sobre SQL Database, vea:

-   [Información general de SQL Database](sql-database-technical-overview.md)
-   [Administración del ciclo de vida de las aplicaciones](https://msdn.microsoft.com/library/jj907294.aspx)
