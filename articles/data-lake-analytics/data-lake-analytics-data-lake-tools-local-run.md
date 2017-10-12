---
title: "Prueba y depuración de trabajos U-SQL mediante la ejecución local y el SDK de U-SQL para Azure Data Lake | Microsoft Docs"
description: "Obtenga información sobre cómo usar Azure Data Lake Tools para Visual Studio y el SDK de U-SQL para Azure Data Lake para probar y depurar los trabajos de U-SQL en la estación de trabajo local."
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: yanacai
ms.openlocfilehash: 771a96df5cc66bac46e7144785be8cc072b57b31
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="test-and-debug-u-sql-jobs-by-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>Prueba y depuración de trabajos U-SQL mediante la ejecución local y el SDK de U-SQL para Azure Data Lake

Puede usar Azure Data Lake Tools para Visual Studio y el SDK de U-SQL para Azure Data Lake para ejecutar trabajos de U-SQL en la estación de trabajo local, de la misma forma que en el servicio Azure Data Lake. Estas dos características de ejecución local ahorran tiempo para probar y depurar los trabajos de U-SQL.

## <a name="understand-the-data-root-folder-and-the-file-path"></a>Información sobre la carpeta raíz de datos y la ruta de acceso de archivo

Tanto la ejecución local como el SDK de U-SQL requieren una carpeta raíz de datos. La carpeta raíz de datos es un "almacén local" de la cuenta de proceso local. Es equivalente a la cuenta de Azure Data Lake Store de una cuenta de Data Lake Analytics. Cambiar a una carpeta raíz de datos diferente es como que cambiar a otra cuenta de almacén. Si desea tener acceso a datos compartidos normalmente con carpetas raíz de datos diferentes, debe usar rutas de acceso absolutas en los scripts. O bien, cree vínculos simbólicos del sistema de archivos (por ejemplo, **mklink** en NTFS) en la carpeta raíz de datos para que apunte a los datos compartidos.

La carpeta raíz de datos se utiliza para:

- Almacenar metadatos, lo que incluye bases de datos, tablas, funciones con valores de tabla (TVF) y ensamblados.
- Buscar las rutas de acceso de entrada y salida que se definen como rutas de acceso relativas en U-SQL. El uso de rutas de acceso relativas facilita la implementación de sus proyectos U-SQL en Azure.

Puede usar tanto una ruta de acceso relativa como una ruta de acceso absoluta local en los scripts U-SQL. La ruta de acceso relativa es relativa a la ruta de acceso de la carpeta raíz de datos especificada. Se recomienda usar "/" como separador de ruta de acceso para que los scripts sean compatibles con el lado servidor. Estos son algunos ejemplos de rutas de acceso relativas y sus rutas de acceso absolutas equivalentes. En estos ejemplos, C:\LocalRunDataRoot es la carpeta raíz de datos.

|Ruta de acceso relativa|Ruta de acceso absoluta|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Uso de ejecución local desde Visual Studio

Data Lake Tools para Visual Studio proporciona la experiencia de ejecución local de U-SQL en Visual Studio. Con esta característica, puede:

- Ejecutar scripts U-SQL localmente, junto con los ensamblados de C#.
- Depurar un ensamblado de C# localmente.
- Crear, ver y eliminar catálogos de U-SQL (bases de datos locales, ensamblados, esquemas y tablas) desde el Explorador de servidores. También puede encontrar el catálogo local en el Explorador de servidores.

    ![Catálogo local de ejecución local de Data Lake Tools para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

El instalador de Data Lake Tools crea una carpeta C:\LocalRunRoot que se usará como la carpeta raíz de datos predeterminada. El paralelismo de ejecución local predeterminado es 1.

### <a name="to-configure-local-run-in-visual-studio"></a>Para configurar la ejecución local en Visual Studio

1. Abra Visual Studio.
2. Abra el **Explorador de servidores**.
3. Expanda **Azure** > **Data Lake Analytics**.
4. Haga clic en el menú **Data Lake** y luego haga clic en **Opciones y configuración**.
5. En el árbol de la izquierda, expanda **Azure Data Lake** y luego expanda **General**.

    ![Configuración de ejecución local de Data Lake Tools para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

Se necesita un proyecto de U-SQL de Visual Studio para realizar la ejecución local. Esta parte es diferente de la ejecución de scripts U-SQL desde Azure.

### <a name="to-run-a-u-sql-script-locally"></a>Para ejecutar un script U-SQL localmente
1. En Visual Studio, abra el proyecto U-SQL.   
2. Haga clic con el botón derecho en un script U-SQL en el Explorador de soluciones y después haga clic en **Submit Script** (Enviar script).
3. Seleccione **(Local)** como la cuenta de Analytics para ejecutar el script localmente.
También puede hacer clic en la cuenta **(Local)** en la parte superior en la ventana de script y luego haga clic en **Submit** (Enviar) (o use la tecla de acceso rápido CTRL + F5).

    ![Trabajos de envío de ejecución local de Data Lake Tools para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)

### <a name="debug-scripts-and-c-assemblies-locally"></a>Depurar scripts y ensamblados de C# localmente

Puede depurar ensamblados de C# sin enviarlos y registrarlos en el servicio Azure Data Lake Analytics. Puede establecer puntos de interrupción en el código subyacente del archivo y en un proyecto de C# al que se hace referencia.

#### <a name="to-debug-local-code-in-code-behind-file"></a>Para depurar el código local en el código subyacente del archivo.

1. Establezca puntos de interrupción en el archivo de código subyacente.
2. Presione F5 para depurar el script localmente.

> [!NOTE]
   > El siguiente procedimiento solo funciona en Visual Studio 2015. En versiones anteriores de Visual Studio puede que necesite agregar manualmente los archivos pdb.  
   >
   >

#### <a name="to-debug-local-code-in-a-referenced-c-project"></a>Para depurar el código local en un proyecto de C# al que se hace referencia.

1. Cree un proyecto de ensamblado de C# y compílelo para generar la dll de salida.
2. Registre la dll mediante una instrucción de U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Establezca puntos de interrupción en el código de C#.
4. Presione F5 para depurar el script con referencia a la dll de C# localmente.

## <a name="use-local-run-from-the-data-lake-u-sql-sdk"></a>Uso de la ejecución local del SDK de U-SQL para Data Lake

Además de ejecutar scripts U-SQL localmente mediante Visual Studio, puede usar el SDK de U-SQL para Azure Data Lake para ejecutar scripts U-SQL localmente con la línea de comandos e interfaces de programación. A través de estos elementos, puede escalar la prueba local de U-SQL.

Obtenga más información sobre el [SDK de U-SQL para Azure Data Lake](data-lake-analytics-u-sql-sdk.md).


## <a name="next-steps"></a>Pasos siguientes

* Para ver una consulta más compleja, consulte [Tutorial: Análisis de registros de sitios web mediante Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Para ver los detalles del trabajo, consulte [Usar el explorador de trabajos y la vista de trabajos para trabajos de Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Para usar la vista de ejecución de vértices, vea [Uso de la vista de ejecución de vértices de Data Lake Tools para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
