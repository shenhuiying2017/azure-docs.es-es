---
title: Uso del portal de Azure Stack | Microsoft Docs
description: "Obtenga información sobre cómo acceder al portal del usuario y usar el mismo en Azure Stack."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 5aa00123-5b87-45e0-a671-4165e66bfbc6
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 63c270affca31d3db7e03116f5e287d8569b0dae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-stack-portal"></a>Uso del portal de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Como consumidor de servicios de Azure Stack, puede usar el portal de Azure Stack para suscribirse a ofertas públicas y utilizar los servicios que están disponibles a través de dichas ofertas. Si ha utilizado Azure Portal antes, ya está familiarizado con la interfaz de usuario.

## <a name="access-the-portal"></a>Acceso al portal

El operador de Azure Stack (un proveedor de servicios o un administrador de su organización), le permitirá conocer la dirección URL correcta para acceder al portal. 

- En un sistema integrado, la dirección URL varía en función de la región y el nombre de dominio externo del operador, y tendrá el formato https://portal.&lt;*región*&gt;.&lt;*FQDN*&gt;.
- Si usa Azure Stack Development Kit, la dirección del portal es https://portal.local.azurestack.external.

![Captura de pantalla del portal del usuario de Azure Stack](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Personalización del panel

El panel contiene un conjunto de iconos predeterminados. Puede hacer clic en **Editar panel** para modificar el panel predeterminado o en **Nuevo panel** para agregar paneles personalizados. Puede agregar fácilmente iconos al panel. Por ejemplo, puede hacer clic en **Nuevo**, hacer clic con el botón derecho en **Proceso** y luego hacer clic en **Anclar al panel**.

## <a name="create-subscription-and-browse-available-resources"></a>Creación de una suscripción y examen de los recursos disponibles
 
Si todavía no tiene una suscripción, lo primero que debe hacer es suscribirse a una oferta. Después de eso, puede examinar los recursos que están a su disposición. Para examinar y crear recursos, realice cualquiera de las siguientes acciones:

- Haga clic en el icono **Marketplace** del panel. 
- En el icono **Todos los recursos**, haga clic en **Crear recursos**.
- En el panel de navegación de la izquierda, haga clic en **Nuevo**.

## <a name="learn-how-to-use-available-services"></a>Aprender a usar los servicios disponibles

Si necesita instrucciones sobre cómo usar los servicios disponibles, puede haber diferentes opciones disponibles.

- Su organización o proveedor de servicios puede proporcionar su propia documentación. Esto es especialmente cierto si ofrecen aplicaciones o servicios personalizados.
- Las aplicaciones de terceros tienen su propia documentación.
- Para servicios coherentes con Azure, es muy recomendable que primero revise la documentación de Azure Stack. Para acceder a la documentación de usuario de Azure Stack, haga clic en el icono Ayuda y luego haga clic en **Ayuda y soporte técnico**.
 
    ![Captura de pantalla de la Ayuda y la opción de soporte técnico en la IU](media/azure-stack-use-portal/HelpAndSupport.png)

    En concreto, se recomienda que revise los artículos siguientes para empezar a trabajar:

    - [Key considerations: Using services or building apps for Azure Stack](azure-stack-considerations.md) (Consideraciones clave: uso de servicios o compilación de aplicaciones para Azure Stack)
    - En la sección “Uso de servicios” de la documentación, podrá ver una enumeración de cada servicio coherente con Azure. Hay un tema sobre "consideraciones" para cada servicio, donde se describen las diferencias entre el servicio que se ofrece en Azure y el mismo servicio que se ofrece en Azure Stack. Para un ejemplo, vea [VM considerations](azure-stack-vm-considerations.md) (Consideraciones para VM). Puede haber otra información en la sección “Uso de servicios” que sea única para Azure Stack. 
     
      Puede usar la documentación de Azure como referencia general para un servicio, pero debe tener en cuenta estas diferencias. Sepa que los vínculos de la documentación del icono **Tutoriales de inicio rápido** apuntan a la documentación de Azure.

## <a name="get-support"></a>Obtención de soporte técnico

Si necesita ayuda adicional, póngase en contacto con su organización o proveedor de servicios. 

Si usa Azure Stack Development Kit, el [foro de Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) es el único método de soporte técnico.

## <a name="next-steps"></a>Pasos siguientes

[Key considerations: Using services or building apps for Azure Stack](azure-stack-considerations.md) (Consideraciones clave: uso de servicios o compilación de aplicaciones para Azure Stack)
