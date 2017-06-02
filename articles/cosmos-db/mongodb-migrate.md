---
title: Uso de mongoimport y mongorestore con la API de Azure Cosmos DB para MongoDB | Microsoft Docs
description: "Obtenga información sobre cómo usar mongoimport y mongorestore para importar datos a una API para la cuenta de MongoDB"
keywords: mongoimport, mongorestore
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: anhoh
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: ab4fb39f8784a2d6aa956056a30649aab1d08487
ms.contentlocale: es-es
ms.lasthandoff: 05/31/2017

---

# <a name="azure-cosmos-db-how-to-import-mongodb-data"></a>Azure Cosmos DB: ¿Cómo importar datos de MongoDB? 

Para migrar datos de MongoDB a una cuenta de Azure Cosmos DB para su uso con la API de MongoDB, debe:

* Descargar *mongoimport.exe* o *mongorestore.exe* desde el [centro de descarga de MongoDB](https://www.mongodb.com/download-center).
* Obtenga su [cadena de conexión de la API de MongoDB](connect-mongodb-account.md).

Si está importando datos de MongoDB y planea su uso con la API de DocumentDB, debe usar la herramienta de migración de datos para importar datos. Para más información, consulte [Herramienta de migración de datos](import-data.md).

En este tutorial se describen las tareas siguientes:

> [!div class="checklist"]
> * Recuperación de la cadena de conexión
> * Importación de datos de MongoDB mediante mongoimport
> * Importación de datos de MongoDB mediante mongorestore

## <a name="prerequisites"></a>Requisitos previos

* Aumente el rendimiento: la duración de la migración de datos depende de la cantidad de rendimiento configurado para las colecciones. Asegúrese de aumentar el rendimiento para migraciones de datos más grandes. Después de haber completado la migración, reduzca el rendimiento para ahorrar costos. Para más información sobre cómo aumentar el rendimiento en [Azure Portal](https://portal.azure.com), consulte [Niveles de rendimiento y planes de tarifa de Azure Cosmos DB](performance-levels.md).

* Habilite SSL: Azure Cosmos DB tiene estrictos estándares y requisitos de seguridad. No olvide habilitar SSL al interactuar con la cuenta. Los procedimientos del resto del artículo incluyen cómo habilitar SSL para *mongoimport* y *mongorestore*.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Búsqueda de la información de la cadena de conexión (host, puerto, nombre de usuario y contraseña)

1. En [Azure Portal](https://portal.azure.com), en el panel izquierdo, haga clic en la entrada **Azure Cosmos DB**.
2. En el panel **Suscripciones**, seleccione el nombre de cuenta.
3. En la hoja **Cadena de conexión**, haga clic en **Cadena de conexión**.  
El panel derecho contiene toda la información que necesita para conectarse correctamente a la cuenta.

    ![La hoja "Cadena de conexión"](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-api-for-mongodb-with-mongoimport"></a>Importar datos a la API para MongoDB con mongoimport

Para importar datos a la cuenta de Azure Cosmos DB, utilice la siguiente plantilla para ejecutar la importación. Rellene *host*, *nombre de usuario* y *contraseña* con los valores específicos de la cuenta.  

Plantilla:

    mongoimport.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Ejemplo:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-api-for-mongodb-with-mongorestore"></a>Importar datos a la API para MongoDB con mongorestore

Para restaurar datos en la cuenta de la API para MongoDB, utilice la siguiente plantilla para ejecutar la importación. Rellene *host*, *nombre de usuario* y *contraseña* con los valores específicos de la cuenta.

Plantilla:

    mongorestore.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Ejemplo:

    mongorestore.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha hecho lo siguiente:

> [!div class="checklist"]
> * Ha recuperado la cadena de conexión
> * Ha importado los datos de MongoDB mediante mongoimport
> * Ha importado los datos de MongoDB mediante mongorestore

Ahora puede pasar al siguiente tutorial y obtener más información sobre cómo consultar los datos de MongoDB con Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[¿Cómo consultar datos de MongoDB?](../cosmos-db/tutorial-query-mongodb.md)

