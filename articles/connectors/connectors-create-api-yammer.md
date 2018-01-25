---
title: "Incorporación del conector de Yammer a Azure Logic Apps | Microsoft Docs"
description: "Información general del conector de Yammer con parámetros de la API de REST"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 37f72d829fc50a0f967f08e068c553f5026f35eb
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-yammer-connector"></a>Introducción al conector de Yammer
Conectarse a Yammer para acceder a conversaciones en la red empresarial. Con Yammer, puede:

* Compilar el flujo de negocio en función de los datos que obtiene de Yammer. 
* Usar desencadenadores cuando haya un nuevo mensaje en un grupo o en una fuente que le sigue.
* Usar acciones para publicar un mensaje, obtener todos los mensajes y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando aparece un nuevo mensaje, puede enviar un correo electrónico mediante Office 365.

Empiece por crear una aplicación lógica ahora. Para ello, consulte [Creación de una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-yammer"></a>Creación de una conexión a Yammer
Para usar el conector de Yammer, cree primero una **conexión** y, después, especifique los detalles de las siguientes propiedades: 

| Propiedad | Obligatorio | DESCRIPCIÓN |
| --- | --- | --- |
| Se necesita el cifrado de tokens |Sí |Proporcionar credenciales de Yammer |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 

## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/yammer/).

## <a name="more-connectors"></a>Más conectores
Volver a la [lista de API](apis-list.md).