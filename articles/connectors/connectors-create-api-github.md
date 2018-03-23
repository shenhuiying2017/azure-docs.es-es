---
title: Conexión a GitHub con Azure Logic Apps | Microsoft Docs
description: Automatice los flujos de trabajo para GitHub con Azure Logic Apps.
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/02/2018
ms.author: estfan; ladocs
ms.openlocfilehash: 830c3e6b5462c5d2d917388c0d0924e444342f93
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="connect-to-github"></a>Conexión a GitHub

GitHub es un servicio de almacenaje de repositorios Git basado en web. Ofrece toda la funcionalidad de control de revisión distribuido y de administración de código fuente (SCM) en Git, además de otras características.

Para empezar a trabajar con el conector de GitHub, [cree primero una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Creación de una conexión a GitHub

Para usar el conector de GitHub en una aplicación de lógica, debe crear primero una *conexión* y luego especificar los detalles de estas propiedades: 

| Propiedad | Obligatorio | DESCRIPCIÓN | 
| -------- | -------- | ----------- | 
| Se necesita el cifrado de tokens | Sí | Proporcione las credenciales de GitHub. |

Después de crear la conexión, puede ejecutar las acciones y escuchar los desencadenadores descritos en este artículo.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Detalles específicos del conector

Para los desencadenadores y las acciones definidos en Swagger y los posibles límites, revise los [detalles del conector](/connectors/github/).

## <a name="find-more-connectors"></a>Búsqueda de más conectores

* Revise la [lista de conectores](apis-list.md).