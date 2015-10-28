<properties 
	title="Multi-tenant applications with elastic database tools and row-level security" 
	pageTitle="Aplicaciones de múltiples inquilinos con herramientas de bases de datos elásticas y seguridad de nivel de fila" 
	description="Aprenda a usar herramientas de bases de datos elásticas junto con la seguridad de nivel de fila para crear una aplicación con un nivel de datos altamente escalable en Base de datos de SQL de Azure que admite particiones de varios inquilinos." 
	metaKeywords="azure sql database elastic tools multi tenant row level security rls" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="tmullaney"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/19/2015" 
	ms.author="thmullan;torsteng;sidneyh" />

# Aplicaciones de múltiples inquilinos con herramientas de bases de datos elásticas y seguridad de nivel de fila 

Las [herramientas de bases de datos elásticas](sql-database-elastic-scale-get-started.md) y la [seguridad de nivel de fila (RLS)](https://msdn.microsoft.com/library/dn765131) ofrecen un conjunto eficaz de capacidades para el escalado de manera flexible y eficaz del nivel de datos de una aplicación de múltiples inquilinos con Base de datos de SQL Azure. Este artículo muestra cómo usar estas tecnologías conjuntamente para crear una aplicación con un nivel de datos altamente escalable que admite particiones de múltiples inquilinos, con **ADO.NET SqlClient** o **Entity Framework**.

* Las **herramientas de bases de datos elásticas** permiten a los desarrolladores escalar horizontalmente el nivel de datos de una aplicación a través de las prácticas de particionamiento estándar del sector, mediante un conjunto de bibliotecas de .NET y plantillas de servicio de Azure. La administración de particiones con el uso de la biblioteca cliente de la base de datos elástica le ayuda a automatizar y simplificar muchas de las tareas de infraestructura asociadas típicamente con el particionamiento. 

* La **seguridad de nivel de fila** permite a los desarrolladores almacenar datos para varios inquilinos en la misma base de datos con las directivas de seguridad para filtrar las filas que no pertenecen al inquilino mediante la ejecución de una consulta. La centralización de la lógica de acceso con RLS dentro de la base de datos, en lugar de en la aplicación, simplifica el mantenimiento y reduce el riesgo de error a medida que crece el código base de la aplicación. RLS requiere la actualización más reciente de [Base de datos SQL de Azure (V12)](sql-database-preview-whats-new.md).

Con todas estas características, una aplicación puede beneficiarse de mejoras de ahorro y la eficacia de costos al almacenar los datos para varios inquilinos en la misma base de datos de la partición. Al mismo tiempo, una aplicación todavía tiene la flexibilidad para ofrecer particiones aisladas de un único inquilino para los inquilinos "premium" que requieren garantías de rendimiento más estrictas ya que las particiones de varios inquilinos no garantizan la distribución equitativa de los recursos entre los inquilinos.

En resumen, las API de [enrutamiento dependiente de datos](sql-database-elastic-scale-data-dependent-routing.md) de la biblioteca cliente de la base de datos elástica conectan automáticamente los inquilinos a la base de datos de la partición correcta que contiene su clave de particionamiento (generalmente "TenantId"). Una vez conectado, una directiva de seguridad RLS dentro de la base de datos garantiza que los inquilinos solo pueden acceder a las filas que contienen su TenantId. Se supone que todas las tablas contienen una columna TenantId para indicar qué filas pertenecen a cada inquilino.

![Arquitectura de la aplicación de creación de blogs][1]

## Descarga del proyecto de ejemplo

### Requisitos previos
* Uso de Visual Studio (2012 o posterior) 
* Creación de tres Bases de datos SQL de Azure 
* Descargue el proyecto de ejemplo: [Herramientas de bases de datos elásticas para SQL de Azure - Particiones con varios inquilinos](http://go.microsoft.com/?linkid=9888163)
  * Rellene la información para las bases de datos al comienzo de **Program.cs** 

Este proyecto amplía el descrito en [Herramientas de bases de datos elásticas para SQL de Azure - Integración de Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) mediante la adición de compatibilidad con bases de datos de partición de varios inquilinos. Genera una aplicación de consola sencilla para la creación de blogs y publicaciones, con cuatro inquilinos y dos bases de datos de partición de varios inquilinos, tal como se muestra en el diagrama anterior.

Compile y ejecute la aplicación. Arrancará el administrador de mapas de particiones de las herramientas de bases de datos elásticas y ejecutará las siguientes pruebas:

1. Con Entity Framework y LINQ, cree un nuevo blog y muestre todos los blogs para cada inquilino.
2. Con ADO.NET SqlClient, muestre todos los blogs para un inquilino.
3. Intente insertar un blog del inquilino erróneo para comprobar que se produce un error.  

Tenga en cuenta que como todavía no se ha habilitado RLS en las bases de datos de la partición, cada una de estas pruebas revela un problema: los inquilinos pueden consultar los blogs que no pertenecen a ellos y la aplicación no impide insertar un blog del inquilino incorrecto. El resto de este artículo describe cómo resolver estos problemas mediante la imposición de aislamiento de inquilinos con RLS. Hay dos pasos:

1. **Capa de aplicación**: modifique el código de aplicación para establecer siempre CONTEXT\_INFO en el TenantId actual después de abrir una conexión. El proyecto de ejemplo ya lo ha hecho. 
2. **Capa de datos**: cree una directiva de seguridad RLS en cada base de datos de partición para filtrar las filas en función del valor de CONTEXT\_INFO. Deberá hacerlo para cada una de las bases de datos de la partición; en caso contrario, no se filtrarán las filas en las particiones de varios inquilinos. 


## Paso 1) Capa de la aplicación: establezca CONTEXT\_INFO en TenantId

Después de conectarse a una base de datos de partición mediante la API de enrutamiento dependiente de datos de la biblioteca cliente de la base de datos elástica, la aplicación todavía necesita indicar a la base de datos el TenantId que utiliza esa conexión para que una directiva de seguridad RLS puede filtrar las filas que pertenecen a otros inquilinos. El método recomendado para pasar esta información es establecer [CONTEXT\_INFO](https://msdn.microsoft.com/library/ms180125) en el TenantId actual para esa conexión. Tenga en cuenta que en Base de datos SQL de Azure, CONTEXT\_INFO se rellena previamente con un GUID específico para cada sesión, por lo que *debe* definir CONTEXT\_INFO en el TenantId correcto antes de ejecutar cualquier consulta en una conexión nueva para asegurarse de que ninguna fila tenga pérdidas inadvertidas.

### Entity Framework

Para las aplicaciones que utilizan Entity Framework, el enfoque más sencillo es establecer CONTEXT\_INFO dentro de la invalidación de ElasticScaleContext descrita en [Enrutamiento dependiente de datos con EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md/#data-dependent-routing-using-ef-dbcontext). Antes de devolver la conexión de intermediación a través de enrutamiento dependiente de datos, solo tiene que crear y ejecutar un SqlCommand que establece CONTEXT\_INFO en shardingKey (TenantId) especificado para esa conexión. De este modo, solo deberá escribir el código una vez para establecer CONTEXT\_INFO.

```
// ElasticScaleContext.cs 
// ... 
// C'tor for data dependent routing. This call will open a validated connection routed to the proper 
// shard by the shard map manager. Note that the base class c'tor call will fail for an open connection 
// if migrations need to be done and SQL credentials are used. This is the reason for the  
// separation of c'tors into the DDR case (this c'tor) and the internal c'tor for new shards. 
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(OpenDDRConnection(shardMap, shardingKey, connectionStr), true /* contextOwnsConnection */)
{
}

public static SqlConnection OpenDDRConnection(ShardMap shardMap, T shardingKey, string connectionStr)
{
    // No initialization
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(shardingKey, connectionStr, ConnectionOptions.Validate);

        // Set CONTEXT_INFO to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SET CONTEXT_INFO @shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
} 
// ... 
```

Ahora CONTEXT\_INFO se establece automáticamente en el TenantId especificado cada vez que se invoca ElasticScaleContext:

```
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
	using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
	{     
		var query = from b in db.Blogs
	                orderby b.Name
	                select b;
		
		Console.WriteLine("All blogs for TenantId {0}:", tenantId);     
		foreach (var item in query)     
		{       
			Console.WriteLine(item.Name);     
		}   
	} 
}); 
```

### SqlClient de ADO.NET 

Para aplicaciones que utilizan SqlClient de ADO.NET, el enfoque recomendado es crear una función de contenedor alrededor de ShardMap.OpenConnectionForKey() que defina automáticamente CONTEXT\_INFO en el TenantId antes de devolver una conexión. Para asegurarse de que CONTEXT\_INFO siempre está correctamente definido, debe abrir conexiones únicamente con esta función de contenedor.

```
// Program.cs
// ...

// Wrapper function for ShardMap.OpenConnectionForKey() that automatically sets CONTEXT_INFO to the correct
// tenantId before returning a connection. As a best practice, you should only open connections using this 
// method to ensure that CONTEXT_INFO is always set before executing a query.
public static SqlConnection OpenConnectionForTenant(ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key
        conn = shardMap.OpenConnectionForKey(tenantId, connectionStr, ConnectionOptions.Validate);

        // Set CONTEXT_INFO to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SET CONTEXT_INFO @shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient
// If row-level security is enabled, only Tenant 4's blogs will be listed
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine("--\nAll blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);
        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## Paso 2) Capa de datos: cree una directiva de seguridad de nivel de fila y restricciones 

### Cree una directiva de seguridad para filtrar la selección, actualización y eliminación de consultas 

Ahora que la aplicación está utilizando CONTEXT\_INFO para el TenantId actual antes de consultar, una directiva de seguridad RLS puede filtrar las consultas y excluir las filas que tienen un TenantId diferente.

RLS se implementa en T-SQL: una función de predicado definido por el usuario define la lógica de filtrado y una directiva de seguridad enlaza esta función a cualquier número de tablas. Para este proyecto, la función de predicado simplemente comprueba que la aplicación (en lugar de otro usuario de SQL) está conectada a la base de datos y que el valor de CONTEXT\_INFO coincide con el TenantId de una fila determinada. Las filas que cumplen estas condiciones se permiten a través del filtro para seleccionar o actualizar y eliminar consultas. Si no se ha establecido CONTEXT\_INFO, no se devolverá ninguna fila.

Para habilitar RLS, ejecute la siguiente instrucción T-SQL en todas las particiones con Visual Studio (SSDT), SSMS o el script de PowerShell incluido en el proyecto (o si está usando los [trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md), se pueden usar para automatizar la ejecución de este código T-SQL en todas las particiones):

```
CREATE SCHEMA rls -- separate schema to organize RLS objects 
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
	RETURNS TABLE     
	WITH SCHEMABINDING
AS
	RETURN SELECT 1 AS fn_accessResult          
		WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- the user in your application’s connection string (dbo is only for demo purposes!)         
		AND CONVERT(int, CONVERT(varbinary(4), CONTEXT_INFO())) = @TenantId -- @TenantId (int) is 4 bytes 
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts
GO 
```

> [AZURE.TIP]Para los proyectos más complejos que deben agregar la función de predicado en cientos de tablas, puede usar un procedimiento almacenado auxiliar que genera automáticamente una directiva de seguridad mediante un predicado en todas las tablas de un esquema. Consulte [Apply Row-Level Security to all tables – helper script (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script) (Aplicación de la seguridad de nivel de fila a todas las tablas - script auxiliar (blog)).

Si agrega después una nueva tabla, solo tiene que modificar la directiva de seguridad y agregar un predicado de filtro en la nueva tabla:

```
ALTER SECURITY POLICY rls.tenantAccessPolicy     
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable 
GO 
```

Ahora si vuelve a ejecutar la aplicación de ejemplo, los inquilinos no podrán ver las filas que no les pertenecen.

### Adición de restricciones CHECK para bloquear las inserciones y actualizaciones de inquilinos erróneos

En la actualidad, las directivas de seguridad RLS no impiden que la aplicación inserte de forma accidental filas al TenantId incorrecto o que actualice el TenantId de una fila visible para que tenga un nuevo valor. Para algunas aplicaciones, como aplicaciones de informes de solo lectura, no es un problema. Sin embargo, puesto que esta aplicación permite a los inquilinos insertar nuevos blogs, vale la pena crear una protección adicional que produce un error si el código de la aplicación por error intenta insertar o actualizar filas que infrinjan el predicado de filtro. Como se describe en [Row-Level Security: Blocking unauthorized INSERTs (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/23/row-level-security-blocking-unauthorized-inserts) (Seguridad a nivel de fila: bloqueo de inserciones no autorizadas (blog), la solución recomendada consiste en crear una restricción CHECK en cada tabla para aplicar el mismo predicado de filtro RLS para insertar y actualizar las operaciones.

Para agregar las restricciones CHECK, ejecute la siguiente instrucción T-SQL en todas las particiones, mediante SSMS, SSDT, o el script de PowerShell incluido (o trabajos de bases de datos elásticas), como se describió anteriormente:

```
-- Create a scalar version of the predicate function for use in check constraints 
CREATE FUNCTION rls.fn_tenantAccessPredicateScalar(@TenantId int)     
	RETURNS bit 
AS     
	BEGIN     
		IF EXISTS( SELECT 1 FROM rls.fn_tenantAccessPredicate(@TenantId) )         
			RETURN 1     
		RETURN 0 
	END 
GO 

-- Add the function as a check constraint on all sharded tables 
ALTER TABLE Blogs     
	WITH NOCHECK -- don't check data already in table     
	ADD CONSTRAINT chk_blocking_Blogs -- needs a unique name     
	CHECK( rls.fn_tenantAccessPredicateScalar(TenantId) = 1 ) 
GO

ALTER TABLE Posts     
	WITH NOCHECK     
	ADD CONSTRAINT chk_blocking_Posts     
	CHECK( rls.fn_tenantAccessPredicateScalar(TenantId) = 1 ) 
GO 
```

Ahora que la aplicación no puede insertar las filas que pertenecen a los inquilinos que no sea el actualmente conectado a la base de datos de la partición. Del mismo modo la aplicación no puede actualizar las filas visibles para tener un TenantId diferente. Si la aplicación intenta realizar una, se generará una excepción DbUpdateException.


### Adición de restricciones DEFAULT para rellenar automáticamente el TenantId para las inserciones 

Además de utilizar las restricciones CHECK para bloquear inserciones de inquilinos incorrectos, puede colocar una restricción DEFAULT en cada tabla para rellenar automáticamente el TenantId con el valor actual de CONTEXT\_INFO al insertar filas. Por ejemplo:

```
-- Create default constraints to auto-populate TenantId with the value of CONTEXT_INFO for inserts 
ALTER TABLE Blogs     
	ADD CONSTRAINT df_TenantId_Blogs      
	DEFAULT CONVERT(int, CONVERT(varbinary(4), CONTEXT_INFO())) FOR TenantId 
GO

ALTER TABLE Posts     
	ADD CONSTRAINT df_TenantId_Posts      
	DEFAULT CONVERT(int, CONVERT(varbinary(4), CONTEXT_INFO())) FOR TenantId 
GO 
```

Ahora la aplicación no necesita especificar un TenantId al insertar filas:

```
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
	using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
	{
		var blog = new Blog { Name = name }; // default constraint sets TenantId automatically     
		db.Blogs.Add(blog);     
		db.SaveChanges();   
	} 
}); 
```

> [AZURE.NOTE]Si utiliza las restricciones DEFAULT para un proyecto de Entity Framework, se recomienda que NO incluyen la columna de TenantId en el modelo de datos EF. Esto es porque las consultas de Entity Framework proporcionan automáticamente los valores predeterminados que invalidarán las restricciones DEFAULT creadas en T-SQL que usan CONTEXT\_INFO. Para utilizar las restricciones DEFAULT en el proyecto de ejemplo, por ejemplo, debe quitar el TenantId de DataClasses.cs (y ejecutar Add-Migration en la consola de administrador de paquetes) y usar T-SQL para asegurarse de que el campo solo existe en las tablas de base de datos. De este modo, EF no proporcionará automáticamente los valores predeterminados incorrectos al insertar datos.

### (Opcional) Habilitar un "superusuario" para tener acceso a todas las filas
Es posible que algunas aplicaciones deseen crear un "superusuario" que puede tener acceso a todas las filas; por ejemplo, para habilitar los informes en todos los inquilinos de todas las particiones, o bien para realizar operaciones de división y combinación en particiones que implican el desplazamiento de filas de inquilinos entre las bases de datos. Para habilitar esto, debe crear un nuevo usuario de SQL ("superusuario" en este ejemplo) en la base de datos de cada partición. Luego modifique la directiva de seguridad con una nueva función de predicado que permita al usuario tener acceso a todas las filas:

```
-- New predicate function that adds superuser logic
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- note, should not be dbo!
            AND CONVERT(int, CONVERT(varbinary(4), CONTEXT_INFO())) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        )
GO

-- Atomically swap in the new predicate function on each table
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts
GO
```


### Mantenimiento 

* **Agregar particiones nuevas**: debe ejecutar el script T-SQL para habilitar RLS (y agregar restricciones CHECK) en las nuevas particiones; en caso contrario, no se filtrarán las consultas en esas particiones.

* **Agregar nuevas tablas**: debe agregar un predicado de filtro a la directiva de seguridad en todas las particiones siempre que se crea una nueva tabla; de lo contrario, no se filtrarán las consultas en la nueva tabla. Esto se puede automatizar mediante un desencadenador DDL, tal como se describe en [Apply Row-Level Security automatically to newly created tables (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx) (Aplicación de la seguridad de nivel de fila a las tablas recientemente creadas (blog)).


## Resumen 

Las herramientas de base de datos elásticas y la seguridad de nivel de fila pueden usarse juntas para escalar horizontalmente el nivel de datos de una aplicación con compatibilidad para particiones de un solo inquilino y de varios. Las particiones de varios inquilinos pueden utilizarse para almacenar datos de manera más eficaz (especialmente en casos donde un gran número de inquilinos solo tienen unas pocas filas de datos), mientras que las particiones de un único inquilino pueden usarse para admitir los inquilinos premium con requisitos más estrictos de aislamiento y rendimiento. Para obtener más información, consulte el [Mapa de documentación de herramientas de bases de datos elásticas](sql-database-elastic-scale-documentation-map.md) o [Referencia de seguridad de nivel de fila](https://msdn.microsoft.com/library/dn765131) en MSDN.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->

<!---HONumber=Oct15_HO3-->