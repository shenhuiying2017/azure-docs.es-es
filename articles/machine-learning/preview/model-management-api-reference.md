---
title: "Creación de una imagen de Docker para la administración de modelos de Azure Machine Learning | Microsoft Docs"
description: Este documento describe los pasos necesarios para crear una imagen de Docker para el servicio web.
services: machine-learning
author: chhavib
ms.author: chhavib
manager: neerajkh
editor: jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 4c4391cecaf10428b5d4cacf3b39e6a08d417053
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Referencia sobre la API de la cuenta de Administración de modelos de Azure Machine Learning

Si desea conocer la información de configuración del entorno de implementación, consulte [Model Management Account Setup](model-management-configuration.md) (Configuración de la cuenta de Administración de modelos).

La API de administración de modelos de Azure Machine Learning permite implementar las operaciones siguientes:

- Registro de modelos
- Creación de manifiestos
- Creación de imágenes de Docker
- Creación de servicios web

Puede usar esta imagen para crear un servicio web ya sea de forma local o en un clúster remoto de ACS, o en cualquier entorno compatible de Docker de su elección.

## <a name="prerequisites"></a>Requisitos previos
Asegúrese de que ha realizado todos los pasos de instalación descritos en el documento de la [guía de inicio rápido de instalación y creación](quickstart-installation.md).

Antes de continuar necesitará lo siguiente:
1. Aprovisionamiento de una cuenta de Administración de modelos
2. Creación de un entorno para implementar y administrar modelos
3. Un modelo de Machine Learning

### <a name="aad-token"></a>Un token de AAD
Cuando use la CLI, inicie sesión con `az login`. La CLI utiliza el token de AAD del archivo .azure. Si desea usar las API, tiene las siguientes opciones:

##### <a name="acquiring-the-aad-token-manually"></a>Adquirir el token de AAD manualmente
Puede ejecutar el comando `az login` y obtener la versión más reciente del archivo .azure en su directorio particular.

##### <a name="acquiring-the-aad-token-programmatically"></a>Adquirir el token de AAD mediante programación
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
Una vez que cree la entidad de servicio, guarde la salida. Podrá usarla para obtener el token de AAD:

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
El token se coloca en el encabezado de autorización para las llamadas API. Consulte a continuación para más información.


## <a name="register-model"></a>Registro del modelo

El paso del registro del modelo permite registrar el modelo de aprendizaje automático con la cuenta de Administración de modelos de Azure que creó. Este registro le permite realizar un seguimiento de los modelos y de sus versiones que se asignan en el momento del registro. El usuario proporciona el nombre del modelo. El registro posterior de modelos con el mismo nombre genera una nueva versión y un nuevo identificador.

### <a name="request"></a>Solicitud

| Método | URI de solicitud |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Descripción
Registra un modelo

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |
| model | body | La carga que se usa para registrar un modelo | Sí | [Model](#model) |


### <a name="responses"></a>Respuestas
| Código | Descripción | Esquema |
|--------------------|--------------------|--------------------|
| 200 | OK. Registro correcto del modelo | [Model](#model) |
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>Consulta de la lista de modelos de una cuenta
### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Descripción
Permite consultar la lista de modelos de una cuenta. Puede filtrar la lista de resultados mediante la etiqueta y el nombre. Si no se aplica ningún filtro, la consulta mostrará todos los modelos de la cuenta especificada. La lista devuelta está paginada y el número de elementos de cada página es un parámetro opcional

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |
| name | query | El nombre del objeto | No | cadena |
| etiqueta | query | La etiqueta del modelo | No | cadena |
| count | query | El número de elementos que se va a recuperar en una página | No | cadena |
| $skipToken | El token de continuación para recuperar la página siguiente | No | cadena |

### <a name="responses"></a>Respuestas
| Código | Descripción | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcto | [PaginatedModelList](#paginatedmodellist) |
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>Obtención de los detalles de los modelos
### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{id}  
 |

### <a name="description"></a>Descripción
Obtiene los modelos por identificador

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| id | path | El id. del objeto | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |

### <a name="responses"></a>Respuestas
| Código | Descripción | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcto | [Model](#model) |
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>Registro de un manifiesto con el modelo registrado y todas las dependencias

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Descripción
Registra un manifiesto con el modelo registrado y todas las dependencias

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |
| manifestRequest | body | La carga que se usa para registrar un manifiesto | Sí | [Manifest](#manifest) |

### <a name="responses"></a>Respuestas
| Código | Descripción | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Se ha registrado correctamente el manifiesto | [Manifest](#manifest) |
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>Consulta de la lista de manifiestos de una cuenta

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Descripción
Permite consultar la lista de manifiestos de una cuenta. La lista de resultados se puede filtrar por el identificador de modelo y el nombre del manifiesto. Si no se aplica ningún filtro, la consulta mostrará todos los manifiestos de la cuenta especificada. La lista devuelta está paginada y el número de elementos de cada página es un parámetro opcional

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |
| modelId | query | El identificador del modelo | No | cadena |
| manifestName | query | El nombre del manifiesto | No | cadena |
| count | query | El número de elementos que se va a recuperar en una página | No | cadena |
| $skipToken | query | El token de continuación para recuperar la página siguiente | No | cadena |

### <a name="responses"></a>Respuestas
| Código | Descripción | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcto | [PaginatedManifestList](#paginatedmanifestlist) |
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>Obtención de los detalles del manifiesto

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{id} | 

### <a name="description"></a>Descripción
Permite obtener el manifiesto por el identificador

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| id | path | El id. del objeto | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |

### <a name="responses"></a>Respuestas
| Código | Descripción | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcto | [Manifest](#manifest) |
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>Creación de una imagen

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Descripción
Crea una imagen como imagen de Docker en ACR

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |
| imageRequest | body | La carga que se usa para crear una imagen | Sí | [ImageRequest](#imagerequest) |

### <a name="responses"></a>Respuestas
| Código | Descripción | Encabezados | Esquema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | La URL de ubicación de la operación asincrónica. Una llamada GET le mostrará el estado de la tarea de creación de la imagen. | Operation-Location |
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>Consulta de la lista de imágenes de una cuenta

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Descripción
Permite consultar la lista de imágenes de una cuenta. La lista de resultados se puede filtrar por el identificador y el nombre del manifiesto. Si no se aplica ningún filtro, la consulta mostrará todas las imágenes de la cuenta especificada. La lista devuelta está paginada y el número de elementos de cada página es un parámetro opcional.

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |
| manifestId | query | El identificador del manifiesto | No | cadena |
| manifestName | query | El nombre del manifiesto | No | cadena |
| count | query | El número de elementos que se va a recuperar en una página | No | cadena |
| $skipToken | query | El token de continuación para recuperar la página siguiente | No | cadena |

### <a name="responses"></a>Respuestas
| Código | Descripción | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcto | [PaginatedImageList](#paginatedimagelist) |
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>Obtención de detalles de la imagen

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{id} | 

### <a name="description"></a>Descripción
Obtiene la imagen por el identificador

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| id | path | El identificador de la imagen | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |

### <a name="responses"></a>Respuestas
| Código | Descripción | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcto | [Imagen](#image) |
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>Creación de un servicio

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Descripción
Crea un servicio a partir de una imagen

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |
| serviceRequest | body | La carga que se usa para crear un servicio | Sí | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Respuestas
| Código | Descripción | Encabezados | Esquema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | La URL de ubicación de la operación asincrónica. Una llamada GET le mostrará el estado de la tarea de creación del servicio. | Operation-Location |
| 409 | Ya existe un servicio con el nombre especificado |
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>Consulta de la lista de imágenes de una cuenta.

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Descripción
Permite consultar la lista de servicios de una cuenta. La lista de resultados se puede filtrar mediante el nombre o el identificador del modelo, el nombre o el identificador del manifiesto, el identificador de la imagen, el nombre del servicio o el identificador del recurso de procesos de Machine Learning. Si no se aplica ningún filtro, la consulta mostrará todos los servicios de la cuenta. La lista devuelta está paginada y el número de elementos de cada página es un parámetro opcional.

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |
| serviceName | query | El nombre del servicio | No | cadena |
| modelId | query | El nombre del modelo | No | cadena |
| modelName | query | El identificador del modelo | No | cadena |
| manifestId | query | El identificador del manifiesto | No | cadena |
| manifestName | query | El nombre del manifiesto | No | cadena |
| imageId | query | El identificador de la imagen | No | cadena |
| computeResourceId | query | El identificador del recurso de procesos de Machine Learning | No | cadena |
| count | query | El número de elementos que se va a recuperar en una página | No | cadena |
| $skipToken | query | El token de continuación para recuperar la página siguiente | No | cadena |

### <a name="responses"></a>Respuestas
| Código | Descripción | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcto | [PaginatedServiceList](#paginatedservicelist) |
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>Obtención de los detalles del servicio

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Descripción
Obtiene el servicio por el identificador

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| id | path | El id. del objeto | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |

### <a name="responses"></a>Respuestas
| Código | Descripción | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcto | [ServiceResponse](#serviceresponse) |
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>Actualización de un servicio

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| PUT |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Descripción
Actualiza un servicio existente

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| id | path | El id. del objeto | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |
| serviceUpdateRequest | body | La carga que se usa para actualizar un servicio ya existente | Sí |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Respuestas
| Código | Descripción | Encabezados | Esquema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | La URL de ubicación de la operación asincrónica. Una llamada GET le mostrará el estado de la tarea de actualización del servicio. | Operation-Location |
| 404 | El servicio con el identificador especificado no existe |
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>Eliminación de un servicio

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| DELETE |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Descripción
Elimina un servicio

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| id | path | El id. del objeto | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |

### <a name="responses"></a>Respuestas
| Código | Descripción | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcto |  |
| 204 | El servicio con el identificador especificado no existe |
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>Obtención de las claves del servicio

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>Descripción
Permite obtener las claves del servicio

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| id | path | El identificador de servicio | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |

### <a name="responses"></a>Respuestas
| Código | Descripción | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcto | [AuthKeys](#authkeys)
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>Regeneración de las claves del servicio

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>Descripción
Regenera las claves del servicio y las devuelve

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| id | path | El identificador de servicio | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |
| regenerateKeyRequest | body | La carga que se usa para actualizar un servicio ya existente | Sí | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Respuestas
| Código | Descripción | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcto | [AuthKeys](#authkeys)
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>Consulta de la lista de implementaciones de una cuenta.

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments | 

### <a name="description"></a>Descripción
Consulta de la lista de implementaciones de una cuenta. La lista de resultados se puede filtrar por el identificador del servicio, lo cual solo devolverá las implementaciones creadas para ese servicio concreto. Si no se aplica ningún filtro, la consulta mostrará todas las implementaciones de la cuenta especificada.

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |
| serviceId | query | El identificador de servicio | No | cadena |

### <a name="responses"></a>Respuestas
| Código | Descripción | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcto | [DeploymentList](#deploymentlist) |
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>Obtención de los detalles de la implementación

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments/{id} | 

### <a name="description"></a>Descripción
Obtiene la implementación por el identificador

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| id | path | El identificador de la implementación | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |

### <a name="responses"></a>Respuestas
| Código | Descripción | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcto | [Implementación](#deployment) |
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>Obtención de los detalles de la operación

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/operations/{id} | 

### <a name="description"></a>Descripción
Obtiene el estado de la operación asincrónica por el identificador de la operación

### <a name="parameters"></a>parameters
| Nombre | Ubicado en | Descripción | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | El identificador de la suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | El nombre de la cuenta de Administración de modelos | Sí | cadena |
| id | path | El identificador de la operación | Sí | cadena |
| api-version | query | La versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | El token de autorización, que debe ser similar a "Portador XXXXXX" | Sí | cadena |

### <a name="responses"></a>Respuestas
| Código | Descripción | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcto | [OperationStatus](#asyncoperationstatus) |
| default | Respuesta de error que describe el motivo del error de la operación | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Definiciones

<a name="asset"></a>
### <a name="asset"></a>Recurso
El objeto de recurso que se necesitará durante la creación de la imagen de Docker


|Nombre|Descripción|Esquema|
|---|---|---|
|**id**  <br>*opcional*|Identificador del recurso|cadena|
|**mimeType**  <br>*opcional*|El tipo MIME del contenido del modelo. Para más información sobre el tipo MIME, abra https://www.iana.org/assignments/media-types/media-types.xhtml|cadena|
|**unpack**  <br>*opcional*|Indica dónde necesitamos desempaquetar el contenido durante la creación de la imagen de Docker.|boolean|
|**url**  <br>*opcional*|La dirección URL de la ubicación del recurso|cadena|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
El estado de la operación asincrónica

*Escriba*: enum (NotStarted, Running, Cancelled, Succeeded, Failed)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
El estado de la operación


|Nombre|Descripción|Esquema|
|---|---|---|
|**createdTime**  <br>*opcional*  <br>*solo lectura*|La hora de creación de la operación asincrónica (UTC)|string (date-time)|
|**endTime**  <br>*opcional*  <br>*solo lectura*|La hora de finalización de la operación asincrónica (UTC)|string (date-time)|
|**error**  <br>*opcional*||[ErrorResponse](#errorresponse)|
|**id**  <br>*opcional*|El identificador de la operación asincrónica|cadena|
|**operationType**  <br>*opcional*|El tipo de operación asincrónica|enum (Image, Service)|
|**resourceLocation**  <br>*opcional*|El recurso creado o actualizado por la operación asincrónica|cadena|
|**state**  <br>*opcional*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
Las claves de autenticación de un servicio


|Nombre|Descripción|Esquema|
|---|---|---|
|**primaryKey**  <br>*opcional*|La clave principal|cadena|
|**secondaryKey**  <br>*opcional*|La clave secundaria|cadena|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>AutoScaler
Configuración del escalador automático


|Nombre|Descripción|Esquema|
|---|---|---|
|**autoscaleEnabled**  <br>*opcional*|Habilita o deshabilita el escalador automático|boolean|
|**maxReplicas**  <br>*opcional*|El número máximo de réplicas de pod para escalar verticalmente.  <br>**Valor mínimo**: `1`|integer|
|**minReplicas**  <br>*opcional*|El número mínimo de réplicas de pod para reducir verticalmente.  <br>**Valor mínimo**: `0`|integer|
|**refreshPeriodInSeconds**  <br>*opcional*|Hora de actualización del desencadenador del escalado automático.  <br>**Valor mínimo**: `1`|integer|
|**targetUtilization**  <br>*opcional*|Porcentaje de uso en el que se desencadenará el escalado automático.  <br>**Valor mínimo**: `0`  <br>**Valor máximo**: `100`|integer|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
Recurso de procesos de Machine Learning


|Nombre|Descripción|Esquema|
|---|---|---|
|**id**  <br>*opcional*|Identificador de recurso|cadena|
|**type**  <br>*opcional*|Tipo de recurso|enum (Cluster)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
Configuración para reservar recursos del contenedor en el clúster


|Nombre|Descripción|Esquema|
|---|---|---|
|**cpu**  <br>*opcional*|Especifica la reserva de CPU. Formato de Kubernetes: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu|cadena|
|**memory**  <br>*opcional*|Especifica la reserva de memoria. Formato de Kubernetes: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory|cadena|


<a name="deployment"></a>
### <a name="deployment"></a>Implementación
Instancia de una implementación de Azure Machine Learning


|Nombre|Descripción|Esquema|
|---|---|---|
|**createdAt**  <br>*opcional*  <br>*solo lectura*|La hora de creación de la implementación (UTC)|string (date-time)|
|**expiredAt**  <br>*opcional*  <br>*solo lectura*|La hora de expiración de la implementación (UTC)|string (date-time)|
|**id**  <br>*opcional*|El identificador de la implementación|cadena|
|**imageId**  <br>*opcional*|El identificador de la imagen asociado a esta implementación|cadena|
|**serviceName**  <br>*opcional*|El nombre del servicio|cadena|
|**estado**  <br>*opcional*|Estado actual de la implementación|cadena|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
Una matriz de objetos de implementación.

*Escriba*: < [Deployment](#deployment) > array


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Detalles del error del servicio Administración de modelos.


|Nombre|Descripción|Esquema|
|---|---|---|
|**code**  <br>*obligatorio*|Código de error|cadena|
|**message**  <br>*obligatorio*|mensaje de error|cadena|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
Detalles del error del servicio Administración de modelos.


|Nombre|Descripción|Esquema|
|---|---|---|
|**code**  <br>*obligatorio*|Código de error|cadena|
|**details**  <br>*opcional*|Una matriz de objetos de detalles del error.|< [ErrorDetail](#errordetail) > array|
|**message**  <br>*obligatorio*|Mensaje de error|cadena|
|**statusCode**  <br>*opcional*|Código de estado HTTP|integer|


<a name="image"></a>
### <a name="image"></a>Imagen
La imagen de Azure Machine Learning


|Nombre|Descripción|Esquema|
|---|---|---|
|**computeResourceId**  <br>*opcional*|El identificador del entorno creado en el proceso de Machine Learning|cadena|
|**createdTime**  <br>*opcional*|la hora de creación de la imagen (UTC)|string (date-time)|
|**creationState**  <br>*opcional*||[AsyncOperationState](#asyncoperationstate)|
|**descripción**  <br>*opcional*|El texto de descripción de la imagen|cadena|
|**error**  <br>*opcional*||[ErrorResponse](#errorresponse)|
|**id**  <br>*opcional*|El identificador de la imagen|cadena|
|**imageBuildLogUri**  <br>*opcional*|URI de los registros cargados de la compilación de la imagen|cadena|
|**imageLocation**  <br>*opcional*|Cadena de ubicación de Azure Container Registry para la imagen creada|cadena|
|**imageType**  <br>*opcional*||[ImageType](#imagetype)|
|**manifest**  <br>*opcional*||[Manifest](#manifest)|
|**name**  <br>*opcional*|El nombre de la imagen|cadena|
|**version**  <br>*opcional*|La versión de la imagen establecida por el servicio Administración de modelos|integer|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>ImageRequest
Solicitud para crear una imagen de Azure Machine Learning


|Nombre|Descripción|Esquema|
|---|---|---|
|**computeResourceId**  <br>*obligatorio*|El identificador del entorno creado en el proceso de Machine Learning|cadena|
|**descripción**  <br>*opcional*|El texto de descripción de la imagen|cadena|
|**imageType**  <br>*obligatorio*||[ImageType](#imagetype)|
|**manifestId**  <br>*obligatorio*|El identificador del manifiesto desde el que se creará la imagen|cadena|
|**name**  <br>*obligatorio*|El nombre de la imagen|cadena|


<a name="imagetype"></a>
### <a name="imagetype"></a>ImageType
Especifica el tipo de imagen

*Escriba*: enum (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>Manifest
El manifiesto de Azure Machine Learning


|Nombre|Descripción|Esquema|
|---|---|---|
|**assets**  <br>*obligatorio*|La lista de recursos|< [Asset](#asset) > array|
|**createdTime**  <br>*opcional*  <br>*solo lectura*|La hora de creación del manifiesto (UTC)|string (date-time)|
|**descripción**  <br>*opcional*|El texto de descripción del manifiesto|cadena|
|**driverProgram**  <br>*obligatorio*|El programa del controlador del manifiesto.|cadena|
|**id**  <br>*opcional*|El identificador del manifiesto|cadena|
|**modelIds**  <br>*opcional*|Lista de identificadores de modelo de los modelos registrados. La solicitud producirá un error si cualquiera de los modelos incluidos no está registrado|< string > array|
|**modelType**  <br>*opcional*|Especifica que los modelos ya se han registrado con el servicio Administración de modelos|enum (Registered)|
|**name**  <br>*obligatorio*|El nombre del manifiesto|cadena|
|**targetRuntime**  <br>*obligatorio*||[TargetRuntime](#targetruntime)|
|**version**  <br>*opcional*  <br>*solo lectura*|La versión del manifiesto asignada por el servicio Administración de modelos|integer|
|**webserviceType**  <br>*opcional*|Especifica el tipo deseado de servicio web que se creará a partir del manifiesto|enum (Realtime)|


<a name="model"></a>
### <a name="model"></a>Modelo
Instancia de un modelo de Azure Machine Learning


|Nombre|Descripción|Esquema|
|---|---|---|
|**createdAt**  <br>*opcional*  <br>*solo lectura*|La hora de creación del modelo (UTC)|string (date-time)|
|**descripción**  <br>*opcional*|El texto de descripción del modelo|cadena|
|**id**  <br>*opcional*  <br>*solo lectura*|El identificador del modelo|cadena|
|**mimeType**  <br>*obligatorio*|El tipo MIME del contenido del modelo. Para más información sobre el tipo MIME, abra https://www.iana.org/assignments/media-types/media-types.xhtml|cadena|
|**name**  <br>*obligatorio*|El nombre del modelo|cadena|
|**etiquetas**  <br>*opcional*|Lista de etiquetas del modelo|< string > array|
|**unpack**  <br>*opcional*|Indica si necesitamos desempaquetar el modelo durante la creación de la imagen de Docker.|boolean|
|**url**  <br>*obligatorio*|La dirección URL del modelo. Normalmente se coloca una dirección URL SAS aquí.|cadena|
|**version**  <br>*opcional*  <br>*solo lectura*|La versión del modelo asignada por el servicio Administración de modelos|integer|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
La información de la recopilación de datos de modelos


|Nombre|Descripción|Esquema|
|---|---|---|
|**eventHubEnabled**  <br>*opcional*|Habilita el centro de eventos para un servicio|boolean|
|**storageEnabled**  <br>*opcional*|Habilita el almacenamiento para un servicio|boolean|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
Lista paginada de imágenes


|Nombre|Descripción|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Un vínculo de continuación (URI absoluto) a la página siguiente de resultados de la lista.|cadena|
|**value**  <br>*opcional*|Una matriz de objetos del modelo|< [Image](#image) > array|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
Lista paginada de manifiestos.


|Nombre|Descripción|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Un vínculo de continuación (URI absoluto) a la página siguiente de resultados de la lista.|cadena|
|**value**  <br>*opcional*|Una matriz de objetos de manifiesto.|< [Manifest](#manifest) > array|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
Lista paginada de modelos.


|Nombre|Descripción|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Un vínculo de continuación (URI absoluto) a la página siguiente de resultados de la lista.|cadena|
|**value**  <br>*opcional*|Una matriz de objetos del modelo.|< [Model](#model) > array|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
Lista paginada de servicios.


|Nombre|Descripción|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Un vínculo de continuación (URI absoluto) a la página siguiente de resultados de la lista.|cadena|
|**value**  <br>*opcional*|Una matriz de objetos de servicio.|< [ServiceResponse](#serviceresponse) > array|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
Solicitud para crear un servicio


|Nombre|Descripción|Esquema|
|---|---|---|
|**appInsightsEnabled**  <br>*opcional*|Habilita Application Insights para un servicio|boolean|
|**autoScaler**  <br>*opcional*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*obligatorio*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*opcional*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*opcional*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*obligatorio*|La imagen para crear el servicio|cadena|
|**maxConcurrentRequestsPerContainer**  <br>*opcional*|Número máximo de solicitudes simultáneas  <br>**Valor mínimo**: `1`|integer|
|**name**  <br>*obligatorio*|El nombre del servicio|cadena|
|**numReplicas**  <br>*opcional*|El número de réplicas de pod que se ejecutan en un momento dado. No se puede especificar si el escalador automático está habilitado.  <br>**Valor mínimo**: `0`|integer|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
Solicitud para regenerar la clave de un servicio


|Nombre|Descripción|Esquema|
|---|---|---|
|**keyType**  <br>*opcional*|Especifica la clave que desea regenerar|enum (Primary, Secondary)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
Estado detallado del servicio


|Nombre|Descripción|Esquema|
|---|---|---|
|**createdAt**  <br>*opcional*|La hora de creación del servicio (UTC)|string (date-time)|
|**id**  <br>*opcional*|El identificador de servicio|cadena|
|**imagen**  <br>*opcional*||[Imagen](#image)|
|**manifest**  <br>*opcional*||[Manifest](#manifest)|
|**models**  <br>*opcional*|Lista de modelos|< [Model](#model) > array|
|**name**  <br>*opcional*|El nombre del servicio|cadena|
|**scoringUri**  <br>*opcional*|El Uri para puntuar el servicio|cadena|
|**state**  <br>*opcional*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*opcional*|La hora de la última actualización (UTC)|string (date-time)|
|**appInsightsEnabled**  <br>*opcional*|Habilita Application Insights para un servicio|boolean|
|**autoScaler**  <br>*opcional*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*obligatorio*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*opcional*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*opcional*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*opcional*|Número máximo de solicitudes simultáneas  <br>**Valor mínimo**: `1`|integer|
|**numReplicas**  <br>*opcional*|El número de réplicas de pod que se ejecutan en un momento dado. No se puede especificar si el escalador automático está habilitado.  <br>**Valor mínimo**: `0`|integer|
|**error**  <br>*opcional*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>ServiceUpdateRequest
Solicitud para actualizar un servicio


|Nombre|Descripción|Esquema|
|---|---|---|
|**appInsightsEnabled**  <br>*opcional*|Habilita Application Insights para un servicio|boolean|
|**autoScaler**  <br>*opcional*||[AutoScaler](#autoscaler)|
|**containerResourceReservation**  <br>*opcional*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*opcional*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*opcional*|La imagen para crear el servicio|cadena|
|**maxConcurrentRequestsPerContainer**  <br>*opcional*|Número máximo de solicitudes simultáneas  <br>**Valor mínimo**: `1`|integer|
|**numReplicas**  <br>*opcional*|El número de réplicas de pod que se ejecutan en un momento dado. No se puede especificar si el escalador automático está habilitado.  <br>**Valor mínimo**: `0`|integer|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
Tipo de entorno de ejecución de destino.


|Nombre|Descripción|Esquema|
|---|---|---|
|**properties**  <br>*obligatorio*||< string, string > map|
|**runtimeType**  <br>*obligatorio*|Especifica el entorno de ejecución|enum (SparkPython, Python)|

