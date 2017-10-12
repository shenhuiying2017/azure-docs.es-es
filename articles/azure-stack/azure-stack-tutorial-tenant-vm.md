---
title: "Máquinas virtuales disponibles para los usuarios de Azure Stack | Microsoft Docs"
description: "Tutorial para que las máquinas virtuales estén disponibles en Azure Stack"
services: azure-stack
documentationcenter: 
author: vhorne
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 9/25/2017
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: afeec92c40262903e6cfd3c6d75a595fead616e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="make-virtual-machines-available-to-your-azure-stack-users"></a>Máquinas virtuales disponibles para los usuarios de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Como administrador de la nube de Azure Stack, puede crear ofertas a las que se pueden suscribir los usuarios (a veces denominados inquilinos). Con su suscripción, los usuarios podrán consumir servicios de Azure Stack.

En este artículo se muestra cómo crear una oferta y, a continuación, cómo probarla. Para la prueba, inicie sesión en el portal como usuario, suscríbase a la oferta y, a continuación, cree una máquina virtual con la suscripción.

Lo qué aprenderá:

> [!div class="checklist"]
> * Creación de una oferta
> * Añadir una imagen
> * Probar la oferta


En Azure Stack, los servicios se prestan a los usuarios mediante planes, ofertas y suscripciones. Los usuarios pueden suscribirse a varias ofertas. Las ofertas pueden tener uno o varios planes, y los planes pueden tener uno o varios servicios.

![Planes, ofertas y suscripciones](media/azure-stack-key-features/image4.png)

Para más información, vea [Características y conceptos clave de Azure Stack](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Creación de una oferta

Ahora puede preparar todo para los usuarios. Cuando se inicia el proceso, primero deberá crear la oferta, a continuación, un plan y, finalmente, las cuotas.

3. **Creación de una oferta**

   Las ofertas son grupos de uno o varios planes que los proveedores presentan a los usuarios para que estos los compren o se suscriban a ellos.

   a. [Inicie sesión](azure-stack-connect-azure-stack.md) en el portal como administrador de la nube y, a continuación, haga clic en **Nuevo** > **Ofertas para los inquilinos + Planes** > **Oferta**.
   ![Nueva oferta](media/azure-stack-tutorial-tenant-vm/image01.png)

   b. En la sección **Nueva oferta**, rellene el **nombre para mostrar** y el **nombre de recurso** y, a continuación, seleccione un **grupo de recursos** nuevo o existente. El nombre para mostrar es el nombre descriptivo de la oferta. Solo el operador de la nube puede ver el nombre del recurso. Es el nombre que usan los administradores para trabajar con la oferta como un recurso de Administrador de recursos de Azure.

   ![Nombre para mostrar](media/azure-stack-tutorial-tenant-vm/image02.png)

   c. Haga clic en **Planes base** y en la sección **Plan**, haga clic en **Agregar** para agregar un nuevo plan a la oferta.

   ![Agregar un plan](media/azure-stack-tutorial-tenant-vm/image03.png)

   d. En la sección **Nuevo plan**, rellene el **nombre para mostrar** y el **nombre del recurso**. El nombre para mostrar es el nombre descriptivo del plan que ven los usuarios. Solo el operador de la nube puede ver el nombre del recurso. Es el nombre que usan los operadores de la nube para trabajar con el plan como recurso de Azure Resource Manager.

   ![Nombre para mostrar del plan](media/azure-stack-tutorial-tenant-vm/image04.png)

   e. Haga clic en **Servicios**, seleccione **Microsoft.Compute**, **Microsoft.Network** y **Microsoft.Storage** y, a continuación, haga clic en **Seleccionar**.

   ![Servicios del plan](media/azure-stack-tutorial-tenant-vm/image05.png)

   f. Haga clic en **Cuotas** y, a continuación, seleccione el primer servicio para el que desea crear una cuota. Para una cuota de la infraestructura como servicio, siga estos pasos para los servicios de proceso, red y almacenamiento.

   En este ejemplo, primero se crea una cuota para el servicio de proceso. En la lista de espacio de nombres, seleccione el espacio de nombres **Microsoft.Compute** y, a continuación, haga clic en **Crear nueva cuota**.
   
   ![Crear nueva cuota](media/azure-stack-tutorial-tenant-vm/image06.png)

   g. En la sección **Crear cuota**, escriba un nombre para la cuota y establezca los parámetros deseados para la cuota y haga clic en **Aceptar**.

   ![Nombre de cuota](media/azure-stack-tutorial-tenant-vm/image07.png)

   h. Ahora, para **Microsoft.Compute**, seleccione la cuota que ha creado.

   ![Seleccione la cuota](media/azure-stack-tutorial-tenant-vm/image08.png)

   Repita estos pasos para Network y Storage services y, a continuación, haga clic en **Aceptar** en la sección **Cuotas**.

   i. Haga clic en **Aceptar** en la sección **Nuevo plan**.

   j. En la sección **Plan**, seleccione el nuevo plan y haga clic en **Seleccionar**.

   k. En la sección **Nueva oferta**, haga clic en **Crear**. Verá una notificación cuando se haya creado la oferta.

   l. En el menú del panel, haga clic en **Ofertas** y, a continuación, haga clic en la oferta que ha creado.

   m. Haga clic en **Cambiar estado** y, a continuación, haga clic en **Público**.

   ![Estado público](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Añadir una imagen

Antes de poder aprovisionar a las máquinas virtuales, debe agregar una imagen en Marketplace de Azure Stack. Puede agregar la imagen que desee, incluidas las imágenes de Linux, de Azure Marketplace.

Si trabaja en un escenario conectado y si ha registrado la instancia de Azure Stack con Azure, a continuación, puede descargar la imagen de la VM de Windows Server 2016 de Azure Marketplace mediante el uso de los pasos descritos en el tema [Descarga de elementos Marketplace de Azure en Azure Stack](azure-stack-download-azure-marketplace-item.md).

Para obtener información acerca de cómo agregar diferentes elementos a Marketplace, vea [Marketplace de Azure Stack](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Probar la oferta

Ahora que ha creado una oferta, puede probarla. Inicie sesión como usuario y suscríbase a la oferta y, a continuación, agregue una máquina virtual.

1. **Suscripción a una oferta**

   Ahora puede iniciar sesión en el portal como usuario para suscribirse a una oferta.

   a. Inicie sesión en el portal de usuarios como usuario y haga clic en **Obtener una suscripción**.
   - En un sistema integrado, la dirección URL varía en función de la región y el nombre de dominio externo del operador, y estará en el formato https://portal.&lt;*región*&gt;.&lt;*nombre completo*&gt;.
   - Si usa Azure Stack Development Kit, la dirección del portal es https://portal.local.azurestack.external.

   ![Obtener una suscripción](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. En el campo **Nombre para mostrar**, escriba un nombre para la suscripción, haga clic en **Oferta**, haga clic en una de las ofertas en la sección **Elija una oferta** y, a continuación, haga clic en **Crear**.

   ![Creación de una oferta](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. Para ver la suscripción que ha creado, haga clic en **Más servicios**, en **Suscripciones** y, luego, en la nueva suscripción.  

   Después de suscribirse a una oferta, actualice el portal para ver los servicios que forman parte de la nueva suscripción.

2. **Aprovisionamiento de una máquina virtual**

   Ahora puede iniciar sesión en el portal como usuario para aprovisionar a una máquina virtual mediante la suscripción. 

   a. Inicie sesión en el portal de usuario como usuario y luego haga clic en **Nuevo** > **Proceso** > **Windows Server 2016 Datacenter Eval**.
      - En un sistema integrado, la dirección URL varía en función de la región y el nombre de dominio externo del operador, y estará en el formato https://portal.&lt;*región*&gt;.&lt;*nombre completo*&gt;.
   - Si usa Azure Stack Development Kit, la dirección del portal es https://portal.local.azurestack.external.

   b. En la sección **Aspectos básicos**, escriba un **Nombre**, **Nombre de usuario** y **Contraseña**. Para **Tipo de disco de máquina virtual**, elija **HDD**. Elija una **suscripción**. Cree un **grupo de recursos** o seleccione uno existente y, a continuación, haga clic en **Aceptar**.  

   c. En la sección **Elegir un tamaño**, haga clic en **A1 Básico**y, a continuación, haga clic en **Seleccionar**.  

   d. En la sección **Configuración**, haga clic en **Red virtual**. En la sección **Elegir red virtual** , haga clic en **Crear nueva**. En la sección **Crear red virtual**, acepte todos los valores predeterminados y haga clic en **Aceptar**. En la sección **Configuración**, haga clic en **Aceptar**.

   ![Creación de una red virtual](media/azure-stack-provision-vm/image04.png)

   e. En la sección **Resumen**, haga clic en **Aceptar** para crear la máquina virtual.  

   f. Para ver la nueva máquina virtual, haga clic en **Todos los recursos** y, a continuación, busque la máquina virtual y haga clic en su nombre.

    ![Todos los recursos](media/azure-stack-provision-vm/image06.png)

En este tutorial aprendió:

> [!div class="checklist"]
> * Creación de una oferta
> * Añadir una imagen
> * Probar la oferta

> [!div class="nextstepaction"]
> [Aplicaciones web, móviles y API Apps disponibles para los usuarios de Azure Stack](azure-stack-tutorial-app-service.md)