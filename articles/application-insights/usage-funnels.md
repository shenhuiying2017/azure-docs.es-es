---
title: Embudos de Azure Application Insights
description: "Obtenga información sobre cómo usar Funnels para descubrir la forma en que los clientes interactúan con la aplicación."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: mbullwin
ms.openlocfilehash: 0396c59d9d95ab71f0af04029d87afbb6e47dc35
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2017
---
# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Descubra cómo los clientes usan la aplicación con los embudos de Application Insights.

Conocer la experiencia de los clientes es de suma importancia para su negocio. Si la aplicación implica varias fases, debe saber si la mayoría de los clientes avanzan a través de todo el proceso, o si finalizan el proceso en algún momento. La progresión a través de una serie de pasos en una aplicación web se conoce como "embudo". Puede usar los embudos de Application Insights para obtener información sobre los usuarios y supervisar las tasas de conversión paso a paso. 

## <a name="create-your-funnel"></a>Creación de un embudo
Antes de crear el embudo, debe decidir la pregunta a la que quiere obtener respuesta. Por ejemplo, es posible que desee saber cuántos usuarios están visualizando la página principal, viendo un perfil de cliente y creando un vale. En este ejemplo, los propietarios de la compañía Fabrikam Fiber desean conocer el porcentaje de clientes que han creado correctamente un vale de cliente.

Estos son los pasos que deben seguir para crear un embudo.

1. Haga clic en el botón Nuevo en la herramienta Embudos.
1. Seleccione el intervalo de tiempo "Últimos 90 días" en la lista desplegable **Intervalo de tiempo**. Seleccione "Mis embudos" o "Embudos compartidos".
1. Seleccione el evento **Índice** en la lista desplegable **Paso 1**. 
1. Seleccione el evento **Cliente** en la lista desplegable **Paso 2**.
1. Seleccione el evento **Crear** en la lista desplegable **Paso 3**.
1. Agregue un nombre para el embudo y haga clic en **Guardar**.

En la siguiente ilustración se muestran los datos que la herramienta Embudos genera. Aquí, los propietarios de Fabrikam pueden ver que, durante los últimos 90 días, el 54,3 % de sus clientes que visitó la página principal crearon un vale de cliente. También pueden ver que 2700 de sus clientes fueron al índice de la página principal, lo que podría indicar un problema de actualización.


![Herramienta Embudos con datos](./media/app-insights-understand-usage-patterns/funnel1.png)

### <a name="funnel-features"></a>Características de embudo
1. Si la aplicación está muestreada, se mostrará un banner de muestreo. Al hacer clic en el banner se abrirá un panel de contexto que indica cómo desactivar el muestreo. 
2. Puede exportar el embudo a [Power BI](app-insights-export-power-bi.md).
3. Haga clic en un paso para obtener información más detallada a la derecha. 
4. La conversión histórica muestra la conversión en los últimos 90 días. 
5. Vaya a la herramienta de los usuarios de Embudos para conocer mejor a los usuarios. Cada paso le proporcionará filtros de usuarios seleccionados. 

## <a name="next-steps"></a>Pasos siguientes
  * [Información general del uso](app-insights-usage-overview.md)
  * [Usuarios, sesiones y eventos](app-insights-usage-segmentation.md)
  * [Retención](app-insights-usage-retention.md)
  * [Libros](app-insights-usage-workbooks.md)
  * [Adición de contexto de usuario](app-insights-usage-send-user-context.md)
  * [Exportación a Power BI](app-insights-export-power-bi.md)

