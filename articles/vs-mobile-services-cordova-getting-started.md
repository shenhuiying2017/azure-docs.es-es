<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Introducción](/documentation/articles/vs-mobile-services-cordova-getting-started/)
> - [¿Qué ha ocurrido?](/documentation/articles/vs-mobile-services-cordova-what-happened/)

## Introducción a Servicios móviles (proyectos Cordova)

El primer paso que debe realizar para seguir el código de estos ejemplos depende del tipo de servicio móvil al que se conecte.

Para usar un servicio móvil del backend de JavaScript, cree una tabla con el nombre TodoItem.  Para crear la tabla, busque el servicio móvil en el nodo Azure en el Explorador de servidores, haga clic con el botón secundario en el nodo del servicio móvil para abrir el menú contextual y elija **Crear tabla**. Escriba "TodoItem" como nombre de la tabla.

Si, por el contrario, utiliza un servicio móvil del backend .NET, hay ya una tabla TodoItem en la plantilla de proyecto predeterminada que Visual Studio ha creado para usted, pero tiene que publicarla en Azure. Para publicarla, abra el menú contextual del proyecto de servicio móvil en el Explorador de soluciones y elija **Publicación web**. Acepte los valores predeterminados y elija el botón **Publicar**.
  
>[WACOM.NOTE]**Aplique esta [solución](http://go.microsoft.com/fwlink/?LinkId=518765) para utilizar Servicios móviles de Azure en proyectos Cordova.**

#####Obtención de referencia a una tabla

El código siguiente obtiene una referencia a una tabla que contiene datos para TodoItem, que puede utilizar en operaciones posteriores para leer y actualizar la tabla de datos. La tabla TodoItem se crea automáticamente cuando se crea un servicio móvil.

	var todoTable = mobileServiceClient.getTable('TodoItem');

Para que funcionen estos ejemplos, los permisos de la tabla deben estar configurados en **Cualquier persona con la clave de aplicación**. Más adelante podrá configurar la autenticación. Consulte [Introducción a la autenticación](http://azure.microsoft.com/es-es/documentation/articles/mobile-services-html-get-started-users/).

#####Incorporación de entrada 

Inserte un nuevo elemento en una tabla de datos. Se crea automáticamente un identificador (un GUID de tipo cadena) como clave principal para la nueva fila. Llame al método [done]() en el objeto [Promise]() devuelto para obtener una copia del objeto insertado y abordar los errores.

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    todoTable.insert(todoItem).done(function (item) {
        items.push(item)
        });
    };

#####Lectura/consulta de tabla 

El código siguiente consulta una tabla para todos los elementos, ordenados por campo de texto. Puede agregar código para procesar los resultados de la consulta en el controlador de proceso correcto. En este caso, se actualiza una matriz local de los elementos.

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
            });
        });

Puede usar el método where para modificar la consulta. Aquí mostramos un ejemplo que filtra los elementos completados.

	todoTable.where(function () {
                 return (this.complete === false);
              })
             .read().done(function (results) {
                items = results.slice();
             });

Para obtener más información acerca de las consultas que puede utilizar, consulte el objeto [query]((http://msdn.microsoft.com/library/azure/jj613353.aspx)).

#####Actualización de entrada

Actualice una fila en la tabla de datos. En este código, si el servicio móvil responde, el elemento se quita de la lista. Llame al método [done]() en el objeto [Promise]() devuelto para obtener una copia del objeto insertado y abordar los errores.

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

#####Eliminación de entrada

Elimine una fila en la tabla de datos utilizando el método **del**. Llame al método [done]() en el objeto [Promise]() devuelto para obtener una copia del objeto insertado y abordar los errores.

	todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
	});

[Más información acerca de Servicios móviles](http://azure.microsoft.com/documentation/services/mobile-services/)
