---
title: Conector de GitHub en Azure Logic Apps | Microsoft Docs
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. GitHub es un servicio de hospedaje del repositorio Git basado en la Web. Ofrece toda la funcionalidad de administración de código fuente (SCM) y control de revisiones distribuidas de Git, además de agregar sus propias características."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: d4614b0ceff0ec0d36dbb1a136551f985f2fc1a1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-github-connector"></a>Introducción al conector GitHub
GitHub es un servicio de hospedaje del repositorio Git basado en la Web. Ofrece toda la funcionalidad de administración de código fuente (SCM) y control de revisiones distribuidas de Git, además de agregar sus propias características.

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-github"></a>Creación de una conexión a GitHub
Para crear Logic Apps con GitHub, primero necesita crear una **conexión** y, después, especificar los detalles de las siguientes propiedades: 

| Propiedad | Obligatorio | Descripción |
| --- | --- | --- |
| Se necesita el cifrado de tokens |Sí |Proporcionar las credenciales de GitHub |

Después de crear la conexión, puede usarla para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo. 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/github/).

## <a name="more-connectors"></a>Más conectores
Volver a la [lista de API](apis-list.md).