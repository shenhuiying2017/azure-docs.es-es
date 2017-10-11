---
title: "Azure Active Directory B2C: uso de la personalización de idioma | Microsoft Docs"
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
ms.date: 04/25/2017
ms.author: sama
ms.openlocfilehash: 3c7c49ee5fbd98762da0eef6f02e7c2f036453cb
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C: uso de la personalización de idioma

>[!NOTE] 
>Esta característica está en versión preliminar pública.  Se recomienda usar un inquilino de prueba cuando se utilice esta característica.  Aunque no tenemos pensado realizar ningún cambio importante entre la versión preliminar y la versión de disponibilidad general, nos reservamos el derecho de realizar dichos cambios para mejorar la característica.  Cuando haya tenido la oportunidad de probar la característica, envíenos sus comentarios sobre sus experiencias y cómo podemos mejorarla.  Puede proporcionar comentarios a través del portal de Azure con la herramienta de cara sonriente de la esquina superior derecha.   Si existe el requisito empresarial de trabajar con esta característica durante la fase de versión preliminar, coméntenos su situación y le podremos proporcionar las instrucciones y ayuda que necesita.  Puede ponerse en contacto con nosotros en [aadb2cpreview@microsoft.com](mailto:aadb2cpreview@microsoft.com).

La personalización de idioma le permite cambiar su recorrido de usuario por un idioma diferente para adaptarlo a las necesidades de su cliente.  Proporcionamos traducciones en 36 idiomas (vea [Información adicional](#additional-information)).  Incluso si su experiencia solo se proporciona para un idioma, puede personalizar cualquier texto de las páginas para adaptarlo a sus necesidades.  

## <a name="how-does-language-customization-work"></a>¿Cómo funciona la personalización de idioma?
La personalización de idioma le permite seleccionar los idiomas en los que está disponible el recorrido de usuario.  Una vez que la característica está habilitada, puede proporcionar el parámetro de cadena de consulta, ui_locales, desde la aplicación.  Al llamar en Azure AD B2C, la página se traduce a la configuración regional que haya indicado.  El uso del tipo de configuración le proporciona un control completo sobre los idiomas del recorrido de usuario y omite la configuración de idioma del explorador del cliente. Como alternativa, quizás no necesite ese nivel de control sobre los idiomas que ve el cliente.  Si no proporciona un parámetro ui_locales, la experiencia del cliente está determinada por la configuración de su explorador.  Aun así, puede controlar los idiomas a los que se traduce el recorrido de usuario si los agrega como idioma admitido.  Si el explorador del cliente está establecido para mostrar un idioma que no desea admitir, se muestra en su lugar el idioma que ha seleccionado como predeterminado en las referencias culturales admitidas.

1. **Idioma especificado por ui-locales**: una vez que habilita la personalización de idioma, el recorrido de usuario se traduce al idioma especificado aquí.
2. **Idioma solicitado por el explorador**: si no se ha especificado ui-locales, se traduce al idioma solicitado por el explorador, **si se incluyó entre los idiomas admitidos**.
3. **Idioma predeterminado de directiva**: si el explorador no especifica un idioma, o si especifica uno que no se admite, se traduce al idioma predeterminado de la directiva.

>[!NOTE]
>Si va a usar atributos de usuario personalizados, debe proporcionar sus propias traducciones. Consulte "[Personalización de las cadenas](#customize-your-strings)" para más información.
>

## <a name="support-uilocales-requested-languages"></a>Compatibilidad con idiomas solicitados de ui_locales 
La habilitación de la personalización de idioma en una directiva le permite controlar ahora el idioma del recorrido de usuario agregando el parámetro ui_locales.
1. [Siga estos pasos para ir a la hoja de características de B2C del portal de Azure.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. Navegue a la directiva que desea habilitar para las traducciones.
3. Haga clic en **Personalización de idioma**.
4. Lea la advertencia atentamente.  Una vez habilitada, no puede desactivar la personalización de idioma.
5. Active la característica y haga clic en **Aceptar**.
6. Guarde la directiva en la esquina superior izquierda de su hoja **Editar directiva**.

## <a name="select-which-languages-your-user-journey-supports"></a>Selección de los idiomas que admite el recorrido de usuario 
Cree una lista de idiomas permitidos para la traducción de su recorrido de usuario cuando no se proporcione el parámetro ui_locales.
1. Asegúrese de que la directiva tenga habilitada la personalización de idioma según las instrucciones anteriores.
2. En la hoja **Editar directiva**, seleccione **Personalización de idioma**.
3. Esta acción le llevará a la hoja **Idiomas admitidos**.  Desde aquí, puede seleccionar **Agregar idioma**.
4. Seleccione todos los idiomas que le gustaría admitir.  

>[!NOTE]
>Si no se proporciona un parámetro ui_locales, la página se traduce entonces al idioma del explorador del cliente solo si está en esta lista.
>

5. Haga clic en **Aceptar** en la parte inferior.
6. Cierre la hoja **Personalización de idioma** y **guarde** la directiva.

## <a name="customize-your-strings"></a>Personalización de las cadenas
La personalización de idioma le permite personalizar cualquier cadena de su recorrido de usuario.
1. Asegúrese de que la directiva tenga habilitada la personalización de idioma según las instrucciones anteriores.
2. En la hoja **Editar directiva**, seleccione **Personalización de idioma**.
3. En el menú de navegación izquierdo, seleccione **Download content** (Descargar contenido).
4. Seleccione la página que quiere editar.
5. En la lista desplegable, seleccione el idioma de la edición.
6. Haga clic en **Descargar**. 

Estos pasos le proporcionan un archivo JSON que puede usar para comenzar a editar las cadenas.

### <a name="changing-any-string-on-the-page"></a>Cambio de cualquier cadena de la página
1. Abra el archivo JSON descargado según las instrucciones anteriores en un editor JSON.
2. Busque el elemento que desea cambiar.  Puede encontrar el valor de `StringId` de la cadena que busca o buscar el atributo `Value` que quiere cambiar.
3. Actualice el atributo `Value` con el que quiere que se muestre.
4. Guarde el archivo y cargue los cambios.

### <a name="changing-extension-attributes"></a>Cambio de los atributos de extensión
Si lo que busca es cambiar la cadena de un atributo de usuario personalizado, o quiere agregar una a JSON, debe estar en el siguiente formato:
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": false,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```
>[!IMPORTANT]
>Si necesita reemplazar una cadena, asegúrese de establecer el valor `Override` en `true`.  Si no se cambia el valor, se omite la entrada. 
>

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
      "Override": false,
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
>[!IMPORTANT]
>Si necesita reemplazar una cadena, asegúrese de establecer el valor `Override` en `true`.  Si no se cambia el valor, se omite la entrada. 
>

* `ElementId` es el atributo de usuario para el que este elemento `LocalizedCollections` es una respuesta.
* `Name` es el valor que se muestra al usuario.
* `Value` es lo que se devuelve en la notificación cuando se selecciona esta opción.

### <a name="upload-your-changes"></a>Carga de los cambios
1. Una vez completados los cambios en el archivo JSON, vuelva al inquilino B2C.
2. En la hoja **Editar directiva**, seleccione **Personalización de idioma**.
3. En el menú de navegación izquierdo, seleccione **Cargar contenido**.
4. Seleccione la página para la que desea cargar los cambios.
5. Si quiere cargar un idioma para el que ha proporcionado anteriormente un archivo JSON, debe eliminar la entrada anterior.  Para eliminarla, haga clic en `...` a la derecha de dicho idioma y seleccione **Eliminar**.
6. Haga clic en **Agregar** en la parte superior izquierda.
7. Seleccione el idioma del archivo JSON.
8. Haga clic en el botón de la carpeta a la derecha y busque el archivo JSON.
9. Haga clic en el botón **Cargar** en la parte inferior de la hoja.
10. Vuelva a su hoja **Editar directiva** y haga clic en **Guardar**.

## <a name="additional-information"></a>Información adicional
### <a name="recommendations-for-language-customization"></a>Recomendaciones para la personalización de idioma
Se recomienda colocar solo entradas para los recursos de idioma de las cadenas que vaya a reemplazar explícitamente.  Se impone un límite de tamaño para el archivo que se compila de todas las traducciones JSON.  Si los archivos se vuelven demasiado grandes, el rendimiento del recorrido de usuario resultará afectado.
### <a name="page-ui-customization-labels-are-removed-once-language-customization-is-enabled"></a>Las etiquetas de personalización de la IU de página se quitan una vez habilitada la personalización de idioma
Cuando se habilita la personalización de idioma, las ediciones anteriores de etiquetas que usan la personalización de IU de página se quitan, excepto para los atributos de usuario personalizados.  Este cambio se realiza para evitar conflictos allí donde puede editar las cadenas.  Para seguir cambiando las etiquetas y otras cadenas, cargue los recursos de idioma en "Personalización de idioma".
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>Microsoft se compromete a proporcionar las traducciones más actualizadas para que haga uso de ellas.
Mejoraremos continuamente las traducciones y garantizaremos su cumplimiento.  Identificaremos errores y cambios en la terminología global y crearemos actualizaciones que funcionen perfectamente en su recorrido de usuario.
### <a name="support-for-right-to-left-languages"></a>Compatibilidad con idiomas que se leen de derecha a izquierda
No hay compatibilidad con los idiomas que se leen de derecha a izquierda. Si necesita esta característica, vótela en [Comentarios de Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Traducciones de proveedores de identidades sociales
Aunque proporcionamos el parámetro ui_locales de OIDC para los inicios de sesión sociales, algunos proveedores de identidades sociales, como Facebook y Google, no lo respetan. 
### <a name="browser-behavior"></a>Comportamiento del explorador
Chrome y Firefox solicitan ambos su idioma configurado y, si es un idioma admitido, se muestra antes que el predeterminado.  Edge no solicita actualmente un idioma y va directamente al predeterminado.
### <a name="known-issues"></a>Problemas conocidos
* La carga de recursos de idioma para la página de MFA en una directiva de edición de perfil no está actualmente disponible.
* No se generan `LocalizedCollections` para valores cuando el tipo de respuesta las requiere.
### <a name="what-if-i-want-a-language-that-isnt-supported"></a>¿Y si quiero un idioma que no se admite?
Tenemos previsto proporcionar una extensión de esta característica que permita cargar un recurso JSON hacia "idiomas personalizados".  La característica le permite especificar el nombre y el código de idioma de cualquier idioma y proporcionar *todas* las traducciones para dicho idioma.  Si necesita esta característica, envíenos su escenario a [ aadb2cpreview@microsoft.com](mailto:aadb2cpreview@microsoft.com).  

### <a name="what-languages-are-supported"></a>¿Qué idiomas se admiten?

| language              | Código de idioma |
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
