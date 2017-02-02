---
title: "Introducción a Azure Active Directory Premium"
description: "Tema que explica cómo suscribirse a la edición Azure Active Directory Premium desde el sitio web de licencias por volumen."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 391764e5-c4eb-4ae6-b8a9-3d09f0de04a6
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/24/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 73cbbce9ae3d727632b8cac2a29c2b1b92332ae7


---
# <a name="getting-started-with-azure-active-directory-premium"></a>Introducción a Azure Active Directory Premium
Para suscribirse a Active Directory Premium, cuenta con varias opciones: 

**Azure u Office 365** : si ya cuenta con una suscripción a Azure u Office 365, puede comprar Active Directory Premium en línea. Para ver los pasos detallados, consulte [How to Purchase Azure Active Directory Premium - Existing Customers](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) (Cómo comprar Azure Active Directory Premium: clientes existentes) o [How to Purchase Azure Active Directory Premium - New Customers](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers) (Cómo comprar Azure Active Directory Premium: nuevos clientes).  

**Enterprise Mobility + Security** : Enterprise Mobility + Security (anteriormente, Enterprise Mobility Suite) es una manera rentable de que las organizaciones usen los siguientes servicios juntos con un solo plan de licencias: Active Directory Premium, Azure Rights Management y Microsoft Intune. Para más información, consulte el sitio web de [Enterprise Mobility + Security](https://www.microsoft.com/en-us/server-cloud/enterprise-mobility/overview.aspx) . Para obtener una evaluación gratuita de 30 días, haga clic [aquí](https://portal.office.com/Signup/Signup.aspx?OfferId=2E63A04D-BE0B-4A0F-A8CF-407C1C299221&dl=EMS&ali=1#0).

**Licencias por volumen de Microsoft**: Azure Active Directory Premium está disponible a través de un [contrato Enterprise de Microsoft](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 licencias o más) o el programa[Open License](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (entre 5 y 250 licencias).

En este tema se muestra cómo empezar a trabajar con Azure Active Directory Premium cuando se ha adquirido a través del programa de licencias por volumen. Si aún no conoce las diferentes ediciones de Azure Active Directory, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).  

> [!NOTE]
> Las ediciones Premium y Básico de Azure Active Directory están disponibles para los clientes de China que utilizan la instancia de Azure Active Directory en todo el mundo. Las ediciones Premium y Básico de Azure Active Directory no se admiten actualmente en el servicio de Microsoft Azure operado por 21Vianet en China. Para obtener más información, póngase en contacto con nosotros en el [foro de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).
> 
> 

## <a name="step-1-sign-up-for-active-directory-premium"></a>Paso 1: Suscripción de Azure Active Directory Premium y Básico
Para suscribirse, consulte [How to purchase through Volume Licensing](http://www.microsoft.com/en-us/licensing/how-to-buy/how-to-buy.aspx)(Cómo comprar mediante Licencias por volumen).

## <a name="step-2-activate-your-license-plan"></a>Paso 2: Activación del plan de licencias
¿Es esta su primera compra de plan de licencias mediante el programa Licencias por Volumen para empresas de Microsoft?
En ese caso, obtendrá un mensaje de confirmación cuando se haya completado la compra.
Necesita este correo electrónico para activar el primer plan de licencias.

En las compras posteriores para este directorio, las licencias se activarán automáticamente en el mismo directorio.

**Para activar el plan de licencias, realice uno de los siguientes pasos:**

1. Para iniciar la activación, haga clic en **Inicio de sesión** o en **Suscribirse**.
   
    ![Iniciar sesión][1]

    - Si tiene un inquilino existente, haga clic en **Inicio de sesión** para iniciar sesión con la cuenta de administrador existente. Debe iniciar sesión con las credenciales de administrador global desde el directorio donde se deben activar las licencias.

    - Si desea crear un nuevo inquilino de Azure Active Directory para usarlo con el plan de licencias, haga clic en **Suscribirse** para abrir el cuadro de diálogo **Crear perfil de cuenta**.

        ![Crear perfil de cuenta][2]

Cuando haya terminado, aparecerá el cuadro de diálogo siguiente como confirmación para la activación del plan de licencias para el inquilino.

![Confirmación][3]

## <a name="step-3-activate-your-azure-active-directory-access"></a>Paso 3: Activación de acceso de Azure Active Directory
Si ha usado Microsoft Azure antes, puede continuar con el [paso 4](#step-4-assign-license-to-user-accounts). 

Cuando las licencias se aprovisionan en su directorio, se le envía un **correo electrónico de bienvenida** . El correo electrónico confirma que puede empezar a administrar las licencias y características de Azure Active Directory Premium o Enterprise Mobility Suite. 

Si intenta activar el acceso a Azure Active Directory antes de recibir el correo electrónico de bienvenida, verá el siguiente mensaje de error. 

![Acceso no disponible][9]

Vuelva a intentarlo en unos minutos una vez que haya recibido el correo electrónico.

Los nuevos administradores de la suscripción también pueden activar su acceso al Portal de Azure clásico a través de este vínculo.

**Para activar el acceso a Azure Active Directory, realice los pasos siguientes:**

1. En el **correo electrónico de bienvenida**, haga clic en **Inicio de sesión**. 
   
    ![correo electrónico de bienvenida][4]
2. Cuando haya iniciado sesión correctamente, debe completar una segunda fase de autenticación por medio de una confirmación por móvil:
   
    ![Confirmación por móvil][5]

La activación puede tardar unos minutos. Una vez que el acceso esté activo, la barra marrón desaparecerá y podrá hacer clic en **Portal**.

![Espere mientras configuramos][6]

En este caso, el acceso a Azure se limita a Azure Active Directory.

![Funcionalidades de Azure][7]

Es posible que ya haya obtenido acceso a Azure previamente; además, puede actualizar su acceso a Azure Active Directory al acceso de Azure completo mediante la activación de suscripciones de Azure adicionales. En estos casos, el Portal de Azure clásico tiene más funcionalidades.

![Funcionalidades de Azure][8]

## <a name="step-4-assign-license-to-user-accounts"></a>Paso 4: Asignación de licencias a cuentas de usuario
Antes de empezar a utilizar el plan que compró, debe asignar manualmente licencias a cuentas de usuario dentro de su organización para que puedan usar las características enriquecidas que se proporcionan con Premium. Utilice los pasos siguientes para asignar licencias a los usuarios para que puedan usar las características de Azure Active Directory Premium.

**Para asignar licencias a usuarios, lleve a cabo los siguientes pasos:**

1. Inicie sesión en el Portal de Azure clásico como el administrador global del directorio que desea personalizar.
2. Haga clic en **Active Directory**y, a continuación, seleccione el directorio donde desee asignar licencias.
3. Seleccione la pestaña **Licencias**, seleccione **Active Directory Premium** o **Enterprise Mobility Suite** y, a continuación, haga clic en **Asignar**.
   
    ![Planes de licencias][10]
4. En el cuadro de diálogo, seleccione los usuarios a los que desee asignar las licencias y, a continuación, haga clic en el icono de marca de verificación para guardar los cambios.
   
    ![Asignación de licencias][11]

### <a name="license-restrictions"></a>Restricciones de licencia
Algunos planes de licencias son subconjuntos o superconjuntos de otros planes de licencias. Normalmente, un usuario no se puede asignar a un plan de licencias ya asignado a ellos. Si tiene pensado asignar un plan de licencias que es un superconjunto, deberá quitar primero el plan de licencias de subconjunto.

### <a name="license-requirements"></a>Requisitos de licencia
Al asignar una licencia a un usuario, puede especificar una ubicación de uso principal en las propiedades de su cuenta. Si no se especifica una ubicación de uso, la ubicación del inquilino se asigna automáticamente al usuario.

![Ubicación del usuario][12]

La disponibilidad de servicios y características de un servicio en la nube de Microsoft varía según el país o la región. Un servicio, como Voz sobre IP (VoIP), puede estar disponible en un país o región y no disponible en otro. Las características dentro de un servicio pueden estar restringidas por motivos legales en determinados países o regiones. Para ver si un servicio o una característica está disponible con o sin restricciones, busque su país o región en el sitio de restricciones de licencia de un servicio.

## <a name="whats-next"></a>Pasos siguientes
* [Incorporación de la marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md)
* [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md)

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



<!--HONumber=Dec16_HO2-->


