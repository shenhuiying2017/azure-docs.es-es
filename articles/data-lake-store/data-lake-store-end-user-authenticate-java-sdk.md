---
title: "Autenticación de usuario final: Java con Data Lake Store en Azure Active Directory | Microsoft Docs"
description: "Aprenda a lograr la autenticación del usuario final con Data Lake Store mediante Azure Active Directory con Java"
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
ms.openlocfilehash: b1fbf3c1e0e5715ff7effcdac45ff03f2b55f9ee
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="end-user-authentication-with-data-lake-store-using-java"></a>Autenticación de usuario final con Data Lake Store mediante Java
> [!div class="op_single_selector"]
> * [Uso de Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Uso del SDK de .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Uso de Python](data-lake-store-end-user-authenticate-python.md)
> * [Uso de la API de REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

En este artículo, aprenderá a usar el SDK de Java para realizar la autenticación de usuario final con Azure Data Lake Store. Para la autenticación entre servicios con Data Lake Store mediante el SDK de Java, consulte [Service-to-service authentication with Data Lake Store using Java](data-lake-store-service-to-service-authenticate-java.md) (Autenticación entre servicios con Data Lake Store mediante Java).

## <a name="prerequisites"></a>Requisitos previos
* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **Cree una aplicación "nativa" de Azure Active Directory**. Debe haber completado los pasos descritos en [Autenticación de usuario final con Data Lake Store mediante Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Este tutorial usa Maven para crear las dependencias de un proyecto. Aunque es posible generarlas sin utilizar un sistema como Maven o Gradle, estos sistemas facilitan mucho la administración de las dependencias.

* (Opcional) Y un IDE como [IntelliJ IDEA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) o similar.

## <a name="end-user-authentication"></a>Autenticación de usuario final
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
        import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;

4. Utilice el siguiente fragmento de código de la aplicación de Java para obtener el token para la aplicación nativa de Active Directory que se ha creado anteriormente con el `DeviceCodeTokenProvider`. Reemplace **FILL-IN-HERE** por los valores reales de la aplicación nativa de Azure Active Directory.

        private static String nativeAppId = "FILL-IN-HERE";
            
        AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   

El SDK de Data Lake Store proporciona métodos útiles que permiten obtener los tokens de seguridad necesarios para comunicarse con la cuenta de Data Lake Store. Sin embargo, el SDK no obliga a que se usen solo estos métodos. También puede usar cualquier otro medio de obtención del token, como el [SDK de Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) o su propio código personalizado.

## <a name="next-steps"></a>pasos siguientes
En este artículo, ha aprendido a usar la autenticación de usuario final para autenticarse en Azure Data Lake Store mediante el SDK de Java. Ahora puede consultar los siguientes artículos, que tratan sobre cómo usar el SDK de Java con Azure Data Lake Store.

* [Operaciones de datos en Data Lake Store mediante el SDK de Java](data-lake-store-get-started-java-sdk.md)


