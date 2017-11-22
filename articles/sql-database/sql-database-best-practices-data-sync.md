---
title: Procedimientos recomendados para Azure SQL Data Sync | Microsoft Docs
description: Conozca los procedimientos recomendados para configurar y ejecutar SQL Data Sync de Azure
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 7d9529fc8acd9347b0505b1c578febc1c2219b37
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2017
---
# <a name="best-practices-for-sql-data-sync-preview"></a>Procedimientos recomendados para SQL Data Sync (versión preliminar) 

En este artículo se describen los procedimientos recomendados para la versión preliminar de SQL Data Sync.

Para obtener información general sobre SQL Data Sync, vea [Sincronización de datos entre varias bases de datos locales y de la nube con SQL Data Sync](sql-database-sync-data.md).

## <a name="security-and-reliability"></a>Seguridad y fiabilidad

### <a name="client-agent"></a>Agente cliente

-   Instale el agente cliente con la cuenta con menos privilegios que tenga acceso al servicio de red.

-   Es mejor si el agente cliente está instalado en un equipo distinto al equipo de SQL Server local.

-   No registre ninguna base de datos local con más de un agente.

-   Aunque vaya a sincronizar tablas diferentes para distintos grupos de sincronización.

-   Registrar una base de datos local con varios agentes cliente puede causar problemas al eliminar uno de los grupos de sincronización.

### <a name="database-accounts-with-least-required-privilege"></a>Cuentas de base de datos con los mínimos privilegios requeridos

-   **Para la configuración de la sincronización**. Crear/modificar tabla, modificar base de datos, crear procedimiento, seleccionar/modificar esquema, crear el tipo definido por el usuario.

-   **Para la sincronización en curso**. Seleccionar/insertar/actualizar/eliminar en tablas seleccionadas para la sincronización y en tablas de seguimiento y metadatos de la sincronización, ejecutar un permiso en procedimientos almacenados creados por el servicio, ejecutar un permiso en tipos de tablas definidas por el usuario.

-   **Para la cancelación del aprovisionamiento**. Modificar en tablas que formen parte de la sincronización, seleccionar/eliminar en tablas de metadatos de la sincronización, controlar en tablas de seguimiento de la sincronización, procedimientos almacenados y tipos definidos por el usuario.

**¿Cómo puede usar esta información cuando solo hay una credencial para una base de datos del grupo de sincronización?**

-   Cambie las credenciales para las distintas fases (por ejemplo, utilice *credencial1* para la configuración y *credencial2* para la sincronización en curso).

-   Cambie el permiso de las credenciales (es decir, cambie el permiso una vez configurada la sincronización).

## <a name="setup"></a>Configuración

### <a name="database-considerations-and-constraints"></a> Restricciones y consideraciones de la base de datos

#### <a name="sql-database-instance-size"></a>Tamaño de la instancia de SQL Database

Al crear una nueva instancia de SQL Database, establezca el tamaño máximo de forma que siempre sea mayor que la base de datos que vaya a implementar. Si no lo hace así, la sincronización fallará. Aunque no haya ningún crecimiento automático, puede usar la instrucción ALTER DATABASE para aumentar el tamaño de la base de datos una vez creada. Asegúrese de que no se sale de los límites de tamaño de la instancia de SQL Database.

> [!IMPORTANT]
> SQL Data Sync almacena metadatos adicionales con cada base de datos. No olvide tener en cuenta estos metadatos al calcular el espacio necesario. La cantidad de sobrecarga adicional está regida por la cantidad de tráfico y por el ancho de las tablas (por ejemplo, las tablas estrechas necesitan más sobrecarga).

### <a name="table-considerations-and-constraints"></a> Restricciones y consideraciones de la tabla

#### <a name="selecting-tables"></a>Selección de tablas

No todas las tablas de una base de datos tienen que estar en un grupo de sincronización. La selección de qué tablas se van a incluir en un grupo de sincronización y qué tablas se van a excluir (o se van a incluir en otro grupo de sincronización) puede repercutir en la eficiencia y los costos. Incluya en un grupo de sincronización únicamente las tablas que las necesidades empresariales requieran y las tablas de las que estas dependan.

#### <a name="primary-keys"></a>Claves principales

Cada tabla de un grupo de sincronización debe tener una clave principal. El servicio SQL Data Sync (versión preliminar) no puede sincronizar ninguna tabla que no tenga una clave principal.

Antes de ponerlo en marcha, pruebe el rendimiento de la sincronización inicial y en curso.

### <a name="provisioning-destination-databases"></a> Aprovisionamiento de bases de datos de destino

La vista previa de SQL Data Sync (versión preliminar) proporciona un aprovisionamiento automático de la base de datos básica.

En esta sección se describen los límites del aprovisionamiento de SQL Data Sync (versión preliminar).

#### <a name="auto-provisioning-limitations"></a>Limitaciones del aprovisionamiento automático

A continuación, se describen los límites del aprovisionamiento automático de SQL Data Sync (versión preliminar).

-   Solo las columnas seleccionadas se crean en la tabla de destino.
Por lo tanto, si algunas columnas no forman parte del grupo de sincronización, estas columnas no se aprovisionan en las tablas de destino.

-   Los índices se crean únicamente para las columnas seleccionadas.
Si el índice de la tabla de origen tiene columnas que no forman parte del grupo de sincronización, estos índices no se aprovisionan en las tablas de destino.

-   Los índices en las columnas de tipo XML no se aprovisionan.

-   Las restricciones CHECK no se aprovisionan.

-   Los desencadenadores existentes en las tablas de origen no se aprovisionan.

-   No se crean vistas ni procedimientos almacenados en la base de datos de destino.

#### <a name="recommendations"></a>Recomendaciones

-   Use la capacidad de aprovisionamiento automático solo para probar el servicio.

-   Para entornos de producción, debe aprovisionar el esquema de base de datos.

### <a name="locate-hub"></a>Dónde encontrar la base de datos central

#### <a name="enterprise-to-cloud-scenario"></a>Escenario de empresa a la nube

Para minimizar la latencia, mantenga la base de datos central cerca de la mayor concentración del tráfico de la base de datos del grupo de sincronización.

#### <a name="cloud-to-cloud-scenario"></a>Escenario de nube a nube

-   Cuando todas las bases de datos de un grupo de sincronización están en un centro de datos, el concentrador debe estar en el mismo centro de datos. Esta configuración reduce la latencia y el costo de la transferencia de datos entre centros de datos.

-   Cuando las bases de datos de un grupo de sincronización están en varios centros de datos, el concentrador debe estar en el mismo centro de datos que la mayoría de las bases de datos y del tráfico de la base de datos.

#### <a name="mixed-scenarios"></a>Escenarios combinados

Las instrucciones anteriores se aplican a configuraciones más complejas de grupos de sincronización.

## <a name="sync"></a>Sync

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a> Evitar una sincronización inicial lenta y costosa

En esta sección se describe la sincronización inicial de un grupo de sincronización y lo que puede hacer para evitar que una sincronización inicial tarde más tiempo del necesario y cueste más de lo que debería.

#### <a name="how-initial-synchronization-works"></a>Cómo funciona la sincronización inicial

Al crear un grupo de sincronización, comience con los datos en una sola base de datos. Si tiene datos en varias bases de datos, SQL Data Sync (versión preliminar) trata cada fila como un conflicto que hay que resolver. La resolución del conflicto provoca la ralentización de la sincronización inicial, que puede tardar de varios días a varios meses en función del tamaño de la base de datos.

Además, si las bases de datos están en distintos centros de datos, los costos de la sincronización inicial son superiores a lo que sería necesario, puesto que cada fila debe viajar entre los distintos centros de datos.

#### <a name="recommendation"></a>Recomendación

Siempre que sea posible, comience con datos en una sola base de datos del grupo de sincronización.

### <a name="design-to-avoid-synchronization-loops"></a> Diseño para evitar bucles de sincronización

Un bucle de sincronización se produce cuando hay referencias circulares dentro de un grupo de sincronización de modo que cada cambio realizado en una base de datos se replica a través de las bases de datos del grupo de sincronización de forma circular e infinita. Es importante evitar los bucles de sincronización porque degradan el rendimiento y pueden aumentar significativamente los costos.

### <a name="handling-changes-that-fail-to-propagate"></a> Control de cambios que no se propagan

#### <a name="reasons-that-changes-fail-to-propagate"></a>Razones por las que los cambios no se propagan

Los cambios pueden no propagarse por diferentes motivos. Algunas causas podrían ser:

-   Incompatibilidad de esquemas o tipos de datos.

-   Intentar insertar valores NULL en columnas que no aceptan valores NULL.

-   Infringir restricciones de claves principales.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>¿Qué ocurre cuando los cambios no se propagan?

-   Se muestra un estado de advertencia en el grupo de sincronización.

-   Los detalles se encuentran en el visor de registro de la interfaz de usuario del portal.

-   Si el problema no se resuelve en 45 días, la base de datos se convierte en obsoleta.

> [!NOTE]
> Estos cambios no se propagan nunca. La única manera de recuperarse es volver a crear el grupo de sincronización.

#### <a name="recommendation"></a>Recomendación

Supervise el estado del grupo de sincronización y la base de datos con regularidad desde la interfaz del portal y de registro.


## <a name="maintenance"></a>Mantenimiento

### <a name="avoid-out-of-date-databases-and-sync-groups"></a> Evitar bases de datos y grupos de sincronización obsoletos

Un grupo de sincronización o una base de datos dentro de un grupo de sincronización pueden llegar a estar obsoletos. Cuando el estado de un grupo de sincronización es "obsoleto", deja de funcionar. Cuando el estado de la base de datos es "obsoleto", se pueden perder datos. Es mejor evitar estas situaciones que recuperarse de ellas.

#### <a name="avoid-out-of-date-databases"></a>Evitar bases de datos obsoletas

El estado de la base de datos se establece en obsoleto cuando ha estado sin conexión durante 45 días o más. Para evitar el estado obsoleto en una base de datos, asegúrese de que ninguna de las bases de datos esté sin conexión durante 45 días o más.

#### <a name="avoid-out-of-date-sync-groups"></a>Evitar grupos de sincronización obsoletos

El estado de un grupo de sincronización se establece en obsoleto cuando cualquier cambio en el grupo de sincronización no puede propagarse al resto del grupo de sincronización durante 45 días o más. Para evitar el estado obsoleto de un grupo de sincronización, compruebe periódicamente si hay registros de historial del grupo de sincronización. Asegúrese de que todos los conflictos se resuelvan y los cambios se propaguen correctamente en las bases de datos del grupo de sincronización.

Algunos de los motivos por los que un grupo de sincronización puede tener problemas para aplicar cambios son:

-   Incompatibilidad de esquemas entre tablas.

-   Incompatibilidad de datos entre tablas.

-   Insertar una fila con un valor NULL en una columna que no admite valores NULL.

-   Actualizar una fila con un valor que infringe una restricción de clave externa.

Para impedir grupos de sincronización obsoletos, haga lo siguiente:

-   Actualice el esquema para permitir los valores incluidos en las filas con errores.

-   Actualice los valores de la clave externa para incluir los valores incluidos en las filas con errores.

-   Actualice los valores de datos en la fila con errores para que sea compatible con el esquema o con las claves externas de la base de datos de destino.

### <a name="avoid-deprovisioning-issues"></a> Evitar problemas de cancelación del aprovisionamiento

En determinadas circunstancias, anular el registro de una base de datos con un agente cliente puede provocar errores en las sincronizaciones.

#### <a name="scenario"></a>Escenario

1. El grupo de sincronización A se creó con una instancia de SQL Database y una base de datos de SQL Server local, asociada al agente local 1.

2. La misma base de datos local está registrada con el agente local 2 (este agente no está asociado a ningún grupo de sincronización).

3. Al anular el registro de la base de datos local del agente local 2, se eliminan las tablas de seguimiento y de metadatos del grupo de sincronización A en la base de datos local.

4. Ahora, en las operaciones del grupo de sincronización A, se producirá el error: "La operación actual no se pudo completar porque la base de datos no está aprovisionada para la sincronización o no tiene permisos para las tablas de configuración de sincronización".

#### <a name="solution"></a>Solución

Evite la situación desde el principio. Para ello, no registre nunca una base de datos con más de un agente.

Para recuperarse de esta situación:

1. Quite la base de datos de todos los grupos de sincronización a los que pertenezca.

2. Vuelva a agregar la base de datos a cada uno de los grupos de sincronización de los que la ha quitado.

3. Implemente cada grupo de sincronización afectado (que aprovisiona la base de datos).

### <a name="modifying-your-sync-group"></a> Modificación de un grupo de sincronización

No intente quitar una base de datos de un grupo de sincronización para después editar el grupo de sincronización sin implementar primero uno de los cambios.

Primero, quite una base de datos de un grupo de sincronización. A continuación, implemente el cambio y espere a que la cancelación del aprovisionamiento finalice. Una vez completada esta operación, puede editar el grupo de sincronización e implementar los cambios.

Si intenta quitar una base de datos para después modificar un grupo de sincronización sin implementar primero uno de los cambios, se produce un error en una de las dos operaciones y la interfaz del portal puede quedar en un estado incoherente. En este caso, puede actualizar la página para restaurar el estado correcto.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre SQL Data Sync, consulte:

-   [Sincronización de datos entre varias bases de datos locales y de la nube con SQL Data Sync](sql-database-sync-data.md)
-   [Configuración de Azure SQL Data Sync](sql-database-get-started-sql-data-sync.md)
-   [Supervisión de Azure SQL Data Sync con Log Analytics de OMS](sql-database-sync-monitor-oms.md)
-   [Solución de problemas de SQL Data Sync de Azure](sql-database-troubleshoot-data-sync.md)

-   Para obtener ejemplos completos de PowerShell que muestren cómo configurar SQL Data Sync:
    -   [Uso de PowerShell para sincronizar entre varias bases de datos SQL de Azure.](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Uso de PowerShell para realizar la sincronización entre Azure SQL Database y una base de datos de SQL Server local](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Descarga de la documentación de la API de REST de SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Para más información sobre SQL Database, consulte:

-   [Información general de SQL Database](sql-database-technical-overview.md)
-   [Administración del ciclo de vida de las aplicaciones](https://msdn.microsoft.com/library/jj907294.aspx)
