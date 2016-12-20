---
title: Proteger los recursos de nube con Azure MFA y AD FS
description: "En esta página de Azure Multi-Factor Authentication se describe cómo empezar a trabajar con Azure MFA y AD FS 2.0 en la nube."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 0927fc67-8090-4fdd-913a-b3cfed3fbe77
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/14/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 0a9ab0aca1a77245f360d0d8976aa9b8f59f15a0


---
# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>Protección de recursos en la nube con Azure Multi-Factor Authentication y AD FS
Si su organización está federada con Azure Active Directory, use Azure Multi-Factor Authentication o Servicios de federación de Active Directory para proteger los recursos a los que se accede mediante Azure AD. Utilice los siguientes procedimientos para proteger recursos de Azure Active Directory mediante Azure Multi-Factor Authentication o Servicios de federación de Active Directory.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Protección de los recursos de Azure AD mediante AD FS
Para proteger los recursos en la nube, habilite una cuenta para los usuarios y configure una regla de notificaciones. Siga este procedimiento para realizar los pasos:

1. Utilice los pasos descritos en [Activación de autenticación multifactor para usuarios](multi-factor-authentication-get-started-cloud.md#turn-on-two-step-verification-for-users) para habilitar una cuenta.
2. Inicie la consola de administración de AD FS.
   ![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)
3. Vaya a **Relaciones de confianza para usuario autenticado** y haga clic con el botón derecho en las relaciones de confianza para usuario autenticado. Seleccione **Editar reglas de notificación...**
4. Haga clic en **Agregar regla...**
5. En la lista desplegable, seleccione **Enviar notificaciones con una regla personalizada** y haga clic en **Siguiente**.
6. Escriba un nombre para la regla de notificación.
7. En Regla personalizada: agregue el siguiente texto:

    ```
    => issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");
    ```

    Notificación correspondiente.

    ```
    <saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
    <saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
    </saml:Attribute>
    ```
8. Haga clic en **Aceptar** y, a continuación, en **Finalizar**. Cierre la consola de administración de AD FS.

Los usuarios ya pueden completar el inicio de sesión mediante el método local (como una tarjeta inteligente).

## <a name="trusted-ips-for-federated-users"></a>Direcciones IP de confianza para usuarios federados
Las direcciones IP de confianza permiten a los administradores omitir la verificación en dos pasos para una dirección IP específica o para usuarios federados que tienen solicitudes que se originan dentro de su propia intranet. En las secciones siguientes se describe cómo configurar IP fiables de Azure Multi-Factor Authentication con usuarios federados y omitir la verificación en dos pasos cuando una solicitud se origina en una intranet de usuarios federados. Esto se consigue configurando AD FS para usar un paso a través o filtrar una plantilla de notificación entrante con el tipo de notificación dentro de la red corporativa.

En este ejemplo se utiliza Office 365 para las relaciones de confianza para usuario autenticado .

### <a name="configure-the-ad-fs-claims-rules"></a>Configurar las reglas de notificaciones de AD FS
Utilice el procedimiento siguiente para configurar las notificaciones de AD FS. Se crearán dos reglas de notificaciones, una para el tipo de notificación dentro de la red corporativa y otra adicional para mantener a nuestros usuarios con la sesión iniciada.

1. Abra Administración de AD FS.
2. A la izquierda, seleccione **Relaciones de confianza para usuario autenticado**.
3. Haga clic con el botón derecho en **Plataforma de identidad de Microsoft Office 365** y seleccione **Editar reglas de notificaciones…**
   ![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. En Reglas de transformación de emisión, haga clic en **Agregar regla**.
   ![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. En el Asistente para agregar regla de notificaciones de transformación, seleccione **Pasar por una notificación entrante o filtrarla** en la lista desplegable y haga clic en **Siguiente**.
   ![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. En el cuadro situado junto al nombre de la regla de notificación, asigne un nombre a la regla. Por ejemplo: InsideCorpNet.
7. En la lista desplegable, junto a Tipo de notificación entrante, seleccione **Dentro de la red corporativa**.
   ![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Haga clic en **Finalizar**
9. En Reglas de transformación de emisión, haga clic en **Agregar regla**.
10. En el Asistente para agregar regla de notificaciones de transformación, seleccione **Enviar notificaciones mediante regla personalizada** en la lista desplegable y haga clic en **Siguiente**.
11. En el cuadro situado bajo el nombre de la regla de notificación: escriba *Mantener a los usuarios con la sesión iniciada*.
12. En el cuadro de regla personalizada, escriba:

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Haga clic en **Finalizar**
14. Haga clic en **Apply**.
15. Haga clic en **Aceptar**.
16. Cierre Administración de AD FS.

### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Configuración de las IP de confianza de Azure Multi-Factor Authentication con usuarios federados
Ahora que las notificaciones están listas, podemos configurar direcciones IP de confianza.

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. En la parte izquierda, haga clic en **Active Directory**.
3. En Directorio, seleccione el directorio en el que desea configurar las direcciones IP de confianza.
4. En el directorio que ha seleccionado, haga clic en **Configurar**.
5. En la sección de Multi-Factor Authentication, haga clic en **Administrar configuración del servicio**.
6. En la página Configuración del servicio, en IP de confianza, seleccione **Omitir autenticación multifactor para solicitudes de usuarios federados en mi intranet**.
   ![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. Haga clic en **guardar**.
8. Una vez que se han aplicado las actualizaciones, haga clic en **Cerrar**.

¡Ya está! En este punto, los usuarios federados de Office 365 solo deberán usar MFA cuando una notificación se origine fuera de la intranet corporativa.



<!--HONumber=Dec16_HO2-->


