---
title: Historial de versiones de Azure AD Connect Health
description: Este documento describe las versiones de Azure AD Connect Health y lo que se ha incluido en dichas versiones.
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 6c990a184d44771c78330f54f518bb4c35a36a35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: historial de versiones
El equipo de Azure Active Directory actualiza periódicamente Azure AD Connect Health con nuevas características y funciones. En este artículo se enumeran las versiones y características que se han publicado.

## <a name="october-2016"></a>Octubre de 2016
**Actualización del agente:**

* Agente de Azure AD Connect Health para AD FS \(versión 2.6.408.0\)
  1. Mejoras en la detección de direcciones IP de cliente en las solicitudes de autenticación
  2. Correcciones de errores relacionados con las alertas
* Agente de Azure AD Connect Health para AD DS (versión 2.6.408.0)
  1. Correcciones de errores relacionados con las alertas
* Agente de Azure AD Connect Health para sincronización (versión 2.6.353.0), publicado con Azure AD Connect versión 1.1.281.0
  1. Proporcionar los datos necesarios para los informes de errores de sincronización
  2. Correcciones de errores relacionados con las alertas

**Nuevas características de la versión preliminar:**

* Informes de errores de sincronización de Azure AD Connect

**Nuevas características:**

* Azure AD Connect Health para AD FS: campo de dirección IP está disponible en el informe de los 50 usuarios principales con el nombre de usuario o contraseña incorrectos.

## <a name="july-2016"></a>Julio de 2016
**Nuevas características de la versión preliminar:**

* [Azure AD Connect Health para AD DS](active-directory-aadconnect-health-adds.md).

## <a name="january-2016"></a>Enero de 2016
**Actualización del agente:**

* Agente de Azure AD Connect Health para AD FS (versión 2.6.91.1512)

**Nuevas características:**

* [Herramienta de conexión de prueba para agentes de Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>Noviembre de 2015
**Nuevas características:**

* Soporte para el [control de acceso basado en rol](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)

**Nuevas características de la versión preliminar:**

* [Azure AD Connect Health para sincronización](active-directory-aadconnect-health-sync.md)

**Problemas corregidos:**

* Correcciones de errores detectados durante los registros del agente.

## <a name="september-2015"></a>Septiembre de 2015
**Nuevas características:**

* Informe de contraseña de nombre de usuario incorrecto para AD FS
* Soporte técnico para configurar el proxy de HTTP sin autenticar
* Soporte técnico para configurar el agente en Server Core
* Mejoras en las alertas de AD FS
* Mejoras en el agente de Azure AD Connect Health para AD FS en la conectividad y carga de datos.

**Problemas corregidos:**

* Correcciones de errores en el uso de tipos de errores de AD FS.

## <a name="june-2015"></a>Junio de 2015
**Versión inicial de Azure AD Connect Health para AD FS y el proxy de AD FS.**

**Nuevas características:**

* Alertas de supervisión de AD FS y servidores proxy de AD FS con notificaciones de correo electrónico.
* Fácil acceso a la topología y patrones de AD FS en los contadores de rendimiento de AD FS.
* Tendencia de las solicitudes correctas de token en servidores de AD FS agrupados por aplicaciones, métodos de autenticación, ubicación de la red de solicitud, etc.
* Tendencias en las solicitudes con error en los servidores de AD FS agrupados por aplicaciones, tipos de error, etc.
* Implementación más sencilla del agente mediante credenciales de administrador global de Azure AD.  

## <a name="next-steps"></a>Pasos siguientes
Conozca más detalles acerca de la [Supervisión de la infraestructura de identidad local y los servicios de sincronización en la nube](active-directory-aadconnect-health.md).

