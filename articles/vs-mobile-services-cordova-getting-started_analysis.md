<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### ¿Qué ha ocurrido?

### Se han agregado referencias

Se ha habilitado el complemento de cliente de servicio móvil de Azure con todas las aplicaciones híbridas multidispositivo.

### Conexión de valores de cadena para Servicios móviles

En services\\mobileServices\\settings, se ha generado un nuevo archivo JavaScript (.js) con MobileServiceClient que contiene la URL de aplicación y la clave de aplicación del servicio móvil seleccionado. El archivo contiene la inicialización de un objeto de cliente de servicio móvil, similar al código siguiente.

    var mobileServiceClient;
    document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
            "<your mobile service name>.azure-mobile.net",
            "<insert your key>"
        );

### Introducción a los Servicios móviles

###### Obtención de referencia a una tabla

El código siguiente obtiene una referencia a una tabla que contiene datos para TodoItem, que puede utilizar en operaciones posteriores para leer y actualizar la tabla de datos. La tabla TodoItem se crea automáticamente cuando se crea un servicio móvil.

    var todoTable = mobileServiceClient.getTable('TodoItem');

Para que funcionen estos ejemplos, los permisos de la tabla deben estar configurados en **Cualquier persona con la clave de aplicación**. Más adelante podrá configurar la autenticación. Consulte [Introducción a la autenticación][Introducción a la autenticación].

###### Incorporación de entrada

Inserte un nuevo elemento en una tabla de datos. Se crea automáticamente un identificador (un GUID de tipo cadena) como clave principal para la nueva fila. Llame al método [done][done] en el objeto [Promise][done] devuelto para obtener una copia del objeto insertado y abordar los errores.

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    todoTable.insert(todoItem).done(function (item) {
        items.push(item)
        });
    };

###### Lectura/consulta de tabla

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

Para obtener más información acerca de las consultas que puede utilizar, consulte el objeto [query][query].

###### Actualización de entrada

Actualice una fila en la tabla de datos. En este código, si el servicio móvil responde, el elemento se quita de la lista. Llame al método [done][done] en el objeto [Promise][done] devuelto para obtener una copia del objeto insertado y abordar los errores.

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

###### Eliminación de entrada

Elimine una fila en la tabla de datos utilizando el método del. Llame al método [done][done] en el objeto [Promise][done] devuelto para obtener una copia del objeto insertado y abordar los errores.

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });

  [Introducción a la autenticación]: http://azure.microsoft.com/es-es/documentation/articles/mobile-services-html-get-started-users/
  [done]: 
  [query]: (http://msdn.microsoft.com/library/azure/jj613353.aspx)
