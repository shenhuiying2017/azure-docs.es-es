---
title: Acceso a Key Vault detrás de un firewall | Microsoft Docs
description: Aprenda a acceder a Key Vault desde una aplicación detrás de un firewall
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager

ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/13/2016
ms.author: ambapat

---
# Acceso a Key Vault detrás de un firewall
### P: Mi aplicación cliente de Key Vault debe estar detrás de un firewall, ¿qué puertos, hosts y direcciones IP debo abrir para habilitar el acceso a Key Vault?
Para acceder a Key Vault, es preciso que la aplicación cliente de Key Vault pueda acceder a varios puntos de conexión para diversas funcionalidades.

* Autenticación (a través de Azure Active Directory)
* Administración de Key Vault (que incluye no solo la creación, lectura, actualización o eliminación, sino también el establecimiento de directivas de acceso) a través de Azure Resource Manager
* Acceso y administración de los objetos (claves y secretos) almacenados en el propio Key Vault a través del punto de conexión específico del Key Vault (por ejemplo, [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).

En función de su configuración y entorno, hay algunas variaciones.

## Puertos
Todo el tráfico al Key Vault de las tres funciones (autenticación, la administración y acceso al plano de datos) se realiza a través de HTTPS: puerto 443. Sin embargo, en el caso de CRL, habrá ocasionalmente tráfico HTTP (puerto 80). Los clientes que admiten OCSP no deberían poder acceder a CRL, pero a veces pueden hacerlo a [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).

## Autenticación
Será preciso que la aplicación cliente de Key Vault acceda a los puntos de conexión de Azure Active Directory para la autenticación. El punto de conexión que se utilice dependerá de la configuración del inquilino de AAD, del tipo de entidad de seguridad (entidad de seguridad de usuario y entidad de servicio) y del el tipo de cuenta, por ejemplo, cuenta de Microsoft o identificador de organización.

| Tipo de entidad de seguridad | Punto de conexión:puerto |
| --- | --- |
| Usuario que utiliza cuenta de Microsoft<br> (p. ej., user@hotmail.com) |**Global:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure gobierno EE.UU.:**<br> login-us.microsoftonline.com:443<br><br>**Azure Alemania:**<br> login.microsoftonline.de:443<br><br> y <br>login.live.com:443 |
| Entidad de seguridad de usuario o entidad de servicio que usa identificador de organización con AAD (p. ej., user@contoso.com) |**Global:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure gobierno EE.UU.:**<br> login-us.microsoftonline.com:443<br><br>**Azure Alemania:**<br> login.microsoftonline.de:443 |
| Entidad de seguridad de usuario o entidad de servicio que usa identificador de organización + ADFS o cualquier otro punto de conexión federado (p. ej., user@contoso.com) |Todos los puntos de conexión anteriores para el identificador de organización, más ADFS u otros puntos de conexión federados |

Hay otros escenarios complejos posibles. Para más información, consulte [Escenarios de autenticación para Azure AD](/documentation/articles/active-directory-authentication-scenarios/), [Integración de aplicaciones con Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) y [Guía del desarrollador de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx).

## Administración de Key Vault
Para la administración de Key Vault (CRUD y establecimiento de directiva de acceso), es preciso que la aplicación cliente de Key Vault acceda al punto de conexión de Azure Resource Manager.

| Tipo de operación | Punto de conexión:puerto |
| --- | --- |
| Operaciones del plano de control de Key Vault<br> a través de Azure Resource Manager |**Global:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Azure gobierno de EE.UU.:**<br> management.usgovcloudapi.net:443<br><br> **Azure Alemania:**<br> management.microsoftazure.de:443 |
| API Graph de Azure Active Directory |**Global:**<br> graph.windows.net:443<br><br> **Azure China:**<br> graph.chinacloudapi.cn:443<br><br> **Azure gobierno de EE.UU.:**<br> graph.windows.net:443<br><br> **Azure Alemania:**<br> graph.cloudapi.de:443 |

## Operaciones de Key Vault
Para todas las operaciones criptográficas y de administración de objetos (claves y secretos), es preciso que el cliente de Key Vault acceda al punto de conexión de Key Vault. En función de la ubicación de su Key Vault, el sufijo DNS del punto de conexión es diferente. El punto de conexión de Key Vault tiene el formato: < nombre del almacén>. < sufijo-dns-específico-de-región> como se describe en la tabla siguiente.

| Tipo de operación | Punto de conexión:puerto |
| --- | --- |
| Operaciones de Key Vault como operaciones criptográficas en claves; crear, leer, actualizar o eliminar claves y secretos; establecer y obtener etiquetas y otros atributos en objetos de Key Vault (claves y secretos) |**Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure gobierno de EE.UU.:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Alemania:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |

## Intervalos de direcciones IP
A su vez, el servicio Key Vault utiliza otros recursos de Azure como la infraestructura de PaaS, por lo que no es posible proporcionar un específico intervalo de direcciones IP que tendrán los puntos de conexión del servicio Key Vault en un momento dado. Sin embargo, si el firewall solo admite intervalos de direcciones IP, consulte el documento [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Intervalos de IP de centro de datos de Microsoft Azure). Para la autenticación e identidad (Azure Active Directory), la aplicación debe poder conectarse a los puntos de conexión que se describen en [Autenticación y direcciones de identidad](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## Pasos siguientes
* Si le queda alguna duda al respecto de Key Vault, visite los [foros de Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

<!---HONumber=AcomDC_0921_2016-->