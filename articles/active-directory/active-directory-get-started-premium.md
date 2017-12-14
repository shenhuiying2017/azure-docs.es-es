---
title: "Suscripción a Azure Active Directory Premium | Microsoft Docs"
description: "En este artículo se explica cómo suscribirse a la edición Azure Active Directory Premium"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
ms.assetid: 391764e5-c4eb-4ae6-b8a9-3d09f0de04a6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: it-pro;
ms.openlocfilehash: e7aa1757ec1720f15ac9d1f00129fd38f86f0668
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="quickstart-sign-up-for-azure-active-directory-premium"></a>Inicio rápido: Suscripción a Azure Active Directory Premium
Para empezar a trabajar con Azure Active Directory (Azure AD) Premium, puede comprar licencias y asociarlas a su suscripción de Azure. Si crea una nueva suscripción de Azure, debe activar el plan de licencias y el acceso del servicio de Azure AD como se describe en las secciones siguientes. 

## <a name="sign-up-for-active-directory-premium"></a>Suscripción a Active Directory Premium
Para suscribirse a Active Directory Premium, cuenta con varias opciones: 
* Use su suscripción a Office 365 o Azure
* Use un plan de licencias de Enterprise Mobility + Security
* Use un plan de Licencias por volumen de Microsoft

### <a name="azure-or-office-365"></a>Azure u Office 365 
Si ya cuenta con una suscripción a Azure u Office 365, puede comprar Azure Active Directory Premium en línea. 

Para ver los pasos detallados, consulte [How to Purchase Azure Active Directory Premium - Existing Customers](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) (Cómo comprar Azure Active Directory Premium: clientes existentes) o [How to Purchase Azure Active Directory Premium - New Customers](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers) (Cómo comprar Azure Active Directory Premium: nuevos clientes).  

### <a name="enterprise-mobility--security"></a>Enterprise Mobility + Security
Enterprise Mobility + Security (EMS) es una manera rentable de que las organizaciones usen los siguientes servicios juntos con un solo plan de licencias: Azure Active Directory Premium, Azure Information Protection y Microsoft Intune. Puede obtener más información sobre EMS en el [sitio web de Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security) y más información sobre los tipos de licencias de EMS que se pueden adquirir en la página de [opciones de precios de Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing).  

Puede empezar a trabajar con Azure AD a través de licencias de EMS mediante una de las siguientes opciones de licencias:

- Pruebe EMS con una [suscripción de prueba de Enterprise Mobility + Security E5](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1)
- Adquiera [licencias E5 de Enterprise Mobility + Security](https://signup.microsoft.com/Signup?OfferId=e6de2192-536a-4dc3-afdc-9e2602b6c790&ali=1)
- Adquiera [licencias E3 de Enterprise Mobility + Security](https://signup.microsoft.com/Signup?OfferId=4BBA281F-95E8-4136-8B0F-037D6062F54C&ali=1)

### <a name="microsoft-volume-licensing"></a>Licencias por volumen de Microsoft
Azure Active Directory Premium está disponible a través de un [contrato Enterprise de Microsoft](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 licencias o más) o el programa [Open License](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (entre 5 y 250 licencias).

Puede conocer más información sobre las opciones de compra de licencias por volumen en la página [How to purchase through Volume Licensing](https://www.microsoft.com/licensing/how-to-buy/how-to-buy.aspx) (Cómo comprar mediante Licencias por volumen).

> [!NOTE]
> Las ediciones Premium y Básico de Azure Active Directory están disponibles para los clientes de China que utilizan la instancia de Azure Active Directory en todo el mundo. Las ediciones Premium y Básico de Azure Active Directory no se admiten actualmente en el servicio de Microsoft Azure operado por 21Vianet en China. Para obtener más información, póngase en contacto con nosotros en el [foro de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

Si anteriormente ha adquirido y activado licencias de Azure AD para la misma suscripción de Azure que usó en los pasos anteriores, estas se activarán automáticamente en el mismo directorio. En otro caso, continúe con los pasos descritos en el resto de este artículo.

## <a name="activate-your-license-plan"></a>Activación del plan de licencias
¿Es este el primer plan de licencias de Azure AD que ha comprado en Microsoft? Si es así, se le enviará un correo electrónico de confirmación cuando haya completado la compra. Necesita este correo electrónico para activar el primer plan de licencias.

**Para activar el plan de licencias, realice uno de los siguientes pasos:**

1. Para iniciar la activación, haga clic en **Inicio de sesión** o en **Suscribirse**.
   
    ![Iniciar sesión][1]

    - Si tiene un inquilino existente, haga clic en **Inicio de sesión** para iniciar sesión con la cuenta de administrador existente. Inicie sesión con las credenciales de administrador global desde el inquilino donde se deben activar las licencias.

    - Si desea crear un nuevo inquilino de Azure AD para usarlo con el plan de licencias, haga clic en **Suscribirse** para abrir el cuadro de diálogo **Crear perfil de cuenta**.

        ![Crear perfil de cuenta][2]

Cuando haya terminado, aparecerá el cuadro de diálogo siguiente como confirmación para la activación del plan de licencias para el inquilino:

![Confirmación][3]

## <a name="activate-your-azure-active-directory-access"></a>Activación de acceso de Azure Active Directory
Si va a agregar nuevas licencias de Azure AD Premium a una suscripción existente, ya se debe activar el acceso a Azure AD. En caso contrario, debe activar el acceso a Azure AD después de recibir el **mensaje de correo electrónico de bienvenida**.  

Cuando las licencias que ha adquirido se hayan aprovisionado en su directorio, se le enviará un **mensaje de correo electrónico de bienvenida** . El correo electrónico confirma que puede empezar a administrar las licencias y características de Azure Active Directory Premium o Enterprise Mobility + Security. 

> [!TIP]
> No podrá acceder a Azure AD en el nuevo inquilino hasta que active el acceso al directorio de Azure AD mediante el correo electrónico de bienvenida que se envía automáticamente al completarse el proceso de aprovisionamiento de licencia. 

**Para activar el acceso a Azure AD, realice los pasos siguientes:**

1. En el **correo electrónico de bienvenida**, haga clic en **Inicio de sesión**. 
   
    ![correo electrónico de bienvenida][4]
2. Después de iniciar sesión correctamente, también deberá realizar una autenticación de segundo factor usando un dispositivo móvil:
   
    ![Confirmación por móvil][5]

La activación solo tardará unos minutos y, a continuación, tendrá acceso a la administración de Azure AD. 

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido a suscribirse a Azure AD Premium y a activar el acceso de Azure Active Directory. 

Si ya tiene una suscripción de Azure, puede usar el siguiente vínculo para iniciar una prueba o adquirir licencias de Azure AD Premium desde Azure Portal.

> [!div class="nextstepaction"]
> [Activar las licencias de Azure AD Premium](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/TryBuyProductBlade) 

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png