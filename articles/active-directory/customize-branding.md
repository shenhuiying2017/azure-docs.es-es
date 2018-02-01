---
title: "Personalización de la página de inicio de sesión del inquilino de Azure Active Directory | Microsoft Docs"
description: "Aprenda a agregar una personalización de marca de empresa a la página de inicio de sesión de Azure"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/19/2018
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 03a6b82f769ed9a36c5d3ff9934de75d1536e1ae
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Guía de inicio rápido: Incorporación de personalización de marca de empresa a su página de inicio de sesión de Azure AD
Para evitar confusiones, muchas empresas quieren aplicar un aspecto coherente en todos los sitios web y servicios que administran. Azure Active Directory (Azure AD) ofrece esta funcionalidad, ya que permite personalizar la apariencia de la página de inicio de sesión con el logotipo de la empresa y combinaciones de colores personalizadas. La página de inicio de sesión se abre al iniciar sesión en aplicaciones web como Office 365 que usan Azure AD como proveedor de identidades. Interactuará con esta página para especificar las credenciales.

> [!NOTE]
> * La personalización de marca de empresa solo está disponible si ha adquirido las licencias de Azure Active Directory Premium o Basic, o si tiene una licencia de Office 365. Para saber si una característica se admite con su tipo de licencia, consulte la página de [información de precios de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * Las ediciones Azure AD Premium y Basic están disponibles para los clientes de China que utilizan la instancia de Azure Active Directory en todo el mundo. Las ediciones Azure AD Premium y Basic no se admiten actualmente en el servicio de Azure administrado por 21Vianet en China. Para más información, póngase en contacto con nosotros en el [foro de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>Personalización de la página de inicio de sesión

<!--You can customize the following elements on the sign-in page: <attach image>-->

Las personalizaciones de marca de la empresa aparecerán en la página de inicio de sesión de Azure AD cuando los usuarios tengan acceso a una dirección URL específica del inquilino como [*https://outlook.com/contoso.com*](https://outlook.com/contoso.com).

Por ejemplo, cuando los usuarios visitan www.office.com, la página de inicio de sesión no muestra ninguna personalización de marca de empresa porque el usuario aún no ha especificado las credenciales. Después de que un usuario escribe su identificador de usuario o selecciona un icono de usuario, se muestra la marca de empresa.

> [!NOTE]
> * El nombre de usuario debe aparecer como "activo" en la parte **Dominios** de Azure Portal donde ha configurado la personalización de marca. Para más información, vea [Incorporación de un nombre de dominio personalizado](add-custom-domain.md).
> * La personalización de marca de la página de inicio de sesión no se incluye en la página de inicio de cuentas de Microsoft personales. Si los empleados o los invitados de la empresa inician sesión con una cuenta de Microsoft personal, su página de inicio de sesión no reflejará la personalización de marca de su organización.


### <a name="banner-logo"></a>Logotipo del banner 

DESCRIPCIÓN | Restricciones | Recomendaciones
------- | ------- | ----------
El logotipo del banner se muestra en las páginas de inicio de sesión y panel de acceso.<br>En la página de inicio de sesión, el logotipo se muestra una vez escrito el nombre de usuario. | JPG o PNG transparente<br>Alto máximo: 36 px<br>Ancho máximo: 245 px | Use el logotipo de su organización aquí.<br>Use una imagen transparente. No dé por hecho que el fondo será blanco.<br>No agregue relleno alrededor del logotipo en la imagen o el logotipo se verá desproporcionadamente pequeño.

### <a name="username-hint"></a>Sugerencia de nombre de usuario   
DESCRIPCIÓN | Restricciones | Recomendaciones
------- | ------- | ----------
Esta opción personaliza la sugerencia en el campo de nombre de usuario. | Texto Unicode hasta 64 caracteres<br>Solo texto sin formato | Se recomienda no establecer esta opción si espera que usuarios invitados ajenos a la organización inicien sesión en la aplicación.
            
### <a name="sign-in-page-text"></a>Texto de la página de inicio de sesión   
DESCRIPCIÓN | Restricciones | Recomendaciones
------- | ------- | ----------
Esta opción aparece en la parte inferior del formulario de inicio de sesión y se puede usar para comunicar información adicional como el número de teléfono a su departamento de soporte técnico o una declaración legal. | Texto Unicode hasta 256 caracteres<br>Solo texto sin formato (sin vínculos ni etiquetas HTML)    

### <a name="sign-in-page-image"></a>Imagen de la página de inicio de sesión  
DESCRIPCIÓN | Restricciones | Recomendaciones
------- | ------- | ----------
Esta opción aparece en el fondo de la página de inicio de sesión, está anclada en el centro del espacio visible y se escala y recorta para rellenar la ventana del explorador.    <br>En las pantallas estrechas, como las de teléfonos móviles, no se muestra esta imagen.<br>Se aplica una máscara gruesa con una opacidad de 0,55 sobre esta imagen al cargarse la página. | JPG o PNG<br>Dimensiones de la imagen: 1920 x 1080 px<br>Tamaño de archivo: &lt; 300 KB | <br>Use imágenes allí donde no haya un enfoque del asunto sólido. El formulario de inicio de sesión opaco aparece sobre el centro de esta imagen y puede cubrir cualquier parte de la imagen según el tamaño de la ventana del explorador.<br>Mantenga el tamaño de archivo pequeño para garantizar tiempos de carga rápidos. 

### <a name="sign-in-page-background-color"></a>Color de fondo de la página de inicio de sesión
DESCRIPCIÓN | Restricciones | Recomendaciones
------- | ------- | ----------
Se utiliza este color en lugar de la imagen de fondo en las conexiones de ancho de banda bajo. | Color RGB en formato hexadecimal (ejemplo: #FFFFFF) | Se recomienda usar el color principal del logotipo del banner o el color de la organización.

### <a name="square-logo-image"></a>Imagen de logotipo cuadrado
DESCRIPCIÓN | Restricciones | Recomendaciones
------- | ------- | ----------
Esta imagen aparece durante la configuración de nuevos equipos con Enterprise Windows 10. Proporciona contexto a los empleados al configurar su nuevo equipo de trabajo. La imagen se muestra para los inquilinos que usan [Windows AutoPilot](https://blogs.windows.com/business/2017/06/29/delivering-modern-promise-windows-10/?utm_source=dlvr.it&utm_medium=twitter#gDTp1u6q35bvDWIS.97) para implementar sus dispositivos de trabajo y en las páginas de escritura de contraseña en otras experiencias con Windows 10. | PNG (preferido) o JPG transparentes<br>Dimensiones de la imagen: 240 x 240 px<br>Tamaño de archivo: &lt; 10 KB | Use el logotipo de su organización aquí.<br> Use una imagen transparente.<br>No dé por hecho que el fondo será blanco.<br>No agregue relleno al logotipo en la imagen o, de lo contrario, el logotipo se verá desproporcionadamente pequeño.

### <a name="show-option-to-remain-signed-in"></a>Visualización de la opción para seguir conectado
DESCRIPCIÓN | Restricciones | Recomendaciones
------- | ------- | ----------
El inicio de sesión de Azure AD ofrece al usuario la opción de seguir conectado al cerrar y volver a abrir el explorador. Esta configuración oculta esa opción.<br>Establezca esto en **No** para ocultar esta opción de sus usuarios. | &nbsp; | Ocultar la opción no afecta a la duración de la sesión.<br>Algunas características de SharePoint Online y Office 2010 dependen de que los usuarios puedan elegir seguir conectados. Si establece esta opción en **No**, puede que los usuarios reciban solicitudes adicionales e inesperadas de inicio de sesión.

> [!NOTE]
> Todos los elementos son opcionales. Por ejemplo, si especifica un logotipo del banner sin ninguna imagen de fondo, se mostrarán en la página de inicio de sesión su logotipo y la imagen de fondo para el sitio de destino (por ejemplo, Office 365).

## <a name="add-company-branding-to-your-directory"></a>Incorporación de personalización de marca de empresa a su directorio

1. Inicie sesión en el [Centro de administración de Azure AD](https://aad.portal.azure.com) con una cuenta que tenga el rol de administrador global en el inquilino.
2. Seleccione **Usuarios y grupos** > **Personalización de marca de empresa** > **Editar**.
  
  ![Apertura de la personalización de marca de empresa](./media/customize-branding/navigation-to-branding.png)
3. Modifique los elementos que desea personalizar. Todos los elementos son opcionales.
  
  ![Edición de la personalización de marca](./media/customize-branding/edit-branding.png)
5. Cuando finalice, seleccione **Guardar**.

Puede transcurrir hasta una hora para que los cambios efectuados se muestren en la personalización de marca de la página de inicio de sesión.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Incorporación de personalización de marca de empresa específica de un idioma a su directorio

1. Inicie sesión en el [Centro de administración de Azure AD](https://aad.portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Usuarios y grupos** > **Personalización de marca de empresa** > **Nuevo idioma**.
  
  ![Adición de elementos de personalización de marca específicos del idioma](./media/customize-branding/add-language.png)
5. Modifique los elementos que desea personalizar. Todos los elementos son opcionales.
6. Cuando finalice, seleccione **Guardar**.

Puede transcurrir hasta una hora para que los cambios efectuados se muestren en la personalización de marca de la página de inicio de sesión.

## <a name="next-steps"></a>pasos siguientes
En esta guía de inicio rápido, ha aprendido a agregar personalización de marca a su directorio de Azure AD. 

Puede usar el vínculo siguiente para configurar la personalización de marca en Azure AD desde Azure Portal.

> [!div class="nextstepaction"]
> [Configuración de la personalización de marca de la compañía](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 