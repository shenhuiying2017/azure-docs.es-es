<properties 
	pageTitle="Introducción a las herramientas de base de datos elástica" 
	description="Explicación básica de la característica de herramientas de base de datos elástica de Base de datos SQL de Azure, incluida una aplicación de ejemplo de ejecución sencilla." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/17/2015" 
	ms.author="sidneyh@microsoft.com"/>

#Introducción a las herramientas de base de datos elástica

La ampliación y reducción de la capacidad bajo demanda es una de las promesas clave de la informática en la nube. En las aplicaciones de base de datos, una técnica importante para la creación de tales soluciones escalables es el patrón conocido como particionamiento, en el que los datos se dividen físicamente entre varias bases de datos estructuradas de forma idéntica. Tradicionalmente, sin embargo, la creación y administración de aplicaciones que usan el particionamiento ha precisado una codificación significativa fuera de la lógica de negocios de la aplicación.

Las herramientas de base de datos elástica simplifican la creación y administración de aplicaciones mediante el particionamiento de base de datos en Base de datos SQL de Azure. Entre estas herramientas se incluyen la biblioteca de clientes de base de datos elástica y la herramienta de división y combinación. Juntas, implementan los aspectos de infraestructura de particionamiento y permiten centrarse en la lógica de negocios de la aplicación.

Este documento presenta la experiencia del desarrollador en el uso de la biblioteca de clientes de base de datos elástica.

Para obtener más información acerca del funcionamiento de las herramientas de base de datos elástica, consulte [Información general acerca de las herramientas de base de datos elástica](sql-database-elastic-scale-introduction.md).

Para obtener una lista de temas sobre las herramientas de base de datos elástica, consulte el [Mapa de documentación](sql-database-elastic-scale-documentation-map.md).

## Aplicación de ejemplo de base de datos elástica

El ejemplo crea una aplicación particionada sencilla y explora las capacidades clave de las herramientas de base de datos elástica. Para descargar y ejecutar la aplicación, siga los pasos mostrados a continuación o en el vídeo [Escalado elástico - Procedimientos iniciales](http://channel9.msdn.com/Blogs/Windows-Azure/Elastic-Scale-with-Azure-SQL-Database-Getting-Started).

### Requisitos previos
Para ejecutar la aplicación de ejemplo, debe usar Visual Studio y debe tener acceso a una Base de datos SQL de Azure que se ejecute en Azure. Si todavía no tiene una suscripción a Azure, regístrese en una [suscripción de prueba](http://azure.microsoft.com/pricing/free-trial/).
#### Visual Studio y NuGet

1. Se necesita Visual Studio 2012 o superior con C#. Descargue una versión gratuita desde [Descargas de Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
2. Nuget 2.7 o superior. Para obtener la versión más reciente, consulte [Instalación de NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)
#### Crear una base de datos SQL de Azure

* Consulte [Introducción a Base de datos SQL de Microsoft Azure](sql-database-get-started.md).

## Descarga y ejecución de la aplicación de ejemplo

La aplicación de ejemplo **Base de datos elástica con SQL de Azure - Introducción** ilustra los aspectos más importantes de la experiencia de desarrollo para aplicaciones particionadas usando herramientas de base de datos elástica SQL de Azure. Se centra en los casos de uso claves para el [Administrador de asignación de particiones](sql-database-elastic-scale-shard-map-management.md), el [Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md) y las [Consultas a través de particiones múltiples](sql-database-elastic-scale-multishard-querying.md). Para descargar y ejecutar el ejemplo, siga estos pasos:

1. Abra Visual Studio y seleccione **Archivo -> Nuevo -> Proyecto**.
2. En el cuadro de diálogo, haga clic en **En línea**.

    ![New Project>Online][2]
3. A continuación, en **Ejemplos**, haga clic en **Visual C#**.

    ![Click Visual C#][3]
4. En el cuadro de búsqueda, escriba **elastic db** para buscar el ejemplo. Aparece el título **Elastic DB Tools for Azure SQL - Getting Started**.

    ![Search Box][1]
 
5. Seleccione el ejemplo, elija un nombre y una ubicación para el nuevo proyecto y presione **Aceptar** para crear el proyecto.
6. Abra el archivo **app.config** en la solución para el proyecto de ejemplo y siga las instrucciones del archivo para agregar el nombre de servidor de base de datos SQL y su información de inicio de sesión (nombre de usuario y contraseña).
7. Compile y ejecute la aplicación. Cuando se le pregunte, permita que Visual Studio restaure los paquetes de NuGet de la solución. Esto descargará la versión más reciente de la biblioteca de clientes de base de datos elástica desde NuGet.
8. Juegue con las diferentes opciones para obtener información sobre las capacidades de la biblioteca de clientes. Anote los pasos que la aplicación lleva a cabo en la salida de la consola y explore el código que hay detrás a su antojo.

    ![progreso][4]

Enhorabuena; ha creado y ejecutado su primera aplicación particionada utilizando herramientas de base de datos elástica en Base de datos SQL de Azure. Eche un vistazo rápido a las particiones que creó el ejemplo conectando Visual Studio o SQL Server Management Studio a su servidor de Base de datos de Azure. Observará nuevas bases de datos de particiones de ejemplo y una base de datos de administrador de mapas de particiones que ha creado el ejemplo.

**Nota**: si no tiene SQL Server Management Studio, consulte [Administración de Base de datos SQL de Azure usando SQL Server Management Studio](sql-database-manage-azure-ssms.md), que incluye instrucciones para obtener la herramienta.

### Piezas clave del ejemplo de código

1. **Administración de particiones y mapas de particiones**: el código ilustra cómo trabajar con particiones, intervalos y asignaciones en el archivo **ShardMapManagerSample.cs**. Puede encontrar más información acerca de este tema aquí: [Administración de mapas de particiones](http://go.microsoft.com/?linkid=9862595).  
2. **Enrutamiento dependiente de los datos**: el enrutamiento de transacciones a la partición correcta se muestra en el archivo **DataDependentRoutingSample.cs**. Para obtener más detalles, consulte [Enrutamiento dependiente de datos](http://go.microsoft.com/?linkid=9862596). 
3. **Consultas a través de particiones múltiples**: las consultas a través de particiones se ilustran en el archivo **MultiShardQuerySample.cs**. Para obtener más detalles, consulte [Consultas a través de particiones múltiples](http://go.microsoft.com/?linkid=9862597).
4. **Incorporación de particiones vacías**: la incorporación interactiva de nuevas particiones vacías se realiza mediante el código del archivo **AddNewShardsSample.cs**. Aquí encontrará detalles de este tema: [Administración de mapas de particiones](http://go.microsoft.com/?linkid=9862595).

### Otras operaciones de escalado elástico

1. **División de una partición existente**: la capacidad de dividir particiones se proporciona a través de la **herramienta de división y combinación**. Puede encontrar más información sobre esta herramienta aquí: [Información general sobre la herramienta de división y combinación](sql-database-elastic-scale-overview-split-and-merge.md).
2. **Combinación de particiones existentes**: las combinaciones de particiones también se realizan mediante la **herramienta de división y combinación**. Para obtener más información, consulte: [Información general sobre la herramienta de división y combinación](sql-database-elastic-scale-overview-split-and-merge).   


## Coste

Las herramientas de base de datos elástica son gratuitas. No imponen cargos adicionales al coste del uso de Azure.

Por ejemplo, la aplicación de ejemplo crea nuevas bases de datos. El coste depende de la edición de Base de datos SQL de Azure que elija y del uso de Azure de la aplicación.

Para obtener información sobre los precios, consulte [Detalles de precios de Base de datos SQL](http://azure.microsoft.com/pricing/details/sql-database/).

## Pasos siguientes
Para obtener más información sobre las herramientas de base de datos elástica, consulte:

* [Mapa de documentación de herramientas de base de datos elástica](sql-database-elastic-scale-documentation-map.md) 
-    Ejemplos de código: 
    -    [Elastic DB with Azure SQL - Getting Started](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE) (Bases de datos elásticas con SQL de Azure - Introducción)
    -    [Elastic DB with Azure SQL - Integrating with Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE) (Bases de datos elásticas con SQL de Azure - Integración con Entity Framework)
    -    [Shard Elasticity on Script Center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe) (Elasticidad de particiones en el Centro de scripts)
-    Blog: [Elastic Scale Announcement](http://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/) (Anuncio de Escalado elástico)
-    Canal 9: [vídeo Elastic Scale Overview](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale) (Información general de Escalado elástico)
-    Foro de discusión: [Foro de Base de datos SQL de Azure](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)


<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
 

<!---HONumber=62-->