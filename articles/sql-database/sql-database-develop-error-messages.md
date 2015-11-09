<properties 
	pageTitle="Mensajes de error para los programas de cliente de base de datos SQL"
	description="Para cada error, esto proporciona el id. numérico y el mensaje de texto. No dude en hacer una referencia cruzada a su propio texto de mensaje de error más descriptivo que prefiera si lo cree adecuado."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/26/2015" 
	ms.author="genemi"/>


# Mensajes de error para los programas de cliente de base de datos SQL


<!--
Old Title on MSDN:  Error Messages (Azure SQL Database)
ShortId on MSDN:  ff394106.aspx
Dx 4cff491e-9359-4454-bd7c-fb72c4c452ca
-->


En este tema se enumeran varias categorías de mensajes de error. La mayoría de las categorías son específicas de la Base de datos SQL de Azure y no se aplican a Microsoft SQL Server.


En su programa de cliente tiene la opción de ofrecer su usuario con un mensaje alternativo personalizado por usted, para cualquier error determinado.


**Sugerencia:** de especial importancia es la sección de *errores transitorios*. Estos errores deben solicitar al programa cliente que ejecute la lógica de *reintento* que diseña para volver a intentar la operación.


<a id="bkmk_connection_errors" name="bkmk_connection_errors">&nbsp;</a>


## Errores transitorios, pérdida de conexión y otros errores temporales

En la siguiente tabla se tratan los errores de pérdida de conexión y otros errores transitorios, que puede encontrar mientras se trabaja a través de Internet con la Base de datos SQL de Azure.

Los errores transitorios también se denominan a veces anomalías transitorias. Cuando el programa detecta un `SqlException`, su programa puede comprobar si el valor `sqlException.Number` es un valor que se muestra en esta sección de errores transitorios. Si el valor `Number` indica un error transitorio, su programa puede volver a intentar establecer una conexión y, a continuación, volver a intentar realizar consultas a través de la conexión. Para obtener ejemplos de lógica de reintento, vea:


- [Ejemplos de código de desarrollo de cliente y de inicio rápido para la base de datos SQL](sql-database-develop-quick-start-client-code-samples.md)

- [Conexión confiable a Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/dn864744.aspx)


| Número de error | Gravedad | Descripción |
| ---: | ---: | :--- |
| 4060 | 16 | No se puede abrir la base de datos "%.&#x2a;ls" solicitada por el inicio de sesión. Error de inicio de sesión. |
|40197|17|Error en el servicio al procesar la solicitud. Vuelva a intentarlo. Código de error %d.<br/><br/>Recibirá este error cuando el servicio esté inactivo debido a actualizaciones de software o hardware, errores de hardware u otros problemas de conmutación por error. El código de error (%d) incrustado en el mensaje de error 40197 proporciona información adicional sobre el tipo de error o conmutación por error que se ha producido. Algunos ejemplos de los códigos de error que se incrustan dentro del mensaje de error 40197 son 40020, 40143, 40166 y 40540.<br/><br/>Al volver a conectarse al servidor de Base de datos SQL se conectará automáticamente a una copia correcta de su base de datos. La aplicación debe detectar el error 40197, registrar el código de error incrustado (%d) dentro del mensaje para solucionar problemas y volver a conectarse a la base de datos SQL hasta que los recursos estén disponibles; entonces, la conexión se establecerá de nuevo.|
|40501|20|El servicio está ocupado actualmente. Vuelva a intentar la solicitud después de 10 segundos. Identificador de incidente: %ls. Código: %d.<br/><br/>*Nota:* para obtener más información acerca de este error y de cómo resolverlo, vea:<br/>• [Limitación de base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/dn338079.aspx).
|40613|17|La base de datos '%.&#x2a;ls' en el servidor '%.&#x2a;ls' no está disponible actualmente. Vuelva a intentar la conexión más tarde. Si el problema continúa, póngase en contacto con el servicio de soporte al cliente y proporcióneles el id. de seguimiento de sesión de '%. & #x2a; ls'.|
|49918|16|No se puede procesar la solicitud. No hay suficientes recursos para procesar la solicitud.<br/><br/>El servicio está ocupado actualmente. Vuelva a intentar realizar la solicitud más tarde. |
|49919|16|No se procesar, crear ni actualizar la solicitud. Hay demasiadas operaciones de creación o actualización en curso para la suscripción "%ld".<br/><br/>El servicio está ocupado procesando varias solicitudes de creación o actualización para su suscripción o servidor. Actualmente las solicitudes están bloqueadas para la optimización de recursos. Consulta [sys.dm\_operation\_status](https://msdn.microsoft.com/library/dn270022.aspx) para las operaciones pendientes. Espere a que se completen solicitudes de creación o actualización pendientes o elimine una de las solicitudes pendientes y vuelva a intentar la solicitud más tarde. |
|49920|16|No se puede procesar la solicitud. Hay demasiadas operaciones en curso para la suscripción "%ld".<br/><br/>El servicio está ocupado procesando varias solicitudes para esta suscripción. Actualmente las solicitudes están bloqueadas para la optimización de recursos. Consulta [sys.dm\_operation\_status](https://msdn.microsoft.com/library/dn270022.aspx) para el estado de la operación. Espere a que las solicitudes pendientes se hayan completado o elimine una de las solicitudes pendientes y vuelva a intentar la solicitud más tarde. |

**Nota:** es posible que los errores de federación 10053 y 10054 requieran también la inclusión en su lógica de reintento.


<a id="bkmk_b_database_copy_errors" name="bkmk_b_database_copy_errors">&nbsp;</a>

## Errores de copia de base de datos


En la tabla siguiente se describen los diversos errores que puede encontrarse al copiar una base de datos en la Base de datos SQL de Azure. Para obtener más información, consulte [Copiar bases de datos en Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/ff951624.aspx).


|Número de error|Gravedad|Descripción|
|---:|---:|:---|
|40635|16|El cliente con la dirección IP '%.&#x2a;ls' está deshabilitado temporalmente.|
|40637|16|Crear copia de base de datos está deshabilitado actualmente.|
|40561|16|Error al copiar la base de datos. No existe la base de datos de origen o de destino.|
|40562|16|Error al copiar la base de datos. La base de datos de origen se ha quitado.|
|40563|16|Error al copiar la base de datos. La base de datos de destino se ha quitado.|
|40564|16|No se pudo copiar la base de datos debido a un error interno. Quite la base de datos de destino e inténtelo de nuevo.|
|40565|16|Error al copiar la base de datos. No se permite más de una copia de base de datos simultánea para el mismo origen. Quite la base de datos de destino y vuelva a intentarlo más adelante.|
|40566|16|No se pudo copiar la base de datos debido a un error interno. Quite la base de datos de destino e inténtelo de nuevo.|
|40567|16|No se pudo copiar la base de datos debido a un error interno. Quite la base de datos de destino e inténtelo de nuevo.|
|40568|16|Error al copiar la base de datos. La base de datos de origen ha dejado de estar disponible. Quite la base de datos de destino e inténtelo de nuevo.|
|40569|16|Error al copiar la base de datos. La base de datos de destino ha dejado de estar disponible. Quite la base de datos de destino e inténtelo de nuevo.|
|40570|16|No se pudo copiar la base de datos debido a un error interno. Quite la base de datos de destino y vuelva a intentarlo más adelante.|
|40571|16|No se pudo copiar la base de datos debido a un error interno. Quite la base de datos de destino y vuelva a intentarlo más adelante.|


<a id="bkmk_c_resource_gov_errors" name="bkmk_c_resource_gov_errors">&nbsp;</a>

## Errores de regulación de recursos


En la tabla siguiente se muestran los errores causados por un uso excesivo de recursos mientras se trabaja con la base de datos SQL de Azure. Por ejemplo:


- Es posible que su transacción haya estado abierta demasiado tiempo.
- Puede que su transacción contenga demasiados bloqueos.
- Es posible que el programa está consumiendo demasiada memoria.
- Puede que el programa está consumiendo demasiado espacio `TempDb`.


**Sugerencia:** el vínculo siguiente ofrece más información que se aplica a la mayoría de los errores de esta sección o a todos ellos:


- [Límites de recursos de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/dn338081.aspx).


|Número de error|Gravedad|Descripción|
|---:|---:|:---|
|10928|20|Id. de recurso: %d. El límite %s para la base de datos es %d y se ha alcanzado. Para obtener más información, visite [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>El identificador de recurso indica el recurso que alcanzó el límite. Para subprocesos de trabajo, el id. de recurso = 1. Para las sesiones, el identificador de recurso = 2.<br/><br/>*Nota:* para obtener más información sobre este error y sobre cómo resolverlo, consulte:<br/>• [Gobernanza de recursos de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/dn338078.aspx). |
|10929|20|Id. de recurso: %d. La garantía mínima de %s es de %d, el límite máximo es %d y el uso actual de la base de datos es %d. Sin embargo, el servidor está demasiado ocupado en estos momentos para admitir solicitudes mayores que %d para esta base de datos. Para obtener más información, visite [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). De lo contrario, vuelva a intentarlo más tarde.<br/><br/>El identificador de recurso indica el recurso que alcanzó el límite. Para subprocesos de trabajo, el id. de recurso = 1. Para las sesiones, el identificador de recurso = 2.<br/><br/>*Nota:* para obtener más información sobre este error y sobre cómo resolverlo, consulte:<br/>• [Gobernanza de recursos de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/dn338078.aspx).|
|40544|20|La base de datos ha alcanzado su cuota de tamaño. Cree particiones o elimine datos, quite índices o consulte la documentación para obtener soluciones posibles.|
|40549|16|La sesión terminó porque tiene una transacción de larga duración. Intente reducir la transacción.|
|40550|16|La sesión ha terminado porque ha adquirido demasiados bloqueos. Intente leer o modificar menos filas en una sola transacción.|
|40551|16|La sesión ha terminado debido al uso excesivo de `TEMPDB`. Intente modificar su consulta para reducir el uso del espacio de tabla temporal.<br/><br/>*Sugerencia: * si usa objetos temporales, puede ahorrar espacio en la base de datos `TEMPDB` quitando objetos temporales cuando la sesión ya no los necesite.|
|40552|16|La sesión ha terminado debido al excesivo uso de espacio del registro de transacciones. Intente modificar menos filas en una sola transacción.<br/><br/>*Sugerencia:* si realiza inserciones masivas con la utilidad `bcp.exe` o la clase `System.Data.SqlClient.SqlBulkCopy`, intente usar las opciones `-b batchsize`o `BatchSize` para limitar el número de filas copiadas al servidor en cada transacción. Si vuelve a crear un índice con la instrucción `ALTER INDEX`, intente usar la opción `REBUILD WITH ONLINE = ON`.|
|40553|16|La sesión ha terminado debido al uso excesivo de la memoria. Intente modificar su consulta para procesar menos filas.<br/><br/>*Sugerencia:* la reducción del número de operaciones `ORDER BY` y `GROUP BY` en el código de Transact-SQL reduce los requisitos de memoria de la consulta.|


Para obtener más información sobre la regulación de recursos y los errores asociados, vea:


- [Gobernanza de recursos de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/dn338078.aspx).


<a id="bkmk_d_federation_errors" name="bkmk_d_federation_errors">&nbsp;</a>

## Errores de federación


En la tabla siguiente se muestran los errores que puede encontrar al trabajar con federaciones. Para obtener más información, consulte [Administración de federaciones de bases de datos (Base de datos SQL de Azure)](http://msdn.microsoft.com/library/azure/hh597455.aspx).


> [AZURE.IMPORTANT]La implementación actual de las federaciones finalizará con los niveles de servicio con Web y Business. La versión V12 de Base de datos SQL de Azure no admite los niveles de servicio Web y Business.
> 
> La característica de escalado elástico está diseñada para crear aplicaciones de particionamiento con el mínimo esfuerzo.
> 
> Para obtener más información acerca del escalado elástico, consulte [Temas de escalado elástico de Base de datos SQL de Azure](sql-database-elastic-scale-documentation-map.md). Considere la posibilidad de implementar soluciones de particionamiento personalizadas para maximizar la escalabilidad, la flexibilidad y el rendimiento. Para obtener más información sobre el particionamiento personalizado, consulte [Escalado horizontal de Bases de datos SQL de Azure](http://msdn.microsoft.com/library/azure/dn495641.aspx).


|Número de error|Gravedad|Descripción|Mitigación|
|---:|---:|:---|:---|
|266|16|No se permite la instrucción <statement> en las transacciones con múltiples instrucciones.|Compruebe que `@@trancount` es 0 en la conexión antes de emitir la instrucción.|
|2072|16|La base de datos '%.&#x2a;ls' no existe|Compruebe `sys.databases` para el estado de la base de datos antes de emitir `USE FEDERATION`.|
|2209|16|%s Error de sintaxis cerca de '%ls'|`FEDERATED ON` solo se puede usar cuando se crean tablas en miembros de federación.|
|2714|16|Ya hay un objeto con el nombre ‘%.&#x2a;ls’ en la base de datos|Ya existe el nombre de federación.|
|10054, 10053|20|Error en el nivel del transporte al recibir los resultados del servidor. Se ha anulado una conexión establecida por el software en su equipo host.|Implemente la lógica de reintento en la aplicación.|
|40530|15|<statement> debe ser la única instrucción del lote|Asegúrese de que no hay ninguna instrucción en el lote|
|40604|16|No se pudo `CREATE DATABASE` porque se superaría la cuota del servidor|Expanda la cuota de número de bases de datos de servidor|
|45000|16|Error en la operación <statement>. El nombre de federación especificado <federation_name> no es válido|El nombre de federación no cumple con las reglas de nombres de federación o no es un identificador válido|
|45001|16|Error en la operación <statement>. El nombre de federación especificado no existe|El nombre de federación no existe|
|45002|16|Error en la operación <statement>. El nombre de la clave de federación especificado <distribution_name> no es válido|Clave de federación no válida o inexistente|
|45004|16|Error en la operación <statement>. El valor especificado no es válido para la clave de federación <distribution_name> y la federación <federation_name>|`USE FEDERATION`: use un valor de límite que se encuentre en el dominio del tipo de datos de la clave de federación o que no sea NULL.<br/><br/>`ALTER FEDERATION SPLIT`: use un valor válido en el dominio de la clave de federación que no sea ya un punto de división existente.<br/><br/>`ALTER FEDERATION DROP`: use un valor válido en el dominio de la clave de federación que ya sea un punto de división.|
|45005|16|No se puede ejecutar <statement> mientras otra operación de federación está en curso en la federación <federation_name> y el miembro con Id. <member_id>|Espere a que finalice la operación simultánea.|
|45006|16|Error en las operaciones <statement>. No se permiten las relaciones de clave externa en las tablas de referencia que hacen referencia a tablas federadas en miembros de federación|No compatible.|
|45007|16|Error en la operación <statement>. Las relaciones de clave externa entre tablas federadas deben incluir las columnas de clave de federación.|No compatible|
|45008|16|Error en la operación <statement>. El tipo de datos de la clave de federación no coincide con el tipo de datos de columna|No compatible.|
|45009|16|Error en la operación <statement>. No se admite la operación en conexiones de filtrado|No compatible.|
|45010|16|Error en la operación <statement>. No se puede actualizar la clave de federación|No compatible.|
|45011|16|Error en la operación <statement>. No se puede actualizar el esquema de clave de federación|No compatible.|
|45012|16|El valor especificado para la clave de federación no es válido|El valor debe encontrarse en el intervalo al que se dirige la conexión.<br/><br/>Si se filtra, el valor de la clave de federación especificado.<br/><br/>Si no se filtra, el intervalo cubierto por el miembro de federación.|
|45013|16|El SID ya existe con un nombre de usuario diferente|El SID de un usuario en un miembro de federación se copia desde el SID de la misma cuenta de usuario en la raíz de federación. Bajo determinadas condiciones, puede que el SID ya esté en uso.|
|45014|16|%ls no se admite en %ls|Operación no admitida.|
|45022|16|Error en la operación <statement>. El valor de límite especificado ya existe para la clave de federación <distribution_name> y la federación <federation_name>|Especifique un valor que ya sea un valor de límite|
|45023|16|Error en la operación <statement>. El valor de límite especificado no existe para la clave de federación <distribution_name> y la federación <federation_name>|Especifique un valor que no sea ya un valor de límite.|


<a id="bkmk_e_general_errors" name="bkmk_e_general_errors">&nbsp;</a>

## Errores generales


En la tabla siguiente se muestran todos los errores generales que no pertenecen a ninguna categoría anterior.


|Número de error|Gravedad|Descripción|
|---:|---:|:---|
|15006|16|<AdministratorLogin> no es un nombre válido porque contiene caracteres no válidos.|
|18452|14|Error de inicio de sesión. El inicio de sesión se realiza desde un dominio que no es de confianza y no se puede utilizar con autenticación de Windows.%.&#x2a;ls (Los inicios de sesión de Windows no se admiten en esta versión de SQL Server.).|
|18456|14|Error de inicio de sesión del usuario '%.&#x2a;ls'.%.&#x2a;ls%.&#x2a;ls (Error de inicio de sesión del usuario "%.&#x2a;ls". Error de cambio de contraseña. El cambio de contraseña durante el inicio de sesión no se admite en esta versión de SQL Server.)|
|18470|14|Error de inicio de sesión del usuario '%.&#x2a;ls'. Motivo: la cuenta está deshabilitada.%.&#x2a;ls|
|40014|16|No se pueden utilizar varias bases de datos en la misma transacción.|
|40054|16|No se admiten las tablas sin un índice agrupado en esta versión de SQL Server. Crear un índice agrupado e inténtelo de nuevo.|
|40133|15|No se admite esta operación en esta versión de SQL Server.|
|40506|16|El SID especificado no es válido para esta versión de SQL Server.|
|40507|16|No se puede invocar '%.&#x2a;ls' con parámetros en esta versión de SQL Server.|
|40508|16|No se admite la instrucción de USE para cambiar entre bases de datos. Use una nueva conexión para conectarse a una base de datos diferente.|
|40510|16|No se admite la instrucción '%.&#x2a;ls' en esta versión de SQL Server.|
|40511|16|No se admite la instrucción integrada '%.&#x2a;ls' en esta versión de SQL Server.|
|40512|16|No se admite la característica desusada '%ls' en esta versión de SQL Server.|
|40513|16|No se admite la variable del servidor '%.&#x2a;ls' en esta versión de SQL Server.|
|40514|16|No se admite '%ls' en esta versión de SQL Server.|
|40515|16|No se admite referencia al nombre de la base de datos o del servidor en '%. & #x2a; ls' en esta versión de SQL Server.|
|40516|16|Los objetos temporales globales no se admiten en esta versión de SQL Server.|
|40517|16|No se admite la opción de palabra clave o instrucción '%.&#x2a;ls' en esta versión de SQL Server.|
|40518|16|No se admite el comando DBCC '%.&#x2a;ls' en esta versión de SQL Server.|
|40520|16|La clase protegible '%S\_MSG' no se admite en esta versión de SQL Server.|
|40521|16|La clase protegible '%S\_MSG' no se admite en el ámbito de servidor en esta versión de SQL Server.|
|40522|16|El tipo de entidad de seguridad de base de datos '%.&#x2a;ls' no se admite en esta versión de SQL Server.|
|40523|16|La creación de usuario implícita '%.&#x2a;ls' no se admite en esta versión de SQL Server. Cree el usuario de forma explícita antes de usarlo.|
|40524|16|El tipo de datos '%.&#x2a;ls' no se admite en esta versión de SQL Server.|
|40525|16|WITH '%ls' no se admite en esta versión de SQL Server.|
|40526|16|El proveedor de conjuntos de filas '%.&#x2a;ls' no se admite en esta versión de SQL Server.|
|40527|16|Los servidores vinculados no se admiten en esta versión de SQL Server.|
|40528|16|Los usuarios no se pueden asignar a certificados, claves asimétricas o inicios de sesión de Windows en esta versión de SQL Server.|
|40529|16|La función integrada '%.&#x2a;ls' en el contexto de suplantación no se admite en esta versión de SQL Server.|
|40532|11|No se puede abrir el servidor "%.&#x2a;ls" solicitado por el inicio de sesión. Error de inicio de sesión.|
|40553|16|La sesión ha terminado debido al uso excesivo de la memoria. Intente modificar la consulta para procesar menos filas.<br/><br/>*Nota:* la reducción del número de operaciones `ORDER BY` y `GROUP BY` en el código de Transact-SQL ayuda a reducir los requisitos de memoria de su consulta.|
|40604|16|No se pudo CREATE/ALTER DATABASE porque superaría la cuota del servidor.|
|40606|16|Adjuntar base de datos no se admite en esta versión de SQL Server.|
|40607|16|Los inicios de sesión de Windows no se admiten en esta versión de SQL Server.|
|40611|16|Los servidores pueden tener como máximo 128 reglas de firewall definidas.|
|40614|16|La dirección IP de inicio de la regla de firewall no puede superar la dirección IP final.|
|40615|16|No se puede abrir el servidor '{0}' solicitado por el inicio de sesión. No está permitido que el cliente con la dirección IP '{1}' acceda al servidor. Para habilitar el acceso, use el Portal de bases de datos SQL o ejecute sp\_set\_firewall\_rule en la base de datos maestra para crear una regla de firewall para esta dirección IP o intervalo de direcciones. Puede tardar hasta cinco minutos que este cambio surta efecto.|
|40617|16|El nombre de la regla de firewall que comienza con <rule name> es demasiado largo. La longitud máxima es 128.|
|40618|16|El nombre de la regla de firewall no puede estar vacío.|
|40620|16|Error de inicio de sesión del usuario "%.&#x2a;ls". Error de cambio de contraseña. El cambio de contraseña durante el inicio de sesión no se admite en esta versión de SQL Server.|
|40627|20|La operación en el servidor '{0}' y la base de datos '{1}' está en curso. Espere algunos minutos antes de volver a intentarlo.|
|40630|16|Error de validación de contraseña. La contraseña no cumple los requisitos de directiva porque es demasiado corta.|
|40631|16|La contraseña especificada es demasiado larga. No debe tener más de 128 caracteres.|
|40632|16|Error de validación de contraseña. La contraseña no cumple los requisitos de directiva porque no es lo suficientemente compleja.|
|40636|16|No se puede usar un nombre de base de datos reservado '%.&#x2a;ls' en esta operación.|
|40638|16|Id. de suscripción no válido <subscription-id>. La suscripción no existe.|
|40639|16|La solicitud no se ajusta al esquema: <schema error>.|
|40640|20|El servidor detectó una excepción inesperada.|
|40641|16|La ubicación especificada no es válida.|
|40642|17|El servidor está demasiado ocupado. Inténtelo de nuevo más tarde.|
|40643|16|El valor del encabezado x-ms-version especificado no es válido.|
|40644|14|No se pudo autorizar el acceso a la suscripción especificada.|
|40645|16|El nombre de servidor <servername> no puede estar vacío ni ser NULL. Solo puede contener letras minúsculas de la 'a' a la 'z', los números del 0 al 9 y guiones. El guión no puede estar al principio ni al final del nombre.|
|40646|16|El id. de suscripción no puede estar vacío.|
|40647|16|La suscripción <subscription-id no tiene nombre de servidor.|
|40648|17|Se han realizado demasiadas solicitudes. Inténtelo de nuevo más tarde.|
|40649|16|Se ha especificado un content-type no válido. Solo se admite application/xml.|
|40650|16|La suscripción <subscription-id> no existe o no está lista para la operación.|
|40651|16|No se pudo crear el servidor porque la suscripción <subscription-id> está deshabilitada.|
|40652|16|No se puede mover ni crear un servidor. La suscripción <subscription-id> excederá la cuota del servidor.|
|40671|17|Error de comunicación entre la puerta de enlace y el servicio de administración. Inténtelo de nuevo más tarde.|
|45168|16|El sistema de SQL Azure está bajo carga y está colocando un límite superior en operaciones DB CRUD simultáneas para un único servidor (por ejemplo, crear base de datos). El servidor especificado en el mensaje de error ha superado el número máximo de conexiones simultáneas. Inténtelo de nuevo más tarde.|
|45169|16|El sistema de SQL Azure está bajo carga y está colocando un límite superior en operaciones CRUD de servidor simultáneas para una única suscripción (por ejemplo, crear servidor). La suscripción especificada en el mensaje de error ha superado el número máximo de conexiones simultáneas y se denegó la solicitud. Inténtelo de nuevo más tarde.|


## Vínculos relacionados

- [Instrucciones y limitaciones generales de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/ee336245.aspx)
- [Administración de recursos](http://msdn.microsoft.com/library/azure/dn338083.aspx)

<!---HONumber=Nov15_HO1-->