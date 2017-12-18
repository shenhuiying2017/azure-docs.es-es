---
title: "Guía sobre carga de datos: Azure SQL Data Warehouse | Microsoft Docs"
description: Recomendaciones para cargar datos y realizar ELT con Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 7b698cad-b152-4d33-97f5-5155dfa60f79
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 12/13/2017
ms.author: barbkess
ms.openlocfilehash: 8903be1361d1574a5d81b69223f608ccb7a698ea
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="guidance-for-loading-data-into-azure-sql-data-warehouse"></a>Guía para cargar datos en Azure SQL Data Warehouse
Recomendaciones y optimizaciones de rendimiento para cargar datos en Azure SQL Data Warehouse. 

- Para más información acerca de PolyBase y del diseño de un proceso de extracción, carga y transformación (ETL), consulte [Design ELT for SQL Data Warehouse](design-elt-data-loading.md) (Diseño de ELT para SQL Data Warehouse).
- Si desea un tutorial sobre carga, consulte [Uso de PolyBase para cargar de datos de Azure Blob Storage en Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).


## <a name="extract-source-data"></a>Extracción de datos de origen

Al exportar datos en formato de archivo ORC desde SQL Server o Azure SQL Data Warehouse, las columnas de texto pesadas se pueden limitar a tan solo 50 columnas debido a errores de memoria insuficiente de Java. Para resolver este problema, exporte solo un subconjunto de las columnas.


## <a name="land-data-to-azure"></a>Dirigir datos a Azure
PolyBase no puede cargar filas que tengan más de un millón de bytes de datos. Cuando pone datos en los archivos de Azure Blob Storage o Azure Data Lake Store, estos deben tener menos de un millón de bytes de datos. Esto es cierto independientemente del esquema de tabla definido.

Reubique la capa de almacenamiento y el almacenamiento de datos para minimizar la latencia.

## <a name="data-preparation"></a>Preparación de los datos

Todos los formatos de archivo tienen diferentes características de rendimiento. Para lograr la carga más rápida, use archivos de texto delimitados comprimidos. La diferencia entre el rendimiento de UTF-8 y UTF-16 es mínima.

Dividir archivos comprimidos grandes en archivos comprimidos más pequeños.

## <a name="create-designated-loading-users"></a>Creación de usuarios de carga designados
Para ejecutar cargas con recursos de proceso adecuados, cree usuarios de carga designados para ejecutar cargas. Asigne cada usuario de carga a una clase de recurso específica. Para ejecutar una carga, inicie sesión como uno de los usuarios de carga y, a continuación, ejecute la carga. La carga se ejecuta con la clase de recurso del usuario.  Este método es más sencillo que intentar cambiar la clase de recursos de un usuario para que se ajuste a la necesidad actual de clase de recurso.

Este código crea un usuario de carga para la clase de recurso staticrc20. Se concede permiso de control de usuarios para una base de datos y, a continuación, se agrega al usuario como miembro del rol de base de datos staticrc20. Para ejecutar una carga con recursos de las clases de recursos staticRC20, simplemente inicie sesión como LoaderRC20 y ejecute la carga. 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

Ejecute cargas en clases de recursos estáticas en lugar de dinámicas. El uso de clases de recursos estáticas garantiza los mismos recursos independientemente del [nivel de servicio](performance-tiers.md#service-levels). Si utiliza una clase de recursos dinámica, los recursos varían según el nivel de servicio. Para las clases dinámicas, un nivel de servicio inferior significa que probablemente necesita usar una clase de recursos más grande para el usuario de carga.

### <a name="example-for-isolating-loading-users"></a>Ejemplo de aislamiento de usuarios de carga

A menudo, es necesario tener varios usuarios que puedan cargar datos en un almacén de datos de SQL. Dado que [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] requiere permisos de CONTROL en la base de datos, existirán múltiples usuarios con acceso de control en todos los esquemas. Para limitar esto, puede usar la instrucción DENY CONTROL.

Por ejemplo: tenemos los esquemas de base de datos schema_A para el departamento A, schema_B para el departamento B y los usuarios de PolyBase user_A y user_B con cargas en los departamentos A y B respectivamente. A ambos se les ha concedido permiso de CONTROL sobre la base de datos. Ahora, los creadores de los esquemas A y B bloquean dichos esquemas utilizando DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   

User_A y user_B estarán ahora bloqueados en el esquema de los otros departamentos.


## <a name="load-to-a-staging-table"></a>Carga en una tabla de almacenamiento provisional

Para la velocidad de carga más rápida, cargar en una tabla de ensayo round robin. Se trata de la manera más eficaz para mover los datos desde la capa de Azure Storage a SQL Data Warehouse.

Escale verticalmente el almacenamiento de datos si espera un trabajo de carga grande.

Ejecute un único trabajo de carga cada vez para obtener un rendimiento óptimo de carga.

### <a name="optimize-columnstore-index-loads"></a>Optimización de cargas de índice de almacén de columnas

Los índices de almacén de columnas necesitan mucha memoria para comprimir los datos en grupos de filas de alta calidad. Para una mejor compresión y eficacia del índice, el índice de almacén de columnas debe comprimir 1 048 576 filas en cada grupo de filas. Este es el máximo número de filas por grupo de filas. Si no hay memoria suficiente, es posible que el índice de almacén de columnas no pueda lograr las tasas de compresión máximas. Esto afecta a su vez al rendimiento de las consultas. Para un análisis detallado, consulte [Maximización de la calidad del grupo de filas del almacén de columnas](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Para asegurarse de que el usuario de carga tenga suficiente memoria para lograr la tasa de compresión máxima, utilice usuarios de carga que sean miembros de una clase de recursos mediana o grande. 
- Cargue filas suficientes para rellenar completamente los nuevos grupos de filas. Durante una carga masiva, cada 1 048 576 filas van directamente al almacén de columnas. En las cargas con menos de 102 400 filas se envían las filas al almacén delta, que almacena las filas en un índice agrupado hasta que hay suficientes para la compresión. Si carga muy pocas filas, puede que pasen todas al almacén delta y que no se compriman inmediatamente con el formato de almacén de columnas.


### <a name="handling-loading-failures"></a>Control de errores de carga

Una carga que utiliza una tabla externa puede producir el error *"Consulta anulada: se alcanzó el umbral de rechazo máximo al leer desde un origen externo"*. Esto indica que sus datos externos contienen registros *con modificaciones* . Un registro de datos se considera "con modificaciones" si los tipos de datos reales o el número de columnas no coincide con las definiciones de columna de la tabla externa o si los datos no se ajustan al formato de archivo externo especificado. 

Para corregirlo, asegúrese de que la tabla externa y las definiciones de formato de archivo externos son correctas y que los datos externos se ajustan a estas definiciones. En el caso de que un subconjunto de registros de datos externos estén modificados, puede rechazar estos registros para sus consultas mediante las opciones de rechazo en CREATE EXTERNAL TABLE DDL.



## <a name="insert-data-into-production-table"></a>Inserción de datos en la tabla de producción
Estas son recomendaciones para insertar filas en las tablas de producción.


### <a name="batch-insert-statements"></a>Instrucciones de INSERCIÓN por lotes
Una tabla pequeña se puede cargar perfectamente una sola vez con una [instrucción INSERT](/sql/t-sql/statements/insert-transact-sql.md) o incluso con una recarga periódica que le funcione bien con una instrucción del tipo `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Las inserciones simples no son tan eficaces como realizar una carga masiva. 

Si se tienen miles de inserciones simples a lo largo del día, agrúpelas por lotes para que pueda cargarlas masivamente.  Desarrolle los procesos para anexar las inserciones sencillas a un archivo y, a continuación, cree otro proceso que cargue el archivo de forma periódica.

### <a name="create-statistics-after-the-load"></a>Creación de estadísticas después de la carga

Para mejorar el rendimiento de las consultas, es importante crear estadísticas de todas las columnas de todas las tablas después de la primera carga, o bien después de que se realicen cambios importantes en los datos.  Para ver una explicación detallada de las estadísticas, consulte [Estadísticas][Estadísticas]. En el ejemplo siguiente se crean las estadísticas de cinco columnas de la tabla Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Rotación de claves de almacenamiento
Es un buen procedimiento de seguridad cambiar la clave de acceso al almacenamiento de blobs de forma regular. Dispone de dos claves de almacenamiento para su cuenta de almacenamiento de blobs. Esto es para que pueda realizar la transición de las claves.

Para rotar las cuentas de Azure Storage:

1. Cree una segunda credencial de ámbito de base de datos en función de la clave de acceso de almacenamiento secundaria.
2. Cree un segundo origen de datos externo basado en esta nueva credencial.
3. Coloque y cree las tablas externas para que señalen a los nuevos orígenes de datos externos. 

Después de migrar las tablas externas al nuevo origen de datos, realice estas tareas de limpieza:

1. Coloque el primer origen de datos externo.
2. Coloque la primera credencial de ámbito de base de datos en función de la clave de acceso de almacenamiento principal.
3. Inicie sesión en Azure y vuelva a generar la clave de acceso principal para que esté lista para la próxima rotación.


## <a name="next-steps"></a>Pasos siguientes
Para supervisar el proceso de carga, consulte [Supervisión de la carga de trabajo mediante DMV](sql-data-warehouse-manage-monitor.md).



