---
title: "Guía de publicación de Cortana Intelligence en AppSource | Documentos de Microsoft"
description: "Como asociado de Microsoft, estos son los pasos que debe seguir para publicar su solución Cortana Intelligence en AppSource."
services: machine-learning
documentationcenter: 
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: anupams;v-bruham;garye
ms.openlocfilehash: 9f867641b77c8148c1d6cbf2913da9c1f5ce5b71
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="cortana-intelligence-appsource-publishing-guide"></a>Guía de publicación de Cortana Intelligence en AppSource

## <a name="overview"></a>Información general
AppSource es el único destino para que los responsables de la toma de decisiones puedan encontrar y probar perfectamente soluciones y aplicaciones creadas por asociados y evaluadas por Microsoft. Vea [este vídeo](https://youtu.be/hpq_Y9LuIB8) para obtener información sobre cómo funciona AppSource. 

Como asociado de Microsoft, puede beneficiarse realmente de la publicación en AppSource si:
- Ha creado una solución o aplicación inteligente con [Cortana Intelligence Suite](https://azure.microsoft.com/en-us/suites/cortana-intelligence-suite/?cdn=disable).
- La solución o aplicación corrige un problema empresarial específico.
- Ha creado módulos o propiedad intelectual que los clientes pueden reutilizar relativamente rápido de una manera predecible.

Eche un vistazo a las [soluciones de Cortana Intelligence](https://appsource.microsoft.com/en-us/marketplace/apps?product=cortana-intelligence&page=1) que ya se han publicado en AppSource. 

Este artículo le guiará por los pasos necesarios para obtener soluciones de Cortana Intelligence de un asociado publicadas en AppSource.

## <a name="getting-started"></a>Introducción
1. En la [guía de beneficios de la comunidad de asociados](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Partner%20Community%20Benefits%20Guide%20-%20Cloud%20and%20Enterprise.pdf) (PDF), vea la página 9 para anunciarse como asociado de análisis avanzados.
1. Completa el formulario [Enviar la aplicación](https://appsource.microsoft.com/en-us/partners/list-an-app).

    En la pregunta *"Choose the products that your app is built for*" (Elija los productos a los que está orientada la aplicación), marque la casilla **Other** (Otros) y especifique “Cortana Intelligence” en el control de edición.
1. Para poder incorporar una aplicación de Cortana Intelligence a AppSource, debe obtener la certificación del equipo de tecnología de soluciones de asociados de Cortana Intelligence. Para que el proceso se inicie, comparta la información sobre la aplicación; para ello, rellene el formulario de encuesta de [evaluación de soluciones de Cortana Intelligence para la publicación en AppSource](https://aka.ms/cisappsrceval). Este sitio está protegido con contraseña y contiene instrucciones sobre cómo obtenerla.

## <a name="solution-evaluation-criteria"></a>Criterios de evaluación de la solución
A continuación se presenta la lista de criterios que la aplicación debe cumplir.
1. La aplicación necesita solucionar un problema de caso de uso empresarial concreto en un área funcional determinada de forma repetible con la mínima configuración para propuestas de valor predefinidas implementables en un corto período de tiempo.
1. La solución debe usar al menos uno de los siguientes componentes:

    - HDInsight
    - Machine Learning
    - Data Lake Analytics
    - Stream Analytics
    - Cognitive Services
    - Bot Framework
    - Analysis Services
    - Microsoft R Server independiente
    - R Services en SQL 2016 o HDInsight Premium
1. La solución debe generar al menos 1000 USD al mes por cada cliente con DPOR/CSP.
1. La solución debe usar el inicio de sesión único federado de Azure Active Directory (SSO federado de AAD) con consentimiento habilitado para la autenticación de usuario y los controles de acceso de recursos. Debe mostrar el equipo de evaluación que la solución tiene habilitado el SSO federado de AAD para poder incorporar la aplicación a AppSource.

     Para ver qué significa tener habilitado el SSO federado de AAD, busque el minuto 02:35 en el vídeo [AppSource trial experience walk through](https://aka.ms/trialexperienceforwebapps) (Tutorial sobre la experiencia de evaluación de AppSource). Si la aplicación aún no tiene habilitado el SSO federado de AAD, aquí se indica alguna documentación al respecto.
   1. [Inicio de sesión con un solo clic](https://identity.microsoft.com/Landing?ru=https://identity.microsoft.com/).
   1. [Integración de aplicaciones con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application)
     
1. Use Power BI en la solución: es opcional, pero se recomienda encarecidamente porque se ha demostrado que genera un número mayor de clientes potenciales.

## <a name="devcenter-account-setup"></a>Configuración de la cuenta de DevCenter
Este es el proceso para registrar su empresa como anunciante en Microsoft. Si ya es un anunciante registrado con una cuenta de DevCenter existente, comparta el identificador de correo electrónico asociado con la cuenta de DevCenter. Una vez aprobada su solicitud de publicación, accederá a la documentación completa sobre el [perfil de publicador de administración del portal en la nube](https://cloudpartner.azure.com/#documentation/manage-publisher-profile).

Si no tiene ninguna cuenta, a continuación se detallan los pasos principales para configurar una cuenta de DevCenter.
1. Cree una cuenta Microsoft [aquí](https://signup.live.com/signup.aspx).
1. Vaya a la [página de registro](http://go.microsoft.com/fwlink/?LinkId=615100) de Microsoft DevCenter y haga clic en "registrarse".
1. Cuando se le solicite información de pago, use el código que se le ha proporcionado. Si no tiene ningún código, póngase en contacto con [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20for%20promotion-code%20for%20DevCenter%20account%20setup).
1. Seleccione el [país/región](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees) en que vive o donde se encuentra su negocio. **No podrá cambiarlo más adelante.**
1. Seleccione el [tipo de cuenta de desarrollador](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees) para AppSource y, después, seleccione **Empresa**. Para una cuenta de empresa, asegúrese de revisar estas [directrices](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account).
1. Escriba la información de contacto que desea usar para la cuenta de desarrollador.
Para obtener instrucciones detalladas paso a paso sobre cómo configurar la cuenta de DevCenter, consulte las instrucciones [aquí](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration).

## <a name="provide-info-for-microsoft-sellers"></a>Proporcione información para los vendedores de Microsoft
Una de las principales propuestas de valor de AppSource para asociados es la capacidad de colaborar con los vendedores de Microsoft para presentar las aplicaciones de los asociados a los clientes potenciales.

Complete el formulario de [información de soluciones de asociados para vendedores de Microsoft](https://aka.ms/aapartnerappinfo) y envíelo a [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs). Se trata de un paso obligatorio para que se apruebe la publicación de una aplicación de Cortana Intelligence.

## <a name="build-a-compelling-customer-walkthrough-on-appsource"></a>Creación de un tutorial atractivo para los clientes sobre AppSource
En primer lugar, eche un vistazo a [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome) en AppSource. Cada entrada de aplicación en AppSource tiene un título, un resumen (100 caracteres como máximo), una descripción (1300 caracteres como máximo), imágenes, vídeos (opcional), documentos PDF y un punto de entrada para una experiencia de evaluación. Los asociados deben aprovechar todos estos recursos para crear una atractiva experiencia de cliente.

Los asociados deben pensar en qué contenido incluyen en AppSource como una orquestación de ventas de un extremo a otro. Los clientes leen el título y la descripción para saber la propuesta de valor y luego consultan las imágenes y los vídeos para ver de qué trata la solución. Los casos prácticos ayudan a los clientes potenciales a observar cómo los clientes existentes obtienen valor. 

Todo esto despierta el interés del cliente y lo incita a saber más. Piense en hacer esto como una presentación empresarial que un buen vendedor técnico expondría a los nuevos clientes. El formato sugerido para la descripción es dividir el texto en subsecciones en función de las propuestas de valor, cada una de ellas resaltada con un subtítulo. 

Los visitantes suelen echar un vistazo al campo del "resumen de la oferta" y a los subtítulos para hacerse una idea de lo que aborda la aplicación y por qué deben considerar la aplicación con tan solo una vista rápida. Por tanto, es importante que la atención del usuario sea un motivo para continuar leyendo a fin de conocer los detalles.

Vea lo que han hecho estos asociados.
- [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)
- [Personalización comercial de Plexure](https://appsource.microsoft.com/en-us/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [AvePoint Citizen Services](https://appsource.microsoft.com/en-us/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

La acción final de ventas consiste en presentar una demostración de la aplicación o solución, demostrando cómo se entrega la propuesta de valor. Esto es exactamente en lo que consiste una experiencia de evaluación de cliente en AppSource. Una buena demostración sería la siguiente:
- Vuelva a resumir la propuesta de valor de la aplicación de forma breve y especifique los roles dentro de la empresa del cliente que interactuarían con la solución.
- Cuente una historia y defina el contexto sobre un cliente de ejemplo que aborda problemas específicos.
- Explique cómo puede delegar la situación en general y afectar al cliente (antes) frente a lo que ocurriría si la solución está en uso. Esto puede mostrarse con los paneles de Power BI, entre otras ubicaciones.
- Resuma cómo la solución consigue esto mediante la utilización de cualquier algoritmo específico de Machine Learning, entre otros.

El contenido que se agrega en AppSource debe ser de alta calidad y ajustarse lo suficiente para permitir lo siguiente:
- Los responsables técnicos de ventas del asociado deben usar este contenido para su orquestación de ventas. Si sus equipos de ventas usan este contenido es una buena señal de que puede esperar que los responsables de ventas de MS también puedan hacer lo mismo. Esto permitirá al punto de contacto del cliente contar la misma historia a los compañeros de su equipo y a sus superiores para obtener un presupuesto y aprobaciones antes de conseguir un contrato de compra.
- Un cliente que visita el sitio puede consultar el contenido de forma orgánica por su cuenta y sentirse entusiasmado por responder a la comunicación del asociado a fin de avanzar con los siguientes pasos.

Este es el motivo por el que los asociados deben pensar en qué contenido incluyen en AppSource como una orquestación de ventas de un extremo a otro. El tipo de oferta puede decidirse en función de la línea y las características de la historia que se van a exponer como una experiencia de evaluación.

## <a name="publish-your-app-on-the-publishing-portal"></a>Publique la aplicación en el portal de publicación.
Después de haber evaluado los pasos anteriores para su aplicación, obtendrá acceso al portal de publicación y puede ver [Publicación de una oferta de Cortana Intelligence a través de Cloud Partner Portal](https://cloudpartner.azure.com/#documentation/cloud-partner-portal-publish-cortana-intelligence-app) para obtener instrucciones detalladas sobre los pasos siguientes.

Si necesita información sobre alguno de estos campos, envíe un correo electrónico a <appsourcecissupport@microsoft.com>.
## <a name="my-app-is-published-on-appsource---now-what"></a>Mi aplicación está publicada en AppSource; ¿qué debo hacer ahora?
En primer lugar, felicidades por haber publicado la aplicación.
El nivel de devoluciones que obtenga de la publicación de la aplicación en AppSource depende considerablemente de qué efecto tenga en la audiencia objetivo. Vea [Growth-Hacking your Cortana Intelligence app on AppSource](http://aka.ms/aagrowthhackguide) (Impulso de la rentabilidad de su aplicación de Cortana Intelligence en AppSource) para obtener más detalles sobre cómo puede maximizar las devoluciones.

Si tiene alguna pregunta o sugerencia, póngase en contacto con nosotros en <appsourcecissupport@microsoft.com>.

