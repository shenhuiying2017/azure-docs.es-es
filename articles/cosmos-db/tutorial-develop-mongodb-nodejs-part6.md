---
title: Tutorial de MongoDB, Angular y Node para Azure (parte 6) | Microsoft Docs
description: "Parte 6 de la serie de tutoriales en la que se explica cómo crear una aplicación de MongoDB con Angular y Node en Azure Cosmos DB utilizando las mismas API que para MongoDB"
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
ms.topic: hero-article
ms.date: 09/05/2017
ms.author: mimig
ms.openlocfilehash: 4dee49f99118cc99c21ce23e32db3686c58cf4a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-6-add-post-put-and-delete-functions-to-the-app"></a>Creación de una aplicación de MongoDB con Angular y Azure Cosmos DB (parte 6): incorporación de las funciones Post, Put y Delete a la aplicación

Este tutorial de varias partes muestra cómo crear una nueva aplicación de la [API de MongoDB](mongodb-introduction.md) escrita en Node.js con Express y Angular, y conectarla con la base de datos de Azure Cosmos DB.

La parte 6 del tutorial se basa en la [parte 5](tutorial-develop-mongodb-nodejs-part5.md) y aborda las tareas siguientes:

> [!div class="checklist"]
> * Creación de las funciones Post, Put y Delete para el servicio Hero
> * Ejecución de la aplicación

> [!VIDEO https://www.youtube.com/embed/Y5mdAlFGZjc]

## <a name="prerequisites"></a>Requisitos previos

Antes de iniciar esta parte del tutorial, asegúrese de que ha completado los pasos descritos en la [parte 5](tutorial-develop-mongodb-nodejs-part5.md) del tutorial.

> [!TIP]
> Este tutorial le guía por los pasos necesarios para crear la aplicación paso a paso. Si desea descargar el proyecto finalizado, puede obtener la aplicación completa en el [repositorio de angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) en GitHub.

## <a name="add-a-post-function-to-the-hero-service"></a>Incorporación de una función Post al servicio Hero

1. En Visual Studio Code, abra **routes.js** y **hero.service.js** al mismo tiempo, presionando el botón **Dividir editor** ![Botón Dividir editor de Visual Studio ](./media/tutorial-develop-mongodb-nodejs-part6/split-editor-button.png).

    Observe que la línea 7 de routes.js está llamando a la función `getHeroes` en la línea 5 de **hero.service.js**.  Necesitamos crear este mismo emparejamiento para las funciones post, put y delete. 

    ![routes.js y hero.service.js en Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/routes-heroservicejs.png)
    
    Empecemos por codificar el servicio Hero. 

2. Copie el código siguiente en **hero.service.js** después de la función `getHeroes` y antes de `module.exports`. Este código:  
   * Usa el modelo de Hero para registrar un nuevo componente Hero.
   * Comprueba las respuestas para ver si hay un error y devuelve el valor de estado 500.

   ```javascript
   function postHero(req, res) {
     const originalHero = { id: req.body.id, name: req.body.name, saying: req.body.saying };
     const hero = new Hero(originalHero);
     hero.save(error => {
       if (checkServerError(res, error)) return;
       res.status(201).json(hero);
       console.log('Hero created successfully!');
     });
   }

   function checkServerError(res, error) {
     if (error) {
       res.status(500).send(error);
       return error;
     }
   }
   ```

3. En **hero.service.js**, actualice `module.exports` para incluir la nueva función `postHero`. 

    ```javascript
    module.exports = {
      getHeroes,
      postHero
    };
    ```

4. En **routes.js**, agregue un enrutador para la función `post` después del enrutador `get`. Este enrutador publica los héroes de uno en uno. Al estructurar el archivo de enrutador de esta forma, se muestran limpiamente todos los puntos de conexión de la API disponibles y se deja el trabajo real para el archivo **hero.service.js**.

    ```javascript
    router.post('/hero', (req, res) => {
      heroService.postHero(req, res);
    });
    ```

5. Ejecute la aplicación para comprobar que todo funcionó. En Visual Studio Code, guarde los cambios, haga clic en el botón **Depurar** ![Icono Depurar en Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/debug-button.png) en el lado izquierdo y haga clic en el botón **Iniciar depuración** ![Icono Iniciar depuración en Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/start-debugging-button.png).

6. Ahora vuelva atrás en el explorador de Internet y abra la pestaña Red de las herramientas de desarrollador; en la mayoría de los equipos, debe presionar F12. Vaya a [http://localhost:3000](http://localhost:3000) para ver las llamadas realizadas a través de la red.

    ![Pestaña Funciones de red en Chrome que muestra la actividad de red](./media/tutorial-develop-mongodb-nodejs-part6/add-new-hero.png)

7. Agregue un nuevo héroe; para ello, haga clic en el botón **Add New Hero** (Agregar nuevo héroe). Escriba el identificador "999", el nombre "Fred" y el mensaje "Hello"; y haga clic en **Guardar**. En la pestaña Red debería ver que ha enviado una solicitud POST para el nuevo héroe. 

    ![Pestaña Funciones de red de Chrome que muestra la actividad de red para las funciones Get y Post](./media/tutorial-develop-mongodb-nodejs-part6/post-new-hero.png)

    Ahora volvamos y agregue las funciones Put y Delete a la aplicación.

## <a name="add-the-put-and-delete-functions"></a>Incorporación de las funciones Put y Delete

1. En **routes.js**, agregue los enrutadores `put` y `delete` tras el enrutador post.

    ```javascript
    router.put('/hero/:id', (req, res) => {
      heroService.putHero(req, res);
    });

    router.delete('/hero/:id', (req, res) => {
      heroService.deleteHero(req, res);
    });
    ```

2. Copie el código siguiente en **hero.service.js** después de la función `checkServerError`. Este código:
   * Crea las funciones `put` y `delete`
   * Comprueba si se ha encontrado el héroe
   * Realiza el control de errores 

   ```javascript
   function putHero(req, res) {
     const originalHero = {
       id: parseInt(req.params.id, 10),
       name: req.body.name,
       saying: req.body.saying
     };
     Hero.findOne({ id: originalHero.id }, (error, hero) => {
       if (checkServerError(res, error)) return;
       if (!checkFound(res, hero)) return;

      hero.name = originalHero.name;
       hero.saying = originalHero.saying;
       hero.save(error => {
         if (checkServerError(res, error)) return;
         res.status(200).json(hero);
         console.log('Hero updated successfully!');
       });
     });
   }

   function deleteHero(req, res) {
     const id = parseInt(req.params.id, 10);
     Hero.findOneAndRemove({ id: id })
       .then(hero => {
         if (!checkFound(res, hero)) return;
         res.status(200).json(hero);
         console.log('Hero deleted successfully!');
       })
       .catch(error => {
         if (checkServerError(res, error)) return;
       });
   }

   function checkFound(res, hero) {
     if (!hero) {
       res.status(404).send('Hero not found.');
       return;
     }
     return hero;
   }
   ```

3. En **hero.service.js**, exporte los nuevos módulos:

   ```javascript
    module.exports = {
      getHeroes,
      postHero,
      putHero,
      deleteHero
    };
    ```

4. Ahora que hemos actualizado el código, haga clic en el botón **Reiniciar** ![botón Reiniciar en Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/restart-debugger-button.png) en Visual Studio Code.

5. Actualice la página en el explorador de Internet y haga clic en el botón **Add New Hero** (Agregar nuevo héroe). Agregue un nuevo héroe con el identificador "9", el nombre "Starlord" y el mensaje "Hello". Haga clic en el botón **Guardar** para guardar el nuevo héroe.

6. Ahora seleccione el héroe **Starlord** y cambie el mensaje de "Hi" a "Bye"; después, haga clic en el botón **Guardar**. 

    Ahora puede seleccionar el identificador en la pestaña Red para mostrar la carga. En la carga puede ver que el mensaje ahora está establecido en "Bye".

    ![Aplicación Heroes y pestaña Red que muestra la carga](./media/tutorial-develop-mongodb-nodejs-part6/put-hero-function.png) 

    También puede eliminar uno de los héroes de la interfaz de usuario y ver el tiempo que se tarda en completar la operación de eliminación. Para probarlo, haga clic en el botón "Eliminar" del héroe llamado "Fred".

    ![Aplicación Heroes y la pestaña Red que muestra el tiempo para completar las funciones](./media/tutorial-develop-mongodb-nodejs-part6/times.png) 

    Si actualiza la página, la pestaña Red muestra el tiempo que se tarda en obtener los héroes. Aunque estos tiempos son cortos, en gran medida ello depende de dónde estén ubicados los datos en el mundo y de su capacidad para replicarlos geográficamente en un área cerca de los usuarios. Puede encontrar más información acerca de la replicación geográfica en el tutorial siguiente, que estará disponible próximamente.

## <a name="next-steps"></a>Pasos siguientes

En esta parte del tutorial, ha hecho lo siguiente:

> [!div class="checklist"]
> * Agregó las funciones Post, Put y Delete a la aplicación 

Compruebe pronto si hay otros vídeos en esta serie de tutoriales.

