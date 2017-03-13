---
title: "Incorporación de la personalización de marca de empresa a sus páginas de inicio de sesión y panel de acceso"
description: "Aprenda cómo agregar una personalización de marca de empresa a la página de inicio de sesión en Azure y la página del panel de acceso"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f74621b4-4ef0-4899-8c0e-0c20347a8c31
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/03/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 1ec1a20dec318153923afd6a0875545d1e102f29
ms.lasthandoff: 03/07/2017


---
# <a name="add-company-branding-to-your-sign-in-and-access-panel-pages"></a>Incorporación de la personalización de marca de empresa a sus páginas de inicio de sesión y panel de acceso
Para evitar confusiones, muchas empresas quieren aplicar un aspecto coherente en todos los sitios web y servicios que administran. Azure Active Directory ofrece esta funcionalidad, ya que permite personalizar la apariencia de las siguientes páginas web con el logotipo y esquemas de color personalizados:

* **Página de inicio de sesión** : esta es la página que aparece al inicio de sesión de Office 365 o en otras aplicaciones basadas en web que están utilizando Azure AD como proveedor de identidades. Se interactúa con la página durante la detección del dominio de inicio o para escribir las credenciales. La detección del dominio de inicio permite al sistema redirigir a los usuarios federados a sus STS locales (por ejemplo, AD FS).
* **Página del Panel de acceso** : el Panel de acceso es un portal basado en web que le permite ver e iniciar las aplicaciones en la nube a las que el administrador de Azure AD concedió acceso. Para tener acceso al Panel de acceso, utilice la siguiente dirección URL: [https://myapps.microsoft.com](https://myapps.microsoft.com).

Este tema explica cómo personalizar la página de inicio de sesión y la página del panel de acceso.

> [!NOTE]
> * La información de marca de empresa es una característica que solo está disponible si ha actualizado a la edición Premium o Básica de Azure Active Directory, o es un usuario de Office 365. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).
> * Las ediciones Premium y Básico de Azure Active Directory están disponibles para los clientes de China que utilizan la instancia de Azure Active Directory en todo el mundo. Las ediciones Premium y Básico de Azure Active Directory no se admiten actualmente en el servicio de Microsoft Azure operado por 21Vianet en China. Para obtener más información, póngase en contacto con nosotros en el [foro de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).
>
>

## <a name="customizing-the-sign-in-page"></a>Personalización de la página de inicio de sesión
Normalmente, si necesita acceso basado en explorador a las aplicaciones y servicios en la nube a los que está suscrita su organización, utiliza la página de inicio de sesión.

Si ha aplicado cambios a la página de inicio de sesión, puede tardar hasta una hora para que aparezcan.

Las páginas de inicio de sesión con marca solamente aparecen cuando se visita un servicio con una dirección URL específica del inquilino, como https://outlook.com/**contoso**.com o https://mail.**contoso**.com.

Si visita un servicio con direcciones URL que no son específicas de un inquilino (por ejemplo, https://mail.office365.com), aparece una página de inicio de sesión sin marca. En este caso, la marca solamente aparecerá una vez que haya especificado el identificador de usuario o haya seleccionado un icono de usuario.

> [!NOTE]
> * El nombre de dominio debe aparecer como “Activo” en la sección **Active Directory** > **Directory** > **Dominios** del Portal de Azure clásico en el que ha configurado la personalización de marca.
> * La personalización de marca de la página de inicio de sesión no se traslada a la página de inicio de sesión de cliente de Microsoft. Si inicia sesión con una cuenta de Microsoft personal, puede ver una lista de iconos de usuario con la personalización de marca presentada por Azure AD, pero la personalización de marca de su organización no se aplicará a la página de inicio de sesión de la cuenta de Microsoft.
>
>

Si desea mostrar en su página la marca y los colores de su empresa, además de otros elementos personalizables, vea las imágenes siguientes para entender la diferencia entre las dos experiencias.

La siguiente captura de pantalla muestra un ejemplo de la página de inicio de sesión de Office 365 en un equipo de escritorio **antes** de una personalización:

![Página de inicio de sesión de Office 365 antes de la personalización][1]

La siguiente captura de pantalla muestra un ejemplo de la página de inicio de sesión de Office 365 en un equipo de escritorio **después** de una personalización:

![Página de inicio de sesión de Office 365 después de la personalización][2]

La siguiente captura de pantalla muestra un ejemplo de la página de inicio de sesión de Office 365 en un dispositivo móvil **antes** de una personalización:

![Página de inicio de sesión de Office 365 antes de la personalización][3]

La siguiente captura de pantalla muestra un ejemplo de la página de inicio de sesión de Office 365 en un dispositivo móvil **después** de una personalización:

![Página de inicio de sesión de Office 365 después de la personalización][4]

Cuando se cambia el tamaño de la ventana de un explorador, una ilustración grande, como la mostrada anteriormente, a menudo se recorta para adaptarse a las diferentes relaciones de aspecto de la pantalla. Teniendo esto en cuenta, debe intentar mantener siempre los elementos visuales más importantes de la ilustración para que siempre aparezcan en la esquina superior izquierda (en la superior derecha para los idiomas que se leen de derecha a izquierda). Esto es importante porque el cambio de tamaño normalmente se produce desde la esquina inferior derecha hacia la parte superior o izquierda o desde la parte inferior hacia la parte superior.

La imagen siguiente muestra cómo se recorta la ilustración cuando se cambia el tamaño del explorador hacia la izquierda:

![][6]

Este es el aspecto que tiene después de que el tamaño del explorador se cambie hacia la parte superior:

![][7]

## <a name="what-elements-on-the-page-can-i-customize"></a>¿Qué elementos de la página puedo personalizar?
Puede personalizar los siguientes elementos de la página de inicio de sesión:

![][5]

| Elemento de la página | Ubicación en la página |
|:--- | --- |
| Logotipo del banner |Se muestra en la parte superior derecha de la página. Reemplaza el logotipo mostrado por el sitio de destino en el que está iniciando sesión (por ejemplo, Office 365 o Azure). |
| Ilustración grande / Color de fondo |Se muestra a la izquierda de la página. Reemplaza la imagen mostrada por el sitio de destino en el que está iniciando sesión. En conexiones con un ancho de banda bajo o en pantallas estrechas, puede aparecer el color de fondo en lugar de la ilustración grande. |
| Mantener la sesión iniciada |Se muestra en el cuadro de texto Contraseña. |
| Texto de la página de inicio de sesión |Se muestra encima del pie de página cuando necesita ofrecer información útil antes de que inicien sesión con su cuenta profesional o educativa. Por ejemplo, es posible que desee incluir el número de teléfono de soporte técnico o una declaración legal. |

> [!NOTE]
> Todos los elementos son opcionales. Por ejemplo, si especifica un logotipo del banner pero no una ilustración grande, la página de inicio de sesión muestra su logotipo y la ilustración del sitio de destino (es decir, la imagen de la autopista de California de Office 365).
>
>

En la página de inicio de sesión, la casilla **Mantener la sesión iniciada** permite que el usuario se mantenga conectado después de cerrar y abrir de nuevo el explorador. No afecta a la duración de la sesión. Puede ocultar la casilla en la página de inicio de sesión de Azure Active Directory.

Puede decidir si se muestra la casilla o no mediante la configuración de **Ocultar KMSI**.

![][9]

Para ocultar la casilla, utilice la opción **Oculto**.

> [!NOTE]
> Algunas características de SharePoint Online y Office 2010 dependen de que los usuarios puedan activar esta casilla. Si configura esta opción en oculto, es posible que los usuarios reciban solicitudes adicionales e inesperadas de inicio de sesión.
>
>

También puede localizar todos los elementos de esta página. Una vez que haya configurado un conjunto de elementos de personalización "predeterminado", puede configurar más versiones para las diferentes configuraciones regionales. También puede mezclar y hacer coincidir varios elementos. Por ejemplo, puede:

* Crear una ilustración grande "predeterminada" que funcione para todas las culturas y, a continuación, crear versiones específicas para inglés y francés. Al establecer los navegadores en uno de estos dos idiomas, aparece la imagen específica, mientras que la ilustración predeterminada aparece en los demás idiomas.
* Configure logotipos diferentes para su organización (por ejemplo, para las versiones en japonés o hebreo).

## <a name="access-panel-page-customization"></a>Personalización de la página del panel de acceso
La página Panel de acceso es esencialmente una página de portal para acceder rápidamente a las aplicaciones de nube a las que ha concedido acceso el administrador. En esta página, las aplicaciones aparecen como iconos de aplicaciones interactivas.

La siguiente captura de pantalla muestra un ejemplo de una página de panel de acceso después de la personalización.

![][8]

## <a name="configure-your-directory-with-company-branding"></a>Configuración de su directorio con la información de marca de empresa
Puede configurar un conjunto predeterminado de elementos personalizables por directorio en el Portal de Azure clásico. Después de guardar los valores predeterminados, un administrador puede agregar versiones localizadas de cada elemento para diferentes idiomas o configuraciones regionales. Todos los elementos personalizables son opcionales.

Por ejemplo, si configura un logotipo del banner predeterminado pero no una ilustración grande, la página de inicio de sesión muestra su logotipo en la esquina superior derecha. Sin embargo, se muestra la ilustración predeterminada del sitio.

Suponga la configuración siguiente:

* Un logotipo del banner predeterminado y el inicio de sesión en el texto de página en inglés
* Un texto de la página de inicio de sesión específico para un idioma en alemán

Si su preferencia de idioma es alemán, obtiene el logotipo del banner predeterminado pero con el texto en alemán.

Aunque técnicamente podría configurar un conjunto diferente para cada idioma admitido por Azure AD, se recomienda que mantenga reducidas las variantes por razones de mantenimiento y rendimiento.

> [!IMPORTANT]
> Yammer no muestra la página de inicio de sesión de marca de Azure AD hasta después de que el usuario inicia sesión. El usuario ve la página de inicio de sesión genérica de Office 365 primero y, a continuación, la página de marca.   
 
 
**Para agregar una personalización de marca de la empresa a su directorio, realice los pasos siguientes:**

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com) como administrador del directorio que desea personalizar.
2. Seleccione el directorio que desea personalizar.
3. En la barra de herramientas de la parte superior, haga clic en **Configurar**.
4. Haga clic en **Personalizar la información de marca**.
5. Modifique los elementos que desea personalizar. Todos los campos son opcionales.
6. Haga clic en **Save**.

Puede transcurrir hasta una hora para que el cambio efectuado se muestre en la personalización de marca de la página de inicio de sesión.

**Para agregar una personalización de marca de la empresa específica de un idioma, realice los pasos siguientes:**

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com) como administrador del directorio que desea personalizar.
2. Seleccione el directorio que desea personalizar.
fs3. En la barra de herramientas de la parte superior, haga clic en **Configurar**.
4. Haga clic en **Personalizar la información de marca**.
5. Haga clic en **Agregar personalización de marca para un idioma específico**.
6. Seleccione el idioma que para el que desea personalizar el logotipo y, después, haga clic en **Siguiente**.
7. Edite únicamente los elementos para los que desea configurar elementos de reemplazo específicos del idioma. Todos los campos son opcionales. Si un campo se deja en blanco, en su lugar se muestra el valor predeterminado personalizado (o el valor predeterminado de Microsoft si no se ha configurado un valor predeterminado personalizado).
8. Haga clic en **Save**.

**Para quitar la personalización de marca de la empresa de su directorio, realice los pasos siguientes:**

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com) como administrador del directorio que desea personalizar.
2. Seleccione el directorio que desea personalizar.
3. En la barra de herramientas de la parte superior, haga clic en **Configurar**.
4. Haga clic en **Personalizar la información de marca**.
5. En la página Personalizar la información de marca, seleccione **Editar configuración de marca existente** y, a continuación, vaya a la página siguiente.
6. En función de los elementos que desee quitar, realice una o varias de las acciones siguientes:

    a. En **Logotipo del banner**, seleccione **Quitar logotipo cargado** .

    b. En **Logotipo de imagen**, seleccione **Quitar logotipo cargado**.

    c. Quite el texto de todos los cuadros de texto.

    d. Haga clic en **Next**.

    e. Quite el texto de todos los cuadros de texto.
7. Haga clic en **Guardar** para quitar los elementos.
8. Si es necesario, haga clic en **Personalizar la información de marca** de nuevo y repita estos pasos para todos los elementos específicos de la marca del idioma que tengan que quitarse.
    Todas las opciones de personalización de marca se habrán quitado cuando haga clic en **Personalizar la información de marca** y vea el formulario **Personalizar información de la marca predeterminada** sin opciones configuradas.

## <a name="testing-and-examples"></a>Pruebas y ejemplos
Se recomienda que experimente con un inquilino de prueba antes de realizar cambios en el entorno de producción.

**Para comprobar si se ha aplicado la información de marca:**

1. Abra una sesión de explorador InPrivate o de incógnito.
2. Visite https://outlook.com/contoso.com (sustituya contoso.com por el dominio personalizado).

Esto también funciona con los dominios que tengan un aspecto similar a contoso.onmicrosoft.com.

Para ayudarle a crear conjuntos eficaces de personalización, hemos personalizado las siguientes dos páginas de inicio de sesión ficticias:

* [http://AKA.ms/aaddemo001](http://aka.ms/aaddemo001)
* [http://AKA.ms/aaddemo002](http://aka.ms/aaddemo002)

Para probar la configuración específica del idioma, debe modificar las preferencias de idioma predeterminado en el explorador web a un idioma que se haya definido en la personalización. En Internet Explorer, configure esto en el menú **Opciones de Internet** .

## <a name="customizable-elements"></a>Elementos personalizables
Algunos elementos personalizables de Azure AD disponen de múltiples casos de uso. Puede configurar los logotipos de la compañía una vez por directorio y se utiliza en las páginas de inicio de sesión y del Panel de acceso. Algunos elementos personalizables son específicos únicamente de la página de inicio de sesión. En la tabla siguiente se proporcionan detalles para los diferentes elementos personalizables.

| Nombre | Description | Restricciones | Recomendaciones |
| --- | --- | --- | --- |
| Logotipo del banner |El logotipo del banner se muestra en la página de inicio de sesión y en el panel de acceso. |<p>JPG o PNG</p><p>60 x&280; píxeles</p><p>10 kB</p> |<p>Utilice el logotipo completo de la organización (incluido el pictograma y el logotipo).</p><p>Manténgalo por debajo de 30 píxeles para que no aparezcan barras de desplazamiento en los dispositivos móviles.</p><p>Manténgalo por debajo de 4 kB.</p><p>Utilice un PNG transparente (no debe presuponer que la página de inicio de sesión tiene un fondo blanco).</p> |
| Logotipo del icono |(no se utiliza actualmente en la página de inicio de sesión). En el futuro, este texto puede utilizarse para reemplazar el pictograma genérico de "cuenta profesional o educativa" en diferentes lugares de la experiencia. |<p>JPG o PNG</p><p>120 x&120; píxeles</p><p>10 kB</p> |<p>Conserve un diseño sencillo (sin texto pequeño), ya que el tamaño de esta imagen puede cambiar hasta en un 50%. |
| </p> | | | |
| Etiqueta de nombre de usuario de la página de inicio de sesión |(no se utiliza actualmente en la página de inicio de sesión). En el futuro, este texto puede utilizarse para reemplazar la cadena genérica de "cuenta profesional o educativa" en diferentes lugares de la experiencia. Puede establecerlo en algo como "Cuenta Contoso" o "Id. de Contoso". |<p>Texto Unicode, hasta 50 caracteres</p><p>Solo texto sin formato (sin vínculos ni etiquetas HTML)</p> |<p>Elija una etiqueta corta y sencilla.</p><p>Pregunte a los usuarios qué nombre utilizan para hacer referencia a la cuenta de la escuela o del trabajo que les proporciona.</p> |
| Texto de la página de inicio de sesión |Este texto "reutilizable" aparece debajo del formulario de la página de inicio de sesión y puede utilizarse para comunicar instrucciones adicionales o dónde acudir para obtener ayuda y soporte técnico. |<p>Texto Unicode, hasta 256 caracteres</p><p>Solo texto sin formato (sin vínculos ni etiquetas HTML)</p> |No sobrepase los 250 caracteres (aproximadamente 3 líneas de texto) |
| Ilustración de la página de inicio de sesión |La ilustración es una imagen grande que se muestra en la página de inicio de sesión a la izquierda del formulario de la página de inicio de sesión. |<p>JPG o PNG</p><p>1420 x&1200;</p><p>500 kB</p> |<p>1420 x&1200; píxeles</p><p>Importante: manténgala lo más pequeña posible, lo ideal es que no sobrepase los 200 KB. Si esta imagen es demasiado grande, afecta al rendimiento de la página de inicio de sesión si la imagen no se almacena en caché</p><p>Esta imagen a menudo se recorta para adaptarse a las diferentes relaciones de la pantalla. Mantenga los elementos visuales principales en la esquina superior izquierda (en la esquina superior derecha para los idiomas que se leen de derecha a izquierda), ya que el cambio de tamaño se produce desde la esquina inferior derecha, hacia la parte superior izquierda, a medida que se reduzca la ventana del explorador.</p> |
| Color de fondo de la página de inicio de sesión |El color de fondo de la página de inicio de sesión se usa en el área a la izquierda del formulario de la página de inicio de sesión. |Debe ser un color RGB en formato hexadecimal (ejemplo: #FFFFFF) |<p>En las conexiones con poco ancho de banda, puede aparecer el color de fondo en lugar de la ilustración grande.</p><p>Le recomendamos que utilice el color principal del logotipo del banner.</p> |

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Active Directory Premium](active-directory-get-started-premium.md)
* [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/SignInPage_beforecustomization.png
[2]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization.png
[3]: ./media/active-directory-add-company-branding/SignInPage_mobile_beforecustomization.png
[4]: ./media/active-directory-add-company-branding/SignInPage_mobile_aftercustomization.png
[5]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_elements.png
[6]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedleft.png
[7]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedtop.png
[8]: ./media/active-directory-add-company-branding/APBranding.png
[9]: ./media/active-directory-add-company-branding/hidekmsi.png

