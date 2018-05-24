---
title: Guía de publicación técnica de aplicaciones SaaS de Azure Marketplace
description: Guía paso a paso y listas de comprobación de publicación para la publicación de aplicaciones SaaS en Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ms.author: pabutler
ms.openlocfilehash: 48b0b4177dad6262105bf30be2b8714f6ea1228f
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076725"
---
# <a name="saas-applications-technical-publishing-guide"></a>Guía de publicación técnica de aplicaciones SaaS

Bienvenido a la Guía de publicación técnica de aplicaciones SaaS de Azure Marketplace. Esta guía está diseñada para ayudar a los candidatos y a los anunciantes existentes a mostrar sus aplicaciones y servicios en Azure Marketplace mediante la oferta de aplicaciones SaaS. 

Para entender mejor cómo publicar una oferta de SaaS, esta guía se divide en las siguientes secciones:
* Información general de la oferta
* Requisitos empresariales
* Requisitos técnicos
* Proceso de publicación
* Uso de Azure Active Directory para habilitar las evaluaciones
* Certificación de la integración de Azure AD para Marketplace

## <a name="offer-overview"></a>Información general de la oferta  

Las aplicaciones de SaaS están disponibles en los escaparates de Azure. En la tabla siguiente, se describen las opciones disponibles actualmente:

| Opción de escaparate | Listado | Evaluación/transacción |  
| --- | --- | --- |  
| AppSource | Sí (Contacto) | Sí (PowerBI/Dynamics) |
| Azure Marketplace | Sin  | Sí (Aplicaciones de SaaS) |   

**Listado:** la opción de publicación de listado consiste en un tipo de oferta Contacto y se utiliza cuando una participación de nivel de Evaluación o Transacción no es viable. La ventaja de este enfoque es que permite que los anunciantes con una solución en el mercado comiencen a recibir inmediatamente clientes potenciales que pueden convertirse en ventas para hacer crecer su negocio.  
**Evaluación/Transacción:** el cliente tiene la opción de comprar directamente o solicitar una versión de evaluación para la solución. Proporcionar una experiencia de Evaluación aumenta el nivel de compromiso que se ofrece a los clientes y permite que exploren la solución antes de comprarla. Con una experiencia de este tipo, tendrá mejores posibilidades de promoción en los escaparates, y deberían generarse más clientes potenciales y más valiosos a partir de la interacción con los clientes. Las versiones de evaluación deben incluir al menos soporte técnico gratuito durante el período de evaluación.  

| Oferta de aplicaciones de SaaS | Requisitos empresariales | Requisitos técnicos |  
| --- | --- | --- |  
| **Póngase en contacto con nosotros** | Sí | Sin  |  
| **PowerBI/Dynamics** | Sí | Sí (integración de Azure AD) |  
| **Aplicaciones de SaaS**| Sí | Sí (integración de Azure AD) |     

Para obtener más información acerca de los escaparates de Marketplace y la descripción de cada opción de publicación, consulte la [Guía de anunciantes de Marketplace](https://aka.ms/sellerguide) y las [opciones de publicación](https://docs.microsoft.com/en-us/azure/marketplace/marketplace-publishers-guide#select-a-publishing-option).

## <a name="business-requirements"></a>Requisitos empresariales
Se pueden cumplir los requisitos empresariales de la oferta de SaaS simultáneamente con los requisitos técnicos. La mayoría de los requisitos empresariales e información se recopilan al crear la oferta de SaaS en Cloud Partner Portal. Los requisitos empresariales son los siguientes: 
* Aceptar las directivas de participación
* Integración con Microsoft 
* Identificar el público de la oferta
* Definir y determinar la gestión de clientes potenciales que se usará
* Configurar la directiva de privacidad y los términos de uso
* Definir los contactos de soporte técnico  

Puede encontrar más información en el tema [Requisitos previos para la publicación en Marketplace](https://docs.microsoft.com/en-us/azure/marketplace/marketplace-publishers-guide#prerequisites-for-marketplace-publishing)

## <a name="technical-requirements"></a>Requisitos técnicos

Los requisitos técnicos para las aplicaciones SaaS son simples. Los anunciantes solo tienen que integrarse con Azure Active Directory (Azure AD) para su publicación. La integración de Azure AD con aplicaciones está bien documentada y Microsoft proporciona varios SDK y recursos para lograr esto.  

Para empezar, se recomienda contar con una suscripción dedicada a la publicación en Azure Marketplace, lo que permitirá aislar el trabajo de otras iniciativas. Una vez hecho esto, se puede empezar a implementar la aplicación de SaaS en esta suscripción para iniciar el trabajo de desarrollo.  

La documentación, ejemplos y guía recomendada de Azure Active Directory se encuentran en los siguientes sitios: 

* [Guía del desarrollador de Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide)

* [Integración con Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integración de aplicaciones con Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)

* [Hoja de ruta de Azure: seguridad e identidad](https://azure.microsoft.com/roadmap/?category=security-identity)

Para ver tutoriales de vídeo, revise lo siguiente:

* [Azure Active Directory Authentication with Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration) (Autenticación de Azure Active Directory con Vittorio Bertocci)

* [Azure Active Directory Identity Technical Briefing - Part 1 of 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration) (Sesión informativa técnica sobre identidad de Azure Active Directory - Parte 1 de 2)

* [Azure Active Directory Identity Technical Briefing - Part 2 of 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration) (Sesión informativa técnica sobre identidad de Azure Active Directory - Parte 2 de 2)

* [Building Apps with Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration) (Creación de aplicaciones con Microsoft Azure Active Directory)

* [Vídeos de Microsoft Azure sobre Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

La capacitación gratuita de Azure Active Directory está disponible en  
* [Microsoft Azure for IT Pros Content Series: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965) (Serie de contenido de Microsoft Azure para profesionales de IT: Azure Active Directory)

Además, Azure Active Directory proporciona un sitio para buscar actualizaciones del servicio   
* [Actualizaciones de servicios de Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

Para obtener soporte técnico, puede usar los siguientes recursos:
* [Foros de MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="publishing-process"></a>Proceso de publicación

El proceso de publicación de SaaS cuenta con pasos técnicos y empresariales.  La mayoría del trabajo que se realiza mediante el desarrollo y la integración de Azure Active Directory puede realizarse simultáneamente con el trabajo necesario para cumplir los requisitos empresariales de la oferta. La mayor parte de los requisitos empresariales forman parte de la configuración de la oferta de SaaS en Cloud Partner Portal.  
En el siguiente diagrama, se muestran los pasos de publicación principales para la oferta de Evaluación o Transacción:  

![Pasos de publicación de SaaS](./media/marketplace-saas-applications-technical-publishing-guide/saaspublishingsteps.png)  

En la tabla siguiente, se describe cada uno de los pasos de publicación principales:  

| Paso de publicación | DESCRIPCIÓN |   
| --- | --- |  
| **Crear la aplicación de SaaS** | Inicie sesión en Cloud Partner Portal, seleccione **Nuevo** y, luego, seleccione la oferta de **aplicaciones de SaaS**. |  
| **Crear la integración con Azure AD** | Siga los requisitos técnicos que se describen en la sección anterior para integrar la oferta de SaaS con Azure AD. |  
| **Definir la configuración de oferta**| Escriba toda la información inicial de la oferta de SaaS. El Id. de oferta y nombre de oferta que le gustaría usar. |     
| **Definir la información técnica** | Escriba la información técnica acerca de la oferta. Para las aplicaciones de SaaS, se necesitan el URI de la solución y el botón de adquisición del tipo de oferta (Gratis, Evaluación o Contacto). |  
| **Versión de evaluación (opcional)** | Se trata de un tipo de evaluación opcional; se necesita principalmente para otros tipos de ofertas de Marketplace. Permite implementar la versión de evaluación en las suscripciones del anunciante frente a las del cliente final. |  
| **Establecer los materiales de escaparate de la oferta**| En esta sección, el anunciante deberá vincular y cargar los logotipos, materiales de marketing y documentos legales, y también deberá configurar el sistema de gestión de clientes potenciales. |
| **Definir los contactos de la oferta** | Escriba la información de contacto de ingeniería y del soporte técnico de la oferta de SaaS. |  
| **Comprobar la integración de Azure AD con la aplicación de SaaS** | Antes de enviar la aplicación de SaaS para la publicación, debe comprobar que la aplicación se integra con Azure AD |  
| **Publicar la oferta**| Después de completar la oferta y los recursos técnicos, la puede enviar. Esto iniciará el proceso de publicación, en el que la plantilla de la solución se evalúa, valida, certifica y aprueba para la publicación. |

## <a name="using-azure-active-directory-to-enable-trials"></a>Uso de Azure Active Directory para habilitar las evaluaciones  

Microsoft autentica a todos los usuarios de Marketplace con Azure AD, de ahí que cuando un usuario autenticado hace clic en la lista de versiones de evaluación de Marketplace y se le redirige a su entorno de evaluación, puede aprovisionar al usuario directamente en una versión de evaluación sin necesidad de un paso adicional de inicio de sesión. El token que recibe la aplicación de Azure AD durante la autenticación incluye información valiosa del usuario que se puede usar para crear una cuenta de usuario en su aplicación, lo que le permite automatizar la experiencia de aprovisionamiento y aumentar la probabilidad de conversión. Para obtener más información sobre el token, consulte [Tokens de ejemplo](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims).

El uso de Azure AD para habilitar la autenticación con un solo clic en su aplicación o versión de evaluación hace lo siguiente:  
* Optimiza la experiencia del cliente de Marketplace a la versión de Evaluación.  
* Mantiene la sensación de una "experiencia dentro del producto" incluso cuando se redirige al usuario desde Marketplace hasta el dominio o el entorno de evaluación.  
* Reduce la probabilidad de abandono en el redireccionamiento al no haber un paso adicional de inicio de sesión.  
* Reduce las barreras de implementación para la gran población de usuarios de Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certificación de la integración de Azure AD para Marketplace  

Puede certificar la integración de Azure AD de varias maneras, en función de si la aplicación es de un inquilino único o si es multiinquilino, y de si no está familiarizado con el inicio de sesión único (SSO) federado de Azure AD o si ya lo admite.  

**Para aplicaciones multiinquilino:**  

Si ya admite Azure AD, siga estos pasos:
1.  Registre la aplicación en Azure Portal
2.  Habilite la característica de compatibilidad multiinquilino en Azure AD para obtener una experiencia de evaluación con un clic. Se puede encontrar información más específica [aquí](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications).  

Si no está familiarizado con el SSO federado de Azure AD, haga lo siguiente: 
1.  Registre la aplicación en Azure Portal
2.  Desarrolle el SSO con Azure AD mediante [OpenID Connect](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-openid-connect-code) o [OAuth 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Habilite la característica de compatibilidad multiinquilino en AAD para obtener la experiencia de evaluación "con un clic". Se puede encontrar información más específica [aquí](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Para la aplicación de inquilino único, use cualquiera de las siguientes opciones:**  
* Agregue usuarios al directorio como usuarios invitados con [Azure B2B](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Aprovisione manualmente versiones de evaluación para los clientes mediante la opción de "Contacto"
* Desarrolle una "versión de prueba" por cliente
* Compile una aplicación de demostración de ejemplo multiinquilino con SSO.

