---
title: 'Herramientas de Azure Data Lake: Usar Herramientas de Azure Data Lake para Visual Studio Code | Microsoft Docs'
description: "Obtenga información acerca de cómo usar Azure Data Lake Tools para código de Visual Studio para crear, probar y ejecutar secuencias de comandos U-SQL. "
Keywords: "VSCode, Herramientas de Azure Data Lake, ejecución local, depuración local, archivo de almacenamiento de versión preliminar, cargar en la ruta de acceso de almacenamiento"
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
ms.date: 07/14/2017
ms.author: jejiang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 0ed3d7a0057eb446b3e1d16019ac74c641ae3138
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="use-the-azure-data-lake-tools-for-visual-studio-code"></a>Uso de Azure Data Lake Tools para Visual Studio Code

Obtenga información acerca de cómo usar Azure Data Lake Tools para Visual Studio Code (VSCode) para crear, probar y ejecutar secuencias de comandos U-SQL.  La información también se explica en el vídeo siguiente:

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Requisitos previos

Data Lake Tools se puede instalar en las plataformas compatibles con VSCode que incluyen Windows, Linux y MacOS. Puede encontrar los requisitos previos para distintas plataformas:

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Java SE Runtime Environment Version 8 Update 77 o posterior](https://java.com/download/manual.jsp). Tiene que agregar la ruta de acceso de java.exe a la variable de entorno del sistema PATH.  ¿Para obtener instrucciones, vea [cómo establecer o cambiar la variable del sistema Path?]( https://www.java.com/download/help/path.xml) La ruta de acceso es similar a C:\Archivos de programa\Java\jdk1.8.0_77\jre\bin
    - [SDK 1.0.3 de .NET Core o el runtime de .NET Core 1.1](https://www.microsoft.com/net/download).
    
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
        - [SDK 1.0.3 de .NET Core o el runtime de .NET Core 1.1](https://www.microsoft.com/net/download).
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Mono 4.2.4](http://download.mono-project.com/archive/4.2.4/macos-10-x86/). 
    - [Java SE Runtime Environment Version 8 Update 77 o posterior](https://java.com/download/manual.jsp). Puede encontrar [aquí](https://java.com/en/download/help/mac_install.xml) las instrucciones.
    - [SDK 1.0.3 de .NET Core o el runtime de .NET Core 1.1](https://www.microsoft.com/net/download).

## <a name="install-the-data-lake-tools"></a>Instalación de Data Lake Tools

Después de haber instalado los requisitos previos, puede instalar Data Lake Tools para VSCode.

**Para instalar Data Lake Tools**

1. Abra **Visual Studio Code**.
2. Presione **CTRL+P** y escriba:
```
ext install usql-vscode-ext
```
Puede ver una lista de extensiones de código de Visual Studio. Una de ellas es **Herramientas de Azure Data Lake**.

3. Haga clic en **Instalar** junto a **Herramientas de Azure Data Lake**. Después de unos segundos, se cambiará el botón Instalar para volver a cargar.
4. Haga clic en **Recargar** para activar la extensión.
5. Haga clic en **ACEPTAR** para continuar. Puede ver Azure Data Lake Tools en el panel Extensiones.
    ![Instalación de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

## <a name="activate-azure-data-lake-tools"></a>Activación de Azure Data Lake Tools
Cree un archivo .USQL o abra uno ya existente para activar la extensión. 

## <a name="connect-to-azure"></a>Conexión a Azure

Para poder compilar y ejecutar scripts de U-SQL en Azure Data Lake Analytics, debe conectarse a su cuenta de Azure.

**Para conectarse a Azure**

1.  Abra la paleta de comandos presionando **CTRL+MAYÚS+P**. 
2.  Escriba **ADL: Login**. La información de inicio de sesión se muestra en el panel de resultados.

    ![Paleta de comandos de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Información de inicio de sesión de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. Presione la tecla CTRL y haga clic en la dirección URL de inicio de sesión: https://aka.ms/devicelogin para abrir la página web de inicio de sesión. Copie y pegue el código G567LX42V en el cuadro de texto que aparece a continuación, haga clic en Continuar para continuar.

   ![Código de inicio de sesión pegado de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Siga las instrucciones para iniciar sesión desde la página web. Una vez realizada la conexión, el nombre de la cuenta de Azure se muestra en la barra de estado en la parte inferior izquierda de la ventana de VSCode. 

    > [!NOTE] 
    > Si la cuenta tiene dos factores habilitados, se recomienda utilizar la autenticación de teléfono en lugar de Pin.

Para cerrar la sesión, use el comando **ADL: Logout**.

## <a name="list-data-lake-analytics-accounts"></a>Enumeración de cuentas de Análisis de Data Lake

Para probar la conexión, puede enumerar las cuentas de Data Lake Analytics:

**Para enumerar las cuentas de Data Lake Analytics en su suscripción de Azure**

1. Abra la paleta de comandos presionando **CTRL+MAYÚS+P**.
2. Escriba **ADL: List Accounts**.  Las cuentas aparecen en el panel **Salida**.

## <a name="open-sample-script"></a>Apertura del script de ejemplo

Use la paleta de comandos (**Ctrl+Mayús+P**) y elija **ADL: Open Sample Script** (ADL: Abrir script de ejemplo). Después se abre otra instancia para este ejemplo. En esta instancia también puede modificar, configurar y enviar scripts.

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
    - Base de datos: Una base de datos en su cuenta. El valor predeterminado es master.
    - Esquema: Un esquema en la base de datos. El valor predeterminado es dbo.
    - Configuración opcional:
        - Prioridad: El intervalo de prioridades va de 1 a 1000, siendo 1 la prioridad más alta. El valor predeterminado es 1000.
        - Paralelismo: El intervalo de paralelismo va de 1 a 150. El valor predeterminado es el paralelismo máximo que se permite en su cuenta de ADLA. 
        
        > [!NOTE] 
        > Si la configuración no es válida, se utilizan los valores predeterminados.

    ![Archivo de configuración de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    Se necesita una cuenta de Data Lake Analytics para compilar y ejecutar trabajos de U-SQL.  Debe configurar la cuenta de equipo antes de poder compilar y ejecutar trabajos de U-SQL.
    
    Después de guardar la configuración, la información cuenta | base de datos | esquema se muestra en la barra de estado en la parte inferior izquierda del archivo USQL correspondiente. 
 
 

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

Se puede registrar el ensamblado a través de **ADL: Register Assembly** (ADL: registrar el ensamblado) o **ADL: Register Assembly through Configuration** (ADL: registrar el ensamblado a través de la configuración).

**ADL: Register Assembly**
1.  Presione **CTRL+MAYÚS+P** para abrir la paleta de comando.
2.  Escriba **ADL: Register Assembly**. 
3.  Especifique la ruta de acceso de ensamblado local. 
4.  Seleccione una cuenta de Data Lake Analytics.
5.  Seleccione una base de datos.

Resultados: El portal se abre en el explorador y muestra el proceso de registro del ensamblado.  

Otra manera cómoda de desencadenar el comando **ADL: Register Assembly** consiste en hacer clic con el botón derecho en el archivo dll en el Explorador. 

**ADL: Register Assembly through Configuration**.
1.  Presione **CTRL+MAYÚS+P** para abrir la paleta de comando.
2.  Escriba **ADL: Register Assembly through Configuration**. 
3.  Especifique la ruta de acceso de ensamblado local. 
4.  Se muestra el archivo json. Revise y edite las dependencias de ensamblado y los parámetros de recursos si es necesario. Las instrucciones se muestran en la ventana de salida. Guarde (**CTRL+S**) el archivo Json para continuar con el registro del ensamblado.

![Código subyacente de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- Dependencias de ensamblado: Herramientas de Azure Data Lake detecta automáticamente si el archivo DLL tiene alguna dependencia. Una vez detectadas las dependencias, se muestran en el archivo Json. 
>- Recursos: Puede cargar los recursos DLL (por ejemplo, txt, png, csv, etc.) junto con el registro del ensamblado. 

Otra manera cómoda de desencadenar el comando **ADL: Register Assembly through Configuration** consiste en hacer clic con el botón derecho en el archivo dll en el Explorador. 

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


## <a name="access-data-lake-analytics-catalog"></a>Acceso al catálogo de Data Lake Analytics

Después de haberse conectado a Azure, puede usar los siguientes pasos para acceder al catálogo de U-SQL:

**Para acceder a los metadatos de Azure Data Lake Analytics**:

1.  Presione **CTRL+MAYÚS+P** y después escriba **ADL: List Tables**.
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

Puede usar los comandos relacionados con ADLS para desplazarse hasta los recursos de ADLS, obtener una vista previa del archivo ADLS y cargar el archivo en ADLS directamente en VSCode.  
### <a name="list-storage-path"></a>Enumeración de la ruta de acceso de almacenamiento

Use la paleta de comandos (**Ctrl+Mayús+P**) y elija **ADL: List Storage Path** (ADL: Enumerar ruta de acceso de almacenamiento).
1.  Abra la paleta de comandos e inserte el comando.

    ![Data Lake Tools para Visual Studio Code: enumeración de la ruta de acceso de almacenamiento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-storage.png)

2.  Seleccione el modo preferido para enumerar la ruta de acceso de almacenamiento. En este paso se usa **Enter a path** (Escribir una ruta de acceso) como ejemplo.

    ![Enumeración de la ruta de acceso de almacenamiento de Herramientas de Data Lake para Visual Studio Code para seleccionar una forma](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)

    > [!NOTE]
    >- VSCode mantiene la última ruta de acceso que ha visitado en todas las cuentas de ADLA. Ejemplo: /tt/ss.
    >- Browser from root path (Explorador desde ruta de acceso raíz): Indica la ruta de acceso de la cuenta local o de ADLA seleccionada.
    >- Enter a path (Escribir una ruta de acceso): Indica la ruta de acceso especificada de la cuenta local o de ADLA seleccionada.
    
3. Seleccione una cuenta local o ADLA.

    ![Más selecciones de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  Haga clic en More (Más) para enumerar más cuentas ADLA y seleccione una.

    ![Herramientas de Data Lake para Visual Studio Code: selección de una cuenta](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

5.  Escriba una ruta de acceso de almacenamiento de Azure. Por ejemplo: /output.

       ![Herramientas de Data Lake para Visual Studio Code: escribir la ruta de acceso de almacenamiento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-path.png)

6.  Resultados: en la paleta de comandos se muestra la información de la ruta de acceso en función de sus entradas.

    ![Herramientas de Data Lake para Visual Studio Code: resultado de la enumeración de la ruta de acceso de almacenamiento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-path.png)

Otra manera más conveniente de mostrar la ruta de acceso relativa es mediante el menú contextual de clic derecho.

1.  Haga clic con el botón derecho en la cadena de la ruta de acceso para seleccionar List Storage Path (Enumerar ruta de acceso de almacenamiento).

       ![Data Lake Tools para Visual Studio Code: menú contextual de clic derecho](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)
2. La ruta de acceso relativa seleccionada se muestra en la paleta de comandos.

   ![Herramientas de Data Lake para Visual Studio Code: enumeración de la ruta de acceso seleccionada](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-relative-path.png)

3.  Seleccione una cuenta local o ADLA.

       ![Herramientas de Data Lake para Visual Studio Code: selección de una cuenta](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  Resultados: en la paleta de comandos se enumeran las carpetas y los archivos de la ruta de acceso actual.

       ![Data Lake Tools para Visual Studio Code: enumeración de la ruta de acceso actual](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-current.png)

### <a name="preview-storage-file"></a>Vista previa del archivo de almacenamiento

Use la paleta de comandos (**Ctrl+Mayús+P**) y elija **ADL: Preview Storage File** (ADL: Vista previa del archivo de almacenamiento).
1.  Abra la paleta de comandos e inserte el comando.

       ![Herramientas de Data Lake para Visual Studio Code: vista previa de archivo de almacenamiento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-preview.png)

2.  Seleccione una cuenta local o ADLA.

       ![Data Lake Tools para Visual Studio Code: enumeración de cuentas](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  Haga clic en More (Más) para enumerar más cuentas ADLA y seleccione una.

       ![Data Lake Tools para Visual Studio Code: selección de una cuenta](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

4.  Escriba una ruta de acceso o un archivo de Azure Storage. Por ejemplo: /output/SearchLog.txt

       ![Herramientas de Data Lake para Visual Studio Code: escribir la ruta de acceso y el archivo de almacenamiento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

5.  Resultados: en la paleta de comandos se muestra la información de la ruta de acceso en función de sus entradas.

       ![Herramientas de Data Lake para Visual Studio Code: resultado de la vista previa de archivo](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

Otra manera más conveniente de obtener una vista previa de un archivo es mediante el menú contextual de clic derecho.

1.  Haga clic con el botón derecho en una ruta de acceso de archivo para obtener una vista previa de un archivo.

       ![Data Lake Tools para Visual Studio Code: menú contextual de clic derecho](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-preview.png)

2.  Seleccione una cuenta local o ADLA.

       ![Data Lake Tools para Visual Studio Code: selección de una cuenta](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  Resultados: VSCode muestra los resultados de la vista previa del archivo.

       ![Herramientas de Data Lake para Visual Studio Code: resultado de la vista previa de archivo](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

### <a name="upload-file"></a>Upload File (Cargar archivo) 

Puede cargar archivos a través del comando **ADL: Upload File** (ADL: Cargar archivo) o **ADL: Upload File through Configuration** (ADL: Cargar archivo a través de la configuración).

**ADL: Upload File**
1.  Presione CTRL+MAYÚS+P para abrir la paleta de comandos o haga clic con el botón derecho en el editor de scripts y, después, escriba **Upload File** (Cargar archivo).
2. Escriba una ruta de acceso local para cargar.

    ![Herramientas de Data Lake para Visual Studio Code: escribir ruta de acceso local](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-input-local-path.png)

3. Seleccione una forma para mostrar la ruta de acceso de almacenamiento. En este paso se usa **Enter a path** (Escribir una ruta de acceso) como ejemplo.

    ![Data Lake Tools para Visual Studio Code: enumeración de la ruta de acceso de almacenamiento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)
    >[!NOTE]
    >- VSCode mantiene la última ruta de acceso que ha visitado en todas las cuentas de ADLA. Ejemplo: /tt/ss.
    >- Browser from root path (Explorador desde ruta de acceso raíz): Indica la ruta de acceso de la cuenta local o de ADLA seleccionada.
    >- Enter a path (Escribir una ruta de acceso): Indica la ruta de acceso especificada de la cuenta local o de ADLA seleccionada.

4. Seleccione una cuenta local o ADLA.

    ![Data Lake Tools para Visual Studio Code: clic derecho en almacenamiento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

5.  Escriba una ruta de acceso de almacenamiento de Azure. Por ejemplo: /output/

       ![Herramientas de Data Lake para Visual Studio Code: escribir la ruta de acceso de almacenamiento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

6. Indica la ruta de acceso de Azure Storage que especifique. Seleccione **Choose current folder** (Elegir carpeta actual).

    ![Herramientas de Data Lake para Visual Studio Code: seleccionar una carpeta](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-choose-current-folder.png)

7.  Resultados: la ventana de salida muestra el estado de carga del archivo.

       ![Data Lake Tools para Visual Studio Code: estado de carga](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)    

**ADL: Upload File through Configuration**
1.  Presione CTRL+MAYÚS+P para abrir la paleta de comandos o haga clic con el botón derecho en el editor de scripts y, después, escriba **Upload File through Configuration** (Cargar archivo a través de la configuración).
2.  VSCode muestra un archivo json. Puede especificar las rutas de acceso de archivo y cargar varios archivos al mismo tiempo. Las instrucciones se muestran en la ventana de salida. Guarde (**CTRL+S**) el archivo Json para continuar con la carga del archivo.

       ![Data Lake Tools para Visual Studio Code: entrada de archivo](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-file.png)

3.  Resultados: la ventana de salida muestra el estado de carga del archivo.

       ![Data Lake Tools para Visual Studio Code: estado de carga](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)     

Otra manera cómoda de cargar el archivo en el almacenamiento es a través del menú contextual en la ruta de acceso completa o relativa del archivo en el editor de scripts. Escriba la ruta de acceso local del archivo y elija la cuenta, y después la ventana de salida muestra el estado de la carga. 

### <a name="open-web-azure-storage-explorer"></a>Abrir el explorador web de Azure Storage
Puede abrir **Explorador web de Azure Storage** mediante el comando: **ADL: Open Web Azure Storage Explorer** (ADL: Abrir Explorador web de Azure Storage) o desde el menú contextual haciendo clic con el botón derecho.

1. Presione CTRL+MAYÚS+P para abrir la paleta de comandos.
2. Escriba **Open Web Azure Storage Explorer** o haga clic con el botón derecho en una ruta de acceso relativa o completa en el editor de scripts para elegir **Open Web Azure Storage Explorer**.
3. Seleccione una cuenta de Data Lake Analytics.

Herramientas de ADL abre la ruta de acceso de Azure Storage en el portal. Puede visitar la ruta de acceso y la vista previa del archivo desde la web.

### <a name="local-run-and-local-debug-windows-users"></a>Ejecución y depuración locales de usuarios de Windows
Se ha implementado la ejecución local de U-SQL para probar los datos locales y validar el script localmente antes de publicar el código en ADLA. La característica de depuración local permite depurar el código de C# subyacente, ejecutarlo paso a paso y validar el script localmente antes de enviarlo a ADLA. Vea las instrucciones en: [U-SQL local run and local debug with Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md) (Ejecución y depuración locales de U-SQL con Visual Studio Code).

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

## <a name="next-steps"></a>Pasos siguientes

- Para la ejecución y depuración locales de U-SQL con Visual Studio Code, vea [U-SQL local run and local debug with Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md) (Ejecución y depuración locales de U-SQL con Visual Studio Code).
- Para obtener información detallada de Data Lake Analytics, consulte [Tutorial: Introducción a Azure Data Lake Analytics mediante Azure Portal](data-lake-analytics-get-started-portal.md).
- Para información sobre el uso de Data Lake Tools para Visual Studio, consulte [Tutorial: Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para obtener información sobre el desarrollo de ensamblados, consulte [Desarrollo de ensamblados U-SQL para trabajos de Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).




