---
title: "Personalización de la página de inicio de sesión en la versión preliminar de Azure Active Directory | Microsoft Docs"
description: "Aprenda a agregar una personalización de marca de empresa a la página de inicio de sesión de Azure"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 48f85fd7e50c1cb3ece7f8b75779fe7cde92c7da


---
# <a name="add-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Adición de personalización de marca de empresa a la página de inicio de sesión en la versión preliminar de Azure Active Directory
Para evitar confusiones, muchas empresas quieren aplicar un aspecto coherente en todos los sitios web y servicios que administran. La versión preliminar de Azure Active Directory ofrece esta funcionalidad, ya que permite personalizar la apariencia de la página de inicio de sesión con esquemas de color personalizados y el logotipo de la empresa. [¿Qué es la versión preliminar?](active-directory-preview-explainer.md)  Se trata de la página que aparece al iniciar sesión en Office 365 u otras aplicaciones web que estén utilizando Azure AD como proveedor de identidades. Interactuará con esta página para especificar las credenciales.

Si desea mostrar en su página la marca y los colores de su empresa, además de otros elementos personalizables, vea las imágenes siguientes para entender la diferencia entre las dos experiencias.

La siguiente captura de pantalla muestra un ejemplo de la página de inicio de sesión de Office 365 en un equipo de escritorio **antes** de una personalización:

![Página de inicio de sesión de Office 365 antes de la personalización](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

La siguiente captura de pantalla muestra un ejemplo de la página de inicio de sesión de Office 365 en un equipo de escritorio **después** de una personalización:

![Página de inicio de sesión de Office 365 después de la personalización](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)

## <a name="customizing-the-sign-in-page"></a>Personalización de la página de inicio de sesión
Normalmente, si necesita acceso basado en explorador a las aplicaciones y servicios en la nube a los que está suscrita su organización, utiliza la página de inicio de sesión.

Si ha aplicado cambios a la página de inicio de sesión, puede tardar hasta una hora para que aparezcan.

Las páginas de inicio de sesión con marca solamente aparecen cuando se visita un servicio con una dirección URL específica del inquilino, como https://outlook.com/**contoso**.com o https://mail.**contoso**.com.

Si visita un servicio con direcciones URL que no son específicas de un inquilino (por ejemplo, https://mail.office365.com), aparece una página de inicio de sesión sin marca. En este caso, la marca solamente aparecerá una vez que haya especificado el identificador de usuario o haya seleccionado un icono de usuario.

> [!NOTE]
> * El nombre de usuario debe aparecer como "activo" en la parte **Dominios** de Azure Portal donde ha configurado la personalización de marca. Para más información, consulte [cómo agregar nombres de dominio personalizados](active-directory-domains-add-azure-portal.md).
> * La personalización de marca de la página de inicio de sesión no se traslada a la página de inicio de sesión de cliente de Microsoft. Si inicia sesión con una cuenta de Microsoft, puede ver una lista de iconos de usuario con la personalización de marca presentada por Azure AD, pero la personalización de marca de su organización no se aplica a la página de inicio de sesión de la cuenta de Microsoft.
>
>

En la página de inicio de sesión, la casilla **Mantener la sesión iniciada** permite que el usuario se mantenga conectado después de cerrar y abrir de nuevo el explorador.

   ![Mantener la sesión iniciada](./media/active-directory-branding-custom-signon-azure-portal/01.png)

No afecta a la duración de la sesión. Puede ocultar la casilla en la página de inicio de sesión de Azure Active Directory.
Puede decidir si se muestra la casilla o no mediante la configuración de **Se deshabilitó la opción Mantener la sesión iniciada**.

   ![Mantener la sesión iniciada](./media/active-directory-branding-custom-signon-azure-portal/02.png)

Para ocultar la casilla, seleccione **Sí** en esta opción.

> [!NOTE]
> Algunas características de SharePoint Online y Office 2010 dependen de que los usuarios puedan activar esta casilla. Si configura esta opción en oculto, es posible que los usuarios reciban solicitudes adicionales e inesperadas de inicio de sesión.
>
>

**Para agregar la información de marca de empresa a su directorio:**

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Más servicios**, escriba **Usuarios y grupos** en el cuadro de texto y presione **Entrar**.

   ![Apertura de Administración de usuarios](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)
3. En la hoja **Usuarios y grupos**, seleccione **Personalización de marca de empresa**.
4. En la hoja **Usuarios y grupos - Personalización de marca de empresa**, seleccione el comando **Editar**.

    ![Edición de la personalización de marca](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)
5. Modifique los elementos que desea personalizar. Todos los elementos son opcionales.
6. Haga clic en **Save**.

Puede transcurrir hasta una hora para que los cambios efectuados se muestren en la personalización de marca de la página de inicio de sesión.

## <a name="next-steps"></a>Pasos siguientes
[Agregar información de marca de empresa específica de un idioma](active-directory-branding-localize-azure-portal.md)



<!--HONumber=Feb17_HO2-->


