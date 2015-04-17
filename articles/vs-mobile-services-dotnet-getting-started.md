<properties 
	pageTitle="" 
	description="" 
	services="mobile-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/8/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Introducción](/documentation/articles/vs-mobile-services-dotnet-getting-started/)
> - [¿Qué ha ocurrido?](/documentation/articles/vs-mobile-services-dotnet-what-happened/)

## Introducción a Servicios móviles (proyectos .NET)

El primer paso que debe realizar para seguir el código de estos ejemplos depende del tipo de servicio móvil al que se conecte.

Para usar un servicio móvil del backend de JavaScript, cree una tabla con el nombre TodoItem.  Para crear la tabla, busque el servicio móvil en el nodo Azure en el Explorador de servidores, haga clic con el botón secundario en el nodo del servicio móvil para abrir el menú contextual y elija **Crear tabla**. Escriba "TodoItem" como nombre de la tabla.

Si, por el contrario, utiliza un servicio móvil del backend .NET, hay ya una tabla TodoItem en la plantilla de proyecto predeterminada que Visual Studio ha creado para usted, pero tiene que publicarla en Azure. Para publicarla, abra el menú contextual del proyecto de servicio móvil en el Explorador de soluciones y elija **Publicación web**. Acepte los valores predeterminados y elija el botón **Publicar**.

#####Obtención de referencia a una tabla

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

Este código funciona si la tabla tiene permisos establecidos en **Cualquier persona con la clave de aplicación**. Si cambia los permisos para asegurar el servicio móvil, tendrá que agregar compatibilidad con la autenticación de usuarios. Consulte [Introducción a la autenticación](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/).

#####Incorporación de entrada 

Inserte un nuevo elemento en una tabla de datos.

	TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
	await todoTable.InsertAsync(todoItem);

#####Lectura/consulta de tabla 

El código siguiente consulta una tabla para todos los elementos. Tenga en cuenta que devuelve solo la primera página de los datos, que de manera predeterminada contiene 50 elementos. Puede pasar el tamaño de página que desee, ya que es un parámetro opcional.

    List<TodoItem> items;
    try
    {
        // Query that returns all items.   
        items = await todoTable.ToListAsync();             
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }


#####Actualización de entrada

Actualice una fila en la tabla de datos. El elemento de parámetro es el objeto TodoItem que se va a actualizar.

	await todoTable.UpdateAsync(item);

#####Eliminación de entrada

Elimine una fila en la base de datos. El elemento de parámetro es el objeto TodoItem que se va a eliminar.

	await todoTable.DeleteAsync(item);


[Más información acerca de Servicios móviles](http://azure.microsoft.com/documentation/services/mobile-services/)



<!--HONumber=42-->
