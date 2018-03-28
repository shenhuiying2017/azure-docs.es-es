---
title: Preguntas más frecuentes sobre el complemento de inicio de sesión único de Microsoft Azure Active Directory | Microsoft Docs
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Microsoft Azure Active Directory single sign-on for JIRA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: 571fbd5078f66375f6e81cba2a790121366f9d60
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-faq"></a>Preguntas más frecuentes sobre el complemento de inicio de sesión único de Microsoft Azure Active Directory 

## <a name="1-whats-the-microsoft-sso-add-on"></a>1. ¿Qué es el complemento de SSO de Microsoft?

Este complemento proporciona la funcionalidad de inicio de sesión único para el software local JIRA (incluido JIRA Core, JIRA Software, JIRA Service Desk) y Confluence de Atlassian. El complemento funciona con Azure AD como IdP.

## <a name="2-add-on-works-with-which-atlassian-products"></a>2. ¿Con qué productos de Atlassian funciona el complemento?

Por ahora, el complemento funciona con las versiones locales de JIRA y Confluence.

## <a name="3-does-this-add-on-work-on-cloud-version"></a>3. ¿Este complemento funciona en la versión en la nube?

Nº Solo se admiten las versiones locales de JIRA y Confluence.

## <a name="4-which-versions-of-jira-and-confluence-are-supported"></a>4. ¿Qué versiones de JIRA y Confluence se admiten?

Esta es la lista de las versiones admitidas:

* JIRA Core y Software: de la 6.0 a la 7.2.2 
* Departamento de servicios de JIRA: de la 3.0 a la 3.2 
* Confluence: de la versión 5.0 a la 5.10

## <a name="5-is-this-add-on-free-or-paid"></a>5. ¿Este complemento es gratuito o de pago?

Se trata de un complemento gratuito y se puede instalar desde Atlassian Marketplace.

## <a name="6-do-i-need-to-restart-jiraconfluence-once-i-deploy-the-add-on"></a>6. ¿Tengo que reiniciar JIRA/Confluence una vez que implemento el complemento?

No se requiere el reinicio después de la implementación del complemento. Puede comenzar a usar el complemento inmediatamente después de la implementación.

## <a name="7-how-do-i-get-support-for-the-add-on"></a>7. ¿Cómo puedo obtener soporte técnico para el complemento?

Póngase en contacto con nosotros en: <email> . Responderemos en un plazo de <> horas. También puede generar una incidencia de soporte técnico con Microsoft a través del canal de Azure Portal. También puede llamarnos: <Number> entre las <> a.m. y las <> p.m. en días laborables.

## <a name="8-would-this-add-on-work-on-mac-or-ubuntu-installation-of-jira-and-confluence"></a>8. ¿Este complemento funciona en instalaciones Mac o Ubuntu de JIRA y Confluence?

Hemos probado este complemento solo en instalaciones de Windows Server de 64 bits de JIRA y Confluence.

## <a name="9-does-this-add-on-work-with-other-idps-than-azure-ad"></a>9. ¿Esta complemento funciona con otros IdP, además de Azure AD?

Nº El complemento solo funciona con Azure AD.

## <a name="10-what-version-of-saml-does-the-add-on-work-with"></a>10. ¿Con qué versión de SAML funciona el complemento?

El complemento solo funciona con SAML 2.0.

## <a name="11-does-the-add-on-do-use-provisioning-as-well"></a>11. ¿El complemento también realiza el aprovisionamiento del usuario?

Nº Por ahora, el complemento solo proporciona SSO basado en SAML 2.0. El usuario tiene que estar aprovisionado en la aplicación antes del inicio de sesión SSO.

## <a name="12-are-cluster-versions-of-jira-and-confluence-supported-by-add-on"></a>12. ¿El complemento admite versiones del clúster de JIRA y Confluence?

Nº El complemento funciona con las versiones locales de JIRA y Confluence.

## <a name="13-would-this-plugin-work-with-http-version-of-jira-and-confluence"></a>13. ¿Este complemento funcionaría con la versión HTTP de JIRA y Confluence?

Nº El complemento solo funciona con instalaciones habilitadas de HTTPS.

## <a name="14-do-i-need-to-buy-license-of-the-add-on"></a>14. ¿Tengo que comprar una licencia para el complemento?

Es un complemento gratuito.