---
title: "Lista de compatibilidad de federación de Azure AD"
description: "Esta página presenta proveedores de identidades de terceros que se pueden utilizar para implementar el inicio de sesión único."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: billmath
ms.openlocfilehash: 86c247b6a9d197e89040109ac6b8686e3310dbf5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-federation-compatibility-list"></a>Lista de compatibilidad de federación de Azure AD
Azure Active Directory brinda mayor seguridad en el acceso a la aplicación e inicio de sesión único para Office 365 y otros servicios en línea de Microsoft para implementaciones híbridas y solo en la nube sin que sea necesaria ninguna solución de terceros. Office 365, al igual que la mayoría de los servicios en línea de Microsoft, está integrado con Azure Active Directory para autorización, autenticación y servicios de directorio. Azure Active Directory también proporciona inicio de sesión único a miles de aplicaciones SaaS y aplicaciones web locales. Consulte la galería de aplicaciones de Azure Active Directory para ver las aplicaciones SaaS compatibles.

En el caso de las organizaciones que invirtieron en soluciones de federación de terceros, este tema contiene guías sobre cómo configurar el inicio de sesión único para sus usuarios de Windows Server Active Directory con servicios en línea de Microsoft mediante el uso de proveedores de identidades de terceros que se incluyen en la "Lista de compatibilidad de federación de Azure Active Directory" que aparece más adelante. 

![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Oxford Computer Group](http://oxfordcomputergroup.com/), un tercero, probó, en nombre de Microsoft, estas experiencias de inicio de sesión único con proveedores de identidades de terceros en relación con un conjunto de casos de uso común con Azure Active Directory.

Para obtener información sobre cómo obtener el proveedor de identidades de terceros que figura aquí, póngase en contacto con Oxford Computer Group en [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com).

> [!IMPORTANT]
> Oxford Computer Group probó únicamente la funcionalidad de federación de estos escenarios de inicio de sesión único. Oxford Computer Group no llevó a cabo ninguna prueba de sincronización, de autenticación de dos factores, etc., componentes de estos escenarios de inicio de sesión único.
> 
> En este programa, tampoco se prueba el uso de inicio de sesión por un identificador alternativo a UPN.
> 
> 

* [Azure Active Directory](#azure-active-directory)
* [AuthAnvil Single Sign On 4.5](#authanvil-single-sign-on-45)
* [BIG-IP con Access Policy Manager BIG-IP ver. 11.3x – 11.6x](#big-ip-with-access-policy-manager-big-ip-ver-113x--116x) 
* [BitGlass](#bitglass)
* [CA Secure Cloud](#ca-secure-cloud) 
* [CA SiteMinder 12.52](#ca-siteminder-1252-sp1-cumulative-release-4) 
* [Centrify](#centrify) 
* [Dell One Identity Cloud Access Manager v7.1](#dell-one-identity-cloud-access-manager-v71) 
* [DigitalPersona Composite Authentication](#digitalpersona-composite-authentication)
* [ForgeRock Identity Platform Access Management V5.x](#forgerock-identity-platform-access-management-v5x)
* [IBM Tivoli Federated Identity Manager 6.2.2](#ibm-tivoli-federated-identity-manager-622) 
* [IceWall Federation versión 3.0](#icewall-federation-version-30) 
* [Memority](#memority)
* [NetIQ Access Manager 4.x](#netiq-access-manager-4x) 
* [Okta](#okta) 
* [OneLogin](#onelogin) 
* [Servicios de federación de servidores de identidad virtual de Optimal IDM](#optimal-idm-virtual-identity-server-federation-services) 
* [PingFederate 6.11, 7.2, 8.x](#pingfederate-611-72-8x)
* [RadiantOne CFS 3.0](#radiantone-cfs-30) 
* [Sailpoint IdentityNow](#sailpoint-identitynow)
* [SecureAuth IdP 7.2.0](#secureauth-idp-720) 
* [Sign&amp;go 5.3](#signgo-53) 
* [SoftBank Technology Online Service Gate](#softbank)
* [VMware Workspace One](#vmware-workspace-one)



> [!IMPORTANT]
> Dado que estos son productos de otros fabricantes, Microsoft no proporciona soporte técnico para la implementación, la configuración, la solución de problemas, los procedimientos recomendados, etc., ni para los problemas o las preguntas que surjan con respecto a estos proveedores de identidades. Para obtener soporte técnico o para preguntas relacionadas con estos proveedores de identidades, póngase en contacto directamente con los fabricantes.
> 
> Se ha probado la interoperabilidad de estos proveedores de identidades de terceros con los servicios en la nube de Microsoft usando únicamente los protocolos WS-Federation y WS-Trust. La prueba no incluyó el protocolo SAML.
> 


## <a name="azure-active-directory"></a>Azure Active Directory

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión:  

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |None |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |None |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |
| Aplicaciones modernas que usan ADAL, como Office 2016 |Compatible |None |

Para obtener más información sobre el uso de Azure Active Directory con AD FS, vea [Servicios de federación de Active Directory (AD FS)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs).

Para más información sobre el uso de Azure Active Directory con sincronización de contraseñas, consulte la referencia sobre [Azure AD Connect](active-directory-aadconnect.md).

## <a name="authanvil-single-sign-on-45"></a>AuthAnvil Single Sign On 4.5

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |No se admite la autenticación integrada de Windows |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |No se admite la autenticación integrada de Windows |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para obtener más información, vea [AuthAnvil Single Sign On](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-).


## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>BIG-IP con Access Policy Manager BIG-IP ver. 11.3x – 11.6x

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único: 

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |None |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |No compatible |No compatible |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para más información sobre BIG-IP Access Policy Manager, consulte la referencia sobre [BIG-IP Access Policy Manager](https://f5.com/products/modules/access-policy-manager) 

Para obtener instrucciones de BIG-IP Access Policy Manager para configurar este STS con el fin de proporcionar una experiencia de inicio de sesión único a los usuarios de Active Directory, descargue el archivo .pdf [BIG-IP](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf).

## <a name="bitglass"></a>BitGlass

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |No se admite la autenticación integrada de Windows |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |No se admite la autenticación integrada de Windows |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para obtener más información sobre BitGlass, vea [BitGlass](http://www.bitglass.com).

## <a name="ca-secure-cloud"></a>CA Secure Cloud

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |No se admite la autenticación integrada de Windows |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |No se admite la autenticación integrada de Windows |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para obtener más información sobre CA Secure Cloud, vea [CA Secure Cloud](http://www.ca.com/us/products/security-as-a-service.aspx).

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>CA SiteMinder 12.52 SP1 Cumulative Release 4

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único: 

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |None |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |None |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para obtener más información sobre CA SiteMinder, vea [CA SiteMinder Federation](http://www.ca.com/us/products/ca-single-sign-on.html). 

## <a name="centrify"></a>Centrify

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |None |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |None |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |No se admite el control de acceso de clientes. |

Para obtener más información sobre Centrify, vea [Centrify](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp).

## <a name="dell-one-identity-cloud-access-manager-v71"></a>Dell One Identity Cloud Access Manager v7.1

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |None |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |None |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para obtener más información sobre Dell One Identity Cloud Access Manager, vea [Dell One Identity Cloud Access Manager](http://software.dell.com/products/cloud-access-manager).

 Para obtener instrucciones sobre cómo configurar este STS con el fin de proporcionar la experiencia de inicio de sesión único a los usuarios de Office 365, vea [Configurar Usuarios de Office 365](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365). 

## <a name="digitalpersona-composite-authentication"></a>DigitalPersona Composite Authentication  

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |No se admite la autenticación integrada de Windows|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |No se admite la autenticación integrada de Windows|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para obtener más información, vea [DigitalPersona Composite Authentication](http://www.crossmatch.com/uploadedFiles/Support/Reference_Material/DigitalPersona-Office-365-Deployment-Guide.pdf).

## <a name="forgerock-identity-platform-access-management-v5x"></a>ForgeRock Identity Platform Access Management V5.x

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |None|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |None|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para obtener más información, consulte [ForgeRock Identity Platform Access Management V5.x](https://backstage.forgerock.com/knowledge/kb/article/a98278517).

## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM Tivoli Federated Identity Manager 6.2.2

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único: 

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |None |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |None |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para obtener más información sobre IBM Tivoli Federated Identity Manager, vea [IBM Security Access Manager for Microsoft Applications](http://www-01.ibm.com/support/docview.wss?uid=swg24029517).

## <a name="icewall-federation-version-30"></a>IceWall Federation versión 3.0

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |No se admite la autenticación integrada de Windows |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |No se admite la autenticación integrada de Windows |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para obtener más información sobre IceWall Federation, vea [IceWall Federation versión 3.0](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) e [IceWall Federation con Office 365](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html).

## <a name="memority"></a>Memority

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión:  

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |None |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |None |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para obtener más información sobre el uso de Memority, vea [Memority](http://www.memority.com).


## <a name="netiq-access-manager-4x"></a>NetIQ Access Manager 4.x

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |None|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |None|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para obtener más información, vea [NetIQ Access Manager](https://www.netiq.com/documentation/access-manager-43/admin/data/b65ogn0.html#b12iqp0m).

## <a name="okta"></a>Okta

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único: 

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |La autenticación integrada de Windows requiere la instalación de un servicio web adicional y de la aplicación Okta. |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |Autenticación integrada de Windows |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para obtener más información sobre Okta, vea [Okta](https://www.okta.com/).

## <a name="onelogin"></a>OneLogin

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único: 

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |Autenticación integrada de Windows |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |Autenticación integrada de Windows |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para obtener más información sobre OneLogin, vea [OneLogin](https://www.onelogin.com/).

## <a name="optimal-idm-virtual-identity-server-federation-services"></a>Servicios de federación de servidores de identidad virtual de Optimal IDM

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |None |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |Autenticación integrada de Windows |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |

Para obtener más información sobre las directivas de acceso de cliente, vea [Limiting Access to Office 365 Services Based on the Location of the Client](https://technet.microsoft.com/library/hh526961.aspx) (Limitación del acceso a los servicios de Office 365 en función de la ubicación del cliente).





## <a name="pingfederate-611-72-8x"></a>PingFederate 6.11, 7.2, 8.x

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |None |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |None |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para conocer las instrucciones de PingFederate sobre cómo configurar este STS para ofrecer una experiencia de inicio de sesión único a los usuarios de Active Directory, vea uno de los siguientes vínculos: 

- [PingFederate 6.11](http://go.microsoft.com/fwlink/?LinkID=266321)
- [PingFederate 7.2](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)
- [PingFederate 8.x](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único: 

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |None |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |Autenticación integrada de Windows |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para obtener más información sobre RadiantOne CFS, vea [RadiantOne CFS](http://www.radiantlogic.com/products/radiantone-cfs/).

## <a name="sailpoint-identitynow"></a>Sailpoint IdentityNow

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |No se admite la autenticación integrada de Windows |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |No se admite la autenticación integrada de Windows |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para obtener más información, vea [Sailpoint IdentityNow](https://www.sailpoint.com/idaas-identity-as-a-service-identitynow/).

## <a name="secureauth-idp-720"></a>SecureAuth IdP 7.2.0

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único: 

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |None |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |None |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para más información sobre SecureAuth, consulte [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293).














## <a name="signgo-53"></a>Sign&go 5.3

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |Admite contratos con Kerberos |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |None |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Sign&go 5.3 admite la autenticación Kerberos mediante la configuración de un contrato con Kerberos.  Para obtener ayuda sobre esta configuración, póngase en contacto con Ilex o vea la guía de configuración [Sign&go](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf).

## <a name="softbank-technology-online-service-gate"></a>SoftBank Technology Online Service Gate

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |No se admite la autenticación integrada de Windows |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |No se admite la autenticación integrada de Windows |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para obtener más información sobre SoftBank Technology Online Service Gate, vea [Softbank](https://www.softbanktech.jp/service/list/osg-pro-ent/).

## <a name="vmware-workspace-one"></a>VMware Workspace One

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente | Soporte técnico | Excepciones |
| --- | --- | --- |
| Clientes basados en web como Exchange Web Access y SharePoint Online |Compatible |No se admite la autenticación integrada de Windows |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM |Compatible |No se admite la autenticación integrada de Windows |
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync |Compatible |None |

Para obtener más información, vea [VMware Workspace One](http://www.vmware.com/pdf/vidm-office365-saml.pdf).

