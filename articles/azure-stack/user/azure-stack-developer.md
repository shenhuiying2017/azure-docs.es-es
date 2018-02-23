---
title: Desarrollo de aplicaciones para Azure Stack | Microsoft Docs
description: "Obtenga información acerca de las consideraciones a la hora de desarrollar prototipos de aplicaciones en Azure Stack"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 4b9e0f5a264296e59579539dd929f5d75a54b62a
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2018
---
# <a name="develop-for-azure-stack"></a>Desarrollo para Azure Stack
Puede empezar a desarrollar aplicaciones hoy mismo, incluso si no tiene acceso a un entorno de Azure Stack. Dado que Azure Stack proporciona servicios de Microsoft Azure que se ejecutan en el centro de datos, puede usar herramientas y procesos similares para desarrollar con Azure Stack tal como lo haría con Azure.  Con un poco de preparación y la orientación que le proporcionarán los siguientes temas, puede usar Azure para emular un entorno de Azure Stack:

* En Azure, puede crear plantillas de Azure Resource Manager que también se pueden implementar en Azure Stack.  Consulte el artículo [template considerations](azure-stack-develop-templates.md) (consideraciones de las plantillas) para obtener ayuda acerca de cómo desarrollar las plantillas para garantizar la portabilidad.
* Existe una diferencia entre la disponibilidad y la versión del servicio entre Azure y Azure Stack. Puede usar el [módulo de directivas de Azure Stack](azure-stack-policy-module.md) para restringir los tipos de recursos y la disponibilidad del servicio de Azure a fin de adaptarlos a la disponibilidad de Azure Stack. La restricción de los servicios disponibles permitirá que su aplicación se base en los servicios disponibles de Azure Stack.
* Las [plantillas de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) son ejemplos comunes de escenarios acerca de cómo desarrollar las plantillas para que se puedan implementar en Azure y en Azure Stack.


