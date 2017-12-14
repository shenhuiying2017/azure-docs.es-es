---
title: 'Azure Active Directory B2C: directivas personalizadas | Microsoft Docs'
description: Un tema acerca de las directivas personalizadas de Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 1ff398a4-2079-4615-94f1-57de22c0aad6
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.openlocfilehash: 6c59075bb1eacb05599b23be3d8731fa40eabf98
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C: directivas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>¿Qué son las directivas personalizadas?

Las directivas personalizadas son archivos de configuración que definen el comportamiento del inquilino de Azure AD B2C. Mientras que las **directivas integradas** están predefinidas en el portal de Azure AD B2C para las tareas de identidad más comunes, un desarrollador de identidad puede editar completamente las directivas personalizadas para completar un número casi ilimitado de tareas. Siga leyendo para determinar si las directivas personalizadas son adecuadas para usted y su escenario de identidad.

## <a name="comparing-built-in-policies-and-custom-policies"></a>Comparación entre directivas integradas y directivas personalizadas

| | Directivas integradas | Directivas personalizadas |
|-|-------------------|-----------------|
|Usuarios de destino | Todos los desarrolladores de aplicaciones con o sin conocimientos sobre la identidad | Profesionales de identidad: integradores de sistemas, consultores y equipos internos de identidad. Se sienten cómodos con los flujos de OpenIDConnect y comprenden a los proveedores de identidades y la autenticación basada en notificaciones |
|Método de configuración | Azure Portal con una interfaz de usuario fácil de usar | Edición directa de archivos XML y posterior carga en Azure Portal |
|Personalización de la interfaz de usuario | Personalización completa de la interfaz de usuario, incluida compatibilidad con HTML, CSS y javascript (requiere un dominio personalizado)<br><br>Compatibilidad multilingüe con cadenas personalizadas | Iguales |
| Personalización de atributos | Atributos estándar y personalizados | Iguales |
|Administración de tokens y sesiones | Varias opciones de sesiones y tokens personalizados | Iguales |
|Proveedores de identidades| **Actualmente**: proveedor social local predefinido<br><br>**En el futuro**: OIDC, SAML, OAuth basado en estándares | **Actualmente**: OIDC, OAUTH, SAML basado en estándares<br><br>**En el futuro**: WsFed |
|Tareas de identidad (ejemplos) | Registro o inicio de sesión con muchas cuentas sociales y cuentas locales<br><br>Restablecimiento de contraseña de autoservicio<br><br>Edición de perfil<br><br>Escenarios de Multi-Factor Authentication<br><br>Sesiones y tokens personalizados<br><br>Flujos de token de acceso | Completar las mismas tareas que con las directivas integradas usando proveedores de identidades personalizados o usar ámbitos personalizados<br><br>Aprovisionar el usuario en otro sistema en el momento del registro<br><br>Enviar un mensaje de bienvenida con su propio proveedor de servicios de correo electrónico<br><br>Usar un almacén de usuario externo a B2C<br><br>Validar la información proporcionada por el usuario con un sistema de confianza a través de API |

## <a name="policy-files"></a>Archivos de directivas

Una directiva personalizada se representa como uno o varios archivos con formato XML que se hacen referencia entre sí en una cadena jerárquica. Los elementos XML definen lo siguiente: esquema de notificaciones, transformaciones de notificaciones, definiciones de contenido, perfiles técnicos/proveedores de notificaciones y pasos de orquestación de Userjourney, entre otros elementos.

Se recomienda usar tres tipos de archivos de directivas:

- **Un archivo BASE**, que contiene la mayoría de las definiciones y para las cuales Azure proporciona un ejemplo completo.  Se recomienda que haga un mínimo de cambios en este archivo para facilitar la solución de problemas y el mantenimiento a largo plazo de las directivas
- **Un archivo EXTensions** que contiene los cambios de configuración únicos para el inquilino
- **Un archivo de usuario de confianza**, que es el archivo centrado en una tarea única que la aplicación o el servicio (también conocido como usuario de confianza) invoca directamente.  Lea el artículo sobre las definiciones de archivos de directivas para más información.  Cada tarea única requiere su propio usuario de confianza y, dependiendo de los requisitos de marca, el número podría ser "total de aplicaciones x número total de casos de uso".


Las directivas integradas de Azure AD B2C siguen el patrón de 3 archivos ya mencionado, pero el desarrollador solo ve el archivo de usuario de confianza mientras que el portal hace cambios en el archivo EXTensions en segundo plano.

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>Conceptos básicos que debe saber cuando usa las directivas personalizadas

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

Servicio de administración de identidades y acceso de cliente (CIAM) de Azure. El servicio incluye:

1. Un directorio de usuarios como instancia de Azure Active Directory especial accesible a través de Microsoft Graph y que contiene los datos de usuario tanto para cuentas locales como cuentas federadas 
2. Acceso a **Identity Experience Framework** que orquesta la confianza entre usuarios y entidades y transmite notificaciones entre ellas para completar una tarea de administración de identidades/acceso 
3. Un servicio de token de seguridad (STS) que emite tokens de identificación, tokens de actualización y tokens de acceso (y aserciones SAML equivalentes) y los valida con el fin de proteger los recursos.

Azure AD B2C interactúa con proveedores de identidades, usuarios, otros sistemas y con el directorio de usuarios locales en secuencia para realizar una tarea de identidad (por ejemplo, iniciar la sesión de un usuario, registrar un usuario nuevo, restablecer una contraseña). La plataforma subyacente que establece una confianza entre varios usuarios y ejecuta estos pasos se llama Identity Experience Framework; además, una directiva (que también se conoce como una directiva de marco de confianza o de recorrido del usuario) define explícitamente los actores, las acciones, los protocolos y la secuencia de pasos que se van a completar.

### <a name="identity-experience-framework"></a>Marco de experiencia de identidad

Una plataforma de Azure basada en la nube, controlada por directivas y completamente configurable que orquesta la confianza entre entidades (en general, proveedores de confianza) en formatos de protocolo estándar como OpenIDConnect, OAuth, SAML, WSFed y algunos no estándar (como intercambios de notificaciones sistema a sistema basados en API de REST, por ejemplo). I2E crea experiencias propias fáciles de usar que admiten HTML, CSS y javascript.  Actualmente, Identity Experience Framework solo está disponible dentro del contexto del servicio de Azure AD B2C y se destina preferentemente a las tareas relacionadas con CIAM.

### <a name="built-in-policies"></a>Directivas integradas

Archivos de configuración predefinidos que indiquen el comportamiento de Azure AD B2C para realizar de forma más habitual las tareas de identidad (es decir, registro de usuario, inicio de sesión, restablecimiento de contraseña) e interactuar con entidades de confianza cuya relación también está predefinida en Azure AD B2C (por ejemplo, proveedor de identidad de Facebook, LinkedIn, Microsoft Account, cuentas de Google).  En el futuro, también se pueden proporcionar directivas integradas para la personalización de los proveedores de identidades, que habitualmente están en el dominio empresarial, como Azure Active Directory Premium, Active Directory/ADFS, proveedor de identidades de Salesforce, etc.


### <a name="custom-policies"></a>Directivas personalizadas

Los archivos de configuración que definen el comportamiento de Identity Experience Framework en el inquilino de Azure AD B2C. Una directiva personalizada es accesible como uno o varios archivos XML (consulte las definiciones de archivos de directivas) que Identity Experience Framework ejecuta cuando lo invoca un usuario de confianza (por ejemplo, una aplicación). Un desarrollador de identidades puede editar directamente las directivas personalizadas para completar un número casi ilimitado de tareas. Los desarrolladores que configuran las directivas personalizadas deben definir las relaciones de confianza con mucho detalle para incluir puntos de conexión de metadatos, definiciones exactas de intercambio de notificaciones, además de configurar secretos, claves y certificados según lo necesite cada proveedor de identidades.

## <a name="policy-file-definitions-for-identity-experience-framework-trustframeworks"></a>Definiciones de archivos de directivas para marcos de confianza de Identity Experience Framework

### <a name="policy-files"></a>Archivos de directivas

Una directiva personalizada se representa como uno o varios archivos con formato XML que se hacen referencia entre sí en una cadena jerárquica. Los elementos XML definen lo siguiente: esquema de notificaciones, transformaciones de notificaciones, definiciones de contenido, perfiles técnicos/proveedores de notificaciones y pasos de orquestación del recorrido del usuario, entre otros elementos.  Se recomienda usar tres tipos de archivos de directivas:

- **Un archivo BASE** contiene la mayoría de las definiciones y para las cuales Azure proporciona un ejemplo completo.  Se recomienda que haga un mínimo de cambios en este archivo para facilitar la solución de problemas y el mantenimiento a largo plazo de las directivas
- **Un archivo EXTensions** que contiene los cambios de configuración únicos para el inquilino
- **Un archivo de usuario de confianza (RP)**  es el archivo centrado en una tarea única que la aplicación o el servicio (también conocido como usuario de confianza) invoca directamente.  Lea el artículo sobre las definiciones de archivos de directivas para más información.  Cada tarea única requiere su propio usuario de confianza y, dependiendo de los requisitos de marca, el número podría ser "total de aplicaciones x número total de casos de uso".

![Tipos de archivos de directivas](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| Tipo de archivo de directiva | Ejemplos de nombre de archivo | Uso recomendado | Hereda de |
|---------------------|--------------------|-----------------|---------------|
| BASE |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | Incluye el esquema de notificaciones central, las transformaciones de notificaciones, los proveedores de notificaciones y los recorridos de usuario que Microsoft configura<br><br>Haga un mínimo de cambios en este archivo | None |
| Extensión (EXT) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | Aquí consolide los cambios en el archivo BASE<br><br>Proveedores de notificaciones modificadas<br><br>Recorridos de usuario modificados<br><br>Sus propias definiciones de esquemas personalizados | Archivo BASE |
| Usuario de confianza | B2C_1A_sign_up_sign_in.xml| Cambiar aquí la configuración de la sesión y la forma del token| Archivo Extensions(EXT) |

### <a name="inheritance-model"></a>Modelo de herencia

Cuando una aplicación llama al archivo de directiva de usuario de confianza, la instancia de Identity Experience Framework en B2C llamará a todos los elementos desde BASE, luego desde EXTENSIONS y, por último, desde el archivo de directiva de usuario de confianza para ensamblar la directiva actual en vigor.  Los elementos del mismo tipo y nombre en el archivo de usuario de confianza reemplazarán los existentes en EXTENSIONS y EXTENSIONS reemplazará a BASE.

Las **directivas integradas** de Azure AD B2C siguen el patrón de 3 archivos ya mencionado, pero el desarrollador solo ve el archivo de usuario de confianza mientras que el portal hace cambios en el archivo EXTensions en segundo plano.  Todas las instancias de Azure AD B2C comparten un archivo de directiva BASE que el equipo de Azure B2C controla y que se actualiza con frecuencia.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md)
