---
title: "Activación de la sincronización sin conexión para una aplicación móvil de Azure (Cordova) | Microsoft Docs"
description: "Obtenga información acerca de cómo usar la aplicación móvil de Servicios de aplicaciones para almacenar en caché y sincronizar datos sin conexión en su aplicación Cordova"
documentationcenter: cordova
author: ggailey777
manager: syntaxc4
editor: 
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/30/2016
ms.author: glenga
ms.openlocfilehash: 45e80ca672dfdb6defc6e5c1aac3d29f5479125c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Activación de la sincronización sin conexión para una aplicación móvil Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Este tutorial presenta la característica de sincronización sin conexión de Aplicaciones móviles de Azure para Cordova. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil &mdash;ver, agregar o modificar datos&mdash; aun cuando no haya conexión de red. Los cambios se almacenan en una base de datos local.  Una vez que el dispositivo se vuelve a conectar, estos cambios se sincronizan con el servicio remoto.

Este tutorial se basa en la solución de inicio rápido de Cordova para aplicaciones móviles que crea al completar el tutorial [Inicio rápido de Apache Cordova]. En este tutorial, actualizará la solución de inicio rápido para agregar las características sin conexión de Azure Mobile Apps.  También nos centraremos en el código sin conexión específico de la aplicación.

Para obtener más información acerca de la característica de sincronización sin conexión, consulte el tema [Sincronización de datos sin conexión en Aplicaciones móviles de Azure]. Para obtener detalles de uso de la API, consulte la [documentación de la API](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Adición de sincronización sin conexión a la solución de inicio rápido
El código de sincronización sin conexión debe agregarse a la aplicación. La sincronización sin conexión requiere el complemento cordova-sqlite-storage, que se agrega automáticamente a la aplicación cuando el complemento Aplicaciones móviles de Azure se incluye en el proyecto. El proyecto de inicio rápido incluye ambos complementos.

1. En el Explorador de soluciones de Visual Studio, abra index.js y reemplace el código siguiente

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    por este otro:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Luego, reemplace el siguiente código:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    por este otro:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              complete: 'boolean',
              version: 'string'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    Las adiciones de los códigos anteriores inicializan el almacén local y definen una tabla local que coincide con los valores de columna utilizados en el back-end de Azure. (No hace falta incluir todos los valores de columna en este código.)  El campo `version` se mantiene mediante el back-end móvil y se usa para la resolución de conflictos.

    Para obtener una referencia al contexto de sincronización, llame a **getSyncContext**. El contexto de sincronización ayuda a mantener las relaciones entre tablas mediante el seguimiento y la inserción de los cambios en todas las tablas modificadas por una aplicación cliente cuando se llama a `.push()` .

3. Actualice la dirección URL de la aplicación a la de la aplicación móvil.

4. Ahora, reemplace este código:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    por este otro:

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    El código anterior inicializa el contexto de sincronización y, después, llama a getSyncTable (en lugar de getTable) para obtener una referencia a la tabla local.

    Este código usa la base de datos local para todas las operaciones de creación, lectura, actualización y eliminación (CRUD) de tablas.

    En este ejemplo se realiza un control de errores simple en los conflictos de sincronización. Una aplicación real controlaría los diversos errores, como los problemas en la red, los conflictos del servidor, etc. Para obtener ejemplos de código, vea el [ejemplo de sincronización sin conexión].

5. A continuación, agregue esta función para realizar la sincronización real.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Decida cuándo se deben insertar los cambios en el back-end de Mobile App mediante la llamada a **syncContext.push()**. Por ejemplo, podría llamar a **syncBackend** en un controlador de eventos de botón asociado a un botón de sincronización.

## <a name="offline-sync-considerations"></a>Consideraciones sobre la sincronización sin conexión

En el ejemplo, el método **push** de **syncContext** solo se llama en el inicio de la aplicación en la función de devolución de llamada del inicio de sesión.  En una aplicación real, también puede hacer que esta funcionalidad de sincronización se desencadene manualmente cuando cambia el estado de la red.

Si se ejecuta una extracción en una tabla que tiene actualizaciones locales pendientes rastreadas por el contexto, la operación de extracción desencadenará de forma automática una inserción. Al actualizar, agregar y completar elementos en este ejemplo, se puede omitir la llamada explícita a **push**, ya que puede ser redundante.

En el código proporcionado, se consultan todos los registros de la tabla todoItem remota, pero también es posible filtrar registros pasando un identificador de consulta y una consulta a **push**. Para más información, consulte la sección *Sincronización incremental* en [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

## <a name="optional-disable-authentication"></a>(Opcional) Deshabilitación de la autenticación

Si no quiere configurar la autenticación antes de probar la sincronización sin conexión, convierta en comentario la función de devolución de llamada del inicio de sesión, pero deje el código de la función de devolución de llamada sin comentarios.  Después de convertir en comentario las líneas de inicio, sigue el código:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Ahora, la aplicación, cuando se ejecuta, se sincroniza con el back-end de Azure.

## <a name="run-the-client-app"></a>Ejecución de la aplicación cliente
Con la sincronización sin conexión ahora habilitada, puede ejecutar la aplicación cliente al menos una vez en cada plataforma para rellenar la base de datos del almacén local. Después, simulará un escenario sin conexión y modificará los datos del almacén local mientras la aplicación está sin conexión.

## <a name="optional-test-the-sync-behavior"></a>(Opcional) Prueba del comportamiento de la sincronización
En esta sección, modificará el proyecto de cliente para simular un escenario sin conexión usando una dirección URL de aplicación no válida para el back-end. Al agregar o cambiar elementos de datos, estos cambios se conservan en el almacén local, pero no se sincronizan con el almacén de datos de back-end hasta que se restablece la conexión.

1. En el Explorador de soluciones, abra el archivo de proyecto index.js y cambie la dirección URL de la aplicación para que apunte a una dirección URL no válida como la siguiente:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. En index.html, actualice el elemento `<meta>` de CSP con la misma dirección URL no válida.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Compile y ejecute la aplicación cliente, y observe que se registra una excepción en la consola cuando la aplicación intenta sincronizarse con el back-end después del inicio de sesión. Los nuevos elementos que agrega solo existirán en el almacén local hasta que se puedan insertar en el back-end móvil. La aplicación cliente se comporta como si estuviera conectada al back-end.

4. Cierre la aplicación y reiníciela para comprobar que los nuevos elementos que creó se mantienen en el almacén local.

5. (Opcional) Use Visual Studio para ver la tabla de base de datos SQL de Azure y observar que los datos de la base de datos de back-end no han cambiado.

    En Visual Studio, abra el **Explorador de servidores**. Vaya a la base de datos en **Azure**->**SQL Databases**. Haga clic con el botón derecho en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**. Ahora puede buscar la tabla de base de datos SQL y su contenido.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Opcional) Prueba de la reconexión a un back-end móvil

En esta sección se volverá a conectar la aplicación al back-end móvil, que simula que la aplicación vuelve a un estado en línea. Cuando inicia sesión, los datos se sincronizan con el back-end móvil.

1. Vuelva a abrir index.js y restaure la dirección URL de la aplicación.
2. Vuelva a abrir index.html y corrija la dirección URL de la aplicación en el elemento `<meta>` de CSP.
3. Vuelva a compilar la aplicación cliente y ejecútela. La aplicación intenta sincronizarse con el back-end de la aplicación móvil después del inicio de sesión. Compruebe que no hay excepciones registradas en la consola de depuración.
4. (Opcional) Vea los datos actualizados mediante el Explorador de objetos de SQL Server o una herramienta REST como Fiddler. Observe que los datos se han sincronizado entre la base de datos back-end y el almacén local.

    Observe que los datos se han sincronizado entre la base de datos y el almacén local, y contienen los elementos que agregó mientras la aplicación estaba desconectada.

## <a name="additional-resources"></a>Recursos adicionales
* [Sincronización de datos sin conexión en Aplicaciones móviles de Azure]
* [Visual Studio Tools para Apache Cordova]

## <a name="next-steps"></a>Pasos siguientes
* Examine características de sincronización sin conexión más avanzadas, como la resolución de conflictos, en el [ejemplo de sincronización sin conexión]
* Examine la referencia de API de sincronización sin conexión en la [documentación de la API](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Inicio rápido de Apache Cordova]: app-service-mobile-cordova-get-started.md
[ejemplo de sincronización sin conexión]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Sincronización de datos sin conexión en Aplicaciones móviles de Azure]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools para Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
