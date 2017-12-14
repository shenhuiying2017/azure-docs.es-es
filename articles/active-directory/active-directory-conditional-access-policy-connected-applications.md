---
title: "Configuración de directivas de acceso condicional basadas en dispositivos de Azure Active Directory | Microsoft Docs"
description: "Obtenga información sobre cómo configurar directivas de acceso condicional basadas en dispositivos de Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: fb646a56b51960d5b076027d942dabe8f2afbe97
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Configuración de directivas de acceso condicional basadas en dispositivos de Azure Active Directory

Con el [acceso condicional de Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), puede ajustar el modo en que los usuarios autorizados pueden tener acceso a sus recursos. Por ejemplo, limita el acceso a determinados recursos a dispositivos de confianza. Una directiva de acceso condicional que requiere un dispositivo de confianza se conoce también como directiva de acceso condicional basada en dispositivos.

En este tema se proporciona información sobre cómo configurar directivas de acceso condicional basadas en dispositivos para aplicaciones conectadas a Azure AD. 


## <a name="before-you-begin"></a>Antes de empezar

El acceso condicional basado en dispositivos une el **acceso condicional de Azure AD** y la **administración de dispositivos de Azure AD**. Si no está familiarizado con ninguna de estas áreas, debería leer los siguientes temas en primer lugar:

- **[Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md)**: en este tema se proporcionan tanto información general conceptual de acceso condicional como la terminología relacionada.

- **[Introducción a la administración de dispositivos en Azure Active Directory](device-management-introduction.md)**: en este tema se proporciona información general de las diversas opciones que tiene para conectar dispositivos con Azure AD. 


## <a name="trusted-devices"></a>Dispositivos de confianza

En un mundo Mobile First, Cloud First, Azure Active Directory permite el inicio de sesión único en dispositivos, aplicaciones y servicios desde cualquier parte. Para determinados recursos de su entorno, es posible que conceder acceso a los usuarios adecuados no sea una acción lo suficientemente buena. Además de los usuarios adecuados, puede que también requiera el uso de un dispositivo de confianza para tener acceso a un recurso. En su entorno, puede definir lo que es un dispositivo de confianza en función de los siguientes componentes:

- Las [plataformas de dispositivo](active-directory-conditional-access-azure-portal.md#device-platforms) de un dispositivo
- Si un dispositivo es compatible
- Si un dispositivo está unido a un dominio 

La [plataforma de dispositivo](active-directory-conditional-access-azure-portal.md#device-platforms) se caracteriza por el sistema operativo que se ejecuta en el dispositivo. En su directiva de acceso condicional basada en dispositivos, puede limitar el acceso a determinados recursos a plataformas de dispositivo específicas.



En una directiva de acceso condicional basada en dispositivos, puede requerir que se marquen dispositivos de confianza como compatibles.

![Aplicaciones de nube](./media/active-directory-conditional-access-policy-connected-applications/24.png)

Los dispositivos se pueden marcar como compatibles en el directorio por medio de:

- Intune 
- Un sistema administrado de dispositivos móviles de terceros que administra los dispositivos de Windows 10 a través de la integración de Azure AD 
 
  

Solo los dispositivos conectados a Azure AD pueden marcarse como compatibles. Para conectar un dispositivo a Azure Active Directory, tiene las opciones siguientes: 

- Registrado en Azure AD
- Unido a Azure AD
- Híbrido unido a Azure AD

    ![Aplicaciones de nube](./media/active-directory-conditional-access-policy-connected-applications/26.png)

Si tiene una superficie de Active Directory (AD) local, podría considerar la posibilidad de hacer que los dispositivos no conectados a Azure AD, pero unidos a AD, sean de confianza.

![Aplicaciones de nube](./media/active-directory-conditional-access-policy-connected-applications/25.png)


## <a name="next-steps"></a>Pasos siguientes

Antes de configurar una directiva de acceso condicional basada en dispositivos en su entorno, debería echar un vistazo a [Procedimientos recomendados para el acceso condicional en Azure Active Directory](active-directory-conditional-access-best-practices.md).

