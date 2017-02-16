---
title: "Introducción al conector de SharePoint Online | Microsoft Docs"
description: "Introducción al uso del conector de SharePoint Online en Logic Apps"
services: 
documentationcenter: 
author: msftman
manager: anneta
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: c462c98e2ecef1afe62ef921265b8842b6ae717d


---
# <a name="get-started-with-the-sharepoint-online-connector"></a>Introducción al conector de SharePoint Online
El conector de SharePoint ofrece una manera de trabajar con listas en SharePoint.

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.
> 
> 

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="lets-talk-about-triggers-and-actions"></a>Hablemos de acciones y desencadenadores
El conector de SharePoint se puede usar como acción (tiene desencadenadores). Todos los conectores admiten datos en formato JSON y XML. 

El conector de SharePoint tiene disponibles las siguientes acciones o desencadenadores:

### <a name="sharepoint-actions"></a>Acciones de SharePoint
Puede realizar estas acciones:

| Acción | Descripción |
| --- | --- |
| GetFileMetadata |Se usa para obtener los metadatos de un archivo en la biblioteca de documentos |
| UpdateFile |Se usa para actualizar un archivo en la biblioteca de documentos |
| DeleteFile |Se usa para eliminar un archivo en la biblioteca de documentos |
| GetFileMetadataByPath |Se usa para obtener los metadatos de un archivo en la biblioteca de documentos |
| GetFileContentByPath |Se usa para obtener un archivo en la biblioteca de documentos |
| GetFileContent |Se usa para obtener un archivo en la biblioteca de documentos |
| CreateFile |Se usa para cargar un archivo en la biblioteca de documentos |
| CopyFile |Se usa para copiar un archivo en la biblioteca de documentos |
| ExtractFolderV2 |Se usa para extraer un archivo en la biblioteca de documentos |
| PostItem |Crea un elemento en una lista de SharePoint |
| GetItem |Recupera un solo elemento de una lista de SharePoint |
| DeleteItem |Elimina un elemento de una lista de SharePoint |
| PatchItem |Actualiza un elemento de una lista de SharePoint |

### <a name="sharepoint-triggers"></a>Desencadenadores de SharePoint
Se pueden escuchar estos eventos:

| Desencadenador | Descripción |
| --- | --- |
| OnNewFile |Desencadena un flujo al crear un archivo en una carpeta de SharePoint |
| OnUpdatedFile |Desencadena un flujo al modificar un archivo en una carpeta de SharePoint |
| GetOnNewItems |Al crear un elemento en una lista de SharePoint |
| GetOnUpdatedItems |Al modificar un elemento existente en una lista de SharePoint |

## <a name="create-a-connection-to-sharepoint"></a>Creación de una conexión a SharePoint
Para usar el conector de SharePoint, cree primero una **conexión** y, después, especifique los detalles de las siguientes propiedades: 

| Propiedad | Obligatorio | Descripción |
| --- | --- | --- |
| Se necesita el cifrado de tokens |Sí |Proporcionar credenciales de SharePoint |

Para poder establecer la conexión con **SharePoint Online**, tiene que proporcionar su identidad (nombre de usuario y contraseña, credenciales de tarjeta inteligente, etc.) a SharePoint Online. Después de autenticarse, podrá usar el conector de SharePoint Online en la aplicación lógica. 

Mientras se encuentra en el diseñador de la aplicación lógica, siga estos pasos para iniciar sesión en SharePoint y crear la **conexión** para su uso en la aplicación lógica:

1. Escriba SharePoint en el cuadro de búsqueda y espere a que la búsqueda devuelva todas las entradas que incluyan SharePoint en el nombre:   
   ![Configurar SharePoint][1]  
2. Seleccione **SharePoint Online - cuando se crea un archivo**   
3. Seleccione **Inicio de sesión en SharePoint Online**:   
   ![Configurar SharePoint][2]    
4. Proporcione sus credenciales de SharePoint para iniciar sesión para autenticarse con SharePoint    
   ![Configurar SharePoint][3]     
5. Una vez completada la autenticación se le redirigirá a la aplicación lógica para terminar mediante la configuración del diálogo de SharePoint **Cuando se crea un archivo** .          
   ![Configurar SharePoint][4]  
6. A continuación, puede agregar otros desencadenadores y acciones que necesita para completar la aplicación lógica.   
7. Guarde el trabajo seleccionando **Guardar** en la barra de menús superior.  

## <a name="sharepoint-rest-api-reference"></a>Referencia de la API de REST de SharePoint
#### <a name="this-documentation-is-for-version-10"></a>Esta documentación corresponde a la versión: 1.0
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>Se usa para obtener los metadatos de un archivo en la biblioteca de documentos
**```GET: /datasets/{dataset}/files/{id}```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |Ninguna |Dirección URL del sitio de SharePoint. Por ejemplo, http://contoso.sharepoint.com/sites/mysite |
| id |cadena |Sí |path |Ninguna |Identificador único del archivo |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

- - -
### <a name="used-for-updating-a-file-on-document-library"></a>Se usa para actualizar un archivo en la biblioteca de documentos
**```PUT: /datasets/{dataset}/files/{id}```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |Ninguna |Dirección URL del sitio de SharePoint. Por ejemplo, http://contoso.sharepoint.com/sites/mysite |
| id |cadena |Sí |path |Ninguna |Identificador único del archivo |
| body | |Sí |body |Ninguna |Contenido del archivo: |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

- - -
### <a name="used-for-deleting-a-file-on-document-library"></a>Se usa para eliminar un archivo en la biblioteca de documentos
**```DELETE: /datasets/{dataset}/files/{id}```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |Ninguna |Dirección URL del sitio de SharePoint. Por ejemplo, http://contoso.sharepoint.com/sites/mysite |
| id |cadena |Sí |path |Ninguna |Identificador único del archivo |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

- - -
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>Se usa para obtener los metadatos de un archivo en la biblioteca de documentos
**```GET: /datasets/{dataset}/GetFileByPath```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |Ninguna |Dirección URL del sitio de SharePoint. Por ejemplo, http://contoso.sharepoint.com/sites/mysite |
| path |cadena |yes |query |Ninguna |Ruta de acceso del archivo |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>Se usa para obtener un archivo en la biblioteca de documentos
**```GET: /datasets/{dataset}/GetFileContentByPath```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |Ninguna |Dirección URL del sitio de SharePoint. Por ejemplo, http://contoso.sharepoint.com/sites/mysite |
| path |cadena |yes |query |Ninguna |Ruta de acceso del archivo |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>Se usa para obtener un archivo en la biblioteca de documentos
**```GET: /datasets/{dataset}/files/{id}/content```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |Ninguna |Dirección URL del sitio de SharePoint. Por ejemplo, http://contoso.sharepoint.com/sites/mysite |
| id |cadena |Sí |path |Ninguna |Identificador único del archivo |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

- - -
### <a name="used-for-uploading-a-file-on-document-library"></a>Se usa para cargar un archivo en la biblioteca de documentos
**```POST: /datasets/{dataset}/files```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |Ninguna |Dirección URL del sitio de SharePoint. Por ejemplo, http://contoso.sharepoint.com/sites/mysite |
| folderPath |cadena |yes |query |Ninguna |Ruta de acceso a la carpeta |
| Nombre |cadena |yes |query |Ninguna |Nombre del archivo |
| body | |Sí |body |Ninguna |Contenido del archivo: |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

- - -
### <a name="used-for-copying-a-file-on-document-library"></a>Se usa para copiar un archivo en la biblioteca de documentos
**```POST: /datasets/{dataset}/copyFile```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |Ninguna |Dirección URL del sitio de SharePoint. Por ejemplo, http://contoso.sharepoint.com/sites/mysite |
| de origen |cadena |yes |query |Ninguna |Ruta de acceso al archivo de origen |
| de destino |cadena |yes |query |Ninguna |Ruta de acceso al archivo de destino |
| overwrite |boolean |no |query |false |Especifica si se sobrescribe un archivo existente |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

- - -
### <a name="triggers-a-flow-when-a-new-file-is-created-in-a-sharepoint-folder"></a>Desencadena un flujo al crear un archivo en una carpeta de SharePoint
**```GET: /datasets/{dataset}/triggers/onnewfile```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |Ninguna |Dirección URL del sitio de SharePoint |
| folderId |cadena |yes |query |Ninguna |Identificador único de la carpeta en SharePoint |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

- - -
### <a name="triggers-a-flow-when-a-file-is-modified-in-a-sharepoint-folder"></a>Desencadena un flujo al modificar un archivo en una carpeta de SharePoint
**```GET: /datasets/{dataset}/triggers/onupdatedfile```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |Ninguna |Dirección URL del sitio de SharePoint |
| folderId |cadena |yes |query |Ninguna |Identificador único de la carpeta en SharePoint |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

- - -
### <a name="used-for-extracting-a-folder-on-document-library"></a>Se usa para extraer un archivo en la biblioteca de documentos
**```POST: /datasets/{dataset}/extractFolderV2```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |Ninguna |Dirección URL del sitio de SharePoint. Por ejemplo, http://contoso.sharepoint.com/sites/mysite |
| de origen |cadena |yes |query |Ninguna |Ruta de acceso al archivo de origen |
| de destino |cadena |yes |query |Ninguna |Ruta de acceso a la carpeta de destino |
| overwrite |boolean |no |query |false |Especifica si se sobrescribe un archivo existente |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

- - -
### <a name="when-a-new-item-is-created-in-a-sharepoint-list"></a>Al crear un elemento en una lista de SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onnewitems```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |Ninguna |URL del sitio de SharePoint (por ejemplo, http://contoso.sharepoint.com/sites/mysite) |
| table |cadena |Sí |path |Ninguna |Nombre de lista de SharePoint |
| $skip |integer |no |query |None |Número de entradas para omitir (valor predeterminado = 0) |
| $top |integer |no |query |None |Número máximo de entradas para recuperar (valor predeterminado = 256) |
| $filter |cadena |no |query |None |Consulta de filtro de ODATA para restringir el número de entradas |
| $orderby |cadena |no |query |None |Consulta orderBy de ODATA para especificar el orden de las entradas |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

- - -
### <a name="when-an-existing-item-is-modified-in-a-sharepoint-list"></a>Al modificar un elemento existente en una lista de SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onupdateditems```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |Ninguna |URL del sitio de SharePoint (por ejemplo, http://contoso.sharepoint.com/sites/mysite) |
| table |cadena |Sí |path |Ninguna |Nombre de lista de SharePoint |
| $skip |integer |no |query |None |Número de entradas para omitir (valor predeterminado = 0) |
| $top |integer |no |query |None |Número máximo de entradas para recuperar (valor predeterminado = 256) |
| $filter |cadena |no |query |None |Consulta de filtro de ODATA para restringir el número de entradas |
| $orderby |cadena |no |query |None |Consulta orderBy de ODATA para especificar el orden de las entradas |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

- - -
### <a name="creates-a-new-item-in-a-sharepoint-list"></a>Crea un elemento en una lista de SharePoint
**```POST: /datasets/{dataset}/tables/{table}/items```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |Ninguna |URL del sitio de SharePoint (por ejemplo, http://contoso.sharepoint.com/sites/mysite) |
| table |cadena |Sí |path |Ninguna |Nombre de lista de SharePoint |
| item | |Sí |body |Ninguna |Elemento que se va a crear |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

- - -
### <a name="retrieves-a-single-item-from-a-sharepoint-list"></a>Recupera un solo elemento de una lista de SharePoint
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |Ninguna |URL del sitio de SharePoint (por ejemplo, http://contoso.sharepoint.com/sites/mysite) |
| table |cadena |Sí |path |Ninguna |Nombre de lista de SharePoint |
| id |integer |Sí |path |Ninguna |Identificador único del elemento que se va a recuperar |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

- - -
### <a name="deletes-an-item-from-a-sharepoint-list"></a>Elimina un elemento de una lista de SharePoint
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |Ninguna |URL del sitio de SharePoint (por ejemplo, http://contoso.sharepoint.com/sites/mysite) |
| table |cadena |Sí |path |Ninguna |Nombre de lista de SharePoint |
| id |integer |Sí |path |Ninguna |Identificador único del elemento que se va a eliminar |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

- - -
### <a name="updates-an-item-in-a-sharepoint-list"></a>Actualiza un elemento de una lista de SharePoint
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| dataset |cadena |Sí |path |Ninguna |URL del sitio de SharePoint (por ejemplo, http://contoso.sharepoint.com/sites/mysite) |
| table |cadena |Sí |path |Ninguna |Nombre de lista de SharePoint |
| id |integer |Sí |path |Ninguna |Identificador único del elemento que se va a actualizar |
| item | |Sí |body |Ninguna |Elemento con propiedades cambiadas |

### <a name="here-are-the-possible-responses"></a>Estas son las posibles respuestas:
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| default |Error en la operación. |

- - -
## <a name="object-definitions"></a>Definiciones de objeto:
 **DataSetsMetadata**:

Propiedades obligatorias para DataSetsMetadata:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| tabular |not defined |
| blob |not defined |

 **TabularDataSetsMetadata**:

Propiedades obligatorias para TabularDataSetsMetadata:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| de origen |cadena |
| DisplayName |cadena |
| urlEncoding |cadena |
| tableDisplayName |cadena |
| tablePluralName |cadena |

 **BlobDataSetsMetadata**:

Propiedades obligatorias para BlobDataSetsMetadata:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| de origen |cadena |
| DisplayName |cadena |
| urlEncoding |cadena |

 **BlobMetadata**:

Propiedades obligatorias para BlobMetadata:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| id |cadena |
| Nombre |cadena |
| DisplayName |cadena |
| path |cadena |
| LastModified |cadena |
| Tamaño |integer |
| MediaType |cadena |
| IsFolder |boolean |
| ETag |cadena |
| FileLocator |cadena |

 **Objeto**:

Propiedades obligatorias para Object:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
|  | |

 **TableMetadata**:

Propiedades obligatorias para TableMetadata:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| Nombre |cadena |
| título |cadena |
| x-ms-permission |cadena |
| schema |not defined |

 **DataSetsList**:

Propiedades obligatorias para DataSetsList:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| value |array |

 **DataSet**:

Propiedades obligatorias para DataSet:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| Nombre |cadena |
| DisplayName |cadena |

 **Tabla**:

Propiedades obligatorias para Table:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| Nombre |cadena |
| DisplayName |cadena |

 **Elemento**:

Propiedades obligatorias para Item:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| ItemInternalId |cadena |

 **ItemsList**:

Propiedades obligatorias para ItemsList:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| value |array |

 **TablesList**:

Propiedades obligatorias para TablesList:

Ninguna de las propiedades es obligatoria. 

**Todas las propiedades**: 

| Nombre | Tipo de datos |
| --- | --- |
| value |array |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md)  

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png



<!--HONumber=Jan17_HO3-->


