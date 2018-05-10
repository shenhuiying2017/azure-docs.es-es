---
title: Qué es AppSource y cómo funciona con Azure
description: Obtenga información sobre AppSource, que permite a los partners de Microsoft hacer que los clientes puedan usar su tecnología y sus servicios a través de un escaparate en línea compatible con Microsoft.
services: Marketplace, AppSource, Compute, Storage, Networking, Security, SaaS
documentationcenter: ''
author: ellacroi
manager: ''
editor: v-brela
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/12/2018
ms.author: ellacroi
ms.openlocfilehash: 5f963e9ef6f0a19599f372b196dc75a327b92b5a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-appsource"></a>Qué es AppSource
AppSource permite a los partners de Microsoft hacer que los clientes puedan usar su tecnología y sus servicios a través de un escaparate en línea compatible con Microsoft. Como anunciante en AppSource, atraiga a los usuarios empresariales a AppSource para que busquen, prueben y obtengan servicios de implementación y aplicaciones SaaS de línea de negocio que les ayuden a impulsar los resultados empresariales y acelerar la rentabilidad: 

| Necesidad del cliente       | AppSource          | 
|:---------------------------------------- |:----------------------------------------------------- |
| **Buscar soluciones empresariales que funcionen con productos de Microsoft ya existentes**    | Permite que los partners usen aplicaciones y servicios de terceros para ampliar las tecnologías y las aplicaciones en la nube de Microsoft. |
| **Posibilidad de detectar fácilmente la solución o el servicio de implementación adecuados.**     | Proporciona una tienda integral donde descubrir, probar y obtener aplicaciones, servicios, complementos y mucho más. |
| **Solución de línea de negocio específica del sector para abordar sus dificultades empresariales concretas**   | Proporciona soluciones completas del sector acabadas que ayudan a abordar requisitos específicos en muchos sectores. |
| **Aplicaciones que ayuden a mejorar la productividad, la eficacia y la información empresarial**       | Proporciona aplicaciones de línea de negocio, que incluyen servicio al cliente, RR. HH., operaciones y mucho más. |
| **Asociado de implementación con experiencia para ayudar a adaptar las aplicaciones a cada situación única**     | Proporciona un catálogo de ofertas de servicios de consultoría para soluciones basadas en Dynamics 365, Power BI, PowerApps y aplicaciones de terceros que están disponibles en AppSource para ayudar a los usuarios empresariales a encontrar servicios de consultoría diseñados para proporcionar resultados predecibles. |

## <a name="appsource-publishing"></a>Publicación de AppSource
Gracias a AppSource, puede publicar una aplicación o una oferta de consultoría que ayude a satisfacer las necesidades de los clientes durante el uso de productos de Microsoft como Office 365, Dynamics 365, Power BI y Power Apps. Las siguientes ofertas están disponibles en AppSource Marketplace: 
*   **Dynamics 365 for Finance and Operations**: una solución de planeación de recursos empresariales (ERP). Las soluciones diferenciadas son la edición Enterprise Edition para empresas medianas y grandes, y Business Edition, para pequeñas y medianas empresas.
*   **Dynamics 365 for Customer Engagement**: una solución de administración de relaciones con clientes (CRM) que incluye aplicaciones de automatización de servicios de proyectos, servicios de campo, atención al cliente y ventas.
*   **Servicio administrado de Dynamics NAV**: Microsoft **Dynamics NAV** es un conjunto de software de planeación de recursos empresariales (ERP) para organizaciones de tamaño mediano. El servicio ofrece funcionalidades especializadas para los sectores de fabricación, distribución, gobierno, minorista y otros.
*   **Power BI**: una solución de visualización de análisis de negocios.
*   **Ofertas de consultoría**: servicios que proporcionan los partners de Microsoft para ayudar a los clientes a comprender, probar e implementar soluciones tecnológicas especializadas.
*   **Cortana Intelligence**: Cortana Intelligence Suite es una colección de datos independientes pero totalmente integrados y de herramientas de análisis de Azure.
*   **Office 365**: el área de trabajo moderno en la nube. Colabore de forma gratuita con Microsoft Word, PowerPoint, Excel y OneNote.

### <a name="office-365"></a>Office 365
Las aplicaciones para Office están ahora disponibles en AppSource. Revise las [directrices y el proceso de publicación]( https://docs.microsoft.com/office/dev/store/submit-to-the-office-store "Office 365: AppSource").

### <a name="dynamics-365-for-finance-and-operations"></a>Dynamics 365 for Financials y Operations
Al realizar la compilación para Enterprise Edition, revise el [proceso y las directrices de publicación](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/lcs-solutions/lcs-solutions-app-source "Dynamics 365 Enterprise Edition: AppSource").  

### <a name="dynamics-365-for-customer-engagement"></a>Dynamics 365 for Customer Engagement
Revise las [directrices y el proceso de publicación](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource "Dynamics 365: AppSource").

### <a name="power-bi"></a>Power BI
Cree objetos visuales personalizados y póngalos a disposición de los usuarios a través de AppSource. Revise las [directrices y el proceso de publicación](https://docs.microsoft.com/power-bi/developer/office-store "Power BI: AppSource").

### <a name="consulting-offers"></a>Ofertas de consultoría
Si ofrece servicios para ayudar a los clientes a facilitar la transformación digital o a implementar una solución o aplicación, puede ofrecer este servicio como una oferta de consultoría en AppSource. 
Revise las [directrices y aprenda a enviar la oferta](https://smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf "AppSource: directrices de publicación para partners").

### <a name="cortana-intelligence"></a>Cortana Intelligence
[Guía para la publicación de aplicaciones Cortana Intelligence en AppSource](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-intelligence-appsource-publishing-guide "Guía para la publicación de aplicaciones de Cortana Intelligence en AppSource")

## <a name="azure-active-directory-integration"></a>Integración de Azure Active Directory
Para que se publiquen algunas aplicaciones del escaparate de AppSource, hay que integrarlas con Azure Active Directory (Azure AD). La integración de Azure AD con aplicaciones está bien documentada y Microsoft proporciona varios SDK y recursos para cumplir sus requisitos. 

| Nombre de la oferta | Requiere integración con Azure AD | Notas |
|:--- |:---:|:--- |
| Dynamics 365 for Operations | Sin  | Una oferta de licencia no requiere la integración con Azure AD |
| Dynamics 365 for Finance | Sin  | Una oferta de licencia no requiere la integración con Azure AD |
| Dynamics 365 for Customer Engagement | Sin  | Una oferta de licencia no requiere la integración con Azure AD |
| Servicio administrado de Dynamics NAV | Sin  | Una oferta de licencia no requiere la integración con Azure AD |
| Power BI | Sin  | Una oferta de licencia no requiere la integración con Azure AD |
| Ofertas de consultoría | Sin  | Los servicios se coordinan mediante el partner, no a través de una experiencia web |
| Cortana Intelligence | Sí | Se recomienda Azure AD para garantizar que el cliente obtenga una experiencia perfecta y generar clientes potenciales de alta calidad. |
| Aplicaciones de SaaS (anteriormente aplicaciones web) | Opción preferida | Se recomienda Azure AD para garantizar que el cliente obtenga una experiencia perfecta y generar clientes potenciales de alta calidad. |
###### <a name="table-appsource-offerings-that-require-azure-active-directory-integration"></a>Tabla: Ofertas de AppSource que requieren integración con Azure Active Directory

Para empezar, se recomienda **configurar una suscripción dedicada a la publicación en Azure Marketplace***, lo que permitirá aislar el trabajo de otras iniciativas. Además, si no ha instalado ya las siguientes herramientas, se recomienda agregarlas al entorno de desarrollo: 
*   [CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest "Azure: CLI")
*   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-5.0.0 "PowerShell: Azure")
*   Revise las herramientas disponibles en la página [Herramientas de desarrollo de Azure](https://azure.microsoft.com/tools/ "Azure: Herramientas de desarrollo").
*   [Visual Studio Code](https://code.visualstudio.com/ "Visual Studio Code: Principal")

Si comienza a trabajar con Azure AD, los vínculos siguientes son los mejores recursos: 

**Documentación**
*   [Guía para desarrolladores de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide "Azure AD: Guía para desarrolladores")
*   [Integración con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate "Integración: Azure AD")
*   [Integración de aplicaciones con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications "Integración de aplicaciones: Azure AD")
*   [Hoja de ruta de Azure: seguridad e identidad](https://azure.microsoft.com/roadmap/?category=security-identity "Hoja de ruta de Azure: seguridad e identidad")

**Vídeos**
*   [Autenticación de Azure Active Directory con Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration "Autenticación de Azure AD: Vittorio Bertocci")
*   [Azure Active Directory Identity Technical Briefing - Part 1 of 2] (Sesión informativa técnica sobre identidad de Azure Active Directory - Parte 1 de 2)(https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration "Azure AD: Sesión informativa técnica sobre identidad de Azure AD 1/2")
*   [Azure Active Directory Identity Technical Briefing - Part 2 of 2] (Sesión informativa técnica sobre identidad de Azure Active Directory - Parte 2 de 2)(https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration "Azure AD: Sesión informativa técnica sobre identidad de Azure AD 2/2")
*   [Building Apps with Microsoft Azure Active Directory] (Creación de aplicaciones con Microsoft Azure Active Directory)(https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration "Creación de aplicaciones: Azure AD")
*   [Vídeos de Microsoft Azure sobre Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory "Azure AD: Vídeos")

**Aprendizaje**
*   [Microsoft Azure for IT Pros Content Series: Azure Active Directory] (Serie de contenido de Microsoft Azure para profesionales de IT: Azure Active Directory)(https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965 "Serie de contenido de Microsoft Azure para profesionales de IT: Azure AD")

**Actualizaciones de servicios de Azure Active Directory**
*   [Actualizaciones de servicios de Azure AD](https://azure.microsoft.com/updates/?product=active-directory "Azure AD: Actualizaciones de servicios")

Para recibir soporte técnico, los siguientes vínculos son buenos recursos: 
*   Formularios MSDN: [Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD "Azure AD en los foros de MSDN")
*   StackOverflow: [Azure Active Directory](https://stackoverflow.com/questions/tagged/azure-active-directory "Azure AD en StackOverflow")
