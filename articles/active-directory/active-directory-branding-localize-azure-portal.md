---
title: "Adición de personalización de marca de empresa a la página de inicio de sesión en la versión preliminar de Azure Active Directory | Microsoft Docs"
description: "Sepa cómo agregar imágenes y texto de personalización de marca específicos del idioma a una página de inicio de sesión de Azure."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a0310d6a-aaa7-4ea0-991d-6d3135b4382a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: fece49df6a35f09ee73bb4b9ec4c33a0529a9f59
ms.lasthandoff: 12/28/2016


---
# <a name="add-language-specific-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Adición de personalización de marca de empresa a la página de inicio de sesión en la versión preliminar de Azure Active Directory
Para evitar confusiones, muchas empresas quieren aplicar un aspecto coherente en todos los sitios web y servicios que administran. La versión preliminar de Azure Active Directory ofrece esta funcionalidad, ya que permite personalizar la apariencia de la página de inicio de sesión con esquemas de color personalizados y el logotipo de la empresa. [¿Qué es la versión preliminar?](active-directory-preview-explainer.md)  Se trata de la página que aparece al iniciar sesión en Office 365 u otras aplicaciones web que estén utilizando Azure AD como proveedor de identidades. Interactuará con esta página para especificar las credenciales.

## <a name="customizing-the-sign-in-page-for-another-language"></a>Personalización de la página de inicio de sesión para otro idioma
Solo podrá agregar elementos específicos del idioma a la página de inicio de sesión personalizada si ya ha creado una página de inicio de sesión personalizada tal y como se describe en el artículo sobre cómo [agregar personalización de marca de empresa a la página de inicio de sesión](active-directory-branding-custom-signon-azure-portal.md). Puede configurar una página de inicio de sesión por directorio con un conjunto predeterminado de elementos personalizables. Cuando haya configurado el conjunto predeterminado de elementos de página, podrá ajustar más versiones para las diferentes configuraciones regionales. También puede mezclar y hacer coincidir varios elementos. Por ejemplo, podría realizar estas acciones:

* Crear una **imagen de página de inicio de sesión** predeterminada que funcione en todas las referencias culturales y, después, crear versiones específicas para inglés y francés. Al establecer los exploradores en uno de estos dos idiomas, aparece la imagen específica del idioma, mientras que la ilustración predeterminada se muestra en los demás idiomas.
* Configure logotipos diferentes para su organización (por ejemplo, para las versiones en japonés o hebreo).

Por motivos de rendimiento y mantenimiento, se recomienda mantener un número de variantes de idioma reducido.

**Para agregar la información de marca de empresa a su directorio:**

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Más servicios**, escriba **Usuarios y grupos** en el cuadro de texto y presione **Entrar**.

   ![Apertura de Administración de usuarios](./media/active-directory-branding-localize-azure-portal/user-management.png)
3. En la hoja **Usuarios y grupos**, seleccione **Personalización de marca de empresa**.
4. En la hoja **Usuarios y grupos - Personalización de marca de empresa**, seleccione el comando **Agregar idioma**.

    ![Adición de elementos de personalización de marca específicos del idioma](./media/active-directory-branding-localize-azure-portal/add-language.png)
5. Modifique los elementos que desea personalizar. Todos los elementos son opcionales.
6. Haga clic en **Save**.

Puede transcurrir hasta una hora para que los cambios efectuados se muestren en la personalización de marca de la página de inicio de sesión.

## <a name="next-steps"></a>Pasos siguientes
[Agregar personalización de marca a la página de inicio de sesión](active-directory-branding-custom-signon-azure-portal.md)

