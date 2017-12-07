---
title: Tutorial de MongoDB, React y Node.js para Azure | Microsoft Docs
description: "En esta serie de tutoriales en vídeo, aprenderá a crear una aplicación de MongoDB con React y Node.js en Azure Cosmos DB utilizando las mismas API que para MongoDB."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: 96cd397a7f04fe579b9c6331423abe107ad1c6d9
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="create-a-mongodb-app-with-react-and-azure-cosmos-db"></a>Creación de una aplicación de MongoDB con React y Azure Cosmos DB  

En este videotutorial de varias partes, se muestra cómo crear una aplicación de seguimiento de héroes con un front-end React. La aplicación utiliza Node y Express para el servidor, se conecta a Azure Cosmos DB con la [API de MongoDB](mongodb-introduction.md) y, a continuación, conecta el front-end de React a la parte del servidor de la aplicación. El tutorial también muestra cómo escalar Azure Cosmos DB mediante apuntar y hacer clic en Azure Portal y cómo implementar la aplicación en Internet, de modo que todos puedan realizar un seguimiento de sus héroes favoritos. 

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) admite conexiones de cliente de MongoDB, por lo que puede usarlo en lugar de MongoDB, con el mismo código que para las aplicaciones de MongoDB, pero con ventajas adicionales, como la implementación sencilla en la nube, el escalado y las lecturas y escrituras muy rápidas.  

En este tutorial de varias partes, se abordan las tareas siguientes:

> [!div class="checklist"]
> * Introducción
> * Configuración del proyecto
> * Creación de la interfaz de usuario con React
> * Creación de una cuenta de Azure Cosmos DB mediante Azure Portal
> * Uso de Mongoose para conectarse a Azure Cosmos DB
> * Incorporación de las operaciones React, Create, Update y Delete a la aplicación

¿Quiere crear esta misma aplicación con Angular? Consulte la [serie de vídeos del tutorial de Angular](tutorial-develop-mongodb-nodejs.md).

## <a name="prerequisites"></a>Requisitos previos
* [Node.js](https://www.nodejs.org)

### <a name="finished-project"></a>Proyecto terminado
Obtenga la aplicación completa [desde GitHub](https://github.com/Azure-Samples/react-cosmosdb).

## <a name="introduction"></a>Introducción 

En este vídeo, Burke Holland le ofrece una introducción a Azure Cosmos DB y le guía a través de la aplicación que se crea en esta serie de vídeos. 

> [!VIDEO https://www.youtube.com/embed/58IflnJbYJc]

## <a name="project-setup"></a>Configuración del proyecto

Este vídeo muestra cómo configurar Express y React en el mismo proyecto. Burke ofrece a continuación un tutorial del código en el proyecto.

> [!VIDEO https://www.youtube.com/embed/ytFUPStJJds]

## <a name="build-the-ui"></a>Creación de la interfaz de usuario

Este vídeo muestra cómo crear la interfaz de usuario (UI) de la aplicación con React. 

> [!NOTE]
> Las reglas de CSS a las que se hace referencia en este vídeo se encuentran en el [repositorio de GitHub react-cosmosdb](https://github.com/Azure-Samples/react-cosmosdb/blob/master/src/index.css).

> [!VIDEO https://www.youtube.com/embed/SzHzX0fTUUQ]

## <a name="connect-to-azure-cosmos-db"></a>Conexión a Azure Cosmos DB

Este vídeo muestra cómo crear una cuenta de Azure Cosmos DB en Azure Portal, instalar los paquetes de MongoDB y Mongoose, y, a continuación, conectar la aplicación a la cuenta recién creada con la cadena de conexión de Azure Cosmos DB. 

> [!VIDEO https://www.youtube.com/embed/0U2jV1thfvs]

## <a name="read-and-create-heroes-in-the-app"></a>Lectura y creación de héroes en la aplicación

Este vídeo muestra cómo leer y crear héroes en la base de datos de Azure Cosmos DB, y cómo probar esos métodos con la interfaz de usuario de React y Postman. 

> [!VIDEO https://www.youtube.com/embed/AQK9n_8fsQI] 

## <a name="delete-and-update-heroes-in-the-app"></a>Eliminación y actualización de héroes en la aplicación

Este vídeo muestra cómo eliminar y actualizar héroes desde la aplicación y cómo mostrar las actualizaciones en la interfaz de usuario. 

> [!VIDEO https://www.youtube.com/embed/YmaGT7ztTQM] 

## <a name="complete-the-app"></a>Completar la aplicación

Este vídeo muestra cómo completar la aplicación y finalizar enlazando la interfaz de usuario con la API de back-end. 

> [!VIDEO https://www.youtube.com/embed/TcSm2ISfTu8]

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, use los pasos siguientes para borrar todos los recursos que se crearon en este tutorial en Azure Portal. 

1. En el menú de la izquierda de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del recurso que creó. 
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba en el cuadro de texto el nombre del recurso que quiere eliminar y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Crear una aplicación con React, Node, Express y Azure Cosmos DB 
> * Creación de una cuenta de Azure Cosmos DB
> * Conectar la aplicación a la cuenta de Azure Cosmos DB
> * Probar la aplicación con Postman
> * Ejecutar la aplicación y agregar héroes a la base de datos

Compruebe si hay un vídeo adicional en esta serie de tutoriales que trate la implementación de la aplicación y la replicación global de los datos.

Puede pasar al tutorial siguiente y aprender a importar datos de MongoDB en Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importación de datos de MongoDB a Azure Cosmos DB](mongodb-migrate.md)
 
