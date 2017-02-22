---
title: Uso del SDK de Java para desarrollar aplicaciones en Azure Data Lake Store | Microsoft Docs
description: "Uso del SDK de Java de Azure Data Lake Store para crear una cuenta de Data Lake Store y realizar operaciones básicas en este"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/23/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 091fadce064086d82b833f8e44edfbba125d3e6b
ms.openlocfilehash: cb5babdd8fea3615d8aa27f05a07c3b489f3faa4


---
# <a name="get-started-with-azure-data-lake-store-using-java"></a>Introducción al Almacén de Azure Data Lake mediante Java
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [SDK de Java](data-lake-store-get-started-java-sdk.md)
> * [API DE REST](data-lake-store-get-started-rest-api.md)
> * [CLI de Azure](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Aprenda a utilizar el SDK de Java de Azure Data Lake Store para realizar operaciones básicas como crear carpetas, cargar y descargar archivos de datos, etc. Para más información sobre Data Lake, consulte [Azure Data Lake Store](data-lake-store-overview.md).

Puede acceder a los documentos de la API del SDK de Java para Azure Data Lake Store en los [documentos de la API de Java de Azure Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Requisitos previos
* Kit de desarrollo de Java (JDK 7 o superior, con Java versión 1.7 o posterior).
* Cuenta de Azure Data Lake Store. Siga las instrucciones que se describen en [Introducción al Almacén de Azure Data Lake mediante el Portal de Azure](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Este tutorial usa Maven para crear las dependencias de un proyecto. Aunque es posible generarlas sin utilizar un sistema como Maven o Gradle, estos sistemas facilitan mucho la administración de las dependencias.
* (Opcional) Y un IDE como [IntelliJ IDEA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) o similar.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>¿Cómo se puede autenticar mediante Azure Active Directory?
En este tutorial, utilizamos un secreto de cliente de la aplicación de Azure AD para recuperar un token de Azure Active Directory (autenticación de servicio a servicio). Usamos este token para crear un objeto de cliente de Data Lake Store para llevar a cabo operaciones de archivos y directorios. Para ver instrucciones sobre cómo autenticar con Azure Data Lake Store mediante el secreto del cliente, realizamos los siguientes pasos de alto nivel:

1. Creación de una aplicación web de Azure AD
2. Recupere el identificador de cliente, el secreto de cliente y el punto de conexión del token para la aplicación web de Azure AD.
3. Configure el acceso para la aplicación web de Azure AD en el archivo o carpeta Data Lake Store al que desee acceder desde la aplicación de Java que está creando.

Para ver instrucciones sobre cómo realizar estos pasos, consulte [Creación de una aplicación de Active Directory](data-lake-store-authenticate-using-active-directory.md#create-an-active-directory-application).

Azure Active Directory ofrece también otras opciones para recuperar un token. Puede elegir entre una serie de diferentes mecanismos de autenticación para adaptarlos a su escenario; por ejemplo, una aplicación que se ejecuta en un explorador, una aplicación distribuida como una aplicación de escritorio o una aplicación de servidor que se ejecuta de forma local o en una máquina virtual de Azure. También puede elegir entre distintos tipos de credenciales, como contraseñas, certificados, autenticación en dos fases, etc. Además, Azure Active Directory permite sincronizar los usuarios de Active Directory local con la nube. Para más detalles, consulte [Escenarios de autenticación para Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md). 

## <a name="create-a-java-application"></a>Creación de una aplicación Java
El ejemplo de código disponible [en GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) le guía a través del proceso de creación de archivos en el almacén, concatenación de archivos, descarga de un archivo y eliminación de algunos archivos en el almacén. Esta sección del artículo le guía a través de las principales partes del código.

1. Cree un proyecto de Maven con [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) en la línea de comandos o mediante un IDE. Para ver instrucciones sobre cómo crear un proyecto de Java mediante IntelliJ, consulte [este artículo](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para ver instrucciones sobre cómo crear un proyecto con Eclipse, consulte [este artículo](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 
2. Agregue las siguientes dependencias a su archivo **pom.xml** de Maven. Agregue el siguiente fragmento de texto entre las etiquetas **\</version>** y **\</project>**:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.4</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    La primera dependencia es el uso del SDK de Data Lake Store (`azure-data-lake-store-sdk`) desde el repositorio de maven. La segunda dependencia (`slf4j-nop`) consiste en especificar qué plataforma de registro se usará para esta aplicación. El SDK de Data Lake Store usa la fachada de registro [slf4j](http://www.slf4j.org/), que permite elegir entre una serie de plataformas de registro populares, como log4j, registro de Java, logback, etc., o no registrarse. En este ejemplo, se deshabilitará el registro; por tanto, usamos el enlace **slf4j-nop**. Para usar otras opciones de registro en su aplicación, consulte [este artículo](http://www.slf4j.org/manual.html#projectDep).

### <a name="add-the-application-code"></a>Revisión del código de la aplicación
Hay tres partes principales en el código.

1. Obtener el token de Azure Active Directory.
2. Usar el token para crear a un cliente de Data Lake Store.
3. Utilizar al cliente de Data Lake Store para realizar operaciones.

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>Paso 1: Obtención de un token de Azure Active Directory.
El SDK de Data Lake Store proporciona métodos útiles que permiten obtener los tokens de seguridad necesarios para comunicarse con la cuenta de Data Lake Store. Sin embargo, el SDK no obliga a que se usen solo estos métodos. También puede usar cualquier otro medio de obtención del token, como el [SDK de Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) o su propio código personalizado.

Para utilizar el SDK de Data Lake Store con el fin de obtener el token para la aplicación web de Active Directory que creó anteriormente, utilice una de las subclases de `AccessTokenProvider` (el ejemplo a continuación utiliza `ClientCredsTokenProvider`). El proveedor de tokens almacena en caché las credenciales que se utilizan para obtener el token en la memoria y renueva el token de forma automática si está a punto de expirar. Puede crear sus propias subclases de `AccessTokenProvider` para que los tokens se obtengan mediante su código de cliente, pero por ahora vamos a usar simplemente el que se proporciona en el SDK.

Reemplace **FILL-IN-HERE** por los valores reales de la aplicación web de Azure Active Directory.

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>Paso 2: Creación de un objeto de cliente de Azure Data Lake Store (ADLStoreClient)
La creación de un objeto [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) requiere que especifique el nombre de la cuenta de Data Lake Store y el proveedor de token que generó en el paso anterior. Tenga en cuenta que el nombre de la cuenta de Data Lake Store debe ser un nombre de dominio completo. Por ejemplo, reemplace **FILL-IN-HERE** por algo similar a **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>Paso 3: Uso de ADLStoreClient para realizar operaciones de archivo y directorio
El código siguiente contiene fragmentos de código de ejemplo de algunas operaciones comunes. Puede buscar en todos los [documentos de la API de SDK de Java de Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/) del objeto **ADLStoreClient** para ver otras operaciones.

Tenga en cuenta que los archivos se leen y escribes mediante secuencias de Java estándar. Esto significa que puede crear niveles de cualquiera de las secuencias de Java sobre las secuencias de Data Lake Store para beneficiarse de la capacidad estándar de Java (por ejemplo, secuencias impresión para la salida con formato o cualquiera de las secuencias de compresión o cifrado para una capacidad adicional en la parte superior, etc.).

     // create file and write some content
     String filename = "/a/b/c.txt";
     OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
     PrintStream out = new PrintStream(stream);
     for (int i = 1; i <= 10; i++) {
         out.println("This is line #" + i);
         out.format("This is the same line (%d), but using formatted output. %n", i);
     }
     out.close();
    
    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>Paso 4: Compilación y ejecución de la aplicación
1. Para ejecutarla desde un IDE, localícela y presione el botón **Ejecutar**. Para ejecutarla desde Maven, utilice [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. Para generar un archivo jar independiente que puede ejecutar desde la línea de comandos, genere el archivo jar con todas las dependencias incluidas mediante el [complemento de ensamblado de Maven](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). El archivo pom.xml en el [ejemplo de código fuente en github](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) tiene un ejemplo de cómo hacerlo.

## <a name="next-steps"></a>Pasos siguientes
* [Explorar JavaDoc para el SDK de Java](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
* [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)




<!--HONumber=Jan17_HO5-->


