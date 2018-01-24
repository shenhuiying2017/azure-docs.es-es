---
title: 'Herramientas de Azure Data Lake: Uso de Herramientas de Azure Data Lake para Visual Studio Code | Microsoft Docs'
description: "Obtenga información sobre cómo usar Herramientas de Azure Data Lake para Visual Studio Code para crear, probar y ejecutar scripts U-SQL. "
Keywords: VScode,Azure Data Lake Tools,Local run,Local debug,Local Debug,preview file,upload to storage path,download,upload
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
ms.date: 11/10/2017
ms.author: jejiang
ms.openlocfilehash: c70cfc309fe60f0641c89b4a341e3364af74771a
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Uso de Herramientas de Azure Data Lake para Visual Studio Code

Obtenga información sobre cómo usar Herramientas de Azure Data Lake para Visual Studio Code (VS Code) para crear, probar y ejecutar scripts U-SQL. La información también se explica en el vídeo siguiente:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>requisitos previos

Herramientas de Azure Data Lake para VSCode es compatible con Windows, Linux y MacOS.  

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

5. Guarde el archivo como **myUSQL.usql** en la carpeta abierta. Un archivo de configuración xxx_settings.json también se agrega a la carpeta.
6. Abra y configure xxx_settings.json con las propiedades siguientes:

    - Cuenta: una cuenta de Data Lake Analytics en su suscripción de Azure que se necesita para compilar y ejecutar trabajos U-SQL, por lo que debe configurar la cuenta de equipo antes de compilar y ejecutar trabajos U-SQL.
    - Base de datos: Una base de datos en su cuenta. El valor predeterminado es **master**.
    - Esquema: Un esquema en la base de datos. El valor predeterminado es **dbo**.
    - Configuración opcional:
        - Prioridad: El intervalo de prioridades va de 1 a 1000, siendo 1 la prioridad más alta. El valor predeterminado es **1000**.
        - Paralelismo: El intervalo de paralelismo va de 1 a 150. El valor predeterminado es el paralelismo máximo que se permite en su cuenta de Azure Data Lake Analytics. 
        
        ![Archivo de configuración de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)
      
        > [!NOTE] 
        > Una vez que se guarda la configuración, la información de cuenta, base de datos y esquema aparece en la barra de estado en la esquina inferior izquierda del archivo .usql correspondiente.

**Para compilar un script U-SQL**

1. Seleccione Ctrl+Mayús+P para abrir la paleta de comandos. 
2. Escriba **ADL: Compile Script**. Los resultados de la compilación aparecen en la ventana **Salida**. También puede hacer clic con el botón derecho en un archivo de script y, luego, seleccione **ADL: Compile Script** para compilar un trabajo U-SQL. El resultado de la compilación aparece en el panel **Salida**.
 

**Para enviar un script de U-SQL**

1. Seleccione Ctrl+Mayús+P para abrir la paleta de comandos. 
2. Escriba **ADL: Submit Job**.  También puede hacer clic con el botón derecho en un archivo de script y, luego, seleccionar **ADL: Submit Job**. 

Después de enviar un trabajo de U-SQL, los registros de envío aparecen en la ventana **Salida** de VS Code. Si el envío se realiza correctamente, la dirección URL del trabajo también se muestra. Puede abrir la dirección URL del trabajo en un explorador web para realizar el seguimiento de estado del trabajo en tiempo real.

Para habilitar la salida de los detalles del trabajo, establezca **jobInformationOutputPath** en el archivo **vscode for u-sql_settings.json**.
 
**Set Git Ignore**

1. Seleccione Ctrl+Mayús+P para abrir la paleta de comandos. 
2. Escriba **ADL: Set Git Ignore**.

    - Si el archivo **.gitIgnore** no está en la carpeta de trabajo de VSCode, se creará un archivo llamado **.gitIgnor** en la carpeta. De forma predeterminada, se agregarán al archivo cuatro elementos (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache** y **obj**). Si es necesario, puede realizar otros cambios.
    - Si ya hay un archivo **.gitIgnore** en la carpeta de trabajo de VSCode, la herramienta agregará cuatro elementos (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache** y **obj**) al archivo **.gitIgnore** si se encuentran en el archivo.

  ![Archivo de configuración de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="use-python-r-and-csharp-code-behind-file"></a>Uso del archivo de código subyacente de Python, R y CSharp
Herramientas de Azure Data Lake admite varios códigos personalizados. Consulte las instrucciones en [Desarrollo de trabajos U-SQL con Python, R y CSharp para Azure Data Lake Analytics en VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

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
2.  Escriba **ADL: Login**. La información de inicio de sesión aparece en la parte superior.

    ![Herramientas de Data Lake para Visual Studio Code: paleta de comandos](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Herramientas de Data Lake para Visual Studio Code: información de inicio de sesión del dispositivo](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3.  Haga clic en **Copy & Open** (Copiar y abrir) para abrir la página web https://aka.ms/devicelogin. Pegue el código **G567LX42V** en el cuadro de texto y seleccione **Continuar**.

   ![Código de inicio de sesión pegado de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Siga las instrucciones para iniciar sesión desde la página web. Cuando se conecte, el nombre de la cuenta de Azure aparecerá en la barra de estado que se encuentra en la esquina inferior izquierda de la ventana **VS Code**. 

    > [!NOTE] 
    >- Herramientas de Data Lake iniciará sesión automáticamente si se inició una sesión con anterioridad que aún no se ha cerrado.
    >- Si la cuenta tiene habilitada la autenticación de dos pasos, se recomienda usar la autenticación por teléfono en lugar de usar un PIN.


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
 - Cargar el archivo directamente a Azure Data Lake Storage en VS Code. [Carga de archivos o carpetas](#upload-file-or-folder).
 - Descargar el archivo directamente de Azure Data Lake Storage en VS Code. [Descarga de archivo](#download-file).

## <a name="list-the-storage-path"></a>Enumeración de la ruta de acceso de almacenamiento 

**Para enumerar la ruta de acceso de almacenamiento a través de la paleta de comandos**

Haga clic con el botón derecho en el editor de scripts y seleccione **ADL: List Path** (ADL: Enumerar ruta de acceso).

Elija la carpeta en la lista o haga clic en **Indicar ruta de acceso** o **Browse from Root** (Examinar desde raíz); use Indicar ruta de acceso como ejemplo. -> Seleccione **Cuenta de ADLA**. -> Navegue hasta la ruta de acceso a la carpeta de almacenamiento o escríbala; por ejemplo: /salida/). -> En la paleta de comandos se muestra la información de la ruta de acceso en función de las entradas.

![Herramientas de Data Lake para Visual Studio Code: enumerar los resultados de la ruta de acceso de almacenamiento](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Una manera más cómoda de mostrar la ruta de acceso relativa es mediante el menú contextual.

**Para enumerar la ruta de acceso de almacenamiento a través del botón derecho**

Haga clic con el botón derecho en la cadena de la ruta de acceso para seleccionar **List Path** (Enumerar ruta de acceso) para continuar.

![Herramientas de Data Lake para Visual Studio Code: menú contextual](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


## <a name="preview-the-storage-file"></a>Vista previa del archivo de almacenamiento

Haga clic con el botón derecho en el editor de scripts y seleccione **ADL: Preview File** (ADL: Obtener vista previa de archivo).

Seleccione **Cuenta de ADLA**. -> Escriba una ruta de acceso del archivo de almacenamiento de Azure (por ejemplo, /salida/RegistroBúsqueda.txt). -> Resultado: el archivo se abre en VSCode.

   ![Herramientas de Data Lake para Visual Studio Code: resultado de la vista previa de archivo](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Otra manera de obtener una vista previa del archivo de vista previa es a través del menú contextual en la ruta de acceso completa o relativa del archivo en el editor de scripts. 

## <a name="upload-file-or-folder"></a>Carga de archivos o carpetas

1. Haga clic con el botón derecho en el editor de scripts y seleccione **Cargar archivo** o **Cargar carpeta**.

2. Elija uno o varios archivos si selecciona Cargar archivo, o elija toda la carpeta si selecciona Carga carpeta. Después, haga clic en **Cargar**. -> Elija la carpeta de almacenamiento en la lista o haga clic en **Indicar ruta de acceso** o **Browse from Root** (Examinar desde raíz); use Indicar ruta de acceso como ejemplo. -> Seleccione **Cuenta de ADLA**. -> Navegue hasta la ruta de acceso a la carpeta de almacenamiento o escríbala; por ejemplo: /salida/). -> Haga clic en **Choose Current Folder** (Elegir la carpeta actual) para especificar el destino de la carga.

   ![Herramientas de Data Lake para Visual Studio Code: estado de carga](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


   Otra manera de cargar los archivos en el almacenamiento es a través del menú contextual en la ruta de acceso completa o relativa del archivo en el editor de scripts.

Al mismo tiempo, puede supervisar el [estado de la carga](#check-storage-tasks-status).


## <a name="download-file"></a>Descarga de archivo 
Puede descargar archivos mediante los comandos **ADL: Download File** (ADL: Descargar archivo) o **ADL: Download File (Advanced)** (ADL: Descargar archivo [Avanzado]).

**Para descargar archivos a través de ADL: Download File (Advanced) (ADL: Descargar archivo [Avanzado])**
1. Haga clic con el botón derecho en el editor de scripts y después seleccione **Download File (Advanced)** (Descargar archivo [Avanzado]).
2. VS Code muestra un archivo JSON. Puede especificar las rutas de acceso de archivo y descargar varios archivos al mismo tiempo. Las instrucciones se muestran en la ventana **Salida**. Para continuar con la descarga del archivo, guarde (Ctrl+S) el archivo JSON.

    ![Descargar archivos a través de configuración con Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  Resultados: la ventana **Salida** muestra el estado de carga del archivo.

    ![Resultados de la descarga de varios archivos de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Al mismo tiempo, puede supervisar el [estado de la descarga](#check-storage-tasks-status).

**Para descargar archivos a través del comando ADL: Download File (ADL: Descargar archivo)**

1. Haga clic con el botón derecho en el editor de scripts, seleccione **Descargar archivo**y, luego, elija la carpeta de destino en el cuadro de diálogo **Seleccionar carpeta**.

2. Elija la carpeta en la lista o haga clic en **Indicar ruta de acceso** o **Browse from Root** (Examinar desde raíz); use Indicar ruta de acceso como ejemplo. -> Seleccione **Cuenta de ADLA**. -> Navegue hasta la ruta de acceso a la carpeta de almacenamiento o escríbala; por ejemplo: /salida/) -> Elija el archivo que desea descargar.

   ![Estado de la descarga de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   
   Otra manera de descargar los archivos de almacenamiento es a través del menú contextual en la ruta de acceso completa o relativa del archivo en el editor de scripts.

Al mismo tiempo, puede supervisar el [estado de la descarga](#check-storage-tasks-status).

## <a name="check-storage-tasks-status"></a>Comprobación del estado de las tareas de almacenamiento
El estado se muestra en la parte inferior de la barra de estado cuando se completa la carga y descarga.
1. Haga clic en la barra de estado siguiente y después se muestra el estado de descarga y carga en el panel de **SALIDA**.

   ![Comprobación del estado de almacenamiento de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="vscode-explorer-integration-with-azure-data-lake"></a>Integración del Explorador de VSCode con Azure Data Lake

**Integración de Azure** 

- Antes de iniciar sesión en Azure, siempre puede expandir el **EXPLORADOR DE DATA LAKE** y hacer clic en **Iniciar sesión en Azure**. Después de iniciar sesión, verá que todas suscripciones de la cuenta de Azure aparecen en el panel izquierdo del **EXPLORADOR DE DATA LAKE**. 

   ![Explorador de DataLake](./media/data-lake-analytics-data-lake-tools-for-vscode/sign-in-datalake-explorer.png)

   ![Explorador de DataLake](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

**Navegación por los metadatos de ADLA** 

- Expanda la suscripción de Azure, donde podrá navegar por la base de datos U-SQL y consultar la información de **Esquemas**, **Credenciales**, **Ensamblados**, **Tabla**, **Índice**, etc. en el nodo Bases de datos U-SQL.

**Administración de entidades de metadatos de ADLA**

- Expanda **Bases de datos U-SQL**. Puede crear una base de datos, un esquema, una tabla, distintos tipos de tablas, un índice y estadísticas haciendo clic con el botón derecho en el menú contextual **Script to Create** (Script para crear) situado bajo el nodo correspondiente. Cuando se abra la página del script, modifíquelo en función de sus necesidades y envíe el trabajo haciendo clic con el botón derecho en el menú contextual **ADL: Submit Job** (ADL: Enviar trabajo). Cuando haya terminado con el proceso de creación, haga clic en el menú contextual **Actualizar** para mostrar el nuevo elemento creado. También puede eliminar el elemento haciendo clic con el botón derecho en el menú contextual **Eliminar**.

   ![El Explorador de Data Lake crea un nuevo elemento de menú](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

   ![El Explorador de Data Lake crea un nuevo script de elemento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

**Registro de ensamblados de ADLA**

 - Puede registrar el ensamblado en la base de datos correspondiente con la opción **Registrar ensamblado** haciendo clic con el botón derecho en el nodo **Ensamblados**.

    ![Explorador de DataLake](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

**Integración de ADLS** 

 - Vaya a **Cuenta de almacenamiento**, donde podrá utilizar las opciones **Vista previa**, **Descargar**, **Eliminar**, **Copiar ruta de acceso relativa** y **Copiar ruta de acceso completa** que encontrará en el menú contextual del nodo del archivo. También puede utilizar las opciones **Actualizar**, **Cargar**, **Cargar carpeta** y **Eliminar** haciendo clic en el menú contextual del nodo de la carpeta.

   ![Explorador de DataLake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

   ![Explorador de DataLake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-download-preview-file.png)

## <a name="open-adl-storage-explorer-in-portal"></a>Apertura del Explorador de almacenamiento de ADL en portal
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

## <a name="next-steps"></a>pasos siguientes
- [Desarrollo de trabajos U-SQL con Python, R y CSharp para Azure Data Lake Analytics en VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Ejecución y depuración locales de U-SQL con Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Tutorial: Introducción a Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Tutorial: Desarrollo de scripts U-SQL mediante Data Lake Tools para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Desarrollo de ensamblados U-SQL para trabajos de Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md)




