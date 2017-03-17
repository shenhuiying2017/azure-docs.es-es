---
title: Uso de mongoimport y mongorestore con Azure DocumentDB | Microsoft Docs
description: "Obtenga información sobre cómo usar mongoimport y mongorestore para migrar datos a una cuenta de DocumentDB: API para MongoDB"
keywords: mongoimport, mongorestore
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 2af8691326550c631e6668890cb0d6b893fa7740
ms.lasthandoff: 03/08/2017


---
# <a name="migrate-data-to-documentdb-by-using-mongoimport-and-mongorestore"></a>Migración de datos a DocumentDB mediante mongoimport y mongorestore
> [!div class="op_single_selector"]
> * [Importar a DocumentDB](documentdb-import-data.md)
> * [Importar a la API para MongoDB](documentdb-mongodb-migrate.md)
>
>

Para migrar datos a una cuenta de DocumentDB: API para MongoDB de Azure, debe hacer lo siguiente:

* Descargar *mongoimport.exe* o *mongorestore.exe* desde el [centro de descarga de MongoDB](https://www.mongodb.com/download-center).
* Obtener la [compatibilidad de DocumentDB para la cadena de conexión de MongoDB](documentdb-connect-mongodb-account.md).

## <a name="before-you-begin"></a>Antes de empezar

* Aumente el rendimiento: la duración de la migración de datos depende de la cantidad de rendimiento configurado para las colecciones. Asegúrese de aumentar el rendimiento para migraciones de datos más grandes. Después de haber completado la migración, reduzca el rendimiento para ahorrar costos. Para obtener más información sobre cómo aumentar el rendimiento en [Azure Portal](https://portal.azure.com), vea [Niveles de rendimiento y planes de tarifa de DocumentDB](documentdb-performance-levels.md).

* Habilite SSL: DocumentDB tiene estrictos estándares y requisitos de seguridad. No olvide habilitar SSL al interactuar con la cuenta. Los procedimientos del resto del artículo incluyen cómo habilitar SSL para *mongoimport* y *mongorestore*.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Búsqueda de la información de la cadena de conexión (host, puerto, nombre de usuario y contraseña)

1. En el panel izquierdo de [Azure Portal](https://portal.azure.com), haga clic en la entrada **NoSQL (DocumentDB)**.
2. En el panel **Suscripciones**, seleccione el nombre de cuenta.
3. En la hoja **Cadena de conexión**, haga clic en **Cadena de conexión**.  
El panel derecho contiene toda la información que necesita para conectarse correctamente a la cuenta.

    ![La hoja "Cadena de conexión"](./media/documentdb-mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-api-for-mongodb-with-mongoimport"></a>Importar datos a la API para MongoDB con mongoimport

Para importar datos a la cuenta de DocumentDB, utilice la siguiente plantilla para ejecutar la importación. Rellene *host*, *nombre de usuario* y *contraseña* con los valores específicos de la cuenta.  

Plantilla:

    mongoimport.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Ejemplo:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-api-for-mongodb-with-mongorestore"></a>Importar datos a la API para MongoDB con mongorestore

Para restaurar datos a la cuenta de DocumentDB, utilice la siguiente plantilla para ejecutar la importación. Rellene *host*, *nombre de usuario* y *contraseña* con los valores específicos de la cuenta.

Plantilla:

    mongorestore.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Ejemplo:

    mongorestore.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07

## <a name="next-steps"></a>Pasos siguientes
* Para más información, explore [Ejemplos de DocumentDB: API para MongoDB](documentdb-mongodb-samples.md).

