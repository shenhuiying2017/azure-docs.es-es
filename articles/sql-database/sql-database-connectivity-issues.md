<properties
	pageTitle="Acciones para corregir la pérdida de conexión transitoria | Microsoft Azure"
	description="Acciones para evitar, diagnosticar y corregir los errores de conexión y otros errores transitorios al interactuar con la Base de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/26/2015"
	ms.author="genemi"/>


# Acciones para corregir errores de conexión y errores transitorios en Base de datos SQL


En este tema se describe cómo evitar, diagnosticar y mitigar los errores de conexión y los errores transitorios que el programa cliente encuentra cuando interactúa con Base de datos SQL de Azure.


<a id="i-transient-faults" name="i-transient-faults"></a>

## Errores transitorios


Un error transitorio es un error cuya causa subyacente se pronto solucionará por sí sola. Una causa ocasional de errores transitorios es cuando el sistema de Azure rápidamente desplaza recursos de hardware para equilibrar mejor la carga de varias cargas de trabajo. Durante este período de tiempo de reconfiguración, puede perderse la conexión con Base de datos SQL de Azure.


Si su programa cliente usa ADO.NET, se notifica al programa los errores transitorios a través del inicio de una excepción **SqlException**. La propiedad **Number** se puede comparar con la lista de errores transitorios, que se encuentra cerca de la parte superior del tema: [Mensajes de error para los programas de cliente de Base de datos SQL](sql-database-develop-error-messages).


### Comparación de conexión y comando


Cuando se produce un error transitorio durante un intento de conexión, se debe reintentar la conexión después de varios segundos.


Cuando se produce un error transitorio durante un comando de consulta SQL, no hay que reintentar el comando inmediatamente. Por el contrario, después de un retraso, hay que establecer de nuevo la conexión. Y se vuelve a probar el comando.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## Lógica de reintento para errores transitorios


Los programas cliente que encuentran ocasionalmente un error transitorio son más sólidos cuando contienen lógica de reintento.


Si el programa se comunica con la Base de datos SQL de Azure a través de un middleware de otros fabricantes, consulte con el proveedor si el middleware contiene lógica de reintento para errores transitorios.


### Principios de reintento


- Se debe volver a intentar abrir una conexión si el error es un error transitorio.


- No se debe reintentar directamente una instrucción SELECT de SQL que produce un error transitorio.
 - En su lugar, establezca una conexión nueva y, después, vuelva a intentar la instrucción SELECT.


- Cuando se produce un error en una instrucción UPDATE de SQL con un error transitorio, se debe establecer una conexión nueva antes de volver a intentar dicha instrucción.
 - La lógica de reintento debe asegurar que se completó toda la transacción de base de datos o que toda la transacción se revirtió.


#### Otras consideraciones para el reintento


- Un programa por lotes que se inicia automáticamente después del horario de trabajo y que se completará antes de mañana, puede permitirse el transcurso de largos intervalos de tiempo entre los reintentos.


- Un programa de la interfaz de usuario debe tener en cuenta la tendencia humana de abandonar tras una espera demasiado larga.
 - Sin embargo, la solución no debe ser repetir el reintento cada pocos segundos, porque dicha directiva puede saturar el sistema con solicitudes.


### Aumento del intervalo entre reintentos


El programa siempre debería esperar al menos entre 6 y 10 segundos antes de su primer reintento. En caso contrario, el servicio en la nube puede inundarse repentinamente con solicitudes que aún no están listas para su procesamiento.


Si se necesita más de un reintento, debe aumentar el intervalo antes de cada reintento sucesivo, hasta un valor máximo. Dos de las estrategias alternativas son las siguientes:


- Aumento monotónico del intervalo. Por ejemplo, podría agregar otros 5 segundos a cada intervalo sucesivo.


- Aumento exponencial del intervalo. Por ejemplo, puede multiplicar cada intervalo sucesivo por 1,5.


También puede establecer un número máximo de reintentos antes de que el programa se cierre automáticamente.


### Ejemplos de código con lógica de reintento


Los ejemplos de código con lógica de reintento, en una variedad de lenguajes de programación, están disponibles en:

- [Ejemplos de código de inicio rápido](sql-database-develop-quick-start-client-code-samples.md). 


<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

## Probar su lógica de reintento


Para probar la lógica de reintento, debe simular o producir un error que se pueda corregir mientras aún se ejecuta el programa.


### Prueba mediante la desconexión de la red


Una forma de probar la lógica de reintento es desconectar el equipo cliente de la red mientras se ejecuta el programa. El error será: - **SqlException.Number** = 11001 - Mensaje: "Este host es desconocido".


Como parte del primer intento de reintento, el programa puede corregir el error ortográfico y, a continuación, intenta conectarse.


Para facilitar esta práctica, desconecte el equipo de la red antes de iniciar el programa. El programa reconoce entonces un parámetro de tiempo de ejecución que hace que el programa: 1. Agregue temporalmente 11001 a la lista de errores para considerarlo como transitorio. 2. Intente su primera conexión como de costumbre. 3. Una vez capturado el error, quite 11001 de la lista. 4. Muestre un mensaje que indique al usuario que conecte el equipo a la red. Pause la ejecución mediante el método **Console.ReadLine** o un cuadro de diálogo con un botón Aceptar. El usuario presiona la tecla Entrar después de que se conecte el equipo a la red. 5. Nuevo intento de conexión, se espera una realización correcta.


### Realización de la prueba escribiendo mal el nombre de la base de datos al conectarse


El programa deliberadamente escribe mal el nombre de usuario antes del primer intento de conexión. El error será:- **SqlException.Number** = 18456 - Mensaje: "Error de inicio de sesión para el usuario 'WRONG\_MyUserName'".


Como parte del primer intento de reintento, el programa puede corregir el error ortográfico y, a continuación, intenta conectarse.


Para facilitar esta práctica, el programa puede reconocer un parámetro de tiempo de ejecución que hace que el programa: 1. Agregue temporalmente 18456 a la lista de errores para considerarlo como transitorio. 2. Agregue deliberadamente 'WRONG\_' al nombre de usuario. 3. Una vez capturado el error, quite 18456 de la lista. 4. Quite 'WRONG\_' del nombre de usuario. 5. Nuevo intento de conexión, se espera una realización correcta.


<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## Conexión: cadena de conexión


La cadena de conexión necesaria para conectarse a Base de datos SQL de Azure es ligeramente diferente de la cadena de conexión a Microsoft SQL Server. Puede copiar la cadena de conexión para la base de datos en el [Portal de vista previa de Azure](http://portal.azure.com/).


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]



#### 30 segundos de tiempo de espera de conexión


La conexión por Internet es menos estable que a través de una red privada. Por lo tanto se recomienda que en la cadena de conexión: establezca el parámetro de **tiempo de espera de la conexión** en **30** segundos (en lugar de 15 segundos).


<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

## Conexión: Dirección IP


Debe configurar el servidor de Base de datos SQL para que acepte la comunicación de la dirección IP del equipo que hospeda el programa cliente. Para ello, edite la configuración del firewall a través del [Portal de vista previa de Azure](http://portal.azure.com/).


Si olvida configurar la dirección IP, el programa fallará con un mensaje de error que indica la dirección IP necesaria.


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


Para obtener más información, consulte [Configuración del firewall en Base de datos SQL](sql-database-configure-firewall-settings.md).


<a id="c-connection-ports" name="c-connection-ports"></a>

## Conexión: puertos


Por lo general solo se necesita asegurar que el puerto 1433 está abierto para la comunicación saliente, en el equipo que hospeda el programa cliente.


Por ejemplo, cuando el programa cliente está hospedado en un equipo con Windows, Firewall de Windows en el host le permite abrir el puerto 1433:


1. Abrir el Panel de control
2. & gt; Todos los elementos de Panel de Control
3. &gt; Firewall de Windows
4. &gt; Configuración avanzada
5. & gt; Reglas de salida
6. &gt; Acciones
7. &gt; Nueva regla


Si el programa cliente se hospeda en una máquina virtual (VM) de Azure, debe leer:<br/>[Puertos más allá de 1433 para ADO.NET 4.5 y Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).


Para obtener información general acerca de la configuración de puertos y la dirección IP, consulte: [Firewall de Base de datos SQL de Azure](sql-database-firewall-configure.md).


<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

## Conexión: ADO.NET 4.5


Si el programa usa clases ADO.NET como **System.Data.SqlClient.SqlConnection** para conectarse a Base de datos SQL de Azure, le recomendamos que use .NET Framework 4.5 o una versión posterior.


ADO.NET 4.5: - Agrega compatibilidad al protocolo TDS 7.4. Esto incluye mejoras de conexión más allá de las que ofrecía 4.0. - Admite la agrupación de conexiones. Esto incluye una comprobación eficaz de que el objeto de conexión que ofrece el programa está funcionando.


Cuando se usa un objeto de conexión desde un grupo de conexiones, se recomienda que el programa cierre temporalmente la conexión cuando no la está usando inmediatamente. Volver a abrir una conexión no es tan costoso como crear una nueva conexión.


Si está usando ADO.NET 4.0 o versiones anteriores, se recomienda que lo actualice a la versión más reciente de ADO.NET más reciente. Desde julio de 2015, puede [descargar ADO.NET 4.6](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).


<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## Diagnóstico: Probar si las utilidades pueden conectarse


Si el programa no puede conectarse a la Base de datos SQL de Azure, una opción de diagnóstico es intentar conectarse con una utilidad. Lo ideal es que la utilidad se conecte mediante la misma biblioteca que usa el programa.


En cualquier equipo con Windows, puede probar estas utilidades: SQL Server Management Studio (ssms.exe), que se conecta mediante ADO.NET, o sqlcmd.exe, que se conecta mediante [ODBC](http://msdn.microsoft.com/library/jj730308.aspx).


Una vez conectado, compruebe que funciona una breve consulta SELECT de SQL.


<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

## Diagnóstico: Comprobar los puertos abiertos


Supongamos que sospecha que fallan los intentos de conexión debido a problemas con el puerto. En el equipo puede ejecutar una utilidad que informe de las configuraciones del puerto.


En Linux, resultan útiles las utilidades siguientes: `netstat -nap` o `nmap -sS -O 127.0.0.1` (cambie el valor del ejemplo por su dirección IP).


En Windows la utilidad [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) resulta útil. Esta es una ejecución de ejemplo que consultó la situación del puerto en un servidor de Base de datos SQL de Azure y que se ejecutó en un equipo portátil:


```
[C:\Users\johndoe]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

## Diagnóstico: Registrar los errores


A veces un problema intermitente se diagnostica mejor mediante la detección de un patrón general durante varios días o semanas.


El cliente puede ayudar al diagnóstico mediante el registro de todos los errores que encuentra. Puede entonces correlacionar las entradas del registro con los datos de error que registra internamente la propia Base de datos SQL de Azure.


Enterprise Library 6 (EntLib60) ofrece clases administradas de .NET para ayudar con el registro: [5 - El procedimiento más sencillo: uso del bloque de aplicación de registro](http://msdn.microsoft.com/library/dn440731.aspx)


<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

## Diagnóstico: Examinar los registros del sistema en busca de errores


Presentamos algunas instrucciones SELECT de Transact-SQL que consultan los registros de error y otra información.


| Consulta de un registro | Descripción |
| :-- | :-- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` | La vista [sys.event\_log](http://msdn.microsoft.com/library/dn270018.aspx) ofrece información sobre eventos individuales, incluidos los errores de conectividad relacionados con la reconfiguración, limitación y acumulación excesiva de recursos.<br/><br/>Lo ideal es poder correlacionar los valores de **start\_time** o **end\_time** con información sobre cuándo el programa cliente experimentó los problemas.<br/><br/>**SUGERENCIA:** tiene que conectarse a la base de datos **principal** para su ejecución. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` | La vista [sys.database\_connection\_stats](http://msdn.microsoft.com/library/dn269986.aspx) ofrece recuentos agregados de los tipos de eventos, para realizar diagnósticos adicionales.<br/><br/>**SUGERENCIA:** tiene que conectarse a la base de datos **principal** para su ejecución. |


### Diagnóstico: Buscar eventos de problema en el registro de la Base de datos SQL


Puede buscar entradas sobre los eventos de problema en el registro de Base de datos SQL de Azure. Pruebe la siguiente instrucción SELECT de Transact-SQL en la base de datos **principal**:


```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### Algunas filas devueltas de sys.fn\_xe\_telemetry\_blob\_target\_read\_file


Después se muestra el aspecto de una fila devuelta. Los valores nulos que se muestran no suelen ser nulos en las demás filas.


```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## Enterprise Library 6


Enterprise Library 6 (EntLib60) es un marco de clases de .NET que ayuda a implementar a clientes sólidos de servicios en la nube, uno de los cuales es el servicio de Base de datos SQL de Azure. Puede buscar temas dedicados a cada área en la que EntLib60 puede servir de ayuda en:- [Enterprise Library 6: abril de 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)-


La lógica de reintento para tratar errores transitorios es un área en la que EntLib60 le puede ayudar: [4 - La perseverancia, el secreto de todos los triunfos: uso del bloque de aplicación de control de errores transitorios](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)


Un breve ejemplo de código C# que usa EntLib60 en su lógica de reintento está disponible en: [Ejemplo de código: lógica de reintento de Enterprise Library 6, en C# para conectarse a Base de datos SQL](sql-database-develop-entlib-csharp-retry-windows.md)


> [AZURE.NOTE]El código fuente de EntLib60 está disponible de forma pública para su [descarga](http://go.microsoft.com/fwlink/p/?LinkID=290898). Microsoft no tiene previsto realizar más actualizaciones de mantenimiento o de característica en EntLib.


### Clases de EntLib60 para errores transitorios y reintentos


Las siguientes clases de EntLib60 son especialmente útiles para la lógica de reintento. Todas están en el espacio de nombres **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

*En el espacio de nombres **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:*

- Clase **RetryPolicy**
 - Método **ExecuteAction**


- Clase **ExponentialBackoff**


- Clase **SqlDatabaseTransientErrorDetectionStrategy**


- Clase **ReliableSqlConnection**
 - Método **ExecuteCommand**


En el espacio de nombres **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- Clase **AlwaysTransientErrorDetectionStrategy**

- Clase **NeverTransientErrorDetectionStrategy**


Estos son vínculos a información sobre EntLib60:

- Descarga gratis del libro [Developer's Guide to Microsoft Enterprise Library, 2nd Edition](http://www.microsoft.com/download/details.aspx?id=41145) (Guía del desarrollador para Microsoft Enterprise Library, 2ª edición).

- Procedimientos recomendados: [Orientación general sobre reintentos](best-practices-retry-general.md) tiene una excelente explicación detallada de la lógica de reintento.

- Descarga de NuGet de [Enterprise Library: Bloque de aplicación de control de errores transitorios 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)


### EntLib60: El bloque de registro


- El bloque de registro es una solución muy flexible y configurable que permite:
 - Crear y almacenar los mensajes de registro en una amplia variedad de ubicaciones.
 - Clasificar y filtrar los mensajes.
 - Recopilar la información contextual es útil para la depuración y el seguimiento, así como para los requisitos de registro generales y de auditoría.


- El bloque de registro abstrae la funcionalidad de registro desde el destino de registro para que el código de la aplicación sea coherente, con independencia de la ubicación y del tipo de almacén de registro de destino.


Para obtener información detallada, consulte: [5 - El procedimiento más sencillo: uso del bloque de aplicación de registro](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)


### Código fuente del método IsTransient de EntLib60


A continuación, en la clase **SqlDatabaseTransientErrorDetectionStrategy**, se encuentra el código fuente de C# para el método **IsTransient**. Desde abril de 2013, el código fuente explica qué errores se consideran transitorios y dignos de reintento.

Se han quitado de esta copia numerosas líneas **//comment** para mejorar la legibilidad.


```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## Más información


- [Agrupación de conexiones de SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)


- [*Retrying* es una biblioteca de reintentos de uso general con licencia de Apache 2.0, escrita en **Python**, para simplificar la tarea de agregar comportamiento de reintento a prácticamente todo.](https://pypi.python.org/pypi/retrying)

<!----HONumber=Nov15_HO1-->