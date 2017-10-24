---
title: Pausa e inicio de la capacidad de Power BI Embedded en Azure Portal | Microsoft Docs
description: "Este artículo lo guía por el proceso de pausar e iniciar una capacidad de Power BI Embedded en Microsoft Azure."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: ab5d184fde2cbcb517b325624c8405954b4d0972
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="pause-and-start-your-power-bi-embedded-capacity-in-the-azure-portal"></a>Pausa e inicio de la capacidad de Power BI Embedded en Azure Portal

Este artículo lo guía por el proceso de pausar e iniciar una capacidad de Power BI Embedded en Microsoft Azure. Se da por hecho que ha creado una capacidad de Power BI Embedded. Si no lo ha hecho, consulte [Creación de una capacidad de Power BI Embedded en Azure Portal](create-capacity.md) para empezar a trabajar.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="pause-your-capacity"></a>Pausa de la capacidad

Si pausa la capacidad, no se le facturará. Si no necesita usar la capacidad durante un período de tiempo, es una excelente opción. Siga los pasos de abajo para pausar la capacidad.

> [!NOTE]
> Al pausar una capacidad, puede evitar que el contenido esté disponible en Power BI. Asegúrese de anular las áreas de trabajo de su capacidad antes de pausarla para evitar la interrupción.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. Seleccione **Más servicios** > **Power BI Embedded** para ver sus capacidades.

    ![Opción Más servicios de Azure Portal](media/pause-start/azure-portal-more-services.png)

3. Seleccione la capacidad que desea pausar.

    ![Lista de capacidades de Power BI Embedded en Azure Portal](media/pause-start/azure-portal-capacity-list.png)

4. Seleccione **Pausar** en los detalles de la capacidad.

    ![Pausa de la capacidad](media/pause-start/azure-portal-pause-capacity.png)

5. Seleccione **Sí** para confirmar que desea pausar la capacidad.

    ![Confirme la pausa](media/pause-start/azure-portal-confirm-pause.png)

## <a name="start-your-capacity"></a>Inicio de la capacidad

Reanude el uso iniciando la capacidad. Al iniciar la capacidad, también se reanuda la facturación.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. Seleccione **Más servicios** > **Power BI Embedded** para ver sus capacidades.

    ![Opción Más servicios de Azure Portal](media/pause-start/azure-portal-more-services.png)

3. Seleccione la capacidad que desea iniciar.

    ![Lista de capacidades de Power BI Embedded en Azure Portal](media/pause-start/azure-portal-capacity-list.png)

4. Seleccione **iniciar** en los detalles de la capacidad.

    ![Inicio de la capacidad](media/pause-start/azure-portal-start-capacity.png)

5. Seleccione **Sí** para confirmar que desea iniciar la capacidad.

    ![Confirmación del inicio](media/pause-start/azure-portal-confirm-start.png)

Si cualquier contenido está asignado a esta capacidad, estará disponible una vez que se inicie.

## <a name="next-steps"></a>Pasos siguientes

Si desea escalar o reducir verticalmente la capacidad, consulte [Scale your Power BI Embedded capacity](scale-capacity.md) (Escalado de la capacidad de Power Bi Embedded).

Para empezar a insertar contenido de Power BI en una aplicación, consulte [Procedimiento para insertar paneles, informes e iconos de Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

¿Tiene más preguntas? [Pruebe a plantearlas en la Comunidad de Power BI](http://community.powerbi.com/)