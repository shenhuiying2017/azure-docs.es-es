---
title: Uso del portal de Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo acceder al portal del usuario y usar el mismo en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: mabrigg
ms.reviewer: efemmano
ms.openlocfilehash: 2ebafc4249f8455bdbe45a07a5bf88aa8984d67a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358972"
---
# <a name="using-the-azure-stack-portal"></a>Uso del portal de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede usar el portal de Azure Stack para suscribirse a ofertas públicas y utilizar los servicios que proporcionan dichas ofertas. Si ha utilizado la versión general de Azure Portal, ya está familiarizado con la manera en que funciona el sitio.

## <a name="access-the-portal"></a>Acceso al portal

El operador de Azure Stack (un proveedor de servicios o un administrador de su organización), le permitirá conocer la dirección URL correcta para acceder al portal.

- En un sistema integrado, la dirección URL varía en función de la región y el nombre de dominio externo del operador y tendrá el formato https://portal.&lt;*región*&gt;.&lt;*FQDN*&gt;.
- Si usa el Kit de desarrollo de Azure Stack, la dirección del portal es https://portal.local.azurestack.external.

![Captura de pantalla del portal del usuario de Azure Stack](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Personalización del panel

El panel contiene un conjunto predeterminado de iconos. Puede hacer clic en **Edit dashboard** (Editar panel) para modificar el panel predeterminado o en **New dashboard** (Nuevo panel) para crear un panel personalizado. Puede personalizar fácilmente un panel agregando o quitando iconos. Por ejemplo, para agregar un icono de Compute, haga clic en **New** (Nuevo). Haga clic con el botón derecho en **Compute** y, luego, haga clic en **Pin to dashboard** (Anclar al panel).

## <a name="create-subscription-and-browse-available-resources"></a>Creación de una suscripción y examen de los recursos disponibles
 
Si todavía no tiene una suscripción, lo primero que debe hacer es suscribirse a una oferta. Luego, puede examinar los recursos disponibles. Para examinar y crear recursos, use cualquiera de las siguientes estrategias:

- Haga clic en el icono **Marketplace** del panel.
- En el icono **Todos los recursos**, haga clic en **Crear recursos**.
- En el panel de navegación de la izquierda, haga clic en **Nuevo**.

## <a name="learn-how-to-use-available-services"></a>Aprender a usar los servicios disponibles

Si necesita instrucciones sobre cómo usar los servicios disponibles, puede haber diferentes opciones disponibles.

- Su organización o un proveedor de servicios puede proporcionar su propia documentación, que suele ser el caso si ofrecen servicios o aplicaciones personalizados.
- Las aplicaciones de terceros tienen su propia documentación.
- Para servicios coherentes con Azure, es muy recomendable que primero revise la documentación de Azure Stack. Para acceder a la documentación de usuario de Azure Stack, haga clic en el icono Ayuda y luego haga clic en **Ayuda y soporte técnico**.
 
    ![Captura de pantalla de la Ayuda y la opción de soporte técnico en la IU](media/azure-stack-use-portal/HelpAndSupport.png)

    En concreto, se recomienda que revise los artículos siguientes para empezar a trabajar:

    - [Key considerations: Using services or building apps for Azure Stack](azure-stack-considerations.md) (Consideraciones clave: uso de servicios o compilación de aplicaciones para Azure Stack)
    - En la sección **Uso de servicios** de la documentación, hay un artículo de consideraciones para cada servicio. En la página de consideraciones se describen las diferencias entre el servicio que se ofrece en Azure y el mismo servicio pero ofrecido en Azure Stack. Para un ejemplo, vea [VM considerations](azure-stack-vm-considerations.md) (Consideraciones para VM). Puede haber otra información en la sección **Uso de servicios** que sea única para Azure Stack.
     
      Puede usar la documentación de Azure como referencia general para un servicio, pero debe tener en cuenta estas diferencias. Sepa que los vínculos de la documentación del icono **Tutoriales de inicio rápido** apuntan a la documentación de Azure.

## <a name="get-support"></a>Obtención de soporte técnico

Si necesita ayuda, póngase en contacto con su organización o proveedor de servicios.

Si usa el Kit de desarrollo de Azure Stack, el [foro de Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) es el único origen de soporte técnico.

## <a name="next-steps"></a>Pasos siguientes

[Key considerations: Using services or building apps for Azure Stack](azure-stack-considerations.md) (Consideraciones clave: uso de servicios o compilación de aplicaciones para Azure Stack)
