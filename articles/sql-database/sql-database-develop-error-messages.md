---
title: 'Códigos de error de SQL: error de conexión de base de datos | Microsoft Docs'
description: 'Obtenga información acerca de los códigos de error de SQL de las aplicaciones cliente de la SQL Database, como los errores de conexión de base de datos más comunes, los problemas de copia de la base de datos y los errores generales. '
keywords: código de error de SQL, acceder a SQL, error de conexión de base de datos, códigos de error de SQL
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: bf94e99d84b7f5b727b185209ba0288096b30607
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195335"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>Códigos de error para las aplicaciones cliente de SQL Database: errores de conexión de bases de datos y otros problemas

En este artículo se enumeran los códigos de error de SQL de la aplicación cliente de SQL Database, incluidos los errores de conexión de base de datos, los errores transitorios, los errores de regulación de recursos, los problemas de copia de la base de datos, el grupo elástico y otros errores. La mayoría de las categorías son específicas de Azure SQL Database y no se aplican a Microsoft SQL Server. Consulte también los [mensajes de error del sistema](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Errores de conexión de base de datos, errores transitorios y otros errores temporales
En la siguiente tabla se abordan los códigos de error de SQL para errores de pérdida de conexión y otros errores transitorios que pueden surgir cuando la aplicación intenta obtener acceso a la SQL Database. Para obtener tutoriales de introducción acerca de cómo conectarse a Azure SQL Database, consulte [Conexión a Azure SQL Database](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Errores de conexión de base de datos y errores transitorios más comunes
La infraestructura de Azure ofrece la posibilidad de volver a configurar dinámicamente servidores cuando surgen cargas de trabajo pesadas en el servicio de SQL Database.  Este comportamiento dinámico podría dar lugar a que el programa cliente perdiera su conexión a SQL Database. Este tipo de condición de error se conoce como *error transitorio*.

Se recomienda encarecidamente que el programa cliente tenga lógica de reintento para poder tratar de restablecer una conexión después de dar tiempo a que se corrijan los errores transitorios.  Se recomienda un retraso de 5 segundos antes del primer reintento. Si se vuelve a intentar después de un retraso menor de 5 segundos, se correrá el riesgo de sobrecargar el servicio en la nube. Para cada intento siguiente el retraso debe aumentar exponencialmente, hasta un máximo de 60 segundos.

Los errores transitorios suelen manifestarse como uno de los siguientes mensajes de error de los programas cliente:

* La base de datos &lt;db_name&gt; del servidor &lt;Azure_instance&gt; no está disponible actualmente. Vuelva a intentar la conexión más tarde. Si el problema continúa, póngase en contacto con el servicio de asistencia al cliente y proporcióneles el identificador de seguimiento de sesión de &lt;session_id&gt;.
* La base de datos &lt;db_name&gt; del servidor &lt;Azure_instance&gt; no está disponible actualmente. Vuelva a intentar la conexión más tarde. Si el problema continúa, póngase en contacto con el servicio de asistencia al cliente y proporcióneles el identificador de seguimiento de sesión de &lt;session_id&gt;. (Microsoft SQL Server, error: 40613)
* El host remoto cerró a la fuerza una conexión ya existente.
* System.Data.Entity.Core.EntityCommandExecutionException: Se produjo un error al ejecutar la definición del comando. Vea la excepción interna para obtener detalles. ---> System.Data.SqlClient.SqlException: Error en el nivel del transporte al recibir los resultados del servidor. (proveedor: proveedor de sesión, error: 19: La conexión física es inservible)
* Error al tratar de conectarse a una base de datos secundaria porque la base de datos está en proceso de reconfiguración y ocupada aplicando nuevas páginas en mitad de una transacción activa en la base de datos principal. 

Para obtener ejemplos de lógica de reintento, vea:

* [Bibliotecas de conexiones para SQL Database y SQL Server](sql-database-libraries.md) 
* [Acciones para corregir errores de conexión y errores transitorios en SQL Database](sql-database-connectivity-issues.md)

Una explicación del *período de bloqueo* para clientes que usan ADO.NET está disponible en [Grupos de conexión de SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Códigos de errores transitorios
Los siguientes errores son transitorios y se deben volver a probar en la lógica de aplicación: 

| Código de error | Severity | DESCRIPCIÓN |
| ---:| ---:|:--- |
| 4060 |16 |No se puede abrir la base de datos "%.&#x2a;ls" solicitada por el inicio de sesión. Error de inicio de sesión. |
| 40197 |17 |Error en el servicio al procesar la solicitud. Vuelva a intentarlo. Código de error %d.<br/><br/>Recibirá este error cuando el servicio esté inactivo debido a actualizaciones de software o hardware, errores de hardware u otros problemas de conmutación por error. El código de error (%d) incrustado en el mensaje de error 40197 proporciona información adicional sobre el tipo de error o conmutación por error que se ha producido. Algunos ejemplos de los códigos de error que se incrustan dentro del mensaje de error 40197 son 40020, 40143, 40166 y 40540.<br/><br/>Al volver a conectarse al servidor de SQL Database se conectará automáticamente a una copia correcta de su base de datos. La aplicación debe detectar el error 40197, registrar el código de error incrustado (%d) dentro del mensaje para solucionar problemas y volver a conectarse a SQL Database hasta que los recursos estén disponibles; entonces, la conexión se establecerá de nuevo. |
| 40501 |20 |El servicio está ocupado actualmente. Vuelva a intentar la solicitud después de 10 segundos. Identificador de incidente: %ls. Código: %d.<br/><br/>Para más información, consulte:<br/>• [Límites de recursos de SQL Database](sql-database-service-tiers-dtu.md). |
| 40613 |17 |La base de datos '%.&#x2a;ls' en el servidor '%.&#x2a;ls' no está disponible actualmente. Vuelva a intentar la conexión más tarde. Si el problema continúa, póngase en contacto con el servicio de soporte al cliente y proporcióneles el id. de seguimiento de sesión de '%.&#x2a;ls'. |
| 49918 |16 |No se puede procesar la solicitud. No hay suficientes recursos para procesar la solicitud.<br/><br/>El servicio está ocupado actualmente. Vuelva a intentar realizar la solicitud más tarde. |
| 49919 |16 |No se procesar, crear ni actualizar la solicitud. Hay demasiadas operaciones de creación o actualización en curso para la suscripción "%ld".<br/><br/>El servicio está ocupado procesando varias solicitudes de creación o actualización para su suscripción o servidor. Actualmente las solicitudes están bloqueadas para la optimización de recursos. Consulta [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para las operaciones pendientes. Espere a que se completen solicitudes de creación o actualización pendientes o elimine una de las solicitudes pendientes y vuelva a intentar la solicitud más tarde. |
| 49920 |16 |No se puede procesar la solicitud. Hay demasiadas operaciones en curso para la suscripción "%ld".<br/><br/>El servicio está ocupado procesando varias solicitudes para esta suscripción. Actualmente las solicitudes están bloqueadas para la optimización de recursos. Consulta [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para el estado de la operación. Espere a que las solicitudes pendientes se hayan completado o elimine una de las solicitudes pendientes y vuelva a intentar la solicitud más tarde. |
| 4221 |16 |No se pudo iniciar sesión en el secundario de lectura debido a una espera prolongada en HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING. La réplica no está disponible para el inicio de sesión porque faltan las versiones de las filas de transacciones que estaban en proceso cuando se recicló la réplica. Se puede resolver el problema al revertir o asignar las transacciones activas en la réplica principal. Se pueden minimizar las repeticiones de esta condición al evitar escribir transacciones en la principal. |

## <a name="database-copy-errors"></a>Errores de copia de base de datos
Pueden encontrarse los siguientes errores al copiar una base de datos en Azure SQL Database. Para más información, vea [Copiar una instancia de Azure SQL Database](sql-database-copy.md).

| Código de error | Severity | DESCRIPCIÓN |
| ---:| ---:|:--- |
| 40635 |16 |El cliente con la dirección IP '%.&#x2a;ls' está deshabilitado temporalmente. |
| 40637 |16 |Crear copia de base de datos está deshabilitado actualmente. |
| 40561 |16 |Error al copiar la base de datos. No existe la base de datos de origen o de destino. |
| 40562 |16 |Error al copiar la base de datos. La base de datos de origen se ha quitado. |
| 40563 |16 |Error al copiar la base de datos. La base de datos de destino se ha quitado. |
| 40564 |16 |No se pudo copiar la base de datos debido a un error interno. Quite la base de datos de destino e inténtelo de nuevo. |
| 40565 |16 |Error al copiar la base de datos. No se permite más de una copia de base de datos simultánea para el mismo origen. Quite la base de datos de destino y vuelva a intentarlo más adelante. |
| 40566 |16 |No se pudo copiar la base de datos debido a un error interno. Quite la base de datos de destino e inténtelo de nuevo. |
| 40567 |16 |No se pudo copiar la base de datos debido a un error interno. Quite la base de datos de destino e inténtelo de nuevo. |
| 40568 |16 |Error al copiar la base de datos. La base de datos de origen ha dejado de estar disponible. Quite la base de datos de destino e inténtelo de nuevo. |
| 40569 |16 |Error al copiar la base de datos. La base de datos de destino ha dejado de estar disponible. Quite la base de datos de destino e inténtelo de nuevo. |
| 40570 |16 |No se pudo copiar la base de datos debido a un error interno. Quite la base de datos de destino y vuelva a intentarlo más adelante. |
| 40571 |16 |No se pudo copiar la base de datos debido a un error interno. Quite la base de datos de destino y vuelva a intentarlo más adelante. |

## <a name="resource-governance-errors"></a>Errores de regulación de recursos
La causa de los siguientes errores es un uso excesivo de recursos mientras se trabaja con Azure SQL Database. Por ejemplo: 

* Una transacción ha permanecido abierta durante demasiado tiempo.
* Una transacción contiene demasiados bloqueos.
* Una aplicación consume demasiada memoria.
* Una aplicación consume demasiado espacio en `TempDb` .

Temas relacionados:

* Encontrará información más detallada aquí: [Límites de recursos de Azure SQL Database](sql-database-service-tiers-dtu.md).

| Código de error | Severity | DESCRIPCIÓN |
| ---:| ---:|:--- |
| 10928 |20 |Id. de recurso: %d. El límite %s para la base de datos es %d y se ha alcanzado. Para más información, consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>El identificador de recurso indica el recurso que ha alcanzado el límite. Para subprocesos de trabajo, el id. de recurso = 1. Para las sesiones, Identificador de recurso = 2.<br/><br/>Para más información sobre este error y cómo solucionarlo, consulte:<br/>• [Límites de recursos de SQL Database](sql-database-service-tiers-dtu.md). |
| 10929 |20 |Id. de recurso: %d. La garantía mínima de %s es de %d, el límite máximo es %d y el uso actual de la base de datos es %d. Sin embargo, el servidor está demasiado ocupado en estos momentos para admitir solicitudes mayores que %d para esta base de datos. Para más información, consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). De lo contrario, inténtelo de nuevo más tarde.<br/><br/>El identificador de recurso indica el recurso que ha alcanzado el límite. Para subprocesos de trabajo, el id. de recurso = 1. Para las sesiones, Identificador de recurso = 2.<br/><br/>Para más información sobre este error y cómo solucionarlo, consulte:<br/>• [Límites de recursos de SQL Database](sql-database-service-tiers-dtu.md). |
| 40544 |20 |La base de datos ha alcanzado su cuota de tamaño. Cree particiones o elimine datos, quite índices o consulte la documentación para obtener soluciones posibles. |
| 40549 |16 |La sesión terminó porque tiene una transacción de larga duración. Intente reducir la transacción. |
| 40550 |16 |La sesión ha terminado porque ha adquirido demasiados bloqueos. Intente leer o modificar menos filas en una sola transacción. |
| 40551 |16 |La sesión ha terminado debido al uso excesivo de `TEMPDB` . Intente modificar la consulta para reducir el uso del espacio de la tabla temporal.<br/><br/>Si está usando objetos temporales, puede ahorrar espacio en la base de datos `TEMPDB` quitándolos una vez que la sesión ya no los necesite. |
| 40552 |16 |La sesión ha terminado debido al excesivo uso de espacio del registro de transacciones. Intente modificar menos filas en una sola transacción.<br/><br/>Si realiza inserciones masivas con la utilidad `bcp.exe` o la clase `System.Data.SqlClient.SqlBulkCopy`, intente usar las opciones `-b batchsize` o `BatchSize` para limitar el número de filas copiadas al servidor en cada transacción. Si está volviendo a crear un índice con la instrucción `ALTER INDEX`, intente usar la opción `REBUILD WITH ONLINE = ON`. |
| 40553 |16 |La sesión ha terminado debido al uso excesivo de la memoria. Intente modificar su consulta para procesar menos filas.<br/><br/>La reducción del número de operaciones `ORDER BY` y `GROUP BY` en el código Transact-SQL disminuye los requisitos de memoria de la consulta. |

## <a name="elastic-pool-errors"></a>Errores de grupos elásticos
Los errores siguientes están relacionados con la creación y el uso de grupos elásticos:

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
|:--- |:--- |:--- |:--- |:--- |:--- |
| 1132 |EX_RESOURCE |El grupo elástico ha alcanzado su límite de almacenamiento. El uso del almacenamiento del grupo elástico no puede superar (%d) MB. |Límite de espacio del grupo elástico en MB. |Se ha intentado escribir datos en una base de datos cuando se ha alcanzado el límite de almacenamiento del grupo elástico. |Considere la posibilidad de incrementar el número de DTU y de agregar almacenamiento al grupo elástico si es posible para aumentar su límite de almacenamiento, reducir el almacenamiento usado por las bases de datos individuales del grupo elástico o quitar bases de datos de este. |
| 10929 |EX_USER |La garantía mínima de %s es de %d, el límite máximo es %d y el uso actual de la base de datos es %d. Sin embargo, el servidor está demasiado ocupado en estos momentos para admitir solicitudes mayores que %d para esta base de datos. Consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) para obtener ayuda. De lo contrario, Inténtelo de nuevo más tarde. |Número mínimo de DTU o de núcleos virtuales por base de datos; número máximo de DTU o de núcleos virtuales por base de datos |El número total de trabajadores simultáneos (solicitudes) de todas las bases de datos del grupo elástico intentó superar el límite del grupo. |Considere la posibilidad de incrementar el número de DTU o de núcleos virtuales del grupo elástico si es posible para aumentar el límite de trabajadores, o bien quite bases de datos del grupo elástico. |
| 40844 |EX_USER |La base de datos '%ls' del servidor '%ls' es una base de datos de la versión '%ls' de un grupo elástico y no puede tener una relación de copia continua. |Nombre de la base de datos, versión de base de datos, nombre del servidor. |Se emitió un comando StartDatabaseCopy para una base de datos que no es Premium de un grupo elástico. |Próximamente |
| 40857 |EX_USER |Grupo elástico no encontrado para el servidor: '%ls', nombre del grupo elástico: '%ls'. |Nombre del servidor; nombre del grupo elástico. |El grupo elástico especificado no existe en el servidor especificado. |Especifique un nombre de grupo elástico válido. |
| 40858 |EX_USER |El grupo elástico '%ls' ya existe en el servidor: '%ls' |Nombre de grupo elástico, nombre del servidor. |El grupo elástico especificado ya existe en el servidor lógico especificado. |Proporcione un nuevo nombre de grupo elástico. |
| 40859 |EX_USER |El grupo elástico no admite el nivel de servicio '%ls'. |Nivel de servicio de grupo elástico. |El nivel de servicio especificado no se admite para el aprovisionamiento de grupo elástico. |Especifique la versión correcta o deje el nivel de servicio en blanco para usar el nivel de servicio predeterminado. |
| 40860 |EX_USER |La combinación del grupo elástico '%ls' y del objetivo de servicio '%ls' no es válida. |Nombre del grupo flexible; nombre del objetivo de nivel de servicio. |El grupo elástico y el objetivo de servicio pueden especificarse juntos solo si se especifica el objetivo de servicio como "ElasticPool". |Especifique la combinación correcta de grupo elástico y objetivo de servicio. |
| 40861 |EX_USER |La versión de la base de datos '%.*ls' no puede ser distinta del nivel de servicio del grupo elástico, que es '%.* ls'. |Versión de la base de datos, nivel de servicio del grupo elástico. |La versión de la base de datos es distinta del nivel de servicio del grupo elástico. |No especifique una versión de la base de datos distinta del nivel de servicio del grupo elástico.  Tenga en cuenta que no es necesario especificar la versión de la base de datos. |
| 40862 |EX_USER |Debe especificarse el nombre del grupo elástico si se especifica el objetivo de servicio del grupo elástico. |None |El objetivo del servicio de grupo elástico no identifica de manera única un grupo elástico. |Especifique el nombre del grupo elástico si usa el objetivo del servicio de grupo elástico. |
| 40864 |EX_USER |El número de DTU del grupo elástico debe ser de al menos (%d) DTU para el nivel de servicio ' %.* ls'. |Número de DTU del grupo elástico; nivel de servicio del grupo elástico. |Se intentó establecer el número de DTU para el grupo elástico por debajo del límite mínimo. |Vuelva a intentar establecer el número de DTU para el grupo elástico al menos en el límite mínimo. |
| 40865 |EX_USER |El número de DTU del grupo elástico no puede superar (%d) DTU para el nivel de servicio ' %.* ls'. |Número de DTU del grupo elástico; nivel de servicio del grupo elástico. |Se intentó establecer el número de DTU para el grupo elástico por encima del máximo. |Vuelva a intentar establecer el número de DTU para el grupo elástico en un valor inferior al límite máximo. |
| 40867 |EX_USER |El número máximo de DTU por base de datos debe ser al menos (%d) para el nivel de servicio '%.*ls'. |Número máximo de DTU por base de datos, nivel de servicio del grupo elástico. |Se intentó establecer el número máximo de DTU por base de datos por debajo del límite admitido. | Considere la posibilidad de utilizar el nivel de servicio del grupo elástico que admita la configuración deseada. |
| 40868 |EX_USER |El número máximo de DTU por base de datos no puede superar (%d) para el nivel de servicio '%.*ls'. |Número máximo de DTU por base de datos, nivel de servicio del grupo elástico. |Se intentó establecer el número máximo de DTU por base de datos por encima del límite admitido. | Considere la posibilidad de utilizar el nivel de servicio del grupo elástico que admita la configuración deseada. |
| 40870 |EX_USER |El número mínimo de DTU por base de datos no puede superar (%d) para el nivel de servicio '%.*ls'. |Número mínimo de DTU por base de datos, nivel de servicio del grupo elástico. |Se intentó establecer el número mínimo de DTU por base de datos por encima del límite admitido. | Considere la posibilidad de utilizar el nivel de servicio del grupo elástico que admita la configuración deseada. |
| 40873 |EX_USER |El número de bases de datos (%d) y el mínimo de DTU por base de datos (%d) no puede superar el número de DTU del grupo elástico (%d). |Número de bases de datos del grupo elástico; número mínimo de DTU por base de datos; número de DTU del grupo elástico. |Se intentó especificar un número mínimo de DTU para las bases de datos del grupo elástico que supera el número de DTU del grupo elástico. | Considere la posibilidad de aumentar el número de DTU del grupo elástico, reduzca el número mínimo de DTU por base de datos, o bien reduzca el número de bases de datos del grupo elástico. |
| 40877 |EX_USER |No se puede eliminar un grupo elástico a menos que no contenga ninguna base de datos. |None |El grupo elástico contiene una o más bases de datos y no se puede eliminar. |Quite las bases de datos del grupo elástico para poder eliminarlo. |
| 40881 |EX_USER |El grupo elástico ' %.* ls' ha alcanzado el límite de recuento de bases de datos.  El límite de bases de datos del grupo elástico no puede superar (%d) para un grupo elástico con (%d) DTU. |Nombre del grupo elástico; límite de bases de datos del grupo elástico; número de eDTU del grupo de recursos. |Se intentó crear una base de datos en el grupo elástico, o agregar una base de datos al grupo elástico, cuando se ha alcanzado el límite de bases de datos del grupo elástico. | Considere la posibilidad de incrementar el número de DTU del grupo elástico si es posible para aumentar el límite de bases de datos, o bien quite bases de datos del grupo elástico. |
| 40889 |EX_USER |El límite de DTU o de almacenamiento del grupo elástico ' %.* ls' no se puede reducir, puesto que no proporcionaría suficiente espacio de almacenamiento para sus bases de datos. |Nombre del grupo elástico. |Se intentó reducir el límite de almacenamiento del grupo elástico por debajo de su uso de almacenamiento. | Considere la posibilidad de reducir el uso de almacenamiento de bases de datos individuales del grupo elástico, o bien quite bases de datos del grupo para reducir su número de DTU o el límite de almacenamiento. |
| 40891 |EX_USER |El número mínimo de DTU por base de datos (%d) no puede superar el número máximo de DTU por base de datos (%d). |Número mínimo de DTU por base de datos; número máximo de DTU por base de datos. |Se intentó establecer el número mínimo de DTU por base de datos con un valor superior al número máximo de DTU por base de datos. |Asegúrese de que el número mínimo de DTU por base de datos no supere el número máximo de DTU por base de datos. |
| TBD |EX_USER |El tamaño de almacenamiento para una base de datos individual de un grupo elástico no puede superar el tamaño máximo permitido por el grupo elástico del nivel de servicio '%.*ls'. |Nivel de servicio de grupo elástico. |El tamaño máximo de la base de datos supera el tamaño máximo permitido por el nivel de servicio del grupo elástico. |Establezca el tamaño máximo de la base de datos por debajo de los límites de tamaño máximo permitidos por el nivel de servicio del grupo elástico. |

Temas relacionados:

* [Creación de un grupo elástico (C#)](sql-database-elastic-pool-manage-csharp.md) 
* [Administración de un grupo de bases de datos elásticas (C#)](sql-database-elastic-pool-manage-csharp.md) 
* [Creación de un grupo de bases de datos elásticas (PowerShell)](sql-database-elastic-pool-manage-powershell.md) 
* [Supervisión y administración de un grupo elástico (PowerShell)](sql-database-elastic-pool-manage-powershell.md)

## <a name="general-errors"></a>Errores generales
Los siguientes errores no se incluyen en ninguna categoría anterior.

| Código de error | Severity | DESCRIPCIÓN |
| ---:| ---:|:--- |
| 15006 |16 |(AdministratorLogin) no es un nombre válido porque contiene caracteres no válidos. |
| 18452 |14 |Error de inicio de sesión. El inicio de sesión se realiza desde un dominio que no es de confianza y no se puede utilizar con autenticación de Windows.%.&#x2a;ls (Los inicios de sesión de Windows no se admiten en esta versión de SQL Server.). |
| 18456 |14 |Error de inicio de sesión del usuario '%.&#x2a;ls'.%.&#x2a;ls%.&#x2a;ls (Error de inicio de sesión del usuario "%.&#x2a;ls". Error de cambio de contraseña. El cambio de contraseña durante el inicio de sesión no se admite en esta versión de SQL Server.) |
| 18470 |14 |Error de inicio de sesión del usuario '%.&#x2a;ls'. Motivo: la cuenta está deshabilitada.%.&#x2a;ls |
| 40014 |16 |No se pueden utilizar varias bases de datos en la misma transacción. |
| 40054 |16 |No se admiten las tablas sin un índice agrupado en esta versión de SQL Server. Crear un índice agrupado e inténtelo de nuevo. |
| 40133 |15 |No se admite esta operación en esta versión de SQL Server. |
| 40506 |16 |El SID especificado no es válido para esta versión de SQL Server. |
| 40507 |16 |No se puede invocar '%.&#x2a;ls' con parámetros en esta versión de SQL Server. |
| 40508 |16 |No se admite la instrucción de USE para cambiar entre bases de datos. Use una nueva conexión para conectarse a una base de datos diferente. |
| 40510 |16 |No se admite la instrucción '%.&#x2a;ls' en esta versión de SQL Server. |
| 40511 |16 |No se admite la instrucción integrada '%.&#x2a;ls' en esta versión de SQL Server. |
| 40512 |16 |No se admite la característica desusada '%ls' en esta versión de SQL Server. |
| 40513 |16 |No se admite la variable del servidor '%.&#x2a;ls' en esta versión de SQL Server. |
| 40514 |16 |No se admite '%ls' en esta versión de SQL Server. |
| 40515 |16 |No se admite referencia al nombre de la base de datos o del servidor en '%.&#x2a;ls' en esta versión de SQL Server. |
| 40516 |16 |Los objetos temporales globales no se admiten en esta versión de SQL Server. |
| 40517 |16 |No se admite la opción de palabra clave o instrucción '%.&#x2a;ls' en esta versión de SQL Server. |
| 40518 |16 |No se admite el comando DBCC '%.&#x2a;ls' en esta versión de SQL Server. |
| 40520 |16 |La clase protegible '%S_MSG' no se admite en esta versión de SQL Server. |
| 40521 |16 |La clase protegible '%S_MSG' no se admite en el ámbito de servidor en esta versión de SQL Server. |
| 40522 |16 |El tipo de entidad de seguridad de base de datos '%.&#x2a;ls' no se admite en esta versión de SQL Server. |
| 40523 |16 |La creación de usuario implícita '%.&#x2a;ls' no se admite en esta versión de SQL Server. Cree el usuario de forma explícita antes de usarlo. |
| 40524 |16 |El tipo de datos '%.&#x2a;ls' no se admite en esta versión de SQL Server. |
| 40525 |16 |WITH '%ls' no se admite en esta versión de SQL Server. |
| 40526 |16 |El proveedor de conjuntos de filas '%.&#x2a;ls' no se admite en esta versión de SQL Server. |
| 40527 |16 |Los servidores vinculados no se admiten en esta versión de SQL Server. |
| 40528 |16 |Los usuarios no se pueden asignar a certificados, claves asimétricas o inicios de sesión de Windows en esta versión de SQL Server. |
| 40529 |16 |La función integrada '%.&#x2a;ls' en el contexto de suplantación no se admite en esta versión de SQL Server. |
| 40532 |11 |No se puede abrir el servidor "%.&#x2a;ls" solicitado por el inicio de sesión. Error de inicio de sesión. |
| 40553 |16 |La sesión ha terminado debido al uso excesivo de la memoria. Intente modificar su consulta para procesar menos filas.<br/><br/> La reducción del número de operaciones `ORDER BY` y `GROUP BY` en el código Transact-SQL ayuda a disminuir los requisitos de memoria de la consulta. |
| 40604 |16 |No se pudo CREATE/ALTER DATABASE porque superaría la cuota del servidor. |
| 40606 |16 |Adjuntar base de datos no se admite en esta versión de SQL Server. |
| 40607 |16 |Los inicios de sesión de Windows no se admiten en esta versión de SQL Server. |
| 40611 |16 |Los servidores pueden tener como máximo 128 reglas de firewall definidas. |
| 40614 |16 |La dirección IP de inicio de la regla de firewall no puede superar la dirección IP final. |
| 40615 |16 |No se puede abrir el servidor "{0}" solicitado por el inicio de sesión. No está permitido que el cliente con la dirección IP "{1}" acceda al servidor.<br /><br />Para habilitar el acceso, use el Portal de SQL Database o ejecute sp\_set\_firewall\_rule en la base de datos maestra para crear una regla de firewall para esta dirección IP o intervalo de direcciones. Puede tardar hasta cinco minutos que este cambio surta efecto. |
| 40617 |16 |El nombre de la regla de firewall que comienza por (nombre de la regla) es demasiado largo. La longitud máxima es 128. |
| 40618 |16 |El nombre de la regla de firewall no puede estar vacío. |
| 40620 |16 |Error de inicio de sesión del usuario "%.&#x2a;ls". Error de cambio de contraseña. El cambio de contraseña durante el inicio de sesión no se admite en esta versión de SQL Server. |
| 40627 |20 |La operación del servidor "{0}" y la base de datos "{1}" está en curso. Espere algunos minutos antes de volver a intentarlo. |
| 40630 |16 |Error de validación de contraseña. La contraseña no cumple los requisitos de directiva porque es demasiado corta. |
| 40631 |16 |La contraseña especificada es demasiado larga. No debe tener más de 128 caracteres. |
| 40632 |16 |Error de validación de contraseña. La contraseña no cumple los requisitos de directiva porque no es lo suficientemente compleja. |
| 40636 |16 |No se puede usar un nombre de base de datos reservado '%.&#x2a;ls' en esta operación. |
| 40638 |16 |Id. de suscripción (identificador_de_suscripción) no válido. La suscripción no existe. |
| 40639 |16 |La solicitud no se ajusta al esquema: (error de esquema). |
| 40640 |20 |El servidor detectó una excepción inesperada. |
| 40641 |16 |La ubicación especificada no es válida. |
| 40642 |17 |El servidor está demasiado ocupado. Inténtelo de nuevo más tarde. |
| 40643 |16 |El valor del encabezado x-ms-version especificado no es válido. |
| 40644 |14 |No se pudo autorizar el acceso a la suscripción especificada. |
| 40645 |16 |El nombre de servidor (nombreDeServidor) no puede estar vacío ni ser NULL. Solo puede contener letras minúsculas de la 'a' a la 'z', los números del 0 al 9 y guiones. El guión no puede estar al principio ni al final del nombre. |
| 40646 |16 |El id. de suscripción no puede estar vacío. |
| 40647 |16 |La suscripción (identificador_de_suscripción) no tiene servidor (nombreDeServidor). |
| 40648 |17 |Se han realizado demasiadas solicitudes. Inténtelo de nuevo más tarde. |
| 40649 |16 |Se ha especificado un content-type no válido. Solo se admite application/xml. |
| 40650 |16 |La suscripción (identificador_de_suscripción) no existe o no está lista para la operación. |
| 40651 |16 |No se pudo crear el servidor porque la suscripción (identificador_de_suscripción) está deshabilitada. |
| 40652 |16 |No se puede mover ni crear un servidor. La suscripción (identificador_de_suscripción) superará la cuota de servidor. |
| 40671 |17 |Error de comunicación entre la puerta de enlace y el servicio de administración. Inténtelo de nuevo más tarde. |
| 40852 |16 |No se puede abrir la base de datos '%.\*ls' del servidor '%.\*ls' solicitada por el inicio de sesión. Solo se permite el acceso a la base de datos mediante una cadena de conexión habilitada para seguridad. Para obtener acceso a esta base de datos, modifique las cadenas de conexión que contienen 'secure' en el FQDN de servidor - 'nombre de servidor'.database.windows.net debe cambiarse a 'nombre de servidor'.database.`secure`.windows.net. |
| 40914 | 16 | No se puede abrir el servidor "*[nombre-servidor]*" solicitado por el inicio de sesión. No está permitido que el cliente acceda al servidor.<br /><br />Para solucionar el problema, considere la posibilidad de agregar una [regla de red virtual](sql-database-vnet-service-endpoint-rule-overview.md). |
| 45168 |16 |El sistema de SQL Azure está bajo carga y está colocando un límite superior en operaciones DB CRUD simultáneas para un único servidor (por ejemplo, crear base de datos). El servidor especificado en el mensaje de error ha superado el número máximo de conexiones simultáneas. Inténtelo de nuevo más tarde. |
| 45169 |16 |El sistema de SQL Azure está bajo carga y está colocando un límite superior en operaciones CRUD de servidor simultáneas para una única suscripción (por ejemplo, crear servidor). La suscripción especificada en el mensaje de error ha superado el número máximo de conexiones simultáneas y se denegó la solicitud. Inténtelo de nuevo más tarde. |

## <a name="next-steps"></a>Pasos siguientes
* Conozca más detalles sobre las [características de Azure SQL Database](sql-database-features.md).
* Conozca más detalles sobre el [modelo de compra basado en DTU](sql-database-service-tiers-dtu.md).
* Conozca más detalles sobre el [modelo de compra basado en núcleos virtuales (versión preliminar)](sql-database-service-tiers-vcore.md).

