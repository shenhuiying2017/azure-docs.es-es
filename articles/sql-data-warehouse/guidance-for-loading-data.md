---
title: 'Procedimientos recomendados para la carga de datos: Azure SQL Data Warehouse | Microsoft Docs'
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
ms.openlocfilehash: 80974f7660696887783e97b674e2d9921fe2feac
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="best-practices-for-loading-data-into-azure-sql-data-warehouse"></a>Procedimientos recomendados para la carga de datos en Azure SQL Data Warehouse
Recomendaciones y optimizaciones de rendimiento para cargar datos en Azure SQL Data Warehouse. 

- Para más información acerca de PolyBase y del diseño de un proceso de extracción, carga y transformación (ETL), consulte [Design ELT for SQL Data Warehouse](design-elt-data-loading.md) (Diseño de ELT para SQL Data Warehouse).
- Si desea un tutorial sobre carga, consulte [Uso de PolyBase para cargar de datos de Azure Blob Storage en Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).


## <a name="preparing-data-in-azure-storage"></a>Preparación de datos en Azure Storage
Para minimizar la latencia, reubique la capa de almacenamiento y el almacenamiento de datos.

Al exportar datos en formato de archivo ORC, pueden producirse errores de falta de memoria de Java con las columnas de texto grandes. Para resolver este problema, exporte solo un subconjunto de las columnas.

PolyBase no puede cargar filas que tengan más de un millón de bytes de datos. Cuando ponga datos en los archivos de texto de Azure Blob Storage o Azure Data Lake Store, estos tienen que tener menos de un millón de bytes de datos. Esta limitación de datos es cierta independientemente del esquema de tabla definido.

Todos los formatos de archivo tienen diferentes características de rendimiento. Para lograr la carga más rápida, use archivos de texto delimitados comprimidos. La diferencia entre el rendimiento de UTF-8 y UTF-16 es mínima.

Dividir archivos comprimidos grandes en archivos comprimidos más pequeños.

## <a name="running-loads-with-enough-compute"></a>Ejecución de cargas con suficientes recursos de proceso

Para una velocidad de carga más rápida, ejecute solo una carga de trabajo de cada vez. Si no es factible, ejecute un número mínimo de cargas al mismo tiempo. Si espera un trabajo de carga grande, considere la posibilidad de escalar verticalmente el almacenamiento de datos antes de la carga.

Para ejecutar cargas con recursos de proceso adecuados, cree usuarios de carga designados para ejecutar cargas. Asigne cada usuario de carga a una clase de recurso específica. Para ejecutar una carga, inicie sesión como uno de los usuarios de carga y, a continuación, ejecute la carga. La carga se ejecuta con la clase de recurso del usuario.  Este método es más sencillo que intentar cambiar la clase de recursos de un usuario para que se ajuste a la necesidad actual de clase de recurso.

### <a name="example-of-creating-a-loading-user"></a>Ejemplo de creación de un usuario de carga
En este ejemplo se crea un usuario de carga para la clase de recurso staticrc20. El primer paso consiste en **conectarse al servidor principal** y crear un inicio de sesión.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```
Conéctese al almacenamiento de datos y cree un usuario. El código siguiente da por supuesto que está conectado a la base de datos denominada mySampleDataWarehouse. Muestra cómo crear un usuario llamado LoaderRC20 y concederle el permiso de control de usuario en una base de datos. A continuación, agrega el usuario como miembro del rol de base de datos staticrc20.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```
Para ejecutar una carga con recursos de las clases de recursos staticRC20, simplemente inicie sesión como LoaderRC20 y ejecute la carga.

Ejecute cargas en clases de recursos estáticas en lugar de dinámicas. El uso de clases de recursos estáticas garantiza los mismos recursos independientemente del [nivel de servicio](performance-tiers.md#service-levels). Si utiliza una clase de recursos dinámica, los recursos varían según el nivel de servicio. Para las clases dinámicas, un nivel de servicio inferior significa que probablemente necesita usar una clase de recursos más grande para el usuario de carga.

## <a name="allowing-multiple-users-to-load"></a>Posibilidad de que varios usuarios realicen cargas

A menudo, es necesario que varios usuarios puedan cargar datos en un almacén de datos. La carga con [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] requiere permisos de CONTROL en la base de datos.  El permiso CONTROL ofrece control de acceso a todos los esquemas. Probablemente no desee que todos los usuarios de carga tengan control de acceso en todos los esquemas. Para limitar los permisos, use la instrucción DENY CONTROL.

Por ejemplo: tenemos los esquemas de base de datos schema_A para el departamento A, schema_B para el departamento B y los usuarios de PolyBase user_A y user_B con cargas en los departamentos A y B respectivamente. A ambos se les ha concedido permiso de CONTROL sobre la base de datos. Ahora, los creadores de los esquemas A y B bloquean dichos esquemas utilizando DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   

User_A y user_B estarán ahora bloqueados en el esquema de los otros departamentos.


## <a name="loading-to-a-staging-table"></a>Carga en una tabla de almacenamiento provisional

Para lograr la velocidad de carga más rápida para mover datos a una tabla de almacenamiento de datos, cargue los datos en una tabla de almacenamiento provisional.  Definir la tabla de almacenamiento provisional como un montón y usar round robin para la opción de distribución. 

Tenga en cuenta que la carga suele ser un proceso de dos pasos en el que se carga primero en una tabla de almacenamiento provisional y, a continuación, se insertan los datos en una tabla de almacenamiento de datos de producción. Si la tabla de producción utiliza una distribución hash, el tiempo total para cargar e insertar puede ser más rápido si define la tabla de almacenamiento provisional con la distribución hash. Cargar la tabla de almacenamiento provisional lleva más tiempo, pero el segundo paso de insertar las filas en la tabla de producción no incurre en movimiento de datos a través de las distribuciones.

## <a name="loading-to-a-columnstore-index"></a>Carga en un índice de almacén de columnas

Los índices de almacén de columnas necesitan mucha memoria para comprimir los datos en grupos de filas de alta calidad. Para una mejor compresión y eficacia del índice, el índice de almacén de columnas tiene que comprimir el máximo de 1.048.576 filas en cada grupo de filas. Si no hay memoria suficiente, es posible que el índice de almacén de columnas no pueda lograr las tasas de compresión máximas. Esto afecta a su vez al rendimiento de las consultas. Para un análisis detallado, consulte [Maximización de la calidad del grupo de filas del almacén de columnas](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Para asegurarse de que el usuario de carga tenga suficiente memoria para lograr la tasa de compresión máxima, utilice usuarios de carga que sean miembros de una clase de recursos mediana o grande. 
- Cargue filas suficientes para rellenar completamente los nuevos grupos de filas. Durante una carga masiva, cada 1.048.576 filas se comprime directamente en el almacén de columnas como un grupo de filas completo. Las cargas con menos de 102.400 filas envían las filas al almacén delta, donde se mantienen las filas en un índice de árbol b. Si carga muy pocas filas, puede que pasen todas al almacén delta y que no se compriman inmediatamente con el formato de almacén de columnas.


## <a name="handling-loading-failures"></a>Control de errores de carga

Una carga que utiliza una tabla externa puede producir el error *"Consulta anulada: se alcanzó el umbral de rechazo máximo al leer desde un origen externo"*. Este mensaje indica que sus datos externos contienen registros con modificaciones. Un registro de datos se considera "con modificaciones" si los tipos de datos y l número de columnas no coincide con las definiciones de columna de la tabla externa o si los datos no se ajustan al formato de archivo externo especificado. 

Para corregir estos registros, asegúrese de que la tabla externa y las definiciones de formato de archivo externos son correctas y que los datos externos se ajustan a estas definiciones. En el caso de que un subconjunto de registros de datos externos contenga registros con modificaciones, puede rechazar estos registros para sus consultas mediante las opciones de rechazo en CREATE EXTERNAL TABLE.

## <a name="inserting-data-into-a-production-table"></a>Inserción de datos en una tabla de producción
Una tabla pequeña se puede cargar una sola vez con una [instrucción INSERT](/sql/t-sql/statements/insert-transact-sql.md), o incluso una recarga periódica de una búsqueda puede funcionar bien con una instrucción del tipo `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Sin embargo las inserciones simples no son tan eficaces como para realizar una carga masiva. 

Si se tienen miles de inserciones simples a lo largo del día, agrúpelas por lotes para que pueda cargarlas masivamente.  Desarrolle los procesos para anexar las inserciones sencillas a un archivo y, a continuación, cree otro proceso que cargue el archivo de forma periódica.

## <a name="creating-statistics-after-the-load"></a>Creación de estadísticas después de la carga

Para mejorar el rendimiento de las consultas, es importante crear estadísticas de todas las columnas de todas las tablas después de la primera carga, o bien después de que se realicen cambios importantes en los datos.  Para ver una explicación detallada de las estadísticas, consulte [Estadísticas][Estadísticas]. En el ejemplo siguiente se crean las estadísticas de cinco columnas de la tabla Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Rotación de claves de almacenamiento
Es un buen procedimiento de seguridad cambiar la clave de acceso al almacenamiento de blobs de forma regular. Tiene dos claves de almacenamiento para la cuenta de Blob Storage, lo que le permite la transición de las claves.

Para rotar las cuentas de Azure Storage:

1. Cree una segunda credencial de ámbito de base de datos en función de la clave de acceso de almacenamiento secundaria.
2. Cree un segundo origen de datos externo basado en esta nueva credencial.
3. Coloque y cree las tablas externas para que señalen a los nuevos orígenes de datos externos. 

Después de migrar las tablas externas al nuevo origen de datos, realice estas tareas de limpieza:

1. Coloque el primer origen de datos externo.
2. Coloque la primera credencial de ámbito de base de datos en función de la clave de acceso de almacenamiento principal.
3. Inicie sesión en Azure y vuelva a generar la clave de acceso principal para que esté lista para la próxima rotación.


## <a name="next-steps"></a>pasos siguientes
Para supervisar las cargas de datos, consulte [Supervisión de la carga de trabajo mediante DMV](sql-data-warehouse-manage-monitor.md).



