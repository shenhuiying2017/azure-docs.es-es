---
title: Usar el marco Mongoose con Azure Cosmos DB | Microsoft Docs
description: "Obtenga información sobre cómo conectar una aplicación Mongoose de Node.js a Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: romitgirdhar
manager: jhubbard
editor: 
ms.assetid: de5eea58-ee7c-4609-b1c9-4af3e61a5883
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: rogirdh
ms.openlocfilehash: 9878936b5dd76730633dec16b1c3a3eaac78e95a
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="azure-cosmos-db-using-the-mongoose-framework-with-azure-cosmos-db"></a>Azure Cosmos DB: usar el marco Mongoose con Azure Cosmos DB

En este tutorial se muestra cómo usar el [marco Mongoose](http://mongoosejs.com/) al almacenar datos en Azure Cosmos DB. Usaremos MongoDB API de Azure Cosmos DB para este tutorial. Si no le resulta familiar, Mongoose es un marco de modelado de objetos de MongoDB en Node.js que proporciona una solución sencilla y basada en esquemas para dar forma a los datos de la aplicación.

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, clave-valor y grafos, y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escala horizontal en Azure Cosmos DB.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) versión v0.10.29 o superior

## <a name="create-an-azure-cosmos-db-account"></a>Creación de una cuenta de Azure Cosmos DB

Vamos a crear una cuenta de Azure Cosmos DB. Si ya tiene una cuenta que desea usar, puede ir directamente a [Configuración de la aplicación de Node.js](#SetupNode). Si usa el emulador de Azure Cosmos DB, siga los pasos que se indican en [Emulador de Azure Cosmos DB](local-emulator.md) para configurar el emulador y vaya directamente a [Configuración de la aplicación de Node.js](#SetupNode).

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="set-up-your-nodejs-application"></a>Configurar la aplicación de Node.js

>[!Note]
> Si quiere usar el código de ejemplo en lugar de configurar la propia aplicación, clone el [ejemplo](https://github.com/Azure-Samples/Mongoose_CosmosDB) que se usa en este tutorial y compile su propia aplicación Node.js de Mongoose en Azure Cosmos DB.

1. Para crear una aplicación Node.js en una carpeta de su elección, ejecute el comando siguiente en un símbolo del sistema de nodo.

    ```npm init```

    Responda a las preguntas y su proyecto estará listo.

1. Agregue un nuevo archivo a la carpeta y asígnele el nombre ```index.js```.
1. Instale los paquetes necesarios mediante una de las opciones ```npm install```:
    * Mongoose: ```npm install mongoose --save```
    * Dotenv (si quiere cargar los secretos de un archivo .env): ```npm install dotenv --save```
    
    >[!Note]
    > La marca ```--save``` agrega la dependencia al archivo package.json.

1. Importe las dependencias en el archivo index.js.
    ```JavaScript
    var mongoose = require('mongoose');
    var env = require('dotenv').load();    //Use the .env file to load the variables
    ```

1. Agregue la cadena de conexión y el nombre de Cosmos DB al archivo ```.env```.

    ```JavaScript
    COSMOSDB_CONNSTR={Your MongoDB Connection String Here}
    COSMOSDB_DBNAME={Your DB Name Here}
    ```

1. Conéctese a Azure Cosmos DB mediante el marco Mongoose; para ello, debe agregar el siguiente código al final del archivo index.js.
    ```JavaScript
    mongoose.connect(process.env.COSMOSDB_CONNSTR+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb"); //Creates a new DB, if it doesn't already exist

    var db = mongoose.connection;
    db.on('error', console.error.bind(console, 'connection error:'));
    db.once('open', function() {
    console.log("Connected to DB");
    });
    ```
    >[!Note]
    > En este caso, las variables de entorno se cargan mediante process.env.{variableName} con el paquete de npm "dotenv".

    Una vez que esté conectado a Azure Cosmos DB, puede empezar a configurar los modelos de objetos en Mongoose.
    
## <a name="caveats-to-using-mongoose-with-azure-cosmos-db"></a>Advertencias sobre el uso Mongoose con Azure Cosmos DB

Por cada modelo que se crea, Mongoose crea por otro lado una nueva colección de MongoDB. Sin embargo, si se tiene en cuenta el modelo de facturación por colección de Azure Cosmos DB, esta no es la forma más eficaz de trabajar si resulta que tiene varios modelos de objetos que apenas se rellenan.

En este tutorial se describen ambos modelos. En primer lugar, le proporcionaremos datos acerca de cómo almacenar un tipo de datos por colección. Este es el comportamiento de facto de Mongoose.

Asimismo, Mongoose también tiene un concepto que se denomina [Discriminadores](http://mongoosejs.com/docs/discriminators.html). Los discriminadores son un mecanismo de herencia de esquemas. Le permiten tener varios modelos con esquemas superpuestos en la misma colección subyacente de MongoDB.

Puede almacenar los distintos modelos de datos en la misma colección y, a continuación, usar una cláusula de filtro a la hora de realizar la consulta para así descargar solo los datos necesarios.

### <a name="one-collection-per-object-model"></a>Una colección por modelo de objetos

El comportamiento predeterminado de Mongoose consiste en crear una colección de MongoDB cada vez que se cree un modelo de objetos. En esta sección le indicaremos cómo puede lograrlo gracias a MongoDB para Azure Cosmos DB. Le recomendamos que use este método con Azure Cosmos DB cuando tenga modelos de objetos con grandes cantidades de datos. Este es el modelo de funcionamiento predeterminado de Mongoose, por lo tanto es posible que ya esté familiarizado con él si ya conoce Mongoose.

1. Abra el archivo ```index.js``` de nuevo.

1. Cree la definición de esquema para "Family".

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. Cree un objeto para "Family".

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Por último, guarde el objeto en Azure Cosmos DB. Esta opción crea una colección en segundo plano.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. A continuación, cree otro esquema y objeto. Esta vez vamos a crear uno para "Vacation Destinations" que pueden ser destinos de interés para las familias.
    1. Igual que la última vez, cree el esquema.
    ```JavaScript
    const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
        name: String,
        country: String
    }));
    ```

    1. Cree un objeto de ejemplo (puede agregar varios objetos a este esquema) y guárdelo.
    ```JavaScript
    const vacaySpot = new VacationDestinations({
        name: "Honolulu",
        country: "USA"
    });

    vacaySpot.save((err, saveVacay) => {
        console.log(JSON.stringify(saveVacay));
    });
    ```

1. A continuación, vaya a Azure Portal y verá dos colecciones que se crearon en Azure Cosmos DB.

    ![Tutorial de Node.js - Captura de pantalla de Azure Portal, que muestra una cuenta de Azure Cosmos DB con el nombre de la colección resaltado - Base de datos del nodo][alldata]

1. Por último, lea los datos de Azure Cosmos DB. Puesto que vamos a usar el modelo de funcionamiento predeterminado de Mongoose, las lecturas son las mismas que cualquier otra en Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Usar discriminadores de Mongoose para almacenar datos en una sola colección

En este método, se usan los [discriminadores de Mongoose](http://mongoosejs.com/docs/discriminators.html) para optimizar los costos de cada colección de Azure Cosmos DB. Los discriminadores le permiten definir una "Clave" diferenciadora que a su vez le permite almacenar, diferenciar y filtrar los diferentes modelos de objetos.

En este caso, crearemos un modelo de objeto base, definiremos una clave diferenciadora y agregaremos "Family" y "VacationDestinations" a modo de extensión para el modelo base.

1. Realice la configuración base y defina la clave del discriminador.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. A continuación, defina el modelo de objetos comunes.

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. Defina el modelo "Family". Tenga en cuenta que estamos usando ```commonModel.discriminator``` en lugar de ```mongoose.model```. Además, también agregaremos la configuración base al esquema de Mongoose. Por lo tanto, en este caso, la clave discriminatorKey es ```FamilyType```.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. De forma similar, agregaremos otro esquema; esta vez para "VacationDestinations". En este caso, la clave DiscriminatorKey es ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. Por último, crearemos objetos para el modelo y lo guardaremos.
    1. Agreguemos objetos al modelo "Family".
    ```JavaScript
    const family_common = new Family_common({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });

    family_common.save((err, saveFamily) => {
        console.log("Saved: " + JSON.stringify(saveFamily));
    });
    ```

    1. A continuación, vamos a agregar objetos al modelo "VacationDestinations" y lo guardaremos.
    ```JavaScript
    const vacay_common = new Vacation_common({
        name: "Honolulu",
        country: "USA"
    });

    vacay_common.save((err, saveVacay) => {
        console.log("Saved: " + JSON.stringify(saveVacay));
    });
    ```

1. Si vuelve a Azure Portal, verá que solo tiene una colección denominada ```alldata``` con los datos de "Family" y "VacationDestinations".

    ![Tutorial de Node.js - Captura de pantalla de Azure Portal, que muestra una cuenta de Azure Cosmos DB con el nombre de la colección resaltado - Base de datos del nodo][mutiple-coll]

1. Además, tenga en cuenta que cada objeto tiene otro atributo denominado "```__type```", que le ayudará a diferenciar entre los dos modelos de objetos diferentes.

1. Por último, lea los datos que se guardan en Azure Cosmos DB. Mongoose se encarga de filtrar los datos basados en el modelo. Por lo tanto, no tiene que hacer nada al leer los datos. Simplemente especifique el modelo (en este caso, ```Family_common```) y Mongoose se encargará de los filtros en "DiscriminatorKey".

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Como puede ver, es muy fácil trabajar con discriminadores de Mongoose. Así que, si tiene una aplicación que usa el marco Mongoose, este tutorial es una manera de desarrollar la aplicación y ejecutarla en MongoDB API en Azure Cosmos DB sin tener que hacer demasiados cambios.

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>pasos siguientes

Obtenga más información sobre las operaciones, operadores, fases, comandos y opciones de MongoDB que admite MongoDB API de Azure Cosmos DB en [Compatibilidad de MongoDB API con la sintaxis y las características de MongoDB](mongodb-feature-support.md).

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[mutiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png