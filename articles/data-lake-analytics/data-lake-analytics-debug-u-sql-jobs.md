---
title: "Depuración de trabajos U-SQL | Microsoft Docs"
description: "Obtenga información sobre cómo depurar vértices U-SQL con error mediante Visual Studio."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: saveenr
ms.openlocfilehash: 2a77c72d3062272305208934d6406d040266c753
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Depuración de código C# definido por el usuario para trabajos de U-SQL con errores

U-SQL proporciona un modelo de extensibilidad con C#, por lo que puede escribir el código para agregar funcionalidad, como un extractor o reductor personalizados. Para más información, vea [Guía de programación de U-SQL](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). En la práctica, puede que un código necesite depuración y los sistemas de macrodatos solo pueden proporcionar información limitada de depuración de tiempo de ejecución, como los archivos de registro.

Herramientas de Azure Data Lake para Visual Studio proporciona una característica denominada **Depuración de vértice con errores**, que le permite clonar un trabajo erróneo de la nube a la máquina local para la depuración. El clon local captura todo el entorno en la nube, incluidos el código de usuario y los datos de entrada.

En el siguiente vídeo se demuestra la depuración de vértice con errores en las Herramientas de Azure Data Lake para Visual Studio.

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>

> [!NOTE]
> Visual Studio requiere las siguientes dos actualizaciones, si no están ya instaladas: [Microsoft Visual C++ 2015 Redistributable Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) y [Universal C Runtime en Windows](https://www.microsoft.com/download/details.aspx?id=50410).

## <a name="download-failed-vertex-to-local-machine"></a>Error al descargar el vértice en la máquina local

Al abrir un trabajo erróneo en Herramientas de Azure Data Lake para Visual Studio, verá una barra de alerta amarilla con mensajes de error detallados en la pestaña Error.

1. Haga clic en **Descargar** para descargar todos los flujos de entrada y recursos necesarios. Si la descarga no se completa, haga clic en **Reintentar**.

2. Haga clic en **Abrir** después de que finalice la descarga para generar el entorno de depuración local. Se creará y abrirá automáticamente una nueva instancia de Visual Studio con una solución de depuración.

![Descarga de vértices para depuración de trabajos U-SQL de Azure Data Lake Analytics en Visual Studio](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

Los trabajos pueden incluir archivos de origen con código subyacente o ensamblados registrados, y estos dos tipos tienen escenarios de depuración diferentes.

- [Depuración de un trabajo con error con código subyacente](#debug-job-failed-with-code-behind)
- [Depuración de un trabajo con error con ensamblados](#debug-job-failed-with-assemblies)


## <a name="debug-job-failed-with-code-behind"></a>Depuración de un trabajo con error con código subyacente

Si se produce un error del trabajo U-SQL (normalmente denominado `Script.usql.cs` en un proyecto U-SQL), el código fuente se importa en la solución de depuración.  Desde aquí, puede usar las herramientas de depuración de Visual Studio (visualización, variables, etc.) para solucionar el problema.

> [!NOTE]
> Antes de realizar la depuración, asegúrese de que ha activado **Common Language Runtime Exceptions** (Excepciones de Common Language Runtime) en la ventana de configuración de excepciones (**Ctrl + Alt + E**).

![Configuración de depuración de trabajos U-SQL de Azure Data Lake Analytics en Visual Studio](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. Pulse **F5** para ejecutar el código subyacente. Se ejecutará hasta que una excepción lo detenga.

2. Abra el archivo `ADLTool_Codebehind.usql.cs` y establezca los puntos de interrupción. A continuación, pulse **F5** para depurar el código paso por paso.

    ![Excepción de depuración de U-SQL de Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>Error de depuración del trabajo con ensamblados

Si utiliza ensamblados registrados en el script de U-SQL, el sistema no puede obtener el código fuente automáticamente. En este caso, agregue manualmente los archivos de código fuente de los ensamblados a la solución.

### <a name="configure-the-solution"></a>Configuración de la solución

1. Haga clic con el botón derecho en **Solution 'VertexDebug' > Add > Existing Project...** (Solución 'VertexDebug' > Agregar > proyecto existente) para buscar el código fuente de los ensamblados y agregar el proyecto a la solución de depuración.

    ![Depuración de U-SQL de Azure Data Lake Analytics: agregar proyecto](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Haga clic con el botón derecho en **LocalVertexHost > Properties** (Propiedades) en la solución y copie la ruta del **directorio de trabajo**.

3. Haga clic con el botón derecho en el **proyecto de código fuente del ensamblado > Properties** (Propiedades), seleccione la pestaña **Build** (Recompilar) a la izquierda y pegue la ruta de acceso en **Output > Output path** (Salida > Ruta de salida).

    ![Depuración de U-SQL de Azure Data Lake Analytics: definición de la ruta de acceso de pdb](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. Presione **Ctrl + Alt + E** y marque **Common Language Runtime Exceptions** (Excepciones de Common Language Runtime) en la ventana de configuración de excepciones.

### <a name="start-debug"></a>Inicio de la depuración

1. Haga clic con el botón derecho en el **proyecto de código fuente de ensamblado > Rebuild** (Recompilar) para enviar los archivos .pdb de salida al directorio de trabajo de `LocalVertexHost`.

2. Presione **F5** y el proyecto se ejecutará e hasta que se detenga debido a una excepción. Es posible que vea el siguiente mensaje de advertencia, del que puede hacer caso omiso sin problemas. La pantalla de depuración puede tardar en mostrarse hasta un minuto.

    ![Advertencia de depuración de trabajos U-SQL de Azure Data Lake Analytics en Visual Studio](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. Abra el código fuente y establezca los puntos de interrupción; a continuación, pulse **F5** para depurar el código paso a paso.

También puede usar las herramientas de depuración de Visual Studio (visualización, variables, etc.) para solucionar el problema.

> [!NOTE]
> Recompile el proyecto de código fuente de ensamblado cada vez que modifique el código para generar archivos .pdb actualizados.

Tras la depuración, si el proyecto se completa correctamente, la ventana de salida muestra el siguiente mensaje:

```
The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).
```

![Azure Data Lake Analytics U-SQL debug succeed (La depuración de U-SQL de Azure Data Lake Analytics se realizó correctamente)](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>Reenviar el trabajo

Cuando haya terminado la depuración, vuelva a enviar el trabajo con error.

1. Para los trabajos con soluciones con código subyacente, copie el código de C# en el archivo de origen de código subyacente (normalmente, `Script.usql.cs`).
2. Para los trabajos con ensamblados, registre los ensamblados .dll actualizados en la base de datos ADLA:
    1. Desde el Explorador de servidores o Cloud Explorer, expanda el nodo **ADLA account > Databases** (Cuenta de ADLA > Bases de datos).
    2. Haga clic con el botón derecho en **Ensamblados** y registre los nuevos ensamblados .dll con la base de datos ADLA: ![Ensamblado de registro de depuración de U-SQL de Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
3. Vuelva a enviar el trabajo.

## <a name="next-steps"></a>Pasos siguientes

- [Guía de programación de U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Desarrollo de operadores U-SQL definidos por el usuario para trabajos de Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Tutorial: Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
