<properties 
   pageTitle="Depuración de trabajos U-SQL | Microsoft Azure" 
   description="Obtenga información sobre cómo depurar vértices U-SQL con error mediante Visual Studio. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/26/2016"
   ms.author="jgao"/>



#Depuración de trabajos U-SQL 

Obtenga información sobre cómo depurar trabajos U-SQL que se crearon debido a errores en el código del usuario mediante la herramienta Azure Data Lake Visual Studio. Gracias a la herramienta Data Lake Visual Studio, los usuarios pueden descargar el código compilado y los datos de vértice necesarios del clúster con el fin de realizar un seguimiento de los trabajos con error y depurarlos.

>[AZURE.NOTE] Visual Studio puede bloquearse si no dispone de estas dos actualizaciones de Windows: [Microsoft Visual C++ 2015 Redistributable Update 2](https://www.microsoft.com/download/details.aspx?id=51682) y [Universal C Runtime para Windows](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0).


##Requisitos previos
-	Leer el artículo de [introducción](data-lake-analytics-data-lake-tools-get-started.md)

## Creación y configuración de proyectos de depuración

Al abrir un trabajo con error en la herramienta Data Lake Visual Studio, recibirá una alerta. La información detallada del error se mostrará en la pestaña de errores y la barra de alerta amarilla de la parte superior de la ventana.

![Descarga de vértices para depuración de trabajos U-SQL de Azure Data Lake Analytics en Visual Studio](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

**Procedimiento para descargar vertet y crear una solución de depuración**

1.	Abra un trabajo U-SQL con error en Visual Studio.
2.	Haga clic en **Descargar** para descargar todos los flujos de entrada y recursos necesarios. Haga clic en **Reintentar** si la descarga no se realiza correctamente.
3.	Haga clic en **Abrir** cuando finalice la descarga para crear un proyecto de depuración local. Se creará una nueva solución de Visual Studio llamada "**VertexDebug**" con un proyecto vacío denominado "**LocalVertexHost**".

Si se utilizan operadores definidos por el usuario en el código U-SQL subyacente (Script.usql.cs), debe crear un proyecto de C# de biblioteca de clases que integre los operadores definidos por el usuario en el código, así como incluir este proyecto en la solución VertexDebug. Si ha registrado ensamblados .dll en la base de datos de Data Lake Analytics, debe agregar el código fuente de estos ensamblados a la solución VertexDebug.
 

**Procedimiento para configurar el proyecto**

1.	En el Explorador de soluciones, haga clic con el botón derecho en **LocalVertexHost** y, después, en **Propiedades**.
2.	Establezca que la ruta de acceso de salida sea la ruta de acceso del directorio de trabajo del proyecto LocalVertexHost. Puede obtener la ruta de acceso del directorio de trabajo del proyecto LocalVertexHost a través de las propiedades de LocalVertexHost.
3.	Compile el proyecto de C# para colocar el archivo .pdb en el directorio de trabajo del proyecto LocalVertexHost; también puede copiarlo manualmente en esta carpeta.
4.	En **Configuración de excepciones**, compruebe las excepciones de Common Language Runtime:

![Configuración de depuración de trabajos U-SQL de Azure Data Lake Analytics en Visual Studio](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)
 
##Depuración de trabajos

Cuando haya creado una solución de depuración descargando el vértice y configurado el entorno, puede comenzar a depurar el código U-SQL.

1.	En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **LocalVertexHost**, seleccione **Depurar** y, después, haga clic en **Iniciar nueva instancia**. LocalVertexHost debe establecerse como proyecto de inicio. Es posible que vea por primera vez el siguiente mensaje, del que puede hacer caso omiso. La pantalla de depuración puede tardar en mostrarse hasta un minuto.
 
    ![Advertencia de depuración de trabajos U-SQL de Azure Data Lake Analytics en Visual Studio](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

4.	Utilice la experiencia de depuración basada en Visual Studio (inspección, variables, etc.) para solucionar el problema.
5.	Cuando haya identificado un problema, corrija el código y, después, vuelva a generar el proyecto de C# antes de probarlo de nuevo hasta que se resuelvan todos los problemas. Cuando haya finalizado correctamente el proceso de depuración, la ventana de salida muestra el siguiente mensaje:

        The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).
 
##Reenvío de trabajos

Cuando haya terminado la depuración del código U-SQL, puede volver a enviar el trabajo con error.

1. Registre los nuevos ensamblados .dll en la base de datos ADLA.

    1.	En el Explorador de servidores o Cloud Explorer de la herramienta Data Lake Visual Studio, expanda el nodo **Bases de datos**.
    2.	Haga clic con el botón derecho en Ensamblados para registrar los ensamblados.
    3.	Registre los nuevos ensamblados .dll en la base de datos ADLA.
 
2.	También puede copiar el código de C# en el archivo de código subyacente script.usql.cs--C#.
3.	Vuelva a enviar el trabajo.

##Pasos siguientes

- [Tutorial: Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md)
- [Tutorial: Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Desarrollo de operadores U-SQL definidos por el usuario para trabajos de Análisis de Azure Data Lake](data-lake-analytics-u-sql-develop-user-defined-operators.md)

<!---HONumber=AcomDC_0727_2016-->