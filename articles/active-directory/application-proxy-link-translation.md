---
title: "Traducción de vínculos y direcciones URL del Proxy de aplicación de Azure AD | Microsoft Docs"
description: "Se explican los conceptos básicos acerca de los conectores del Proxy de aplicación de Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: d716476deb81f6627cf03401f78ab399ae940e68
ms.contentlocale: es-es
ms.lasthandoff: 07/04/2017

---

# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Redirección de los vínculos codificados de manera rígida para las aplicaciones publicadas con el Proxy de aplicación de Azure AD

El Proxy de aplicación de Azure AD permite que las aplicaciones locales estén disponibles para los usuarios remotos o en sus propios dispositivos. Sin embargo, algunas aplicaciones se desarrollaron pensando en la audiencia local, lo que habitualmente significa que los vínculos locales se insertan en el HTML y no funcionan correctamente cuando la aplicación se usa de forma remota. Esto es muy habitual cuando varias aplicaciones locales se hacen referencia entre sí y los usuarios esperan usar vínculos para moverse entre las aplicaciones en lugar de escribir la dirección URL externa de cada aplicación.

La característica de traducción de vínculos para el proxy de aplicación se desarrolló para solucionar este problema. Cuando tiene aplicaciones que apunta directamente a puertos o puntos de conexión internos, puede asignar estas direcciones URL internas a las direcciones URL del proxy de aplicación externas publicadas. Habilite la traducción de vínculos y el proxy de aplicación busca a través de HTML, CSS, selecciona etiquetas de JavaScript para vínculos internos publicados y, luego, los traduce para que los usuarios reciban una experiencia sin interrupciones.

>[!NOTE]
>La característica de traducción de vínculos está pensada para inquilinos que, por cualquier motivo, no pueden usar dominios personalizados para tener las mismas direcciones URL internas y externas para sus aplicaciones. Antes de habilitar esta característica, vea si los [dominios personalizados en el Proxy de aplicación de Azure AD](active-directory-application-proxy-custom-domains.md) son adecuados para usted.

## <a name="how-link-translation-works"></a>Funcionamiento de la traducción de vínculos

Después de la autenticación, cuando el servidor proxy pasa los datos de aplicación al usuario, el Proxy de aplicación busca vínculos codificados de manera rígida en la aplicación y los reemplaza por sus direcciones URL externas publicadas correspondientes.

### <a name="which-links-are-affected"></a>¿Qué vínculos se ven afectados?

La característica de traducción de vínculos solo busca los vínculos que estén en las etiquetas de código del cuerpo de una aplicación. El Proxy de aplicación tiene una característica independiente para traducir cookies o direcciones URL en los encabezados. 

Hay dos tipos comunes de vínculos internos en aplicaciones locales:

- **Vínculos internos relativos** que apuntan a un recurso compartido en una estructura de archivos local como `/claims/claims.html`. Estos vínculos funcionan automáticamente en aplicaciones que se publican mediante el Proxy de aplicación y seguirán funcionando con o sin traducción de vínculos. 
- **Vínculos internos codificados de manera rígida** a otras aplicaciones locales como `http://expenses` o archivos publicados como `http://expenses/logo.jpg`. La característica de traducción de vínculos funciona en vínculos internos codificados de manera rígida y los modifica para que apunten a las direcciones URL por las que deben pasar los usuarios remotos.

### <a name="how-do-apps-link-to-each-other"></a>¿Cómo se vinculan entre sí las aplicaciones?

La traducción de vínculos está habilitada para cada aplicación, para que se tenga control sobre la experiencia del usuario en el nivel por aplicación. Active la traducción de vínculos para una aplicación cuando desee que se traduzcan los vínculos *desde* esa aplicación y no los vínculos *hacia* ella. 

Por ejemplo, imagine que tiene tres aplicaciones publicadas a través del Proxy de aplicación que se vinculan entre sí: Beneficios, Gastos y Viajes. Existe una cuarta aplicación, Comentarios, que no se publica mediante el Proxy de aplicación.

Cuando habilita la traducción de vínculos para la aplicaciones Beneficios, los vínculos a Gastos y Viajes se redirigen a las direcciones URL externas de esas aplicaciones, pero el vínculo a Comentarios no se redirige porque no hay ninguna dirección URL externa. Los vínculos de Gastos y Viajes a Beneficios no funcionan, porque la traducción de vínculos no está habilitada para esas dos aplicaciones.

![Vínculos desde Beneficios a las otras aplicaciones cuando está habilitada la traducción de vínculos](./media/application-proxy-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>¿Qué vínculos no se traducen?

Para mejorar el rendimiento y la seguridad, no se traducen algunos vínculos:

- Los vínculos que no están dentro de las etiquetas de código. 
- Los vínculos internos que se abren desde otros programas. No se traducirán los vínculos que se envían a través de correos electrónicos o mensajes instantáneos o que se incluyen en otros documentos. Los usuarios deben saber ir a la dirección URL externa.

Si necesita admitir uno de estos dos escenarios, puede usar las mismas direcciones URL internas y externas, lo que elimina la necesidad de traducir los vínculos.  

## <a name="enable-link-translation"></a>Habilitación de la traducción de vínculos

Comenzar a trabajar con la traducción de vínculos es tan fácil como hacer clic en un botón:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador.
2. Vaya a **Azure Active Directory** > **Aplicaciones empresariales** > **Todas las aplicaciones** > seleccione la aplicación que desea administrar > **Proxy de aplicación**.
3. Establezca **Translate URLs in application body** (Traducir direcciones URL en el cuerpo de la aplicación) en **Sí**.

   ![Seleccionar Sí para traducir las direcciones URL en el cuerpo de la aplicación](./media/application-proxy-link-translation/select_yes.png).
4. Seleccione **Guardar** para aplicar los cambios.

Ahora, cuando los usuarios accedan a esta aplicación, el proxy examinará automáticamente las direcciones URL que se hayan publicado a través del Proxy de aplicación en el inquilino.

## <a name="send-feedback"></a>Envío de comentarios

Queremos que nos ayude a hacer que esta característica funcione para todas las aplicaciones. Buscamos en más de 30 etiquetas en HTML y CSS y estamos evaluando qué casos de JavaScript admitir. Si tiene un ejemplo de vínculos generados que no está traducido, envíe un fragmento de código a [Comentarios del Proxy de aplicación](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Pasos siguientes
- [Uso de dominios personalizados con el Proxy de aplicación de Azure AD para tener la misma dirección URL interna y externa](active-directory-application-proxy-custom-domains.md)

