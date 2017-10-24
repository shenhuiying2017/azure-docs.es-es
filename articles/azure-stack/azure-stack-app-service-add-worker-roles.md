---
title: 'Escalado horizontal de roles de trabajo de App Services: Azure Stack | Microsoft Docs'
description: Instrucciones detalladas de escalado de App Services en Azure Stack
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: slinehan
editor: anwestg
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: anwestg
ms.openlocfilehash: dbb6d956a615482e42745296c94b2c07c0086653
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="app-service-on-azure-stack-add-more-worker-roles"></a>App Service en Azure Stack: incorporación de más roles de trabajo

Este documento proporciona instrucciones acerca de cómo escalar App Service en roles de trabajo de Azure Stack. Contiene los pasos para crear roles de trabajo adicionales para admitir aplicaciones de cualquier tamaño.

> [!NOTE]
> Si su entorno de prueba de concepto de Azure Stack no tiene más de 96 GB de RAM, puede encontrar dificultades al agregar capacidad adicional.

App Service en Azure Stack, de forma predeterminada, es compatible con los niveles de trabajo gratuito y compartido. Para agregar otros niveles de trabajo, debe agregar más roles de trabajo.

Si no está seguro de lo que se implementó con la instalación predeterminada de App Service en Azure Stack, en [App Service on Azure Stack overview](azure-stack-app-service-overview.md) (Introducción a App Service en Azure Stack) encontrará más detalles.

Azure App Service en Azure Stack implementa todos los roles mediante conjuntos de escalado de máquinas virtuales y así aprovecha las ventajas de las funcionalidades de escalado de esta carga de trabajo. Por lo tanto, el escalado de los niveles de trabajo se realiza mediante el administrador de App Service.

Incorporación de roles de trabajo adicionales directamente desde el administrador del proveedor de recursos de App Service.

1. Inicie sesión en el portal de administración de Azure Stack como administrador de servicios.
2. Vaya a **App Services**.

  ![](media/azure-stack-app-service-add-worker-roles/image01.png)
3. Haga clic en **Roles**. Aquí verá el desglose de los roles de App Service implementados.
4. Haga clic en **Escalar instancias**.

  ![](media/azure-stack-app-service-add-worker-roles/image02.png)
5. En **Escalar instancias**:
    1. Seleccione el **Tipo de rol**. En esta versión preliminar, esta opción se limita a rol de trabajo web.
    2. Seleccione el **Nivel de trabajo** en el que le gustaría implementar este rol de trabajo, las opciones predeterminadas son Pequeño, Medio, Grande o Compartido. Si ha creado sus propios niveles de trabajo, también estarán disponibles para la selección.
    3. Elija cuántas **instancias de rol** adicionales desea agregar.
    4. Haga clic en **Aceptar** para implementar los roles de trabajo adicionales.
    ![](media/azure-stack-app-service-add-worker-roles/image03.png)
6. App Service en Azure Stack ahora incorporará máquinas virtuales adicionales, las configurará, instalará el software necesario y las marcará como listas cuando finalice el proceso, que puede tardar unos 80 minutos.
7. Puede supervisar el progreso de la disponibilidad de los roles de trabajo nuevos desde la hoja **Roles**.

Cuando estén totalmente implementados y listos, los roles de trabajo estarán disponibles para que los usuarios implementen su carga de trabajo en ellos. A continuación se muestra un ejemplo de los distintos niveles de precios predeterminados disponibles. Si no hay ningún rol de trabajo disponible para un nivel de trabajo concreto, la opción para elegir el nivel de precios correspondiente tampoco lo estará.
![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Para escalar horizontalmente la adición de los roles de administración, front-end o publicador, debe escalar horizontalmente el conjunto de escalado de máquinas virtuales correspondiente. Se agregará la capacidad de escalar horizontalmente estos roles a través de la administración de App Service en una versión futura.

### <a name="next-steps"></a>Pasos siguientes

[Configuración de orígenes de implementación](azure-stack-app-service-configure-deployment-sources.md)
