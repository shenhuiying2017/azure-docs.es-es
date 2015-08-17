<properties 
	pageTitle="Azure AD Connect: requisitos del certificado SSL" 
	description="Requisitos del certificado SSL de Azure AD Connect para su uso con AD FS." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Azure AD Connect: requisitos del certificado SSL

**Importante:** se recomienda encarecidamente utilizar el mismo certificado SSL en todos los nodos de la granja de AD FS, así como todos los servidores del Proxy de aplicación web.

- Este certificado debe ser del tipo X 509. 
- Puede usar un certificado autofirmado en servidores de federación en un entorno de laboratorio de pruebas; sin embargo, para un entorno de producción, recomendamos que obtenga el certificado de una entidad de certificación pública. 
	- Si usa un certificado que no es de confianza pública, asegúrese de que el certificado instalado en cada servidor del Proxy de aplicación web sea de confianza tanto en el servidor local como en todos los servidores de federación. 
- No se admiten certificados basados en claves CryptoAPI Next Generation (CNG) ni en proveedores de almacenamiento de claves. Esto significa que debe utilizar un certificado basado en un CSP (proveedor de servicios criptográficos) y no en un KSP (proveedor de almacenamiento de claves). 
- La identidad del certificado debe coincidir con el nombre del servicio de federación (por ejemplo, fs.contoso.com). 
	- La identidad es una extensión de nombre alternativo del firmante (SAN) de tipo dNSName, o bien, si no hay ninguna entrada de SAN, el nombre del firmante se especifica como un nombre común.  
	- Puede haber varias entradas de SAN en el certificado, siempre que una de ellas coincida con el nombre de servicio de federación. 
	- Si piensa usar la unión al área de trabajo, se requiere un SAN adicional con el valor "enterpriseregistration." seguido del sufijo de nombre principal de usuario (UPN) de su organización, por ejemplo, enterpriseregistration.contoso.com. 

Se admiten certificados comodín.
 

<!---HONumber=August15_HO6-->