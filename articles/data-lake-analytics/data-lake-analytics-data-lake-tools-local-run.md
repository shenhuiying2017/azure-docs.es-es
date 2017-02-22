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
ms.sourcegitcommit: c43fb7b513136bb3ea964419d68f58585bba48fd
ms.openlocfilehash: 348971e07169ae9c155753c7943124e96e6e9597


---
# <a name="test-and-debug-u-sql-jobs-by-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>Prueba y depuración de trabajos U-SQL mediante la ejecución local y el SDK de U-SQL para Azure Data Lake

Puede usar Azure Data Lake Tools para Visual Studio y el SDK de U-SQL para Azure Data Lake para ejecutar trabajos de U-SQL en la estación de trabajo local, de la misma forma que en el servicio Azure Data Lake. Estas dos características de ejecución local ahorran tiempo para probar y depurar los trabajos de U-SQL.

Requisitos previos:

- Una cuenta de Azure Data Lake Analytics. Consulte [Tutorial: Introducción a Azure Data Lake Analytics mediante Azure Portal](data-lake-analytics-get-started-portal.md).
- Azure Data Lake Tools para Visual Studio. Consulte [Tutorial: Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- La experiencia de desarrollo de script U-SQL. Consulte [Tutorial: Introducción a Azure Data Lake Analytics mediante Azure Portal](data-lake-analytics-get-started-portal.md).


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



## <a name="use-local-run-from-the-data-lake-u-sql-sdk"></a>Uso de la ejecución local del SDK de U-SQL para Data Lake

Además de ejecutar scripts U-SQL localmente mediante Visual Studio, puede usar el SDK de U-SQL para Azure Data Lake para ejecutar scripts U-SQL localmente con la línea de comandos e interfaces de programación. A través de estos elementos, puede escalar la prueba local de U-SQL.

### <a name="install-the-sdk"></a>Instalación del SDK

El SDK de U-SQL para Data Lake requiere las siguientes dependencias:

- [Microsoft .NET Framework 4.6 o superior](https://www.microsoft.com/en-us/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 y Windows SDK 10.0.10240.0 o posterior. Existen dos formas de obtenerlo:

    - Instale [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). Debe tener una carpeta \Windows Kits\10 en la carpeta de archivos de programa, por ejemplo, C:\Archivos de programa (x86)\Windows Kits\10\.. También deberá encontrar la versión del SDK de Windows 10 en \Windows Kits\10\Lib. Si no ve estas carpetas, vuelva a instalar Visual Studio y asegúrese de seleccionar el SDK de Windows 10 durante la instalación. El script del compilador local de U-SQL encontrará estas dependencias automáticamente.

    ![SDK de Windows 10 para ejecución local de Data Lake Tools para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

    - Instale [Data Lake Tools para Visual Studio](http://aka.ms/adltoolsvs). Puede encontrar los archivos preconfigurados del SDK de Windows y Visual C++ en C:\Archivos de programa (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. En este caso, el script del compilador local de U-SQL no podrá encontrar estas dependencias automáticamente. Debe especificar la ruta de acceso de CppSDK para él. Puede copiar los archivos en otra ubicación o simplemente usarla como está. A continuación, puede optar por establecer una variable de entorno **SCOPE_CPP_SDK** en el directorio o especificar el argumento **-CppSDK** con este directorio en la línea de comandos de la aplicación auxiliar de ejecución local.

Después de instalar el SDK, debe realizar los siguientes pasos de configuración:

- Establezca la variable de entorno **SCOPE_CPP_SDK**.

    Si obtiene Microsoft Visual C++ y Windows SDK con la instalación de Data Lake Tools para Visual Studio, compruebe que dispone de la siguiente carpeta:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Defina una nueva variable de entorno denominada **SCOPE_CPP_SDK** para que apunte a este directorio. O copie la carpeta en otra ubicación y especifique **SCOPE_CPP_SDK** como eso.

    Además de establecer la variable de entorno, también puede especificar el argumento **-CppSDK** cuando se utiliza la línea de comandos. Este argumento sobrescribe la variable de entorno CppSDK predeterminada.

- Establezca la variable de entorno **LOCALRUN_DATAROOT**.

    Defina una nueva variable de entorno denominada **LOCALRUN_DATAROOT** que apunte a la raíz de datos.

    Además de establecer la variable de entorno, puede especificar el argumento **-DataRoot** con la ruta de acceso raíz de datos cuando se utiliza la línea de comandos. Este argumento sobrescribe la variable de entorno raíz de datos predeterminada. Necesita agregar este argumento a cada línea de comandos que esté ejecutando para que pueda sobrescribir la variable de entorno raíz de datos predeterminada para todas las operaciones.

### <a name="use-the-sdk-from-the-command-line"></a>Uso del SDK desde la línea de comandos

#### <a name="command-line-interface-of-the-helper-application"></a>Interfaz de la línea de comandos de la aplicación auxiliar

En el SDK, el archivo LocalRunHelper.exe es la aplicación auxiliar de la línea de comandos que proporciona interfaces a la mayoría de las funciones de ejecución local de uso frecuente. Tenga en cuenta que tanto el comando como los modificadores de argumentos distinguen entre mayúsculas y minúsculas. Para invocarla:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Ejecute LocalRunHelper.exe sin argumentos o con el modificador **help** para mostrar la información de ayuda:

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

-  **Command** proporciona el nombre del comando.  
-  **Required Argument** enumera los argumentos que se deben proporcionar.  
-  **Optional Argument** enumera argumentos que son opcionales, con los valores predeterminados.  Los argumentos opcionales booleanos no tienen parámetros y sus repeticiones implican un valor negativo de sus valores predeterminados.

#### <a name="return-value-and-logging"></a>Valor devuelto y registro

La aplicación auxiliar devuelve **0** si se ha realizado correctamente y **-1** en caso contrario. De forma predeterminada, la aplicación auxiliar envía todos los mensajes a la consola actual. Sin embargo, la mayoría de los comandos admiten el argumento opcional **-MessageOut ruta_de_acceso_al_archivo_de_registro** que redirige las salidas a un archivo de registro.


### <a name="sdk-usage-samples"></a>Ejemplos de uso del SDK

#### <a name="compile-and-run"></a>Compilación y ejecución

El comando **run** se utiliza para compilar el script y ejecutar después los resultados compilados. Sus argumentos de línea de comandos son la combinación de **compile** y **run**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Este es un ejemplo:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Además de combinar **compile** y **run** juntos, puede compilar y ejecutar los ejecutables compilados por separado.

#### <a name="compile-a-u-sql-script"></a>Compilación de un script U-SQL

El comando **compile** se utiliza para compilar un script U-SQL en los archivos ejecutables.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

A continuación se muestra los argumentos opcionales para la compilación:

|Argumento|Descripción|
|--------|-----------|
|-CppSDK [valor predeterminado '']|Directorio CppSDK.|
|-DataRoot [valor predeterminado '']|Raíz de datos para datos y metadatos. De manera predeterminada, es la variable de entorno **LOCALRUN_DATAROOT**.|
|-MessageOut [valor predeterminado '']|Volcar mensajes de la consola en un archivo.|
|-Shallow [valor predeterminado 'False']|Compilación superficial. Solamente comprueba la sintaxis del script y regresa.|
|-WorkDir [valor predeterminado 'D:\localrun\t\ScopeWorkDir']|Directorio de uso del compilador y salidas. Para más información, consulte "Directorio de trabajo" en el apéndice.|

Lo siguiente son argumentos opcionales para ensamblados y código subyacente:

|Argumento|Descripción|
|--------|-----------|
|-CodeBehind [valor predeterminado 'False']|Indicador de que el script tiene código subyacente .cs, que se compilará y registrará automáticamente como objeto definido por el usuario (UDO)|
|-References [valor predeterminado '']|Lista de rutas de acceso a los ensamblados de referencia adicionales o archivos de datos de código subyacente, separadas por ';'|
|-UseDatabase [valor predeterminado 'master']|Base de datos que se utilizará para el registro de ensamblados temporal de código subyacente|
|-UdoRedirect [valor predeterminado 'False']|Configuración de redirección de ensamblado UDO que indica el tiempo de ejecución de .NET para sondear ensamblados dependientes desde el directorio de salida compilado en primer lugar cuando se llama a UDO|

Estos son algunos ejemplos de uso.

Compile un script U-SQL:

    LocalRunHelper compile -Script d:\test\test1.usql

Compile un script U-SQL y establezca la carpeta raíz de datos. Tenga en cuenta que esta acción sobrescribirá la variable de entorno del conjunto.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Compile un script U-SQL y establezca el directorio de trabajo, el ensamblado de referencia y la base de datos:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Ejecución de resultados compilados

El comando **execute** se usa para ejecutar resultados compilados.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

A continuación se muestran los argumentos opcionales:

|Argumento|Descripción|
|--------|-----------|
|-DataRoot [valor predeterminado '']|Raíz de datos para la ejecución de metadatos. De manera predeterminada, es la variable de entorno **LOCALRUN_DATAROOT**.|
|-MessageOut [valor predeterminado '']|Volcar mensajes de la consola en un archivo.|
|-Parallel [valor predeterminado '1']|Indicador para ejecutar los pasos de ejecución local generados con el nivel de paralelismo especificado.|
|-Verbose [valor predeterminado 'False']|Indicador para mostrar las salidas detalladas del tiempo de ejecución.|

Presentamos un ejemplo de uso:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5

## <a name="use-the-sdk-with-programming-interfaces"></a>Uso del SDK con las interfaces de programación

Las interfaces de programación se encuentran en el ensamblado Microsoft.Analytics.LocalRun. Puede utilizarlas para integrar la funcionalidad del SDK de U-SQL y la plataforma de pruebas de C# para escalar la prueba local del script U-SQL. Consulte el apéndice para más información sobre las interfaces.

## <a name="appendix"></a>Anexo

### <a name="working-directory"></a>Directorio de trabajo

Al ejecutar localmente el script U-SQL, se crea un directorio de trabajo durante la compilación. Además de las salidas de compilación, se creará una instantánea de los archivos del runtime necesarios para la ejecución local en este directorio de trabajo. Si el argumento **-WorkDir** no se especifica en la línea de comandos, el directorio de trabajo predeterminado ScopeWorkDir se creará en el directorio actual. Los archivos en el directorio de trabajo son los siguientes:

|Directorio o archivo|Definición|Descripción|
|--------------|----------|-----------|
|ScopeWorkDir|Directorio de trabajo|Carpeta raíz|
|C6A101DDCB470506|Cadena de hash de la versión del runtime|Instantánea de los archivos del runtime necesarios para la ejecución local|
|\.\Script_66AE4909AA0ED06C|Nombre de script más cadena de hash de la ruta de acceso del script|Resultados de compilación y registro de los pasos de ejecución|
|\.\.\\_\_script\_\_.abr|Salida del compilador|Archivo de álgebra|
|\.\.\\_\_ScopeCodeGen\_\_.*|Salida del compilador|Código administrado generado|
|\.\.\\_\_ScopeCodeGenEngine\_\_.*|Salida del compilador|Código nativo generado|
|\.\.\referenced_assemblies|Referencia de ensamblado|Archivos de ensamblado de referencia|
|\.\.\deployed_resources|Implementación de recursos|Archivos de implementación de recursos|
|\.\.\xxxxxxxx.xxx[1..n]_*.*|Registro de ejecución|Registro de pasos de ejecución|

### <a name="programming-interfaces-for-the-azure-data-lake-u-sql-sdk"></a>Interfaces de programación para el SDK de U-SQL para Azure Data Lake

Las interfaces de programación se encuentran en el ensamblado Microsoft.Analytics.LocalRun.

#### <a name="microsoftanalyticslocalrunconfiguration"></a>Microsoft.Analytics.LocalRun.Configuration
Microsoft.Analytics.LocalRun.Configuration es la clase del parámetro de configuración de compilación.

**Constructor**

public Configuration(string rootPath)

|Parámetro|Tipo|Descripción|
|---------|----|-----------|
|rootPath|System.String|Ruta de acceso al directorio actual del contexto de trabajo. Si no se establece WorkingDirectory, el directorio de trabajo predeterminado es rootPath más ScopeWorkDir.|

**Propiedades**

|Nombre|Descripción|
|----|-----------|
|CppSDK|Ubicación de CppSDK, si no es la configuración predeterminada del sistema. |
|DataDirectory|Ubicación donde se guardan las tablas, los ensamblados y los datos de entrada/salida. El valor predeterminado es ScopeWorkDir\DataDir. |
|GenerateUdoRedirect|Indicador de si desea generar la configuración de invalidación de redirección de carga de ensamblados.|
|WorkingDirectory|Directorio de trabajo del compilador. El valor predeterminado es ScopeWorkDir.|


#### <a name="microsoftanalyticslocalrunlocalcompiler"></a>Microsoft.Analytics.LocalRun.LocalCompiler
Microsoft.Analytics.LocalRun.LocalCompiler es la clase del compilador local de U-SQL.

**Constructor**

public LocalCompiler(Configuration configuration)

|Parámetro|Tipo|Descripción|
|---------|----|-----------|
|configuración|Microsoft.Analytics.LocalRun.Configuration||

**Método**

public bool Compile( string script, string filePath, bool shallow, out CommonCompileResult result )

|Parámetro|Tipo|Descripción|
|---------|----|-----------|
|script|System.String|Cadena del script de entrada.|
|filePath|System.String|Ruta de acceso del archivo de script. Se establece en un valor nulo para usar el valor predeterminado.|
|shallow|System.Boolean|Compilación superficial (solamente comprobación de sintaxis) o compilación completa.|
|result|Microsoft.Cosmos.ClientTools.Shared.CommonCompileResult|Resultados de compilación detallados.|
|Valor devuelto|System.Boolean|true: ningún error grave en la compilación. <br>false: error grave en la compilación.|

#### <a name="microsoftanalyticslocalrunlocalrunner--idisposable"></a>Microsoft.Analytics.LocalRun.LocalRunner : IDisposable
Microsoft.Analytics.LocalRun.LocalRunner : IDisposable es la clase del ejecutor local de U-SQL.

**Constructor**

public LocalRunner( string algebraFilePath, string dataRoot, Action<string> postMessage = null )

|Parámetro|Tipo|Descripción|
|---------|----|-----------|
|algebraFilePath|System.String|Ruta de acceso al archivo de álgebra|
|dataRoot|System.String|Ruta de acceso a DataRoot|
|postMessage (opcional)|System.Action<String>|Controlador de registro para el progreso|

public LocalRunner( string algebraFilePath, string dataRoot, string cachePath, string runtimePath, string tempPath, string logPath, Action<Object, ExecutionStatusBase. ExecutionEventArgs> execEventHandler, Object eventContext, Action<string> postMessage = null )

|Parámetro|Tipo|Descripción|
|---------|----|-----------|
|algebraFilePath|System.String|Ruta de acceso al archivo de álgebra.|
|dataRoot|System.String|Ruta de acceso a DataRoot.|
|cachePath|System.String|Ruta al directorio del resultado de la compilación. Se establece en un valor nulo para usar el valor predeterminado, donde se ubica el archivo de álgebra.|
|runtimePath|System.String|Ruta de acceso al directorio del sistema en tiempo de ejecución con instantáneas. Se establece en null para usar el valor predeterminado, donde se encuentra el directorio principal de cachePath.|
|tempPath|System.String|Ruta de acceso de almacenamiento temporal, solo para uso interno. Se establece en null.|
|logPath|System.String|Ruta de acceso en donde se escribirán los registros de ejecución. Se establece en null para usar el valor de cachePath como valor predeterminado.|
|execEventHandler|System. Action<Object, ExecutionStatusBase.ExecutionEventArgs>|Controlador de notificación de eventos de cambio de estado de ejecución.|
|eventContext|System. Objeto|Contexto para el controlador de eventos.|
|postMessage (opcional)|System.Action<String>|Controlador de registro para el progreso.|

**Propiedades**

|Nombre|Descripción|
|----|-----------|
|AlgebraPath|Ruta de acceso al archivo de álgebra.|
|CachePath|Ruta de acceso de caché de resultados del compilador donde se encuentran los archivos binarios generados.|
|CompletedSteps|Número de pasos completados.|
|DataRoot|DataRoot para metadatos.|
|LastErrorMessage|(Se hereda de ExecutionStatusBase.)|
|LogPath|Ubicación de almacenamiento de archivos de registro. El establecedor creará el directorio si no existe. La ruta de acceso de registro creada anteriormente no se limpiará.|
|OutputHeader|Encabezado de esquema de volcado de memoria en las salidas de texto.|
|Paralelismo|Paralelismo. Valor predeterminado en procesadores de lógica: 1. Si se cambia después del inicio, se producirá una excepción.|
|Progreso|Progreso de la ejecución en una escala de 0 a 100 por cien.|
|RuntimePath|Donde se encuentran los archivos de runtime. Debe ser un directorio por encima de CachePath cuando es la instantánea del compilador.|
|Estado|Estado de la ejecución. <br><br>enum ExecutionStatusBase.ExecutionStatus <br>{ <br>Initialized, // Inicializada. <br>Running,     // En ejecución.  WaitOne solo comprueba el evento en este estado. <br>Success,     // El trabajo se completó correctamente. <br>Error,       // Error. <br>}|
|TotalSteps|Número total de pasos para ejecutar. El valor válido solo está disponible después de crear el DAG de vértice.|
|Detallado|Detalles durante la ejecución.|

**Método**

|Método|Descripción|
|------|-----------|
|Cancel()|Cancelar el álgebra en ejecución. <br><br>El tipo de valor devuelto es un valor booleano. <br><br>Valor de devolución de false: no se pudo cancelar debido a un error; compruebe LastErrorMessage para detalles.|
|Start()|Empezar a ejecutar el álgebra. <br><br>El tipo de valor devuelto es un valor booleano. <br><br>Valor devuelto de false: no se pudo iniciar debido a un error; compruebe LastErrorMessage para detalles.|
|WaitOne() <br>WaitOne(Int32) <br>WaitOne(TimeSpan) <br>WaitOne(Int32, Boolean) <br>WaitOne(TimeSpan, Boolean)|Esperar a que finalicen. Consulte WaitHandle.WaitOne.|
|Dispose()||


## <a name="next-steps"></a>Pasos siguientes

* Para obtener información general sobre Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).
* Para empezar a desarrollar aplicaciones con U-SQL, consulte [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para conocer las tareas de administración, consulte [Administración de Azure Data Lake Analytics mediante el Azure Portal](data-lake-analytics-manage-use-portal.md).
* Para registrar información de diagnóstico, consulte [Acceso a los registros de diagnóstico de Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Para ver una consulta más compleja, consulte [Tutorial: Análisis de registros de sitios web mediante Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Para ver los detalles del trabajo, consulte [Usar el explorador de trabajos y la vista de trabajos para trabajos de Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Para usar la vista de ejecución de vértices, vea [Uso de la vista de ejecución de vértices de Data Lake Tools para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).



<!--HONumber=Dec16_HO1-->


