---
title: Creación de una aplicación de cadena de bloques en Azure Blockchain Workbench
description: Creación de una aplicación de cadena de bloques en Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/9/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 485f761e22871396dace71333868ba7712e41f67
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
---
# <a name="create-a-blockchain-application-in-azure-blockchain-workbench"></a>Creación de una aplicación de cadena de bloques en Azure Blockchain Workbench

Puede usar Azure Blockchain Workbench para crear aplicaciones de cadena de bloques que representen flujos de trabajo de varias partes que se definen mediante configuración y código de contrato inteligente.

Aprenderá a:

> [!div class="checklist"]
> * Configurar una aplicación de cadena de bloques
> * Crear un archivo de código de contrato inteligente
> * Agregar una aplicación de cadena de bloques a Azure Blockchain Workbench
> * Agregar miembros a la aplicación de cadena de bloques

## <a name="prerequisites"></a>requisitos previos

* Una implementación de Blockchain Workbench. Para más información sobre la implementación, consulte [Implementación de Azure Blockchain Workbench](blockchain-workbench-deploy.md).
* Usuarios de Azure Active Directory en el inquilino asociado a Blockchain Workbench. Para más información, consulte [Add Azure AD users in Azure Blockchain Workbench](blockchain-workbench-manage-users.md#add-azure-ad-users) (Incorporación de usuarios de Azure AD en Azure Blockchain Workbench).
* Una cuenta de administrador de Blockchain Workbench. Para más información, consulte [Blockchain Workbench administrators in Azure Blockchain Workbench](blockchain-workbench-manage-users.md#manage-blockchain-workbench-administrators) (Administradores de Azure Blockchain Workbench).

Vamos a compilar una aplicación básica en la que un solicitante envía una solicitud y un respondedor envía una respuesta a esta. Por ejemplo, la solicitud puede ser "Hola, ¿cómo está?", y la respuesta puede ser "Muy bien". La solicitud y la respuesta se registran en la cadena de bloques subyacente. 

Siga los pasos para crear los archivos de la aplicación o [descargue el ejemplo de GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain). 

## <a name="configuration-file"></a>Archivo de configuración

Los metadatos de configuración definen los flujos de trabajo de alto nivel y el modelo de interacción de la aplicación de cadena de bloques. Los metadatos de configuración representan las etapas de los flujos de trabajo y el modelo de interacción de la aplicación de cadena de bloques.

1. En el editor que prefiera, cree un archivo llamado `HelloBlockchain.json`.
2. Agregue el siguiente JSON para definir la configuración de la aplicación de cadena de bloques.

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
          "Name": "HelloBlockchain",
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

3. Guarde el archivo `HelloBlockchain.json`.

El archivo de configuración tiene varias secciones. A continuación se ofrece información detallada sobre cada sección:

### <a name="application-metadata"></a>Metadatos de la aplicación

El principio del archivo de configuración contiene información acerca de la aplicación incluyendo el nombre y la descripción.

### <a name="application-roles"></a>Roles de la aplicación

La sección de roles de aplicación define los roles de usuario que pueden actuar o participar en la aplicación de cadena de bloques. Puede definir un conjunto de roles diferentes según la funcionalidad. En el escenario de solicitud-respuesta, hay una diferencia entre la funcionalidad de un solicitante como entidad que genera solicitudes y la de un respondedor como entidad que genera respuestas.

### <a name="workflows"></a>Flujos de trabajo

Los flujos de trabajo definen una o varias etapas y acciones del contrato. En el escenario de solicitud-respuesta, la primera etapa (estado) del flujo de trabajo es que un solicitante (rol) realiza una acción (transición) para enviar una solicitud (función). La siguiente etapa (estado) es que un respondedor (función) realiza una acción (transición) para enviar una respuesta (función). Un flujo de trabajo de una aplicación puede incluir las propiedades, funciones y estados necesarios para describir el flujo de un contrato. 

Para más información sobre el contenido de los archivos de configuración, consulte [Azure Blockchain Workflow configuration reference](blockchain-workbench-configuration-overview.md) (Referencia sobre la configuración de flujos de trabajo de Azure Blockchain)///.

## <a name="smart-contract-code-file"></a>Archivo de código de contrato inteligente

Los contratos inteligentes representan la lógica de negocios de la aplicación de cadena de bloques. Actualmente, Blockchain Workbench es compatible con Ethereum como libro de contabilidad de la cadena de bloques. Ethereum usa [Solidity](https://solidity.readthedocs.io) como lengua de programación para escribir lógica de negocios de aplicación automática para contratos inteligentes.

Los contratos inteligentes de Solidity son parecidos a las clases de los lenguajes orientados a objetos. Cada contrato contiene el estado y las funciones para implementar las etapas y acciones del contrato inteligente.

En el editor que prefiera, cree un archivo llamado `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Pragma de versión

Se recomienda indicar la versión de destino de Solidity. Especificar la versión ayuda a evitar incompatibilidades con versiones futuras de Solidity. 

Agregue la siguiente pragma de versión en la parte superior del archivo de código de contrato inteligente `HelloBlockchain.sol`.


  ``` solidity
  pragma solidity ^0.4.20;
  ```

### <a name="base-class"></a>Clase base

La clase base **WorkbenchBase** permite a Blockchain Workbench crear y actualizar el contrato. La clase base es necesaria para el código de contrato inteligente específico de Blockchain Workbench. El contrato se debe heredar de la clase base **WorkbenchBase**.

En el archivo de código de contrato inteligente `HelloBlockchain.sol`, agregue la clase **WorkbenchBase** al comienzo del archivo. 

```
contract WorkbenchBase {
    event WorkbenchContractCreated(string applicationName, string workflowName, address originatingAddress);
    event WorkbenchContractUpdated(string applicationName, string workflowName, string action, address originatingAddress);

    string internal ApplicationName;
    string internal WorkflowName;

    function WorkbenchBase(string applicationName, string workflowName) internal {
        ApplicationName = applicationName;
        WorkflowName = workflowName;
    }

    function ContractCreated() internal {
        WorkbenchContractCreated(ApplicationName, WorkflowName, msg.sender);
    }

    function ContractUpdated(string action) internal {
        WorkbenchContractUpdated(ApplicationName, WorkflowName, action, msg.sender);
    }
}
```
La clase base incluye dos funciones importantes:

|Función de clase base  | Propósito  | Cuándo efectúa la llamada  |
|---------|---------|---------|
| ContractCreated() | Notifica a Blockchain Workbench que se ha creado un contrato | Antes de salir del constructor del contrato |
| ContractUpdated() | Notifica a Blockchain Workbench que se ha actualizado un estado de contrato | Antes de salir de una función de contrato |

### <a name="configuration-and-smart-contract-code-relationship"></a>Configuración y relación de código de contrato inteligente

Blockchain Workbench utiliza el archivo de configuración y el archivo de código de contrato inteligente para crear una aplicación de cadena de bloques. Hay una relación entre lo que se define en la configuración y el código del contrato inteligente. Es necesario que coincidan los detalles, funciones, parámetros y tipos del contrato para crear la aplicación. Blockchain Workbench comprueba los archivos antes de la creación de las aplicaciones. 

### <a name="contract"></a>Contrato

Para Blockchain Workbench, los contratos se deben heredar de la clase base **WorkbenchBase**. Cuando se declara el contrato, debe pasar el nombre de la aplicación y el nombre del flujo de trabajo como argumentos.

Agregue el encabezado de **contrato** al archivo de código de contrato inteligente `HelloBlockchain.sol`. 

```
contract HelloBlockchain is WorkbenchBase('HelloBlockchain', 'HelloBlockchain') {
```

El contrato se debe heredar de la clase base **WorkbenchBase** y se deben pasar los parámetros **ApplicationName** y el flujo de trabajo **Name** tal como se define en el archivo de configuración. En este caso, el nombre de la aplicación y el nombre del flujo de trabajo son los mismos.

### <a name="state-variables"></a>Variables de estado

Las variables de estado almacenan los valores de estado de cada instancia del contrato. Las variables de estado del contrato deben coincidir con las propiedades del flujo de trabajo que se definieron en el archivo de configuración.

Agregue las variables de estado al contrato en el archivo de código de contrato inteligente `HelloBlockchain.sol`. 

```
    //Set of States
    enum StateType { Request, Respond}
    
    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;
    
    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>Constructor

El constructor define parámetros de entrada para una nueva instancia de contrato inteligente de un flujo de trabajo. El constructor se declara como una función con el mismo nombre que el contrato. Los parámetros necesarios para el constructor se definen como parámetros del constructor en el archivo de configuración. El número, orden y tipo de parámetros deben coincidir en ambos archivos.

En la función de constructor, escriba cualquier lógica de negocios que desee realizar antes de crear el contrato. Por ejemplo, inicialice las variables de estado con valores iniciales.

Antes de salir de la función de constructor, llame a la función `ContractCreated()`. Esta función notifica a Blockchain Workbench que se ha creado un contrato.

Agregue la función de constructor al contrato en el archivo de código de contrato inteligente `HelloBlockchain.sol`. 

```
    // constructor function
    function HelloBlockchain(string message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    
        // call ContractCreated() to create an instance of this workflow
        ContractCreated();
    }
```

### <a name="functions"></a>Functions

Las funciones son las unidades ejecutables de la lógica de negocios de un contrato. Los parámetros necesarios para la función se definen como parámetros de función en el archivo de configuración. El número, orden y tipo de parámetros deben coincidir en ambos archivos. Las funciones están asociadas a las transiciones en un flujo de trabajo de Blockchain Workbench del archivo de configuración. Una transición es una acción que se lleva a cabo para pasar a la siguiente etapa del flujo de trabajo de una aplicación según lo determinado por el contrato.

Escriba cualquier lógica de negocios que desee realizar en la función. Por ejemplo, modificar el valor de una variable de estado.

Antes de salir de la función, llame a la función `ContractUpdated()`. La función notifica a Blockchain Workbench que se ha actualizado un estado de contrato. Si desea deshacer los cambios de estado realizados en la función, llame a revert(). Revert descarta los cambios de estado realizados desde la última llamada a ContractUpdated().

1. Agregue las siguiente funciones al contrato en el archivo de código de contrato inteligente `HelloBlockchain.sol`. 

    ```
        // call this function to send a request
        function SendRequest(string requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
    
            // call ContractUpdated() to record this action
            ContractUpdated('SendRequest');
        }
    
        // call this function to send a response
        function SendResponse(string responseMessage) public
        {
            Responder = msg.sender;
    
            // call ContractUpdated() to record this action
            ResponseMessage = responseMessage;
            State = StateType.Respond;
            ContractUpdated('SendResponse');
        }
    }
    ```

2. Guarde el archivo de código de contrato inteligente `HelloBlockchain.sol`.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Incorporación de una aplicación de cadena de bloques a Azure Blockchain Workbench

Para agregar una aplicación de cadena de bloques a Blockchain Workbench, cargue los archivos de configuración y del contrato inteligente para definir la aplicación.

1. En un explorador web, vaya a la dirección web de Blockchain Workbench. Por ejemplo, `https://{workbench URL}.azurewebsites.net/`. La aplicación web se crea al implementar Blockchain Workbench. Para más información sobre cómo buscar la dirección web de Blockchain Workbench, consulte [Dirección URL web de Blockchain Workbench](blockchain-workbench-deploy.md#blockchain-workbench-web-url)
2. Inicie sesión como administrador de Blockchain Workbench. Para más información acerca de cómo administrar usuarios, consulte [Administración de usuarios en Azure Blockchain Workbench](blockchain-workbench-manage-users.md).
3. Seleccione **Aplicaciones** > **Nuevo**. Aparece el panel **Nueva aplicación**.
4. Seleccione **Cargar configuración del contrato** > **Examinar** para encontrar el archivo de configuración **HelloBlockchain.json** que creó. El archivo de configuración se valida automáticamente. Seleccione el vínculo **Mostrar** para mostrar los errores de validación. Corrija los errores de validación antes de implementar la aplicación.
5. Seleccione **Cargar código de contrato** > **Examinar** para buscar el archivo de código de contrato inteligente **HelloBlockchain.sol**. El archivo de código se valida automáticamente. Seleccione el vínculo **Mostrar** para mostrar los errores de validación. Corrija los errores de validación antes de implementar la aplicación.
6. Seleccione **Implementar** para crear la aplicación de cadena de bloques basándose en la configuración y los archivos del contrato inteligente.

La implementación de la aplicación de cadena de bloques puede tardar unos minutos. Cuando haya finalizado la implementación, la nueva aplicación aparecerá en **Aplicaciones**. 

> [!NOTE]
> También puede crear aplicaciones de cadena de bloques mediante la [API REST de Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench). 

## <a name="add-blockchain-application-members"></a>Incorporación de miembros a una aplicación de cadena de bloques

Agregue miembros a la aplicación para iniciar y realizar acciones en contratos. Para agregar miembros a la aplicación, debe ser un [administrador de Blockchain Workbench](blockchain-workbench-manage-users.md#manage-blockchain-workbench-administrators).

1. Seleccione **Aplicaciones** > **Hello, Blockchain!**.
2. El número de los miembros asociados a la aplicación se muestra en la esquina superior derecha de la página. En el caso de una aplicación nueva, el número de miembros será cero.
3. Seleccione el vínculo **miembros** de la esquina superior derecha de la página. Aparecerá una lista actual de los miembros de la aplicación.
4. En la lista de miembros, seleccione **Agregar miembros**.
5. Seleccione o escriba el nombre del miembro que desea agregar. Solo se muestran los usuarios de Azure AD que existen en el inquilino de Blockchain Workbench. Si no se encuentra el usuario, deberá [agregar usuarios de Azure AD](blockchain-workbench-manage-users.md#add-azure-ad-users).
6. Seleccione el **rol** del miembro. Para el primer miembro, seleccione el rol **Solicitante**.
7. Seleccione **Agregar** para agregar el miembro con el rol asociado a la aplicación.
8. Agregue otro miembro a la aplicación con el rol **Respondedor**.

Para más información acerca de cómo administrar los usuarios de Blockchain Workbench, consulte el artículo sobre [administración de usuarios en Azure Blockchain Workbench](blockchain-workbench-manage-users.md)

## <a name="next-steps"></a>Pasos siguientes

En este artículo de procedimientos, ha creado una aplicación básica de solicitud y respuesta. Para aprender a usar la aplicación, continúe con el siguiente artículo de procedimientos.

> [!div class="nextstepaction"]
> [Uso de una aplicación de cadena de bloques](blockchain-workbench-use.md)
