---
title: Usar la API de REST de Azure Blockchain Workbench
description: Escenarios para saber cómo usar la API de REST de Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 63e87c59a2e560b5a78708482c2ed89f5f8fb127
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257910"
---
# <a name="using-the-azure-blockchain-workbench-rest-api"></a>Usar la API de REST de Azure Blockchain Workbench 

La API de REST de Azure Blockchain Workbench proporciona a los desarrolladores y trabajadores de la información una forma de crear integraciones enriquecidas para aplicaciones de cadenas de bloques. Este documento los guía por varios métodos clave de la API de REST de Workbench. Considere un escenario donde un desarrollador quiere crear un cliente de cadena de bloques personalizado, lo que permite a los usuarios que hayan iniciado sesión ver sus aplicaciones de cadena de bloques asignadas e interactuar con ellas. El cliente permite a los usuarios ver las instancias del contrato y tomar medidas en los contratos inteligentes. El cliente usa la API de REST de Workbench en el contexto del usuario que ha iniciado sesión para hacer lo siguiente:

* Enumerar aplicaciones
* Enumerar flujos de trabajo para una aplicación
* Enumerar las instancias de contrato inteligente para un flujo de trabajo
* Enumerar las acciones disponibles para un contrato
* Ejecutar una acción para un contrato

Las aplicaciones de cadena de bloques de ejemplo utilizadas en los escenarios pueden [descargarse de GitHub](https://github.com/Azure-Samples/blockchain). 

## <a name="list-applications"></a>Enumerar aplicaciones

Una vez que un usuario haya iniciado sesión en el cliente de cadena de bloques, la primera tarea consiste en recuperar todas las aplicaciones de Blockchain Workbench para el usuario. En este escenario, el usuario tiene acceso a dos aplicaciones:

1.  [Asset Transfer](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
2.  [Refrigerated Transportation](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

Use la [API Applications GET](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications 
Authorization : Bearer {access token}
```

La respuesta enumera todas las aplicaciones de cadena de bloques a las que un usuario tiene acceso en Blockchain Workbench. Los administradores de Blockchain Workbench obtienen todas las aplicaciones de cadena de bloques, mientras que los administradores que no son de Workbench obtienen todas las cadenas de bloque para las que tienen asociado al menos un rol de aplicación o un rol de instancia de contrato inteligente.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Enumerar flujos de trabajo para una aplicación

Una vez que un usuario selecciona la aplicación de cadena de bloques aplicable, en este caso, **Asset Transfer**, el cliente de la cadena de bloques recupera todos los flujos de trabajo de la aplicación de cadena de bloques específica. Los usuarios luego pueden seleccionar el flujo de trabajo aplicable antes de ver todas las instancias de contrato inteligente del flujo de trabajo. Cada aplicación de cadena de bloques tiene uno o varios flujos de trabajo y cada flujo de trabajo tiene cero o instancias de contrato inteligente. Al compilar aplicaciones cliente de cadena de bloques, es recomendable omitir el flujo de la experiencia de usuario que permite a los usuarios seleccionar el flujo de trabajo adecuado cuando hay un solo flujo de trabajo para la aplicación de cadena de bloques. En este caso, **Asset Transfer** tiene un solo flujo de trabajo, que también se llama **Asset Transfer**.

Use la [API Applications Workflows GET](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

La respuesta enumera todos los flujos de trabajo de la aplicación de cadena de bloques especificada a los que un usuario tiene acceso en Blockchain Workbench. Los administradores de Blockchain Workbench obtienen todos los flujos de trabajo de cadena de bloques, mientras que los administradores que no son de Workbench obtienen todos los flujos de trabajo para los que tienen asociado al menos un rol de aplicación o está asociado a un rol de instancia de contrato inteligente.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Enumerar las instancias de contrato inteligente para un flujo de trabajo

Una vez que un usuario selecciona el flujo de trabajo aplicable, en este caso, **Asset Transfer**, el cliente de la cadena de bloques recuperará todas las instancias de contrato inteligente para el flujo de trabajo especificado. Puede usar esta información para mostrar todas las instancias de contrato inteligente del flujo de trabajo y permitir a los usuarios profundizar en cualquiera de las instancias de contrato inteligente que se muestran. En este ejemplo, considere la posibilidad de que un usuario desee interactuar con una de las instancias de contrato inteligente para tomar medidas.

Use la [API Contracts GET](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

La respuesta enumera todas las instancias de contrato inteligente del flujo de trabajo especificado. Los administradores de Workbench obtienen todas las instancias de contrato inteligente, mientras que los administradores que no son de Workbench obtienen todas las instancias de contrato inteligente para las que tienen asociado al menos un rol de aplicación o está asociado a un rol de instancia de contrato inteligente.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Enumerar las acciones disponibles para un contrato

Una vez que un usuario ha decidido profundizar en uno de los contratos, el cliente de la cadena de bloques luego puede mostrar todas las acciones disponibles para el usuario dado el estado del contrato. En este ejemplo, el usuario examina todas las acciones disponibles para un nuevo contrato inteligente que creó:

* Modify: permite al usuario modificar la descripción y el precio de un recurso.
* Terminate: permite al usuario finalizar el contrato del recurso.

Use la [API Contract Action GET](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionget):

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

La respuesta enumera todas las acciones que un usuario puede realizar dado el estado actual de la instancia de contrato inteligente especificada. Los usuarios obtienen todas las acciones aplicables si el usuario tiene un rol de aplicación asociado o está asociado a un rol de instancia de contrato inteligente para el estado actual de la instancia de contrato inteligente especificada.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Ejecutar una acción para un contrato

Un usuario luego puede decidir tomar medidas para la instancia de contrato inteligente especificada. En este caso, considere el escenario en el que un usuario quiere modificar la descripción y el precio de un recurso a lo siguiente:

* Description: "My updated car"
* Price: 54321

Use la [API Contract Action POST](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionpost):

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

Los usuarios solo pueden ejecutar la acción dada el estado actual de la instancia de contrato inteligente especificada y el rol de aplicación asociada del usuario o el rol de instancia de contrato inteligente. Si la publicación se realiza correctamente, se devuelve una respuesta HTTP 200 OK sin ningún cuerpo de respuesta.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Referencia de la API de REST de Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)