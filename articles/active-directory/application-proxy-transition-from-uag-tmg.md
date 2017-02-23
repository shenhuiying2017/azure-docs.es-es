---
title: "Transición al Proxy de aplicación de Azure AD desde Microsoft Forefront | Microsoft Docs"
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
ms.date: 01/27/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 9945512d18d66c321c0d24ee1050497d4716fd47
ms.openlocfilehash: 699112846cc1e4e9fc6b04b1b8509152d7aecdef


---
#<a name="transition-to-azure-ad-app-proxy-from-microsoft-forefront"></a>Transición al Proxy de aplicación de Azure AD desde Microsoft Forefront

En este artículo describe cómo mover desde soluciones de Microsoft Forefront Threat Management Gateway (TMG) y United Access Gateway (UAG) a estos servidores de proxy de aplicación de Azure AD: Proxy de aplicación web (WAP) y Proxy de aplicación de Azure AD (AADAP). 

> [!NOTE]
> Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).
> 
 
Muchos clientes nos pregunta cómo mover desde Forefront UAG y TMG a los nuevos servidores proxy de aplicación. Para obtener información, vea estas [notas del producto](http://download.microsoft.com/download/3/E/3/3E335D93-6DB8-4834-90A8-B86105419F05/Microsoft%20TMG%20and%20UAG%20EOL%20and%20transitioning%20to%20WAP%20and%20AADAP.docx) en las que se describe la transición en detalle. 
 
## <a name="tmguag-conversion-to-azure-ad-application-proxy-table"></a>Tabla de conversión de TMG/UAG a Proxy de aplicación de Azure AD
 
|**Funcionalidad TMG/UAG**|**Proxy de aplicación web (WAP) / Proxy de aplicación de Azure AD (PA AAD)**|
|:-----|:-----|
|Publicación HTTP selectiva para las aplicaciones de explorador|Disponible en WAP en Windows Server 2012 R2 (disponible ya en PA AAD)|
|Integración de AD FS|Disponible en WAP en Windows Server 2012 R2 (disponible ya en PA AAD)|
|Publicación de protocolos enriquecidos (como Citrix, Lync, puerta de enlace de Escritorio remoto)|Disponible en WAP en Windows Server 2012 R2 (disponible ya en PA AAD)|
|Autenticación previa para ActiveSync (HTTP básico) y puerta de enlace de Escritorio remoto|Estará disponible en WAP en Windows Server vNext (próximamente en PA AAD)|
|Portal|Usar Intune / System Center para WAP (usar el Panel de acceso de AAD o el Iniciador de aplicaciones de Office 365 disponible para AADAP)|
|Detección del estado de punto de conexión|Usar Intune y System Center|
|Tunelización de SSH|Usar la funcionalidad SSL/VPN de Windows|
|Firewall de capa 2/3|Usar funciones de Windows Server|
|Firewall de aplicaciones web|No hay ninguna solución actual de Microsoft|
|Puerta de enlace web segura (proxy de reenvío)|No hay ninguna solución actual de Microsoft|


## <a name="next-steps"></a>Pasos siguientes

[Blog: Web Application Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy) (Blog: proxy de aplicación web)<br>
[Blog: Application Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap) (Blog: proxy de aplicación)



<!--HONumber=Feb17_HO1-->


