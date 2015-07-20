<properties 
   pageTitle="Migración a la base de datos SQL de Azure" 
   description="Base de datos SQL de Microsoft Azure, implementación de base de datos, migración de base de datos, importación de base de datos, exportación de base de datos, asistente para migración" 
   services="sql-database" 
   documentationCenter="" 
   authors="pehteh" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/15/2015"
   ms.author="pehteh"/>

# Información general
La base de datos SQL de Azure V12 ofrece una compatibilidad de motor con SQL Server 2014 casi completa. Como tal, simplifica enormemente la migración de la mayoría de las bases de datos de SQL Server a bases de datos SQL de Azure. La migración de muchas bases de datos es una operación sencilla que requiere pocos cambios, o ninguno, en el esquema, así como pocas operaciones de reingeniería de aplicaciones, o ninguna. Además, el ámbito de los cambios de las bases de datos es muy limitado.

Por diseño, la base de datos SQL no admite características de ámbito de servidor de SQL Server. De este modo, las bases de datos y las aplicaciones basadas en estas características seguirán necesitando operaciones de reingeniería para poder migrarse. Aunque la base de datos SQL V12 mejora la compatibilidad con SQL Server, la migración sigue siendo una operación que debe planearse y realizarse con cuidado, sobre todo en las bases de datos más complejas y de gran tamaño.

## Visión general
Existen varios enfoques que se pueden adoptar a la hora de migrar una base de datos de SQL Server a Azure. Cada uno de estos enfoques usa una o varias herramientas. Algunos enfoques son rápidos y sencillos, mientras que otros requieren una mayor preparación. Tenga en cuenta que la migración de una base de datos compleja y de gran tamaño puede tardar varias horas.

### Opción n.º 1:
***Migración de una base de datos compatible mediante SQL Server Management Studio (SSMS)***

La base de datos se implementa a la base de datos SQL de Azure mediante SSMS. La base de datos se puede implementar directamente o mediante la exportación a un BACPAC que, a continuación, se importa para crear una nueva base de datos SQL de Azure. Use esta opción cuando la base de datos de origen sea totalmente compatible con la base de datos SQL de Azure.

### Opción n.º 2:
***Migración de una base de datos casi compatible mediante el Asistente para migración a SQL de Azure (SAMW)***

La base de datos se procesa con el Asistente para migración a SQL de Azure para generar una secuencia de comandos de migración que contiene el esquema o el esquema con los datos en formato BCP. Use esta opción cuando el esquema de la base de datos requiera actualización y cuando se puedan controlar los cambios mediante el asistente.

### Opción n.º 3:
***Actualización del esquema de la base de datos sin conexión con Visual Studio (VS) y SAMW e implementación con SSMS***

La base de datos de origen se importa en un proyecto de base de datos de Visual Studio para el procesamiento sin conexión. A continuación, se ejecuta el Asistente para migración a SQL de Azure en todas las secuencias de comandos del proyecto para aplicar una serie de transformaciones y correcciones. El proyecto se adapta y se crea para la base de datos SQL V12 y se notifican los errores restantes. Dichos errores, a continuación, se resuelven manualmente mediante las herramientas de SQL Server de Visual Studio. Una vez compilado correctamente el proyecto, este se publica en una copia de la base de datos de origen. Dicha base de datos actualizada se implementa en la base de datos SQL de Azure mediante la opción n.º 1. Si solo es necesaria la migración del esquema, es posible publicar el esquema directamente desde Visual Studio a la base de datos SQL de Azure. Use esta opción cuando el esquema de la base de datos requiera más cambios de los que pueda realizar SAMW por sí solo.

## Cómo decidirse por la opción correcta
- Si prevé que se puede migrar la base de datos sin realizar cambios, use la opción n.º 1, ya que esta es la opción más rápida y sencilla. Si no está seguro, comience con la exportación de un BACPAC con solo el esquema de la base de datos, tal como se describe en la opción n.º 1. Si la exportación se realiza correctamente sin errores, podrá usar la opción n.º 1 para migrar la base de datos con sus datos.  
- Si se producen errores durante la exportación de la opción n.º 1, use el Asistente para migración a SQL de Azure (SAMW) para procesar la base de datos en modo de solo esquema, tal como se describe en la opción n.º 2. Si el asistente SAMW no notifica ningún error, podrá usar la opción n.º 2. 
- Si el asistente SAMW informa de que el esquema requiere trabajo adicional; a no ser que solo necesite pequeñas correcciones, se recomienda usar la opción n.º 3 y corregir el esquema de la base de datos sin conexión en Visual Studio mediante la combinación de SAMW y de los cambios de esquema de aplicación manual. Con este proceso, se actualizará in situ una copia de la base de datos de origen que, a continuación, se migrará a Azure mediante la opción n.º 1.

## Herramientas de migración
Entre las herramientas que se usan para realizar la migración se incluyen SQL Server Management Studio (SSMS), las herramientas de SQL Server de Visual Studio (VS, SSDT), el Asistente para migración a SQL de Azure (SAMW), así como el portal de Azure.

> Asegúrese de instalar las versiones más recientes de las herramientas cliente, ya que las versiones anteriores no son compatibles con la base de datos SQL v12.

### SQL Server Management Studio (SSMS)
SSMS puede usarse para implementar una base de datos compatible directamente en la base de datos SQL de Azure o para exportar una copia de seguridad lógica de la base de datos como BACPAC, que podrá importarse, a continuación, mediante SSMS para crear una nueva base de datos SQL de Azure.

Debe usar la versión más reciente de SSMS (CU6 en SQL Server 2013 y versiones posteriores) o descargar la [última versión](http://msdn.microsoft.com/evalcenter/dn434042.aspx) de la herramienta.

### Asistente para migración a SQL de Azure (SAMW)
El asistente SAMW puede usarse para analizar la compatibilidad del esquema de una base de datos existente con la base de datos SQL de Azure y, en muchos casos, puede usarse para generar y, a continuación, implementar una secuencia de comandos T-SQL que contiene esquemas y datos. El asistente informará de los errores detectados si encuentra contenido del esquema que no puede transformar. Si esto ocurre, será necesario editar la secuencia de comandos generada para poder implementarla correctamente. El asistente SAMW procesará el cuerpo de funciones o procedimientos almacenados que normalmente se excluyen de la validación que realizan las herramientas de SQL Server en Visual Studio (vea los detalles a continuación), por lo que es posible que se encuentren problemas que no detectaría Visual Studio por sí solo. La combinación del uso del asistente SAMW con las herramientas de Visual Studio de SQL Server puede reducir considerablemente la cantidad de trabajo necesario para migrar un esquema complejo.

Asegúrese de usar la versión más reciente del [Asistente para migración a SQL de Azure](http://sqlazuremw.codeplex.com/) desde CodePlex.

### Herramientas de SQL Server de Visual Studio (VS, SSDT)
Es posible usar las herramientas de SQL Server de Visual Studio para crear y administrar un proyecto de base de datos compuesto por un conjunto de archivos T-SQL para cada objeto del esquema. El proyecto se puede importar desde una base de datos o desde un archivo de secuencia de comandos. Una vez creado, el proyecto puede importarse en la base de datos SQL de Azure v12. La compilación del proyecto validará la compatibilidad del esquema. Al hacer clic en un error, se abrirá el archivo T-SQL correspondiente para su modificación, lo que permite corregir el error. Una vez corregidos todos los errores, el proyecto se podrá publicar directamente en la base de datos SQL, para crear una base de datos vacía, o en la base de datos de SQL Server original (una copia), para actualizar su esquema. Esto permitirá implementar la base de datos con sus datos mediante SSMS tal como se ha descrito anteriormente.

Debe usar la versión más reciente de las herramientas de Visual Studio para la base de datos SQL de Azure V12. Asegúrese de que tiene instalado Visual Studio 2013 con la actualización 4.

## Comparaciones
| Opción n.º 1: | Opción n.º 2: | Opción n.º 3: |
| ------------ | ------------ | ------------ |
| Implementación de una base de datos compatible en la base de datos SQL de Azure | Generación de una secuencia de comandos de migración con cambios y ejecución en la base de datos SQL de Azure | Actualización de la base de datos in situ para su posterior implementación en la base de datos SQL de Azure |
|![SSMS](./media/sql-database-cloud-migrate/01SSMSDiagram.png)| ![SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png) | ![Edición sin conexión](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png) |
| Usa SSMS | Usa SAMW | Usa SAMW, VS, SSMS |
|Proceso sencillo que requiere que el esquema sea compatible. El esquema se migra sin cambios. | SAMW genera la secuencia de comandos de T-SQL e incluye los cambios necesarios para garantizar la compatibilidad. Algunas características no compatibles no se incluirán en el esquema. La mayoría de dichas características excluidas se marcarán como errores. | El esquema se importa en un proyecto de base de datos en Visual Studio y (de manera opcional) se transforma con el asistente SAMW. Las actualizaciones adicionales se realizan con las herramientas de SQL Server de Visual Studio y el esquema final se usa para actualizar la base de datos in situ. |
| Si exporta a un BACPAC, podrá optar por migrar solo el esquema. | Permite configurar el asistente para crear una secuencia de comandos del esquema o del esquema y los datos. | Permite publicar solo el esquema directamente desde Visual Studio en Azure. La base de datos se actualiza con los cambios necesarios in situ para permitir la implementación/exportación del esquema y los datos. |
| Siempre se implementa o exporta la base de datos completa. | Permite excluir objetos específicos de la migración. | Control total de los objetos que se incluyen en la migración. |
| No permite cambiar el resultado si hay errores, por lo que el esquema de origen debe ser compatible. | La edición de la secuencia de comandos monolítica generada puede resultar compleja en caso de ser necesaria. La secuencia de comandos se puede abrir y editar en SSMS o Visual Studio con las herramientas de la base de datos de SQL Server. Todos los errores deben corregirse para poder implementar la secuencia de comandos en la base de datos SQL de Azure.| Todas las características de las herramientas de SQL Server que están disponibles en Visual Studio. El esquema se cambia sin conexión. |
| La validación de la aplicación se produce en Azure. Debe ser mínima, ya que el esquema se migra sin cambios. | La validación de la aplicación se produce en Azure tras la migración. La secuencia de comandos generada también se puede instalar de manera local para la validación de la aplicación inicial. | La validación de la aplicación puede realizarse en SQL Server antes de implementar la base de datos en Azure. |
| Herramienta compatible con Microsoft. | Herramienta compatible con la comunidad cuya descarga está disponible desde CodePlex. | Herramientas compatibles con Microsoft con uso opcional de la herramienta de la comunidad descargada desde CodePlex. |
| Proceso de uno o dos pasos de configuración sencilla. | La transformación, generación e implementación del esquema en la nube se organiza desde un asistente fácil de usar. | Proceso de varios pasos más complejo (más sencillo si solo se implementa el esquema). |


 

<!---HONumber=July15_HO2-->