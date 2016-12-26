---
title: "Exportación de certificados del Emulador de DocumentDB | Microsoft Docs"
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
ms.date: 11/29/2016
ms.author: tvoellm
translationtype: Human Translation
ms.sourcegitcommit: c47ff7045a7c69f2f4a0235fa591a6a8cc820192
ms.openlocfilehash: dc3d297f44fb99b6fad58810eb31f429fdb1bd25

---

# <a name="export-the-documentdb-emulator-certificates"></a>Exportación de certificados del Emulador de DocumentDB

[**Descarga del Emulador**](https://aka.ms/documentdb-emulator)

El Emulador de Azure DocumentDB proporciona un entorno local que emula el servicio de Azure DocumentDB con fines de desarrollo, incluido su uso de las conexiones SSL. En esta publicación se muestra cómo exportar los certificados SSL para su uso en lenguajes y tiempos de ejecución que no se integran con el almacén de certificados de Windows, como Java, que utiliza su propio [almacén de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), y Python, que usa [contenedores de sockets](https://docs.python.org/2/library/ssl.html). Puede leer más acerca del emulador en el artículo "[Use the Azure DocumentDB Emulator for development and testing](./documentdb-nosql-local-emulator.md)" (Uso del Emulador de Azure DocumentDB para desarrollo y pruebas).

# <a name="certification-rotation"></a>Rotación de certificación

Los certificados del Emulador local de DocumentDB se generan la primera vez que se ejecuta el emulador. Hay dos certificados. Uno se utiliza para la conexión con el emulador local y otro para la administración de secretos en el emulador. El certificado que desea exportar es el certificado de conexión con el nombre descriptivo "DocumentDBEmulatorCertificate".

Ambos certificados se pueden regenerar; para ello, haga clic en "Restablecer datos" tal y como se muestra a continuación desde el Emulador local de DocumentDB que se ejecuta en la Bandeja de Windows. Si regenera los certificados y los tiene instalados en el almacén de certificados de Java o los usa en otro lugar, deberá actualizarlos; en caso contrario, la aplicación ya no se conectará al emulador local.

![Restablecer datos en el Emulador local de DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-reset-data.png)

# <a name="how-to-export-the-documentdb-ssl-certificate"></a>Exportación del certificado SSL de DocumentDB

- Paso 1: inicie el administrador de certificados de Windows; para ello, ejecute certlm.msc, vaya a la carpeta Personal-> Certificados y abra el certificado con el nombre descriptivo "DocumentDBEmulatorCertificate".

![Paso 1 de exportación de un Emulador de DocumentDB local](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-1.png)

- Paso 2: haga clic en "Detalles" y, a continuación, en "Aceptar".

![Paso 1 de exportación de un Emulador de DocumentDB local](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-2.png)

- Paso 3: haga clic en "Copiar a archivo".

![Paso 1 de exportación de un Emulador de DocumentDB local](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-3.png)

- Paso 4: haga clic en "Siguiente".

![Paso 1 de exportación de un Emulador de DocumentDB local](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-4.png)

- Paso 5: haga clic en "No, no exportar la clave privada" y, a continuación, haga clic en "Siguiente".

![Paso 1 de exportación de un Emulador de DocumentDB local](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-5.png)

- Paso 6: haga clic en "X.509 codificado base 64 (. CER)" y luego en "Siguiente".

![Paso 1 de exportación de un Emulador de DocumentDB local](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-6.png)

- Paso 7: asigne un nombre al certificado. En este caso, "documentdbemulatorcert" y, a continuación, haga clic en "Siguiente".

![Paso 1 de exportación de un Emulador de DocumentDB local](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-7.png)

- Paso 8: haga clic en "Finalizar".

![Paso 1 de exportación de un Emulador de DocumentDB local](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-8.png)

# <a name="how-to-use-the-certificate-in-java"></a>Uso del certificado en Java

Al ejecutar aplicaciones de Java o MongoDB que utilizan el cliente de Java, es más fácil instalar el certificado en el almacén de certificados predeterminado de Java que pasar las marcas "-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>. Por ejemplo, la [aplicación de demostración de Java](https://localhost:8081/_explorer/index.html) incluida depende del almacén de certificados predeterminado.

Siga las instrucciones de la publicación "[Incorporación de un certificado al almacén de certificados CA de Java](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store)" para importar el certificado X.509 en el almacén de certificados de Java. Tenga en cuenta que trabajará en el directorio %JAVA_HOME% al ejecutar keytool.

Una vez que el certificado SSL "DocumentDBEmulatorCertificate" está instalado en la aplicación, debe poder conectarse y utilizar el Emulador de DocumentDB local. Si sigue teniendo problemas, puede seguir el artículo "[Debugging SSL/TLS Connections](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html)" (Depuración de conexiones SSL/TLS). Es muy probable que el certificado no esté instalado en el almacén %JAVA_HOME%/jre/lib/security/cacerts. Por ejemplo, si tiene varias versiones instaladas de Java, es probable que la aplicación esté utilizando un almacén de certificados CA diferente del actualizado.

# <a name="how-to-use-the-certificate-in-python"></a>Uso del certificado en Python

De forma predeterminada, el SDK de Python para DocumentDB no probará ni usará el certificado SSL al conectar con el emulador local. Sin embargo, si desea utilizar la validación de SSL, puede seguir los ejemplos de la documentación de los [contenedores de sockets de Python](https://docs.python.org/2/library/ssl.html).



<!--HONumber=Nov16_HO5-->


