<properties
	pageTitle="Informe de uso sin licencia | Microsoft Azure"
	description="El informe de uso sin licencia ayuda a identificar usuarios sin licencia que utilizan características de Azure AD de pago,"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="markvi"/>

# Informe de uso sin licencia

El informe de uso sin licencia ayuda a identificar usuarios sin licencia que utilizan características de Azure AD de pago, lo que permite hacer un mejor uso de las licencias que se han adquirido e identificar en qué momento se necesitan más licencias.

El informe muestra el uso activo de las características de pago en los últimos 30 días.

## Estructura del informe
 
| Nombre de la columna |	Descripción |
| :--                  | :--         |
| Usuario sin licencia |	Nombre del usuario |
| Característica | El nombre de la característica. Por ejemplo: acceso condicional |
| Aplicación a la que se accede | El nombre de la aplicación a la que se accede con la característica. Por ejemplo: Office 365 SharePoint Online |

 
> [AZURE.NOTE] Si se ha eliminado una cuenta de usuario, la columna "Usuario sin licencia" se rellenará con un identificador, como 1003000090D8B285


## Características de acceso condicional

A los usuarios sin licencia se les marcará cuando accedan a un servicio al que se haya aplicado la directiva de acceso condicional, siempre que no tengan una licencia de Azure AD Premium.

Esto se aplica a las directivas de MFA/ubicación, así como a las directivas de dispositivos que usan Intune.
 

## Consulte también

- [Protección del acceso a Office 365 y otras aplicaciones conectadas a Azure Active Directory](active-directory-conditional-access.md)
- [Introducción al acceso condicional a Azure AD](active-directory-conditional-access-azuread-connected-apps.md)

<!---HONumber=AcomDC_0727_2016-->