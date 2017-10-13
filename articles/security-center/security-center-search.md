---
title: "Búsqueda de registros en Azure Security Center | Microsoft Docs"
description: "Obtenga información acerca de cómo Azure Security Center usa la búsqueda de registros de Log Analytics para recuperar y analizar los datos de seguridad."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 513c98237a322dabd6b2bf13443e8998ca843b1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-search"></a>Búsqueda de registros en Azure Security Center
Azure Security Center usa la [búsqueda de registros de Log Analytics](../log-analytics/log-analytics-log-searches.md) para recuperar y analizar los datos de seguridad. Log Analytics incluye un lenguaje de consulta para recuperar y consolidar rápidamente los datos. En Security Center, puede aprovechar la búsqueda de registros de Log Analytics para la construcción de consultas y el análisis de los datos recopilados.

Las búsquedas están disponibles en el nivel Gratis y en el nivel Estándar de Security Center.  Los datos disponibles en las búsquedas de registros dependen del nivel que se aplica al área de trabajo.  Para más información, consulte la [página de precios](../security-center/security-center-pricing.md) de Security Center.


> [!NOTE]
> Security Center no guarda los datos de seguridad de un área de trabajo en el nivel Gratis. Puede enviar diversos registros a un área de trabajo en el nivel Gratis y realizar búsquedas en esos datos, pero los resultados de la búsqueda no incluyen datos de Security Center. Security Center solo guarda los datos en un área de trabajo en el nivel Estándar.
>
>

## <a name="access-search"></a>Acceso a las búsquedas
1. En el menú principal de Security Center, seleccione **Buscar**.

  ![Seleccionar Búsqueda de registros][1]

2. Security Center enumera todas las áreas de trabajo de sus suscripciones de Azure. Seleccione un área de trabajo. (Si solo tiene un área de trabajo, este selector de área de trabajo no aparecerá).

  ![Seleccionar un área de trabajo][2]

3. Se abre la opción **Búsqueda de registros**. Para consultar más datos en el área de trabajo seleccionada, escriba esta consulta de ejemplo:

  SecurityEvent | where EventID == 4625 | summarize count() by TargetAccount

  Los resultados muestran todas las cuentas en las que no se pudo iniciar sesión (evento 4625).

  ![Search Results][3]

Consulte el [lenguaje de consulta de Log Analytics](../log-analytics/log-analytics-search-reference.md) para más información sobre cómo consultar los datos del área de trabajo seleccionada.

## <a name="next-steps"></a>Pasos siguientes
En este artículo ha descubierto cómo acceder a las búsquedas de registros en Security Center. Security Center utiliza la búsqueda de registros de Log Analytics. Para más información sobre la búsqueda de registros de Log Analytics, consulte:

- [¿Qué es Log Analytics?](../log-analytics/log-analytics-overview.md) - Introducción a Log Analytics
- [Descripción de las búsquedas de registros en Log Analytics:](../log-analytics/log-analytics-log-search-new.md) en este artículo se describe cómo se usan las búsquedas de registros en Log Analytics y proporciona los conceptos que debe comprender antes de crear una.
- [Búsqueda de datos mediante búsquedas de registros en Log Analytics](../log-analytics/log-analytics-log-searches.md): tutorial sobre cómo usar las búsquedas de registros
- [Referencia sobre la búsqueda de registros de Log Analytics](../log-analytics/log-analytics-search-reference.md): describe el lenguaje de consultas de Log Analytics

Para más información sobre Security Center, consulte:

- [Introducción a Security Center](security-center-intro.md): aquí se describen las principales funciones de Security Center.

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
