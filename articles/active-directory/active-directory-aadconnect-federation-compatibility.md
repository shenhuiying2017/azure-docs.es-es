<properties
	pageTitle="Lista de compatibilidad de federación de Azure AD"
	description="Esta página presenta proveedores de identidades de terceros que se pueden utilizar para implementar el inicio de sesión único."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="billmath"/>

# Lista de compatibilidad de federación de Azure AD
Azure Active Directory brinda mayor seguridad en el acceso a la aplicación e inicio de sesión único para Office 365 y otros servicios en línea de Microsoft para implementaciones híbridas y solo en la nube sin que sea necesaria ninguna solución de terceros. Office 365, al igual que la mayoría de los servicios en línea de Microsoft, está integrado con Azure Active Directory para autorización, autenticación y servicios de directorio. Azure Active Directory también proporciona inicio de sesión único a miles de aplicaciones SaaS y aplicaciones web locales. Consulte la galería de aplicaciones de Azure Active Directory para ver las aplicaciones SaaS compatibles.

En el caso de las organizaciones que invirtieron en soluciones de federación de terceros, este tema contiene guías sobre cómo configurar el inicio de sesión único para sus usuarios de Windows Server Active Directory con servicios en línea de Microsoft mediante el uso de proveedores de identidades de terceros que se incluyen en la "Lista de compatibilidad de federación de Azure Active Directory" que aparece más adelante.


![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Oxford Computer Group](http://oxfordcomputergroup.com/), un tercero, probó, en nombre de Microsoft, estas experiencias de inicio de sesión único con proveedores de identidades de terceros en relación con un conjunto de casos de uso común con Azure Active Directory.

Para obtener información sobre cómo obtener el proveedor de identidades de terceros que figura aquí, póngase en contacto con Oxford Computer Group en [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com).

>[AZURE.IMPORTANT] Oxford Computer Group probó únicamente la funcionalidad de federación de estos escenarios de inicio de sesión único. Oxford Computer Group no llevó a cabo ninguna prueba de sincronización, de autenticación de dos factores, etc., componentes de estos escenarios de inicio de sesión único.

>En este programa, tampoco se prueba el uso de inicio de sesión por un identificador alternativo a UPN.



- [Azure Active Directory](#azure-active-directory)
- [Servicios de federación de servidores de identidad virtual de Optimal IDM](#optimal-idm-virtual-identity-server-federation-services)
- [PingFederate 6.11](#pingfederate-611)
- [PingFederate 7.2](#pingfederate-72)
- [PingFederate 8.x](#pingfederate-8.x)
- [Centrify](#centrify)
- [IBM Tivoli Federated Identity Manager 6.2.2](#ibm-tivoli-federated-identity-manager-622)
- [SecureAuth IdP 7.2.0](#secureauth-idp-720)
- [CA SiteMinder 12.52](#ca-siteminder-1252)
- [RadiantOne CFS 3.0](#radiantone-cfs-30)
- [Okta](#okta)
- [OneLogin](#onelogin)
- [NetIQ Access Manager 4.0.1](#netiq-access-manager-401)
- [BIG-IP con Access Policy Manager BIG-IP ver. 11.3x – 11.6x](#big-ip-with-access-policy-manager-big-ip-ver-113x-116x)
- [VMware Workspace Portal versión 2.1](#vmware-workspace-portal-version-21)
- [Sign&go 5.3](#signampgo-53)
- [IceWall Federation versión 3.0](#icewall-federation-version-30)
- [CA Secure Cloud](#ca-secure-cloud)
- [Dell One Identity Cloud Access Manager v7.1](#dell-one-identity-cloud-access-manager-v71)
- [AuthAnvil Single Sign On 4.5](#authavil-single-sign-on-45)

>[AZURE.IMPORTANT] Dado que estos son productos de otros fabricantes, Microsoft no proporciona soporte técnico para la implementación, la configuración, la solución de problemas, los procedimientos recomendados, etc., ni para los problemas o las preguntas que surjan con respecto a estos proveedores de identidades. Para obtener soporte técnico o para preguntas relacionadas con estos proveedores de identidades, póngase en contacto directamente con los fabricantes.

>Se ha probado la interoperabilidad de estos proveedores de identidades de terceros con los servicios en la nube de Microsoft usando únicamente los protocolos WS-Federation y WS-Trust. La prueba no incluyó el protocolo SAML.

## Azure Active Directory 
Azure Active Directory puede autenticar a los usuarios si establece una federación con su instancia de Active Directory local o sin un servidor de federación local mediante el uso de la sincronización de contraseñas.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión:


| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |None|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |None|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|
|Aplicaciones modernas que usan ADAL, como Office 2016| Compatible|None|

Para más información sobre el uso de Azure Active Directory con AD FS, consulte la referencia sobre [Servicios de federación de Active Directory (AD FS)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)

Para más información sobre el uso de Azure Active Directory con sincronización de contraseñas, consulte la referencia sobre [Azure AD Connect](active-directory-aadconnect.md).


## Servicios de federación de servidores de identidad virtual de Optimal IDM 
Los Servicios de federación de servidores de identidad virtual de Optimal IDM pueden autenticar usuarios que residen en las instancias locales de Active Directory de los clientes.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:


| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |None|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |Autenticación integrada de Windows|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |Para más información sobre las directivas de acceso de cliente, consulte [Limiting Access to Office 365 Services Based on the Location of the Client](https://technet.microsoft.com/library/hh526961.aspx) (Limitación del acceso a servicios de Office 365 según la ubicación del cliente).|



## PingFederate 6.11 

PingFederate 6.11 implementa el estándar de identidad ampliamente utilizado WS Federation a fin de proporcionar un inicio de sesión único y un marco de intercambio de atributos.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:


| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |None|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |Ninguna (las versiones anteriores deben actualizarse a 6.11)|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|

Para conocer las instrucciones de PingFederate sobre cómo configurar este STS para ofrecer una experiencia de inicio de sesión único a los usuarios de Active Directory, descargue el archivo .pdf [aquí.](http://go.microsoft.com/fwlink/?LinkID=266321)

## PingFederate 7.2 
PingFederate 7.2 implementa el estándar de identidad ampliamente utilizado WS Federation/WS-Trust a fin de proporcionar un inicio de sesión único y un marco de intercambio de atributos.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:


| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |None|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |None|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|

Para conocer las instrucciones de PingFederate sobre cómo configurar este STS para ofrecer una experiencia de inicio de sesión único a los usuarios de Active Directory, consulte [esta referencia.](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)

## PingFederate 8.x 
PingFederate 8.x implementa el estándar de identidad ampliamente utilizado WS Federation/WS-Trust a fin de proporcionar un inicio de sesión único y un marco de intercambio de atributos.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:


| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |None|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |None|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|

Para conocer las instrucciones de PingFederate sobre cómo configurar este STS para ofrecer una experiencia de inicio de sesión único a los usuarios de Active Directory, consulte [esta referencia.](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## Centrify 
Centrify permite proporcionar una experiencia federada de inicio de sesión único para Office 365 sin el requisito de hospedar un servidor de federación local.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:


| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |None|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |None|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |No se admite el control de acceso de clientes. 

Para más información sobre Centrify, consulte [esta referencia.](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp)|

## IBM Tivoli Federated Identity Manager 6.2.2 
IBM Tivoli Federated Identity Manager 6.2.2 con IBM Security Access Manager for Microsoft Applications 1.4 implementa el estándar de identidad WS-Federation/WS-Trust ampliamente utilizado para proporcionar un inicio de sesión único y un marco de intercambio de atributos.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |None|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |None|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|

Para más información sobre IBM Tivoli Federated Identity Manager, consulte [IBM Security Access Manager for Microsoft Applications](http://www-01.ibm.com/support/docview.wss?uid=swg24029517) (Administrador de acceso de seguridad de IBM para aplicaciones de Microsoft).

## SecureAuth IdP 7.2.0 
SecureAuth IdP 7.2.0 implementa el estándar de identidad ampliamente utilizado WS Federation/WS-Trust a fin de proporcionar una experiencia de inicio de sesión único y un marco de intercambio de atributos.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |None|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |None|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|

Para más información sobre SecureAuth, consulte [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293).

## CA SiteMinder 12.52 
CA SiteMinder Federation 12.52 implementa el estándar de identidad ampliamente utilizado WS Federation/WS-Trust a fin de proporcionar un inicio de sesión único y un marco de intercambio de atributos.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |Autenticación integrada de Windows|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |Autenticación integrada de Windows|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|

Para más información sobre CA SiteMinder, consulte [CA SiteMinder Federation](http://www.ca.com/us/products/ca-single-sign-on.html) (Federación de CA SiteMinder).

## RadiantOne CFS 3.0 
RadiantOne Cloud Federation Service (CFS) 3.0 implementa el estándar de identidad ampliamente utilizado WS Federation/WS-Trust a fin de proporcionar un inicio de sesión único y un marco de intercambio de atributos.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |None|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |Autenticación integrada de Windows|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|

Para más información sobre RadiantOne CFS, consulte [RadiantOne CFS](http://www.radiantlogic.com/products/radiantone-cfs/).


## Okta 
Okta implementa el estándar de identidad ampliamente utilizado WS Federation/WS-Trust a fin de proporcionar un inicio de sesión único y un marco de intercambio de atributos.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:


| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |La autenticación integrada de Windows requiere la instalación de un servicio web adicional y de la aplicación Okta.|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |Autenticación integrada de Windows|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|

Para más información sobre Okta, consulte [Okta](https://www.okta.com/).
 
## OneLogin 
OneLogin, según se probó en mayo de 2014, implementa el estándar de identidad ampliamente utilizado WS-Federation/WS-Trust a fin de proporcionar un inicio de sesión único y un marco de intercambio de atributos.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |Autenticación integrada de Windows|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |Autenticación integrada de Windows|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|

Para más información sobre OneLogin, consulte [OneLogin.](https://www.onelogin.com/)

## NetIQ Access Manager 4.0.1 
NetIQ Access Manager 4.0.1 implementa el estándar de identidad ampliamente utilizado WS Federation/WS-Trust a fin de proporcionar un inicio de sesión único y un marco de intercambio de atributos.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |* Admite contratos con Kerberos.|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |No se admite la autenticación integrada de Windows|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|

*NetIQ admite autenticación Kerberos mediante la configuración de un contrato con Kerberos. Para obtener ayuda sobre esta configuración, póngase en contacto con NetIQ o consulte la guía de configuración. Para más información sobre NetIQ Access Manager, consulte [NetIQ Access Manager](https://www.netiq.com/documentation/netiqaccessmanager4/identityserverhelp/data/b12iqp0m.html).

## BIG-IP con Access Policy Manager BIG-IP ver. 11.3x – 11.6x 
BIG-IP con Access Policy Manager (APM), BIG-IP ver. 11.3x – 11.6x implementa el estándar de identidad SAML ampliamente utilizado para proporcionar un marco de inicio de sesión único e intercambio de atributos.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |None|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | No compatible |No compatible|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|

Para más información sobre BIG-IP Access Policy Manager, consulte la referencia sobre [BIG-IP Access Policy Manager](https://f5.com/products/modules/access-policy-manager).

Para obtener instrucciones de BIG-IP Access Policy Manager para configurar este STS con el fin de proporcionar una experiencia de inicio de sesión único a los usuarios de Active Directory, descargue el archivo .pdf [aquí](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf).

## VMware Workspace Portal versión 2.1 
VMware Workspace Portal versión 2.1 implementa el estándar de identidad ampliamente utilizado WS Federation/WS-Trust a fin de proporcionar un inicio de sesión único y un marco de intercambio de atributos.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |No se admite la autenticación integrada de Windows|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |No se admite la autenticación integrada de Windows|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|

Para más información sobre VMware Workspace Portal versión 2.1, descargue el archivo .pdf [aquí.](http://pubs.vmware.com/workspace-portal-21/topic/com.vmware.ICbase/PDF/workspace-portal-21-resource.pdf)

## Sign&go 5.3 
Sign&go 5.3 implementa el estándar de identidad ampliamente utilizado WS Federation/WS-Trust a fin de proporcionar un inicio de sesión único y un marco de intercambio de atributos.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |Admite contratos con Kerberos |
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |None|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|


Sign&go 5.3 admite la autenticación Kerberos mediante la configuración de un contrato con Kerberos. Para obtener ayuda sobre esta configuración, póngase en contacto con Ilex o consulte la guía de configuración [aquí](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf).


## IceWall Federation versión 3.0 
IceWall Federation versión 3.0 implementa el estándar de identidad ampliamente utilizado WS Federation/WS-Trust a fin de proporcionar un inicio de sesión único y un marco de intercambio de atributos.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |No se admite la autenticación integrada de Windows|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |No se admite la autenticación integrada de Windows|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|

Para más información sobre IceWall Federation, consulte [esta página](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) y [esta referencia](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html).

## CA Secure Cloud 

CA Secure Cloud implementa el estándar de identidad ampliamente utilizado WS Federation/WS-Trust a fin de proporcionar un inicio de sesión único y un marco de intercambio de atributos.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |No se admite la autenticación integrada de Windows|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |No se admite la autenticación integrada de Windows|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|

Para más información sobre CA Secure Cloud, consulte la referencia sobre [CA Secure Cloud](http://www.ca.com/us/products/security-as-a-service.aspx).

## Dell One Identity Cloud Access Manager v7.1 
Dell One Identity Cloud Access Manager implementa el estándar de identidad ampliamente utilizado WS Federation/WS-Trust a fin de proporcionar un inicio de sesión único y un marco de intercambio de atributos.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |None|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |None|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|

Para más información sobre Dell One Identity Cloud Access Manager, consulte la referencia sobre [Dell One Identity Cloud Access Manager](http://software.dell.com/products/cloud-access-manager).

 Para obtener instrucciones sobre cómo configurar este STS con el fin de proporcionar la experiencia de inicio de sesión único a los usuarios de Office 365, consulte [Configure Office 365 Users](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365) (Configurar usuarios de Office 365).

## AuthAnvil Single Sign On 4.5 
AuthAnvil Single Sign On 4.5 implementa el estándar de identidad WS Federation/WS-Trust, ampliamente utilizado, para proporcionar un marco de inicio de sesión único e intercambio de atributos.

A continuación, se presenta la matriz de compatibilidad de escenarios para esta experiencia de inicio de sesión único:

| Cliente |Soporte técnico |Excepciones|
| --------- | --------- |--------- |
| Clientes basados en web como Exchange Web Access y SharePoint Online | Compatible |No se admite la autenticación integrada de Windows|
| Aplicaciones cliente enriquecidas como Lync, suscripción a Office, CRM | Compatible |No se admite la autenticación integrada de Windows|
| Clientes de correo electrónico enriquecidos, como Outlook y ActiveSync | Compatible |None|


Para más información, consulte [AuthAnvil Single Sign On](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-) (Inicio de sesión único de AuthAnvil).

<!----HONumber=AcomDC_0720_2016-->
