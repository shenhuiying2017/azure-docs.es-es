<properties title="Data Dependent Routing" pageTitle="Enrutamiento dependiente de los datos" description="Cómo usar el ShardMapManager para enrutamiento dependiente de datos, una característica de escala elástica para Base de datos SQL de Azure" metaKeywords="sharding scaling, Azure SQL DB sharding, elastic scale, multi-shard, multishard, querying" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Enrutamiento dependiente de los datos

La clase **ShardMapManager** proporciona a las aplicaciones ADO.NET la capacidad de dirigir fácilmente los comandos y consultas de base de datos a la base de datos física adecuada en un entorno particionado. Se llama **enrutamiento dependiente de los datos** y es un patrón fundamental cuando se trabaja con bases de datos particionadas. Cada consulta o transacción específica en una aplicación que usa enrutamiento dependiente de los datos tiene restringido el acceso a una base de datos única por solicitud.  

A través del uso del enrutamiento dependiente de los datos, no es necesario que la aplicación haga seguimiento de las distintas cadenas de conexión o ubicaciones de base de datos asociadas con diferentes segmentos de datos en el entorno particionado. En lugar de eso, el [Administrador de mapa de particiones](./sql-database-elastic-scale-shard-map-management.md) asume la responsabilidad de entregar conexiones abiertas a la base de datos correcta cuando sea necesario, según los datos del mapa de particiones y el valor de la clave de particionamiento que es el destino de la solicitud de la aplicación. (Esta clave normalmente es *customer_id*, *tenant_id*, *date_key* o algún otro identificador específico que es un parámetro fundamental de la solicitud de base de datos). 

## Uso de ShardMapManager en una aplicación de enrutamiento dependiente de los datos 

En el caso de las aplicaciones que usan un enrutamiento dependiente de los datos, se debe crear una instancia de **ShardMapManager** por dominio de aplicación, durante la inicialización, usando la llamada de fábrica **GetSQLShardMapManager**.

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

En este ejemplo se inicializan un **ShardMapManager** y un **ShardMap** específico. 

En el caso de una aplicación que no manipula el mapa de particiones mismo, las credenciales que se usan en el método de fábrica para obtener el **ShardMapManager** (en el ejemplo anterior, *smmConnectionString*) deben ser credenciales que únicamente tienen permisos de solo lectura en la base de datos del **Mapa de particiones global** a la que hace referencia la cadena de conexión. Estas credenciales normalmente son distintas de las credenciales que se usan para abrir conexiones con el administrador de mapa de particiones. Consulte también [Administración de credenciales de Escalado elástico](sql-database-elastic-scale-manage-credentials.md). 

##Invocación de enrutamiento dependiente de los datos 

El método **ShardMap.OpenConnectionForKey(key, connectionString, connectionOptions)** devuelve una conexión ADO.Net lista para emitir comandos a la base de datos adecuada basada en el valor del parámetro **clave**. El **ShardMapManager** almacena en caché de la aplicación la información de partición, por lo que estas solicitudes no implican normalmente una consulta de base de datos contra la base de datos del **mapa de particiones global**. 

* El parámetro **clave** se usa como clave de consulta en el mapa de particiones para determinar la base de datos adecuada para la solicitud. 

* La **connectionString** se usa para pasar solo las credenciales de usuario para la conexión deseada. Ningún nombre de base de datos o de servidor se incluye en esta *connectionString* dado que el método determinará la base de datos y el servidor usando el **ShardMap**. 

* La enumeración **connectionOptions** se usa para indicar si la validación se produce o no cuando se entrega la conexión abierta. **ConnectionOptions.Validate** se recomienda. En un entorno donde es probable que los mapas de particiones cambien y las filas se muevan a otras bases de datos como resultado de operaciones de división o combinación, la validación garantiza que la consulta en caché de la base de datos basada en un valor clave sigue siendo correcta. La validación implica una breve consulta al mapa de particiones local en la base de datos de destino (no al mapa de particiones global) antes de que se entregue la conexión a la aplicación. 

Si falla la validación contra el mapa de particiones local (lo que indica que el caché es incorrecto), el Administrador de mapa de particiones consultará el mapa de particiones global para obtener el nuevo valor correcto de la consulta, actualizar la caché y obtener y devolver la conexión de base de datos adecuada. 

La única vez que **ConnectionOptions.None** (no validar) se acepta es cuando no se esperan cambios en la asignación de particiones mientras que una aplicación esté en línea. En ese caso, los valores en caché se pueden asumir como correctos siempre y la llamada de validación de ida y vuelta adicional a la base de datos de destino se puede omitir sin problemas. Eso puede reducir las latencias de transacción y el tráfico de la base de datos. Las **connectionOptions** también se pueden definir a través de un valor en un archivo de configuración para indicar si se esperan o no cambios en el particionamiento durante un período.  

Este es un ejemplo de código que usa el Administrador de mapa de partición para realizar el enrutamiento dependiente de los datos según el valor de una clave de tipo entero **CustomerID**, usando un objeto **ShardMap** llamado **customerShardMap**.  

###Ejemplo: enrutamiento dependiente de los datos 

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connection to the shard for that customer ID
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command 
        SqlCommand cmd = conn.CreateCommand(); 
        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 
    }  

Observe que en lugar de usar un constructor para una **SqlConnection**, seguido de una llamada **Open()** al objeto de conexión, se usa el método **OpenConnectionForKey** y entrega una nueva conexión ya abierta a la base de datos correcta. Las conexiones utilizadas de esta manera seguirán aprovechando completamente las agrupaciones de conexiones de ADO.Net. Siempre y cuando las transacciones y las solicitudes puedan verse satisfecha por una partición a la vez, esta debiera ser la única modificación necesaria en una aplicación utilizando ya ADO.Net. 

##Integración con el control de errores transitorios 

Un procedimiento recomendado para el desarrollo de aplicaciones de acceso a datos en la nube es asegurar de que la aplicación capture los errores transitorios en la conexión o la consulta a la base de datos y que las operaciones se recuperen varias veces antes de arrojar un error. El control de errores transitorios para aplicaciones en la nube se analiza en [Control de errores transitorios](http://msdn.microsoft.com/es-es/library/dn440719\(v=pandp.60\).aspx). 
 
El control de errores transitorios puede coexistir naturalmente con el patrón de Enrutamiento dependiente de los datos. El requisito clave es volver a intentar la solicitud de acceso a los datos completa, incluido el bloque **using** que obtuvo la conexión de enrutamiento dependiente de los datos. El ejemplo anterior se podría reescribir de la siguiente manera (observe el cambio resaltado). 

###Ejemplo: enrutamiento dependiente de los datos con control de errores temporales 

<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() => </span> 
<span style="background-color:  #FFFF00">    { </span>
        // Connection to the shard for that customer ID 
        using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId,  
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            // Execute a simple command 
            SqlCommand cmd = conn.CreateCommand(); 

            cmd.CommandText = @&quot;UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID&quot;; 

            cmd.Parameters.AddWithValue(&quot;@customerID&quot;, customerId); 
            cmd.Parameters.AddWithValue(&quot;@newPersonID&quot;, newPersonId); 
            cmd.ExecuteNonQuery(); 

            Console.WriteLine(&quot;Update completed&quot;); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


Los paquetes necesarios para implementar el control de errores transitorio se descargan automáticamente cuando crea la aplicación Kit de inicio de Escalado elástico. Los paquetes también están disponibles por separado en [Biblioteca de información empresarial: bloque de aplicación Control de errores transitorios](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/). Use la versión 6.0 o posterior. 

##Coherencia de las transacciones 

Las propiedades de las transacciones están garantizadas para todas las operaciones locales respecto a una partición. Por ejemplo, las transacciones enviadas a través del enrutamiento dependiente de los datos se ejecutan dentro del ámbito de la partición de destino de la conexión. En este momento, no se proporcionan funcionalidades para alistar conexiones múltiples en una transacción y, por lo tanto, no hay garantías de transacciones para las operaciones realizadas a través de las particiones.  

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
