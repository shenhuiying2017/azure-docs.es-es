---
title: Embudos de Azure Application Insights
description: "Obtenga información sobre cómo usar Funnels para descubrir la forma en que los clientes interactúan con la aplicación."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cfreeman
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 8d20995ebb63e8cf1e4d15c57628b80aaf9e036b
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Descubra cómo los clientes usan la aplicación con los embudos de Application Insights.

Conocer la experiencia de los clientes es de suma importancia para su negocio. Si la aplicación implica varias fases, debe saber si la mayoría de los clientes avanzan a través de todo el proceso, o si finalizan el proceso en algún momento. La progresión a través de una serie de pasos en una aplicación web se conoce como "embudo". Puede usar los embudos de Application Insights para obtener información sobre los usuarios y supervisar las tasas de conversión paso a paso. 

## <a name="get-started-with-the-funnels-blade"></a>Introducción a la hoja Embudos
La manera más fácil de obtener información sobre los embudos es a través de un ejemplo. En las siguientes ilustraciones se muestran los pasos que los propietarios de un negocio de comercio electrónico deberían seguir para obtener información sobre cómo sus clientes interactúan con su aplicación web.  

### <a name="create-your-funnel"></a>Creación de un embudo
Antes de crear el embudo, debe decidir la pregunta a la que quiere obtener respuesta. Por ejemplo, podría plantearse cuántos clientes de los que visitan su página principal hacen clic en un anuncio. En este ejemplo, los propietarios de la empresa Fabrikam Fiber desean saber el porcentaje de clientes que realizan una compra después de agregar artículos a la cesta durante el último mes.

Estos son los pasos que deben seguir para crear un embudo.

1. Haga clic en el botón Nuevo en la hoja Embudos.
1. Seleccione el intervalo de tiempo "Último mes" en la lista desplegable **Intervalo de tiempo**. 
1. Seleccione el evento **Página del producto** en la lista desplegable **Paso 1**. 
1. Seleccione el evento **Añadir a cesta** en la lista desplegable **Paso 2**.
1. Seleccione el evento **Haga clic en Comprar** en la lista desplegable **Paso 3**.
1. Agregue un nombre para el embudo y haga clic en **Guardar**.

En la siguiente ilustración se muestran los datos que la hoja Embudos genera. Desde aquí los propietarios de Fabrikam pueden ver que, durante la última semana, el 22,7 % de los clientes que han agregado algún artículo a la cesta han completado la compra. También pueden ver que el 1% de los clientes hicieron clic en un anuncio antes de visitar la página del producto y que el 20% de los clientes cerraron la sesión después de completar la compra.


![Hoja Embudos con datos](./media/app-insights-understand-usage-patterns/funnel1.png)

## <a name="next-steps"></a>Pasos siguientes
- Obtenga más información sobre los [análisis de uso](app-insights-usage-overview.md).
