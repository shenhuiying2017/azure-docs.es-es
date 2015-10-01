<properties
   pageTitle="Migrar una base de datos a bases de datos SQL de Azure"
   description="Base de datos SQL de Microsoft Azure, implementación de base de datos, migración de base de datos, importación de base de datos, exportación de base de datos, asistente para migración"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="09/02/2015"
   ms.author="carlrab"/>

# Migrar una base de datos a bases de datos SQL de Azure

La base de datos SQL de Azure V12 ofrece una compatibilidad de motor con SQL Server 2014 y versiones posteriores casi completa. Por ello, la tarea de migrar la mayoría de las bases de datos de una instancia local de SQL Server 2005 o posterior a una Base de datos SQL de Azure es mucho más sencilla. La migración de muchas bases de datos es una operación de movimiento de datos y esquemas sencilla que requiere pocos cambios, o ninguno, en el esquema, así como pocas operaciones de reingeniería de aplicaciones, o ninguna. El ámbito de los cambios de las bases de datos es muy limitado.

Las funciones de ámbito de servidor de SQL Server no son compatibles con Base de datos SQL de Azure V12. Será necesario efectuar tareas de reingeniería en las bases de datos y las aplicaciones que dependen de estas funciones para poder migrarse. Aunque la Base de datos SQL de Azure V12 mejora la compatibilidad con una base de datos SQL Server local, la migración sigue siendo una operación que debe planearse y realizarse con cuidado, sobre todo en las bases de datos más complejas y de gran tamaño.

## Determinar la compatibilidad
Para determinar si la base de datos de SQL Server local es compatible con Base de datos SQL de Azure V12, puede comenzar la migración mediante uno de los dos métodos descritos en la opción 1 siguiente y ver si las rutinas de validación de esquemas detectan una incompatibilidad o puede usar SQL Server Data Tools en Visual Studio como se describe en la opción 2 siguiente para validar la compatibilidad. Si la base de datos de SQL Server local tiene problemas de compatibilidad, puede usar SQL Server Data Tools en Visual Studio o SQL Server Management Studio para abordar y resolver los problemas de compatibilidad.

## Métodos de migración
Existen varios métodos para migrar una base de datos de SQL Server local a Base de datos SQL de Azure V12.

- En las bases de datos de pequeñas a medianas, migrar bases de datos de SQL Server 2005 o posteriores compatibles es un procedimiento tan simple como ejecutar el asistente de implementación de bases de datos en la Base de datos de Microsoft Azure en SQL Server Management Studio, siempre que no tenga problemas de conectividad (ausencia de conectividad, ancho de banda reducido o problemas de tiempo de espera).
- En bases de datos de medianas a grandes o si tiene problemas de conectividad, puede usar SQL Server Management Studio para exportar los datos y esquemas a un archivo BACPAC (almacenado localmente o en un blob de Azure) y, a continuación, importar el archivo BACPAC en la instancia SQL de Azure. Si almacena el archivo BACPAC en un blob de Azure, también puede importar el archivo BACPAC desde el portal de Azure. Para obtener más información sobre un archivo BACPAC, consulte [Aplicaciones de capa de datos](https://msdn.microsoft.com/library/ee210546.aspx).
- En las bases de datos grandes, obtendrá el mejor rendimiento posible si migra el esquema y los datos por separado. Puede extraer el esquema en un proyecto de base de datos mediante SQL Server Management Studio o Visual Studio y, a continuación, implementar el esquema para crear la Base de datos SQL de Azure. A continuación, puede extraer los datos mediante BCP y, a continuación, usar BCP para importar los datos mediante secuencias paralelas a la Base de datos SQL de Azure. Migrar una base de datos grande y compleja llevará muchas horas independientemente del método que elija.

### Opción n.º 1:
***Migración de una base de datos compatible mediante SQL Server Management Studio ***

SQL Server Management Studio proporciona dos métodos para migrar la base de datos de SQL Server local compatible a una Base de datos SQL de Azure. Puede usar el asistente de implementación de bases de datos a Base de datos SQL de Microsoft Azure o exportar la base de datos a un archivo BACPAC, que puede importarse después para crear una nueva Base de datos SQL de Azure. El asistente valida la compatibilidad con la Base de datos SQL de Azure V12, extrae el esquema y los datos a un archivo BACPAC y, a continuación, la importa a la instancia de Base de datos SQL de Azure especificada. Para usar esta opción, consulte [Usar SSMS](sql-database-migrate-ssms.md).

### Opción n.º 2:
***Actualización del esquema de la base de datos sin conexión con Visual Studio e implementación con SQL Server Management Studio***

Si la base de datos de SQL Server local no es compatible o para determinar si es compatible, puede importar el esquema de la base de datos a un proyecto de base de datos de Visual Studio para efectuar su análisis. Para efectuar el análisis, especifique la plataforma de destino del proyecto como Base de datos SQL V12 y, a continuación, compile el proyecto. Si la compilación se realiza correctamente, esto significa que la base de datos es compatible. Si se produce un error en la compilación, puede resolver los errores de SQL Server Data Tools para Visual Studio ("SSDT"). Una vez que el proyecto se compila correctamente, puede publicarlo como una copia de la base de datos de origen y, a continuación, usar la función de comparación de datos de SSDT para copiar los datos de la base de datos de origen en la base de datos compatible con Azure SQL V12. Dicha base de datos actualizada se implementa en la base de datos SQL de Azure mediante la opción n.º 1. Si solo es necesaria la migración del esquema, es posible publicar el esquema directamente desde Visual Studio a la base de datos SQL de Azure. Use este método cuando el esquema de la base de datos requiera más cambios de los que se pueden controlar mediante el asistente para la migración por sí solo. Para usar esta opción, consulte [Usar Visual Studio](sql-database-migrate-visualstudio-ssdt.md).

## Cómo decidirse por la opción correcta
- Si prevé que se puede migrar la base de datos sin realizar cambios, use la opción n.º 1, ya que esta es la opción más rápida y sencilla. Si no está seguro, comience con la exportación de un BACPAC con solo el esquema de la base de datos, tal como se describe en la opción n.º 1. Si la exportación se realiza correctamente sin errores, podrá usar la opción n.º 1 para migrar la base de datos con sus datos.  
- Si se producen errores durante la exportación de la opción 1, use la opción 2 y corrija el esquema de base de datos sin conexión en Visual Studio mediante una combinación del Asistente para la migración y los cambios de esquema aplicados manualmente. Con este proceso, se actualizará in situ una copia de la base de datos de origen que, a continuación, se migrará a Azure mediante la opción n.º 1.

## Herramientas de migración
Entre las herramientas que se usan para realizar la migración se incluyen SQL Server Management Studio (SSMS) y las herramientas de SQL Server de Visual Studio (VS, SSDT), así como el portal de Azure.

> [AZURE.IMPORTANT]Asegúrese de instalar las versiones más recientes de las herramientas cliente, ya que las versiones anteriores no son compatibles con la Base de datos SQL de Azure V12.

### SQL Server Management Studio (SSMS)
SSMS puede usarse para implementar una base de datos compatible directamente en la base de datos SQL de Azure o para exportar una copia de seguridad lógica de la base de datos como BACPAC, que podrá importarse, a continuación, mediante SSMS para crear una nueva base de datos SQL de Azure.

[Descargue la versión más reciente de SSMS](https://msdn.microsoft.com/library/mt238290.aspx)

### Herramientas de SQL Server de Visual Studio (VS, SSDT)
Es posible usar las herramientas de SQL Server de Visual Studio para crear y administrar un proyecto de base de datos compuesto por un conjunto de archivos Transact-SQL para cada objeto del esquema. El proyecto se puede importar desde una base de datos o desde un archivo de secuencia de comandos. Una vez creado, el proyecto puede importarse en la base de datos SQL de Azure v12. La compilación del proyecto validará la compatibilidad del esquema. Al hacer clic en un error, se abrirá el archivo Transact-SQL correspondiente para su modificación, lo que permite corregir el error. Una vez corregidos todos los errores, el proyecto se podrá publicar directamente en la base de datos SQL, para crear una base de datos vacía, o en la base de datos de SQL Server original (una copia), para actualizar su esquema. Esto permitirá implementar la base de datos con sus datos mediante SSMS tal como se ha descrito anteriormente.

Use las [SQL Server Data Tools para Visual Studio más recientes](https://msdn.microsoft.com/library/mt204009.aspx) con Visual Studio 2013 Update 4 o versiones posteriores.

## Comparaciones
| Opción n.º 1: | Opción n.º 2: |
| ------------ | ------------ | ------------ |
| Implementación de una base de datos compatible en la base de datos SQL de Azure | Actualización de la base de datos in situ para su posterior implementación en la base de datos SQL de Azure |
|![SSMS](./media/sql-database-cloud-migrate/01SSMSDiagram.png)| ![Edición sin conexión](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png) |
| Usa SSMS | Usa VS y SSMS |
|Proceso sencillo que requiere que el esquema sea compatible. El esquema se migra sin cambios. | El esquema se importa a un proyecto de base de datos de Visual Studio. Las actualizaciones adicionales se realizan mediante SSDT para Visual Studio y el esquema final se usa para actualizar la base de datos in situ. |
| Siempre se implementa o exporta la base de datos completa. | Control total de los objetos que se incluyen en la migración. |
| No permite cambiar el resultado si hay errores, por lo que el esquema de origen debe ser compatible. | Estarán disponibles todas las características de SSDT para Visual Studio. El esquema se cambia sin conexión. | La validación de la aplicación se produce en Azure. Debe ser mínima, ya que el esquema se migra sin cambios. | La validación de la aplicación puede realizarse en SQL Server antes de implementar la base de datos en Azure. |
| Proceso de uno o dos pasos de configuración sencilla. | Proceso de varios pasos más complejo (más sencillo si solo se implementa el esquema). |

<!----HONumber=Sept15_HO3-->