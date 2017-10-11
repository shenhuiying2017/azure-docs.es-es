---
pageTitle: Synonyms in Azure Search (preview) | Microsoft Docs
description: "La documentación preliminar de la característica Sinónimos (versión preliminar), expuesta en la API de REST de Azure Search."
services: search
documentationCenter: 
authors: mhko
manager: pablocas
editor: 
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/07/2016
ms.author: nateko
ms.openlocfilehash: 739a0ad77c68ea74ec25bc80c7539ac8b3f18201
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="synonyms-in-azure-search-preview"></a>Sinónimos en Azure Search (versión preliminar)

Los sinónimos de los motores de búsqueda asocian términos equivalentes que expanden implícitamente el ámbito de una consulta, sin que el usuario tenga que proporcionar realmente el término. Por ejemplo, con el término "perro" y las asociaciones de sinónimos de "canino" y "cachorro", los documentos que contengan los términos "perro", "canino" o "cachorro" estarán dentro del ámbito de la consulta.

En Azure Search, la expansión de sinónimos se realiza en el momento de la consulta. Puede agregar asignaciones de sinónimos a un servicio sin que se interrumpan las operaciones existentes. Puede agregar una propiedad **synonymMaps** a una definición de campo sin tener que volver a crear un índice. Para más información, vea [Actualización de índice](https://docs.microsoft.com/rest/api/searchservice/update-index).

## <a name="feature-availability"></a>Disponibilidad de características

La característica Sinónimos está actualmente en la versión preliminar y solo es compatible con la versión de la API de versión preliminar más reciente (api-version=2016-09-01-Preview). En este momento no es compatible con Azure Portal. Puesto que la versión de la API se especifica en la solicitud, es posible combinar API de versión preliminar y disponibles en general en la misma aplicación. Sin embargo, la versión preliminar de las API no se someten a las condiciones del Acuerdo de Nivel de Servicio y sus características pueden cambiar, por lo que no se recomienda su uso en aplicaciones de producción.

## <a name="how-to-use-synonyms-in-azure-search"></a>Cómo utilizar sinónimos en Azure Search

En Azure Search, la compatibilidad de los sinónimos se basa en las asignaciones de sinónimos que defina y cargue en el servicio. Estas asignaciones constituyen un recurso independiente (como índices u orígenes de datos), y cualquier campo buscable puede usarlas en cualquier índice en el servicio de búsqueda.

Los índices y asignaciones de sinónimos se mantienen de forma independiente. Una vez que defina una asignación de sinónimos y la cargue en el servicio, podrá habilitar la característica Sinónimos en un campo agregando una nueva propiedad denominada **synonymMaps** en la definición del campo. La creación, carga y eliminación de una asignación de sinónimos es siempre una operación de documento completo, lo que significa que no puede crear, actualizar o eliminar partes de la asignación de sinónimos de forma incremental. La actualización de incluso una entrada única requiere que se vuelva a cargar.

La incorporación de sinónimos en la aplicación de búsqueda es un proceso de dos pasos:

1.  Agregar una asignación de sinónimos al servicio de búsqueda a través de las API siguientes.  

2.  Configurar un campo buscable para usar la asignación de sinónimos en la definición del índice.

### <a name="synonymmaps-resource-apis"></a>API de recursos de SynonymMaps

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Adición o actualización de una asignación de sinónimos en su servicio, con POST o PUT

Las asignaciones de sinónimos se cargan en el servicio a través de POST o PUT. Cada regla debe estar delimitada por el nuevo carácter de línea ('\n'). Puede definir hasta 5000 reglas por asignación de sinónimos en un servicio gratuito y 10 000 reglas en las demás SKU. Cada regla puede tener hasta 20 expansiones.

En esta versión preliminar, las asignaciones de sinónimos deben estar en formato Apache Solr, que se explica a continuación. Si dispone de un diccionario de sinónimos existente en un formato distinto y desea usarlo directamente, háganoslo saber en [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Puede crear una nueva asignación de sinónimos con HTTP POST, como en el siguiente ejemplo:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2016-09-01-Preview
    api-key: [admin key]

    {  
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

De forma alternativa, puede usar PUT y especificar el nombre de asignación del sinónimo en el identificador URI. Si la asignación de sinónimos no existe, se creará.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

    {  
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Formato de sinónimos de Apache

El formato Solr es compatible con asignaciones de sinónimos equivalentes y explícitos. Las reglas de asignación se adhieren a la especificación del filtro de sinónimos de código abierto de Apache Solr descrita en este documento: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). A continuación se muestra una regla de ejemplo para los sinónimos equivalentes.
```
              USA, United States, United States of America
```

Con la regla anterior, la consulta de búsqueda "EE. UU." se ampliará a "EE. UU." O "Estados Unidos" O "Estados Unidos de América".

La asignación explícita se denota mediante una flecha "=>". Cuando se especifica, una secuencia de términos de una consulta de búsqueda que coincide con el lateral izquierdo de "=>" se sustituirá por las alternativas de la derecha. Según la regla siguiente, las consultas de búsqueda "Washington", "Wash." o "WA" se reescribirán a "WA". La asignación explícita solo se aplica en la dirección especificada y no reescribe la consulta "WA" a "Washington" en este caso.
```
              Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Enumeración de asignaciones de sinónimos en su servicio

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2016-09-01-Preview
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Obtención de la asignación de sinónimos en su servicio

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Eliminación de la asignación de sinónimos en su servicio

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Configuración de un campo buscable para usar la asignación de sinónimos en la definición del índice

Puede usarse una nueva propiedad de campo **synonymMaps** para especificar una asignación de sinónimos que usar para un campo buscable. Las asignaciones de sinónimos son recursos de nivel de servicio y puede hacerse referencia a ellas mediante cualquier campo del índice en el servicio.

    POST https://[servicename].search.windows.net/indexes?api-version=2016-09-01-Preview
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

**synonymMaps** puede especificarse para los campos buscables del tipo 'Edm.String' o 'Collection(Edm.String)'.

> [!NOTE]
> En esta versión preliminar, solo puede tener una asignación de sinónimos por campo. Si desea usar varias asignaciones de sinónimos, indíquenoslo en [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Repercusión de Sinónimos en otras características de búsqueda

La característica Sinónimos reescribe la consulta original con sinónimos con el operador OR. Por este motivo, el resaltado de referencias y los perfiles de puntuación tratan el término original y los sinónimos como equivalentes.

La característica Sinónimos se aplica a consultas de búsquedas y no se aplica a filtros o facetas. De forma similar, las sugerencias se basan solo en el término original; las coincidencias de sinónimos no aparecen en la respuesta.

Las expansiones de sinónimos no se aplican a los términos de búsqueda de carácter comodín; los prefijos, las coincidencias parciales y las regex no se expanden.

## <a name="tips-for-building-a-synonym-map"></a>Consejos para crear un asignación de sinónimos

- Una asignación de sinónimos concisa y bien diseñada es más eficiente que una lista exhaustiva de posibles coincidencias. Unos diccionarios excesivamente grandes o complejos tardan más en analizarse y afectan a la latencia de la consulta si esta se expande a muchos sinónimos. En lugar de adivinar qué términos pueden usarse, puede obtener los verdaderos términos a través de un [informe de análisis de tráfico de búsqueda](search-traffic-analytics.md).

- Como ejercicio preliminar y de validación, habilite y luego use este informe para determinar de forma precisa qué términos se beneficiarán de una coincidencia de sinónimo y, a continuación, siga usándolo como validación de que la asignación de sinónimos está generando un mejor resultado. En el informe predefinido, los iconos "Consultas de búsquedas más comunes" y "Consultas de búsqueda con resultado cero" le proporcionarán la información necesaria.

- Puede crear varias asignaciones para la aplicación de búsqueda (por ejemplo, mediante el idioma si la aplicación es compatible con una base de cliente de varios idiomas). Actualmente, un campo solo puede usar una de ellas. Puede actualizar una propiedad synonymMaps del campo en cualquier momento.

## <a name="next-steps"></a>Pasos siguientes

- Si dispone de un índice existente en un entorno de desarrollo (no producción), experimente con un diccionario pequeño para ver cómo la adición de sinónimos cambia la experiencia de búsqueda, incluida la repercusión en los perfiles de puntuación, el resaltado de referencias y las sugerencias.

- [Habilite el análisis de tráfico de búsqueda](search-traffic-analytics.md) y use el informe de Power BI predefinido para conocer qué términos se usan con mayor frecuencia y cuáles devuelven cero documentos. Con estos datos, revise el diccionario para incluir sinónimos para consultas que no sean productivas que deban resolverse en documentos en su índice.
