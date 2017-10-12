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
ms.date: 08/10/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 57218346d236b376d2227e0ffaea6c6dd5ebe855
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Redirección de los vínculos codificados de manera rígida para las aplicaciones publicadas con el Proxy de aplicación de Azure AD

El Proxy de aplicación de Azure AD permite que las aplicaciones locales estén disponibles para los usuarios remotos o en sus propios dispositivos. Algunas aplicaciones, sin embargo, se desarrollaron con vínculos locales insertados en el código HTML. Estos vínculos no funcionan correctamente si la aplicación se usa de forma remota. Cuando tiene varias aplicaciones locales que se señalan entre sí, sus usuarios esperan que los vínculos sigan funcionando mientras no se encuentran en la oficina. 

La mejor forma de asegurarse de que los vínculos funcionan del mismo modo tanto dentro como fuera de la red corporativa es configurar las direcciones URL externas de sus aplicaciones para que sean iguales que las direcciones URL internas. Use [dominios personalizados](active-directory-application-proxy-custom-domains.md) para configurar las direcciones URL externas de modo que tengan su nombre de dominio corporativo en lugar del dominio del proxy de aplicación predeterminado.

Si no puede usar dominios personalizados en el inquilino, la característica de traducción de vínculos del Proxy de aplicación hará que sus vínculos sigan funcionando independientemente de dónde se encuentren los usuarios. Cuando tiene aplicaciones que apunta directamente a puertos o puntos de conexión internos, puede asignar estas direcciones URL internas a las direcciones URL del proxy de aplicación externas publicadas. Cuando se habilita la traducción de vínculos y el Proxy de aplicación busca a través de etiquetas de JavaScript selectas, CSS y HTML para vínculos internos publicados. A continuación, el servicio de Proxy de aplicación los traduce para que sus usuarios tengan una experiencia sin interrupciones.

>[!NOTE]
>La característica de traducción de vínculos es para inquilinos que, por cualquier motivo, no pueden usar dominios personalizados para tener las mismas direcciones URL internas y externas para sus aplicaciones. Antes de habilitar esta característica, vea si los [dominios personalizados en el Proxy de aplicación de Azure AD](active-directory-application-proxy-custom-domains.md) son adecuados para usted.
>
>O, en caso de que la aplicación que necesita configurar con la traducción de vínculos sea SharePoint, consulte [Configurar las asignaciones alternativas de acceso en SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx) para otro enfoque para la asignación de vínculos.

## <a name="how-link-translation-works"></a>Funcionamiento de la traducción de vínculos

Después de la autenticación, cuando el servidor proxy pasa los datos de aplicación al usuario, el Proxy de aplicación busca vínculos codificados de manera rígida en la aplicación y los reemplaza por sus direcciones URL externas publicadas correspondientes.

El Proxy de aplicación da por supuesto que las aplicaciones están codificadas en UTF-8. Si no es el caso, especifique el tipo de codificación en un encabezado de respuesta HTTP, como `Content-Type:text/html;charset=utf-8`.

### <a name="which-links-are-affected"></a>¿Qué vínculos se ven afectados?

La característica de traducción de vínculos solo busca los vínculos que estén en las etiquetas de código del cuerpo de una aplicación. El Proxy de aplicación tiene una característica independiente para traducir cookies o direcciones URL en los encabezados. 

Hay dos tipos comunes de vínculos internos en aplicaciones locales:

- **Vínculos internos relativos** que apuntan a un recurso compartido en una estructura de archivos local como `/claims/claims.html`. Estos vínculos funcionan automáticamente en aplicaciones que se publican mediante el Proxy de aplicación y siguen funcionando con o sin traducción de vínculos. 
- **Vínculos internos codificados de manera rígida** a otras aplicaciones locales como `http://expenses` o archivos publicados como `http://expenses/logo.jpg`. La característica de traducción de vínculos funciona en vínculos internos codificados de manera rígida y los modifica para que apunten a las direcciones URL por las que deben pasar los usuarios remotos.

### <a name="how-do-apps-link-to-each-other"></a>¿Cómo se vinculan entre sí las aplicaciones?

La traducción de vínculos está habilitada para cada aplicación, para que se tenga control sobre la experiencia del usuario en el nivel por aplicación. Active la traducción de vínculos para una aplicación cuando desee que se traduzcan los vínculos *desde* esa aplicación y no los vínculos *hacia* ella. 

Por ejemplo, imagine que tiene tres aplicaciones publicadas a través del Proxy de aplicación que se vinculan entre sí: Beneficios, Gastos y Viajes. Existe una cuarta aplicación, Comentarios, que no se publica mediante el Proxy de aplicación.

Cuando habilita la traducción de vínculos para la aplicaciones Beneficios, los vínculos a Gastos y Viajes se redirigen a las direcciones URL externas de esas aplicaciones, pero el vínculo a Comentarios no se redirige porque no hay ninguna dirección URL externa. Los vínculos de Gastos y Viajes a Beneficios no funcionan, porque la traducción de vínculos no está habilitada para esas dos aplicaciones.

![Vínculos desde Beneficios a las otras aplicaciones cuando está habilitada la traducción de vínculos](./media/application-proxy-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>¿Qué vínculos no se traducen?

Para mejorar el rendimiento y la seguridad, no se traducen algunos vínculos:

- Los vínculos que no están dentro de las etiquetas de código. 
- Los vínculos que no están en HTML, CSS o JavaScript. 
- Los vínculos internos que se abren desde otros programas. No se traducirán los vínculos que se envían a través de correos electrónicos o mensajes instantáneos o que se incluyen en otros documentos. Los usuarios deben saber ir a la dirección URL externa.

Si necesita admitir uno de estos dos escenarios, use las mismas direcciones URL internas y externas en lugar de la traducción de vínculos.  

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
[Uso de dominios personalizados con el Proxy de aplicación de Azure AD](active-directory-application-proxy-custom-domains.md) para tener la misma dirección URL interna y externa

[Configurar las asignaciones alternativas de acceso en SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
