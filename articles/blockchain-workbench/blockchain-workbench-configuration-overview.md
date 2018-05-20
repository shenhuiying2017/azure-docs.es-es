---
title: Referencia sobre la configuración de Azure Blockchain Workbench
description: Introducción a la configuración de aplicaciones de Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 3/20/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 6318b6aafda972242239ce91abb0f67a15721e1e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Referencia sobre la configuración de Azure Blockchain Workbench

 Las aplicaciones de Azure Blockchain Workbench son flujos de trabajo de varias partes definidas por metadatos de configuración y código de contrato inteligente. Los metadatos de configuración definen los flujos de trabajo de alto nivel y el modelo de interacción de la aplicación de cadena de bloques. Los contratos inteligentes definen la lógica de negocios de la aplicación de cadena de bloques. Workbench usa la configuración y el código de contrato inteligente para generar experiencias de usuario de aplicaciones de cadena de bloques.

Los metadatos de configuración especifican la siguiente información para cada aplicación de cadena de bloques: 

* Nombre y descripción de la aplicación de cadena de bloques
* Funciones exclusivas de los usuarios que pueden actuar o participar en la aplicación de cadena de bloques
* Uno o varios flujos de trabajo. Cada flujo de trabajo actúa como una máquina de estados que controla el flujo de la lógica de negocios. Los flujos de trabajo pueden ser independiente o interactuar entre sí.

Cada flujo de trabajo definido especifica lo siguiente:

* Nombre y descripción del flujo de trabajo
* Estados del flujo de trabajo.  Cada estado es una etapa del flujo de control de la lógica de negocios. 
* Las acciones para efectuar la transición al estado siguiente
* Los roles de usuario permitidos para iniciar cada acción
* Los contratos inteligentes que representan la lógica de negocios en archivos de código

## <a name="application"></a>Application

Una aplicación de cadena de bloques contiene metadatos de configuración, flujos de trabajo y roles de los usuarios que pueden actuar o participar en la aplicación.

| Campo | DESCRIPCIÓN | Obligatorio |
|-------|-------------|:--------:|
| ApplicationName | Nombre único de aplicación. El contrato inteligente correspondiente debe usar el mismo **ApplicationName** para la clase de contrato aplicable.  | Sí |
| DisplayName | Nombre descriptivo para mostrar de la aplicación. | Sí |
| DESCRIPCIÓN | Descripción de la aplicación. | Sin  |
| ApplicationRoles | Colección de [ApplicationRoles](#application-roles). Roles de los usuarios que pueden actuar o participar en la aplicación.  | Sí |
| Flujos de trabajo | Colección de [flujos de trabajo](#workflows). Cada flujo de trabajo actúa como una máquina de estados que controla el flujo de la lógica de negocios. | Sí |

Para obtener un ejemplo, consulte [ejemplo de archivo de configuración](#configuration-file-example).

## <a name="workflows"></a>Flujos de trabajo

Se puede modelar la lógica de negocios de una aplicación como una máquina de estados en la que la realización de una acción provoca que el flujo de la lógica de negocios pase de un estado a otro. Un flujo de trabajo es una colección de estos estados y acciones. Cada flujo de trabajo consta de uno o varios contratos inteligentes que representan la lógica de negocios en archivos de código. Un contrato ejecutable es una instancia de un flujo de trabajo.

| Campo | DESCRIPCIÓN | Obligatorio |
|-------|-------------|:--------:|
| NOMBRE | Nombre único de flujo de trabajo. El contrato inteligente correspondiente debe usar el mismo **nombre** para la clase de contrato aplicable. | Sí |
| DisplayName | Nombre descriptivo para mostrar del flujo de trabajo. | Sí |
| DESCRIPCIÓN | Descripción del flujo de trabajo. | Sin  |
| Iniciadores | Colección de [ApplicationRoles](#application-roles). Roles que se asignan a los usuarios que están autorizados para crear contratos en el flujo de trabajo. | Sí |
| StartState | Nombre del estado inicial del flujo de trabajo. | Sí |
| Properties (Propiedades) | Colección de [identificadores](#identifiers). Representa los datos que se pueden leer fuera de la cadena o visualizar en una herramienta de experiencia del usuario. | Sí |
| Constructor | Define los parámetros de entrada para la creación de una instancia del flujo de trabajo. | Sí |
| Functions | Una colección de [funciones](#functions) que se pueden ejecutar en el flujo de trabajo. | Sí |
| States | Una colección de [estados](#states) del flujo de trabajo. | Sí |

Para obtener un ejemplo, consulte [ejemplo de archivo de configuración](#configuration-file-example).

## <a name="type"></a>Escriba

Tipos de datos admitidos.

| Escriba | DESCRIPCIÓN |
|-------|-------------|
| address  | Tipo de dirección de Blockchain, como *contratos* o *usuarios* |
| booleano     | Tipo de datos booleano |
| contrato | Dirección del contrato de tipo |
| int      | Tipo de datos Integer |
| money    | Tipo de datos Money |
| state    | Estado de flujo de trabajo |
| string   | Tipo de datos String |
| user     | Dirección del usuario de tipo |
| Twitter en tiempo     | Tipo de datos Time |
|`[ Application Role Name ]`| Cualquier nombre especificado en un rol de aplicación. Se limita solo a los usuarios de ese tipo de rol. |

## <a name="constructor"></a>Constructor

Define los parámetros de entrada de una instancia de un flujo de trabajo.

| Campo | DESCRIPCIÓN | Obligatorio |
|-------|-------------|:--------:|
| Parámetros | Colección de [identificadores](#identifiers) necesarios para iniciar un contrato inteligente. | Sí |

### <a name="constructor-example"></a>Ejemplo de constructor

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}

```

## <a name="functions"></a>Functions

Define las funciones que se pueden ejecutar en el flujo de trabajo.

| Campo | DESCRIPCIÓN | Obligatorio |
|-------|-------------|:--------:|
| NOMBRE | El nombre único de la función. El contrato inteligente correspondiente debe usar el mismo **nombre** para la función aplicable. | Sí |
| DisplayName | Nombre descriptivo para mostrar de la función. | Sí |
| DESCRIPCIÓN | Descripción de la función | Sin  |
| Parámetros | Colección de [identificadores](#identifiers) que se corresponden con los parámetros de la función. | Sí |

### <a name="functions-example"></a>Ejemplo de funciones

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>States

Una colección de estados únicos dentro de un flujo de trabajo. Cada estado captura un paso del flujo de control de la lógica de negocios. 

| Campo | DESCRIPCIÓN | Obligatorio |
|-------|-------------|:--------:|
| NOMBRE | Nombre único del estado. El contrato inteligente correspondiente debe usar el mismo **nombre** para el estado aplicable. | Sí |
| DisplayName | Nombre descriptivo para mostrar del estado. | Sí |
| DESCRIPCIÓN | Descripción del estado. | Sin  |
| PercentComplete | Un valor entero que se muestra en la interfaz de usuario de Blockchain Workbench para mostrar el progreso en el flujo de control de la lógica de negocios. | Sí |
| Estilo | Sugerencia visual que indica si el estado representa un estado correcto o erróneo. Hay dos valores válidos: `Success` o `Failure`. | Sí |
| Transiciones | Colección de las [transiciones](#transitions) disponibles desde el estado actual hasta el siguiente conjunto de estados. | Sin  |

### <a name="states-example"></a>Ejemplo de estados

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been cancelled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Transiciones

Las acciones disponibles hasta el siguiente estado. Uno o varios roles de usuario pueden realizar una acción en cada estado donde, a su vez, una acción puede realizar la transición de un estado a otro en el flujo de trabajo. 

| Campo | DESCRIPCIÓN | Obligatorio |
|-------|-------------|:--------:|
| AllowedRoles | Lista de roles de aplicación a los que se permite iniciar la transición. Todos los usuarios del rol especificado pueden llevar a cabo la acción. | Sin  |
| AllowedInstanceRoles | Lista de roles de usuario que participan o de los cuales se especifica en el contrato inteligente que tienen permiso para iniciar la transición. Los roles de instancia se definen en **Propiedades** en los flujos de trabajo. Estos usuarios representan a un usuario que participa, o que está especificado en el contrato inteligente, en oposición a todos los usuarios de un tipo de rol. | Sin  |
| DisplayName | Nombre descriptivo para mostrar de la transición. | Sí |
| DESCRIPCIÓN | Descripción de la transición. | Sin  |
| Función | El nombre de la función que va a iniciar la transición. | Sí |
| NextStates | Una colección de los posibles estados siguientes después de una transición correcta. | Sí |

### <a name="transitions-example"></a>Ejemplo de transiciones

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Roles de la aplicación

Los roles de aplicación definen un conjunto de roles que se pueden asignar a los usuarios que deseen actuar o participar en la aplicación. Estos roles se pueden usar para restringir las acciones y la participación en la aplicación de cadena de bloques y en los flujos de trabajo correspondientes. 

| Campo | DESCRIPCIÓN | Obligatorio |
|-------|-------------|:--------:|
| NOMBRE | El nombre único del rol de aplicación. El contrato inteligente correspondiente debe usar el mismo **nombre** para el rol aplicable. Se reservan los nombres de tipo base. No puede asignar un nombre a un rol de aplicación con el mismo nombre como [Tipo](#type)| Sí |
| DESCRIPCIÓN | Descripción del rol de aplicación. | Sin  |

### <a name="application-roles-example"></a>Ejemplo de roles de aplicación

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>Identifiers (Identificadores)

Los identificadores representan una colección de información que se usa para describir las propiedades, el constructor y los parámetros de función del flujo de trabajo. 

| Campo | DESCRIPCIÓN | Obligatorio |
|-------|-------------|:--------:|
| NOMBRE | El nombre único de la propiedad o parámetro. El contrato inteligente correspondiente debe usar el mismo **nombre** para la propiedad o parámetro aplicables. | Sí |
| DisplayName | Nombre descriptivo para mostrar de la propiedad o parámetro. | Sí |
| DESCRIPCIÓN | Descripción de la propiedad o parámetro. | Sin  |

### <a name="identifiers-example"></a>Ejemplo de identificadores

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>Ejemplo de archivo de configuración

En el ejemplo siguiente se define una aplicación de solicitud-respuesta básica en la que un solicitante envía una solicitud y un respondedor envía una respuesta a esta.

``` json
{
  "ApplicationName": "HelloBlockchain",
  "DisplayName": "Hello, Blockchain!",
  "Description": "A simple application to send request and get response",
  "ApplicationRoles": [
    {
      "Name": "Requestor",
      "Description": "A person sending a request."
    },
    {
      "Name": "Responder",
      "Description": "A person responding to a request"
    }
  ],
  "Workflows": [
    {
      "Name": "RequestResponse",
      "DisplayName": "Request Response",
      "Description": "A simple workflow to send a request and receive a response.",
      "Initiators": [ "Requestor" ],
      "StartState": "Request",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract.",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Requestor",
          "DisplayName": "Requestor",
          "Description": "A person sending a request.",
          "Type": {
            "Name": "Requestor"
          }
        },
        {
          "Name": "Responder",
          "DisplayName": "Responder",
          "Description": "A person sending a response.",
          "Type": {
            "Name": "Responder"
          }
        },
        {
          "Name": "RequestMessage",
          "DisplayName": "Request Message",
          "Description": "A request message.",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "ResponseMessage",
          "DisplayName": "Response Message",
          "Description": "A response message.",
          "Type": {
            "Name": "string"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "message",
            "Description": "...",
            "DisplayName": "Request Message",
            "Type": {
              "Name": "string"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "SendRequest",
          "DisplayName": "Request",
          "Description": "...",
          "Parameters": [
            {
              "Name": "requestMessage",
              "Description": "...",
              "DisplayName": "Request Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        },
        {
          "Name": "SendResponse",
          "DisplayName": "Response",
          "Description": "...",
          "Parameters": [
            {
              "Name": "responseMessage",
              "Description": "...",
              "DisplayName": "Response Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        }
      ],
      "States": [
        {
          "Name": "Request",
          "DisplayName": "Request",
          "Description": "...",
          "PercentComplete": 50,
          "Value": 0,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": ["Responder"],
              "AllowedInstanceRoles": [],
              "Description": "...",
              "Function": "SendResponse",
              "NextStates": [ "Respond" ],
              "DisplayName": "Send Response"
            }
          ]
        },
        {
          "Name": "Respond",
          "DisplayName": "Respond",
          "Description": "...",
          "PercentComplete": 90,
          "Value": 1,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": ["Requestor"],
              "Description": "...",
              "Function": "SendRequest",
              "NextStates": [ "Request" ],
              "DisplayName": "Send Request"
            }
          ]
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Implementación de Azure Blockchain Workbench](blockchain-workbench-deploy.md)

