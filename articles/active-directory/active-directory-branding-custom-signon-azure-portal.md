<properties
pageTitle="Personalización de la página de inicio de sesión en la versión preliminar de Azure Active Directory | Microsoft Azure"
description="Aprenda a agregar una personalización de marca de empresa a la página de inicio de sesión de Azure"
services="active-directory"
documentationCenter=""
authors="curtand"
manager="femila"
editor=""/>

<tags
ms.service="active-directory"
ms.workload="identity"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="09/12/2016"
ms.author="curtand"/>

# Adición de personalización de marca de empresa a la página de inicio de sesión en la versión preliminar de Azure Active Directory

Para evitar confusiones, muchas empresas quieren aplicar un aspecto coherente en todos los sitios web y servicios que administran. La versión preliminar de Azure Active Directory ofrece esta funcionalidad, ya que permite personalizar la apariencia de la página de inicio de sesión con esquemas de color personalizados y el logotipo de la empresa. [¿Qué hay en la versión preliminar?](active-directory-preview-explainer.md) La página de inicio de sesión es la página que aparece al iniciar sesión en Office 365 u otras aplicaciones web que estén usando Azure AD como proveedor de identidades. Interactuará con esta página para especificar las credenciales.

Si desea mostrar en su página la marca y los colores de su empresa, además de otros elementos personalizables, vea las imágenes siguientes para entender la diferencia entre las dos experiencias.

La siguiente captura de pantalla muestra un ejemplo de la página de inicio de sesión de Office 365 en un equipo de escritorio **antes** de una personalización:

![Página de inicio de sesión de Office 365 antes de la personalización](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

La siguiente captura de pantalla muestra un ejemplo de la página de inicio de sesión de Office 365 en un equipo de escritorio **después** de una personalización:

![Página de inicio de sesión de Office 365 después de la personalización](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)


## Personalización de la página de inicio de sesión

Normalmente, si necesita acceso basado en explorador a las aplicaciones y servicios en la nube a los que está suscrita su organización, utiliza la página de inicio de sesión.

Si ha aplicado cambios a la página de inicio de sesión, puede tardar hasta una hora para que aparezcan.

Una página de inicio de sesión de marca solo aparece cuando visite un servicio con una dirección URL específica de inquilino como https://outlook.com/**contoso**.com o https://mail.**contoso**.com.

Cuando visita un servicio con direcciones URL no específicas de inquilinos (p. ej.: https://mail.office365.com), aparece una página de inicio de sesión sin marca. En este caso, la marca aparece una vez que ha escrito el identificador de usuario o que ha seleccionado un icono de usuario.

> [AZURE.NOTE]
>
- El nombre de usuario debe aparecer como "activo" en la parte **Dominios** de Azure Portal donde ha configurado la personalización de marca. Para más información, consulte [cómo agregar nombres de dominio personalizados](active-directory-domains-add-azure-portal.md).
- La personalización de marca de la página de inicio de sesión no se traslada a la página de inicio de sesión de cliente de Microsoft. Si inicia sesión con una cuenta de Microsoft, puede ver una lista de iconos de usuario con la personalización de marca presentada por Azure AD, pero la personalización de marca de su organización no se aplica a la página de inicio de sesión de la cuenta de Microsoft.

**Para agregar la información de marca de empresa a su directorio:**

1.  Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.

2.  Seleccione **Más servicios**, escriba **Usuarios y grupos** en el cuadro de texto y, después, presione **Entrar**.

    ![Apertura de Administración de usuarios](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)

3. En la hoja **Usuarios y grupos**, seleccione **Company branding** (Personalización de marca de empresa).

4. En la hoja **Usuarios y grupos - Company branding (Personalización de marca de empresa)**, seleccione el comando **Editar**.

    ![Edición de la personalización de marca](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)

5. Modifique los elementos que desea personalizar. Todos los elementos son opcionales.

6. Haga clic en **Save**.

Puede transcurrir hasta una hora para que los cambios efectuados se muestren en la personalización de marca de la página de inicio de sesión.

## Pasos siguientes

[Agregar información de marca de empresa específica de un idioma](active-directory-branding-localize-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->