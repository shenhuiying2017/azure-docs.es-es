---
title: "Sincronización de datos sin conexión en Azure Mobile Apps | Microsoft Docs"
description: "Referencia e información general conceptual de la característica de sincronización de datos sin conexión para Azure Mobile Apps"
documentationcenter: windows
author: conceptdev
manager: crdun
editor: 
services: app-service\mobile
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 5ea1d655f50da49be88f7b6ae91231c4d2258fa7
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Sincronización de datos sin conexión en Azure Mobile Apps
## <a name="what-is-offline-data-sync"></a>¿Qué es la sincronización de datos sin conexión?
La sincronización de datos sin conexión es una característica de SDK de cliente y de servidor de Azure Mobile Apps que facilita a los desarrolladores la creación de aplicaciones que funcionen sin una conexión de red.

Cuando la aplicación está en modo sin conexión, aún se pueden crear y modificar datos, que se guardan en un almacén local. Cuando la aplicación se vuelve a conectar, esta puede sincronizar los cambios locales con el back-end de la aplicación de Azure. La característica también admite la detección de conflictos cuando se cambia el mismo registro en el cliente y el back-end. Luego, los conflictos se pueden manejar en el servidor o el cliente.

La sincronización sin conexión tiene varias ventajas:

* Mejore la capacidad de respuesta de las aplicaciones almacenando en caché datos de servidor de forma local en el dispositivo.
* Creación de aplicaciones sólidas que sigan siendo útiles cuando hay problemas de red
* Permitir a los usuarios finales crear y modificar datos incluso cuando no hay acceso de red, proporcionando escenarios con muy poca o ninguna conectividad.
* Sincronizar datos entre diferentes dispositivos y detectar conflictos cuando dos dispositivos modifican el mismo registro.
* Limitar el uso de las redes medidas o de alta latencia

Los siguientes tutoriales muestran cómo incorporar la sincronización sin conexión a los clientes móviles con Azure Mobile Apps:

* [Android: habilitar la sincronización sin conexión]
* [Apache Cordova: habilitación de la sincronización sin conexión](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: habilitar la sincronización sin conexión]
* [Xamarin iOS: habilitar la sincronización sin conexión]
* [Xamarin Android: habilitar la sincronización sin conexión]
* [Xamarin.Forms: habilitar la sincronización sin conexión](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Plataforma universal de Windows: habilitación de la sincronización sin conexión]

## <a name="what-is-a-sync-table"></a>¿Qué es una tabla de sincronización?
Para tener acceso al extremo de "/tables", los SDK de cliente móvil de Azure proporcionan interfaces como `IMobileServiceTable` (SDK de cliente de .NET) o `MSTable` (cliente de iOS). Estas API se conectan directamente al back-end de la aplicación móvil de Azure y generan errores si el dispositivo cliente no tiene una conexión de red.

Para admitir el uso sin conexión, la aplicación debe usar las API de la *tabla de sincronización*, como `IMobileServiceSyncTable` (SDK de cliente de .NET) o `MSSyncTable` (cliente de iOS). Las mismas operaciones CRUD (creación, lectura, actualización, eliminación) funcionan con las API de la tabla de sincronización, salvo que ahora leerán desde un *almacén local* o escribirán en él. Antes de poder realizar cualquier operación de la tabla de sincronización, se debe inicializar el almacén local.

## <a name="what-is-a-local-store"></a>¿Qué es un almacén local?
Un almacén local es la capa de persistencia de datos del dispositivo cliente. Los SDK de cliente de Azure Mobile Apps proporcionan una implementación de almacén local predeterminada. En Windows, Xamarin y Android, se basa en SQLite. En iOS, se basa en Core Data.

Para usar la implementación basada en SQLite en Windows Phone o Windows Store 8.1, debe instalar una extensión de SQLite. Para más información, consulte [Plataforma universal de Windows: habilitación de la sincronización sin conexión]. Android y iOS se distribuyen con una versión de SQLite en el sistema operativo del dispositivo, por lo que no es necesario hacer referencia a su propia versión de SQLite.

Los desarrolladores también pueden implementar su propio almacén local. Por ejemplo, si desea almacenar los datos en un formato cifrado en el cliente móvil, puede definir un almacén local que use SQLCipher para el cifrado.

## <a name="what-is-a-sync-context"></a>¿Qué es un contexto de sincronización?
Un *contexto de sincronización* está asociado a un objeto de cliente móvil (como `IMobileServiceClient` o `MSClient`) y realiza el seguimiento de los cambios que se realizan con las tablas de sincronización. El contexto de sincronización mantiene una *cola de operaciones* que tiene una lista ordenada de operaciones CUD (creación, actualización, eliminación) que posteriormente se enviará al servidor.

Un almacén local se asocia con el contexto de sincronización mediante un método de inicialización como `IMobileServicesSyncContext.InitializeAsync(localstore)` en el [SDK de cliente de .NET].

## <a name="how-sync-works"></a>Funcionamiento de la sincronización sin conexión
Al usar tablas de sincronización, el código de cliente determina el momento en que se sincronizan los cambios locales con un back-end de Azure Mobile App. No se envía nada al back-end hasta que hay una llamada a los cambios locales de *inserción* . De forma similar, el almacén local se rellena con datos nuevos solo cuando hay una llamada a los datos de *extracción* .

* **Inserción**: la inserción es una operación en el contexto de sincronización que envía todos los cambios CUD realizados desde la última inserción. Tenga en cuenta que no es posible enviar solo los cambios de una tabla individual porque las operaciones se podrían enviar desordenadas. La inserción ejecuta una serie de llamadas REST al back-end de Azure Mobile App, que a su vez, modifica la base de datos del servidor.
* **Extracción**: la extracción se realiza tabla por tabla y se puede personalizar con una consulta para recuperar solo un subconjunto de los datos del servidor. Luego, los SDK de cliente móvil de Azur insertan los datos que se obtienen en el almacén local.
* **Inserciones implícitas**: si se ejecuta una extracción en una tabla que tiene actualizaciones locales pendientes, la extracción ejecutará primero un elemento `push()` en el contexto de sincronización. Esta inserción ayuda a minimizar los conflictos entre los cambios que ya están en cola y los datos nuevos del servidor.
* **Sincronización incremental**: el primer parámetro de la operación de extracción es un *nombre de consulta* que solo se usa en el cliente. Si usa un nombre de consulta no nulo, Azure Mobile SDK lleva a cabo una *sincronización incremental*. Cada vez que una operación de extracción devuelve un conjunto de resultados, la última marca de tiempo `updatedAt` de dicho conjunto se almacena en las tablas del sistema local del SDK. Las operaciones de extracción posteriores solo recuperarán registros después de esa marca de tiempo.

  Para usar la sincronización incremental, el servidor debe devolver valores `updatedAt` significativos y también admitir la ordenación mediante este campo. Sin embargo, puesto que el SDK agrega su propio orden en el campo updatedAt, no puede usar una consulta de extracción que tenga su propia cláusula `orderBy` .

  El nombre de consulta puede ser cualquier cadena que elija, pero debe ser único para cada consulta lógica de la aplicación.
  De lo contrario, diferentes operaciones de extracción podrían sobrescribir la misma marca de tiempo de sincronización incremental y las consultas podrían devolver resultados incorrectos.

  Si la consulta tiene un parámetro, una forma de crear un nombre de consulta único es incorporar el valor del parámetro.
  Por ejemplo, si está filtrando por userid, el nombre de consulta podría ser el siguiente (en C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Si desea cancelar la sincronización incremental, pase `null` como identificador de consulta. En este caso, se recuperan todos los registros en cada llamada a `PullAsync`, que es potencialmente ineficaz.
* **Purga**: el contenido del almacén local se puede eliminar mediante `IMobileServiceSyncTable.PurgeAsync`.
  La purga puede ser necesaria si tiene datos obsoletos en la base de datos cliente o si desea descartar todos los cambios pendientes.

  Una purga borra una tabla del almacén local. Si hay operaciones a la espera de sincronizarse con la base de datos del servidor, la purga genera una excepción, a menos que esté establecido el parámetro *force purge*.

  Como un ejemplo de datos obsoletos en el cliente, supongamos que en el ejemplo "lista de tareas pendientes", Dispositivo1 extrae solo los elementos que no se han completado. Otro dispositivo marca una tarea pendiente "Comprar leche" como completada en el servidor. Sin embargo, Dispositivo1 seguirá teniendo la tarea pendiente "Comprar leche" en el almacén local porque solo está extrayendo los elementos que no están marcados como completados Una purga borra este elemento obsoleto.

## <a name="next-steps"></a>pasos siguientes
* [iOS: habilitar la sincronización sin conexión]
* [Xamarin iOS: habilitar la sincronización sin conexión]
* [Xamarin Android: habilitar la sincronización sin conexión]
* [Plataforma universal de Windows: habilitación de la sincronización sin conexión]

<!-- Links -->
[SDK de cliente de .NET]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: habilitar la sincronización sin conexión]: app-service-mobile-android-get-started-offline-data.md
[iOS: habilitar la sincronización sin conexión]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: habilitar la sincronización sin conexión]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: habilitar la sincronización sin conexión]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Plataforma universal de Windows: habilitación de la sincronización sin conexión]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
