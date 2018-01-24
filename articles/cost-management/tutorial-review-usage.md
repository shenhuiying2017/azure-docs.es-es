---
title: "Revisión del uso y los costos en Azure Cost Management | Microsoft Docs"
description: Revise el uso y los costos para realizar un seguimiento de las tendencias, detectar deficiencias y crear alertas.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 363a7e8a5b5be2175cb2f6d3539878673a2b469a
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="review-usage-and-costs"></a>Revisión del uso y los costos

Azure Cost Management de Cloudyn muestra el uso y los costos para que pueda realizar un seguimiento de las tendencias, detectan deficiencias y crear alertas. Todos los datos de uso y costos se muestran en los informes y paneles de Cloudyn. Los ejemplos de este tutorial le guiarán en la revisión del uso y los costos de los paneles e informes. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Realizar un seguimiento de las tendencias de uso y costos
> * Detectar deficiencias de uso
> * Crear alertas de gastos innecesarios o inusuales



## <a name="open-the-cloudyn-portal"></a>Abrir el portal de Cloudyn

Revise todos los usos y costos en el portal de Cloudyn. Abra el portal de Cloudyn desde Azure Portal o vaya a https://azure.cloudyn.com e inicie sesión.

## <a name="track-usage-and-cost-trends"></a>Realizar un seguimiento de las tendencias de uso y costos

El seguimiento del dinero real gastado en uso y costos se realiza con informes a lo largo del tiempo para identificar tendencias. Para empezar a observar tendencias, use el informe de costo real a lo largo del tiempo. En el menú de informes en la parte superior del portal, haga clic en **Costo** > **Análisis de costos** > **Actual Cost Over Time (Costo real a lo largo del tiempo)**. Cuando abra el informe por primera vez, no tendrá grupos ni filtros aplicados.

A continuación se muestra un informe de ejemplo:

![informe de ejemplo](./media/tutorial-review-usage/actual-cost01.png)

El informe muestra todos los gastos de los últimos 30 días. Para ver solo el gasto de los servicios de Azure, aplique el grupo de servicio y, a continuación, filtre por todos los servicios de Azure. En la siguiente imagen se muestran los servicios de filtrado.

![servicios filtrados](./media/tutorial-review-usage/actual-cost02.png)

En el ejemplo anterior, se gastó menos dinero a partir del 31/08/2017 que antes. La tendencia de costos se mantiene para los distintos servicios durante unos nueve días. Posteriormente, los gastos adicionales vuelven a ser los de antes. Sin embargo, un exceso de columnas puede ocultar una tendencia obvia. Puede cambiar la vista de informe a un gráfico de línea o área para ver los datos mostrados en otras vistas. En la siguiente imagen muestra la tendencia con mayor claridad.

![tendencia en el informe](./media/tutorial-review-usage/actual-cost03.png)

En el ejemplo, se ve claramente la caída de los costos de Azure Storage a partir del 31-08-2017, mientras que los gastos en otros servicios de Azure permanecían estables. Por lo tanto, ¿qué causó esa reducción en los gastos? En este ejemplo, algunos empleados estaban de vacaciones y no usaron el servicio de Storage.

Para ver un tutorial de vídeo sobre el seguimiento de las tendencias de uso y costo, consulte [Analyzing your cloud billing data vs. time with Azure Cost Management by Cloudyn](https://youtu.be/7LsVPHglM0g) (Análisis de los datos frente al tiempo de facturación en la nube con Azure Cost Management de Cloudyn).

## <a name="detect-usage-inefficiencies"></a>Detectar deficiencias de uso

Los informes de optimizador mejoran la eficacia, optimizan el uso e identifican formas de ahorrar dinero invertido en recursos de nube. Resultan especialmente útiles con las recomendaciones de ajuste de tamaño rentables diseñadas para ayudar a reducir las máquinas virtuales inactivas o costosas.

Un problema común que afecta a las organizaciones cuando mueven recursos inicialmente a la nube es su estrategia de virtualización. A menudo usan un enfoque similar al que usan para crear máquinas virtuales para el entorno de virtualización local. Además, supone que los costos se reducen moviendo sus máquinas virtuales locales a la nube, tal cual. Sin embargo, es improbable que este enfoque reduzca los costos.

El problema es que ya se ha pagado su infraestructura existente. Los usuarios pueden crear y mantener máquinas virtuales muy grandes en ejecución: inactivas o no, y con muy pocas consecuencias. Mover máquinas virtuales grandes o inactivas a la nube es probable que *aumente* los costos. La asignación de costos para recursos es importante al formalizar acuerdos con proveedores de servicios en la nube. Deberá pagar por aquello con lo que se comprometa, independientemente de si usa el recurso completamente.

El informe de recomendaciones de tamaño rentables identifica posibles ahorros anuales comparando la capacidad de tipo de instancia de máquina virtual con los datos de uso de memoria y CPU históricos correspondientes.  

En el menú de informes en la parte superior del portal, haga clic en **Optimizer (Optimizador)** > **Pricing Optimization (Optimización de precios)** > **Cost Effective Sizing Recommendations (Recomendaciones de tamaño rentables)**. Filtrar el proveedor por Azure para observar solo las máquinas virtuales de Azure. A continuación se muestra una imagen de ejemplo.

![Máquinas virtuales de Azure](./media/tutorial-review-usage/sizing01.png)

En este ejemplo, se pudieron ahorrar 3114 USD siguiendo las recomendaciones para cambiar los tipos de instancia de máquina virtual. Haga clic en el signo más (+) en **Detalles** para ver la primera recomendación. Aquí encontrará detalles acerca de la primera recomendación.

![detalles de recomendación](./media/tutorial-review-usage/sizing02.png)

Para ver los identificadores de instancia de máquina virtual, haga clic en el símbolo más junto a **Lista de candidatos**.

![Lista de candidatos](./media/tutorial-review-usage/sizing03.png)

Para ver un tutorial de vídeo sobre cómo detectar deficiencias de uso, consulte [Optimizing VM Size in Azure Cost Management by Cloudyn](https://youtu.be/1xaZBNmV704) (Optimización del tamaño de máquina virtual en Azure Cost Management de Cloudyn).

## <a name="create-alerts-for-unusual-spending"></a>Crear alertas de gastos inusuales

También pueden alertar automáticamente a las partes interesadas sobre anomalías de gastos y riesgos de gastos innecesarios. Se pueden crear alertas fácilmente mediante informes que admitan alertas basadas en umbrales de presupuesto y costos.

Cree una alerta para cualquier gasto mediante cualquier informe de costos. En este ejemplo, use el informe de costo a lo largo del tiempo para recibir una notificación cuando el gasto de la máquina virtual de Azure se acerque al presupuesto total. En el menú de informes en la parte superior del portal, haga clic en **Costo** > **Análisis de costos** > **Actual Cost Over Time (Costo real a lo largo del tiempo)**. Establezca **Grupos** en **Servicio** y **Filter on the service (Filtrar el servicio)** en **Azure/VM**. En la parte superior derecha del informe, haga clic en **Acciones** y, a continuación, seleccione **Schedule report (Programar informe)**.

Use la pestaña **Programación** para enviarse un correo electrónico del informe con la frecuencia que desee. Las etiquetas, la agrupación y el filtrado que usa se incluyen en el informe enviado por correo electrónico. Haga clic en la pestaña **Umbral** y seleccione **Actual Cost vs. Threshold (Costo real frente a umbral)**. Si tiene un presupuesto total de 500 000 USD y desea una notificación cuando los costos se acerquen a la mitad, cree un **alerta roja** en 250 000 dólares y una **alerta amarilla** en 240 000 USD. A continuación, elija el número de alertas consecutivas. Cuando recibe el número total de alertas especificado, no se envían alertas adicionales. Guarde el informe programado.

![informe de ejemplo](./media/tutorial-review-usage/schedule-alert01.png)

También puede elegir la métrica del umbral de porcentaje frente a presupuesto para crear alertas. Si usa esa métrica, puede emplear porcentajes de presupuesto en lugar de valores de divisa.


## <a name="next-steps"></a>pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Realizar un seguimiento de las tendencias de uso y costos
> * Detectar deficiencias de uso
> * Crear alertas de gastos innecesarios o inusuales


Vaya al siguiente tutorial para más información sobre el control del acceso a datos.

> [!div class="nextstepaction"]
> [Control del acceso a los datos](tutorial-user-access.md)
