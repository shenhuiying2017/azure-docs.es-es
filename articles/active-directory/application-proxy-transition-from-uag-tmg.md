---
title: "Transición a los servidores proxy de aplicación de Azure AD desde Microsoft Forefront | Microsoft Docs"
description: "Se describen los conceptos básicos sobre cómo pasar de las soluciones TMG y UAG de Microsoft Forefront a los servidores proxy de aplicación de Azure Active Directory."
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
ms.date: 01/27/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: c887adaae811972efd7fcea86eaa67a899f3f4c2
ms.openlocfilehash: 542dd7df7e0b887298522f29cb597f1df73709cb
ms.lasthandoff: 02/27/2017


---
# <a name="transition-to-azure-ad-application-proxies-from-microsoft-forefront"></a>Transición a los servidores proxy de aplicación de Azure AD desde Microsoft Forefront

En este artículo se describe cómo pasar de las soluciones Threat Management Gateway (TMG) y Unified Access Gateway (UAG) de Microsoft Forefront a estos servidores proxy de aplicación de Azure Active Directory (Azure AD): Proxy de aplicación web y Proxy de aplicación de Azure AD.

> [!NOTE]
> Proxy de aplicación de Azure AD es una característica que solo está disponible si actualizó a la edición Premium o Básica de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).


Para más información sobre la transición de TMG y UAG de Forefront a los nuevos servidores proxy de aplicación, puede [descargar las notas del producto relacionadas de Microsoft](http://download.microsoft.com/download/3/E/3/3E335D93-6DB8-4834-90A8-B86105419F05/Microsoft%20TMG%20and%20UAG%20EOL%20and%20transitioning%20to%20WAP%20and%20AADAP.docx).

## <a name="functionality-details-for-the-conversion"></a>Detalles de la funcionalidad de conversión

|**Funcionalidad TMG/UAG**|**Proxy de aplicación web/Proxy de aplicación de Azure AD**|
|:-----|:-----|
|Publicación selectiva HTTP para las aplicaciones de explorador|Disponible en el Proxy de aplicación web en Windows Server 2012 R2. Disponible ya en el Proxy de aplicación de Azure AD.|
|Integración de Servicios de federación de Active Directory (AD FS)|Disponible en el Proxy de aplicación web en Windows Server 2012 R2. Disponible ya en el Proxy de aplicación de Azure AD.|
|Publicación completa de protocolos (por ejemplo,, Citrix, Lync, RDG)|Disponible en el Proxy de aplicación web en Windows Server 2012 R2. Disponible ya en el Proxy de aplicación de Azure AD.|
|Autenticación previa para ActiveSync (HTTP básico) y puerta de enlace de Escritorio remoto|Actualmente no está disponible en el Proxy de aplicación web o el Proxy de aplicación de Azure AD.|
|Portal|Use Intune o System Center para el Proxy de aplicación web. Use el panel de acceso de Azure AD o el iniciador de aplicaciones de Office 365 con el Proxy de aplicación de Azure AD.|
|Detección del estado del punto de conexión|Use Intune o System Center.|
|Tunelización de SSH|Use la funcionalidad SSL o VPN de Windows.|
|Firewall de capa 2/3|Use las funcionalidades de Windows Server.|
|Firewall de aplicaciones web|No hay ninguna solución actual de Microsoft.|
|Puerta de enlace web segura (proxy de reenvío)|No hay ninguna solución actual de Microsoft.|


## <a name="next-steps"></a>Pasos siguientes

[Blog: Web Application Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy) (Blog: proxy de aplicación web)<br>
[Blog: Azure AD Application Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap) (Blog: Proxy de aplicación de Azure AD)

