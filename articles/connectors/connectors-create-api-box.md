---
title: "Incorporación del conector de Box a Logic Apps | Microsoft Docs"
description: "Información general del conector de Box con parámetros de la API de REST"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 949579cf-a81c-4790-9ef5-fe39b4fbd0c5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: bb6bef230f4ba4f7019fd2f120c2115d97649b81


---
# <a name="get-started-with-the-box-connector"></a>Introducción al conector de Box
Conéctese a Box y cree y elimine archivos, entre muchas otras cosas. 

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.
> 
> 

Con Box, puede hacer lo siguiente:

* Compilar el flujo de negocio en función de los datos que obtiene de Box. 
* Usar desencadenadores para cuando se crea o actualiza un archivo.
* Usar acciones que copian o eliminan archivos, entre otras muchas cosas. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando se modifique un archivo en Box, puede tomar ese archivo y enviarlo por correo electrónico mediante Office 365.

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
Box incluye los siguientes desencadenadores y acciones.

| Desencadenadores | Acciones |
| --- | --- |
| <ul><li>Cuando se crea un archivo</li><li>Cuando se modifica un archivo</li></ul> |<ul><li>Crear archivo</li><li>Cuando se crea un archivo</li><li>Copiar archivo</li><li>Eliminar archivo</li><li>Extraer archivo en la carpeta</li><li>Obtener contenido de archivo mediante el identificador</li><li>Obtener contenido de archivo mediante la ruta de acceso</li><li>Obtener metadatos de archivo mediante el identificador</li><li>Obtener metadatos de archivo mediante la ruta de acceso</li><li>Actualizar archivo</li><li>Cuando se modifica un archivo</li></ul> |

Todos los conectores admiten datos en formato JSON y XML.

## <a name="create-a-connection-to-box"></a>Creación de una conexión a Box
Al agregar este conector a las aplicaciones lógicas, debe autorizar a estas para que se conecten a su Box.

> [!INCLUDE [Steps to create a connection to box](../../includes/connectors-create-api-box.md)]
> 
> 

Después de crear la conexión, escriba las propiedades de Box. En la **referencia de la API de REST** de este tema se describen estas propiedades.

> [!TIP]
> Puede usar esta misma conexión de Box en otras aplicaciones lógicas.
> 
> 

## <a name="swagger-rest-api-reference"></a>Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### <a name="create-file"></a>Crear archivo
Carga un archivo en Box.  
```POST: /datasets/default/files```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| folderPath |cadena |Sí |query |None |Ruta de acceso de la carpeta para cargar el archivo en Box |
| Nombre |cadena |Sí |query |None |Nombre del archivo que se va a crear en Box |
| body |string(binary) |Sí |body |None |Contenido del archivo que se va a cargar en Box |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="when-a-file-is-created"></a>Cuando se crea un archivo
Desencadena un flujo cuando se crea un nuevo archivo en una carpeta de Box.  
```GET: /datasets/default/triggers/onnewfile```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| folderId |cadena |Sí |query |None |Identificador único de la carpeta en Box |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="copy-file"></a>Copiar archivo
Copia un archivo en Box.  
```POST: /datasets/default/copyFile```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| de origen |cadena |Sí |query |None |Dirección URL al archivo de origen |
| de destino |cadena |Sí |query |None |Ruta de acceso al archivo de destino en Box, incluido el nombre del archivo de destino |
| overwrite |boolean |No |query |None |Sobrescribe el archivo de destino si está establecido en 'true' |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="delete-file"></a>Eliminar archivo
Elimina un archivo de Box.  
```DELETE: /datasets/default/files/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |cadena |Sí |path |None |Identificador único del archivo que se va a eliminar de Box |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="extract-archive-to-folder"></a>Extraer archivo en la carpeta
Extrae un archivo de almacenamiento en una carpeta de Box (ejemplo: .zip).  
```POST: /datasets/default/extractFolderV2```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| de origen |cadena |Sí |query | |Ruta de acceso al archivo de almacenamiento |
| de destino |cadena |Sí |query | |Ruta de acceso de Box para extraer el contenido del archivo |
| overwrite |boolean |No |query | |Sobrescribe los archivos de destino si está establecido en 'true' |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="get-file-content-using-id"></a>Obtener contenido de archivo mediante el identificador
Recupera el contenido del archivo de Box mediante el identificador.  
```GET: /datasets/default/files/{id}/content```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |cadena |Sí |path |None |Identificador único del archivo en Box |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="get-file-content-using-path"></a>Obtener contenido de archivo mediante la ruta de acceso
Recupera el contenido del archivo de Box mediante la ruta de acceso.  
```GET: /datasets/default/GetFileContentByPath```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| path |cadena |Sí |query |None |Ruta de acceso única al archivo en Box |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="get-file-metadata-using-id"></a>Obtener metadatos de archivo mediante el identificador
Recupera los metadatos del archivo de Box mediante el identificador de archivo.  
```GET: /datasets/default/files/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |cadena |Sí |path |None |Identificador único del archivo en Box |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="get-file-metadata-using-path"></a>Obtener metadatos de archivo mediante la ruta de acceso
Recupera los metadatos del archivo de Box mediante la ruta de acceso.  
```GET: /datasets/default/GetFileByPath```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| path |cadena |Sí |query |None |Ruta de acceso única al archivo en Box |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="update-file"></a>Actualizar archivo
Actualiza un archivo en Box.  
```PUT: /datasets/default/files/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |cadena |Sí |path |None |Identificador único del archivo que se va a actualizar en Box |
| body |string(binary) |Sí |body |None |Contenido del archivo que se va a actualizar en Box |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

### <a name="when-a-file-is-modified"></a>Cuando se modifica un archivo
Desencadena un flujo cuando se modifica un archivo en una carpeta de Box.  
```GET: /datasets/default/triggers/onupdatedfile```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| folderId |cadena |Sí |query |None |Identificador único de la carpeta en Box |

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

## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).




<!--HONumber=Jan17_HO3-->


