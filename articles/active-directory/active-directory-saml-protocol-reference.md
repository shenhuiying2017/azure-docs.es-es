---
title: Referencia del protocolo SAML de Azure AD | Microsoft Docs
description: En este artículo se proporciona información general de los perfiles SAML de inicio y cierre de sesión único de Azure Active Directory.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2016
ms.author: priyamo

---
# Cómo Azure Active Directory usa el protocolo SAML
Azure Active Directory (Azure AD) utiliza el protocolo SAML 2.0 para permitir que las aplicaciones ofrezcan una experiencia de inicio de sesión único a sus usuarios. Los perfiles SAML de [inicio](active-directory-single-sign-on-protocol-reference.md) y [cierre de sesión único](active-directory-single-sign-out-protocol-reference.md) de Azure AD explican cómo se usan las aserciones, los protocolos y los enlaces SAML en el servicio de proveedor de identidades.

El protocolo SAML requiere que el proveedor de identidades (Azure AD) y el proveedor de servicios (la aplicación) intercambien información sobre sí mismos.

Cuando una aplicación se registra en Azure AD, el desarrollador de esta registra información relacionada con la federación en Azure AD. Esto incluye el **URI de redireccionamiento** y de **metadatos** de la aplicación.

Azure AD usa el **URI de metadatos** del servicio en la nube para recuperar la clave de firma y el URI de cierre de sesión del servicio en la nube. Si la aplicación no admite un URI de metadatos, el desarrollador debe ponerse en contacto con el servicio de soporte técnico de Microsoft para proporcionar el URI de cierre de sesión y la clave de firma.

Azure Active Directory expone puntos de conexión de inicio y cierre de sesión único comunes y específicos del inquilino (independientes del inquilino). Estas direcciones URL representan ubicaciones direccionables; no se trata únicamente identificadores, así que puede acceder al punto de conexión para leer los metadatos.

* El punto de conexión específico del inquilino se encuentra en `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. El marcador de posición <TenantDomainName> representa un nombre de dominio registrado o GUID TenantID de un inquilino de Azure AD. Por ejemplo, los metadatos de federación del inquilino contoso.com se encuentran en https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml.

* El punto de conexión independiente del inquilino está en `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. En esta dirección de punto de conexión, se muestra **common**, en lugar de un id. o nombre de dominio del inquilino.

Para obtener información sobre los documentos de metadatos de federación que publica Azure AD, consulte [Metadatos de federación](active-directory-federation-metadata.md).

<!---HONumber=AcomDC_0629_2016-->