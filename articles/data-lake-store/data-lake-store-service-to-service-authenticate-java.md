---
title: "Autenticación entre servicios: Java con Data Lake Store mediante Azure Active Directory | Microsoft Docs"
description: "Aprenda a realizar la autenticación entre servicios con Data Lake Store mediante Azure Active Directory con Java"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: e537d8a6ea53bf4366168727de8ef95b96281d5b
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="service-to-service-authentication-with-data-lake-store-using-java"></a>Autenticación entre servicios con Data Lake Store mediante Java
> [!div class="op_single_selector"]
> * [Uso de Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Uso del SDK de .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Uso de Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Uso de la API de REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

En este artículo, aprenderá a usar el SDK de Java para realizar la autenticación entre servicios con Azure Data Lake Store. No se admite la autenticación de usuario final con Data Lake Store mediante el SDK de Java.

## <a name="prerequisites"></a>Requisitos previos
* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **Cree una aplicación "web" de Azure Active Directory**. Debe haber completado los pasos descritos en [Service-to-service authentication with Data Lake Store using Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md) (Autenticación entre servicios con Data Lake Store mediante Azure Active Directory).

* [Maven](https://maven.apache.org/install.html). Este tutorial usa Maven para crear las dependencias de un proyecto. Aunque es posible generarlas sin utilizar un sistema como Maven o Gradle, estos sistemas facilitan mucho la administración de las dependencias.

* (Opcional) Y un IDE como [IntelliJ IDEA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) o similar.

## <a name="service-to-service-authentication"></a>Autenticación entre servicios
1. Cree un proyecto de Maven mediante [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) en la línea de comandos o con un entorno de desarrollo integrado. Para ver instrucciones sobre cómo crear un proyecto de Java mediante IntelliJ, consulte [este artículo](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para ver instrucciones sobre cómo crear un proyecto con Eclipse, consulte [este artículo](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Agregue las siguientes dependencias a su archivo **pom.xml** de Maven. Agregue el siguiente fragmento de código antes de la etiqueta **\</project>**:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.2.3</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    La primera dependencia es el uso del SDK de Data Lake Store (`azure-data-lake-store-sdk`) desde el repositorio de maven. La segunda dependencia consiste en especificar qué plataforma de registro (`slf4j-nop`) se usará para esta aplicación. El SDK de Data Lake Store usa la fachada de registro [slf4j](http://www.slf4j.org/), que permite elegir entre una serie de plataformas de registro populares, como log4j, registro de Java, logback, etc., o no registrarse. En este ejemplo, se deshabilita el registro; por tanto, se usa el enlace **slf4j-nop**. Para usar otras opciones de registro en su aplicación, consulte [este artículo](http://www.slf4j.org/manual.html#projectDep).

3. Agregue las siguientes instrucciones de importación a la aplicación.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

4. Utilice el siguiente fragmento de código de la aplicación de Java para obtener el token para la aplicación web de Active Directory que se ha creado anteriormente con una de las subclases de `AccessTokenProvider` (el siguiente ejemplo usa `ClientCredsTokenProvider`). El proveedor de tokens almacena en caché las credenciales que se utilizan para obtener el token en la memoria y renueva el token de forma automática si está a punto de expirar. Puede crear sus propias subclases de `AccessTokenProvider` para que los tokens se obtengan mediante su código de cliente. Por ahora vamos a usar simplemente el que se proporciona en el SDK.

    Reemplace **FILL-IN-HERE** por los valores reales de la aplicación web de Azure Active Directory.

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

El SDK de Data Lake Store proporciona métodos útiles que permiten obtener los tokens de seguridad necesarios para comunicarse con la cuenta de Data Lake Store. Sin embargo, el SDK no obliga a que se usen solo estos métodos. También puede usar cualquier otro medio de obtención del token, como el [SDK de Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) o su propio código personalizado.

## <a name="next-steps"></a>pasos siguientes
En este artículo, ha aprendido a usar la autenticación de usuario final para autenticarse en Azure Data Lake Store mediante el SDK de Java. Ahora puede consultar los siguientes artículos, que tratan sobre cómo usar el SDK de Java con Azure Data Lake Store.

* [Operaciones de datos en Data Lake Store mediante el SDK de Java](data-lake-store-get-started-java-sdk.md)


