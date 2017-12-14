---
title: "Opciones de ayuda y soporte técnico para los desarrolladores que trabajan con identidades de Azure | Microsoft Docs"
description: "Infórmese sobre cómo obtener ayuda y soporte técnico para preguntas relacionadas con el desarrollo, y para resolver los problemas que pueden surgir al crear aplicaciones que se integran con las identidades de Microsoft Azure (Azure Active Directory y MSA)"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/27/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 06da07aa699d19602449dc365abb971867214a31
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="support-and-help-options-for-developers"></a>Opciones de ayuda y soporte técnico para desarrolladores 

Independientemente de si está empezando a integrarse con Azure Active Directory, las identidades de Microsoft o Microsoft API Graph, o si está implementando una nueva característica en una aplicación, hay ocasiones en las que puede necesitar ayuda de la comunidad de usuarios o querer saber las opciones de soporte técnico que tienen como desarrollador. Este artículo le ayuda a conocer estas opciones, a continuación le presentamos un resumen:

> [!div class="checklist"]
> * Realice una búsqueda para comprobar si la pregunta sobre su problema ya ha sido contestada en la comunidad, o si existe documentación para la característica que está intentando implementar
> * En algunos casos, puede que lo que desea es usar nuestras herramientas de soporte técnico para ayudarle a depurar un problema específico
> * Si no encuentra la respuesta que necesita, puede formular una pregunta en *Stack Overflow*
> * Si tiene algún problema con una de nuestras bibliotecas de autenticación, genere un *problema de GitHub*
> * Por último, si necesita hablar con alguien puede abrir una solicitud de soporte técnico


## <a name="search"></a>Search

Si tiene alguna pregunta relacionada con el desarrollo, puede que encuentre la respuesta que necesita en nuestra documentación, los [ejemplos de github](https://github.com/azure-samples) o las respuestas a las preguntas de [Stack Overflow](https://www.stackoverflow.com).

### <a name="scoped-search"></a>Búsqueda restringida
Para obtener resultados más rápidos, limite su búsqueda a Stack Overflow, nuestra documentación y nuestros ejemplos de código utilizando lo siguiente en su [motor de búsqueda favorito](https://bing.com):
```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/en-us/graph)
```
Donde *{Your Search Terms}* son sus palabras clave de búsqueda.
<br/>

## <a name="use-our-development-support-tools"></a>Uso de nuestras herramientas de soporte técnico de desarrollo

|Herramienta  |Descripción  |
|---------|---------|
|[jwt.ms](https://jwt.ms)| Pegue un identificador o un token de acceso para descodificar los valores y nombres de notificaciones |
|[Error Code Analyzer](https://apps.dev.microsoft.com/portal/tools/errors)| Pegue un código de error recibido durante el inicio de sesión o las páginas de consentimiento para ver posibles causas y correcciones |
|[Explorador de Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)| Esta herramienta le permite realizar las solicitudes y ver las respuestas en Microsoft API Graph|

<br/>

[![Stack Overflow](media/active-directory-develop-help-support/stackoverflow-logo.png)](https://www.stackoverflow.com)
## <a name="post-a-question-to-stack-overflow"></a>Publique una pregunta en Stack Overflow

Stack Overflow es el canal preferido para preguntas relacionadas con el desarrollo, en él tanto los miembros de la comunidad de usuarios como los del equipo de Microsoft se implican directamente para ayudarle a resolver su problema.

Si no encuentra una respuesta a su problema a través de la búsqueda, envíe una nueva pregunta a Stack Overflow: utilice una de las siguientes etiquetas al realizar preguntas para ayudar a la comunidad a identificar y después a responder a su pregunta de manera puntual:

|Componente/área  |Etiquetas  |
|---------|---------|
|Biblioteca ADAL |[[adal]](http://stackoverflow.com/questions/tagged/adal)|
|Biblioteca MSAL     |[[msal]](http://stackoverflow.com/questions/tagged/msal)|
|Middleware OWIN  |[[azure-active directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)|
|[B2B de Azure](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  |[[azure-ad-b2b]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[B2C de Azure](https://azure.microsoft.com/services/active-directory-b2c/)  |[[azure-ad-b2c]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Microsoft API Graph](https://developer.microsoft.com/graph/) |[[microsoft-graph]](http://stackoverflow.com/questions/tagged/microsoft-graph)
|Cualquier otra área relacionada con temas de autenticación o autorización |[[azure-active directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)
<br/>
> [!TIP]
> Las entradas siguientes de Stack Overflow contienen sugerencias sobre cómo hacer preguntas, y sugerencias sobre cómo agregar código fuente; si sigue estas instrucciones ayudará a aumentar las posibilidades de que los miembros de la comunidad evalúen y respondan rápidamente a su pregunta:  
> - [Cómo se puede formular una buena pregunta](https://stackoverflow.com/help/how-to-ask)
> - [Cómo crear un ejemplo mínimo, completo y comprobable](https://stackoverflow.com/help/mcve)

<br/>


[![Stack Overflow](media/active-directory-develop-help-support/github-logo.png)](https://www.github.com)
## <a name="create-a-github-issue"></a>Creación de un problema de GitHub

 Si encuentra un error o un problema relacionado con nuestras bibliotecas, genere un problema en los repositorios de GitHub. Dado que nuestra bibliotecas son de código abierto, también puede enviar una solicitud de incorporación de cambios. El artículo siguiente contiene una lista de bibliotecas y sus repositorios de GitHub:

- Bibliotecas y repositorios de GitHub de [ADAL, MSAL y middleware OWIN](active-directory-authentication-libraries.md)

<br/>

## <a name="open-a-support-request"></a>Apertura de una solicitud de soporte técnico

Si necesita hablar con alguien, puede abrir una solicitud de soporte técnico. Si es un cliente de Azure, tiene varias opciones de soporte técnico. Para comparar los planes, consulte [esta página](https://azure.microsoft.com/support/plans/). El soporte técnico Developer también está disponible para los clientes de Azure. Para obtener información sobre cómo adquirir planes de soporte técnico de Developer, consulte [esta página](https://azure.microsoft.com/support/plans/developer/).

- Si ya tiene un plan de soporte técnico de Azure, [abra una solicitud de soporte aquí](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- Si no es un cliente de Azure, también puede abrir una solicitud de soporte técnico con Microsoft a través de [nuestro soporte comercial](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

También puede intentar [nuestro agente virtual](https://support.microsoft.com/contactus/?ws=support) para obtener soporte técnico o formular alguna pregunta.

### <a name="free-chat-support-for-a-limited-time"></a>Soporte técnico por chat gratuito disponible durante un tiempo limitado

También puede utilizar nuestro soporte técnico por chat que es gratuito para los partners de Microsoft durante un tiempo limitado. Si su empresa no es un Partner de Microsoft, puede inscribirla de forma gratuita y obtener otros beneficios [aquí](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Tras inscribir a su empresa, puede iniciar la solicitud de chat [aquí](https://aka.ms/devchat).