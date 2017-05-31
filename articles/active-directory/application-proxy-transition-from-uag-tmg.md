---
title: "Actualización al proxy de aplicación de Azure AD | Microsoft Docs"
description: "Elija qué solución de proxy es la mejor si va a actualizar desde Microsoft Forefront o Unified Access Gateway."
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
ms.date: 04/27/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: e9d5caa4d11012744ce9f26648166371f3aa17ba
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017

---
# <a name="upgrade-to-azure-ad-proxies-from-microsoft-forefront-or-unified-access-gateway"></a>Actualización a servidores proxy de Azure AD desde Microsoft Forefront o Unified Access Gateway

En este artículo se describe cómo pasar de las soluciones Threat Management Gateway (TMG) y Unified Access Gateway (UAG) de Microsoft Forefront a Proxy de aplicación de Azure AD.

Para más información sobre la transición de TMG y UAG de Forefront a Proxy de aplicación, puede [leer las notas del producto relacionadas de Microsoft](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).

## <a name="functionality-details-for-the-conversion"></a>Detalles de la funcionalidad de conversión

|**Funcionalidad TMG/UAG**|**Solución moderna**|
|:-----|:-----|
|Publicación selectiva HTTP para las aplicaciones de explorador|Proxy de aplicación de Azure AD|
|Integración de Servicios de federación de Active Directory (AD FS)|Proxy de aplicación de Azure AD|
|Publicación completa de protocolos (por ejemplo,, Citrix, Lync, RDG)|Proxy de aplicación de Azure AD|
|Portal|Panel de acceso de Azure AD o iniciador de aplicaciones de Office 365 para el proxy de aplicación de Azure AD|
|Detección del estado del punto de conexión|Intune o System Center|
|Tunelización de SSH|Windows SSL o VPN|
|Firewall de capa 2/3|Windows Server|
|Autenticación previa para ActiveSync (HTTP básico) y puerta de enlace de Escritorio remoto|No hay ninguna solución actual de Microsoft|
|Firewall de aplicaciones web|No hay ninguna solución actual de Microsoft.|
|Puerta de enlace web segura (proxy de reenvío)|No hay ninguna solución actual de Microsoft.|


## <a name="next-steps"></a>Pasos siguientes

[Blog: Web Application Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy) (Blog: proxy de aplicación web)<br>
[Blog: Azure AD Application Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap) (Blog: Proxy de aplicación de Azure AD)

