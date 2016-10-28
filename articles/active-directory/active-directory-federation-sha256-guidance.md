<properties
	pageTitle="Cambio del algoritmo hash de firma para relación de confianza para usuario autenticado de O365 | Microsoft Azure"
	description="En esta página se proporcionan instrucciones para cambiar el algoritmo SHA para la confianza de federación con Office 365."
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

# Cambio del algoritmo hash de firma para relación de confianza para usuario autenticado de Office 365

## Información general

Azure Active Directory Federation Services (ADFS) firma sus tokens en Microsoft Azure Active Directory para asegurarse de que no pueden ser modificados. Esta firma se puede basar en SHA1 o SHA256. Azure Active Directory ahora es compatible con tokens firmados con un algoritmo SHA256 y recomendamos establecer el algoritmo con firma de tokens en SHA256 para proporcionar el máximo nivel de seguridad. En este artículo se describen los pasos necesarios para establecer el algoritmo de firma de tokens para el nivel SHA256 más seguro.

## Cambio del algoritmo de firma de tokens

Después de haber establecido el algoritmo de firma con uno de los dos procesos siguientes, AD FS firma los tokens para la relación de confianza para usuario autenticado de Office 365 con SHA-256. No es necesario realizar ningún cambio de configuración adicional y este cambio no tendrá efecto en su capacidad de acceder a Office 365 o a otras aplicaciones Azure AD.

### Consola de administración de AD FS

1. Abra la consola de administración de AD FS en el servidor de AD FS principal.
2. Expanda el nodo de AD FS y haga clic en el nodo de **relaciones de confianza para usuario autenticado**.
3. Haga clic con el botón derecho en la relación de confianza para usuario autenticado de Office 365 o Azure y seleccione **Propiedades**.
4. Seleccione la pestaña **Opciones avanzadas** y el algoritmo hash seguro SHA256.
5. Haga clic en **Aceptar**.

![Algoritmo de firma SHA256 - MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### Cmdlets de PowerShell de AD FS

1. En cualquier servidor de AD FS, abra PowerShell con privilegios de administrador.
2. Establezca el algoritmo hash seguro mediante el cmdlet **Set-AdfsRelyingPartyTrust**.

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## Consulte también

* [Reparación de la confianza de Office 365 con Azure AD Connect](./active-directory-aadconnect-federation-management.md#repairing-the-trust)

<!---HONumber=AcomDC_0817_2016-->