<properties 
	pageTitle="Creación y uso de grupos para administrar cuentas de desarrollador en Administración de API de Azure" 
	description="Obtenga información acerca de cómo administrar cuentas de desarrollador mediante los grupos en Administración de API de Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Creación y uso de grupos para administrar cuentas de desarrollador en Administración de API de Azure

En Administración de API (vista previa), los grupos se usan para administrar la visibilidad de productos para los desarrolladores. Los productos son visibles en primer lugar para los grupos y luego los desarrolladores de dichos grupos pueden ver y suscribirse a los productos asociados a los grupos.

Administración de API tiene los siguientes grupos integrados.

-   **Administradores** - Los administradores gestionan instancias del servicio Administración de API, creando las API, las operaciones y los productos que usan los desarrolladores.
-   **Desarrolladores** - Los desarrolladores son los clientes que crean aplicaciones usando sus API. Los desarrolladores, después de que se les concede acceso al portal para desarrolladores, crean aplicaciones que llaman a las operaciones de una API.
-   **Invitados** - A este grupo pertenecen los usuarios no autenticados, como por ejemplo clientes potenciales, que visitan el portal para desarrolladores de una instancia de Administración de API. Se les concede determinado acceso de solo lectura, como por ejemplo la posibilidad de ver API pero no llamarlas.

Además de estos grupos integrados, los administradores pueden crear grupos personalizados. Los grupos personalizados tienen los mismos privilegios que el grupo Desarrolladores integrado y se pueden usar para administrar varios grupos de desarrolladores. Por ejemplo, podría crear un grupo personalizado para desarrolladores que usarán las API de un producto y otro grupo para desarrolladores que usarán las API de otro producto.

En esta guía se muestra cómo los administradores de una instancia de Administración de API pueden agregar nuevos grupos y asociarlos a productos y desarrolladores.

## En este tema

-   [Creación de un grupo][Creación de un grupo]
-   [Asociación de un grupo a un producto][Asociación de un grupo a un producto]
-   [Asociación de grupos a desarrolladores][Asociación de grupos a desarrolladores]
-   [Pasos siguientes][Pasos siguientes]

## <a name="create-group"> </a>Creación de un grupo

Para crear un grupo, haga clic en la **consola de administración** en el Portal de Azure para su servicio Administración de API. Esta operación le llevará al portal administrativo Administración de API.

> Si todavía no ha creado una instancia de servicio Administración de API, consulte [Creación de una instancia de Administración de API][Creación de una instancia de Administración de API] en el tutorial [Introducción a la Administración de API de Azure][Introducción a la Administración de API de Azure].

![API Management console][API Management console]

Haga clic en **Grupos** en el menú **Administración de API** de la izquierda y haga clic en **Agregar grupo**.

![Add new group][Add new group]

Especifique un nombre único para el grupo y una descripción opcional, y haga clic en **Guardar**.

![Add new group][1]

El nuevo grupo se muestra en la pestaña Grupos. Para editar el **nombre** o la **descripción** del grupo, haga clic en el nombre del grupo en la lista. Para eliminar el grupo, haga clic en **Eliminar**.

![Group added][Group added]

Ahora que se ha creado el grupo, se puede asociar a productos y desarrolladores.

## <a name="associate-group-product"> </a>Asociación de un grupo a un producto

Para asociar un grupo a un producto, haga clic en **Productos** en el menú **Administración de API** de la izquierda y haga clic en el nombre del producto que desee.

![Set visibility][Set visibility]

Seleccione la pestaña **Visibilidad** para agregar y quitar grupos, así como para ver los grupos actuales para el producto. Para agregar o quitar grupos, active o desactive las casillas correspondientes a los grupos que desee y haga clic en **Guardar**.

![Set visibility][2]

> Para configurar grupos en la pestaña **Visibilidad** para un producto, haga clic en **Administrar grupos**.

Una vez asociado un producto a un grupo, los desarrolladores del grupo pueden ver el producto y suscribirse a él.

## <a name="associate-group-developer"> </a>Asociación de grupos a desarrolladores

Para asociar grupos a desarrolladores, haga clic en **Desarrolladores** en el menú **Administración de API** de la izquierda y active la casilla situada junto a los desarrolladores que desee asociar a un grupo.

![Add developer to group][Add developer to group]

Una vez seleccionados los desarrolladores que desee, haga clic en el grupo que desee en la lista desplegable **Agregar a grupo**. Los desarrolladores se pueden quitar de los grupos con la lista desplegable **Quitar de grupo**.

![Desarrolladores][Desarrolladores]

Una vez agregada, la asociación entre el desarrollador y el grupo se puede ver en la pestaña **Desarrolladores**.

## <a name="next-steps"> </a>Pasos siguientes

Una vez agregado a un grupo, un desarrollador puede ver los productos asociados al grupo y suscribirse a ellos. Para obtener más información, consulte [Creación y publicación de un producto en Administración de API de Azure][Creación y publicación de un producto en Administración de API de Azure],

  [Creación de un grupo]: #create-group
  [Asociación de un grupo a un producto]: #associate-group-product
  [Asociación de grupos a desarrolladores]: #associate-group-developer
  [Pasos siguientes]: #next-steps
  [Creación de una instancia de Administración de API]: ../api-management-get-started/#create-service-instance
  [Introducción a la Administración de API de Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-create-groups/api-management-management-console.png
  [Add new group]: ./media/api-management-howto-create-groups/api-management-add-group.png
  [1]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
  [Group added]: ./media/api-management-howto-create-groups/api-management-new-group.png
  [Set visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
  [2]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
  [Add developer to group]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
  [Desarrolladores]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png
  [Creación y publicación de un producto en Administración de API de Azure]: ../api-management-howto-add-products

<!--HONumber=46--> 

<!--HONumber=46--> 
