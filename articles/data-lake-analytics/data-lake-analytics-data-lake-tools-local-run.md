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
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 9d088a3afd8830c80462099cc869f1493d09fb41
ms.openlocfilehash: e0385315434ac7a10e121f88469aaaec45160b86


---
# <a name="test-and-debug-u-sql-jobs-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>Prueba y depuración de trabajos U-SQL mediante la ejecución local y el SDK de U-SQL para Azure Data Lake

Obtenga información sobre cómo usar Azure Data Lake Tools para Visual Studio y el SDK de U-SQL para Azure Data Lake para probar y depurar los trabajos de U-SQL en la estación de trabajo local.  Estas dos características de ejecución local permiten ejecutar trabajos de U-SQL en la estación de trabajo igual que lo haría en el servicio Azure Data Lake. Estas características ahorran tiempo para probar y depurar los trabajos de U-SQL.

Requisitos previos: 

- Una cuenta de Azure Data Lake Analytics. Consulte [Tutorial: Introducción a Azure Data Lake Analytics mediante Azure Portal](data-lake-analytics-get-started-portal.md). 
- Azure Data Lake Tools para Visual Studio.  Consulte [Tutorial: Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). 
- La experiencia de desarrollo de script U-SQL. Consulte [Tutorial: Introducción a Azure Data Lake Analytics mediante Azure Portal](data-lake-analytics-get-started-portal.md). 


## <a name="understand-data-root-and-file-path"></a>Comprender la ruta de acceso raíz de datos y de archivo

Tanto la ejecución local como y el SDK de U-SQL requieren una carpeta raíz de datos. Raíz de datos es un "almacén local" para la cuenta de proceso local. Es equivalente a la cuenta de Data Lake Store de una cuenta de Data Lake Analytics en Azure. Cambiar a una carpeta raíz de datos diferente es como que cambiar a otra cuenta de almacén. Si desea acceder a datos compartidos normalmente con diferentes carpetas raíz de datos, debe usar rutas de acceso absolutas en los scripts o crear vínculos simbólicos de sistema de archivo (por ejemplo, mklink en NTFS) en esta carpeta de datos raíz que señalen a los datos compartidos. 

La carpeta raíz de datos se utiliza para:

- Almacenar metadatos, lo que incluye bases de datos, tablas, TVF, ensamblados, etc.
- Buscar las rutas de acceso de entrada y salida que se definen como rutas de acceso relativas en U-SQL. El uso de rutas de acceso relativas facilita la implementación de sus proyectos U-SQL en Azure.

Puede usar tanto la ruta de acceso relativa como la ruta de acceso absoluta local en scripts U-SQL, y la ruta de acceso relativa es relativa respecto a la ruta de acceso de la carpeta raíz de datos especificada. Se recomienda usar "/" como separador de ruta de acceso para que los scripts sean compatibles con el lado del servidor. Estos son algunos ejemplos de rutas de acceso relativas y su ruta de acceso absoluta equivalente. En estos ejemplos, "C:\LocalRunDataRoot" es la raíz de datos:

|Ruta de acceso relativa|Ruta de acceso absoluta|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Uso de ejecución local desde Visual Studio

Data Lake Tools para Visual Studio proporciona la experiencia de ejecución local de U-SQL en Visual Studio. Con esta característica, puede:

- Ejecutar scripts U-SQL localmente, junto con los ensamblados de C#.
- Depurar un ensamblado de C# localmente.
- Crear, ver y eliminar catálogos de U-SQL (bases de datos locales, ensamblados, esquemas y tablas) desde el Explorador de servidores. El catálogo local también se puede encontrar desde el Explorador de servidores.

    ![Catálogo local de ejecución local de Data Lake Tools para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

El instalador de Data Lake Tools crea una carpeta "C:\LocalRunRoot" que se usará como la carpeta raíz de datos predeterminada. El paralelismo de ejecución local predeterminado es 1. 

### <a name="to-configure-local-run-in-visual-studio"></a>Para configurar la ejecución local en Visual Studio

1. Abra Visual Studio.
2. Abra el **Explorador de servidores**.
3. Expanda **Azure**, **Data Lake Analytics**.
4. Haga clic en el menú **Data Lake** y luego haga clic en **Opciones y configuración**. 
5. En el árbol de la izquierda, expanda **Azure Data Lake** y luego expanda **General**.

    ![Configuración de ejecución local de Data Lake Tools para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

Se necesita un proyecto de U-SQL de Visual Studio para realizar la ejecución local. Esta parte es diferente de la ejecución de scripts U-SQL desde Azure.

### <a name="to-run-a-u-sql-script-locally"></a>Para ejecutar un script U-SQL localmente
1. En Visual Studio, abra el proyecto U-SQL.   
2. Haga clic con el botón derecho en un script U-SQL en el Explorador de soluciones y después haga clic en **Submit Script** (Enviar script). Seleccione (local) como la cuenta de análisis para ejecutar el script localmente.
3. También puede haga clic en la cuenta (local) en la parte superior en la ventana de script y luego haga clic en **Enviar** (o use la tecla de acceso rápido **CTRL + F5**).

    ![Trabajos de envío de ejecución local de Data Lake Tools para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)



## <a name="use-local-run-from-data-lake-u-sql-sdk"></a>Uso de la ejecución local del SDK de U-SQL para Data Lake
 
Además de ejecutar scripts U-SQL localmente mediante Visual Studio, también puede usar el SDK de U-SQL para Azure Data Lake para ejecutar scripts U-SQL localmente con la línea de comandos e interfaces de programación, gracias a lo cual puede escalar la prueba local U-SQL.

### <a name="install-the-sdk"></a>Instalación del SDK

El SDK de U-SQL para Data Lake requiere las siguientes dependencias:

- [Microsoft .NET Framework 4.6 o superior](https://www.microsoft.com/en-us/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 y Windows SDK 10.0.10240.0 o posterior. Hay 2 formas posibles de obtener esto:

    - Instalar Visual Studio ([Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou)). Debe tener una carpeta "\Windows Kits\10" en la carpeta de archivos de programa, por ejemplo, "C:\Archivos de programa (x86)\Windows Kits\10\"; también deberá encontrar la versión del SDK de Windows 10 en "\Windows Kits\10\Lib". Si no ve estas carpetas, vuelva a instalar Visual Studio y asegúrese de que tiene el SDK de Windows 10 activado cuando realice la instalación. El script del compilador local U-SQL encontrará estas dependencias automáticamente a través de esta manera.

    ![SDK de Windows 10 para ejecución local de Data Lake Tools para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)
 
    - Alternativamente, instale [Data Lake Tools para Visual Studio](http://aka.ms/adltoolsvs). Los archivos preconfigurados del SDK de Windows y VC++ se pueden encontrar en C:\Archivos de programa (x86) \Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. En este caso, el compilador local U-SQL no puede encontrar las dependencias automáticamente, por lo que es necesario especificar la ruta de acceso de CppSDK para él. Puede copiar los archivos en otra ubicación o simplemente usarla como está. A continuación, puede optar por establecer una variable de entorno "SCOPE_CPP_SDK" en el directorio o especificar el argumento "-CppSDK" con este directorio en la línea de comandos de la aplicación auxiliar de ejecución local. 

Después de instalar el SDK, debe realizar los siguientes pasos de configuración:

- Establecer la variable de entorno **SCOPE_CPP_SDK**

    Si obtiene Microsoft Visual C++ y Windows SDK instalando Data Lake Tools para Visual Studio, compruebe que dispone de la siguiente carpeta:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Definir una nueva variable de entorno denominada "SCOPE_CPP_SDK" para que apunte a este directorio. O copie la carpeta en otra ubicación y especifique "SCOPE_CPP_SDK" como eso.

    Además de establecer la variable de entorno, también puede especificar el argumento "-CppSDK" cuando se utiliza la línea de comandos. Este argumento sobrescribe la variable de entorno CppSDK predeterminada. 

- Establecer la variable de entorno **LOCALRUN_DATAROOT**

    Defina una nueva variable de entorno denominada "LOCALRUN_DATAROOT" que apunte a la raíz de datos. 

    Además de establecer la variable de entorno, también especifica el argumento "-DataRoot" con la ruta de acceso raíz de datos cuando se utiliza la línea de comandos. Este argumento sobrescribe la variable de entorno DataRoot. Y necesita agregar este argumento a cada línea de comandos que esté ejecutando para que pueda utilizar la misma nueva raíz de datos de sobrescritura para todas las operaciones. 

### <a name="using-the-sdk-from-command-line"></a>Uso del SDK desde la línea de comandos

#### <a name="the-command-line-interface-of-the-helper-application"></a>Interfaz de línea la de comandos de la aplicación auxiliar

El archivo LocalRunHelper.exe del SDK es la aplicación auxiliar de línea de comandos que proporciona interfaces a la mayoría de las funciones de ejecución local de uso frecuente. Tenga en cuenta que tanto el comando como los modificadores de argumentos distinguen entre mayúsculas y minúsculas. Para invocarla:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Ejecute "LocalRunHelper.exe" sin argumentos o con el modificador **help** para mostrar la información de ayuda:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

En la información de ayuda: 

-  **Command**  proporciona el nombre del comando.  
-  **Required Argument** enumera los argumentos que se deben proporcionar.  
-  **Optional Argument** enumera los argumentos son opcionales y con los valores predeterminados.  Argumentos opcionales booleanos no tienen parámetros y sus repeticiones significa negativo en sus valores predeterminados.

#### <a name="return-value-and-logging"></a>Valor devuelto y registro

La aplicación auxiliar devuelve **0** en el caso de éxito y **-1** en caso de error. De forma predeterminada, la aplicación auxiliar enviará todos los mensajes a la consola actual.  Sin embargo, la mayoría de los comandos admiten el argumento opcional **-MessageOut ruta_de_acceso_al_archivo_de_registro** que redirigirá las salidas a un archivo de registro.


### <a name="the-sdk-usage-samples"></a>Ejemplos de uso del SDK

#### <a name="compile-and-run"></a>Compilación y ejecución

El comando "run" se utiliza para compilar el script y ejecutar después los resultados compilados. Sus argumentos de línea de comandos son la combinación de "compile" y "run".

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Este es un ejemplo:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Además de combinar "compile" y "run" juntos, puede compilar y ejecutar los ejecutables compilados por separado. 

#### <a name="compile-u-sql-script"></a>Compilación del script U-SQL

El comando "compile" se utiliza para compilar un script U-SQL en los archivos ejecutables. 

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Argumentos opcionales para la compilación: puede encontrar más información sobre el directorio de trabajo (-WorkDir) en el apéndice.

|Argumento|Descripción|
|--------|-----------|
|-CppSDK [valor predeterminado '']|Directorio CppSDK|
|-DataRoot [valor predeterminado '']|DataRoot para datos y metadatos, el valor predeterminado es la variable de entorno 'LOCALRUN_DATAROOT'|
|-MessageOut [valor predeterminado '']|Volcar mensajes de la consola en un archivo|
|-Shallow [valor predeterminado 'False']|Compilación superficial, solamente comprueba la sintaxis del script y regresa.|
|-WorkDir [valor predeterminado 'D:\localrun\t\ScopeWorkDir']|Directorio para el uso del compilador y salidas; obtenga información en la sección Directorio de trabajo del apéndice.|

Argumentos opcionales para ensamblados y código subyacente:

|Argumento|Descripción|
|--------|-----------|
|-CodeBehind [valor predeterminado 'False']|El script tiene código subyacente .cs que se compilará y registrará automáticamente como objeto UDO|
|-References [valor predeterminado '']|Lista de rutas de acceso a los ensamblados de referencia adicionales o archivos de datos de código subyacente, separadas por ';'|
|-UseDatabase [valor predeterminado 'master']|Base de datos que se utilizará para el registro de ensamblados temporal de código subyacente|
|-UdoRedirect [valor predeterminado 'False']|Generar la configuración de redirección de ensamblado Udo que indica el tiempo de ejecución de .NET para sondear ensamblados dependientes desde el directorio de salida compilado en primer lugar cuando se llama a UDO|

Estos son algunos ejemplos de uso:

Compilación del script U-SQL:

    LocalRunHelper compile -Script d:\test\test1.usql

Compile el script U-SQL y establezca la carpeta raíz de datos. Tenga en cuenta que esta acción sobrescribirá la variable de entorno establecida.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Compile el script U-SQL y establezca el directorio de trabajo, el ensamblado de referencia y la base de datos.

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-result"></a>Ejecución del resultado compilado

El comando "execute" se usa para ejecutar resultados compilados.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Argumentos opcionales:

|Argumento|Descripción|
|--------|-----------|
|-DataRoot [valor predeterminado '']|DataRoot para ejecución de metadatos; el valor predeterminado es la variable de entorno 'LOCALRUN_DATAROOT'|
|-MessageOut [valor predeterminado '']|Volcar mensajes de la consola en un archivo|
|-Parallel [valor predeterminado '1']|Realización de los pasos de ejecución local generados con el nivel de paralelismo especificado|
|-Verbose [valor predeterminado 'False']|Mostrar resultados detallados del runtime|

Presentamos un ejemplo de uso:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5

## <a name="using-the-sdk-with-programming-interface"></a>Uso del SDK con la interfaz de programación

La interfaz de programación se encuentra en el ensamblado "Microsoft.Analytics.LocalRun", a través del cual puede integrar la funcionalidad del SDK de U-SQL y el marco de pruebas de C# para escalar la prueba local del script U-SQL (próximamente aparecerá un nuevo documento para esto). Consulte el apéndice para información sobre las interfaces.

## <a name="appendix"></a>Anexo

### <a name="working-directory"></a>Directorio de trabajo

Al ejecutar el script U-SQL localmente, se crea un directorio de trabajo durante la compilación.  Además de las salidas de compilación, también se creará una instantánea de los archivos del runtime necesarios para la ejecución local en este directorio de trabajo.   Si el argumento **- WorkDir** no se especifica en la línea de comandos, el directorio de trabajo predeterminado "ScopeWorkDir" se creará en el directorio actual. Tal y como se muestra a continuación, se muestran los archivos en el directorio de trabajo.

|Directorio o archivo|Definición|Descripción|
|--------------|----------|-----------|
|ScopeWorkDir|Directorio de trabajo|Carpeta raíz|
|C6A101DDCB470506|Cadena de hash de la versión del runtime|Instantánea de los archivos del runtime necesarios para la ejecución local|
|\.\Script_66AE4909AA0ED06C|Nombre de script + cadena de hash de la ruta de acceso del script|Resultados de compilación y registro de los pasos de ejecución|
|\.\.\\_\_script\_\_.abr|Salida del compilador|Archivo de álgebra|
|\.\.\\_\_ScopeCodeGen\_\_.*|Salida del compilador|Código administrado generado|
|\.\.\\_\_ScopeCodeGenEngine\_\_.*|Salida del compilador|Código nativo generado|
|\.\.\referenced_assemblies|Referencia de ensamblado|Archivos de ENSAMBLADO DE REFERENCIA|
|\.\.\deployed_resources|Implementación de recursos|Archivos de IMPLEMENTACIÓN DE RECURSOS|
|\.\.\xxxxxxxx.xxx[1..n]_*.*|Registro de ejecución|Registro de pasos de ejecución|

### <a name="programming-interfaces-for-azure-data-lake-u-sql-sdk"></a>Interfaces de programación para el SDK de U-SQL para Azure Data Lake

La interfaz de programación se encuentra en el ensamblado "Microsoft.Analytics.LocalRun".

#### <a name="microsoftanalyticslocalrunconfiguration"></a>Microsoft.Analytics.LocalRun.Configuration
Clase de parámetro de configuración de compilación

**Constructor**

public Configuration(string rootPath)

|Parámetro|Tipo|Descripción|
|---------|----|-----------|
|rootPath|System.String|Ruta de acceso al directorio actual del contexto de trabajo. Si no se establece WorkingDirectory, el directorio de trabajo predeterminado será rootPath + "ScopeWorkDir".|

**Propiedades**

|Nombre|Descripción|
|----|-----------|
|CppSDK|Dónde encontrar el SDK Cpp; utilizar de forma predeterminada la configuración predeterminada del sistema |
|DataDirectory|Donde se guardan las tablas y los ensamblados y los datos de entrada y salida; de forma predeterminada en ScopeWorkDir\DataDir |
|GenerateUdoRedirect|Si desea generar carga la configuración de invalidación de redirección de carga de ensamblados|
|WorkingDirectory|Directorio de trabajo del compilador; de forma predeterminada, ScopeWorkDir no está establecido|


#### <a name="microsoftanalyticslocalrunlocalcompiler"></a>Microsoft.Analytics.LocalRun.LocalCompiler
La clase de compilador local U-SQL

**Constructor**

public LocalCompiler(Configuration configuration)

|Parámetro|Tipo|Descripción|
|---------|----|-----------|
|configuración|Microsoft.Analytics.LocalRun.Configuration||

**Método**

public bool Compile( string script, string filePath, bool shallow, out CommonCompileResult result )

|Parámetro|Tipo|Descripción|
|---------|----|-----------|
|script|System.String|cadena del script de entrada|
|filePath|System.String|ruta de acceso del archivo de script; establecido en null de forma predeterminada|
|shallow|System.Boolean|Compilación superficial (solamente comprobación de sintaxis) o compilación completa|
|result|Microsoft.Cosmos.ClientTools.Shared.CommonCompileResult|Resultados de compilación detallados|
|Valor devuelto|System.Boolean|true: Ningún error grave en la compilación. <br>false: Error grave en la compilación.|

#### <a name="microsoftanalyticslocalrunlocalrunner--idisposable"></a>Microsoft.Analytics.LocalRun.LocalRunner : IDisposable
Clase del ejecutor local U-SQL

**Constructor**

public LocalRunner( string algebraFilePath, string dataRoot, Action<string> postMessage = null )

|Parámetro|Tipo|Descripción|
|---------|----|-----------|
|algebraFilePath|System.String|Ruta de acceso al archivo de álgebra|
|dataRoot|System.String|Ruta de acceso a DataRoot|
|postMessage (opcional)|System.Action<String>|controlador de registro para el progreso|

public LocalRunner( string algebraFilePath, string dataRoot, string cachePath, string runtimePath, string tempPath, string logPath, Action<Object, ExecutionStatusBase. ExecutionEventArgs> execEventHandler, Object eventContext, Action<string> postMessage = null )

|Parámetro|Tipo|Descripción|
|---------|----|-----------|
|algebraFilePath|System.String|Ruta de acceso al archivo de álgebra|
|dataRoot|System.String|Ruta de acceso a DataRoot|
|cachePath|System.String|Ruta de acceso al directorio de resultados de compilación; establecer en null para usar el valor predeterminado donde se encuentra el archivo de álgebra|
|runtimePath|System.String|Ruta de acceso al directorio del runtime de instantáneas; establecer en null para usar el valor predeterminado donde se encuentra el directorio principal de cachePath|
|tempPath|System.String|Ruta de acceso de almacenamiento temporal; solo uso interno; establecer en null|
|logPath|System.String|Ruta de acceso donde se escribirán los registros de ejecución; establecer en null para usar el valor predeterminado como cachePath|
|execEventHandler|System. Action<Object, ExecutionStatusBase.ExecutionEventArgs>|controlador de notificación de eventos de cambio de estado de ejecución|
|eventContext|System. Objeto|contexto para el controlador de eventos|
|postMessage (opcional)|System.Action<String>|controlador de registro para el progreso|

**Propiedades**

|Nombre|Descripción|
|----|-----------|
|AlgebraPath|Ruta de acceso al archivo de álgebra|
|CachePath|Ruta de acceso de caché de resultados del compilador donde se encuentran los archivos binarios generados|
|CompletedSteps|Número de pasos completados|
|DataRoot|DataRoot para metadatos|
|LastErrorMessage|(Se hereda de ExecutionStatusBase.)|
|LogPath|Ubicación de almacenamiento del archivo de registro. El establecedor creará el directorio si no existía. La ruta de acceso de registro creada no se limpiará|
|OutputHeader|Encabezado de esquema de volcado de memoria en las salidas de texto|
|Paralelismo|Paralelismo. Procesadores predeterminados: 1. El cambio de esto después del inicio provocará una excepción.|
|Progreso|Progreso de la ejecución en una escala de 0 a 100 por cien|
|RuntimePath|Donde se encuentran los archivos de runtime; debe ser un directorio por encima de CachePath cuando es la instantánea por el compilador|
|Estado|Estado de la ejecución <br>enum ExecutionStatusBase.ExecutionStatus <br>{ <br>Initialized, // Inicializada <br>Running,     // Se está ejecutando, WaitOne solo comprueba el evento en este estado <br>Success,     // El trabajo se completó correctamente <br>Error,       // Error <br>}|
|TotalSteps|Número total de pasos para ejecutar; el valor válido solo está disponible después de crear el DAG de vértice|
|Detallado|Detalles durante la ejecución|

**Método**

|Método|Descripción|
|------|-----------|
|Cancel()|Cancelar el álgebra en ejecución <br>Valor devuelto <br>Tipo: Boolean <br>false: no se pudo cancelar debido a un error; compruebe LastErrorMessage para detalles|
|Start()|Empezar a ejecutar el álgebra <br>Valor devuelto <br>Tipo: Boolean <br>false: no se pudo iniciar debido a un error; compruebe LastErrorMessage para detalles|
|WaitOne() <br>WaitOne(Int32) <br>WaitOne(TimeSpan) <br>WaitOne(Int32, Boolean) <br>WaitOne(TimeSpan, Boolean)|Esperar la finalización; consultar WaitHandle.WaitOne|
|Dispose()||


## <a name="see-also"></a>Otras referencias

* Para obtener información general acerca de Análisis de Data Lake, consulte la página de [información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).
* Para empezar a desarrollar aplicaciones con U-SQL, consulte [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para conocer las tareas de administración, consulte [Administración de Azure Data Lake Analytics mediante el Azure Portal](data-lake-analytics-manage-use-portal.md).
* Para registrar información de diagnóstico, consulte [Accessing Diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Para ver una consulta más compleja, consulte la página sobre el [análisis de registros de sitio web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para ver los detalles del trabajo, vea [Usar el explorador de trabajos y la vista de trabajos para trabajos de Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md)
* Para saber cómo usar la vista de ejecución de vértices, vea [Use the Vertex Execution View in Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md) (Usar la vista de ejecución de vértices de Data Lake Tools para Visual Studio).


<!--HONumber=Nov16_HO4-->


