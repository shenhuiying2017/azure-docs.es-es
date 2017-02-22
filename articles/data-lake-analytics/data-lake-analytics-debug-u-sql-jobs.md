---
title: "Depuración de trabajos U-SQL | Microsoft Docs"
description: "Obtenga información sobre cómo depurar vértices U-SQL con error mediante Visual Studio. "
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: yanacai
translationtype: Human Translation
ms.sourcegitcommit: a5bb452582f05981a17c2514e0e40db0571bf61d
ms.openlocfilehash: f9b485bfbfbeb8a95ae1908ef6b1733b9cc6999a


---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Depuración de código C# definido por el usuario para trabajos de U-SQL con errores

Aprenda a depurar trabajos de U-SQL con errores de código definido por el usuario mediante Herramientas de Azure Data Lake para Visual Studio.

## <a name="background"></a>Fondo

U-SQL proporciona un modelo de extensibilidad mediante C# con el que los usuarios pueden escribir código C# definido por el usuario, como extractores, reductores, etc., para conseguir una mayor extensibilidad (aprenda más sobre el [código definido por el usuario de U-SQL](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#user-defined-functions---udf)). Sin embargo, ningún desarrollador puede crear código sin cometer errores, y depurar sistemas de macrodatos es difícil dado que muchos sistemas solo proporcionan información limitada de depuración en tiempo de ejecución, como registros, etc. 

Las Herramientas de Azure Data Lake para Visual Studio ofrecen una característica llamada **depuración de vértice con errores** mediante la cual puede clonar fácilmente los entornos con errores (lo que incluye los datos de entrada intermedios y el código de usuario, etc.) de la nube a la máquina local para realizar el seguimiento de los trabajos con errores y depurarlos con el mismo tiempo de ejecución y los datos de entrada exactos en la nube.

En el siguiente vídeo se demuestra la **depuración de vértice con errores** en las Herramientas de Azure Data Lake para Visual Studio.

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>
>

> [!NOTE]
> Visual Studio puede bloquearse si no dispone de estas dos actualizaciones de Windows: [Microsoft Visual C++ 2015 Redistributable Update 2](https://www.microsoft.com/download/details.aspx?id=51682) y [Universal C Runtime para Windows](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0).
> 
> 

## <a name="prerequisites"></a>Requisitos previos
* Leer el artículo de [introducción](data-lake-analytics-data-lake-tools-get-started.md)

## <a name="download-failed-vertex-to-local"></a>Error al descargar el vértice en el entorno local

Al abrir un trabajo con error en las Herramientas de Azure Data Lake para Visual Studio, obtendrá una alerta. Los mensajes de error detallados se muestran en la pestaña de errores y la barra de alerta amarilla en la parte superior de la ventana.

1. Haga clic en **Descargar** para descargar todos los flujos de entrada y recursos necesarios. Haga clic en **Reintentar** si la descarga no se realiza correctamente. 

2. Haga clic en **Abrir** después de que finalice la descarga para generar el entorno de depuración local. Se creará y abrirá automáticamente una nueva instancia de Visual Studio con una solución de depuración. 

3. Los pasos de depuración varían un poco entre los trabajos con error con código subyacente y los ensamblados.

    - [Error de depuración del trabajo con código subyacente](#debug-job-failed-with-code-behind)
    - [Error de depuración del código con ensamblados](#debug-job-failed-with-assemblies)

![Descarga de vértices para depuración de trabajos U-SQL de Azure Data Lake Analytics en Visual Studio](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="debug-job-failed-with-code-behind"></a>Error de depuración del trabajo con código subyacente

Si ha escrito código definido por el usuario en el archivo de código subyacente (normalmente denominado "Script.usql.cs" en el proyecto de SQL U) y el trabajo de SQL U da error, el código fuente se importa automáticamente a la solución de depuración generada. Puede usar la experiencia de depuración basada en Visual Studio (inspección, variables, etc.) para solucionar el problema. 

Antes de realizar la depuración, asegúrese de que ha activado **Common Language Runtime Exceptions** (Excepciones de Common Language Runtime) en la ventana de configuración de excepciones (**Ctrl + Alt + E**).

![Configuración de depuración de trabajos U-SQL de Azure Data Lake Analytics en Visual Studio](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. Presione **F5** y el código subyacente se ejecutará automáticamente hasta que se detenga debido a una excepción.

2. Abra **ADLTool_Codebehind.usql.cs** en el proyecto, establezca los puntos de interrupción y luego presione F5 para depurar el código paso a paso.

    ![Excepción de depuración de U-SQL de Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>Error de depuración del trabajo con ensamblados

Si usa ensamblados registrados en scripts de U-SQL, el sistema no puede obtener el código fuente automáticamente; deberá realizar algunas configuraciones antes de depurar el código definido por el usuario, es decir, debe agregar el código fuente de los ensamblados a la solución generada automáticamente.

### <a name="configure-the-solution"></a>Configuración de la solución

1. Haga clic con el botón derecho en **Solution 'VertexDebug'** > **Add** > **Existing Project...** (Solución 'VertexDebug' > Agregar > proyecto existente) para buscar el código fuente de los ensamblados y agregar el proyecto a la solución de depuración.

    ![Depuración de U-SQL de Azure Data Lake Analytics: agregar proyecto](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Haga clic con el botón derecho en **LocalVertexHost** > **Properties** (Propiedades) en la solución y copie la ruta del **directorio de trabajo**.

3. Haga clic con el botón derecho en el **proyecto de código fuente del ensamblado** > **Properties** (Propiedades), seleccione la pestaña **Build** (Compilar) a la izquierda, pegue la ruta de acceso copiada en el paso 2 en **Output** > **Output path** (Salida > Ruta de salida).  

    ![Depuración de U-SQL de Azure Data Lake Analytics: definición de la ruta de acceso de pdb](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. Presione **Ctrl + Alt + E** y marque **Common Language Runtime Exceptions** (Excepciones de Common Language Runtime) en la ventana de configuración de excepciones.

### <a name="start-debug"></a>Inicio de la depuración

1. Haga clic con el botón derecho en el **proyecto de código fuente de ensamblado** > **Rebuild** (Recompilar) para enviar los archivos pdb de salida al directorio de trabajo de LocalVertexHost.

2. Presione **F5**; el proyecto se ejecutará automáticamente hasta que se detenga debido a una excepción. Es posible que vea por primera vez el siguiente mensaje, del que puede hacer caso omiso. La pantalla de depuración puede tardar en mostrarse hasta un minuto.

    ![Advertencia de depuración de trabajos U-SQL de Azure Data Lake Analytics en Visual Studio](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. Abrir el código fuente y establezca los puntos de interrupción; a continuación, presione **F5** para depurar el código paso a paso.

También puede usar la otra experiencia de depuración basada en Visual Studio (inspección, variables, etc.) para depurar el problema. 

**Tenga en cuenta que** debe recompilar el proyecto de código fuente de ensamblado cada vez que modifique el código para que entren en vigor los nuevos archivos pdb.

Cuando haya finalizado correctamente el proceso de depuración, la ventana de salida muestra el siguiente mensaje:

    The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).

![Azure Data Lake Analytics U-SQL debug succeed (La depuración de U-SQL de Azure Data Lake Analytics se realizó correctamente)](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>Reenviar el trabajo
Cuando haya terminado la depuración, puede volver a enviar el trabajo con error.

1. Registre los nuevos ensamblados .dll en la base de datos ADLA.

    1. Desde el Explorador de servidores o Cloud Explorer, expanda el nodo **ADLA account** > **Databases** (Cuenta de ADLA > Bases de datos).
    2. Haga clic con el botón derecho en **Assemblies** (Ensamblados) para registrar los ensamblados. 
    3. Registre los nuevos ensamblados .dll en la base de datos ADLA.
    ![Depuración de U-SQl de Azure Data Lake Analytics: registro del ensamblado](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
2. También puede copiar el código de C# de nuevo en el archivo de código subyacente script.usql.cs--C#.
3. Vuelva a enviar el trabajo.

## <a name="next-steps"></a>Pasos siguientes

* [Guía de programación de U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
* [Desarrollo de operadores U-SQL definidos por el usuario para trabajos de Análisis de Azure Data Lake](data-lake-analytics-u-sql-develop-user-defined-operators.md)
* [Tutorial: Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md)
* [Tutorial: Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)





<!--HONumber=Jan17_HO1-->


