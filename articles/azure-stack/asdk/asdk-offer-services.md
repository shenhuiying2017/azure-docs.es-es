---
title: En este tutorial, va a crear una oferta de Azure Stack | Microsoft Docs
description: Aprenda a crear una oferta de Azure Stack, incluidos los planes y las cuotas.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 083b5e20b89f22cb8e523926858fe9ffb1441319
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-offer-azure-stack-iaas-services"></a>Tutorial: Ofrecimiento de servicios de IaaS para Azure Stack
Como administrador de la nube de Azure Stack, puede crear ofertas a las que se pueden suscribir los usuarios (a veces denominados inquilinos). Con su suscripción, los usuarios podrán consumir servicios de Azure Stack.

En este tutorial se muestra cómo crear una oferta para permitir a los usuarios crear máquinas virtuales basadas en la imagen de Windows Server 2016 Datacenter de Marketplace de Azure Stack que ha cargado en el [tutorial anterior](asdk-marketplace-item.md).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una oferta
> * Creación de un plan
> * Establecimiento de cuotas
> * Establecimiento de una oferta en pública

En Azure Stack, los servicios se prestan a los usuarios mediante planes, ofertas y suscripciones. Los usuarios pueden suscribirse a varias ofertas. Las ofertas pueden tener uno o varios planes, y los planes pueden tener uno o varios servicios, tal como se muestra en el diagrama siguiente:

![Planes, ofertas y suscripciones](media/asdk-offer-services/sop.png)

Como operador de Azure Stack que ofrece servicios, primero se le pide que cree la oferta, después, un plan y, finalmente, las cuotas. Una vez creada la oferta, los usuarios de Azure Stack pueden suscribirse a las ofertas a través del portal de usuarios.

## <a name="create-an-offer"></a>Creación de una oferta
Las **ofertas** son grupos de uno o varios planes que los operadores de Azure Stack presentan a los usuarios para que estos los compren o se suscriban a ellos.

1. Inicie sesión en el [portal de Azure Stack](https://adminportal.local.azurestack.external) como operador.

2. Haga clic en **Nuevo** > **Ofertas + Planes** > **Oferta**.

   ![Nueva oferta](media/asdk-offer-services/new-offer.png)

2. En la sección **Nueva oferta**, rellene el **nombre para mostrar** y el **nombre de recurso** y, a continuación, seleccione un **grupo de recursos** nuevo o existente. El nombre para mostrar es el nombre descriptivo público de la oferta que ven los usuarios. Solo el operador de la nube puede ver el nombre del recurso, que usan los administradores para trabajar con la oferta como un recurso de Azure Resource Manager.

   ![Nombre para mostrar](media/asdk-offer-services/offer-display-name.png)


## <a name="create-a-plan"></a>Creación de un plan
Después de escribir la información de la oferta básica, debe agregar al menos un plan base a la oferta. 

Los **planes** permiten a los operadores de Azure Stack agrupar los servicios, y sus cuotas asociadas, que se ofrecen a los usuarios.

1. Haga clic en **Planes base** y en la sección **Plan**, haga clic en **Agregar** para agregar un nuevo plan a la oferta.

   ![Agregar un plan](media/asdk-offer-services/new-plan.png)

2. En la sección **Nuevo plan**, rellene el **nombre para mostrar** y el **nombre del recurso**. El nombre para mostrar es el nombre descriptivo del plan que ven los usuarios. Solo el operador de la nube puede ver el nombre del recurso, que usan los operadores de la nube para trabajar con el plan como un recurso de Azure Resource Manager.

   ![Nombre para mostrar del plan](media/asdk-offer-services/plan-display-name.png)

3. Después, seleccione los servicios que se van a incluir en el plan. Para ofrecer servicios IaaS, haga clic en **Servicios**, seleccione **Microsoft.Compute**, **Microsoft.Network** y **Microsoft.Storage** y, después, haga clic en **Seleccionar**.

   ![Servicios del plan](media/asdk-offer-services/select-services.png)


## <a name="set-quotas"></a>Establecimiento de cuotas
Ahora que la oferta tiene un plan que incluye servicios, debe establecer cuotas para cada uno de ellos. 

Las **cuotas** determinan la cantidad de recursos que un usuario puede consumir por cada servicio incluido en el plan que se ofrece.

1. Haga clic en **Cuotas** y, a continuación, seleccione el servicio para el que desea crear una cuota. 

   Para comenzar, primero se crea una cuota para el servicio de proceso. En la lista de espacio de nombres, seleccione el espacio de nombres **Microsoft.Compute** y, a continuación, haga clic en **Crear nueva cuota**.
   
   ![Crear nueva cuota](media/asdk-offer-services/create-quota.png)

2. En la sección **Crear cuota**, escriba un nombre para la cuota, establezca los parámetros deseados para la cuota y haga clic en **Aceptar**.

   ![Nombre de cuota](media/asdk-offer-services/quota-properties.png)

3. Seleccione la cuota que ha creado para el servicio **Microsoft.Compute**.

   ![Seleccione la cuota](media/asdk-offer-services/set-quota.png)

4. Repita los pasos 1 a 3 para establecer cuotas para los servicios de red y de almacenamiento y, después, haga clic en Aceptar en la sección Cuotas. Después, haga clic en **Aceptar** en la sección **Nuevo plan** para completar la configuración del plan. 

   ![Todas las cuotas establecidas](media/asdk-offer-services/all-quotas-set.png)

5. Termine de crear la oferta haciendo clic en **Crear** en la sección del plan. Verá una notificación cuando la oferta se haya creado correctamente y aparecerá como oferta disponible.

   ![Oferta creada](media/asdk-offer-services/offer-complete.png)

## <a name="set-offer-to-public"></a>Establecimiento de una oferta en pública
Las ofertas deben hacerse públicas para que los usuarios las vean al seleccionar una oferta a la que suscribirse. 

Las ofertas pueden ser:
- **Públicas**: visibles para todos los usuarios.
- **Privadas**: solo son visibles para los administradores de la nube. Resulta útil durante la elaboración del plan o la oferta, o si el administrador de la nube desea crear cada una de las suscripciones para los usuarios.
- **Retirados**: cerrados a nuevos suscriptores. El administrador de la nube puede usar las ofertas y planes retirados para evitar futuras suscripciones, pero dejar intactos los suscriptores actuales.

> [!TIP]
> Los cambios en la oferta no son inmediatamente visibles para los usuarios. Para ver los cambios, es posible que los usuarios tengan que cerrar sesión y volver a iniciarla en el [portal de usuarios](https://portal.local.azurestack.external) para ver la nueva oferta.

Para establecer la nueva oferta en pública: 

1. En el menú del panel, haga clic en **Ofertas** y, a continuación, haga clic en la oferta que ha creado.

2. Haga clic en **Cambiar estado** y, a continuación, haga clic en **Público**.

   ![Estado público](media/asdk-offer-services/set-public.png)

3. La oferta ahora estará disponible en el portal de usuarios de Azure Stack.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Creación de una oferta
> * Creación de un plan
> * Establecimiento de cuotas
> * Establecimiento de una oferta en pública

Avance al siguiente tutorial para aprender a suscribirse a la oferta que acaba de crear como usuario de Azure Stack.

> [!div class="nextstepaction"]
> [Suscripción a una oferta](asdk-subscribe-services.md)