---
title: Exportar los certificados del Emulador de DocumentDB | Microsoft Docs
description: "Al desarrollar en lenguajes y tiempos de ejecución que no utilizan el almacén de certificados de Windows, debe exportar y administrar los certificados SSL. Esta publicación proporciona instrucciones detalladas para ello."
services: documentdb
documentationcenter: 
keywords: Emulador de DocumentDB
author: voellm
manager: jhubbard
editor: 
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2017
ms.author: tvoellm
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 5f2fd8810041d47be2ef0b21e1487426a643d541
ms.lasthandoff: 03/07/2017


---

# <a name="export-the-documentdb-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Exportar los certificados del Emulador de DocumentDB para su uso con Java, Python y Node.js

[**Descarga del Emulador**](https://aka.ms/documentdb-emulator)

El Emulador de Azure DocumentDB proporciona un entorno local que emula el servicio de Azure DocumentDB con fines de desarrollo, incluido su uso de las conexiones SSL. En esta publicación se muestra cómo exportar los certificados SSL para su uso en lenguajes y entornos de ejecución que no se integran con el almacén de certificados de Windows, como Java, que utiliza su propio [almacén de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), Python, que usa [contenedores de sockets](https://docs.python.org/2/library/ssl.html) y Node.js, que usa [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Puede leer más sobre el emulador en el artículo [Uso del Emulador de Azure DocumentDB para desarrollo y pruebas](./documentdb-nosql-local-emulator.md).

## <a name="certification-rotation"></a>Rotación de certificación

Los certificados del Emulador local de DocumentDB se generan la primera vez que se ejecuta el emulador. Hay dos certificados. Uno se utiliza para la conexión con el emulador local y otro para la administración de secretos en el emulador. El certificado que desea exportar es el certificado de conexión con el nombre descriptivo "DocumentDBEmulatorCertificate".

Ambos certificados se pueden regenerar; para ello, haga clic en **Restablecer datos** tal y como se muestra a continuación desde el Emulador de DocumentDB que se ejecuta en la Bandeja de Windows. Si regenera los certificados y los tiene instalados en el almacén de certificados de Java o los usa en otro lugar, deberá actualizarlos; en caso contrario, la aplicación ya no se conectará al emulador local.

![Restablecer datos en el Emulador local de DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-reset-data.png)

## <a name="how-to-export-the-documentdb-ssl-certificate"></a>Exportación del certificado SSL de DocumentDB

1. Inicie el administrador de certificados de Windows; para ello, ejecute certlm.msc, vaya a la carpeta Personal-> Certificados y abra el certificado con el nombre descriptivo **DocumentDBEmulatorCertificate**.

    ![Paso 1 de exportación de un Emulador de DocumentDB local](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-1.png)

2. Haga clic en **Detalles** y, luego, en **Aceptar**.

    ![Paso 2 de exportación de un Emulador de DocumentDB local](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-2.png)

3. Haga clic en **Copiar en archivo... **.

    ![Paso 3 de exportación de un Emulador de DocumentDB local](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-3.png)

4. Haga clic en **Next**.

    ![Paso 4 de exportación de un Emulador de DocumentDB local](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-4.png)

5. Haga clic en **No exportar la clave privada** y, luego, haga clic en **Siguiente**.

    ![Paso 5 de exportación de un Emulador de DocumentDB local](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-5.png)

6. Haga clic en **X.509 codificado base&64; (. CER)** y luego en **Siguiente**.

    ![Paso 6 de exportación de un Emulador de DocumentDB local](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-6.png)

7. Asigne un nombre al certificado. En este caso, **documentdbemulatorcert** y, luego, haga clic en **Siguiente**.

    ![Paso 7 de exportación de un Emulador de DocumentDB local](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-7.png)

8. Haga clic en **Finalizar**

    ![Paso 8 de exportación de un Emulador de DocumentDB local](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Uso del certificado en Java

Al ejecutar aplicaciones de Java o MongoDB que utilizan el cliente de Java, es más fácil instalar el certificado en el almacén de certificados predeterminado de Java que pasar las marcas "-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>. Por ejemplo, la [aplicación de demostración de Java](https://localhost:8081/_explorer/index.html) incluida depende del almacén de certificados predeterminado.

Siga las instrucciones de la publicación [Incorporación de un certificado al almacén de certificados CA de Java](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store) para importar el certificado X.509 en el almacén de certificados de Java. Tenga en cuenta que trabajará en el directorio %JAVA_HOME% al ejecutar keytool.

Una vez que el certificado SSL "DocumentDBEmulatorCertificate" está instalado en la aplicación, debe poder conectarse y utilizar el Emulador de DocumentDB local. Si sigue teniendo problemas, puede seguir los pasos indicados en el artículo [Debugging SSL/TLS Connections](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) (Depuración de conexiones SSL/TLS). Es muy probable que el certificado no esté instalado en el almacén %JAVA_HOME%/jre/lib/security/cacerts. Por ejemplo, si tiene varias versiones instaladas de Java, es probable que la aplicación esté utilizando un almacén de certificados CA diferente del actualizado.

## <a name="how-to-use-the-certificate-in-python"></a>Uso del certificado en Python

De forma predeterminada, el [SDK de Python (versión 2.0.0 o posterior)](https://docs.microsoft.com/en-us/azure/documentdb/documentdb-sdk-python) para DocumentDB no probará ni usará el certificado SSL al conectarse con el emulador local. Sin embargo, si desea utilizar la validación de SSL, puede seguir los ejemplos de la documentación de los [contenedores de sockets de Python](https://docs.python.org/2/library/ssl.html).

## <a name="how-to-use-the-certificate-in-nodejs"></a>Uso del certificado en Node.js

De forma predeterminada, el [SDK de Node.js (versión 1.10.1 o posterior)](https://docs.microsoft.com/en-us/azure/documentdb/documentdb-sdk-node) para DocumentDB no probará ni usará el certificado SSL al conectarse con el emulador local. Sin embargo, si desea utilizar la validación de SSL, puede seguir los ejemplos de la [documentación de Node.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información acerca de DocumentDB, vea [Introduction to Azure DocumentDB](documentdb-introduction.md) (Introducción a Azure DocumentDB).
* Para empezar a desarrollar en el Emulador de DocumentDB, descargue uno de los [SDK de DocumentDB admitidos](documentdb-sdk-dotnet.md).

