<properties 
   pageTitle="Migración con Visual Studio y SSDT" 
   description="Base de datos SQL de Microsoft Azure, migración de base de datos, importación de base de datos, exportación de base de datos, asistente para migración" 
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
   ms.date="04/14/2015"
   ms.author="pehteh"/>

#Actualización de la base de datos in situ para su posterior implementación en Base de datos SQL de Azure

![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/01VSSSDTDiagram.png)

Utilice esta opción cuando al migrar una base de datos a Base de datos SQL de Azure V12 se requieren cambios de esquema que no se pueden abordar con el Asistente para migración a SQL de Azure (SAMW). Esto ocurrirá si la base de datos usa características de SQL Server que no se admiten o todavía no se admiten en Base de datos SQL de Azure. En esta opción, Visual Studio se usa en primer lugar para crear un proyecto de base de datos a partir de la base de datos de origen. La plataforma de destino del proyecto se establece en Base de datos SQL de Azure V12 y se compila el proyecto para identificar todos los problemas de compatibilidad. SAMW puede corregir muchos problemas compatibilidad pero no todos, por lo que se utiliza para procesar todos los scripts en los proyectos como primer paso. El uso de SAMW es opcional pero muy recomendable. La compilación del proyecto después de procesar los archivos de script con SAMW identificará los problemas restantes que, a continuación, deben resolverse manualmente mediante las herramientas de edición de T-SQL en Visual Studio. Cuando el proyecto se compila correctamente, el esquema se publica en una copia (recomendado) de la base de datos de origen para actualizar su esquema y los datos in situ. La base de datos actualizada se implementa en Azure, ya sea directamente o bien mediante la exportación e importación de un archivo BACPAC, con las técnicas descritas en la opción 1.
 
Como esta opción implica la actualización del esquema de la base de datos in situ antes de realizar la implementación en Azure, se recomienda encarecidamente realizar esta operación en una copia de la base de datos. La herramienta de comparación de esquemas de Visual Studio puede utilizarse para revisar el conjunto completo de cambios que se aplicarán a la base de datos antes de publicar el proyecto.

El uso del Asistente para migración a SQL de Azure (SAMW) es opcional pero recomendado. SAMW detectará los problemas de compatibilidad en el cuerpo de funciones, procedimientos almacenados y desencadenadores que, en caso contrario, no se detectarán hasta la implementación. Si solo es necesario la implementación del esquema, el esquema actualizado se puede publicar directamente desde Visual Studio a Base de datos SQL de Azure.

## Pasos de migración

1.	Abra el **Explorador de objetos de SQL Server** en Visual Studio. Use **Agregar SQL Server** para conectarse a la instancia de SQL Server que contiene la base de datos que se está migrando. Busque la base de datos en el explorador, haga clic con el botón derecho en ella y seleccione **Crear nuevo proyecto...** 

![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)

2.	Configure las opciones de importación para **solo los objetos con ámbito de aplicación de importación**. Anule la selección de las opciones para importar los inicios de sesión de referencia, permisos y configuraciones de base de datos.

![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)

3.	Haga clic en **Iniciar** para importar la base de datos y crear el proyecto, que contiene un archivo de scripts T-SQL para cada objeto de la base de datos. Los archivos de scripts se anidan en las carpetas del proyecto.

![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)

4.	En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho y seleccione Propiedades. Se abrirá la página **Configuración del proyecto** en donde va a configurar la plataforma de destino para Base de datos de SQL de Microsoft Azure V12.

![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)

5.	Opcional: haga clic con el botón derecho en el proyecto y seleccione **Crear** para generar el proyecto (no es estrictamente necesario en este momento, pero hacerlo ahora le proporcionará una línea de base para el número de problemas de compatibilidad en el proyecto y la eficacia de los siguientes pasos).

![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)

6.	Opcional: haga clic con el botón derecho en el proyecto y seleccione **Proyecto de instantánea**. Tomando una instantánea al principio y posiblemente en fases posteriores durante la transformación, puede utilizar la herramienta de comparación de esquemas para comparar el esquema en cada fase.

![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png) - La toma de instantáneas del proyecto crea un archivo .dacpac con marca de tiempo que contiene el esquema completo del proyecto. Puede modificar el nombre de archivo para indicar en qué etapa del proceso se tomó esta instantánea. ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

7.	Procese los archivos de scripts importados mediante el Asistente para migración a SQL de Azure (SAMW). Utilice la opción de carpeta y seleccione la carpeta del proyecto raíz. ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)

8.	El Asistente procesará los archivos de scripts de esta carpeta y de todas sus subcarpetas. Se mostrará un resumen de los resultados en la página siguiente. ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)
9.	Haga clic en Siguiente para ver una lista resumen de los archivos que han cambiado. 

![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)

>Tenga en cuenta que se realizan copias temporales tanto de los archivos originales antes del procesamiento como de los archivos afectados después del procesamiento en las ubicaciones indicadas en la parte superior de la página.

10.	Haga clic en Sobrescribir y en Aceptar en el cuadro de diálogo de confirmación y los archivos originales se sobrescribirán con los archivos modificados. Tenga en cuenta que solo se sobrescribirán los archivos que ya se hayan modificado.
11.	Opcional. Utilice la comparación de esquemas para comparar el proyecto a una instantánea anterior o a la base de datos original para comprender qué cambios se han realizado por el asistente. Es posible que desee tomar también otra instantánea en este momento. 

![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)

12.	Vuelva a compilar el proyecto (consulte el paso anterior) para determinar qué errores permanecen.

13.	Examine los errores de forma sistemática para resolver cada problema. Evalúe el impacto de cada cambio en las aplicaciones que usan la base de datos.

14.	Cuando la base de datos está libre de errores, haga clic con el botón derecho en el proyecto y seleccione **Publicar** para generar y publicar la base de datos en una copia de la base de datos de origen (se recomienda usar una copia, al menos inicialmente). El objetivo de este paso es actualizar el esquema de la base de datos de origen y realice los cambios consiguientes en los datos de la base de datos.
- Antes de realizar la publicación, dependiendo de la versión de SQL Server de origen, puede que necesite restablecer la plataforma de destino del proyecto para habilitar la implementación. Si va a migrar una base de datos de SQL Server anterior, no debe introducir ninguna característica en el proyecto que no se admita en SQL Server de origen a menos que migre primero la base de datos a una versión más reciente de SQL Server. 
- Debe configurar el paso de publicación para habilitar las opciones de publicación adecuadas. Por ejemplo, si se han eliminado o comentado objetos no admitidos en el proyecto, estos deben eliminarse de la base de datos para que pueda permitir la publicación y eliminar datos de la base de datos de destino. 
- Si prevé que va a publicar de forma repetida 

15.	Implemente la base de datos copiada en Base de datos SQL de Azure mediante las técnicas descritas en la opción 1.

## Validación de la migración

Una vez completada la migración, es una buena idea para comparar el esquema de la base de datos migrada con el esquema de la base de datos de origen para familiarizarse con los cambios que se han realizado y asegurarse de que son los esperados y no causarán problemas al migrar aplicaciones a la nueva base de datos. Puede utilizar la herramienta de comparación de esquemas que se incluye con las herramientas de SQL Server de Visual Studio para realizar la comparación. Puede comparar la base de datos en Base de datos SQL de Azure con la base de datos de SQL Server original o con una instantánea tomada cuando se importó la base de datos por primera vez en el proyecto.

1.	Conéctese al servidor de Base de datos SQL de Azure que contiene la base de datos migrada y busque la base de datos. 

2.	Haga clic con el botón derecho en la base de datos y seleccione **Comparación de esquemas...** Se abrirá una nueva comparación de esquemas con la base de datos de Azure seleccionada como origen en el lado izquierdo. Use la lista desplegable Seleccionar destino en el lado derecho para seleccionar la base de datos de destino o el archivo de instantáneas para la comparación.

3.	Con el origen y el destino seleccionados, haga clic en Comparar para desencadenar la comparación. La carga del esquema de una base de datos compleja en Base de datos SQL de Azure puede tardar bastante tiempo. La carga del esquema y la realización de otras tareas de metadatos en Base de datos SQL de Azure tardará menos tiempo con un nivel de precios superior.

4.	Una vez completada la comparación, revise las diferencias. A menos que tenga alguna duda, no debe como regla general aplicar los cambios a cualquier esquema.

En la comparación de esquemas debajo de la base de datos Adventure Works 2014 de Base de datos SQL de Azure V12 situada a la izquierda, transformada y migrada mediante el Asistente para migración a SQL de Azure, se compara con la base de datos de origen de SQL Server situada a la derecha.

![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)

<!---HONumber=58-->