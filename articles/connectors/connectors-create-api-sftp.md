---
title: "Información sobre cómo utilizar el conector de SFTP en Logic Apps | Microsoft Docs"
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Conéctese a la API de SFTP para enviar y recibir archivos. Puede realizar varias operaciones, como crear, actualizar, obtener o eliminar archivos."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 31253d8daee1581167a96a20ba8ad529a04b3e92
ms.contentlocale: es-es
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-sftp-connector"></a>Introducción al conector de SFTP
Use el conector SFTP a fin de acceder a una cuenta SFTP para enviar y recibir archivos. Puede realizar varias operaciones, como crear, actualizar, obtener o eliminar archivos.  

Para poder usar [un conector](apis-list.md), primero debe crear una aplicación lógica. Por tanto, puede comenzar [creando una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-sftp"></a>Conexión a SFTP
Para que la aplicación lógica pueda acceder a un servicio, primero debe crear una *conexión* con dicho servicio. Una [conexión](connectors-overview.md) proporciona conectividad entre una aplicación lógica y otro servicio.  

### <a name="create-a-connection-to-sftp"></a>Creación de una conexión a SFTP
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>Uso de un desencadenador de SFTP
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

En este ejemplo, el desencadenador **SFTP - When a file is added or modified** (SFTP - Cuando se agrega o modifica un archivo) se usa para iniciar un flujo de trabajo de una aplicación lógica cuando un archivo se agrega a un servidor SFTP, o se modifica en él. También se agrega una condición que comprueba el contenido del archivo nuevo o modificado, y toma una decisión para extraer el archivo si su contenido indica que debe extraerse antes de usar el contenido. Por último, se agrega una acción para extraer el contenido de un archivo y colocar el contenido extraído en una carpeta del servidor SFTP. 

En un entorno empresarial, este desencadenador se puede utilizar para supervisar en una carpeta SFTP los nuevos archivos que representan los pedidos de los clientes.  Posteriormente, puede utilizar una acción del conector de SFTP, como **Get file content** (Obtener contenido del archivo), para obtener el contenido del pedido, con el fin de procesarlo y almacenarlo en una base de datos de pedidos.

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Agregar una condición
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>Uso de una acción de SFTP
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/sftpconnector/).

## <a name="more-connectors"></a>Más conectores
Volver a la [lista de API](apis-list.md).
