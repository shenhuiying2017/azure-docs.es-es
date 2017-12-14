---
title: Procedimiento para implementar un servicio web en varias regiones | Microsoft Docs
description: Pasos para implementar (copiar) un servicio web nuevo en otras regiones
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondl
editor: cgronlun
ms.assetid: 36c60411-f2db-4ee2-9b66-b1f1d77a8f44
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: abd6f05e9b9ce711ce55e88f07aa13287c76ebc2
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2017
---
# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>Procedimiento para implementar un servicio web en varias regiones
Gracias a los servicios web nuevos de Azure, se puede implementar fácilmente un servicio web en varias regiones sin necesidad de disponer de varias suscripciones o áreas de trabajo. 

Los precios dependen de la región, por lo tanto, debe definir un plan de facturación para cada región en la que implementará el servicio web.

## <a name="to-create-a-plan-in-another-region"></a>Pasos para crear un plan en otra región
1. Inicie sesión en el [portal de servicios web de Microsoft Azure Machine Learning](https://services.azureml.net/).
2. Haga clic en la opción de menú **Planes** .
3. En la página de información general de Planes, haga clic en **Nuevo**.
4. En el menú desplegable **Suscripción** , seleccione la suscripción en que residirá el nuevo plan.
5. En el menú desplegable **Región** , seleccione una región para el nuevo plan. Las opciones de planes de la región seleccionada se mostrarán en la sección de la página **Plan Options** (Opciones de planes).
6. En el menú desplegable **Grupo de recursos** , seleccione un grupo de recursos para el plan. Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
7. En **Nombre del plan** , escriba el nombre del plan.
8. En **Plan Options**(Opciones de planes), haga clic en el nivel de facturación del nuevo plan.
9. Haga clic en **Crear**.

## <a name="deploying-the-web-service-to-another-region"></a>Implementación del servicio web en otra región
1. Haga clic en la pestaña **Servicios web** .
2. Seleccione el servicio web que se va a implementar en una nueva región.
3. Haga clic en **Copiar**.
4. En **Nombre de servicio web**, escriba un nombre nuevo del servicio web.
5. En **Descripción del servicio web**, escriba una descripción del servicio web.
6. En el menú desplegable **Suscripción** , seleccione la suscripción en que residirá el servicio web nuevo.
7. En el menú desplegable **Grupo de recursos** , seleccione un grupo de recursos para el servicio web. Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
8. En el menú desplegable **Región** , seleccione la región en la que se va a implementar el servicio web.
9. En el menú desplegable **Cuenta de almacenamiento** , seleccione la cuenta de almacenamiento en la que se va a almacenar el servicio web.
10. En el menú desplegable **Price Plan** (Plan de precios), seleccione un plan en la región que seleccionó en el paso 8.
11. Haga clic en **Copiar**.

