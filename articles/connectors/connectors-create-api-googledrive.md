---
title: "Incorporación del conector de Google Drive a Logic Apps | Microsoft Docs"
description: "Información general del conector de Google Drive con parámetros de la API de REST"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2bcebc5-02d2-435b-b0da-ef53bc51c4b6
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 497f963870d24a335379a7f015a515c425737a73


---
# <a name="get-started-with-the-google-drive-connector"></a>Introducción al conector de Google Drive
Conéctese a Google Drive para crear archivos, obtener filas, etc. Con Google Drive, puede: 

* Compilar el flujo de negocio en función de los datos obtenidos de la búsqueda. 
* Usar acciones para buscar imágenes, noticias, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, puede buscar un vídeo y luego usar Twitter para publicar ese vídeo en una fuente de Twitter.

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
Google Drive incluye las siguientes acciones. No hay desencadenadores. 

| Desencadenadores | Acciones |
| --- | --- |
| None |<ul><li>Crear archivo</li><li>Insertar fila</li><li>Copiar archivo</li><li>Eliminar archivo</li><li>Eliminar fila</li><li>Extraer archivo en la carpeta</li><li>Obtener contenido de archivo mediante el identificador</li><li>Obtener contenido de archivo mediante la ruta de acceso</li><li>Obtener metadatos de archivo mediante el identificador</li><li>Obtener metadatos de archivo mediante la ruta de acceso</li><li>Obtener fila</li><li>Actualizar archivo</li><li>Actualizar fila</li></ul> |

Todos los conectores admiten datos en formato JSON y XML.

## <a name="create-the-connection-to-google-drive"></a>Creación de la conexión a Google Drive
Al agregar este conector a las aplicaciones lógicas, debe autorizar a estas para que se conecten a su Google Drive.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

Después de crear la conexión, especifique las propiedades de Google Drive, como la ruta de acceso a la carpeta o el nombre de archivo. En la **referencia de la API de REST** de este tema se describen estas propiedades.

> [!TIP]
> Puede usar esta misma conexión de Google Drive en otras aplicaciones lógicas.
> 
> 

## <a name="swagger-rest-api-reference"></a>Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### <a name="create-file"></a>Crear archivo
Carga un archivo en Google Drive.  
```POST: /datasets/default/files```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| folderPath |cadena |yes |query |None |Ruta de acceso de carpeta para cargar el archivo en Google Drive |
| Nombre |cadena |yes |query |None |Nombre del archivo que se va a crear en Google Drive |
| body |string(binary) |Sí |body |None |Contenido del archivo que se va a cargar en Google Drive |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="insert-row"></a>Insertar fila
Inserta una fila en una hoja de Google.  
```POST: /datasets/{dataset}/tables/{table}/items```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |None |Identificador único del archivo de hoja de Google |
| table |cadena |Sí |path |None |Identificador único de la hoja de cálculo |
| item |ItemInternalId: string |Sí |body |None |Fila que se va a insertar en la hoja especificada |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="copy-file"></a>Copiar archivo
Copia un archivo en Google Drive.  
```POST: /datasets/default/copyFile```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| de origen |cadena |yes |query |Ninguna |Dirección URL al archivo de origen |
| de destino |cadena |yes |query |None |Ruta de acceso al archivo de destino en Google Drive, incluido el nombre de archivo de destino |
| overwrite |boolean |no |query |Ninguna |Sobrescribe el archivo de destino si está establecido en 'true' |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="delete-file"></a>Eliminar archivo
Elimina un archivo de Google Drive.  
```DELETE: /datasets/default/files/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |cadena |Sí |path |None |Identificador único del archivo que se va a eliminar de Google Drive |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="delete-row"></a>Eliminar fila
Elimina una fila de una hoja de Google.  
```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |None |Identificador único del archivo de hoja de Google |
| table |cadena |Sí |path |None |Identificador único de la hoja de cálculo |
| id |cadena |Sí |path |None |Identificador único de la fila que se va a eliminar |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="extract-archive-to-folder"></a>Extraer archivo en la carpeta
Extrae un archivo de almacenamiento en una carpeta de Google Drive (por ejemplo: .zip).  
```POST: /datasets/default/extractFolderV2```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| de origen |cadena |yes |query |Ninguna |Ruta de acceso al archivo de almacenamiento |
| de destino |cadena |yes |query |None |Ruta de acceso de Google Drive para extraer el contenido del archivo |
| overwrite |boolean |no |query |Ninguna |Sobrescribe los archivos de destino si está establecido en 'true' |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="get-file-content-using-id"></a>Obtener contenido de archivo mediante el identificador
Recupera el contenido del archivo de Google Drive mediante el id.  
```GET: /datasets/default/files/{id}/content```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |cadena |Sí |path |None |Identificador único del archivo que se va a recuperar en Google Drive |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="get-file-content-using-path"></a>Obtener contenido de archivo mediante la ruta de acceso
Recupera el contenido del archivo de Google Drive mediante la ruta de acceso.  
```GET: /datasets/default/GetFileContentByPath```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| path |cadena |yes |query |None |Ruta de acceso del archivo de Google Drive |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="get-file-metadata-using-id"></a>Obtener metadatos de archivo mediante el identificador
Recupera los metadatos del archivo de Google Drive mediante el identificador.  
```GET: /datasets/default/files/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |cadena |Sí |path |None |Identificador único del archivo en Google Drive |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="get-file-metadata-using-path"></a>Obtener metadatos de archivo mediante la ruta de acceso
Recupera los metadatos del archivo de Google Drive mediante la ruta de acceso.  
```GET: /datasets/default/GetFileByPath```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| path |cadena |yes |query |None |Ruta de acceso del archivo de Google Drive |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="get-row"></a>Obtener fila
Recupera una sola fila de una hoja de Google.  
```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |None |Identificador único del archivo de hoja de Google |
| table |cadena |Sí |path |None |Identificador único de la hoja de cálculo |
| id |cadena |Sí |path |None |Identificador único de la fila que se va a recuperar |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="update-file"></a>Actualizar archivo
Actualiza un archivo en Google Drive.  
```PUT: /datasets/default/files/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |cadena |Sí |path |None |Identificador único del archivo que se va a actualizar en Google Drive |
| body |string(binary) |Sí |body |None |Contenido del archivo que se va a cargar en Google Drive |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="update-row"></a>Actualizar fila
Actualiza una fila en una hoja de Google.  
```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |None |Identificador único del archivo de hoja de Google |
| table |cadena |Sí |path |None |Identificador único de la hoja de cálculo |
| id |cadena |Sí |path |None |Identificador único de la fila que se va a actualizar |
| item |ItemInternalId: string |Sí |body |None |Fila con valores actualizados |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

## <a name="object-definitions"></a>Definiciones de objeto
#### <a name="datasetsmetadata"></a>DataSetsMetadata
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| tabular |not defined |no |
| blob |not defined |no |

#### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| de origen |cadena |no |
| DisplayName |cadena |no |
| urlEncoding |cadena |no |
| tableDisplayName |cadena |no |
| tablePluralName |cadena |no |

#### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| de origen |cadena |no |
| DisplayName |cadena |no |
| urlEncoding |cadena |no |

#### <a name="blobmetadata"></a>BlobMetadata
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |cadena |no |
| Nombre |cadena |no |
| DisplayName |cadena |no |
| path |cadena |no |
| LastModified |cadena |no |
| Tamaño |integer |no |
| MediaType |cadena |no |
| IsFolder |boolean |no |
| ETag |cadena |no |
| FileLocator |cadena |no |

#### <a name="tablemetadata"></a>TableMetadata
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Nombre |cadena |no |
| título |cadena |no |
| x-ms-permission |cadena |no |
| schema |not defined |no |

#### <a name="tableslist"></a>TablesList
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| value |array |no |

#### <a name="table"></a>table
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Nombre |cadena |no |
| DisplayName |cadena |no |

#### <a name="item"></a>item
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| ItemInternalId |cadena |no |

#### <a name="itemslist"></a>ItemsList
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| value |array |no |

## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

Volver a la [lista de API](apis-list.md).

<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/connectors-create-api-googledrive/google-developers-console.png
[8]: ./media/connectors-create-api-googledrive/use-google-apis.png
[9]: ./media/connectors-create-api-googledrive/googledrive-api-overview.png
[10]: ./media/connectors-create-api-googledrive/enable-googledrive-api.png
[12]: ./media/connectors-create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/connectors-create-api-googledrive/configure-consent-screen.png
[14]: ./media/connectors-create-api-googledrive/create-client-id.png



<!--HONumber=Jan17_HO3-->


