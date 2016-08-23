<properties
	pageTitle="Cambiar el algoritmo hash de firma para relación de confianza para usuario autenticado de O365 | Microsoft Azure"
	description="En esta página se proporcionan instrucciones para cambiar el algoritmo SHA para la confianza de federación con O365"
    keywords="SHA1,SHA256,O365,federación,aadconnect,adfs,ad fs,cambiar sha,confianza de federación,relación de confianza para usuario autenticado"
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="samueld"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="anandy"/>

#Cambiar el algoritmo hash de firma para relación de confianza para usuario autenticado de O365

##Información general

AD FS firma tokens en Azure Active Directory para asegurarse de que no se pueden alterar. Esta firma se puede basar en SHA1 o SHA256. Microsoft Azure Active Directory ahora es compatible con tokens firmados con un algoritmo SHA256 y recomienda establecer el algoritmo con firma de token en SHA256 para proporcionar el máximo nivel de seguridad. En este artículo se proporciona el pasos para establecer el algoritmo de firma de token para el nivel SHA256 más seguro.

##Cambiar el algoritmo de firma de token

Una vez que haya establecido el algoritmo de firma como se indica en los pasos siguientes, AD FS comenzará a firmar los tokens para relación de confianza para usuario autenticado de O365 con SHA-256. No hay ningún cambio de configuración adicional necesario y realizar este cambio no afecta a los usuarios finales que acceden a Office 365 u otras aplicaciones de Azure AD.

###Usar la consola de administración

* Abra la consola de administración de AD FS en el servidor de AD FS principal.
* Expanda el nodo de AD FS y haga clic en el nodo de relaciones de confianza para usuario autenticado.
* Haga clic con el botón derecho en la relación de confianza para usuario autenticado de O365 o Azure y seleccione Propiedades.
* Seleccione la ficha Opciones avanzadas y el algoritmo hash seguro como SHA256
* Haga clic en Aceptar.

![Algoritmo de firma SHA256 - MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

###Usar cmdlets de PowerShell de AD FS

* En cualquier servidor de AD FS, abra PowerShell con privilegios de administrador
* Establecer el algoritmo hash seguro mediante el cmdlet Set-AdfsRelyingPartyTrust

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

##Leer también

* [Repair O365 trust with AAD Connect](./active-directory-aadconnect-federation-management.md#repairing-the-trust) (Reparar relación de confianza de Office 365 con AAD Connect)

<!---HONumber=AcomDC_0810_2016-->