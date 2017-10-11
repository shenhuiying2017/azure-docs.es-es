---
title: "Azure Active Directory B2C: solución de problemas de directivas personalizadas | Microsoft Docs"
description: "Obtenga información sobre los métodos para resolver errores cuando se trabaja con directivas personalizadas en Azure Active Directory."
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: joroja
ms.openlocfilehash: 023390ba36a74217503ff8b3076ad17978fe3fb8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Solución de problemas de directivas personalizadas de Azure AD B2C y el marco de experiencia de identidad

Si usa directivas personalizadas de Azure Active Directory B2C (Azure AD B2C), es posible que experimente problemas al configurar el marco de experiencia de identidad en el formato XML de su lenguaje de directiva.  Aprender a escribir directivas personalizadas es casi como aprender un idioma nuevo. En este artículo se describen las herramientas y las sugerencias que pueden ayudarle a detectar y solucionar problemas rápidamente. 

> [!NOTE]
> Este artículo se centra en la solución de problemas de configuración de directivas personalizadas de Azure AD B2C. No trata sobre las aplicaciones de usuarios de confianza ni sobre su biblioteca de identidades.

## <a name="xml-editing"></a>Edición de XML

El error más común al configurar directivas personalizadas es el formato incorrecto del código XML. Es fundamental contar con un buen editor XML. Un buen editor XML muestra XML nativo y contenido codificado por colores, rellena previamente términos comunes, mantiene los elementos XML indexados y puede validar con esquemas. Dos de nuestros editores XML favoritos son:

* [código de Visual Studio](https://code.visualstudio.com/)
* [Notepad++](https://notepad-plus-plus.org/)

La validación del esquema XML identifica los errores antes de cargar el archivo XML. En la carpeta raíz del paquete de inicio, obtenga la definición de esquema XML TrustFrameworkPolicy_0.3.0.0.xsd. Para obtener más información, en la documentación de su editor XML, busque *herramientas de XML* y *validación de XML*.

Podría resultarle útil revisar las reglas del código XML. Azure AD B2C rechaza todos los errores de formato XML que detecta. En ocasiones, un formato XML incorrecto puede dar lugar a mensajes de error que resultan engañosos.

## <a name="upload-policies-and-policy-validation"></a>Validación de directivas y directivas de carga

 La validación de la carga de archivos XML es automática. La mayoría de los errores hacen que se produzca un error en la carga. En la validación se incluye el archivo de directiva que se está cargando. Además, se incluye la cadena de archivos a la que hace referencia el archivo de carga (el archivo de directiva de usuario de confianza, el archivo de extensiones y el archivo base). 
 
 Entre los errores de validación comunes se incluyen los que se indican a continuación.

Fragmento de código de error: `... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* El valor de ClaimType podría estar mal escrito o no existir en el esquema.
* Se deben definir valores de ClaimType en al menos uno de los archivos de la directiva. 
    Por ejemplo: ` <ClaimType Id="socialIdpUserId">`
* Si ClaimType se define en el archivo de extensiones, pero también se usa en el valor de TechnichalProfile en el archivo base, al cargar dicho archivo se producirá un error.

Fragmento de código de error: `...makes a reference to a ClaimsTransformation with id...`
* Las causas del error podrían ser las mismas que las del error de ClaimType.

Fragmento de código de error: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* Compruebe que el valor de TenantId de los elementos **\<TrustFrameworkPolicy\>** y **\<BasePolicy\>** coincide con el inquilino de Azure AD B2C de destino.  

## <a name="troubleshoot-the-runtime"></a>Solución de problemas del tiempo de ejecución

* Use `Run Now` y `https://jwt.io` para probar sus directivas con independencia de su aplicación web o móvil. Este sitio web actúa como una aplicación de usuario de confianza. Muestra el contenido del JSON Web Token (JWT) que genera la directiva de Azure AD B2C. Para crear una aplicación de prueba en el marco de experiencia de identidad, use los valores siguientes:
    * Nombre: TestApp
    * Aplicación web/API web: No
    * Cliente nativo: No

* Use [Fiddler](http://www.telerik.com/fiddler) para realizar un seguimiento del intercambio de mensajes entre su explorador cliente y Azure AD B2C. Puede ayudarle a detectar un error en el recorrido del usuario en los pasos de la orquestación.

* Use **Application Insights** en **modo de desarrollo** para realizar un seguimiento de la actividad de su recorrido del usuario en el marco de experiencia de identidad. En el **modo de desarrollo**, es posible observar el intercambio de notificaciones entre el maco de experiencia de identidad y los diversos proveedores de notificaciones definidos por perfiles técnicos, como proveedores de identidades, servicios de basados en API, el directorio de usuario de Azure AD B2C y otros servicios como Azure Multi-Factor-Authentication.  

## <a name="recommended-practices"></a>Procedimientos recomendados

**Mantenga varias versiones de los escenarios. Agrúpelas en un proyecto con la aplicación.** Los archivos base, de extensiones y de usuario de confianza dependen directamente los unos de los otros. Guárdelos como un grupo. A medida que se agreguen nuevas características a las directivas, mantenga versiones operativas distintas. Organice las versiones operativas en su propio sistema de archivos con el código de aplicación con el que interactúan.  Las aplicaciones pueden invocar muchas directivas de usuario de confianza diferentes en un inquilino. Pueden convertirse en dependientes de las notificaciones que esperan de sus directivas de Azure AD B2C.

**Desarrolle y pruebe perfiles técnicos con recorridos de usuario conocidos.** Use directivas probadas del paquete de inicio para configurar los perfiles técnicos. Pruébelas por separado antes de incorporarlas a sus propios recorridos de usuario.

**Desarrolle y pruebe recorridos de usuario con perfiles técnicos conocidos.** Cambie los pasos de orquestación de un recorrido del usuario de forma incremental. Compile progresivamente los escenarios previstos.

## <a name="next-steps"></a>Pasos siguientes

* En GitHub, descargue el archivo ZIP [active-directory-b2c-custom-policy-starterpack] (https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip).
