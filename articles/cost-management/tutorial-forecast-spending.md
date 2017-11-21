---
title: "Previsión de gastos con Azure Cost Management | Microsoft Docs"
description: Pronostique los gastos mediante el historial de uso y los datos de gastos.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: d8b0cd2a3e5f9829f0844783aad22d375eb9d7a8
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2017
---
# <a name="forecast-future-spending"></a>Previsión de gastos futuros

Azure Cost Management de Cloudyn le ayuda a pronosticar los gastos futuros mediante el historial de uso y los datos de gasto. Use informes de Cloudyn para ver todos los datos de proyección de costos. Los ejemplos de este tutorial le guían por la revisión de las proyecciones de costos mediante los informes. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Previsión de gastos futuros

## <a name="forecast-future-spending"></a>Previsión de gastos futuros

Cloudyn incluye informes de proyección de costos que le ayudarán a pronosticar los gastos según su uso con el paso del tiempo. Su objetivo principal es ayudarle a asegurarse de que sus tendencias de costo no superen las expectativas de su organización. Los informes que usará son el de costo previsto del mes en curso y el de costo previsto anual. Ambos muestran el gasto futuro previsto si su uso permanece relativamente coherente con los últimos 30 días de uso.

El informe de costo previsto del mes en curso muestra los costos de los servicios. Emplea los costos desde el comienzo del mes y el mes anterior para mostrar el costo previsto. En el menú de informes de la parte superior del portal, haga clic en **Cost** >  (Costo) **Projection and Budget** >  (Proyección y presupuesto) **Current Month Projected Cost** (Costo previsto del mes en curso). En la imagen siguiente se muestra un ejemplo:

![costo previsto del mes en curso](./media/tutorial-forecast-spending/project-month01.png)

En el ejemplo, puede ver en qué servicios se realiza un mayor gasto. Los costos de Azure fueron más bajos que los costos de AWS. Si quiere ver los detalles de la proyección de costos para las máquinas virtuales de Azure, en la lista **Filter** (Filtro), seleccione **Azure/VM**.

![Costo previsto del mes en curso de la máquina virtual de Azure](./media/tutorial-forecast-spending/project-month02.png)

Siga los mismos pasos básicos anteriores para examinar las proyecciones de costos mensuales para otros servicios que le interesen.

El informe de costo previsto anual muestra el costo extrapolado de los servicios durante los próximos 12 meses.

En el menú de informes de la parte superior del portal, haga clic en **Cost** >  (Costo) **Projection and Budget** >  (Proyección y presupuesto) **Annual Projected Cost** (Costo previsto anual). En la imagen siguiente se muestra un ejemplo:

![informe de costo previsto anual](./media/tutorial-forecast-spending/project-annual01.png)

En el ejemplo, puede ver en qué servicios se realiza un mayor gasto. Al igual que en el ejemplo mensual, los costos de Azure eran más bajos que los costos de AWS. Si quiere ver los detalles de la proyección de costos para las máquinas virtuales de Azure, en la lista **Filter** (Filtro), seleccione **Azure/VM**.

![costo previsto anual de máquinas virtuales](./media/tutorial-forecast-spending/project-annual02.png)

En la imagen anterior, el costo previsto anual de máquinas virtuales de Azure es 28.374 dólares USA.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Previsión de gastos futuros


Vaya al siguiente tutorial para conocer cómo administrar los costos con los informes de visualización y asignación de costos.

> [!div class="nextstepaction"]
> [Administración de costos con informes de visualización y asignación de costos](tutorial-manage-costs.md)
