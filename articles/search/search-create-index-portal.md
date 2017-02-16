---
title: "Realización de una consulta en el índice de Azure Search mediante Azure Portal | Microsoft Docs"
description: "Cree un índice mediante el Portal de Azure."
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: c54d8787-6dae-4943-85ed-c8928d2a5caf
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/13/2017
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 1f06a7197cc1a6dcf7a39c91183a4317bef126bb
ms.openlocfilehash: cc439b5da8753b41f8d65781c567b4808f385078


---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Creación de un índice de Búsqueda de Azure en el Portal de Azure
> [!div class="op_single_selector"]
> * [Información general](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Este artículo le guiará en el proceso de creación de un [índice](search-what-is-an-index.md) de Búsqueda de Azure mediante el Portal de Azure.

Antes de seguir con esta guía y crear un índice, debe haber [creado ya un servicio de Búsqueda de Azure](search-create-service-portal.md).

## <a name="go-to-your-azure-search-blade"></a>Vaya a la hoja Búsqueda de Azure.
1. Haga clic en "Todos los recursos" en el menú en el lado izquierdo de [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).
2. Seleccione el servicio Búsqueda de Azure.

## <a name="add-and-name-your-index"></a>Agregue el índice y asígnele un nombre.
1. Haga clic en el botón "Agregar índice".
2. Asigne un nombre a su índice de Búsqueda de Azure. Puesto que vamos a crear un índice para buscar hoteles en esta guía, hemos llamado a nuestro índice "hoteles".
   * El nombre del índice debe empezar por una letra y contener solo letras minúsculas, dígitos o guiones ("-").
   * Al igual que el nombre del servicio, el nombre del índice que seleccione también será parte de la dirección URL del punto de conexión donde enviará sus solicitudes HTTP para la API de Búsqueda de Azure
3. Haga clic en la entrada "Campos" para abrir una nueva hoja.

![](./media/search-create-index-portal/add-index.png)

## <a name="create-and-define-the-fields-of-your-index"></a>Cree y defina los campos del índice.
1. Al seleccionar la entrada de "Campos", se abre una nueva hoja con un formulario para escribir la definición del índice.
2. Agregue campos a su índice mediante el formulario.
   
   * Es obligatorio un campo *clave* de tipo Edm.String para cada índice de Búsqueda de Azure. Este campo clave se crea de forma predeterminada con el nombre de campo "id". Lo hemos cambiado por "hotelId" en nuestro índice.
   * Algunas propiedades de su esquema de índice solo pueden establecerse una vez y no se pueden actualizar en el futuro. Por este motivo, las actualizaciones de esquema que requerirían volver a indexar, como cambiar los tipos de campo, no son actualmente posibles tras la configuración inicial.
   * Hemos elegido cuidadosamente los valores de propiedad de cada campo según cómo creemos que se van a usar en una aplicación. Tenga en cuenta la experiencia de usuario de búsqueda y las necesidades del negocio al diseñar el índice ya que a cada campo se le deben asignar las [propiedades adecuadas](https://msdn.microsoft.com/library/azure/dn798941.aspx). Estas propiedades controlan qué características de búsqueda (filtrado, facetas, ordenación, búsqueda de texto completo, etc.) se aplican a qué campos. Por ejemplo, es probable que las personas que buscan hoteles estén interesadas en coincidencias de palabras clave en el campo "descripción", así que habilitamos la búsqueda de texto completo para ese campo estableciendo la propiedad "Permite búsqueda".
     * También puede establecer el [analizador de idioma](https://msdn.microsoft.com/en-us/library/azure/dn879793.aspx) para cada campo haciendo clic en la pestaña "Analizador" de la parte superior de la hoja. A continuación puede ver que hemos seleccionado un analizador de francés para un campo de nuestro índice destinado a texto en francés.
3. Haga clic en **Aceptar** en la hoja "Campos" para confirmar las definiciones de campo
4. Haga clic en **Aceptar** en la hoja "Agregar índice" para guardar y crear el índice que acaba de definir.

En las capturas de pantalla siguientes, puede ver cómo hemos denominado y definido los campos de nuestro índice "hoteles".

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>Pasos siguientes
Después de crear un índice de Búsqueda de Azure, ya podrá [cargar el contenido en el índice](search-what-is-data-import.md) y empezar la búsqueda de los datos.




<!--HONumber=Jan17_HO2-->


