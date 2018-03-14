---
title: "Uso de la personalización de idioma en Azure AD B2C | Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 02/26/2018
ms.author: sama
ms.openlocfilehash: 332c6b4ffd841a2c7aef9db5c8ba9e843790f4d6
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C: uso de la personalización de idioma

>[!NOTE]
>Esta característica está en versión preliminar pública.
>

La personalización de idioma permite que la directiva albergue distintos idiomas a fin de satisfacer las necesidades de los clientes.  Microsoft proporciona las traducciones de 36 idiomas (consulte [Información adicional](#additional-information)), pero el usuario también puede proporcionar sus propias traducciones para cualquier idioma.  Incluso si su experiencia solo se proporciona para un idioma, puede personalizar cualquier texto de las páginas.  

## <a name="how-does-language-customization-work"></a>¿Cómo funciona la personalización de idioma?
La personalización de idioma le permite seleccionar los idiomas en los que está disponible el recorrido de usuario.  Una vez que la característica está habilitada, puede proporcionar el parámetro de cadena de consulta, ui_locales, desde la aplicación.  Al llamar en Azure AD B2C, la página se traduce a la configuración regional que haya indicado.  Este tipo de configuración le proporciona un control completo sobre los idiomas del recorrido del usuario y omite la configuración de idioma del explorador del cliente. Como alternativa, quizás no necesite ese nivel de control sobre los idiomas que ve el cliente.  Si no proporciona un parámetro ui_locales, la experiencia del cliente está determinada por la configuración de su explorador.  Aun así, puede controlar los idiomas a los que se traduce el recorrido de usuario si los agrega como idioma admitido.  Si el explorador del cliente está establecido para mostrar un idioma que no desea admitir, se muestra en su lugar el idioma que ha seleccionado como predeterminado en las referencias culturales admitidas.

1. **Idioma especificado por ui-locales**: una vez que habilita la personalización de idioma, el recorrido de usuario se traduce al idioma especificado aquí.
2. **Idioma solicitado por el explorador**: si no se ha especificado ui-locales, se traduce al idioma solicitado por el explorador, **si se incluyó entre los idiomas admitidos**.
3. **Idioma predeterminado de directiva**: si el explorador no especifica un idioma, o si especifica uno que no se admite, se traduce al idioma predeterminado de la directiva.

>[!NOTE]
>Si va a usar atributos de usuario personalizados, debe proporcionar sus propias traducciones. Consulte "[Personalización de las cadenas](#customize-your-strings)" para más información.
>

## <a name="support-uilocales-requested-languages"></a>Compatibilidad con idiomas solicitados de ui_locales 
Las directivas que se crearon antes de la versión de disponibilidad general de personalización de idioma deberán habilitar primero esta característica.  Las directivas creadas después tendrán habilitada de forma predeterminada la personalización de idioma.  La habilitación de la personalización de idioma en una directiva le permite controlar ahora el idioma del recorrido de usuario agregando el parámetro ui_locales.
1. [Siga estos pasos para navegar a la página de características de B2C de Azure Portal.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. Navegue a la directiva que desea habilitar para las traducciones.
3. Haga clic en **Personalización de idioma**.  
4. Haga clic en **Habilitación de la personalización de idioma** en la parte superior.
5. Lea el cuadro de diálogo y haga clic en "Sí".

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>Seleccione los idiomas que están habilitados en su recorrido de usuario. 
Habilite un conjunto de idiomas para la traducción de su recorrido de usuario cuando no se proporcione el parámetro ui_locales.
1. Asegúrese de que la directiva tenga habilitada la personalización de idioma según las instrucciones anteriores.
2. En la página **Editar directiva**, seleccione **Personalización de idioma**.
3. Seleccione un idioma que quiera admitir.
4. En el panel de propiedades, cambie **Habilitado** a Sí.  
5. Haga clic en **Guardar** en la parte superior del panel de propiedades.

>[!NOTE]
>Si no se proporciona un parámetro ui_locales, la página se traduce al idioma del explorador del cliente solo si está habilitado.
>

## <a name="customize-your-strings"></a>Personalización de las cadenas
La personalización de idioma le permite personalizar cualquier cadena de su recorrido de usuario.
1. Asegúrese de que la directiva tenga habilitada la personalización de idioma según las instrucciones anteriores.
2. En la página **Editar directiva**, seleccione **Personalización de idioma**.
3. Seleccione el idioma que quiera personalizar.
4. Seleccione la página que quiere editar.
5. Haga clic en **Descargar valores predeterminados** (o **Descargar invalidaciones** si ha editado anteriormente este idioma). 

Estos pasos le proporcionan un archivo JSON que puede usar para comenzar a editar las cadenas.

### <a name="changing-any-string-on-the-page"></a>Cambio de cualquier cadena de la página
1. Abra el archivo JSON descargado según las instrucciones anteriores en un editor JSON.
2. Busque el elemento que desea cambiar.  Puede encontrar el valor de `StringId` de la cadena que busca o buscar el atributo `Value` que quiere cambiar.
3. Actualice el atributo `Value` con el que quiere que se muestre.
4. Para cada cadena que quiera modificar, no olvide cambiar `Override` a **True**.
5. Guarde el archivo y cargue los cambios (puede encontrar el control de carga en el mismo lugar que donde descargó el archivo JSON). 

>[!IMPORTANT]
>Si necesita reemplazar una cadena, asegúrese de establecer el valor `Override` en `true`.  Si no se cambia el valor, se omite la entrada. 
>

### <a name="changing-extension-attributes"></a>Cambio de los atributos de extensión
Si lo que busca es cambiar la cadena de un atributo de usuario personalizado, o quiere agregar una a JSON, debe estar en el siguiente formato:
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Reemplace `<ExtensionAttribute>` por el nombre de su atributo de usuario personalizado.  

Reemplace `<ExtensionAttributeValue>` por la nueva cadena que se mostrará.

### <a name="using-localizedcollections"></a>Uso de LocalizedCollections
Si desea proporcionar una lista establecida de valores para respuestas, debe crear un elemento `LocalizedCollections`.  `LocalizedCollections` es una matriz de pares de `Name` y `Value`.  `Name` es lo que se muestra y `Value` es lo que se devuelve en la notificación.  Para agregar un elemento `LocalizedCollections`, el formato es el siguiente:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` es el atributo de usuario para el que este elemento `LocalizedCollections` es una respuesta.
* `Name` es el valor que se muestra al usuario.
* `Value` es lo que se devuelve en la notificación cuando se selecciona esta opción.

### <a name="upload-your-changes"></a>Carga de los cambios
1. Una vez completados los cambios en el archivo JSON, vuelva al inquilino B2C.
2. En la página **Editar directiva**, seleccione **Personalización de idioma**.
3. Seleccione el idioma para el que quiere proporcionar traducciones.
4. Seleccione la página para la que quiere proporcionar traducciones.
5. Haga clic en el icono de carpeta y seleccione el archivo JSON para cargar.
6. Este cambio se guarda en la directiva automáticamente.

## <a name="using-page-ui-customization-with-language-customization"></a>Uso de personalización de la interfaz de usuario de página con personalización de idioma

Hay dos maneras de localizar el contenido HTML.  Activando la opción ["Personalización de idioma"](active-directory-b2c-reference-language-customization.md).  Al habilitar esta característica, Azure AD B2C puede reenviar el parámetro de OpenID Connect, `ui-locales`, al punto de conexión.  El servidor de contenido puede usar este parámetro para proporcionar páginas HTML personalizadas específicas del idioma.

Como alternativa, podemos extraer contenido de distintos lugares, según la configuración regional usada.  En el punto de conexión habilitado para CORS, puede configurar una estructura de carpetas para hospedar contenido de idiomas concretos, y se llamará al adecuado si coloca el valor de carácter comodín, `{Culture:RFC5646}`.  Por ejemplo, si tengo esto como mi URI de página personalizada:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Puedo cargar mi página en `fr` y cuando se extraiga contenido html y css, se extraerá de:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="custom-locales"></a>Configuraciones regionales personalizadas

También puede agregar idiomas para los que Microsoft no proporciona actualmente traducciones.  Deberá proporcionar las traducciones para todas las cadenas de la directiva.

1. En la página **Editar directiva**, seleccione **Personalización de idioma**.
2. Seleccione **Agregar un idioma personalizado** en la parte superior de la página.
3. En el panel de contexto que se abre, identifique el idioma para el que va a proporcionar traducciones mediante la especificación de un código de configuración regional válido.
4. Puede descargar para cada página un conjunto de invalidaciones para inglés y trabajar en las traducciones.
5. Cuando haya terminado con los archivos JSON, puede cargarlos para cada página.
6. Seleccione **Habilitar** y la directiva mostrará ahora este idioma para sus usuarios.
7. Recuerde guardar el idioma una vez que se ha habilitado.

## <a name="additional-information"></a>Información adicional

### <a name="page-ui-customization-labels-are-persisted-as-your-first-set-of-overrides-once-language-customization-is-enabled"></a>Las etiquetas de personalización de la interfaz de usuario de página se almacenan como el primer conjunto de invalidaciones una vez habilitada la personalización de idioma.
Cuando se habilita la personalización de idioma, las ediciones anteriores de etiquetas que usan la personalización de la interfaz de usuario de página se almacenan en un archivo JSON para inglés (en).  Para seguir cambiando las etiquetas y otras cadenas, cargue los recursos de idioma en "Personalización de idioma".
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>Microsoft se compromete a proporcionar las traducciones más actualizadas para que haga uso de ellas.
Mejoraremos continuamente las traducciones y garantizaremos su cumplimiento.  Identificaremos errores y cambios en la terminología global y crearemos actualizaciones que funcionen perfectamente en su recorrido de usuario.
### <a name="support-for-right-to-left-languages"></a>Compatibilidad con idiomas que se leen de derecha a izquierda
Actualmente no se admiten idiomas que se leen de derecha a izquierda. Si necesita esta característica, vótela en la sección de [comentarios de Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Traducciones de proveedores de identidades sociales
Aunque proporcionamos el parámetro ui_locales de OIDC para los inicios de sesión sociales, algunos proveedores de identidades sociales, como Facebook y Google, no lo respetan. 
### <a name="browser-behavior"></a>Comportamiento del explorador
Chrome y Firefox solicitan ambos su idioma configurado y, si es un idioma admitido, se muestra antes que el predeterminado.  Edge no solicita actualmente un idioma y va directamente al predeterminado.

### <a name="what-languages-are-supported"></a>¿Qué idiomas se admiten?

| Idioma              | Código de idioma |
|-----------------------|---------------|
| Bangla                | bn            |
| Checo                 | cs            |
| Danés                | da            |
| Alemán                | de            |
| Griego                 | el            |
| English               | en            |
| Español               | es            |
| Finés               | fi            |
| Francés                | fr            |
| Gujarati              | gu            |
| Hindi                 | hi            |
| Croata              | hr            |
| Húngaro             | hu            |
| Italiano               | it            |
| Japonés              | ja            |
| Kannada               | kn            |
| Coreano                | ko            |
| Malayalam             | ml            |
| Marathi               | mr            |
| Malayo                 | ms            |
| Noruego Bokmal      | nb            |
| Neerlandés                 | nl            |
| Punjabi               | pa            |
| Polaco                | pl            |
| Portugués (Brasil)   | pt-br         |
| Portugués (Portugal) | pt-pt         |
| Rumano              | ro            |
| Ruso               | ru            |
| Eslovaco                | sk            |
| Sueco               | sv            |
| Tamil                 | ta            |
| Telugu                | te            |
| Tailandés                  | th            |
| Turco               | tr            |
| Chino (simplificado)  | zh-hans       |
| Chino (tradicional) | zh-hant       |
