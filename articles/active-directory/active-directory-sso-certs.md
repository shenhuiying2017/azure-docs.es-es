---
title: "Administración de certificados de federación en Azure AD | Microsoft Docs"
description: "Aprenda a personalizar la fecha de expiración de los certificados de federación y a renovar certificados que expiran pronto."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f516f7f0-b25a-4901-8247-f5964666ce23
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4b859d631617fc3d375711876cf23e201662e43a
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="managing-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Administración de certificados para inicio de sesión único federado en Azure Active Directory
Este artículo aborda información y preguntas comunes relacionadas con los certificados que crea Azure Active Directory para establecer el inicio de sesión único (SSO) federado para las aplicaciones SaaS. Estas aplicaciones se pueden agregar desde la galería de aplicaciones de Azure AD o mediante una plantilla de aplicación ajena a la galería. La aplicación debe configurarse mediante una opción de inicio de sesión único federado.

Este artículo solo es relevante para las aplicaciones que están configuradas para usar **Inicio de sesión único de Azure AD** mediante la **federación de SAML**, como se muestra en el ejemplo siguiente:

![Inicio de sesión único de Azure AD ](./media/active-directory-sso-certs/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Generación automática de certificado para aplicaciones de la galería y ajenas a la misma
Al agregar una nueva aplicación desde la galería y configurar el inicio de sesión basado en SAML, Azure AD genera un certificado con una validez de 3 años para la aplicación. Puede descargar este certificado desde la sección **Certificado de firma SAML**. Para las aplicaciones de la galería, en esta sección puede que se muestre la opción para descargar el **certificado** o los **metadatos de federación** según el requisito de la aplicación.

![Inicio de sesión único de Azure AD ](./media/active-directory-sso-certs/saml_certificate_download.png)

## <a name="how-to-customize-the-expiration-date-for-your-federation-certificate-and-roll-over-the-new-certificate"></a>Personalización de la fecha de expiración para el certificado de la federación y desplazamiento del mouse sobre el nuevo certificado
De forma predeterminada, los certificados se establecen para que expiren después de tres años. Puede elegir otra fecha de expiración para el certificado siguiendo los pasos siguientes: las capturas de pantalla incluidas usan Salesforce como ejemplo, pero estos pasos se pueden aplicar a cualquier aplicación SaaS federada.

1. En el Portal de administración de [Azure Active Directory](https://aad.portal.azure.com), haga clic en la navegación de la **aplicación empresarial** y, a continuación, haga clic en **Nueva aplicación** desde la página **Información general** como se muestra a continuación:

   ![Abra el asistente para configuración de SSO.](./media/active-directory-sso-certs/enterprise_application_new_application.png)

2. Busque la aplicación de la galería y seleccione la aplicación, la cual desea agregar. Si no encuentra la aplicación necesaria, agregue la aplicación mediante la opción **Aplicación situada fuera de la galería**. Esta característica solo está disponible en la SKU **Azure AD Premium (P1 y P2)**.

    ![Inicio de sesión único de Azure AD ](./media/active-directory-sso-certs/add_gallery_application.png)

3. Haga clic en el vínculo **Inicio de sesión único** del panel de navegación izquierdo y cambie el **Modo de inicio de sesión único** a **Inicio de sesión basado en SAML**. De este modo se genera el certificado válido de tres años para la aplicación.

4. Para crear un nuevo certificado, haga clic en el vínculo **Crear nuevo certificado** de la sección **Certificado de firma de SAML**.

    ![Generar un certificado nuevo](./media/active-directory-sso-certs/create_new_certficate.png)

5. El vínculo Crear nuevo certificado abre el control del calendario y ahora establece cualquier fecha y hora hasta tres años. La fecha y hora seleccionadas son la fecha y hora de expiración del nuevo certificado. Haga clic en el botón **Guardar** para guardar el certificado.

    ![Descarga y carga del certificado](./media/active-directory-sso-certs/certifcate_date_selection.PNG)

6. Ahora el nuevo certificado está disponible para su descarga. Haga clic en el vínculo **Certificado** para descargarlo. En este momento, el certificado no está activo. Si desea desplazar el mouse sobre este certificado en ese momento, haga clic en la casilla **Make new certificate** active (Activar nuevo certificado) y haga clic en Guardar. Desde ese momento, Azure AD empieza a usar el nuevo certificado para firmar la respuesta.

7.  Para obtener información sobre cómo cargar el certificado en su aplicación SaaS particular, haga clic en el vínculo Ver instrucciones de configuración de la página.

## <a name="how-to-renew-a-certificate-that-will-soon-expire"></a>Renovación de un certificado que expirará pronto
Lo ideal es que los pasos de renovación que se muestran a continuación no supongan un tiempo de inactividad considerable para los usuarios. Las capturas de pantalla que se usan en esta sección incorporan Salesforce como ejemplo, pero estos pasos se pueden aplicar a cualquier aplicación SaaS federada.

1. En la página **Inicio de sesión único** de la aplicación **Azure Active Directory**, genere el nuevo certificado para su aplicación. Puede hacerlo haciendo clic en el vínculo **Crear nuevo certificado** de la sección **Certificado de firma de SAML**

    ![Generar un certificado nuevo](./media/active-directory-sso-certs/create_new_certficate.png)

2. Seleccione la fecha y hora de expiración deseadas y la hora para el nuevo certificado y haga clic en el botón **Guardar**.

3. Descargue el certificado en la opción Certificado de firma de SAML. Cargue el nuevo certificado en la pantalla de configuración de inicio de sesión único de la aplicación SaaS. A fin de obtener información sobre cómo hacer esto para su aplicación SaaS particular, haga clic en el vínculo **Ver instrucciones de configuración** de la página.
   
4. Para activar el nuevo certificado en Azure AD, active la casilla Make new certificate active (Activar nuevo certificado) y haga clic en el botón **Guardar** en la parte superior de la página. De este modo se desplaza el mouse sobre el nuevo certificado en Azure AD. El estado del certificado cambia de **Nuevo** a **Activo**. Desde ese momento, Azure AD empieza a usar el nuevo certificado para firmar la respuesta. 
   
    ![Generar un certificado nuevo](./media/active-directory-sso-certs/new_certificate_download.png)

## <a name="related-articles"></a>Artículos relacionados
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Cómo depurar el inicio de sesión único basado en SAML en aplicaciones de Azure Active Directory](active-directory-saml-debugging.md)
