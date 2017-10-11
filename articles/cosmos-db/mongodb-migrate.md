---
title: Usar mongoimport y mongorestore con la API de Azure Cosmos DB para MongoDB | Microsoft Docs
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
ms.date: 06/12/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: 1555f13c3ea88b61be0ea240b51218b83f6f9724
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-cosmos-db-import-mongodb-data"></a>Azure Cosmos DB: importar datos de MongoDB 

Para migrar datos de MongoDB a una cuenta de Azure Cosmos DB para su uso con la API de MongoDB, debe hacer lo siguiente:

* Descargar *mongoimport.exe* o *mongorestore.exe* desde el [centro de descarga de MongoDB](https://www.mongodb.com/download-center).
* Obtenga su [cadena de conexión de la API de MongoDB](connect-mongodb-account.md).

Si está importando datos de MongoDB y planea usarlos con Azure Cosmos DB, debe usar la [Herramienta de migración de datos](import-data.md) para importarlos.

En este tutorial se describen las tareas siguientes:

> [!div class="checklist"]
> * Recuperación de la cadena de conexión
> * Importación de datos de MongoDB mediante mongoimport
> * Importación de datos de MongoDB mediante mongorestore

## <a name="prerequisites"></a>Requisitos previos

* Aumente el rendimiento: la duración de la migración de datos depende de la cantidad de rendimiento configurado para las colecciones. Asegúrese de aumentar el rendimiento para migraciones de datos más grandes. Después de haber completado la migración, reduzca el rendimiento para ahorrar costos. Para más información sobre cómo aumentar el rendimiento en [Azure Portal](https://portal.azure.com), consulte [Niveles de rendimiento y planes de tarifa de Azure Cosmos DB](performance-levels.md).

* Habilite SSL: Azure Cosmos DB tiene estrictos estándares y requisitos de seguridad. No olvide habilitar SSL al interactuar con la cuenta. Los procedimientos del resto del artículo incluyen cómo habilitar SSL para mongoimport y mongorestore.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Búsqueda de la información de la cadena de conexión (host, puerto, nombre de usuario y contraseña)

1. En [Azure Portal](https://portal.azure.com), en el panel izquierdo, haga clic en la entrada **Azure Cosmos DB**.
2. En el panel **Suscripciones**, seleccione el nombre de cuenta.
3. En la hoja **Cadena de conexión**, haga clic en **Cadena de conexión**.  
El panel derecho contiene toda la información que necesita para conectarse correctamente a la cuenta.

    ![Hoja Cadena de conexión](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-the-api-for-mongodb-by-using-mongoimport"></a>Importar datos a la API para MongoDB mediante mongoimport

Para importar datos a la cuenta de Azure Cosmos DB, use la plantilla siguiente. Rellene *host*, *nombre de usuario* y *contraseña* con los valores específicos de la cuenta.  

Plantilla:

    mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Ejemplo:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-the-api-for-mongodb-by-using-mongorestore"></a>Importar datos a la API para MongoDB mediante mongorestore

Para restaurar datos en la cuenta de la API para MongoDB, utilice la siguiente plantilla para ejecutar la importación. Rellene *host*, *nombre de usuario* y *contraseña* con los valores específicos de la cuenta.

Plantilla:

    mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Ejemplo:

    mongorestore.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
    
## <a name="guide-for-a-successful-migration"></a>Guía para una migración correcta

1. Cree previamente las colecciones y escálelas:
        
    * De forma predeterminada, Azure Cosmos DB aprovisiona una nueva colección de MongoDB con 1000 unidades de solicitud (RU). Antes de empezar la migración mediante mongoimport, mongorestore o mongomirror, cree todas las colecciones mediante [Azure Portal](https://portal.azure.com) o mediante los controladores y herramientas de MongoDB. Si la colección supera los 10 GB, asegúrese de crear una [colección con particiones](partition-data.md) con una clave de partición adecuada.

    * En [Azure Portal](https://portal.azure.com), aumente el valor de rendimiento de las colecciones de 1000 RU para una colección con una sola partición y 2500 RU para una colección con particiones simplemente para la migración. Gracias al mayor rendimiento, puede evitar la limitación y realizar la migración en menos tiempo. Con la facturación por horas en Azure Cosmos DB, puede reducir el rendimiento inmediatamente después de la migración para ahorrar costes.

2. Calcule la carga de RU aproximada para una sola operación de escritura de documento:

    a. Conéctese a la base de datos MongoDB de Azure Cosmos DB desde el shell de MongoDB. Encontrará instrucciones en [Connect a MongoDB application to Azure Cosmos DB](connect-mongodb-account.md) (Conectar una aplicación de MongoDB a Azure Cosmos DB).
    
    b. Ejecute un comando de inserción de ejemplo en uno de los documentos de ejemplo desde el shell de MongoDB:
    
        ```db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })```
        
    c. Ejecute ```db.runCommand({getLastRequestStatistics: 1})``` y recibirá una respuesta como la siguiente:
     
        ```
        globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
        {
            "_t": "GetRequestStatisticsResponse",
            "ok": 1,
            "CommandName": "insert",
            "RequestCharge": 10,
            "RequestDurationInMilliSeconds": NumberLong(50)
        }
        ```
        
    d. Tome nota de la carga de solicitudes.
    
3. Determine la latencia del equipo en el servicio en la nube de Azure Cosmos DB:
    
    a. Habilite el registro detallado desde el shell de MongoDB mediante este comando: ```setVerboseShell(true)```.
    
    b. Ejecute una consulta simple en la base de datos: ```db.coll.find().limit(1)```. Recibirá una respuesta como la siguiente:

        ```
        Fetched 1 record(s) in 100(ms)
        ```
        
4. Quite el documento insertado antes de la migración para asegurarse de que no hay ningún documento duplicado. Puede quitar los documentos con este comando: ```db.coll.remove({})```.

5. Calcular los valores aproximados de *batchSize* y *numInsertionWorkers*:

    * En el caso de *batchSize*, divida el total de RU aprovisionadas entre las RU consumidas en la operación de escritura de documento del paso 3.
    
    * Si el valor de *batchSize* calculado es <= 24, use ese número como valor de *batchSize*.
    
    * Si el valor de *batchSize* calculado es > 24, establezca *batchSize* en 24.
    
    * En el caso de *numInsertionWorkers*, use esta ecuación: *numInsertionWorkers = (rendimiento aprovisionado * latencia en segundos) / (tamaño del lote * RU usadas en una sola operación de escritura)*.
        
    |Propiedad|Valor|
    |--------|-----|
    |batchSize| 24 |
    |RU aprovisionadas | 10000 |
    |Latency | 0,100 s |
    |RU cargadas en una sola operación de escritura de documento | 10 RU |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10 000 RU x 0,1 s) / (24 x 10 RU) = 4,1666*

6. Ejecute el comando de migración final:

   ```
   mongoimport.exe --host anhoh-mongodb.documents.azure.com:10255 -u anhoh-mongodb -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```

## <a name="next-steps"></a>Pasos siguientes

Puede pasar al tutorial siguiente y obtener más información sobre cómo consultar datos de MongoDB con Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[¿Cómo consultar datos de MongoDB?](../cosmos-db/tutorial-query-mongodb.md)
