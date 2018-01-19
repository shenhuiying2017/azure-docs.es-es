---
title: Filtros de seguridad para restringir los resultados en Azure Search | Microsoft Docs
description: Control de acceso al contenido de Azure Search mediante filtros de seguridad e identidades de usuario.
ms.custom: 
ms.date: 08/07/2017
ms.service: search
ms.reviewer: 
ms.suite: 
ms.tgt_pltfrm: 
caps.latest.revision: "26"
author: revitalbarletz
ms.author: revitalb
manager: jlembicz
ms.openlocfilehash: c829399f9c21846d8ee5b43945e2565565279820
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Filtros de seguridad para restringir los resultados en Azure Search

Puede aplicar filtros de seguridad para restringir los resultados de las búsquedas en Azure Search en función de la identidad del usuario. Esta experiencia de búsqueda requiere normalmente comparar la identidad de la persona que solicita la búsqueda en un campo que contiene los principios que tienen permisos para el documento. Cuando se encuentra una coincidencia, el usuario o la entidad (por ejemplo, un grupo o rol) tienen acceso a dicho documento.

Una forma de conseguir el filtrado de seguridad es a través de una disyunción complicada de expresiones de igualdad: por ejemplo, `Id eq 'id1' or Id eq 'id2'`, y así sucesivamente. Este enfoque es propenso a errores, es difícil de mantener y, en los casos en que la lista contiene cientos o miles de valores, ralentiza muchos segundos el tiempo de respuesta de consulta. 

Existe un enfoque más sencillo y rápido con la función `search.in`. Si utiliza `search.in(Id, 'id1, id2, ...')` en lugar de una expresión de igualdad, puede esperar tiempos de respuesta de fracciones de segundo.

En este artículo se explica cómo realizar el filtrado de seguridad mediante los siguientes pasos:
> [!div class="checklist"]
> * Creación de un campo que contiene los identificadores de entidad de seguridad 
> * Inserción o actualización de los documentos existentes con los identificadores de entidad de seguridad relevantes
> * Emisión de una solicitud de búsqueda con `search.in` `filter`

>[!NOTE]
> El proceso de recuperación de los identificadores de entidad de seguridad no se trata en este documento. Debe obtenerlo de su proveedor de servicio de identidad.

## <a name="prerequisites"></a>requisitos previos

En este artículo se da por hecho que dispone de una [suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), el [servicio Azure Search](https://docs.microsoft.com/azure/search/search-create-service-portal) y el [Índice de Azure Search](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Creación del campo de seguridad

Los documentos deben incluir un campo en el que se especifiquen los grupos con acceso. Esta información se convierte en los criterios de filtro por los que se seleccionan o rechazan los documentos del conjunto de resultados devuelto al emisor.
Supongamos que tenemos un índice de los archivos protegidos y que un conjunto distinto de usuarios puede acceder a cada uno de ellos.
1. Agregue un campo `group_ids` (puede elegir cualquier nombre aquí) como `Collection(Edm.String)`. Asegúrese de que el campo tiene un atributo `filterable` establecido en `true` para que los resultados de la búsqueda se filtren según el acceso del usuario. Por ejemplo, si establece el campo `group_ids` en `["group_id1, group_id2"]` para el documento con `file_name` "secured_file_b", solo los usuarios que pertenecen al grupo de identificadores "group_id1" o "group_id2" tienen acceso de lectura al archivo.
   Asegúrese de que el atributo `retrievable` del campo está establecido en `false` para que no se devuelva como parte de la solicitud de búsqueda.
2. Agregue los campos `file_id` y `file_name` para este ejemplo.  

```JSON
{
    "name": "securedfiles",  
    "fields": [
        {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "file_name", "type": "Edm.String"},
        {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
    ]
}
```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Inserción de datos en el índice mediante la API de REST
  
Emita una solicitud POST HTTP al punto de conexión de la dirección URL del índice. El cuerpo de la solicitud HTTP es un objeto JSON que contiene los documentos que se van a agregar:

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=[api-version]  
Content-Type: application/json
api-key: [admin key]
```

En el cuerpo de la solicitud, especifique el contenido de los documentos:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Si necesita actualizar un documento existente con la lista de grupos, puede usar la acción `merge` o `mergeOrUpload`:

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

Para obtener detalles completos sobre cómo agregar o actualizar documentos, puede leer [Edición de documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Aplicación del filtro de seguridad

Para recortar documentos basados en el acceso `group_ids`, debe emitir una consulta de búsqueda con un filtro `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))`, donde "group_id1, group_id2,..." son los grupos a los que pertenece el emisor de la solicitud de búsqueda.
Este filtro coincide con todos los documentos para los que el campo `group_ids` contiene uno de los identificadores especificados.
Para obtener detalles completos sobre cómo buscar documentos con Azure Search, puede leer [Búsqueda en documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents).
Tenga en cuenta que en este ejemplo se muestra cómo buscar en documentos mediante una solicitud POST.

Emita la solicitud POST HTTP:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=[api-version]  
Content-Type: application/json  
api-key: [admin or query key]
```

Especifique el filtro en el cuerpo de la solicitud:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Debería obtener los documentos en que `group_ids` contenga "group_id1" o "group_id2". En otras palabras, obtendrá los documentos a los que el emisor de la solicitud tiene acceso de lectura.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```
## <a name="conclusion"></a>Conclusión

Se explica cómo puede filtrar los resultados según la identidad del usuario y la función `search.in()` de Azure Search. Puede usar esta función para pasar los identificadores de entidad de seguridad del usuario solicitante para que coincidan con los identificadores de entidad de seguridad asociados a cada documento de destino. Cuando se controla una solicitud de búsqueda, la función `search.in` filtra los resultados de la búsqueda para los que ninguna de las entidades de seguridad del usuario tiene acceso de lectura. Los identificadores de entidad de seguridad pueden representar elementos como los grupos de seguridad, los roles o incluso la identidad del usuario.
 
## <a name="see-also"></a>Otras referencias

+ [Recorte de seguridad en Azure Search con Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtros de Azure Search](search-filters.md)
+ [Control de acceso y seguridad de datos en las operaciones de Azure Search](search-security-overview.md)