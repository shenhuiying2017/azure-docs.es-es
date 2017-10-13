---
title: "Personalización de la página de inicio de sesión de Azure Active Directory | Microsoft Docs"
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
ms.date: 09/06/2017
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 6bfce3d4ec243779229cc4f39e1c22149229a66a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Guía de inicio rápido: Incorporación de personalización de marca de empresa a su página de inicio de sesión de Azure AD
Para evitar confusiones, muchas empresas quieren aplicar un aspecto coherente en todos los sitios web y servicios que administran. Azure Active Directory (Azure AD) ofrece esta funcionalidad, ya que permite personalizar la apariencia de la página de inicio de sesión con el logotipo de la empresa y combinaciones de colores personalizadas. Se trata de la página que aparece al iniciar sesión en Office 365 u otras aplicaciones web que estén utilizando Azure AD como proveedor de identidades. Interactuará con esta página para especificar las credenciales.

> [!NOTE]
> * La personalización de marca de empresa solo está disponible si ha actualizado a las ediciones Azure Active Directory Premium o Basic, o si tiene licencia de Office 365. Para saber si una característica se admite con su tipo de licencia, consulte la página de [información de precios de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * Las ediciones Premium y Básico de Azure Active Directory están disponibles para los clientes de China que utilizan la instancia de Azure Active Directory en todo el mundo. Las ediciones Premium y Básico de Azure Active Directory no se admiten actualmente en el servicio de Microsoft Azure operado por 21Vianet en China. Para obtener más información, póngase en contacto con nosotros en el [foro de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>Personalización de la página de inicio de sesión

<!--You can customize the following elements on the sign-in page: <attach image>-->

Las personalizaciones de marca de la empresa aparecerán en la página de inicio de sesión de Azure AD cuando los usuarios tengan acceso a una dirección URL específica del inquilino como [*https://outlook.com/contoso.com*](https://outlook.com/contoso.com).

Cuando los usuarios visitan una dirección URL genérica como www.office.com, la página de inicio de sesión no contiene personalizaciones de marca de la empresa porque el sistema desconoce quién es el usuario. La marca de la empresa aparecerá una vez que los usuarios escriban su id. de usuario o seleccionen un icono de usuario.

> [!NOTE]
> * El nombre de usuario debe aparecer como "activo" en la parte **Dominios** de Azure Portal donde ha configurado la personalización de marca. Para más información, vea [Incorporación de un nombre de dominio personalizado](add-custom-domain.md).
> * La personalización de marca de la página de inicio de sesión no se incluye en la página de inicio de cuentas de Microsoft personales. Si los empleados o los invitados de la empresa inician sesión con una cuenta de Microsoft personal, su página de inicio de sesión no reflejará la personalización de marca de su organización.


### <a name="banner-logo"></a>Logotipo del banner 

Descripción | Restricciones | Recomendaciones
------- | ------- | ----------
El logotipo del banner se muestra en las páginas de inicio de sesión y panel de acceso.<br>En la página de inicio de sesión, se muestra una vez que se determina la organización del usuario (normalmente, después de que escriba su nombre de usuario).  | JPG o PNG transparente<br>Alto máximo: 36 px<br>Ancho máximo: 245 px | Use el logotipo de su organización aquí.<br>Use una imagen transparente. No dé por hecho que el fondo será blanco.<br>No agregue relleno alrededor del logotipo en la imagen o el logotipo se verá desproporcionadamente pequeño.

### <a name="username-hint"></a>Sugerencia de nombre de usuario   
Descripción | Restricciones | Recomendaciones
------- | ------- | ----------
De este modo se personaliza la sugerencia en el campo de nombre de usuario. | Texto Unicode hasta 64 caracteres<br>Solo texto sin formato | Recomendamos que no establezca esto si espera que usuarios invitados ajenos a la organización inicien sesión en la aplicación.
            
### <a name="sign-in-page-text"></a>Texto de la página de inicio de sesión   
Descripción | Restricciones | Recomendaciones
------- | ------- | ----------
Esto aparece en la parte inferior del formulario de inicio de sesión y se puede usar para comunicar información adicional como el número de teléfono a su departamento de soporte técnico o una declaración legal. | Texto Unicode hasta 256 caracteres<br>Solo texto sin formato (sin vínculos ni etiquetas HTML)   

### <a name="sign-in-page-image"></a>Imagen de la página de inicio de sesión  
Descripción | Restricciones | Recomendaciones
------- | ------- | ----------
Esto aparece en el fondo de la página de inicio de sesión, está anclado en el centro del espacio visible y se escalará y recortará para rellenar la ventana del explorador.    <br>En las pantallas estrechas, como las de teléfonos móviles, no se muestra esta imagen.<br>Nuestro código aplicará una máscara gruesa con una opacidad de 0,55 sobre esta imagen al cargarse la página. | JPG o PNG<br>Dimensiones de la imagen: 1920 x 1080 px<br>Tamaño de archivo: &lt; 300 KB | <br>Use imágenes allí donde no haya un enfoque del asunto sólido. El formulario de inicio de sesión opaco aparece sobre el centro de esta imagen y puede cubrir cualquier parte de la imagen según el tamaño de la ventana del explorador.<br>Mantenga el tamaño de archivo lo más pequeño posible para garantizar tiempos de carga rápidos. 

### <a name="background-color"></a>Color de fondo
Descripción | Restricciones | Recomendaciones
------- | ------- | ----------
Se utiliza este color en lugar de la imagen de fondo en las conexiones de ancho de banda bajo. | Color RGB en formato hexadecimal (ejemplo: #FFFFFF) | Se recomienda usar el color principal del logotipo del banner o el color de la organización.

### <a name="show-option-to-remain-signed-in"></a>Visualización de la opción para seguir conectado
Descripción | Restricciones | Recomendaciones
------- | ------- | ----------
El inicio de sesión de Azure AD ofrece al usuario la opción de seguir conectado al cerrar y volver a abrir el explorador. Use esto para ocultar esa opción.<br>Establezca esto en "No" para ocultar esta opción de sus usuarios. | &nbsp; | No afecta a la duración de la sesión.<br>Algunas características de SharePoint Online y Office 2010 dependen de que los usuarios puedan elegir seguir conectados. Si establece esto para ocultarlo, es posible que los usuarios reciban solicitudes adicionales e inesperadas de inicio de sesión.

> [!NOTE]
> Todos los elementos son opcionales. Por ejemplo, si especifica un logotipo del banner sin ninguna imagen de fondo, se mostrarán en la página de inicio de sesión su logotipo y la imagen de fondo para el sitio de destino (por ejemplo, Office 365).

## <a name="add-company-branding-to-your-directory"></a>Incorporación de personalización de marca de empresa a su directorio

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Más servicios**, escriba **Usuarios y grupos** en el cuadro de texto y presione **Entrar**.

   ![Apertura de Administración de usuarios](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)
3. En la hoja **Usuarios y grupos**, seleccione **Personalización de marca de empresa**.
4. En la hoja **Usuarios y grupos - Personalización de marca de empresa**, seleccione el comando **Editar**.

    ![Edición de la personalización de marca](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)
5. Modifique los elementos que desea personalizar. Todos los elementos son opcionales.
6. Haga clic en **Save**.

Puede transcurrir hasta una hora para que los cambios efectuados se muestren en la personalización de marca de la página de inicio de sesión.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Incorporación de personalización de marca de empresa específica de un idioma a su directorio

1. Inicie sesión en el [Centro de administración de Azure AD](https://aad.portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Usuarios y grupos** en el cuadro de texto y presione **Entrar**.

   ![Apertura de Administración de usuarios](./media/active-directory-branding-localize-azure-portal/user-management.png)
3. En la hoja **Usuarios y grupos**, seleccione **Personalización de marca de empresa**.
4. En la hoja **Usuarios y grupos - Personalización de marca de empresa**, seleccione el comando **Agregar idioma**.

    ![Adición de elementos de personalización de marca específicos del idioma](./media/active-directory-branding-localize-azure-portal/add-language.png)
5. Modifique los elementos que desea personalizar. Todos los elementos son opcionales.
6. Haga clic en **Save**.

Puede transcurrir hasta una hora para que los cambios efectuados se muestren en la personalización de marca de la página de inicio de sesión.

## <a name="next-steps"></a>Pasos siguientes
En esta guía de inicio rápido, ha aprendido a agregar personalización de marca a su directorio de Azure AD. 

Puede usar el vínculo siguiente para configurar la personalización de marca en Azure AD desde Azure Portal.

> [!div class="nextstepaction"]
> [Configuración de la personalización de marca de la compañía](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 