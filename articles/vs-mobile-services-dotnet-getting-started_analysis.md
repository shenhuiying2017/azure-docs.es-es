<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### ¿Qué ha ocurrido?

###### Se han agregado referencias

El paquete de NuGet de servicios móviles de Azure se agregó a su proyecto. Como resultado, se agregaron las siguientes referencias de .NET a su proyecto: Microsoft.WindowsAzure.Mobile, Microsoft.WindowsAzure.Mobile.Ext, Newtonsoft.Json, System.Net.Http.Extensions, System.Net.Http.Primitives

###### Conexión de valores de cadena para Servicios móviles

En el archivo App.xaml.cs, se ha creado un objeto MobileServiceClient con la URL de aplicación y la clave de aplicación del servicio móvil seleccionado.

### Introducción a los Servicios móviles

###### Obtención de referencia a una tabla

El código siguiente obtiene una referencia a una tabla que contiene datos para TodoItem, que puede utilizar en operaciones posteriores para leer y actualizar la tabla de datos. Necesitará la clase TodoItem con atributos configurados para interpretar el JSON que el servicio móvil envía en respuesta a sus consultas.

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

    IMobileServiceTable<TodoItem> todoTable = App.<yourClient>.GetTable<TodoItem>();

Este código funciona si la tabla tiene permisos establecidos en **Cualquier persona con la clave de aplicación**. Si cambia los permisos para asegurar el servicio móvil, tendrá que agregar compatibilidad con la autenticación de usuarios. Consulte [Introducción a la autenticación][Introducción a la autenticación].

###### Incorporación de entrada

Inserte un nuevo elemento en una tabla de datos.

    TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
    await todoTable.InsertAsync(todoItem);

###### Lectura/consulta de tabla

El código siguiente consulta una tabla para todos los elementos. Tenga en cuenta que devuelve solo la primera página de los datos, que de manera predeterminada contiene 50 elementos. Puede pasar el tamaño de página que desee, ya que es un parámetro opcional.

    List<TodoItem> items = await todoTable.ToListAsync();
    try
    {
        // Query that returns all items.   
        items = await todoTable.ToCollectionAsync();             
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }

###### Actualización de entrada

Actualice una fila en la tabla de datos. El elemento de parámetro es el objeto TodoItem que se va a actualizar.

    await todoTable.UpdateAsync(item);

###### Eliminación de entrada

Elimine una fila en la base de datos. El elemento de parámetro es el objeto TodoItem que se va a eliminar.

    await todoTable.DeleteAsync(item);

  [Introducción a la autenticación]: http://azure.microsoft.com/es-es/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/
