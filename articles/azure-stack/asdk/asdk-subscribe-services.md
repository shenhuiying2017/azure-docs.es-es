---
title: En este tutorial, aprenderá a suscribirse a una oferta de Azure Stack | Microsoft Docs
description: En este tutorial se muestra cómo crear una nueva suscripción a los servicios de Azure Stack y probar la oferta mediante la creación de una máquina virtual de prueba.
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
ms.openlocfilehash: d2adda5613b9a10bc3314045b9d68b0f3196f19a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-create-and-test-a-subscription"></a>Tutorial: Creación y prueba de una suscripción
En este tutorial se muestra cómo crear una suscripción que contiene una oferta y, después, probarla. Para la prueba, inicie sesión en el [portal de usuarios](https://portal.local.azurestack.external) como administrador de la nube, suscríbase a la oferta y, a continuación, cree una máquina virtual.

> [!TIP]
> Para una experiencia de evaluación más avanzada, puede [crear una suscripción para un usuario en particular](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator) y después iniciar sesión como ese usuario en el portal de usuarios. 

En este tutorial se muestra cómo suscribirse a la oferta que creó en el [tutorial anterior](asdk-offer-services.md).

Lo qué aprenderá:

> [!div class="checklist"]
> * Suscripción a una oferta 
> * Probar la oferta

## <a name="subscribe-to-an-offer"></a>Suscripción a una oferta
Para suscribirse a una oferta como usuario, debe iniciar sesión en el portal de usuarios de Azure Stack para descubrir los servicios que ha ofrecido el operador de Azure Stack.

1. Inicie sesión en el [portal de usuarios](https://portal.local.azurestack.external) y haga clic en **Obtener una suscripción**.

   ![Obtener una suscripción](media/asdk-subscribe-services/get-subscription.png)

2. En el campo **Nombre para mostrar** escriba un nombre para la suscripción. Después, haga clic en **Oferta** para seleccionar una de las ofertas disponibles en la sección **Elegir una oferta** y, finalmente, haga clic en **Crear**.

   ![Creación de una oferta](media/asdk-subscribe-services/create-subscription.png)

   > [!TIP]
   > Debe actualizar ahora el portal de usuarios para empezar a usar la suscripción.

3. Para ver la suscripción que acaba de crear, haga clic en **Más servicios**, en **Suscripciones** y, luego, en la nueva suscripción. Después de suscribirse a una oferta, actualice el portal para ver si se han incluido nuevos servicios como parte de la nueva suscripción. En este ejemplo, se ha agregado **Máquinas virtuales**.

   ![Ver suscripción](media/asdk-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>Probar la oferta
Al iniciar sesión en el [portal de usuarios](https://portal.local.azurestack.external), puede probar la oferta mediante el aprovisionamiento de una máquina virtual con las nuevas funcionalidades de suscripción. 

> [!NOTE]
> Esta prueba requiere la imagen de la máquina virtual de Windows Server 2016 Datacenter que se agregó al Marketplace de Azure Stack en un [tutorial anterior](asdk-marketplace-item.md). 

1. Inicie sesión en el [portal de usuarios](https://portal.local.azurestack.external).

2. En el portal de usuarios, haga clic en **Máquinas virtuales** > **Agregar** > **Windows Server 2016 Datacenter** y, después, haga clic en **Crear**.

3. En la sección **Datos básicos**, escriba un **nombre**, **nombre de usuario** y **contraseña**, elija una **suscripción**, cree un **grupo de recursos** (o seleccione uno existente) y, a continuación, haga clic en **Aceptar**.

4. En la sección **Elegir un tamaño**, haga clic en **A1 Estándar** y, después, en **Seleccionar**.  

5. En la hoja de configuración, acepte los valores predeterminados y haga clic en **Aceptar**.

6. En la sección **Resumen**, haga clic en **Aceptar** para crear la máquina virtual.  

7. Para ver la nueva máquina virtual, haga clic en **Máquinas virtuales** y, a continuación, busque la nueva máquina virtual y haga clic en su nombre.

    ![Todos los recursos](media/asdk-subscribe-services/view-vm.png)

> [!NOTE]
> La implementación de la máquina virtual tardará unos minutos en completarse.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió:

> [!div class="checklist"]
> * Suscripción a una oferta 
> * Probar la oferta

Avance al siguiente tutorial para aprender a crear una máquina virtual con una plantilla.

> [!div class="nextstepaction"]
> [Creación de una máquina virtual desde una plantilla](asdk-create-vm-template.md)