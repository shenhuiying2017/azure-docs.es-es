<properties 
   pageTitle="Límites de recursos de Base de datos SQL"
   description="Esta página describe algunos límites de recursos comunes para Base de datos SQL de Azure."
   services="sql-database"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar" />
<tags 
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="07/13/2015"
   ms.author="jroth" />

# Límites de recursos de Base de datos SQL

Base de datos SQL de Azure supervisa el uso de los recursos compartidos, como el registro de transacciones, E/S y muchos otros recursos. Esto permite a Base de datos SQL de Azure mantener las bases de datos dentro de los límites de los recursos establecidos. Este límite de recursos o umbral se denomina límite de recursos. Cuando el uso de recursos por parte de los clientes supera estos límites, ya sea un inquilino o un nodo físico, Base de datos SQL de Azure responde administrando el uso de los recursos, lo que produce pérdida de conexiones o denegaciones de solicitudes.

> [AZURE.NOTE]Cuando los límites de recursos impiden las consultas para analizar los problemas de rendimiento de la base de datos, quizás tenga que usar la conexión de administrador dedicada (DAC), disponible a partir de Base de datos SQL V12 de Azure. Para obtener más información acerca del uso de la DAC, consulte [Diagnóstico de conexión para administradores de base de datos](https://msdn.microsoft.com/library/ms189595.aspx).

## Tabla de resumen de los límites de recursos

La tabla siguiente proporciona un resumen de los límites para cada recurso más allá del cual Base de datos SQL de Azure deniega solicitudes o termina las conexiones a los recursos afectados, y se devuelve un código de error. En algunos casos, el nivel de servicio (Basic, Standard, Premium) y el nivel de rendimiento determinan el límite exacto. En esos casos, consulte [Niveles de servicio y niveles de rendimiento de Base de datos SQL](https://msdn.microsoft.com/library/azure/dn741336.aspx).

[AZURE.INCLUDE [azure-sql-database-limits](../../includes/azure-sql-database-limits.md)]

## Descripción de los códigos de error

A veces se devuelve el mismo código de error para varias condiciones de limitación de un recurso. Estas condiciones se identifican como estados en el mensaje de error. Por ejemplo, se muestran los dos siguientes mensajes de error para el recurso de longitud del registro de transacciones. Aunque el texto del mensaje es el mismo, tienen diferentes valores de estado (1 y 2 respectivamente) según las distintas condiciones de limitación.

Mensaje de error 1:

	Msg 40552, Level 17, State 1, Line 1
	The session has been terminated because of excessive transaction log space usage.
	Try modifying fewer rows in a single transaction.

Mensaje de error 2:

	Msg 40552, Level 17, State 2, Line 1
	The session has been terminated because of excessive transaction log space usage.
	Try modifying fewer rows in a single transaction.

La misma situación se aplica al identificador de recurso que aparece en algunos mensajes. El identificador de recurso se traduce en un recurso del sistema que está experimentando la limitación.

El resto de este tema explica los posibles códigos de error con más detalle, incluidos aquellos casos en los que el valor de estado y el identificador de recurso proporcionan información adicional sobre el error.

## Tamaño de base de datos

| &nbsp; | Más información |
| :--- | :--- |
| **Condición** | Cuando se llena el espacio de base de datos asignado a una base de datos de usuario, el usuario obtiene un error de base de datos llena. |
| **Código de error** | **40544**: La base de datos ha alcanzado su cuota de tamaño. Cree particiones o elimine datos, quite índices o consulte la documentación para obtener soluciones posibles. |
| **Límite** | Depende del [Nivel de servicio y de rendimiento](https://msdn.microsoft.com/library/azure/dn741336.aspx). |
| **Tipo de solicitudes denegadas** | DML que no sea de selección (inserción, actualización, combinación de inserción o actualización). |
| **Recomendación** | Use las instrucciones DELETE/DROP para quitar datos de la base de datos hasta que el tamaño de la base de datos esté por debajo del límite. |

## Inicios de sesión

| &nbsp; | Más información |
| :--- | :--- |
| **Condición** | Base de datos SQL regula el límite en el número de conexiones simultáneas que pueden establecerse con una base de datos. Cuando se alcanza el límite de inicios de sesión simultáneos para una base de datos, se deniegan las nuevas solicitudes de inicio de sesión en la base de datos y se devuelve el código de error 10928. |
| **Código de error** | **10928**: Id. de recurso: 3. El límite %s para la base de datos es %d y se ha alcanzado. Consulte http://go.microsoft.com/fwlink/?LinkId=267637 para obtener ayuda. |
| **Límite** | Depende del [Nivel de servicio y de rendimiento](https://msdn.microsoft.com/library/azure/dn741336.aspx). |
| **Recomendación** | Consulte dm_exec_connections para ver qué conexiones de usuario están activas en ese momento. <br><br>Retroceda y vuelva a intentar iniciar sesión después de 10 segundos. |

> [AZURE.NOTE]El valor de identificador de recurso en el mensaje de error indica el recurso para el que se ha alcanzado el límite. Para los inicios de sesión, id. de recurso = 3.

## Uso de la memoria

| &nbsp; | Más información |
| :--- | :--- |
| **Condición** | Cuando hay sesiones en espera de concesiones de memoria durante 20 segundos o más, las sesiones que usan más de 16 MB de concesión de memoria durante más de 20 segundos se terminan en orden descendente del tiempo que el recurso se ha retenido, para que finalice la sesión más antigua primero. La terminación de sesiones se detiene tan pronto como la memoria requerida está disponible. |
| **Código de error** | **40553**: La sesión ha terminado debido al uso excesivo de la memoria. Intente modificar la consulta para procesar menos filas. |
| **Límite** | Más de 16 MB de concesión de memoria durante más de 20 segundos. |
| **Tipo de solicitudes denegadas** | Consultas que usan concesiones de memoria, que incluyen consultas que usan ordenaciones y combinaciones hash. |
| **Recomendación** | Realice un ajuste en las consultas que requieran ordenaciones y combinaciones hash. |

## Sesiones

| &nbsp; | Más información |
| :--- | :--- |
| **Condición** | Base de datos SQL regula el límite en el número de sesiones simultáneas que pueden establecerse en una base de datos. Cuando se alcanza el límite de sesiones simultáneas para una base de datos, se deniegan las nuevas conexiones a la base de datos y el usuario recibirá el código de error 10928. Sin embargo, no se finalizan las sesiones existentes en la base de datos. |
| **Código de error** | **10928**: Id. de recurso: 2. El límite %s para la base de datos es %d y se ha alcanzado. Consulte http://go.microsoft.com/fwlink/?LinkId=267637 para obtener ayuda. |
| **Límite** | Depende del [Nivel de servicio y de rendimiento](https://msdn.microsoft.com/library/azure/dn741336.aspx). |
| **Recomendación** | Compruebe dm_exec_requests para ver qué solicitudes de usuario se están ejecutando actualmente. |

> [AZURE.NOTE]El valor de identificador de recurso en el mensaje de error indica el recurso para el que se ha alcanzado el límite. Para las sesiones, id. de recurso = 2.

## Tempdb

| &nbsp; | Más información |
| :--- | :--- |
| **Condición** | Sus solicitudes en tempdb pueden denegarse debido a alguna de las tres condiciones siguientes:<br><br>**Estado 1:** cuando una sesión usa más de 5 GB de espacio de tempdb, la sesión se termina.<br><br>**Estado 2:** las transacciones en tempdb con registros de más de 2 GB de tamaño se truncan. Ejemplos de operaciones que pueden consumir el espacio de registro en tempdb: insertar, actualizar, eliminar, combinar y crear índice.<br><br>** Estado 3:** las transacciones no confirmadas en tempdb pueden bloquear el truncamiento de archivos de registro. Para evitar esto, la distancia desde el número de secuencia de registro (LSN) de transacción activa más antiguo hasta el final del registro (LSN actual) en tempdb no puede exceder el 20% del tamaño del archivo de registro. Cuando infringe esta condición, la transacción responsable se termina en tempdb y se revierte para poder truncar el registro. |
| **Código de error** | **40551**: La sesión ha terminado debido al uso excesivo de tempdb. Intente modificar la consulta para reducir el uso del espacio de la tabla temporal. |
| **Límite** | **Estado 1:** 5 GB de espacio de tempdb<br><br>**Estado 2:** 2 GB por transacción en tempdb<br><br>**Estado 3:** 20% del espacio total de registro en tempdb |
| **Tipo de solicitudes denegadas** | Cualquier instrucción DDL o DML en tempdb. |
| **Recomendación** | Modifique las consultas para reducir el uso del espacio de la tabla temporal, quite objetos temporales cuando ya no se necesiten, trunque tablas o quite tablas que no se usan. Reduzca el tamaño de los datos de la transacción en tempdb reduciendo el número de filas o dividiendo la operación en varias transacciones. |

## Duración de la transacción

| &nbsp; | Más información |
| :--- | :--- |
| **Condición** | Las transacciones solicitan bloqueos de recursos como filas, páginas o tablas, de los que depende la transacción y, cuando ya no tienen una dependencia de los recursos bloqueados, liberan los bloqueos. Las solicitudes pueden denegarse debido a alguna de las siguientes dos condiciones: Estado 1: si una transacción se ha estado ejecutando durante más de 24 horas, se termina. Estado 2: si una transacción bloquea durante más de 20 segundos un recurso que una tarea del sistema subyacente necesita, se termina. |
| **Código de error** | **40549**: La sesión terminó porque tiene una transacción de larga duración. Intente reducir la transacción. |
| **Límite** | **Estado 1:** 24 horas<br><br>**Estado 2:** 20 segundos si una transacción bloquea un recurso que una tarea del sistema subyacente necesita |
| **Tipo de solicitudes denegadas** | Cualquier transacción que se ejecute durante más de 24 horas o instrucciones DDL o DML que realicen un bloqueo que provoque un bloqueo de una tarea del sistema. |
| **Recomendación** | Las operaciones en Base de datos SQL no deben bloquear por la entrada del usuario ni tener otras dependencias que produzcan transacciones de larga ejecución. |

## Recuento de bloqueos de transacciones

| &nbsp; | Más información |
| :--- | :--- |
| **Condición** | Las sesiones que usan más de un millón de bloqueos se terminan. |
| **Código de error** | **40550**: La sesión ha terminado porque ha adquirido demasiados bloqueos. Intente leer o modificar menos filas en una sola transacción. |
| **Límite** | 1 millón de bloqueos por transacción |
| **Tipo de solicitudes denegadas** | Cualquier instrucción DDL o DML. |
| **Recomendación** | Las siguientes DMV se pueden usar para supervisar transacciones: **sys.dm_tran_active_transactions**, **sys.dm_tran_database_transactions**, **sys.dm_tran_locks** y **sys.dm_tran_session_transactions**. Según el tipo de aplicación, se puede usar sugerencias más generales para los bloqueos, como **PAGLOCK** o **TABLOCK**, para reducir el número de bloqueos realizados en una determinada instrucción o transacción. Tenga en cuenta que esto puede afectar negativamente a la simultaneidad de aplicaciones. |

## Longitud del registro de transacciones

| &nbsp; | Más información |
| :--- | :--- |
| **Condición** | Las solicitudes podrían denegarse debido a alguna de las dos condiciones siguientes:<br><br>**Estado 1:** Base de datos SQL admite transacciones que generan un registro de hasta 2 GB de tamaño. Las transacciones con registros que superan este límite se truncan. Ejemplos de operaciones que pueden consumir el espacio de registro en este volumen: insertar, actualizar, eliminar, combinar y crear índice.<br><br>** Estado 2:** las transacciones no confirmadas pueden bloquear el truncamiento de archivos de registro. Para evitar esto, la distancia desde el número de secuencia de registro (LSN) de transacción activa más antiguo hasta el final del registro (LSN actual) no puede exceder el 20% del tamaño del archivo de registro. Cuando infringe esta condición, la transacción responsable se termina y se revierte para poder truncar el registro. |
| **Código de error** | **40552**: La sesión ha terminado debido al excesivo uso de espacio del registro de transacciones. Intente modificar menos filas en una sola transacción. |
| **Límite** | **Estado 1:** 2 GB por transacción<br><br>**Estado 2:** 20% del espacio total de registro |
| **Tipo de solicitudes denegadas** | Cualquier instrucción DDL o DML. |
| **Recomendación** | Para las operaciones de fila, reduzca el tamaño de los datos de la transacción, por ejemplo reduciendo el número de filas o dividiendo la operación en varias transacciones. Para las operaciones de índice o tabla que requieren una sola transacción, asegúrese de que se cumple la siguiente fórmula: número de filas afectadas en la tabla * (tamaño promedio del campo que se va a actualizar en bytes + 80) < 2 GB (en el caso de la reconstrucción de índices, el tamaño promedio del campo que se está actualizando debe sustituirse por el tamaño promedio del índice). |

## Subprocesos de trabajo (número máximo de solicitudes simultáneas)

| &nbsp; | Más información |
| :--- | :--- |
| **Condición** | Base de datos SQL regula el límite en el número de subprocesos de trabajo (solicitudes simultáneas) a una base de datos. Cualquier base de datos más del límite permitido de solicitudes simultáneas recibirá el error 10928 y las solicitudes posteriores a esta base de datos se podrán denegar. |
| **Códigos de error** | **10928**: Id. de recurso: 1. El límite %s para la base de datos es %d y se ha alcanzado. Consulte http://go.microsoft.com/fwlink/?LinkId=267637 para obtener ayuda.<br><br>** 10929**: Id. de recurso: 1. La garantía mínima de %s es de %d, el límite máximo es %d y el uso actual de la base de datos es %d. Sin embargo, el servidor está demasiado ocupado en estos momentos para admitir solicitudes mayores que %d para esta base de datos. Consulte http://go.microsoft.com/fwlink/?LinkId=267637 para obtener ayuda. De lo contrario, Inténtelo de nuevo más tarde. |
| **Límite** | Para los niveles Basic, Standard y Premium, depende del [nivel de rendimiento](https://msdn.microsoft.com/library/azure/dn741336.aspx). Para las bases de datos de ediciones Web o Business anteriores, el límite máximo de solicitudes simultáneas es de 180 y podría ser inferior en función de la actividad del sistema. |
| **Recomendación** | Compruebe dm_exec_requests para ver qué solicitudes de usuario se están ejecutando actualmente.<br><br>Retroceda y vuelva a intentar la solicitud después de 10 segundos. |

> [AZURE.NOTE]El valor de identificador de recurso en el mensaje de error indica el recurso para el que se ha alcanzado el límite. Para los subprocesos de trabajo, id. de recurso = 1.

Errores debidos a que la regulación de subprocesos de trabajo (10928/10929) reemplaza el error original de limitación del motor (40501) para los subprocesos de trabajo. En condiciones normales, los usuarios ya no deberían recibir el error de limitación del motor para los subprocesos de trabajo.

En ciertos escenarios, como el uso de características de bases de datos federadas, puede que aparezca el error de límite de subprocesos de trabajo (10928) en el momento de iniciar sesión en una base de datos porque esta operación usaría un subproceso de trabajo bajo la llamada a Connection.Open. Esto podría hacer que la aplicación supere el umbral de límite de subprocesos de trabajo. Las aplicaciones deben tener integrada la lógica que controle este error de forma adecuada para tratar estos casos.

## Otras referencias

[Administrador de recursos de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/dn338083.aspx)

[Mensajes de error (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/azure/ff394106.aspx)

[Prácticas recomendadas de Base de datos SQL de Azure para impedir denegaciones de solicitudes o la terminación de conexiones](https://msdn.microsoft.com/library/azure/dn338082.aspx)

<!---HONumber=July15_HO3-->