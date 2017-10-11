---
title: "Acceso a Key Vault detrás de un firewall | Microsoft Docs"
description: "Aprenda a acceder a Azure Key Vault desde una aplicación detrás de un firewall"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 50d21774-2ee1-4212-8995-570c9de603c5
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: ambapat
ms.openlocfilehash: d00c6e0acf437d2bfc3c27e948f4646a6685b08f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>Acceso a Azure Key Vault desde detrás de un firewall
### <a name="q-my-key-vault-client-application-needs-to-be-behind-a-firewall-what-ports-hosts-or-ip-addresses-should-i-open-to-enable-access-to-a-key-vault"></a>P: mi aplicación cliente de Key Vault debe estar detrás de un firewall. ¿Qué puertos, hosts o direcciones IP debo abrir para habilitar el acceso a un almacén de claves?
Para acceder a un almacén de claves, la aplicación cliente de Key Vault tiene que acceder a varios puntos de conexión para diversas funcionalidades:

* Autenticación a través de Azure Active Directory (Azure AD).
* Administración de Azure Key Vault. Aquí se incluye la creación, lectura, actualización, eliminación y establecimiento de directivas de acceso a través de Azure Resource Manager.
* Acceso y administración de los objetos (claves y secretos) almacenados en el propio Key Vault a través del punto de conexión específico del Key Vault (por ejemplo, [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).  

En función de su configuración y entorno, hay algunas variaciones.   

## <a name="ports"></a>Puertos
Todo el tráfico a un almacén de claves de las tres funciones (autenticación, la administración y acceso al plano de datos) se realiza a través de HTTPS: puerto 443. Sin embargo, ocasionalmente habrá tráfico HTTP (puerto 80) para CRL. Los clientes que admiten OCSP no deberían poder acceder a CRL, pero a veces pueden hacerlo a [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).  

## <a name="authentication"></a>Autenticación
Será preciso que las aplicaciones cliente de Key Vault accedan a los puntos de conexión de Azure Active Directory para la autenticación. El punto de conexión que se utilice dependerá de la configuración del inquilino de Azure AD, del tipo de entidad de seguridad (entidad de seguridad de usuario o entidad de servicio) y del tipo de cuenta (por ejemplo, cuenta Microsoft o una cuenta profesional o educativa.  

| Tipo de entidad de seguridad | Punto de conexión:puerto |
| --- | --- |
| Usuario que utiliza cuenta Microsoft<br> (por ejemplo, user@hotmail.com) |**Global:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure Gobierno de EE. UU.:**<br> login-us.microsoftonline.com:443<br><br>**Azure Alemania:**<br> login.microsoftonline.de:443<br><br> y <br>login.live.com:443 |
| Usuario o entidad de servicio con una cuenta profesional o educativa con Azure AD (por ejemplo, user@contoso.com) |**Global:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure Gobierno de EE. UU.:**<br> login-us.microsoftonline.com:443<br><br>**Azure Alemania:**<br> login.microsoftonline.de:443 |
| Usuario o entidad de servicio con un trabajo o cuenta del centro educativo, además de los servicios de federación de Active Directory (AD FS) u otro punto de conexión federado (por ejemplo, user@contoso.com) |Todos los puntos de conexión de una cuenta profesional o educativa, más AD FS u otros puntos de conexión federados |

Hay otros escenarios complejos posibles. Para más información, consulte [Escenarios de autenticación para Azure AD](/documentation/articles/active-directory-authentication-scenarios/), [Integración de aplicaciones con Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) y [Guía del desarrollador de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx).  

## <a name="key-vault-management"></a>Administración de Key Vault
Para la administración de Key Vault (CRUD y establecimiento de una directiva de acceso), es preciso que la aplicación cliente de Key Vault acceda a un punto de conexión de Azure Resource Manager.  

| Tipo de operación | Punto de conexión:puerto |
| --- | --- |
| Operaciones del plano de control de Key Vault<br> a través de Azure Resource Manager |**Global:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Azure Gobierno de EE. UU.:**<br> management.usgovcloudapi.net:443<br><br> **Azure Alemania:**<br> management.microsoftazure.de:443 |
| API Graph de Azure Active Directory |**Global:**<br> graph.windows.net:443<br><br> **Azure China:**<br> graph.chinacloudapi.cn:443<br><br> **Azure Gobierno de EE. UU.:**<br> graph.windows.net:443<br><br> **Azure Alemania:**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Operaciones de Key Vault
Para todas las operaciones criptográficas y de administración de objetos (claves y secretos) de Key Vault, es preciso que el cliente de Key Vault acceda al punto de conexión de Key Vault. En función de la ubicación del almacén de claves, el sufijo DNS del punto de conexión varía. El punto de conexión de Key Vault tiene el formato *vault-name*.*region-specific-dns-suffix*, como se describe en la tabla siguiente.  

| Tipo de operación | Punto de conexión:puerto |
| --- | --- |
| Operaciones que incluyen operaciones criptográficas en claves; crear, leer, actualizar y eliminar claves y secretos; establecer u obtener etiquetas y otros atributos de objetos de Key Vault (claves o secretos) |**Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure Gobierno de EE. UU.:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Alemania:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>Intervalos de direcciones IP
El servicio Key Vault utiliza otros recursos de Azure como la infraestructura de PaaS. Por consiguiente, no es posible proporcionar un intervalo específico de direcciones IP que los puntos de conexión del servicio Key Vault tendrán en un momento concreto. Si el firewall admite solo intervalos de direcciones IP, consulte el documento [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Intervalos de IP de los centro de datos de Microsoft Azure). Para la autenticación y la identidad (Azure Active Directory), la aplicación debe poder conectarse a los puntos de conexión descritos en [Autenticación y direcciones de identidad](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="next-steps"></a>Pasos siguientes
Si le queda alguna duda acerca de Key Vault, visite los [foros de Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

