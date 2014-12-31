<properties title="Get Started with  Azure SQL Database Elastic Scale" pageTitle="Introducción al Escalado elástico de Base de datos SQL de Azure" description="Explicación básica de la característica Escalado elástico de Base de datos SQL de Azure, incluida una aplicación de ejemplo de fácil ejecución." metaKeywords="sharding scaling, Azure SQL DB sharding, elastic scale" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Introducción a la vista previa de Escalado elástico de Base de datos SQL de Azure

La ampliación y reducción de la capacidad bajo demanda es una de las promesas clave de la informática en la nube. Históricamente, el cumplimiento de esta promesa ha sido tedioso y complejo para el nivel de base de datos de aplicaciones en la nube. En los últimos años, la industria ha convergido en patrones de diseño perfectamente establecidos comúnmente conocidos como particionamiento. Aunque el patrón de particionamiento general es la solución al reto, la creación y administración de aplicaciones que usan particionamiento requieren inversiones en infraestructura importantes independientes de la lógica de negocios de la aplicación. 

Escalado elástico de Base de datos SQL de Azure (en vista previa) permite a la capa de datos de una aplicación escalar horizontal y verticalmente a través de prácticas de particionamiento estándar de la industria, racionalizando al mismo tiempo y de forma significativa el desarrollo y administración de las aplicaciones en la nube partición hadas. Escalado elástico entrega funcionalidades para desarrolladores y administración que se proporcionan a través de un conjunto de bibliotecas .Net y a través de plantillas de servicio de Azure que puede hospedar en su propia suscripción de Azure para administrar sus aplicaciones altamente escalables. Escalado elástico de base de datos de Azure implementa los aspectos de infraestructura de particionamiento y, por lo tanto, le permite centrarse en la lógica de negocios de su aplicación. 

Este documento le presentaba la experiencia del desarrollador para Escalado elástico de Base de datos SQL de Azure. 

Para obtener más información acerca de cómo funciona Escalado elástico, consulte [Información general de Escalado elástico](http://go.microsoft.com/?linkid=9862592).

Para obtener una lista de temas sobre Escalado elástico, consulte [Mapa de documentación de Escalado elástico](./sql-database-elastic-scale-documentation-map.md)

## Aplicación de ejemplo de escalado elástico

El ejemplo crea una aplicación particionada sencilla y explora las capacidades clave de escalado elástico. Para descargar y ejecutar la aplicación, siga los pasos mostrados a continuación o en el vídeo [Escalado elástico: introducción](http://go.microsoft.com/?linkid=9862983). 

###Requisitos previos
Para ejecutar la aplicación de ejemplo, debe usar Visual Studio y debe tener acceso a una Base de datos SQL de Azure que se ejecute en Azure. Si todavía no tiene una suscripción a Azure, regístrese en una [suscripción de prueba](http://azure.microsoft.com/en-us/pricing/free-trial/).
####Visual Studio y NuGet

1. Se necesita Visual Studio 2012 o superior con C#. Descargue una versión gratuita en [Descargas de Visual Studio](http://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx).
2. Nuget 2.7 o superior. Para obtener la versión más reciente, consulte [Instalación de NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)
####Crear una base de datos SQL de Azure

* Consulte [Introducción a Base de datos SQL de Microsoft Azure](http://azure.microsoft.com/en-us/documentation/articles/sql-database-get-started/).

##Descarga y ejecución de la aplicación de ejemplo
La aplicación de ejemplo **Escalado elástico con Base de datos SQL de Azure: introducción** ilustra los aspectos más importantes de la experiencia de desarrollo para aplicaciones particionadas usando Escalado elástico de Base de datos SQL de Azure. Se centra en casos de uso clave para [administración de mapas de particiones](http://go.microsoft.com/?linkid=9862595), [enrutamiento dependiente de datos](http://go.microsoft.com/?linkid=9862596) y [consultas a través de particiones múltiples](http://go.microsoft.com/?linkid=9862597). Para descargar y ejecutar el ejemplo, siga estos pasos: 

1. Abra Visual Studio y seleccione **Archivo -> Nuevo -> Proyecto**.
2. En el cuadro de diálogo, haga clic en **En línea**.

    ![New Project>Online][2]
3. A continuación, haga clic en **Visual C#** bajo **Ejemplos**.

    ![Click Visual C#][3]
4. En el cuadro de búsqueda, escriba **Escalado elástico** para buscar el ejemplo. Aparece el título **Escalado elástico con Base de datos SQL de Azure: introducción**.

    ![Search Box][1]
 
5. Seleccione el ejemplo, elija un nombre y una ubicación para el nuevo proyecto y presione **Aceptar** para crear dicho proyecto.
6. Abra el archivo **app.config** en la solución para el proyecto de ejmplo y siga las instrucciones del archivo para agregar el nombre de servidor de Base de datos SQL y su información de inicio de sesión (nombre de usuario y contraseña).
7. Compile y ejecute la aplicación. Cuando se le pregunte, permita que Visual Studio restaure los paquetes de NuGet de la solución. Esto descargará la versión más reciente de las bibliotecas de cliente de Escalado elástico desde NuGet.
8. Juegue con las diferentes opciones para obtener información sobre las capacidades de Escalado elástico. Anote los pasos que la aplicación lleva a cabo en la salida de la consola y explore el código que hay detrás a su antojo.

    ![progress][4]

Enhorabuena; ha creado y ejecutado su primera aplicación de Escalado elástico correctamente en Base de datos SQL de Azure. Eche un vistazo rápido a las particiones que creó el ejemplo conectando SQL Server Management Studio a su servidor de Base de datos de Azure. Observará nuevas bases de datos de particiones de ejemplo y una base de datos de administrador de mapas de particiones que ha creado el ejemplo.

**Nota** Si no tiene SQL Server Management Studio, consulte [Administración de Base de datos SQL de Azure usando SQL Server Management Studio](http://azure.microsoft.com/en-us/documentation/articles/sql-database-manage-azure-ssms/), que incluye instrucciones para obtener la herramienta.  

### Piezas clave del ejemplo de código

1. **Administrar particiones y mapas de particiones**: el código ilustra cómo trabajar con particiones, intervalos y asignaciones en el archivo **ShardMapManagerSample.cs**. Puede encontrar más información acerca de este tema aquí: [Administración de mapas de particiones](http://go.microsoft.com/?linkid=9862595).  
2. **Enrutamiento dependiente de datos**: el enrutamiento de transacciones a la partición correcta se muestra en el archivo **DataDependentRoutingSample.cs**. Para obtener más detalles, consulte [Enrutamiento dependiente de datos](http://go.microsoft.com/?linkid=9862596). 
3. **Consultas a través de particiones múltiples**: las consultas a través de particiones se ilustran en el archivo **MultiShardQuerySample.cs**. Para obtener más detalles, consulte [Consultas a través de particiones múltiples](http://go.microsoft.com/?linkid=9862597).
4. **Agregar particiones vacías**: la incorporación iterativa de nuevas particiones vacías se realiza mediante el código del
archivo **AddNewShardsSample.cs**. Aquí encontrará detalles de este tema: [Administración de mapas de particiones](http://go.microsoft.com/?linkid=9862595).

### Otras operaciones de Escalado elástico

1. **División de una partición existente**: la capacidad de dividir particiones se proporciona a través del **servicio de división y combinación**. Puede encontrar más información acerca de este servicio aquí: [Servicio de división y combinación](http://go.microsoft.com/?linkid=9862795).
2. **Combinación de particiones existentes**: las combinaciones de particiones también se realizan usando el **servicio de división y combinación**. Para obtener más información, consulte: [Servicio de división y combinación](http://go.microsoft.com/?linkid=9862795).   


## Coste

Las bibliotecas y plantillas de servicio de Escalado elástico son gratuitas. Escalado elástico no impone cargos adicionales sobre el coste de su uso de Azure. 

Por ejemplo, la aplicación de ejemplo crea nuevas bases de datos. El coste depende de la edición de Base de datos SQL de Azure que elija y del uso de Azure de la aplicación.

Para obtener información sobre los precios, consulte [Detalles de precios de Base de datos SQL](http://azure.microsoft.com/en-us/pricing/details/sql-database/).

## Pasos siguientes
Para obtener más información acerca de la característica Escalado elástico, consulte:

* [Página de aprendizaje de Escalado elástico](./sql-database-elastic-scale-documentation-map.md) 
-    Ejemplos de código: 
    -    [Escalado elástico con Base de datos SQL de Azure: Introducción](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [Escalado elástico con Base de datos SQL de Azure: Integración con Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [Elasticidad de particiones en el centro de scripts](http://go.microsoft.com/?linkid=9862617)
-    Blog: [Anuncio de Escalado elástico](http://go.microsoft.com/?linkid=9862608)
-    Channel 9: [vídeo Información general de Escalado elástico](http://go.microsoft.com/?linkid=9862609)
-    Foro de discusión: [Foro de Base de datos SQL de Azure](http://social.msdn.microsoft.com/forums/azure/en-US/home?forum=ssdsgetstarted)


<!--Anchors-->
[Aplicación de ejemplo de Escalado elástico]: #The-Elastic-Scale-Sample-Application
[Descarga y ejecución de la aplicación de ejemplo]: #Download-and-Run-the-Sample-App
[Costo]: #Cost
[Pasos siguientes]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png

<!--HONumber=35_1-->
