---
title: 'Herramientas de Azure Data Lake: Uso de Herramientas de Azure Data Lake para Visual Studio Code | Microsoft Docs'
description: "Obtenga información sobre cómo usar Herramientas de Azure Data Lake para Visual Studio Code para crear, probar y ejecutar scripts U-SQL. "
Keywords: "VSCode, Herramientas de Azure Data Lake, ejecución local, depuración local, archivo de almacenamiento de versión preliminar, cargar en la ruta de acceso de almacenamiento, descargar, cargar"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/10/2017
ms.author: jejiang
ms.openlocfilehash: e724a8db4424a1e608ae7ee5625cd4cc16f6078f
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Uso de Herramientas de Azure Data Lake para Visual Studio Code

Obtenga información sobre cómo usar Herramientas de Azure Data Lake para Visual Studio Code (VS Code) para crear, probar y ejecutar scripts U-SQL. La información también se explica en el vídeo siguiente:

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Requisitos previos

Las Herramientas de Azure Data Lake para Visual Studio son compatibles con Windows, Linux y MacOS.  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).

En MacOS y Linux:
- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core). 
- [Mono 5.2.x](http://www.mono-project.com/download/).

## <a name="install-data-lake-tools"></a>Instalación de Herramientas de Data Lake

Después de instalar los requisitos previos, puede instalar Herramientas de Data Lake para VS Code.

**Para instalar Herramientas de Data Lake**

1. Abra Visual Studio Code.
2. Haga clic en **Extensiones** en el panel izquierdo. Escriba **Azure Data Lake** en el cuadro de búsqueda.
3. Haga clic en **Instalar** junto a **Herramientas de Azure Data Lake**. Después de unos segundos, el botón **Instalar** cambiará a **Recargar**.
4. Haga clic en **Recargar** para activar la extensión **Herramientas de Azure Data Lake**.
5. Haga clic en **Recargar ventana** para confirmar. Puede ver **Herramientas de Azure Data Lake** en el panel Extensiones.

    ![Panel Extensiones de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

 
## <a name="activate-azure-data-lake-tools"></a>Activación de Azure Data Lake Tools
Cree un archivo .usql o abra uno ya existente para activar la extensión. 

## <a name="open-the-sample-script"></a>Apertura del script de ejemplo
Abra la paleta de comandos (Ctrl+Mayús+P) y escriba **ADL: Open Sample Script**. Se abre otra instancia de este ejemplo. En esta instancia también puede editar, configurar y enviar scripts.

## <a name="work-with-u-sql"></a>Trabajo con U-SQL

Se necesita abrir un archivo U-SQL o una carpeta para que funcione con U-SQL.

**Para abrir una carpeta para el proyecto U-SQL**

1. En Visual Studio Code, seleccione el menú **Archivo** y, luego, seleccione **Abrir carpeta**.
2. Especifique una carpeta y, luego, seleccione **Seleccionar carpeta**.
3. Seleccione el menú **Archivo** y, luego, seleccione **Nuevo**. Se agregará un archivo Sin título-1 al proyecto.
4. Escriba el código siguiente en el archivo Sin título-1:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    El script crea un archivo departments.csv con algunos datos incluidos en la carpeta /output.

5. Guarde el archivo como **myUSQL.usql** en la carpeta abierta. Un archivo de configuración adltools_settings.json también se agrega al proyecto.
4. Abra y configure adltools_settings.json con las propiedades siguientes:

    - Cuenta: Cuenta de Data Lake Analytics de su suscripción de Azure.
    - Base de datos: Una base de datos en su cuenta. El valor predeterminado es **master**.
    - Esquema: Un esquema en la base de datos. El valor predeterminado es **dbo**.
    - Configuración opcional:
        - Prioridad: El intervalo de prioridades va de 1 a 1000, siendo 1 la prioridad más alta. El valor predeterminado es **1000**.
        - Paralelismo: El intervalo de paralelismo va de 1 a 150. El valor predeterminado es el paralelismo máximo que se permite en su cuenta de Azure Data Lake Analytics. 
        
        > [!NOTE] 
        > Si la configuración no es válida, se utilizan los valores predeterminados.

        ![Archivo de configuración de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

        Se necesita una cuenta de Data Lake Analytics para compilar y ejecutar trabajos de U-SQL. Debe configurar la cuenta de equipo antes de poder compilar y ejecutar trabajos de U-SQL.
    
        Una vez que se guarda la configuración, la información de cuenta, base de datos y esquema aparece en la barra de estado en la esquina inferior izquierda del archivo .usql correspondiente. 
 
 
En comparación con abrir un archivo, cuando abre una carpeta puede hacer lo siguiente:

- Usar un archivo de código subyacente. En el modo de archivo único, no se admite el código subyacente.
- Usar un archivo de configuración. Cuando se abre una carpeta, los scripts de la carpeta de trabajo compartan un solo archivo de configuración.


El script U-SQL se compila de forma remota mediante el servicio Data Lake Analytics. Cuando se emite el comando **compile**, el script U-SQL se envía a su cuenta de Data Lake Analytics. Luego, Visual Studio Code recibe el resultado de la compilación. Debido a la compilación remota, Visual Studio Code requiere que se muestre la información para conectarse a su cuenta de Data Lake Analytics en el archivo de configuración.

**Para compilar un script U-SQL**

1. Seleccione Ctrl+Mayús+P para abrir la paleta de comandos. 
2. Escriba **ADL: Compile Script**. Los resultados de la compilación aparecen en la ventana **Salida**. También puede hacer clic con el botón derecho en un archivo de script y, luego, seleccione **ADL: Compile Script** para compilar un trabajo U-SQL. El resultado de la compilación aparece en el panel **Salida**.
 

**Para enviar un script de U-SQL**

1. Seleccione Ctrl+Mayús+P para abrir la paleta de comandos. 
2. Escriba **ADL: Submit Job**.  También puede hacer clic con el botón derecho en un archivo de script y, luego, seleccionar **ADL: Submit Job**. 

Después de enviar un trabajo de U-SQL, los registros de envío aparecen en la ventana **Salida** de VS Code. Si el envío se realiza correctamente, la dirección URL del trabajo también se muestra. Puede abrir la dirección URL del trabajo en un explorador web para realizar el seguimiento de estado del trabajo en tiempo real.

Para habilitar la salida de los detalles del trabajo, establezca **jobInformationOutputPath** en el archivo **vscode for u-sql_settings.json**.
 
## <a name="use-a-code-behind-file"></a>Uso de un archivo de código subyacente

Un archivo de código subyacente es un archivo C# asociado con un solo script U-SQL. Puede definir un script dedicado a UDO, UDA, UDT o UDF en el archivo de código subyacente. UDF, UDO, UDA o UDT se puede usar directamente en el script sin tener que registrar primero el ensamblado. El archivo de código subyacente se coloca en la misma carpeta que su archivo de script U-SQL de emparejamiento. Si el script se denomina xxx.usql, el código subyacente se denomina xxx.usql.cs. Si elimina manualmente el archivo de código subyacente, la característica de código subyacente se deshabilita para su script U-SQL asociado. Para más información sobre cómo escribir código de cliente para el script U-SQL, consulte [Writing and Using Custom Code in U-SQL – User-Defined Functions]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/) (Escritura y uso de código personalizado en U-SQL: funciones definidas por el usuario).

Para admitir el código subyacente, debe abrir una carpeta de trabajo. 

**Para generar un archivo de código subyacente**

1. Abra un archivo de origen. 
2. Seleccione Ctrl+Mayús+P para abrir la paleta de comandos.
3. Escriba **ADL: Generate Code Behind**. Se crea un archivo de código subyacente en la misma carpeta. 

También puede hacer clic con el botón derecho en un archivo de script y, luego, seleccionar **ADL: Generate Code Behind**. 

Para compilar y enviar un script U-SQL con un archivo de código subyacente es lo mismo que con el archivo de script U-SQL independiente.

Las dos capturas de pantalla siguientes muestran un archivo de código subyacente y su archivo de script U-SQL asociado:
 
![Herramientas de Data Lake para Visual Studio Code: código subyacente](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![Herramientas de Data Lake para Visual Studio Code: archivo de script de código subyacente](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

Para instrucciones sobre la ejecución y la depuración locales, consulte [Ejecución y depuración locales de U-SQL con Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="use-assemblies"></a>Uso de ensamblados

Para información sobre el desarrollo de ensamblados, consulte [Desarrollo de ensamblados U-SQL para trabajos de Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).

Puede usar Herramientas de Data Lake para registrar ensamblados de código personalizados en el catálogo de Data Lake Analytics.

**Para registrar un ensamblado**

Se puede registrar el ensamblado a través de los comandos **ADL: Register Assembly** o **ADL: Register Assembly through Configuration**.

**Para registrar a través del comando ADL: Register Assembly**
1.  Seleccione Ctrl+Mayús+P para abrir la paleta de comandos.
2.  Escriba **ADL: Register Assembly**. 
3.  Especifique la ruta de acceso de ensamblado local. 
4.  Seleccione una cuenta de Data Lake Analytics.
5.  Seleccione una base de datos.

Resultados: El portal se abre en un explorador y muestra el proceso de registro del ensamblado.  

Otra manera cómoda de desencadenar el comando **ADL: Register Assembly** es hacer clic con el botón derecho en el archivo .dll en el Explorador de archivos. 

**Para registrar a través del comando ADL: Register Assembly through Configuration**
1.  Seleccione Ctrl+Mayús+P para abrir la paleta de comandos.
2.  Escriba **ADL: Register Assembly through Configuration**. 
3.  Especifique la ruta de acceso de ensamblado local. 
4.  Se muestra el archivo JSON. Revise y edite las dependencias de ensamblado y los parámetros de recurso si es necesario. Las instrucciones se muestran en la ventana **Salida**. Para continuar con el registro del ensamblado, guarde (Ctrl+S) el archivo JSON.

![Herramientas de Data Lake para Visual Studio Code: código subyacente](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- Dependencias de ensamblado: Herramientas de Azure Data Lake detecta automáticamente si el archivo DLL tiene alguna dependencia. Las dependencias se muestran en el archivo JSON una vez que se detectan. 
>- Recursos: puede cargar los recursos DLL (por ejemplo, .txt, .png y .csv) como parte del registro del ensamblado. 

Otra manera de desencadenar el comando **ADL: Register Assembly through Configuration** es hacer clic con el botón derecho en el archivo .dll en el Explorador de archivos. 

El siguiente código de U-SQL muestra cómo llamar a un ensamblado. En el ejemplo, el nombre del ensamblado es *test*.

```
REFERENCE ASSEMBLY [test];

@a = 
    EXTRACT 
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string 
    FROM @"Sample/SearchLog.txt" 
    USING Extractors.Tsv();

@d =
    SELECT DISTINCT Region 
    FROM @a;

@d1 = 
    PROCESS @d
    PRODUCE 
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();

OUTPUT @d1 
    TO @"Sample/SearchLogtest.txt" 
    USING Outputters.Tsv();
```

## <a name="connect-to-azure"></a>Conexión a Azure

Para poder compilar y ejecutar scripts de U-SQL en Data Lake Analytics, debe conectarse a su cuenta de Azure.

**Para conectarse a Azure**

1.  Seleccione Ctrl+Mayús+P para abrir la paleta de comandos. 
2.  Escriba **ADL: Login**. La información de inicio de sesión aparece en el panel **Salida**.

    ![Herramientas de Data Lake para Visual Studio Code: paleta de comandos](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Herramientas de Data Lake para Visual Studio Code: información de inicio de sesión del dispositivo](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. Seleccione Ctrl y haga clic en la dirección URL de inicio de sesión: https://aka.ms/devicelogin para abrir la página web de inicio de sesión. Escriba el código **G567LX42V** en el cuadro de texto y, luego, seleccione **Continuar**.

   ![Código de inicio de sesión pegado de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Siga las instrucciones para iniciar sesión desde la página web. Cuando se conecte, el nombre de la cuenta de Azure aparecerá en la barra de estado que se encuentra en la esquina inferior izquierda de la ventana **VS Code**. 

    > [!NOTE] 
    > Si la cuenta tiene habilitada la autenticación de dos pasos, se recomienda usar la autenticación por teléfono en lugar de usar un PIN.

Para cerrar la sesión, escriba el comando **ADL: Logout**.

## <a name="list-your-data-lake-analytics-accounts"></a>Enumeración de las cuentas de Data Lake Analytics

Para probar la conexión, obtenga una lista de las cuentas de Data Lake Analytics.

**Para enumerar las cuentas de Data Lake Analytics en su suscripción de Azure**

1. Seleccione Ctrl+Mayús+P para abrir la paleta de comandos.
2. Escriba **ADL: List Accounts**. Las cuentas aparecen en el panel **Salida**.


## <a name="access-the-data-lake-analytics-catalog"></a>Acceso al catálogo de Data Lake Analytics

Después de haberse conectado a Azure, puede usar los siguientes pasos para acceder al catálogo de U-SQL.

**Para acceder a los metadatos de Azure Data Lake Analytics**

1.  Seleccione Ctrl+Mayús+P y, luego, escriba **ADL: List Tables**.
2.  Seleccione una de las cuentas de Data Lake Analytics.
3.  Seleccione una de las bases de datos de Data Lake Analytics.
4.  Seleccione uno de los esquemas. Puede ver la lista de tablas.

## <a name="view-data-lake-analytics-jobs"></a>Vista de los trabajos de Data Lake Analytics

**Para ver los trabajos de Data Lake Analytics**
1.  Abra la paleta de comandos (Ctrl+Mayús+P) y seleccione **ADL: Show Job**. 
2.  Seleccione una cuenta de Data Lake Analytics o una cuenta local. 
3.  Espere a que se muestre la lista de trabajos de la cuenta.
4.  Seleccione un trabajo de la lista de trabajos, Herramientas de Data Lake abre los detalles del trabajo en Azure Portal y muestra el archivo JobInfo en VS Code.

    ![Tipos de objetos de IntelliSense de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-integration"></a>Integración de Azure Data Lake Storage

Puede usar comandos relacionados con Azure Data Lake Storage para:
 - Examinar los recursos de Azure Data Lake Storage. [Enumeración de la ruta de acceso de almacenamiento](#list-the-storage-path). 
 - Obtener una vista previa del archivo de Azure Data Lake Storage. [Vista previa del archivo de almacenamiento](#preview-the-storage-file). 
 - Cargar el archivo directamente a Azure Data Lake Storage en VS Code. [Carga de archivo](#upload-file).
 - Descargar el archivo directamente de Azure Data Lake Storage en VS Code. [Descarga de archivo](#download-file).

## <a name="list-the-storage-path"></a>Enumeración de la ruta de acceso de almacenamiento 

**Para enumerar la ruta de acceso de almacenamiento a través de la paleta de comandos**

Haga clic con el botón derecho en el editor de scripts y seleccione **ADL: List Storage Path**.

Elija la carpeta en la lista o haga clic en **Indicar ruta de acceso** o **Browse from Root** (Examinar desde raíz); use Indicar ruta de acceso como ejemplo. -> Seleccione **Cuenta de ADLA**. -> Navegue hasta la ruta de acceso a la carpeta de almacenamiento o escríbala; por ejemplo: /salida/). -> En la paleta de comandos se muestra la información de la ruta de acceso en función de las entradas.

![Herramientas de Data Lake para Visual Studio Code: enumerar los resultados de la ruta de acceso de almacenamiento](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Una manera más cómoda de mostrar la ruta de acceso relativa es mediante el menú contextual.

**Para enumerar la ruta de acceso de almacenamiento a través del botón derecho**

Haga clic con el botón derecho en la cadena de la ruta de acceso para seleccionar **List Storage Path** (Enumerar ruta de acceso de almacenamiento) para continuar.

![Herramientas de Data Lake para Visual Studio Code: menú contextual](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


## <a name="preview-the-storage-file"></a>Vista previa del archivo de almacenamiento

Haga clic con el botón derecho en el editor de scripts y seleccione **ADL: Preview Storage File**.

Seleccione **Cuenta de ADLA**. -> Escriba una ruta de acceso del archivo de almacenamiento de Azure (por ejemplo, /salida/RegistroBúsqueda.txt). -> Resultado: el archivo se abre en VSCode.

   ![Herramientas de Data Lake para Visual Studio Code: resultado de la vista previa de archivo](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Otra manera de previsualizar el archivo de almacenamiento es a través del menú contextual en la ruta de acceso completa o relativa del archivo en el editor de scripts. 

## <a name="upload-file"></a>Carga de archivo 

Puede cargar archivos a través de los comandos **ADL: Upload File** o **ADL: Upload File through Configuration**.

**Para cargar archivos a través del comando ADL: Upload File through Configuration**
1.  Haga clic con el botón derecho en el editor de scripts y después seleccione **Upload File through Configuration** (Cargar archivo a través de configuración).
2.  VS Code muestra un archivo JSON. Puede especificar las rutas de acceso de archivo y cargar varios archivos al mismo tiempo. Las instrucciones se muestran en la ventana **Salida**. Para continuar con la carga del archivo, guarde (Ctrl+S) el archivo JSON.

       ![Herramientas de Data Lake para Visual Studio Code: ruta de acceso de archivo](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-file.png)

3.  Resultados: la ventana **Salida** muestra el estado de carga del archivo.

       ![Herramientas de Data Lake para Visual Studio Code: estado de carga](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)     

Al mismo tiempo, puede supervisar el [estado de la carga](#check-storage-tasks-status).

**Para cargar archivos a través del comando ADL: Upload File**

Haga clic con el botón derecho en el editor de scripts y seleccione **Upload File** (Cargar archivo).

Introduzca la **ruta de acceso del archivo local**. -> Elija la carpeta en la lista o haga clic en **Indicar ruta de acceso** o **Browse from Root** (Examinar desde raíz); use Indicar ruta de acceso como ejemplo. -> Seleccione **Cuenta de ADLA**. -> Navegue hasta la ruta de acceso a la carpeta de almacenamiento o escríbala; por ejemplo: /salida/). -> Haga clic en **Choose Current Folder** (Elegir la carpeta actual) para especificar el destino de la carga.

![Herramientas de Data Lake para Visual Studio Code: estado de carga](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


Otra manera de cargar los archivos en el almacenamiento es a través del menú contextual en la ruta de acceso completa o relativa del archivo en el editor de scripts.

Al mismo tiempo, puede supervisar el [estado de la carga](#check-storage-tasks-status).

## <a name="download-file"></a>Descarga de archivo 
Puede descargar archivos mediante los comandos **ADL: Download Storage File** o **ADL: Download Storage File through Configuration**.

**Para descargar archivos a través del comando ADL: Download File through Configuration**
1. Haga clic con el botón derecho en el editor de scripts y después seleccione **Download Storage File through Configuration**.
2. VS Code muestra un archivo JSON. Puede especificar las rutas de acceso de archivo y descargar varios archivos al mismo tiempo. Las instrucciones se muestran en la ventana **Salida**. Para continuar con la descarga del archivo, guarde (Ctrl+S) el archivo JSON.

    ![Descargar archivos a través de configuración con Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  Resultados: la ventana **Salida** muestra el estado de carga del archivo.

    ![Resultados de la descarga de varios archivos de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Al mismo tiempo, puede supervisar el [estado de la descarga](#check-storage-tasks-status).

**Para descargar archivos a través del comando ADL: Download Storage File**

Haga clic con el botón derecho en el editor de scripts y después seleccione **Download Storage File**.

Elija la carpeta en la lista o haga clic en **Indicar ruta de acceso** o **Browse from Root** (Examinar desde raíz); use Indicar ruta de acceso como ejemplo. -> Seleccione **Cuenta de ADLA**. -> Navegue hasta la ruta de acceso a la carpeta de almacenamiento o escríbala; por ejemplo: /salida/) -> Elija el archivo que desea descargar.

   ![Estado de la descarga de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   En la imagen del resultado, el archivo se guarda como la carpeta temporal. Puede definir una ruta de acceso de descarga predeterminada para el parámetro **usql.defaultLocalFolderForDownload** con el menú de VSCode **Archivo** -> **Preferencias** -> **Configuración**.

Otra manera de descargar los archivos de almacenamiento es a través del menú contextual en la ruta de acceso completa o relativa del archivo en el editor de scripts.

Al mismo tiempo, puede supervisar el [estado de la descarga](#check-storage-tasks-status).

## <a name="check-storage-tasks-status"></a>Comprobación del estado de las tareas de almacenamiento
El estado se muestra en la parte inferior de la barra de estado cuando se completa la carga y descarga.
1. Haga clic en la barra de estado siguiente y después se muestra el estado de descarga y carga en el panel de **SALIDA**.

   ![Comprobación del estado de almacenamiento de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="open-azure-storage-explorer"></a>Apertura del Explorador de Azure Storage
Puede abrir **Explorador de Azure Storage** si escribe el comando **ADL: Open Web Azure Storage Explorer** o si lo selecciona en el menú contextual con un clic con el botón derecho.

**Para abrir Explorador de Azure Storage**

1. Seleccione Ctrl+Mayús+P para abrir la paleta de comandos.
2. Escriba **Abrir el explorador web de Azure Storage** o haga clic con el botón derecho en una ruta de acceso relativa o completa en el editor de scripts y, luego, seleccione **Abrir el explorador web de Azure Storage**.
3. Seleccione una cuenta de Data Lake Analytics.

Herramientas de Data Lake abre la ruta de acceso de almacenamiento de Azure en Azure Portal. Puede encontrar la ruta de acceso y obtener la vista previa del archivo desde la Web.

## <a name="local-run-and-local-debug-for-windows-users"></a>Ejecución y depuración locales de usuarios de Windows
La ejecución local de U-SQL prueba los datos locales y valida el script de manera local antes de publicar el código en Data Lake Analytics. La característica de depuración local le permite completar las tareas siguientes antes de enviar el código a Data Lake Analytics: 
- Depure el código subyacente de C#. 
- Recorra el código. 
- Valide localmente el script.

Para instrucciones sobre la ejecución y la depuración locales, consulte [Ejecución y depuración locales de U-SQL con Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="additional-features"></a>Características adicionales

Herramientas de Data Lake para VS Code es compatible con las características siguientes:

-   Autocompletar de IntelliSense: aparecen sugerencias en ventanas emergentes en torno a elementos, como palabras clave, métodos y variables. Los distintos iconos representan diferentes tipos de los objetos:

    - Tipo de datos de escala
    - Tipo de datos complejo
    - UDT integrada
    - Clases y colección .NET
    - Expresiones de C#
    - UDF, UDO y UDAAG integrados de C# 
    - Funciones de U-SQL
    - Función de ventana de U-SQL
 
    ![Tipos de objetos de IntelliSense de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Autocompletar de IntelliSense en metadatos de Data Lake Analytics: Herramientas de Data Lake descarga localmente la información de metadatos de Data Lake Analytics. La característica IntelliSense rellena automáticamente los objetos, lo que incluye base de datos, esquema, tabla, vista, función con valores de tabla, procedimientos y ensamblados de C#, desde los metadatos de Data Lake Analytics.
 
    ![Metadatos de IntelliSense de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   Marcador de error de IntelliSense: Herramientas de Data Lake subraya los errores de edición para U-SQL y C#. 
-   Aspectos destacados de la sintaxis: Herramientas de Data Lake usa colores distintos para diferenciar elementos como variables, palabras clave, tipo de datos y funciones. 

    ![Aspectos destacados de la sintaxis de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Pasos siguientes

- Para la ejecución y depuración locales de U-SQL con Visual Studio Code, consulte [Ejecución y depuración locales de U-SQL con Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).
- Para información detallada sobre cómo empezar a trabajar con Data Lake Analytics, consulte [Tutorial: Introducción a Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- Para información sobre Herramientas de Data Lake para Visual Studio, consulte [Tutorial: Desarrollo de scripts U-SQL mediante Herramientas de Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para información sobre el desarrollo de ensamblados, consulte [Desarrollo de ensamblados U-SQL para trabajos de Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).



