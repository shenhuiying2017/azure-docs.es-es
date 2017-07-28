---
title: Uso de Microsoft Azure Data Lake Tools para Visual Studio | Microsoft Docs
description: "Obtenga información acerca de cómo usar Azure Data Lake Tools para código de Visual Studio para crear, probar y ejecutar secuencias de comandos U-SQL. "
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/30/2016
ms.author: jejiang
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 038c84e3eb78d1654ec035ada48cdeed6dd03002
ms.contentlocale: es-es
ms.lasthandoff: 06/15/2017

---

# <a name="use-the-azure-data-lake-tools-for-visual-studio-code"></a>Uso de Azure Data Lake Tools para Visual Studio Code

Obtenga información acerca de cómo usar Azure Data Lake Tools para Visual Studio Code (VSCode) para crear, probar y ejecutar secuencias de comandos U-SQL.  La información también se explica en el vídeo siguiente:

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Requisitos previos

Data Lake Tools se puede instalar en las plataformas compatibles con VSCode que incluyen Windows, Linux y MacOS. Puede encontrar los requisitos previos para distintas plataformas:

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Java SE Runtime Environment Version 8 Update 77 o posterior](https://java.com/download/manual.jsp). Debe agregar la ruta de acceso de java.exe a la variable de entorno de sistema Path.  ¿Para obtener instrucciones, vea [cómo establecer o cambiar la variable del sistema Path?]( https://www.java.com/download/help/path.xml) La ruta de acceso es similar a C:\Archivos de programa\Java\jdk1.8.0_77\jre\bin
    - [Versión preliminar 2 de .NET Core SDK 1.0.1 o el runtime de .NET Core 1.0.1]( https://www.microsoft.com/net/download).
    
- Linux (se recomienda Ubuntu 14.04 LTS)

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx). Use el siguiente comando para instalar:

        sudo dpkg -i code_<número_de_versión>_amd64.deb

    - [Mono 4.2.x](http://www.mono-project.com/docs/getting-started/install/linux/). 

        - Actualice el origen del paquete deb mediante la ejecución de los comandos siguientes:

                sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
                echo "deb http://download.mono-project.com/repo/debian wheezy/snapshots 4.2.4.4/main" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list
                sudo apt-get update

        - Instale mono ejecutando el comando:

                sudo apt-get install mono-complete

            > [!NOTE] 
            > Mono 4.6 no se admite.  Debe desinstalar la versión 4.6 completamente antes de instalar 4.2.x.  

        - [Java SE Runtime Environment Version 8 Update 77 o posterior](https://java.com/download/manual.jsp). Puede encontrar [aquí]( https://java.com/en/download/help/linux_x64_install.xml) las instrucciones.
        - [Versión preliminar 2 de .NET Core SDK 1.0.1 o el runtime de .NET Core 1.0.1]( https://www.microsoft.com/net/download).
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Mono 4.2.4](http://download.mono-project.com/archive/4.2.4/macos-10-x86/). 
    - [Java SE Runtime Environment Version 8 Update 77 o posterior](https://java.com/download/manual.jsp). Puede encontrar [aquí](https://java.com/en/download/help/mac_install.xml) las instrucciones.
    - [Versión preliminar 2 de .NET Core SDK 1.0.1 o el runtime de .NET Core 1.0.1]( https://www.microsoft.com/net/download).

## <a name="install-the-data-lake-tools"></a>Instalación de Data Lake Tools

Después de haber instalado los requisitos previos, puede instalar Data Lake Tools para VSCode.

**Para instalar Data Lake Tools**

1. Abra **Visual Studio Code**.
2. Presione **CTRL+P** y escriba:

        ext install usql-vscode-ext
    Puede ver una lista de extensiones de código de Visual Studio. Una de ellas es **Azure Data Lake Tool (versión preliminar)**.
3. Haga clic en **Instalar** junto a **Azure Data Lake Tool (Preview) (Azure Data Lake Tool (versión preliminar))**. Después de unos segundos, se cambiará el botón Instalar para volver a cargar.
4. Haga clic en **Recargar** para activar la extensión.
5. Haga clic en **ACEPTAR** para continuar. Puede ver Azure Data Lake Tools en el panel Extensiones.

    ![Instalación de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

## <a name="activate-azure-data-lake-tools"></a>Activación de Azure Data Lake Tools
Cree un archivo .USQL o abra uno ya existente para activar la extensión. 

## <a name="connect-to-azure"></a>Conexión a Azure

Para poder compilar y ejecutar scripts de U-SQL en Azure Data Lake Analytics, debe conectarse a su cuenta de Azure.

**Para conectarse a Azure**

1.  Abra la paleta de comandos presionando **CTRL+MAYÚS+P**. 
2.  Escriba **ADL:Login**.

    ![Paleta de comandos de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

2.  Siga las instrucciones para iniciar sesión desde la página web. Una vez realizada la conexión, el nombre de cuenta se muestra en la barra de estado en la parte inferior de la ventana.

> [!NOTE] 
> Si la cuenta tiene dos factores habilitados, se recomienda utilizar la autenticación de teléfono en lugar de Pin.

Para cerrar la sesión, use el comando **ADL:Logout**.

## <a name="list-data-lake-analytics-accounts"></a>Enumeración de cuentas de Análisis de Data Lake

Para probar la conexión, puede enumerar las cuentas de Data Lake Analytics:

**Para enumerar las cuentas de Data Lake Analytics en su suscripción de Azure**

1. Abra la paleta de comandos presionando **CTRL+MAYÚS+P**.
2. Escriba **ADL:List Accounts**.  Las cuentas aparecen en el panel **Salida**.

## <a name="open-sample-script"></a>Apertura del script de ejemplo

Use la paleta de comandos (**Ctrl+Mayús+P**) y elija **ADL: Open Sample Script** (ADL: Abrir script de ejemplo). A continuación se abre otra instancia de este ejemplo. También modificará, configurará y enviará scripts en esta instancia.

## <a name="work-with-u-sql"></a>Trabajo con U-SQL

Se necesita abrir un archivo U-SQL o una carpeta para que funcione con U-SQL.

**Para abrir una carpeta para el proyecto U-SQL**

1. En Visual Studio Code, haga clic en el menú **Archivo** menú y luego en **Abrir carpeta**.
2. Especifique una carpeta y haga clic en **Seleccionar carpeta**.
3. Haga clic en el menú **Archivo** y luego en **Nuevo**. Se agregará un archivo **Sin título-1** al proyecto.
4. Copie y pegue el código siguiente en el archivo Sin título-1:

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
            TO “/Output/departments.csv”

    El script crea un archivo departments.csv con algunos datos en la carpeta /output.

5. Guarde el archivo como **myUSQL.usql** en la carpeta abierta. Observe que el archivo de configuración **adltools_settings.json** también se agrega al proyecto.
4. Abra y configure **adltools_settings.json** con las siguientes propiedades:

    - Cuenta: Cuenta de Data Lake Analytics de su suscripción de Azure.
    - Configuración opcional:

        - Prioridad: El intervalo de prioridades va de 1 a 1000, siendo 1 la prioridad más alta. El valor predeterminado es 1000.
        - Paralelismo: El intervalo de paralelismo va de 1 a 150. El valor predeterminado es 150. 

        > [!NOTE] 
        > Si la configuración no es válida, se utilizan los valores predeterminados.

     ![Archivo de configuración de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    Se necesita una cuenta de Data Lake Analytics para compilar y ejecutar trabajos de U-SQL.  Debe configurar la cuenta de equipo antes de poder compilar y ejecutar trabajos de U-SQL.

En comparación con abrir un archivo, abrir una carpeta permite:

- Utilizar un archivo de código subyacente.  En el modo de archivo único, no se admite el código subyacente.
- Utilizar un archivo de configuración. Cuando se abre una carpeta, los scripts de la carpeta de trabajo compartan un archivo de configuración.


La compilación del script U-SQL se realiza de forma remota mediante el servicio Data Lake Analytics.  Cuando se emite el comando de compilación, el script U-SQL se envía a su cuenta de Data Lake Analytics. El resultado de compilación lo recibe tarde Visual Studio Code. Debido a la compilación remota, Visual Studio Code requiere la información para conectarse a su cuenta de Data Lake Analytics en el archivo de configuración.

**Para compilar un script U-SQL**

1. Abra la paleta de comandos presionando **CTRL+MAYÚS+P**. 
2. Escriba **ADL: Compile Script**. Los resultados de la compilación se muestran en la ventana de salida. También puede hacer clic con el botón derecho en un archivo de script y luego en **ADL: Compile Script** para compilar un trabajo U-SQL. El resultado de la compilación se muestra en el panel de salida.
 

**Para enviar un script de U-SQL**

1. Abra la paleta de comandos presionando **CTRL+MAYÚS+P**. 
2. Escriba **ADL: Submit Job**.  También puede hacer clic con el botón derecho en un archivo de script y luego en **ADL: Submit Job** para enviar un trabajo U-SQL. 

Después de enviar un trabajo de U-SQL, los registros de envío se muestran en la ventana de resultados de VSCode. Si el envío se realiza correctamente, la dirección URL del trabajo también se muestra. Puede abrir la dirección URL del trabajo en un explorador web para realizar el seguimiento de estado del trabajo en tiempo real.

Para habilitar los detalles del trabajo de salida: establezca ‘jobInformationOutputPath’ en el archivo **vscode for u-sql_settings.json**.
 
## <a name="use-code-behind-file"></a>Uso de un archivo de código subyacente

El archivo de código subyacente es un archivo de CSharp asociado con un script U-SQL. Puede definir un script UDO, UDA, UDT o UDF dedicado en el archivo de código subyacente. UDF, UDO, UDA o UDT se puede utilizar directamente en el script sin registrar el ensamblado en primer lugar. El archivo de código subyacente se coloca en la misma carpeta que su archivo de script U-SQL de emparejamiento. Si el script se denomina xxx.usql, el código subyacente se denomina xxx.usql.cs. Al eliminar manualmente el archivo de código subyacente, se deshabilita la característica de código subyacente para su script U-SQL asociado. Para más información sobre cómo escribir código de cliente para el script U-SQL, consulte [Writing and Using Custom Code in U-SQL – User-Defined Functions]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/) (Escritura y uso de código personalizado en U-SQL: funciones definidas por el usuario).

Para admitir el código subyacente, se debe abrir una carpeta de trabajo. 

**Para generar un archivo de código subyacente**

1. Abra un archivo de origen. 
2. Abra la paleta de comandos presionando **CTRL+MAYÚS+P**.
3. Escriba **ADL: Generate Code Behind**.  Se crea un archivo de código subyacente en la misma carpeta. 

También puede hacer clic con el botón derecho en un archivo de script y luego en **ADL: Generate Code Behind** para generar un archivo de código subyacente. 

Compilar y enviar un script U-SQL con código subyacente es lo mismo que el script U-SQL independiente.

Las dos capturas de pantalla siguientes muestran un archivo de código subyacente y su archivo de script U-SQL asociado:
 
![Código subyacente de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![Código subyacente de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

## <a name="use-assemblies"></a>Uso de ensamblados

Para obtener información sobre el desarrollo de ensamblados, consulte [Desarrollo de ensamblados U-SQL para trabajos de Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).

Con Data Lake Tools puede registrar ensamblados de código personalizados para el catálogo de Data Lake Analytics.

**Para registrar un ensamblado**

1.  Presione **CTRL+MAYÚS+P** para abrir la paleta de comando.
2.  Escriba **ADL:Register Assembly**.
3.  Seleccione una cuenta de Data Lake Analytics.
4.  Seleccione una base de datos.
5.  Especifique la ruta de acceso de ensamblado local.

El siguiente código de U-SQL muestra cómo llamar a un ensamblado. En el ejemplo, el nombre de ensamblaje es *test*.

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


## <a name="access-data-lake-analytics-catalog"></a>Acceso al catálogo de Data Lake Analytics

Después de haberse conectado a Azure, puede usar los siguientes pasos para acceder al catálogo de U-SQL:

**Para acceder a los metadatos de Azure Data Lake Analytics**:

1.  Presione **CTRL+MAYÚS+P** y escriba **ADL:List Tables**.
2.  Haga clic en una de las cuentas de Data Lake Analytics.
3.  Haga clic en una de las bases de datos de Data Lake Analytics.
4.  Haga clic en uno de los esquemas. Puede ver las tablas.

## <a name="show-data-lake-analytics-jobs"></a>Presentación de trabajos de Data Lake Analytics

Use la paleta de comandos (**Ctrl+Mayús+P**) y elija **ADL: Show Job** (ADL: Mostrar trabajo). 

1.  Seleccione una cuenta Local o ADLA. 
2.  Espere a que se muestre la lista de trabajos de la cuenta.
3.  Seleccione un trabajo de la lista de trabajos; las herramientas de ADL abren los detalles del trabajo en el portal y muestran el archivo JobInfo en VSCode.

    ![Tipos de objetos de IntelliSense de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-adls-integration"></a>Integración de Azure Data Lake Storage (ADLS)

Puede usar los comandos relacionados con ADLS para desplazarse hasta los recursos de ADLS, obtener una vista previa del archivo ADLS y cargar el archivo en ADLS directamente en VSCode.  También puede abrir **Explorador web de Azure Storage** mediante el comando: **ADL: Open Web Azure Storage Explorer** (ADL: Abrir Explorador web de Azure Storage) o desde el menú contextual de clic derecho.

### <a name="list-storage-path"></a>Enumeración de la ruta de acceso de almacenamiento

Use la paleta de comandos (**Ctrl+Mayús+P**) y elija **ADL: List Storage Path** (ADL: Enumerar ruta de acceso de almacenamiento).
1.  Abra la paleta de comandos e inserte el comando.

    ![Data Lake Tools para Visual Studio Code: enumeración de la ruta de acceso de almacenamiento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-storage.png)

2.  Seleccione una cuenta local o ADLA.

    ![Data Lake Tools para Visual Studio Code: enumeración de la ruta de acceso de almacenamiento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  Haga clic en More (Más) para enumerar más cuentas ADLA y seleccione una.

    ![Data Lake Tools para Visual Studio Code: enumeración de la ruta de acceso de almacenamiento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

4.  Escriba una ruta de acceso de almacenamiento de Azure. Por ejemplo: /output/

       ![Data Lake Tools para Visual Studio Code: enumeración de la ruta de acceso de almacenamiento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-path.png)

5.  Resultados: en la paleta de comandos se muestra la información de la ruta de acceso en función de sus entradas.

    ![Data Lake Tools para Visual Studio Code: enumeración de la ruta de acceso de almacenamiento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-path.png)

Otra manera más conveniente de mostrar la ruta de acceso relativa es mediante el menú contextual de clic derecho.

1.  Haga clic con el botón derecho en la cadena de la ruta de acceso para seleccionar List Storage Path (Enumerar ruta de acceso de almacenamiento).

       ![Data Lake Tools para Visual Studio Code: menú contextual de clic derecho](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

2.  Seleccione una cuenta local o ADLA.

       ![Data Lake Tools para Visual Studio Code: clic derecho en almacenamiento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  Resultados: en la paleta de comandos se enumeran las carpetas y los archivos de la ruta de acceso actual.

       ![Data Lake Tools para Visual Studio Code: enumeración de la ruta de acceso actual](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-current.png)

### <a name="preview-storage-file"></a>Vista previa del archivo de almacenamiento

Use la paleta de comandos (**Ctrl+Mayús+P**) y elija **ADL: Preview Storage File** (ADL: Vista previa del archivo de almacenamiento).
1.  Abra la paleta de comandos e inserte el comando.

       ![Data Lake Tools para Visual Studio Code: enumeración de vista previa](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-preview.png)

2.  Seleccione una cuenta local o ADLA.

       ![Data Lake Tools para Visual Studio Code: enumeración de cuentas](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  Haga clic en More (Más) para enumerar más cuentas ADLA y seleccione una.

       ![Data Lake Tools para Visual Studio Code: selección de una cuenta](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

4.  Escriba una ruta de acceso de almacenamiento de Azure. Por ejemplo: /output/

       ![Data Lake Tools para Visual Studio Code: entrada de archivo](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-file.png)

5.  Resultados: en la paleta de comandos se muestra la información de la ruta de acceso en función de sus entradas.

       ![Data Lake Tools para Visual Studio Code: vista previa de archivo](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

Otra manera más conveniente de obtener una vista previa de un archivo es mediante el menú contextual de clic derecho.

1.  Haga clic con el botón derecho en una ruta de acceso de archivo para obtener una vista previa de un archivo.

       ![Data Lake Tools para Visual Studio Code: menú contextual de clic derecho](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-preview.png)

2.  Seleccione una cuenta local o ADLA.

       ![Data Lake Tools para Visual Studio Code: selección de una cuenta](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  Resultados: VSCode muestra los resultados de la vista previa del archivo.

       ![Data Lake Tools para Visual Studio Code: enumeración de la ruta de acceso actual](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

### <a name="upload-to-storage-path"></a>Carga en la ruta de acceso de almacenamiento

Use la paleta de comandos (**Ctrl+Mayús+P**) y elija **ADL: Upload to Storage Path** (ADL: Cagar en la ruta de acceso de almacenamiento).
1.  Abra la paleta de comandos e inserte el comando.

       ![Data Lake Tools para Visual Studio Code: enumeración de vista previa](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-storage.png)

2.  Seleccione una cuenta local o ADLA.

       ![Data Lake Tools para Visual Studio Code: enumeración de cuentas](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  Haga clic en More (Más) para enumerar más cuentas ADLA y seleccione una.

       ![Data Lake Tools para Visual Studio Code: selección de una cuenta](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

4.  Escriba una ruta de acceso de almacenamiento de Azure. Por ejemplo: /output/

       ![Data Lake Tools para Visual Studio Code: entrada de archivo](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-file.png)

5.  Escriba una ruta de acceso local al archivo de origen.

       ![Data Lake Tools para Visual Studio Code: entrada de archivo](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-file.png)

6.  VSCode muestra un archivo de configuración json, en el que puede realizar las actualizaciones que sean necesarias. Guarde el archivo (CTRL+S) para proceder a su carga.

       ![Data Lake Tools para Visual Studio Code: entrada de archivo](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-file.png)

7.  Resultados: la ventana de salida muestra el estado de carga del archivo.

       ![Data Lake Tools para Visual Studio Code: estado de carga](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)    

## <a name="additional-features"></a>Características adicionales

Data Lake Tools para VSCode admite las siguientes características:

-   Autocompletar de IntelliSense. Las sugerencias aparecen en palabra clave, método, variables, etc. Los distintos iconos representan diferentes tipos de los objetos:

    - Tipo de datos de escala
    - Tipo de datos complejo
    - UDT integrada
    - Clases y colección .NET
    - Expresiones de C#
    - UDF, UDO y UDAAG integrados de C# 
    - Funciones de U-SQL
    - Funciones de ventana de U-SQL
 
    ![Tipos de objetos de IntelliSense de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Autocompletar de IntelliSense en los metadatos de Data Lake Analytics. Data Lake Tools descarga la información de metadatos de Data Lake Analytics localmente.  La característica IntelliSense rellena automáticamente los objetos, lo que incluye base de datos, esquema, tabla, vista, TVF, procedimientos, ensamblados de C#, desde los metadatos de Data Lake Analytics.
 
    ![Metadatos de IntelliSense de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   Marcador de error de IntelliSense. Data Lake Tools subraya los errores de edición para U-SQL U y C#. 
-   Aspectos destacados de la sintaxis. Data Lake Tools usa un color diferente para diferenciar variables, palabras clave, tipo de datos, funciones, etc. 

    ![Aspectos destacados de la sintaxis de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Pasos siguientes:

- Para obtener información detallada de Data Lake Analytics, consulte [Tutorial: Introducción a Azure Data Lake Analytics mediante Azure Portal](data-lake-analytics-get-started-portal.md).
- Para información sobre el uso de Data Lake Tools para Visual Studio, consulte [Tutorial: Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para obtener información sobre el desarrollo de ensamblados, consulte [Desarrollo de ensamblados U-SQL para trabajos de Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).




