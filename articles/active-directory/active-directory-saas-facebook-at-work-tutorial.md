---
title: "Tutorial: Integración de Azure Active Directory con Workplace by Facebook | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Workplace by Facebook."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: efd29518fc207eb3e554f1389a6109ae4f1f3205
ms.openlocfilehash: 252e7b3c198a3433ecd21aef2222b2ca14b7ac04


---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Tutorial: integración de Azure Active Directory con Workplace by Facebook
El objetivo de este tutorial es mostrar cómo integrar Workplace by Facebook con Azure Active Directory (Azure AD).

Integrar Workplace by Facebook con Azure AD le proporciona las siguientes ventajas: 

* Puede controlar en Azure AD quién tiene acceso a Workplace by Facebook 
* Puede aprovisionar automáticamente cuentas para los usuarios a los que concedió acceso a Workplace by Facebook
* Puede permitir que los usuarios inicien sesión automáticamente en Workplace by Facebook (inicio de sesión único) con sus cuentas de Azure AD
* Puede administrar sus cuentas en una ubicación central. 

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con CS Stars, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Workplace by Facebook con inicio de sesión único habilitado

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Incorporación de Workplace by Facebook desde la galería
Para configurar la integración de Workplace by Facebook en Azure AD, deberá agregar Workplace by Facebook desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Workplace by Facebook desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Workplace by Facebook**.
7. En el panel de resultados, seleccione **Workplace by Facebook** y haga clic en **Completar** para agregar la aplicación.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Workplace by Facebook con su cuenta de Azure AD a través de la federación basada en el protocolo SAML.

**Para configurar el inicio de sesión único de Azure AD con Workplace by Facebook, siga estos pasos:**

1. Después de agregar Workplace by Facebook al Portal de Azure clásico, haga clic en **Configurar inicio de sesión único**.
2. En la pantalla **Configurar dirección URL de la aplicación**, escriba la dirección URL donde los usuarios iniciarán sesión en su aplicación Workplace by Facebook. Se trata de la dirección URL del inquilino de Workplace by Facebook (ejemplo: https://example.facebook.com/). Una vez que termine, haga clic en **Siguiente**.
3. En otra ventana del explorador web, inicie sesión en el sitio de empresa de Workplace by Facebook como administrador.
4. Siga las instrucciones de la dirección URL siguiente para configurar Workplace by Facebook para usar Azure AD como proveedor de identidades: [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)
5. Una vez completado, vuelva a las ventanas del explorador que muestran el Portal de Azure clásico, haga clic en la casilla para confirmar que completó el procedimiento y, después, en **Siguiente** y **Completar**.


## <a name="automatically-provisioning-users-to-workplace-by-facebook"></a>Aprovisionamiento automático de usuarios en Workplace by Facebook
Azure AD admite la capacidad de sincronizar automáticamente los detalles de la cuenta de usuarios asignados a Workplace by Facebook. Esta sincronización automática permite a Workplace by Facebook obtener los datos que necesita para autorizar a los usuarios a acceder, antes de que intenten iniciar sesión por primera vez. También desaprovisiona usuarios de Workplace by Facebook cuando se revoque el acceso en Azure AD.

El aprovisionamiento automático puede configurarse haciendo clic en **Configurar aprovisionamiento de cuentas** en la ventana del Portal de Azure clásico.

Para obtener más detalles sobre cómo configurar el aprovisionamiento automático, consulte [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

## <a name="assigning-users-to-workplace-by-facebook"></a>Asignación de usuarios a Workplace by Facebook
Para que los usuarios de AAD aprovisionados puedan ver Workplace by Facebook en su panel de acceso, deben tener acceso asignado en el Portal de Azure clásico.

**Para asignar usuarios a Workplace by Facebook:**

1. En la página de inicio de Workplace by Facebook en el Portal de Azure clásico, haga clic en **Asignar cuentas**.
2. En el menú **Mostrar**, seleccione si desea asignar un usuario o un grupo a Workplace by Facebook y haga clic en el botón de marca de verificación.
3. En la lista resultante, seleccione los usuarios o el grupo al que desea asignar Workplace by Facebook.
4. En el pie de página, haga clic en el botón **Asignar** .

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png







<!--HONumber=Nov16_HO5-->


