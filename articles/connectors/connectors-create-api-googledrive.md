---
title: "Incorporación del conector de Google Drive a Logic Apps | Microsoft Docs"
description: "Información general del conector de Google Drive con parámetros de la API de REST"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2bcebc5-02d2-435b-b0da-ef53bc51c4b6
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia; ladocs
ms.openlocfilehash: c066a10b33e172eb5f16eede43ec407794000c90
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-google-drive-connector"></a>Introducción al conector de Google Drive
Conéctese a Google Drive para crear archivos, obtener filas, etc. Con Google Drive, puede: 

* Compilar el flujo de negocio en función de los datos obtenidos de la búsqueda. 
* Usar acciones para buscar imágenes, noticias, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, puede buscar un vídeo y luego usar Twitter para publicar ese vídeo en una fuente de Twitter.

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-the-connection-to-google-drive"></a>Creación de la conexión a Google Drive
Al agregar este conector a las aplicaciones lógicas, debe autorizar a estas para que se conecten a su Google Drive.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

Después de crear la conexión, especifique las propiedades de Google Drive, como la ruta de acceso a la carpeta o el nombre de archivo. 

## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/googledrive/).

## <a name="more-connectors"></a>Más conectores
Volver a la [lista de API](apis-list.md).
