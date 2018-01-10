---
title: "Creación de una imagen de Docker para la administración de modelos de Azure Machine Learning | Microsoft Docs"
description: "En este artículo se describen los pasos para crear una imagen de Docker para el servicio web."
services: machine-learning
author: chhavib
ms.author: chhavib
manager: neerajkh
editor: jasonwhowell
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 134971e4a663baefa4e1051f087038d3debcb969
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Referencia sobre la API de la cuenta de Administración de modelos de Azure Machine Learning

Para obtener información sobre la configuración del entorno de implementación, consulte [Model Management Account Setup](deployment-setup-configuration.md) (Configuración de la cuenta de Administración de modelos).

La API de la cuenta de Administración de modelos de Azure Machine Learning permite implementar las operaciones siguientes:

- Registro de modelos
- Creación de manifiestos
- Creación de imágenes de Docker
- Creación de servicios web

Puede usar esta imagen para crear un servicio web, ya sea de forma local o en un clúster remoto de Azure Container Service, o en cualquier otro entorno compatible de Docker de su elección.

## <a name="prerequisites"></a>requisitos previos
Asegúrese de que ha realizado todos los pasos de instalación descritos en el documento de la [guía de inicio rápido de instalación y creación](quickstart-installation.md).

Antes de continuar necesitará lo siguiente:
1. Aprovisionamiento de una cuenta de Administración de modelos
2. Creación de un entorno para implementar y administrar modelos
3. Un modelo de Machine Learning

### <a name="azure-ad-token"></a>Token de Azure AD
Cuando use CLI de Azure, inicie sesión con `az login`. CLI utiliza el token de Azure Active Directory (Azure AD) del archivo .azure. Si quiere usar las API, tiene las siguientes opciones.

##### <a name="acquire-the-azure-ad-token-manually"></a>Adquirir el token de Azure AD manualmente
Puede utilizar `az login` y obtener el último token del archivo .azure en su directorio particular.

##### <a name="acquire-the-azure-ad-token-programmatically"></a>Adquirir el token de Azure AD mediante programación
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
Después de crear la entidad de servicio, guarde la salida. Ahora puede usarla para obtener un token de Azure AD:

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
El token se coloca en un encabezado de autorización para las llamadas API.


## <a name="register-a-model"></a>Registrar un modelo

El paso del registro del modelo permite registrar el modelo de Machine Learning con la cuenta de Administración de modelos de Azure que ha creado. Este registro le permite realizar un seguimiento de los modelos y de sus versiones que se asignan en el momento del registro. El usuario proporciona el nombre del modelo. El registro posterior de modelos con el mismo nombre genera una nueva versión y un nuevo identificador.

### <a name="request"></a>Solicitud

| Método | URI de solicitud |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>DESCRIPCIÓN
Registra un modelo.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |
| model | Cuerpo | Carga que se usa para registrar un modelo. | Sí | [Model](#model) |


### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Aceptar. Registro correcto del modelo. | [Model](#model) |
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>Permite consultar la lista de modelos de una cuenta
### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>DESCRIPCIÓN
Permite consultar la lista de modelos de una cuenta. Puede filtrar la lista de resultados por etiqueta y nombre. Si no se aplica ningún filtro, la consulta mostrará todos los modelos de la cuenta. La lista devuelta está paginada y el número de elementos de cada página es un parámetro opcional.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |
| Nombre | query | Nombre de objeto. | Sin  | cadena |
| etiqueta | query | Etiqueta del modelo. | Sin  | cadena |
| count | query | Número de elementos que se van a recuperar en una página. | Sin  | cadena |
| $skipToken | query | Token de continuación para recuperar la página siguiente. | Sin  | cadena |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcta. | [PaginatedModelList](#paginatedmodellist) |
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>Obtención de los detalles de los modelos
### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{id}  
 |

### <a name="description"></a>DESCRIPCIÓN
Obtiene un modelo por identificador.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| id | path | Identificador de objeto. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcta. | [Model](#model) |
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>Registro de un manifiesto con el modelo registrado y todas las dependencias

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>DESCRIPCIÓN
Registro de un manifiesto con el modelo registrado y todas las dependencias.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |
| manifestRequest | Cuerpo | Carga que se usa para registrar un manifiesto. | Sí | [Manifest](#manifest) |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Se ha registrado correctamente el manifiesto. | [Manifest](#manifest) |
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>Permite consultar la lista de manifiestos de una cuenta

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>DESCRIPCIÓN
Permite consultar la lista de manifiestos de una cuenta. Puede filtrar la lista de resultados por el identificador de modelo y el nombre del manifiesto. Si no se aplica ningún filtro, la consulta mostrará todos los manifiestos de la cuenta. La lista devuelta está paginada y el número de elementos de cada página es un parámetro opcional.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |
| modelId | query | Identificador del modelo. | Sin  | cadena |
| manifestName | query | Nombre del manifiesto. | Sin  | cadena |
| count | query | Número de elementos que se van a recuperar en una página. | Sin  | cadena |
| $skipToken | query | Token de continuación para recuperar la página siguiente. | Sin  | cadena |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcta. | [PaginatedManifestList](#paginatedmanifestlist) |
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>Obtención de los detalles del manifiesto

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{id} | 

### <a name="description"></a>DESCRIPCIÓN
Permite obtener el manifiesto por el identificador.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| id | path | Identificador de objeto. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcta. | [Manifest](#manifest) |
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>Crear una imagen

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>DESCRIPCIÓN
Crea una imagen como una imagen de Docker en Azure Container Registry.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |
| imageRequest | Cuerpo | Carga que se usa para crear una imagen. | Sí | [ImageRequest](#imagerequest) |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | encabezados | Esquema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL de ubicación de la operación asincrónica. Una llamada GET le mostrará el estado de la tarea de creación de la imagen. | Operation-Location |
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>Consulta de la lista de imágenes de una cuenta

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>DESCRIPCIÓN
Permite consultar la lista de imágenes de una cuenta. Puede filtrar la lista de resultados por el identificador y el nombre del manifiesto. Si no se aplica ningún filtro, la consulta mostrará todas las imágenes de la cuenta. La lista devuelta está paginada y el número de elementos de cada página es un parámetro opcional.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |
| manifestId | query | Identificador del manifiesto. | Sin  | cadena |
| manifestName | query | Nombre del manifiesto. | Sin  | cadena |
| count | query | Número de elementos que se van a recuperar en una página. | Sin  | cadena |
| $skipToken | query | Token de continuación para recuperar la página siguiente. | Sin  | cadena |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcta. | [PaginatedImageList](#paginatedimagelist) |
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>Obtención de detalles de la imagen

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{id} | 

### <a name="description"></a>DESCRIPCIÓN
Obtiene una imagen por el identificador.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| id | path | Identificador de la imagen. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcta. | [Imagen](#image) |
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>Crear un servicio

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>DESCRIPCIÓN
Crea un servicio a partir de una imagen.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |
| serviceRequest | Cuerpo | Carga que se usa para crear un servicio. | Sí | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | encabezados | Esquema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL de ubicación de la operación asincrónica. Una llamada GET le mostrará el estado de la tarea de creación del servicio. | Operation-Location |
| 409 | Ya existe un servicio con el nombre especificado. |
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>Consulta de la lista de imágenes de una cuenta

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>DESCRIPCIÓN
Consulta de la lista de imágenes de una cuenta. Puede filtrar la lista de resultados por el nombre o el identificador del modelo, el nombre o el identificador del manifiesto, el identificador de la imagen, el nombre del servicio o el identificador del recurso de procesos de Machine Learning. Si no se aplica ningún filtro, la consulta mostrará todos los servicios de la cuenta. La lista devuelta está paginada y el número de elementos de cada página es un parámetro opcional.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |
| serviceName | query | Nombre del servicio. | Sin  | cadena |
| modelId | query | Nombre del modelo. | Sin  | cadena |
| modelName | query | Identificador del modelo. | Sin  | cadena |
| manifestId | query | Identificador del manifiesto. | Sin  | cadena |
| manifestName | query | Nombre del manifiesto. | Sin  | cadena |
| imageId | query | Identificador de la imagen. | Sin  | cadena |
| computeResourceId | query | Identificador del recurso de procesos de Machine Learning. | Sin  | cadena |
| count | query | Número de elementos que se van a recuperar en una página. | Sin  | cadena |
| $skipToken | query | Token de continuación para recuperar la página siguiente. | Sin  | cadena |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcta. | [PaginatedServiceList](#paginatedservicelist) |
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>Obtención de los detalles del servicio

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>DESCRIPCIÓN
Obtiene un servicio por el identificador.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| id | path | Identificador de objeto. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcta. | [ServiceResponse](#serviceresponse) |
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>Actualización de un servicio

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| PUT |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>DESCRIPCIÓN
Actualiza un servicio existente.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| id | path | Identificador de objeto. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |
| serviceUpdateRequest | Cuerpo | Carga que se usa para actualizar un servicio existente. | Sí |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | encabezados | Esquema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL de ubicación de la operación asincrónica. Una llamada GET le mostrará el estado de la tarea de actualización del servicio. | Operation-Location |
| 404 | El servicio con el identificador especificado no existe. |
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>Eliminar un servicio

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| DELETE |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>DESCRIPCIÓN
Elimina un servicio.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| id | path | Identificador de objeto. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcta. |  |
| 204 | El servicio con el identificador especificado no existe. |
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>Obtención de las claves del servicio

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>DESCRIPCIÓN
Permite obtener las claves del servicio.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| id | path | Identificador del servicio. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcta. | [AuthKeys](#authkeys)
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>Regeneración de las claves del servicio

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>DESCRIPCIÓN
Regenera las claves del servicio y las devuelve.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| id | path | Identificador del servicio. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |
| regenerateKeyRequest | Cuerpo | Carga que se usa para actualizar un servicio existente. | Sí | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcta. | [AuthKeys](#authkeys)
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>Consulta de la lista de implementaciones de una cuenta

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments | 

### <a name="description"></a>DESCRIPCIÓN
Consulta de la lista de implementaciones de una cuenta. Puede filtrar la lista de resultados por el identificador del servicio, lo cual solo devolverá las implementaciones creadas para ese servicio concreto. Si no se aplica ningún filtro, la consulta mostrará todas las implementaciones de la cuenta.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |
| serviceId | query | Identificador del servicio. | Sin  | cadena |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcta. | [DeploymentList](#deploymentlist) |
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>Obtención de los detalles de la implementación

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments/{id} | 

### <a name="description"></a>DESCRIPCIÓN
Obtiene la implementación por el identificador.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| id | path | Identificador de la implementación. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcta. | [Implementación](#deployment) |
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>Obtención de los detalles de la operación

### <a name="request"></a>Solicitud
| Método | URI de solicitud |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/operations/{id} | 

### <a name="description"></a>DESCRIPCIÓN
Obtiene el estado de la operación asincrónica por el identificador de la operación.

### <a name="parameters"></a>Parámetros
| NOMBRE | Ubicado en | DESCRIPCIÓN | Obligatorio | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Identificador de suscripción de Azure | Sí | cadena |
| resourceGroupName | path | Nombre del grupo de recursos en el que se encuentra la cuenta de Administración de modelos. | Sí | cadena |
| accountName | path | Nombre de la cuenta de Administración de modelos. | Sí | cadena |
| id | path | Identificador de la operación. | Sí | cadena |
| api-version | query | Versión de la API del proveedor de recursos Microsoft.Machine.Learning que se utilizará. | Sí | cadena |
| Autorización | encabezado | Token de autorización. Debe ser similar a "Portador XXXXXX". | Sí | cadena |

### <a name="responses"></a>Respuestas
| Código | DESCRIPCIÓN | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Correcta. | [OperationStatus](#asyncoperationstatus) |
| default | Respuesta de error que describe el motivo del error de la operación. | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Definiciones

<a name="asset"></a>
### <a name="asset"></a>Recurso
El objeto de recurso que se necesitará durante la creación de la imagen de Docker.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**id**  <br>*opcional*|Identificador del recurso.|cadena|
|**mimeType**  <br>*opcional*|Tipo MIME del contenido del modelo. Para obtener más información sobre el tipo MIME, consulte la [lista de tipos de medios IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|cadena|
|**unpack**  <br>*opcional*|Indica dónde debemos desempaquetar el contenido durante la creación de la imagen de Docker.|boolean|
|**url**  <br>*opcional*|Dirección URL de la ubicación del recurso.|cadena|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
El estado de la operación asincrónica.

*Tipo*: enum (NotStarted, Running, Cancelled, Succeeded, Failed)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
El estado de la operación.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**createdTime**  <br>*opcional*  <br>*solo lectura*|Hora de creación de la operación asincrónica (UTC).|string (date-time)|
|**endTime**  <br>*opcional*  <br>*solo lectura*|Hora de finalización de la operación asincrónica (UTC).|string (date-time)|
|**error**  <br>*opcional*||[ErrorResponse](#errorresponse)|
|**id**  <br>*opcional*|Identificador de la operación asincrónica.|cadena|
|**operationType**  <br>*opcional*|Tipo de operación asincrónica.|enum (Image, Service)|
|**resourceLocation**  <br>*opcional*|Recurso creado o actualizado por la operación asincrónica.|cadena|
|**state**  <br>*opcional*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
Las claves de autenticación de un servicio.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**primaryKey**  <br>*opcional*|Clave principal.|cadena|
|**secondaryKey**  <br>*opcional*|Clave secundaria.|cadena|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>AutoScaler
Configuración del Autoscaler.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**autoscaleEnabled**  <br>*opcional*|Habilita o deshabilita el AutoScaler.|boolean|
|**maxReplicas**  <br>*opcional*|Número máximo de réplicas de pod para escalar verticalmente.  <br>**Valor mínimo**: `1`|integer|
|**minReplicas**  <br>*opcional*|Número mínimo de réplicas de pod para reducir verticalmente.  <br>**Valor mínimo**: `0`|integer|
|**refreshPeriodInSeconds**  <br>*opcional*|Hora de actualización del desencadenador del escalado automático.  <br>**Valor mínimo**: `1`|integer|
|**targetUtilization**  <br>*opcional*|Porcentaje de utilización que desencadena el escalado automático.  <br>**Valor mínimo**: `0`  <br>**Valor máximo**: `100`|integer|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
El recurso de procesos de Machine Learning.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**id**  <br>*opcional*|Identificador del recurso.|cadena|
|**type**  <br>*opcional*|Tipo de recurso.|enum (Cluster)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
Configuración para reservar recursos para un contenedor en el clúster.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**cpu**  <br>*opcional*|Especifica la reserva de CPU. Formato de Kubernetes: consulte [Significado de CPU](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu).|cadena|
|**memory**  <br>*opcional*|Especifica la reserva de memoria. Formato de Kubernetes: consulte [Significado de memoria](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory).|cadena|


<a name="deployment"></a>
### <a name="deployment"></a>Implementación
Una instancia de una implementación de Azure Machine Learning.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**createdAt**  <br>*opcional*  <br>*solo lectura*|Hora de creación de la implementación (UTC).|string (date-time)|
|**expiredAt**  <br>*opcional*  <br>*solo lectura*|Hora de expiración de la implementación (UTC).|string (date-time)|
|**id**  <br>*opcional*|Identificador de la implementación.|cadena|
|**imageId**  <br>*opcional*|Identificador de la imagen asociado a esta implementación.|cadena|
|**serviceName**  <br>*opcional*|Nombre del servicio.|cadena|
|**estado**  <br>*opcional*|Estado actual de la implementación.|cadena|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
Una matriz de objetos de implementación.

*Tipo*: matriz <[implementación](#deployment)>


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Detalles del error del servicio Administración de modelos.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**code**  <br>*requerido*|Código de error.|cadena|
|**message**  <br>*requerido*|Mensaje de error.|cadena|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
Un objeto de error del servicio Administración de modelos.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**code**  <br>*requerido*|Código de error.|cadena|
|**details**  <br>*opcional*|Matriz de objetos de detalles del error.|Matriz <[ErrorDetail](#errordetail)>|
|**message**  <br>*requerido*|Mensaje de error.|cadena|
|**statusCode**  <br>*opcional*|Código de estado HTTP.|integer|


<a name="image"></a>
### <a name="image"></a>Imagen
La imagen de Azure Machine Learning.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**computeResourceId**  <br>*opcional*|Identificador del entorno creado en el recurso de procesos de Machine Learning.|cadena|
|**createdTime**  <br>*opcional*|Hora de creación de la imagen (UTC).|string (date-time)|
|**creationState**  <br>*opcional*||[AsyncOperationState](#asyncoperationstate)|
|**descripción**  <br>*opcional*|Texto de descripción de la imagen.|cadena|
|**error**  <br>*opcional*||[ErrorResponse](#errorresponse)|
|**id**  <br>*opcional*|Identificador de la imagen.|cadena|
|**imageBuildLogUri**  <br>*opcional*|URI de los registros cargados de la compilación de la imagen.|cadena|
|**imageLocation**  <br>*opcional*|Cadena de ubicación de Azure Container Registry para la imagen creada.|cadena|
|**imageType**  <br>*opcional*||[ImageType](#imagetype)|
|**manifest**  <br>*opcional*||[Manifest](#manifest)|
|**name**  <br>*opcional*|Nombre de la imagen.|cadena|
|**version**  <br>*opcional*|Versión de la imagen establecida por el servicio Administración de modelos.|integer|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>ImageRequest
Una solicitud para crear una imagen de Azure Machine Learning.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**computeResourceId**  <br>*requerido*|Identificador del entorno creado en el recurso de procesos de Machine Learning.|cadena|
|**descripción**  <br>*opcional*|Texto de descripción de la imagen.|cadena|
|**imageType**  <br>*requerido*||[ImageType](#imagetype)|
|**manifestId**  <br>*requerido*|Identificador del manifiesto desde el que se creará la imagen.|cadena|
|**name**  <br>*requerido*|Nombre de la imagen.|cadena|


<a name="imagetype"></a>
### <a name="imagetype"></a>ImageType
Especifica el tipo de imagen.

*Tipo*: enum (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>Manifest
El manifiesto de Azure Machine Learning.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**assets**  <br>*requerido*|Lista de recursos.|Matriz <[Asset](#asset)>|
|**createdTime**  <br>*opcional*  <br>*solo lectura*|Hora de creación del manifiesto (UTC).|string (date-time)|
|**descripción**  <br>*opcional*|Texto de descripción del manifiesto.|cadena|
|**driverProgram**  <br>*requerido*|Programa del controlador del manifiesto.|cadena|
|**id**  <br>*opcional*|Identificador del manifiesto.|cadena|
|**modelIds**  <br>*opcional*|Lista de identificadores de modelo de los modelos registrados. La solicitud producirá un error si cualquiera de los modelos incluidos no está registrado.|Matriz <string>|
|**modelType**  <br>*opcional*|Especifica que los modelos ya se han registrado con el servicio Administración de modelos.|enum (Registered)|
|**name**  <br>*requerido*|Nombre del manifiesto.|cadena|
|**targetRuntime**  <br>*requerido*||[TargetRuntime](#targetruntime)|
|**version**  <br>*opcional*  <br>*solo lectura*|Versión del manifiesto asignada por el servicio Administración de modelos.|integer|
|**webserviceType**  <br>*opcional*|Especifica el tipo deseado de servicio web que se creará a partir del manifiesto.|enum (Realtime)|


<a name="model"></a>
### <a name="model"></a>Modelo
Una instancia de un modelo de Azure Machine Learning.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**createdAt**  <br>*opcional*  <br>*solo lectura*|Hora de creación del modelo (UTC).|string (date-time)|
|**descripción**  <br>*opcional*|Texto de descripción del modelo.|cadena|
|**id**  <br>*opcional*  <br>*solo lectura*|Identificador del modelo.|cadena|
|**mimeType**  <br>*requerido*|Tipo MIME del contenido del modelo. Para obtener más información sobre el tipo MIME, consulte la [lista de tipos de medios IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|cadena|
|**name**  <br>*requerido*|Nombre del modelo.|cadena|
|**etiquetas**  <br>*opcional*|Lista de etiquetas del modelo.|Matriz <string>|
|**unpack**  <br>*opcional*|Indica si debemos desempaquetar el modelo durante la creación de la imagen de Docker.|boolean|
|**url**  <br>*requerido*|Dirección URL del modelo. Normalmente aquí se coloca una dirección URL de firma de acceso compartido.|cadena|
|**version**  <br>*opcional*  <br>*solo lectura*|Versión del modelo asignada por el servicio Administración de modelos.|integer|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
La información de la recopilación de datos de modelos.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**eventHubEnabled**  <br>*opcional*|Habilita un centro de eventos para un servicio.|boolean|
|**storageEnabled**  <br>*opcional*|Habilita el almacenamiento para un servicio.|boolean|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
Una lista paginada de imágenes.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Vínculo de continuación (URI absoluto) a la página siguiente de resultados de la lista.|cadena|
|**value**  <br>*opcional*|Matriz de objetos del modelo.|Matriz <[Image](#image)>|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
Una lista paginada de manifiestos.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Vínculo de continuación (URI absoluto) a la página siguiente de resultados de la lista.|cadena|
|**value**  <br>*opcional*|Matriz de objetos de manifiesto.|Matriz <[Manifest](#manifest)>|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
Una lista paginada de modelos.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Vínculo de continuación (URI absoluto) a la página siguiente de resultados de la lista.|cadena|
|**value**  <br>*opcional*|Matriz de objetos del modelo.|Matriz <[Model](#model)>|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
Una lista paginada de servicios.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Vínculo de continuación (URI absoluto) a la página siguiente de resultados de la lista.|cadena|
|**value**  <br>*opcional*|Matriz de objetos de servicio.|Matriz <[ServiceResponse](#serviceresponse)>|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
Una solicitud para crear un servicio.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**appInsightsEnabled**  <br>*opcional*|Habilita Application Insights para un servicio.|boolean|
|**autoScaler**  <br>*opcional*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*requerido*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*opcional*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*opcional*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*requerido*|Imagen para crear el servicio.|cadena|
|**maxConcurrentRequestsPerContainer**  <br>*opcional*|Número máximo de solicitudes simultáneas.  <br>**Valor mínimo**: `1`|integer|
|**name**  <br>*requerido*|Nombre del servicio.|cadena|
|**numReplicas**  <br>*opcional*|Número de réplicas de pod que se ejecutan en un momento dado. No se puede especificar si el escalador automático está habilitado.  <br>**Valor mínimo**: `0`|integer|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
Una solicitud para regenerar la clave de un servicio.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**keyType**  <br>*opcional*|Especifica la clave que desea volver a generar.|enum (Primary, Secondary)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
El estado detallado del servicio.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**createdAt**  <br>*opcional*|Hora de creación del servicio (UTC).|string (date-time)|
|**Id**  <br>*opcional*|Identificador del servicio.|cadena|
|**image**  <br>*opcional*||[Imagen](#image)|
|**manifest**  <br>*opcional*||[Manifest](#manifest)|
|**models**  <br>*opcional*|Lista de modelos.|Matriz <[Model](#model)>|
|**name**  <br>*opcional*|Nombre del servicio.|cadena|
|**scoringUri**  <br>*opcional*|URI para puntuar el servicio.|cadena|
|**state**  <br>*opcional*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*opcional*|Hora de la última actualización (UTC).|string (date-time)|
|**appInsightsEnabled**  <br>*opcional*|Habilita Application Insights para un servicio.|boolean|
|**autoScaler**  <br>*opcional*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*requerido*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*opcional*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*opcional*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*opcional*|Número máximo de solicitudes simultáneas.  <br>**Valor mínimo**: `1`|integer|
|**numReplicas**  <br>*opcional*|Número de réplicas de pod que se ejecutan en un momento dado. No se puede especificar si el escalador automático está habilitado.  <br>**Valor mínimo**: `0`|integer|
|**error**  <br>*opcional*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>ServiceUpdateRequest
Una solicitud para actualizar un servicio.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**appInsightsEnabled**  <br>*opcional*|Habilita Application Insights para un servicio.|boolean|
|**autoScaler**  <br>*opcional*||[AutoScaler](#autoscaler)|
|**containerResourceReservation**  <br>*opcional*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*opcional*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*opcional*|Imagen para crear el servicio.|cadena|
|**maxConcurrentRequestsPerContainer**  <br>*opcional*|Número máximo de solicitudes simultáneas.  <br>**Valor mínimo**: `1`|integer|
|**numReplicas**  <br>*opcional*|Número de réplicas de pod que se ejecutan en un momento dado. No se puede especificar si el escalador automático está habilitado.  <br>**Valor mínimo**: `0`|integer|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
El tipo de entorno de ejecución de destino.


|NOMBRE|DESCRIPCIÓN|Esquema|
|---|---|---|
|**properties**  <br>*requerido*||Asignación de <string, string>|
|**runtimeType**  <br>*requerido*|Especifica el entorno de ejecución.|enum (SparkPython, Python)|

