---
title: "Análisis de patrones de uso de la red CDN de Azure | Microsoft Docs"
description: "Los clientes pueden habilitar el análisis de registros para la red CDN de Azure."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: v-semcev
ms.openlocfilehash: af396e9f8847421d529c32956216cfc47294edb2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Análisis de patrones de uso de la red CDN de Azure

Después de habilitar CDN para la aplicación, puede supervisar el uso de la red CDN, comprobar el mantenimiento de su entrega y solucionar posibles problemas. Azure CDN proporciona estas funcionalidades de las siguientes dos maneras: 

## <a name="verizon-core-reports"></a>Informes principales de Verizon

Como usuario de Azure CDN con un perfil estándar o premium de Verizon, puede ver los informes principales de Verizon en el portal complementario de Verizon. El portal complementario de Verizon ofrece una variedad de gráficos y vistas y se puede acceder a él con la opción **Administrar** de Azure Portal. Para más información, vea [Informes principales de Verizon](cdn-analyze-usage-patterns.md).

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Análisis básicos a través de los registros de diagnóstico de Azure

Los análisis básicos están disponibles para todos los puntos de conexión de red CDN que pertenecen a Verizon (estándar y premium) y perfiles de red CDN de Akamai (estándar). Los registros de diagnóstico de Azure permite exportar los análisis básicos a Azure Storage, Event Hubs o Log Analytics de Operations Management Suite (OMS). Log Analytics de OMS ofrece una solución con gráficos que el usuario puede configurar y personalizar. Para más información, vea [Azure diagnostic logs](cdn-azure-diagnostic-logs.md) (Registros de diagnóstico de Azure).

