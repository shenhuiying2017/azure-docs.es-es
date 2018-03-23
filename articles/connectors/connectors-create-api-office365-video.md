---
title: Uso del conector de Office 365 Video en Logic Apps | Microsoft Docs
description: Introducción al uso del conector de Office 365 Video en las aplicaciones lógicas del Servicio de aplicaciones de Microsoft Azure
services: ''
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 738e5aa7-2523-4116-8b65-211b9063852d
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 30ec8c7aa55013668d2ec0897278d435b9e864c7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-office365-video-connector"></a>Introducción al conector de Office 365 Video
Conéctese a Office 365 Video para conseguir información acerca de un vídeo Office 365 específico, obtener una lista de vídeos y mucho más. Con Office 365 Video puede:

* Compilar el flujo de su negocio en función de los datos que obtiene de Office 365 Video. 
* Usar acciones que comprueban el estado del portal de vídeo, obtener una lista de todos los vídeos en un canal y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, puede utilizar el conector de Búsqueda de Bing para buscar vídeos de Office 365 y, después, usar el conector de Office 365 Video para conseguir información sobre ese vídeo. Si el vídeo cumple sus requisitos, puede publicarlo en Facebook. 

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office365-video-connector"></a>Creación de una conexión al conector de Office 365 Video
Al agregar este conector a las aplicaciones lógicas, debe iniciar sesión en su cuenta de Office 365 Video y permitir que estas se conecten a su cuenta.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

Después de crear la conexión, especifique las propiedades del vídeo de Office 365, como el nombre de inquilino o el identificador del canal. 


## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/office365videoconnector/).

## <a name="more-connectors"></a>Más conectores
Volver a la [lista de API](apis-list.md).