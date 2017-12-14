---
title: "La identidad híbrida requería puertos y protocolos - Azure | Microsoft Docs"
description: "Esta página es una página de referencia técnica para puertos que deben estar abiertos para Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: billmath
ms.openlocfilehash: 79c339c136e614be8a98461533c63d244dabc166
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>La identidad híbrida requería puertos y protocolos
El documento siguiente es una referencia técnica sobre los puertos y protocolos para implementar una solución de identidad híbrida. Use la siguiente ilustración y consulte la tabla correspondiente.

![Qué es Azure AD Connect](./media/active-directory-aadconnect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabla 1: Azure AD Connect y AD local
En esta tabla se describen los puertos y protocolos que son necesarios para la comunicación entre el servidor de Azure AD Connect y AD local.

| Protocol | Puertos | Description |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Búsquedas DNS en el bosque de destino. |
| Kerberos |88 (TCP/UDP) |Autenticación Kerberos para el bosque de AD. |
| MS-RPC |135 (TCP/UDP) |Se usa durante la configuración inicial del Asistente para Azure AD Connect, cuando se enlaza con el bosque de AD, además de durante la sincronización de contraseñas. |
| LDAP |389 (TCP/UDP) |Se usa para la importación de datos de AD. Los datos se cifran con Kerberos Sign & Seal. |
| RPC | 445 (TCP/UDP) |Lo usa el SSO de conexión directa para crear una cuenta de equipo en el bosque de AD. |
| LDAP/SSL |636 (TCP/UDP) |Se usa para la importación de datos de AD. La transferencia de datos se firma y se cifra. Solo se utiliza si está usando SSL. |
| RPC |49152- 65535 (Puerto RCP alto aleatorio)(TCP/UDP) |Se usa durante la configuración inicial de Azure AD Connect, cuando se enlaza con los bosques de AD, además de durante la sincronización de contraseñas. Consulte [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017) y [KB224196](https://support.microsoft.com/kb/224196) para más información. |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabla 2: Azure AD Connect y Azure AD
En esta tabla se describen los puertos y protocolos que son necesarios para la comunicación entre el servidor de Azure AD Connect y Azure AD.

| Protocol | Puertos | Description |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Se usa para descargar CRL (listas de revocación de certificados) para comprobar certificados SSL. |
| HTTPS |443 (TCP/UDP) |Se usa para sincronizar con Azure AD. |

Para una lista de direcciones URL y de direcciones IP que necesita para abrir en el firewall, consulte [URL de Office 365 e intervalos de direcciones IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabla 3: Azure AD Connect y servidores de federación AD FS/WAP
En esta tabla se describen los puertos y protocolos que son necesarios para la comunicación entre el servidor de Azure AD Connect y servidores de federación AD FS/WAP.  

| Protocolo | Puertos | Description |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Se usa para descargar CRL (listas de revocación de certificados) para comprobar certificados SSL. |
| HTTPS |443 (TCP/UDP) |Se usa para sincronizar con Azure AD. |
| WinRM |5985 |Agente de escucha de WinRM |

## <a name="table-4---wap-and-federation-servers"></a>Tabla 4: Servidores de federación y WAP
En esta tabla se describen los puertos y protocolos que son necesarios para la comunicación entre los servidores de federación y los servidores WAP.

| Protocol | Puertos | Description |
| --- | --- | --- |
| HTTPS |443 (TCP/UDP) |Se usa para autenticación. |

## <a name="table-5---wap-and-users"></a>Tabla 5 - WAP y usuarios
En esta tabla se describen los puertos y protocolos que son necesarios para la comunicación entre los usuarios y los servidores WAP.

| Protocol | Puertos | Description |
| --- | --- | --- |
| HTTPS |443 (TCP/UDP) |Se usa para la autenticación de dispositivos. |
| TCP |49443 (TCP) |Se usa para la autenticación de certificados. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabla 6a y 6b: autenticación de paso a través con inicio de sesión único (SSO) y sincronización de Hash de contraseña con inicio de sesión único (SSO)
Las siguientes tablas describen los puertos y protocolos que son necesarios para la comunicación entre Azure AD Connect y Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabla 6a: autenticación de paso a través con SSO
|Protocol|Número de puerto|Descripción
| --- | --- | ---
|HTTP|80|Habilita el tráfico HTTP saliente para la validación de seguridad, como SSL. También se necesita para que la funcionalidad de actualización automática del conector se lleve a cabo correctamente.
|HTTPS|443| Habilite el tráfico HTTPS saliente para operaciones como habilitar y deshabilitar la característica, registrar conectores, descargar actualizaciones de conector y controlar todas las solicitudes de inicio de sesión de usuario.

Además, Azure AD Connect debe ser capaz de establecer conexiones directas de IP con [intervalos de direcciones IP del centro de datos de Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabla 6b: sincronización de Hash de contraseña con SSO

|Protocol|Número de puerto|Descripción
| --- | --- | ---
|HTTPS|443| Habilita el registro de SSO (solo es necesario para el proceso de registro de SSO).

Además, Azure AD Connect debe ser capaz de establecer conexiones directas de IP con [intervalos de direcciones IP del centro de datos de Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653). Una vez más, esto solo es necesario para el proceso de registro SSO.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabla 7a y 7b: Agente de Azure AD Connect Health para (AD FS/sincronización) y Azure AD
En las tablas siguientes se describen los puntos de conexión, puertos y protocolos que son necesarios para la comunicación entre los agentes de Azure AD Connect Health y Azure AD.

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabla 7a: Puertos y protocolos para el agente de Azure AD Connect Health para (AD FS/sincronización) y Azure AD
En esta tabla se describen los siguientes puertos y protocolos de salida que son necesarios para la comunicación entre los agentes de Azure AD Connect Health y Azure AD.  

| Protocol | Puertos | Description |
| --- | --- | --- |
| HTTPS |443 (TCP/UDP) |Salida |
| Azure Service Bus |5671 (TCP/UDP) |Salida |

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b: Puntos de conexión para el agente de Azure AD Connect Health para (AD FS/sincronización) y Azure AD
Para ver una lista de puntos de conexión, consulte [la sección de requisitos para el agente de Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-agent-install.md#requirements).

