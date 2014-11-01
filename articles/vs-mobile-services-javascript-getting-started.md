<properties title="Introducci&oacute;n a Servicios m&oacute;viles" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Tareas iniciales][Tareas iniciales]
> -   [¿Qué ha ocurrido?][¿Qué ha ocurrido?]

## Introducción a Servicios móviles

El primer paso que debe realizar para seguir el código de estos ejemplos depende del tipo de servicio móvil al que se conecte.

Para usar un servicio móvil del backend de JavaScript, cree una tabla con el nombre TodoItem. Para crear la tabla, busque el servicio móvil en el nodo Azure en el Explorador de servidores, haga clic con el botón secundario en el nodo del servicio móvil para abrir el menú contextual y elija **Crear tabla**. Escriba "TodoItem" como nombre de la tabla.

Si, por el contrario, utiliza un servicio móvil del backend .NET, hay ya una tabla TodoItem en la plantilla de proyecto predeterminada que Visual Studio ha creado para usted, pero tiene que publicarla en Azure. Para publicarla, abra el menú contextual del proyecto de servicio móvil en el Explorador de soluciones y elija **Publicación web**. Acepte los valores predeterminados y elija el botón **Publicar**.

##### Obtención de referencia a una tabla

Ya se ha agregado el objeto de cliente a su proyecto. Su nombre es el nombre del servicio móvil con la palabra "Client" agregada. El código siguiente obtiene una referencia a una tabla que contiene datos para TodoItem, que puede utilizar en operaciones posteriores para leer y actualizar la tabla de datos.

    var todoTable = yourMobileServiceClient.getTable('TodoItem');

##### Incorporación de entrada

Inserte un nuevo elemento en una tabla de datos. Se crea automáticamente un identificador (un GUID de tipo cadena) como clave principal para la nueva fila. No cambie el tipo de la columna del identificador, ya que la infraestructura de los servicios móviles lo utiliza.

    var todoTable = client.getTable('TodoItem');
    var todoItems = new WinJS.Binding.List();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            todoItems.push(item);
        });
    };

##### Lectura/consulta de tabla

El código siguiente consulta una tabla para todos los elementos, actualiza una colección local y enlaza el resultado al listItems de elementos de IU.

        // This code refreshes the entries in the list view 
        // by querying the TodoItems table.
        todoTable.where()
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });

Puede usar el método where para modificar la consulta. Aquí mostramos un ejemplo que filtra los elementos completados.

    todoTable.where(function () {
        return (this.complete === false && this.createdAt !== null);
    })
    .read()
    .done(function (results) {
        todoItems = new WinJS.Binding.List(results);
        listItems.winControl.itemDataSource = todoItems.dataSource;
    });

Para obtener más ejemplos de las consultas que puede utilizar, consulte el [objeto query][objeto query].

##### Actualización de entrada

Actualice una fila en la tabla de datos. En este ejemplo, todoItem es el elemento actualizado, e item es el mismo elemento que devuelve el servicio móvil. Cuando el servicio móvil responde, el elemento se actualiza en la lista todoItems local utilizando el método [splice][splice]. Llame al método [done][done] en el objeto [Promise][done] devuelto para obtener una copia del objeto insertado y abordar los errores.

        todoTable.update(todoItem).done(function (item) {
            todoItems.splice(todoItems.indexOf(item), 1, item);
        });

##### Eliminación de entrada

Elimine una fila en la tabla de datos. Llame al método [done][done] en el objeto [Promise][done] devuelto para obtener una copia del objeto insertado y abordar los errores.

    todoTable.delete(todoItem).done(function (item) {
        todoItems.splice(todoItems.indexOf(item), 1);
    }

[Más información acerca de servicios móviles][Más información acerca de servicios móviles]

  [Tareas iniciales]: /documentation/articles/vs-mobile-services-javascript-getting-started/
  [¿Qué ha ocurrido?]: /documentation/articles/vs-mobile-services-javascript-what-happened/
  [objeto query]: http://msdn.microsoft.com/library/azure/jj613353.aspx
  [splice]: http://msdn.microsoft.com/library/windows/apps/Hh700810.aspx
  [done]: 
  [Más información acerca de servicios móviles]: http://azure.microsoft.com/documentation/services/mobile-services/
