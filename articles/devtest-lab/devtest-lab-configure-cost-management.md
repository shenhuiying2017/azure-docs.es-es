---
title: "Visualización de las tendencias de costos mensuales estimados de laboratorio en Azure DevTest Labs | Microsoft Docs"
description: "Aprenda sobre el gráfico de tendencias de costos mensuales estimados de Azure DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2017
ms.author: v-craic
ms.openlocfilehash: 660180fac4f4743bc543b1babf7dbe921bf8c26b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Visualización de las tendencias de costos mensuales estimados de laboratorio en Azure DevTest Labs
La característica de administración de costos de Laboratorios de desarrollo y pruebas le ayuda a controlar el costo del laboratorio. En este artículo se muestra el uso del gráfico **Monthly Estimated Cost Trend** (Tendencia de costos estimados mensuales) para ver el costo estimado hasta la fecha del mes de calendario actual, así como el costo a fin de mes previsto para el mes de calendario actual. En este artículo también se muestra cómo administrar mejor los costos de laboratorio estableciendo objetivos y umbrales de gastos que, al alcanzarlos, desencadenan que DevTest Labs le informe de los resultados.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Visualización del gráfico de tendencias de costo estimado mensual
Para ver el gráfico de tendencias de costos mensuales estimados, siga estos pasos: 

1. Inicie sesión en el [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Si es necesario, seleccione **Todos los servicios** y, luego, **DevTest Labs** en la lista. (Puede que su laboratorio ya aparezca en el panel, en **Todos los recursos**).
1. En la lista de laboratorios, seleccione el laboratorio que desee.  
1. En el área **Introducción** del laboratorio, seleccione **Configuración y directivas**.   
1. A la izquierda, en **SEGUIMIENTO DE COSTOS**, seleccione **Tendencia de costos**.

   La siguiente captura de pantalla muestra un ejemplo de gráfico de costos. 
   
    ![Gráfico de costos](./media/devtest-lab-configure-cost-management/graph.png)

El valor **Costo estimado** es el costo estimado hasta la fecha del mes de calendario actual. El valor de **Projected cost** (Costo previsto) es el costo estimado para el mes de calendario actual completo, calculado con el costo de laboratorio para los cinco días anteriores.

Los importes de los costos se redondean al siguiente número entero. Por ejemplo:  

* 5,01 se redondea a 6. 
* 5,50 se redondea a 6.
* 5,99 se redondea a 6.

Como indica anteriormente el gráfico, los costos que se ven de forma predeterminada en el gráfico son costos *estimados* con tarifas de oferta de [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/). También puede establecer sus propios objetivos de gastos que se muestran en los gráficos mediante la [administración de los objetivos de costos para un laboratorio](#managing-cost-targets-for-your-lab).

Además, en el cálculo de costos *no* se incluye lo siguiente:

* Actualmente no se admiten suscripciones de DreamSpark y CSP, puesto que Azure DevTest Labs emplea las [API de facturación de Azure](../billing/billing-usage-rate-card-overview.md) para calcular el costo de laboratorio, y estas suscripciones no las admiten.
* Las tarifas de su oferta. Actualmente, no puede usar las tarifas de oferta (que aparecen en su suscripción) negociadas con Microsoft o con asociados de Microsoft. Solo se usan tarifas de pago por uso.
* Los impuestos
* Los descuentos
* La divisa de facturación. Actualmente, el costo de laboratorio solo se muestra en divisa USD.

## <a name="managing-cost-targets-for-your-lab"></a>Administración de objetivos de costos para un laboratorio
DevTest Labs le permite administrar mejor los costos de su laboratorio estableciendo un objetivo de gasto que puede ver en el gráfico de tendencias de costos mensuales estimados. DevTest Labs también puede enviarle una notificación cuando se alcanza el umbral o el gasto objetivo especificado. 

1. En el panel **Información general** del laboratorio, seleccione **Configuración y directivas**.
1. A la izquierda, en **SEGUIMIENTO DE COSTOS**, seleccione **Tendencia de costos**.

    ![Botón Administrar objetivo](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. En el panel **Tendencia de costos**, seleccione **Administrar objetivo**.

    ![Botón Administrar objetivo](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. En el panel Administrar objetivo, especifique los objetivos y umbrales de gastos deseados. También puede establecer si se informa de cada umbral seleccionado en el gráfico de tendencias de costos o mediante una notificación de webhook.

    ![Panel Administrar objetivo](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Seleccione un período de tiempo durante el que desea que se realice el seguimiento de los objetivos de costos.
      - **Mensual**: se realiza un seguimiento de los objetivos de costos mensual.
      - **Fijo**: se realiza un seguimiento de los objetivos de costos para el rango de fechas que especifique en los campos Fecha de inicio y Fecha de finalización. Por lo general, esto puede corresponder a cuánto tiempo está programada la ejecución del proyecto.
   - Especifique un **Costo objetivo**. Por ejemplo, puede tratarse de cuánto planea gastar en este laboratorio en el período de tiempo definido.
   - Seleccione habilitar o deshabilitar cualquier umbral del que desee que le informen, en incrementos del 25 %, hasta un 125 % del **Costo objetivo** que ha especificado.
      - **Notificar**: cuando se alcanza este umbral, se le notificará mediante la dirección URL de webhook que especifique.
      - **Trazado en gráfico**: cuando se alcanza este umbral, los resultados se trazan en el gráfico de tendencias de costos que puede ver, tal como se describe en [Visualización del gráfico de tendencias de costo estimado mensual](#viewing-the-monthly-estimated-cost-trend-chart).
   - Si opta por **Notificar** cuando se alcanza el umbral, debe especificar una dirección URL de webhook. En el área de integraciones de costos, seleccione **Haga clic aquí para agregar una integración**.

      Escriba una dirección URL de webhook en el panel Configurar notificación y seleccione **Aceptar**.

       ![Panel Configurar notificación](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - Si especifica **Notificar**, debe definir una dirección URL de webhook.
      - Del mismo modo, si define una dirección URL de webhook, debe establecer **Notificación** en **Activado** en el panel Umbral de costo.
      - Tiene que crear un webhook antes de especificarlo aquí.  

      Para obtener más información sobre los webhooks, consulte [Creación de un webhook o una función de API de Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Entradas blogs relacionadas
* [Two more things to keep your cost on track in DevTest Labs (Dos cosas para mantener el costo por el buen camino en DevTest Labs)](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Why Cost Thresholds? (¿Por qué los umbrales de costo?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>pasos siguientes
Vea algunas acciones que puede probar a continuación:

* [Definición de directivas de laboratorio](devtest-lab-set-lab-policy.md): obtenga información sobre cómo establecer las distintas directivas que se emplean para controlar el uso del laboratorio y sus máquinas virtuales. 
* [Creación de una imagen personalizada](devtest-lab-create-template.md): cuando cree una máquina virtual, tendrá que especificar una base, que puede ser una imagen personalizada o una imagen de Marketplace. Este artículo muestra cómo crear una imagen personalizada desde un archivo VHD.
* [Configuración de imágenes de Marketplace](devtest-lab-configure-marketplace-images.md): DevTest Labs admite la creación de máquinas virtuales basadas en imágenes de Azure Marketplace. En este artículo se muestra cómo especificar las imágenes de Azure Marketplace, si corresponde, que se pueden usar en el momento de crear máquinas virtuales en un laboratorio.
* [Creación de una máquina virtual en un laboratorio](devtest-lab-add-vm.md): se muestra cómo crear una máquina virtual desde una imagen base (ya sea personalizada o de Marketplace) y cómo trabajar con artefactos en la máquina virtual.

