---
title: "Incorporación de personalización de marca de empresa a sus páginas de inicio de sesión y panel de acceso en Azure Active Directory"
description: "Aprenda cómo agregar una personalización de marca de empresa a la página de inicio de sesión en Azure y la página del panel de acceso"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f74621b4-4ef0-4899-8c0e-0c20347a8c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/07/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 6d4fbfe97288fcb76628b45649b8b678152198a9
ms.lasthandoff: 04/07/2017


---
# <a name="add-company-branding-to-sign-in-and-access-panel-pages"></a>Incorporación de personalización de marca de empresa a sus páginas de inicio de sesión y panel de acceso
Muchas empresas quieren aplicar un aspecto coherente en todos los sitios web y servicios que administran. Azure Active Directory ofrece esta funcionalidad, ya que permite que los profesionales de TI personalicen la apariencia de las siguientes páginas web con logotipos e imágenes de la empresa:

* **Página de inicio de sesión**: es la página que aparece cuando los empleados y los invitados de la empresa inician sesión en Office 365 u otras aplicaciones que usan Azure AD.
* **Página de panel de acceso**: el panel de acceso es un portal basado en Web que le permite ver e iniciar las aplicaciones en la nube a las que el administrador de Azure AD le concediera acceso. El panel de acceso se encuentra en: [https://myapps.microsoft.com](https://myapps.microsoft.com).

Este tema explica cómo personalizar la página de inicio de sesión y la página del panel de acceso.

> [!NOTE]
> * La personalización de marca de empresa solo está disponible si ha actualizado a Azure Active Directory Premium o Basic Edition, o si tiene licencia de Office 365. Para más información, consulte Ediciones de Azure Active Directory.
> 
> * Las ediciones Premium y Básico de Azure Active Directory están disponibles para los clientes de China que utilizan la instancia de Azure Active Directory en todo el mundo. Las ediciones Premium y Básico de Azure Active Directory no se admiten actualmente en el servicio de Microsoft Azure operado por 21Vianet en China. Para más información, póngase en contacto con nosotros en el foro de Azure Active Directory.


## <a name="customizing-the-sign-in-page"></a>Personalización de la página de inicio de sesión
Los usuarios normalmente interactúan con la página de inicio de sesión de Azure AD al intentar acceder a servicios y aplicaciones en la nube a los que su organización está suscrita.

Si ha aplicado cambios de personalización de marca a la página de inicio de sesión, pueden tardar hasta una hora en aparecer a los usuarios finales.

Los elementos de personalización de marca de la empresa aparecerán en la página de inicio de sesión de Azure AD cuando los usuarios accedan a una URL específica del inquilino como https://outlook.com/contoso.com.

Cuando los usuarios visitan un servicio en una dirección URL genérica como www.office.com, la página de inicio de sesión no contiene aún la información de personalización de marca de la empresa porque el sistema desconoce quién es el usuario. Sin embargo, sí que aparece una vez que los usuarios escriben su id. de usuario o seleccionan un icono de usuario.

> [!NOTE]
> * El nombre de dominio debe aparecer como “Activo” en la sección **Active Directory** > **Directory** > **Dominios** del Portal de Azure clásico en el que ha configurado la personalización de marca.
> * La personalización de marca de la página de inicio de sesión no se incluye en la página de inicio de cuentas de Microsoft personales. Si los empleados o los invitados de la empresa inician sesión con una cuenta de Microsoft personal, su página de inicio de sesión no reflejará la personalización de marca de su organización.
>

En las capturas de pantalla siguientes se explica cómo se personalizan las páginas de inicio de sesión.

### <a name="scenario-1-contoso-employee-goes-to-a-generic-app-url-for-example-wwwofficecom"></a>Escenario 1: Un empleado de Contoso va a una dirección URL de aplicación genérica (por ejemplo, www.office.com)

En este ejemplo, un usuario de Contoso inicia sesión en una aplicación móvil o en una aplicación web mediante una dirección URL genérica. La ilustración de la izquierda siempre representará la aplicación y el panel de interacción de la derecha se actualizará para mostrar elementos de marca de Contoso cuando corresponda.

![Página de inicio de sesión de Office 365 antes y después de la personalización][1]

### <a name="scenario-2-contoso-employee-goes-to-contoso-app-thats-restricted-to-internal-users"></a>Escenario 2: Un empleado de Contoso va a la aplicación de Contoso restringida a los usuarios internos

En este ejemplo, un usuario de Contoso está iniciando sesión en una aplicación interna mediante una dirección URL específica de la compañía. La ilustración de la izquierda representa la marca de la empresa (Contoso). El panel de interacción de la derecha está bloqueado para Contoso y ayuda a los empleados con el inicio de sesión.

![página de inicio de sesión de aplicación restringida][2]

### <a name="scenario-3-contoso-employee-goes-to-a-contoso-app-thats-open-to-external-users"></a>Escenario 3: Un empleado de Contoso va a la aplicación de Contoso abierta a usuarios externos

En este ejemplo, los usuarios inician sesión en una aplicación de LOB de Contoso, pero el usuario no tiene por qué ser empleado de Contoso. La ilustración de la izquierda representa el propietario del recurso (Contoso), al igual que el caso \#2 anterior. Pero esta vez el panel de interacción de la derecha no está bloqueado para Contoso, con el fin de transmitir que los usuarios externos pueden iniciar sesión.

![inicio de sesión en acceso abierto][3]

### <a name="scenario-4-fabrikam-business-guest-goes-to-contoso-app-thats-open-to-external-users"></a>Escenario 4: Un invitado de la empresa Fabrikam entra en una aplicación de Contoso abierta a usuarios externos

En este ejemplo, un usuario de Contoso está iniciando sesión en una aplicación interna mediante una dirección URL específica de la compañía. La ilustración de la izquierda representa la marca de la empresa (Contoso). El panel de interacción de la derecha está bloqueado para Contoso y ayuda a los empleados con el inicio de sesión.

![inicio de sesión como usuario externo][4]


## <a name="what-elements-on-the-page-can-i-customize"></a>¿Qué elementos de la página puedo personalizar?

Puede personalizar los siguientes elementos de la página de inicio de sesión:

![][5]

| Elemento de la página | Ubicación en la página |
|:--- | --- |
| Logotipo del banner | Se muestra en la parte superior derecha de la página. Reemplaza el logotipo de la aplicación una vez que se determine la organización del usuario (normalmente, después de que este escriba su nombre de usuario). |
| Ilustración de fondo | Se muestra como una imagen de sangrado completo en el lado izquierdo de la página de inicio de sesión. Reemplaza la ilustración de la aplicación, para los escenarios de inicio de sesión con inquilinos (cuando los usuarios acceden a una aplicación publicada por su propia organización o una organización donde están invitados).<br>En conexiones de ancho de banda reducido, la ilustración de fondo se reemplaza por un color de fondo. En las pantallas estrechas, como las de teléfonos, no se muestra la ilustración.<br>Se recortará la ilustración de fondo cuando los usuarios cambien el tamaño del explorador. Al diseñar la ilustración, mantenga los elementos visuales clave en la esquina superior izquierda para que no se recorten. | 
| Casilla "Mantener la sesión iniciada" | Se muestra bajo el cuadro de texto **Contraseña**. |
| Texto de la página de inicio de sesión | Texto reutilizable que se muestra encima del pie de página. Se puede utilizar para ofrecer información útil para los usuarios, como el número de teléfono del departamento de soporte técnico o una declaración legal. |

> [!NOTE]
> Todos los elementos son opcionales. Por ejemplo, si especifica un logotipo del banner pero no una ilustración de fondo, la página de inicio de sesión muestra su logotipo y la ilustración del sitio de destino (por ejemplo, la imagen de la autopista de California de Office 365).
>

En la página de inicio de sesión, la casilla **Mantener la sesión iniciada** permite que el usuario se mantenga conectado después de cerrar y abrir de nuevo el explorador; no afecta a la duración de la sesión.

El que la casilla se muestre o no depende de la configuración de **Ocultar KMSI**.

![Configuración Ocultar KMSI][6]

Para ocultar la casilla, utilice la opción **Oculto**.

> [!NOTE]
> Algunas características de SharePoint Online y Office 2010 dependen de que los usuarios puedan activar esta casilla. Si configura esta opción en Oculto, es posible que los usuarios reciban solicitudes adicionales e inesperadas de inicio de sesión.
>
>

También puede localizar todos los elementos de esta página. Una vez que haya configurado un conjunto de elementos de personalización "predeterminado", puede configurar más versiones para las diferentes configuraciones regionales. También puede mezclar y hacer coincidir varios elementos. Por ejemplo, puede:

* Crear una ilustración "predeterminada" que funcione para todas las culturas y, a continuación, crear versiones específicas para inglés y francés. Al establecer los navegadores en uno de estos dos idiomas, aparece la imagen específica, mientras que la ilustración predeterminada aparece en los demás idiomas.
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


## <a name="customizable-elements"></a>Elementos personalizables
Los logotipos de empresa se utilizan para las páginas de inicio de sesión y panel de acceso, mientras que otros elementos solo se utilizan en la página de inicio de sesión. En la tabla siguiente se proporcionan detalles para los diferentes elementos personalizables.

| Nombre | Description | Restricciones | Recomendaciones |
| --- | --- | --- | --- |
| Logotipo del banner |El logotipo del banner se muestra en la página de inicio de sesión y en el panel de acceso. |<p>JPG o PNG</p><p>60 x 280 píxeles</p><p>10 kB</p> |<p>Utilice el logotipo completo de la organización (incluido el pictograma y el logotipo).</p><p>Manténgalo por debajo de 30 píxeles para que no aparezcan barras de desplazamiento en los dispositivos móviles.</p><p>Manténgalo por debajo de 4 kB.</p><p>Utilice un PNG transparente (no debe presuponer que la página de inicio de sesión tiene un fondo blanco).</p> |
| Logotipo del icono | Actualmente no se usa. |<p>JPG o PNG</p><p>120 x 120 píxeles</p><p>10 kB</p> |<p>Conserve un diseño sencillo (sin texto pequeño), ya que el tamaño de esta imagen puede cambiar hasta en un 50%. |
| </p> | | | |
| Etiqueta de nombre de usuario de inicio de sesión | Actualmente no se usa. |<p>Texto Unicode, hasta 50 caracteres</p><p>Solo texto sin formato (sin vínculos ni etiquetas HTML)</p> |<p>Elija una etiqueta corta y sencilla.</p><p>Pregunte a los usuarios qué nombre utilizan para hacer referencia a la cuenta de la escuela o del trabajo que les proporciona.</p> |
| Texto reutilizable de página de inicio de sesión |Este texto reutilizable aparece debajo del formulario de la página de inicio de sesión y puede utilizarse para comunicar instrucciones adicionales o dónde acudir para obtener ayuda y soporte técnico. |<p>Texto Unicode, hasta 256 caracteres</p><p>Solo texto sin formato (sin vínculos ni etiquetas HTML)</p> |No sobrepase los 250 caracteres (aproximadamente 3 líneas de texto) |
| Ilustración de fondo de la página de inicio de sesión | Imagen grande que se muestra en el lado izquierdo de la página de inicio de sesión (en el derecho para los idiomas de derecha a izquierda) cuando los usuarios acceden a direcciones URL específicas del inquilino. |<p>JPG o PNG</p><p>1420 x 1200</p><p>500 kB</p> |<p>1420 x 1200 píxeles</p><p>Importante: manténgala lo más pequeña posible, lo ideal es que no sobrepase los 200 KB. Si esta imagen es demasiado grande, afecta al rendimiento de la página de inicio de sesión si la imagen no se almacena en caché</p><p>Esta imagen se recortará casi siempre para dar cabida a relaciones de aspecto de pantalla diferentes. Mantenga los elementos visuales principales en la esquina superior izquierda.</p> |
| Color de fondo de la página de inicio de sesión | En conexiones de ancho de banda reducido, se utiliza este color sólido en lugar de la ilustración de fondo. | Debe ser un color RGB en formato hexadecimal (ejemplo: \#FFFFFF). | Le recomendamos que utilice el color principal del logotipo del banner. |

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Active Directory Premium](active-directory-get-started-premium.md)
* [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/signin-page_before-customization.png
[2]: ./media/active-directory-add-company-branding/signin-page-restricted-app.png
[3]: ./media/active-directory-add-company-branding/signin-page-open-access.png
[4]: ./media/active-directory-add-company-branding/signin-page-external-guest.png
[5]: ./media/active-directory-add-company-branding/which-elements-can-i-customize.png
[6]: ./media/active-directory-add-company-branding/hide-kmsi.png
[8]: ./media/active-directory-add-company-branding/APBranding.png
[9]: ./media/active-directory-add-company-branding/hidekmsi.png

