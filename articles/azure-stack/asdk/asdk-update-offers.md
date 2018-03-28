---
title: En este tutorial, aprenderá a actualizar ofertas y planes de Azure Stack | Microsoft Docs
description: En este artículo se describe cómo ver y modificar ofertas y planes de Azure Stack.
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
ms.openlocfilehash: 3b5d2ab5e924f578f5838d11b0d2058aacf67dec
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-update-offers-and-plans"></a>Tutorial: Actualización de ofertas y planes
Como operador de Azure Stack, va a crear planes que contienen los servicios deseados y las cuotas aplicables para que sus usuarios se suscriban. Estos *planes base* contienen los servicios básicos que se ofrecerán a los usuarios y solo se puede tener un plan base por oferta. Si necesita modificar su oferta, puede utilizar *planes complementarios* que le permitan modificar el plan para ampliar las cuotas de equipo, almacenamiento o red ofrecidas inicialmente con el plan base. 

Aunque combinar todo en un solo plan puede ser adecuado en algunos casos, es posible que desee tener un plan base y ofrecer servicios adicionales utilizando planes complementarios. Por ejemplo, podría decidir ofrecer servicios IaaS como parte de un plan base, con todos los servicios PaaS tratados como planes complementarios. Los planes también se pueden utilizar para controlar el consumo de recursos en su entorno del Kit de desarrollo de Azure Stack limitado. Por ejemplo, si desea que los usuarios sean conscientes de su uso de recursos, podría tener un plan base relativamente pequeño (dependiendo de los servicios requeridos) y a medida que los usuarios alcancen la capacidad, se les avisaría de que ya han consumido la asignación de recursos basada en su plan asignado. Desde allí, los usuarios pueden seleccionar un plan complementario disponible para recursos adicionales. 

> [!NOTE]
> Cuando un usuario agrega un plan complementario a una suscripción de oferta existente, los recursos adicionales pueden tardar hasta una hora en aparecer. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un plan complementario 
> * Suscribirse al plan complementario

## <a name="create-an-add-on-plan"></a>Crear un plan complementario
Los **planes complementarios** se crean al modificar una oferta existente.

1. Inicie sesión en el [portal de Azure Stack](https://adminportal.local.azurestack.external) como operador.
2. Siga los mismos pasos utilizados anteriormente para [crear un plan base](asdk-offer-services.md) para crear un nuevo plan que ofrezca servicios que no se ofrecían anteriormente. En este ejemplo, los servicios de Key Vault (Microsoft.KeyVault) se incluirán en el plan.
3. En el portal de administrador, haga clic en **Ofertas** y, a continuación, seleccione la oferta que desea actualizar con un plan complementario.

   ![](media/asdk-update-offers/1.PNG)

4.  Desplácese hasta el final de las propiedades de la oferta y seleccione **Planes complementarios**. Haga clic en **Agregar**.
   
    ![](media/asdk-update-offers/2.PNG)

5. Seleccione el plan que se va a agregar. En este ejemplo, el plan se denomina **Key vault plan** (Plan de almacén de claves) y, después, haga clic en **Seleccionar** para agregar el plan a la oferta. Debería recibir una notificación de que el plan se agregó correctamente a la oferta.
   
    ![](media/asdk-update-offers/3.PNG)

6. Revise la lista de planes complementarios incluidos con la oferta para comprobar que el nuevo plan complementario aparezca en la lista.
   
    ![](media/asdk-update-offers/4.PNG)

## <a name="subscribe-to-the-add-on-plan"></a>Suscripción al plan complementario
Necesita iniciar sesión en el portal de usuarios de Azure Stack para ampliar una suscripción existente de Azure Stack con un plan complementario.

Siga estos pasos para detectar recursos adicionales que ha ofrecido el operador de Azure Stack y agregue un plan complementario a una oferta a la que se haya suscrito previamente.

1. Inicie sesión en el [portal de usuarios](https://portal.local.azurestack.external).
2. Para encontrar la suscripción que desea ampliar con el plan complementario, haga clic en **Más servicios**, haga clic en **Suscripciones** y, después, seleccione su suscripción.
   
    ![](media/asdk-update-offers/5.PNG)

3.  En la información general de la suscripción, haga clic en **Agregar plan**.
   
    ![](media/asdk-update-offers/6.PNG)

4. En la hoja Agregar plan, seleccione el plan complementario que desea agregar a la suscripción. En este ejemplo, se selecciona **Key vault plan** (Plan de almacén de claves). A continuación, recibirá una notificación de que el plan complementario se ha adquirido correctamente y de que necesita actualizar el portal para acceder a la suscripción actualizada.
   
    ![](media/asdk-update-offers/7.PNG)

5. Por último, revise los planes complementarios asociados con la suscripción para asegurarse de que el plan complementario se ha agregado correctamente.
   
    ![](media/asdk-update-offers/8.PNG)


## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Crear un plan complementario 
> * Suscribirse al plan complementario

> [!div class="nextstepaction"]
> [Creación de una máquina virtual desde una plantilla](asdk-create-vm-template.md)

