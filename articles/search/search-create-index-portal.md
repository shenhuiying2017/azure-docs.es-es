---
title: "Crear un índice (portal - Azure Search) | Microsoft Docs"
description: "Cree un índice mediante Azure Portal."
services: search
manager: jhubbard
author: heidisteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/20/2017
ms.author: heidist
ms.openlocfilehash: a7d98ab0937a7d3f932d5df34c19ae091129804e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Creación de un índice de Azure Search en Azure Portal
> [!div class="op_single_selector"]
> * [Información general](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Use el diseñador de índices integrado en Azure Portal para crear un prototipo de o un [índice de búsqueda](search-what-is-an-index.md) para que se ejecute en el servicio Azure Search. 

## <a name="prerequisites"></a>Requisitos previos

En este artículo se dan por hecho una [suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) y el [servicio Azure Search](search-create-service-portal.md).  

## <a name="find-your-search-service"></a>Búsqueda del servicio de búsqueda
1. Inicie sesión en la página de Azure Portal y revise los [servicios de búsqueda de la suscripción](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Seleccione el servicio Azure Search.

## <a name="name-the-index"></a>Nombre del índice

1. Haga clic en el botón **Agregar índice** de la barra de comandos de la parte superior de la página.
2. Asigne un nombre a su índice de Azure Search. 
   * Empiece con una letra.
   * Use únicamente letras minúsculas, números o guiones ("-").
   * Limite el nombre a 60 caracteres.

  El nombre del índice se convierte en parte de la dirección URL del punto de conexión que se usa en las conexiones al índice y para enviar solicitudes HTTP en la API de REST de Azure Search.

## <a name="define-the-fields-of-your-index"></a>Definición de los campos del índice

La composición del índice incluye una *colección de campos* que define los datos del índice que se pueden buscar. Más concretamente, especifica la estructura de documentos que se carga por separado. La colección de campos incluye campos obligatorios y opcionales, con nombre y tipo, con atributos de índice para determinar cómo se puede usar el campo.

1. En la hoja **Agregar índice**, haga clic en **Campos >** para abrir la hoja de definición del campo. 

2. Acepte el campo *clave* generado de tipo Edm.String. De forma predeterminada, el campo se denomina *id*, pero se puede cambiar de nombre siempre que la cadena se ajuste a las [reglas de nomenclatura](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Hay un campo de clave obligatorio para cada índice de Azure Search y debe ser una cadena.

3. Agregue campos para especificar completamente los documentos que va a cargar. Si los documentos constan de *id.*, *nombre de hotel*, *dirección*, *ciudad* y *región*, cree un campo correspondiente para cada uno de ellos en el índice. Revise la [guía de diseño de la siguiente sección](#design) para obtener ayuda para configurar atributos.

4. También puede agregar cualquier campo que se use internamente en expresiones de filtro. Los atributos del campo se pueden establecer de modo que se excluyan campos de operaciones de búsqueda.

5. Cuando termine, haga clic en **Aceptar** para guardar y crear el índice.

## <a name="tips-for-adding-fields"></a>Sugerencias para agregar campos

Para la creación de un índice en el portal se usa mucho el teclado. Minimice los pasos con este flujo de trabajo:

1. En primer lugar, cree la lista de campos al escribir los nombres y establecer los tipos de datos.

2. Entonces use las casillas de verificación de la parte superior de cada atributo para habilitar en masa el valor de todos los campos y luego desactive de forma selectiva las casillas de los pocos campos que no lo necesiten. Por ejemplo, los campos de cadena normalmente permiten realizar búsquedas. Por lo tanto, puede hacer clic en **Recuperable** y **Buscable** para devolver los valores del campo en los resultados de búsqueda, así como para permitir la búsqueda de texto completo en el campo. 

<a name="design"></a>
## <a name="design-guidance-for-setting-attributes"></a>Guía de diseño para establecer atributos

Aunque puede agregar nuevos campos en cualquier momento, las definiciones de campo existentes se bloquean durante la vigencia del índice. Por este motivo, los desarrolladores suelen usar el portal para crear índices sencillos, probar ideas o emplear las páginas del portal para buscar una configuración. La iteración frecuente de un diseño de índice es más eficaz si se sigue un enfoque basado en código para poder volver a crear el índice fácilmente.

Los analizadores y los proveedores de sugerencias se asocian a los campos antes de guardar el índice. Asegúrese de hacer clic en cada pestaña para agregar analizadores o proveedores de sugerencias de idioma a la definición de índice.

Los campos de cadena se suelen marcar como **Buscable** y **Recuperable**.

Los campos que se usan para restringir los resultados de búsqueda incluyen **Ordenable**, **Filtrable** y **Clasificable**.

Los atributos de campo determinan cómo se usa un campo, por ejemplo, si se usa en la búsqueda de texto completo, la navegación por facetas, las operaciones de ordenación, etc. En la tabla siguiente se describe cada atributo.

|Atributo|Descripción|  
|---------------|-----------------|  
|**buscable**|Permite realizar búsquedas de texto completo, sujetas a análisis léxico, como la separación de palabras durante la indexación. Si establece un campo buscable en un valor como "día soleado", internamente se dividirá en los tokens individuales "soleado" y "día". Para obtener detalles, vea [Búsqueda de texto completo](search-lucene-query-architecture.md).|  
|**filtrable**|Se hace referencia en consultas **$filter**. Los campos filtrables de tipo `Edm.String` o `Collection(Edm.String)` no sufren separación de palabras, por lo que las comparaciones son solo de coincidencias exactas. Por ejemplo, si establece un campo de este tipo en "día soleado", `$filter=f eq 'sunny'` no encontrará ninguna coincidencia, pero `$filter=f eq 'sunny day'` sí. |  
|**ordenable**|De forma predeterminada, el sistema ordena los resultados por puntuación, pero puede configurar la ordenación en función de los campos de los documentos. Los campos de tipo `Collection(Edm.String)` no pueden ser **ordenables**. |  
|**clasificable**|Normalmente se usa en una presentación de resultados de búsqueda que incluye un recuento de visitas por categoría (por ejemplo, hoteles de una ciudad concreta). Esta opción no puede utilizarse con campos de tipo `Edm.GeographyPoint`. Los campos de tipo `Edm.String` que son **filtrables**, **ordenables** o **clasificables** pueden tener como máximo 32 kilobytes de longitud. Para obtener detalles, vea [Creación de un índice de Búsqueda de Azure con la API de REST](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**key**|Identificador único de los documentos del índice. Es necesario elegir exactamente un campo como campo de clave, y debe ser de tipo `Edm.String`.|  
|**recuperable**|Determina si el campo se puede devolver en un resultado de búsqueda. Esto resulta útil cuando se quiere usar un campo (por ejemplo, *margen de beneficio*) como mecanismo de filtrado, ordenación o puntuación, pero no se quiere que sea visible para el usuario final. Este atributo debe ser `true` for `key` .|  

## <a name="create-the-hotels-index-used-in-example-api-sections"></a>Creación del índice de hoteles usado en las secciones de la API de ejemplo

La documentación de la API de Azure Search incluye ejemplos de código con un sencillo índice de *hoteles*. En las capturas de pantalla siguientes, puede ver la definición del índice, incluido el analizador de idioma francés especificado durante la definición del índice, que puede volver a crear como ejercicio práctico en el portal.

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>Pasos siguientes

Después de crear un índice de Azure Search, puede ir al siguiente paso: [cargar datos buscables en el índice](search-what-is-data-import.md).

También podría profundizar en los índices. Además de la colección de campos, un índice también especifica analizadores, proveedores de sugerencias, perfiles de puntuación y configuración de CORS. El portal proporciona pestañas para definir los elementos más comunes: campos, analizadores y proveedores de sugerencias. Para crear o modificar otros elementos, puede usar la API de REST o el SDK de .NET.

## <a name="see-also"></a>Otras referencias

 [Cómo funciona la búsqueda de texto completo](search-lucene-query-architecture.md)  
 [API de REST del servicio Search](https://docs.microsoft.com/rest/api/searchservice/)[SDK de .NET](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

