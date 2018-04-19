---
title: Preguntas más frecuentes para el complemento SSO de Azure Active Directory | Microsoft Docs
description: Obtenga respuestas a preguntas frecuentes acerca de cómo configurar un inicio de sesión único entre Azure Active Directory y Jira/Confluence.
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
ms.openlocfilehash: b642d6de9fd9265d7f7ee11b827a36d20e5bcce7
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="faq-for-the-azure-active-directory-sso-plug-in"></a>Preguntas más frecuentes para el complemento SSO de Azure Active Directory 

## <a name="what-does-the-plug-in-do"></a>¿Qué hace el complemento?

El complemento proporciona la funcionalidad de inicio de sesión único para el software local Jira (como Jira Core, Jira Software, Jira Service Desk) y Confluence de Atlassian. El complemento funciona con Azure Active Directory (Azure AD) como proveedor de identidades (IdP).

## <a name="which-atlassian-products-does-the-plug-in-work-with"></a>¿Con qué productos de Atlassian funciona el complemento?

El complemento funciona con las versiones locales de Jira y Confluence.

## <a name="does-the-plug-in-work-on-cloud-versions"></a>¿Este complemento funciona en las versiones en la nube?

Nº El complemento admite solo las versiones locales de Jira y Confluence.

## <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>¿Qué versiones de JIRA y Confluence admite el complemento?

El complemento admite estas versiones:

* Jira Core y Software: de 6.0 a 7.2.2 
* Jira Service Desk: de 3.0 a 3.2 
* Confluence: de la versión 5.0 a la 5.10

## <a name="is-the-plug-in-free-or-paid"></a>¿El complemento es gratuito o de pago?

Es un complemento gratuito. Se puede instalar desde Atlassian Marketplace.

## <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>¿Tengo que reiniciar Kira o Confluence una vez que implemente el complemento?

No es necesario reiniciar. Puede empezar a usar el complemento de inmediato.

## <a name="how-do-i-get-support-for-the-plug-in"></a>¿Cómo puedo obtener soporte técnico para el complemento?

Escriba al [equipo de integración de SSO de Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). El equipo responde en una plazo entre 24 y 48 horas laborales. 

También puede generar una incidencia de soporte técnico con Microsoft a través del canal de Azure Portal.

## <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>¿El complemento funciona en una instalación Mac o Ubuntu de Jira y Confluence?

Hemos probado este complemento solo en instalaciones de Windows Server de 64 bits de Jira y Confluence.

## <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>¿El complemento funciona con IdP distintos de Azure AD?

Nº Solo funciona con Azure AD.

## <a name="what-version-of-saml-does-the-plug-in-work-with"></a>¿Con qué versión de SAML funciona el complemento?

Funciona con SAML 2.0.

## <a name="does-the-plug-in-do-user-provisioning"></a>¿El complemento se ocupa del aprovisionamiento de usuarios?

Nº El complemento solo proporciona SSO basado en SAML 2.0. El usuario tiene que estar aprovisionado en la aplicación antes del inicio de sesión SSO.

## <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>¿El complemento admite versiones en clúster de Jira y Confluence?

Nº El complemento funciona con las versiones locales de Jira y Confluence.

## <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>¿El complemento admite versiones HTTP de Jira y Confluence?

Nº El complemento solo funciona con instalaciones habilitadas de HTTPS.