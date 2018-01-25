---
title: "Incorporación del conector de Facebook a Logic Apps | Microsoft Docs"
description: "Información general del conector de Facebook con parámetros de la API de REST"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f4d6f0ed-c09b-488c-be1c-8cf2b5b1d4b8
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 27a9297c5d4cfb52f254ceb12e37423403e7f41b
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-facebook-connector"></a>Introducción al conector de Facebook
Conéctese a Facebook y publique en una biografía, obtenga una fuente de página y mucho más. Con Facebook, puede hacer lo siguiente:

* Compilar el flujo de negocio en función de los datos que obtiene de Facebook. 
* Utilizar un desencadenador cuando se reciba un nuevo mensaje.
* Usar acciones para publicar en la biografía, obtener una fuente de página y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando haya un nuevo mensaje en su biografía, puede tomar ese mensaje e insertarlo en su fuente de Twitter. 

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Creación de una conexión a Facebook
Al agregar este conector a las aplicaciones lógicas, debe autorizar a estas para que se conecten a su Facebook.

1. Inicie sesión en su cuenta de Facebook.
2. Seleccione **Authorize**(Autorizar) y permita que sus aplicaciones lógicas se conecten y utilicen su aplicación de Facebook. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 


## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/facebook/).

## <a name="more-connectors"></a>Más conectores
Volver a la [lista de API](apis-list.md).