---
title: Introducción a los mensajes de Azure Blockchain Workbench
description: Introducción al uso de mensajes en Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 9fa0d74b2c07d3e460abc54ea6ef9733f07a64d1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="azure-blockchain-workbench-messages-overview"></a>Introducción a los mensajes de Azure Blockchain Workbench

Además de proporcionar una API REST, Azure Blockchain Workbench proporciona integración basada en mensajería. Workbench publica eventos centrados en el libro de contabilidad a través de Azure Event Grid, lo que permite a los consumidores de nivel final ingerir datos o realizar acciones en función de estos eventos. Para aquellos clientes que precisan de mensajería de confianza, Azure Blockchain Workbench también entrega mensajes a un punto de conexión de Azure Service Bus.

Los desarrolladores también han manifestado interés en la posibilidad de que sistemas externos puedan comunicarse e iniciar transacciones para crear usuarios, crear contratos y actualizar contratos en un libro de contabilidad. Aunque esta funcionalidad no se expone actualmente en la versión preliminar pública, puede encontrar un ejemplo que desarrolla esta funcionalidad en [http://aka.ms/blockchain-workbench-integration-sample](http://aka.ms/blockchain-workbench-integration-sample).


## <a name="event-notifications"></a>Notificaciones de eventos

Las notificaciones de eventos se pueden usar para notificar a los usuarios y a los sistemas de nivel final de eventos que se producen en Workbench y la red a la que está conectada la cadena de bloques. Las notificaciones de eventos se pueden consumir directamente en código o se pueden utilizar con herramientas como Logic Apps y Flow para desencadenar el flujo de datos hacia los sistemas de nivel final.

Consulte la [Referencia de mensajes de notificación](#notification-message-reference) para más detalles sobre los distintos mensajes que se pueden recibir.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Consumo de eventos de Event Grid con Azure Functions

Si un usuario desea usar Event Grid para recibir notificaciones sobre eventos que se producen en Blockchain Workbench, puede consumir eventos de Event Grid mediante el uso de Azure Functions.

1. Cree una **aplicación de función de Azure** en Azure Portal.
2. Cree una nueva función.
3. Busque la plantilla de Event Grid. Se muestra el código de la plantilla básica para leer el mensaje. Modifique el código según sea necesario.
4. Guarde la función. 
5. Seleccione la instancia de Event Grid desde el grupo de recursos de Blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Consumo de eventos de Event Grid con Logic Apps

1.  Cree una nueva **aplicación lógica de Azure** en Azure Portal.
2.  Al abrir la aplicación de la lógica de Azure en el portal, se le pedirá seleccionar un desencadenador. Seleccione **Azure Event Grid: cuando se produce un evento de recurso**.
3. Cuando se muestra el diseñador de flujo de trabajo, se le pedirá que inicie sesión.
4. Seleccione la suscripción. Recurso como **Microsoft.EventGrid.Topics**. Seleccione el **Nombre de recurso** desde el nombre del recurso del grupo de recursos de Azure Blockchain Workbench.
5. Seleccione la instancia de Event Grid desde el grupo de recursos de Blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Uso de temas de Service Bus para notificaciones

Los temas de Service Bus se pueden utilizar para notificar a los usuarios acerca de los eventos que se producen en Blockchain Workbench. 

1.  Vaya a Service Bus en el grupo de recursos de Workbench.
2.  Seleccione **Temas**.
3.  Seleccione **workbench-external**.
4.  Cree una nueva suscripción a este tema. Obtenga una clave para la suscripción.
5.  Cree un programa, que se suscribe a los eventos de esta suscripción.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Consumo de mensajes de Service Bus con Logic Apps

1. Cree una nueva **aplicación lógica de Azure** en Azure Portal.
2.  Al abrir la aplicación de la lógica de Azure en el portal, se le pedirá seleccionar un desencadenador. Escriba **Service Bus** en el cuadro de búsqueda y seleccione el desencadenador adecuado para el tipo de interacción que desea tener con Service Bus. Por ejemplo, **Service Bus: cuando se recibe un mensaje en una suscripción de tema (autocompletar)**.
3. Cuando se muestra el diseñador de flujo de trabajo, especifique la información de conexión para Service Bus.
4. Seleccione la suscripción y especifique el tema **workbench-external**.
5. Desarrolle la lógica de la aplicación que utiliza el mensaje de este desencadenador.

## <a name="notification-message-reference"></a>Referencia de mensajes de notificación

En función de OperationName (nombre de la operación), los mensajes de notificación tienen uno de los siguientes tipos de mensaje.

### <a name="accountcreated"></a>AccountCreated

Indica que se ha solicitado agregar una nueva cuenta a la cadena especificada.

| NOMBRE    | DESCRIPCIÓN  |
|----------|--------------|
| UserId  | Identificador del usuario que se ha creado |
| ChainIdentifier | Dirección del usuario que se ha creado en la red de la cadena de bloques. En Ethereum, esto sería la dirección del usuario "en la cadena". |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

Indica que se ha realizado una solicitud para insertar o actualizar un contrato en un libro de contabilidad distribuida.

| NOMBRE | DESCRIPCIÓN |
|-----|--------------|
| ChainID | Identificador único de la cadena asociada a la solicitud.|
  BlockId | Identificador único de un bloque en el libro de contabilidad.|
  ContractId | Identificador único del contrato.|
  ContractAddress |       Dirección del contrato en el libro de contabilidad.|
  TransactionHash  |     Hash de la transacción en el libro de contabilidad.|
  OriginatingAddress |   Dirección del remitente de la transacción.|
  ActionName       |     Nombre de la acción.|
  IsUpdate        |      Identifica si se trata de una actualización.|
  Parámetros       |     Una lista de objetos que identifican el nombre, valor y tipo de datos de los parámetros que se envían a una acción.|
  TopLevelInputParams |  En escenarios en los que un contrato está conectado a uno o más contratos, estos son los parámetros del contrato de nivel superior. |

``` csharp
public class ContractInsertOrUpdateRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public int ContractId { get; set; }
    public string ContractAddress { get; set; }
    public string TransactionHash { get; set; }
    public string OriginatingAddress { get; set; }
    public string ActionName { get; set; }
    public bool IsUpdate { get; set; }
    public List<ContractProperty> Parameters { get; set; }
    public bool IsTopLevelUpdate { get; set; }
    public List<ContractInputParameter> TopLevelInputParams { get; set; }
}
```

#### <a name="updatecontractaction"></a>UpdateContractAction

Indica que se ha realizado una solicitud para ejecutar una acción en un contrato específico en un libro de contabilidad distribuida.

| NOMBRE                     | DESCRIPCIÓN                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | Identificador único de esta acción del contrato.                                                                                                                                |
| ChainIdentifier          | Identificador único de la cadena.                                                                                                                                           |
| ConnectionId             | Identificador único de la conexión.                                                                                                                                      |
| UserChainIdentifier      | Dirección del usuario que se ha creado en la red de la cadena de bloques. En Ethereum, esto sería la dirección del usuario "en la cadena".                                                     |
| ContractLedgerIdentifier | Dirección del contrato en el libro de contabilidad.                                                                                                                                        |
| WorkflowFunctionName     | Nombre de la función de flujo de trabajo.                                                                                                                                                |
| WorkflowName             | Nombre del flujo de trabajo.                                                                                                                                                         |
| WorkflowBlobStorageURL   | Dirección URL del contrato en el almacenamiento de blobs.                                                                                                                                      |
| ContractActionParameters | Parámetros de la acción de contrato.                                                                                                                                           |
| TransactionHash          | Hash de la transacción en el libro de contabilidad.                                                                                                                                    |
| Estado de aprovisionamiento      | Estado de aprovisionamiento actual de la acción.</br>0: Creada</br>1: En proceso</br>2: Completa</br> Completa indica una confirmación desde el libro de contabilidad de que se agregó correctamente.                                               |
|                          |                                                                                                                                                                               |

```csharp
public class ContractActionRequest : MessageModelBase
{
    public int ContractActionId { get; set; }
    public int ConnectionId { get; set; }
    public string UserChainIdentifier { get; set; }
    public string ContractLedgerIdentifier { get; set; }
    public string WorkflowFunctionName { get; set; }
    public string WorkflowName { get; set; }
    public string WorkflowBlobStorageURL { get; set; }
    public IEnumerable<ContractActionParameter> ContractActionParameters { get; set; }
    public string TransactionHash { get; set; }
    public int ProvisioningStatus { get; set; }
}
```

### <a name="updateuserbalance"></a>UpdateUserBalance

Indica que se ha realizado una solicitud para actualizar el saldo del usuario en un libro de contabilidad distribuida específico.

> [!NOTE]
> Este mensaje se genera solo para los libros que requieren fondos de cuentas.
> 

| NOMBRE    | DESCRIPCIÓN                              |
|---------|------------------------------------------|
| Dirección | Dirección del usuario que recibe los fondos. |
| Saldo | Saldo del usuario.         |
| ChainID | Identificador único de la cadena.     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

El mensaje indica que se ha realizado una solicitud para agregar un bloque en un libro de contabilidad distribuida.

| NOMBRE           | DESCRIPCIÓN                                                            |
|----------------|------------------------------------------------------------------------|
| ChainId        | Identificador único de la cadena a la que se agregó el bloque.             |
| BlockId        | Identificador único del bloque en Azure Blockchain Workbench. |
| BlockHash      | Hash del bloque.                                                 |
| BlockTimeStamp | Marca de tiempo del bloque.                                            |

``` csharp
public class InsertBlockRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string BlockHash { get; set; }
    public int BlockTimestamp { get; set; }
}
```

### <a name="inserttransaction"></a>InsertTransaction

El mensaje proporciona detalles sobre una solicitud para agregar una transacción en un libro de contabilidad distribuida.

| NOMBRE            | DESCRIPCIÓN                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainId         | Identificador único de la cadena a la que se agregó el bloque.             |
| BlockId         | Identificador único del bloque en Azure Blockchain Workbench. |
| TransactionHash | Hash de la transacción.                                           |
| De            | Dirección del remitente de la transacción.                      |
| Para              | Dirección del destinatario pretendido de la transacción.              |
| Valor           | Valor incluido en la transacción.                                 |
| IsAppBuilderTx  | Identifica si se trata de una transacción de Blockchain Workbench.                         |

``` csharp
public class InsertTransactionRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string TransactionHash { get; set; }
    public string From { get; set; }
    public string To { get; set; }
    public decimal Value { get; set; }
    public bool IsAppBuilderTx { get; set; }
}
```

### <a name="assigncontractchainidentifier"></a>AssignContractChainIdentifier

Proporciona detalles sobre la asignación de un identificador de cadena para un contrato. Por ejemplo, en la cadena de bloques Ethereum, la dirección de un contrato en el libro de contabilidad.

| NOMBRE            | DESCRIPCIÓN                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Identificador único del contrato en Azure Blockchain Workbench. |
| ChainIdentifier | Este es el identificador del contrato en la cadena.                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="next-steps"></a>Pasos siguientes

* [Arquitectura de Azure Blockchain Workbench](blockchain-workbench-architecture.md)