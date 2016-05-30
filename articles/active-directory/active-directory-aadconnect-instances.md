<properties
	pageTitle="Azure AD Connect: Instancias de servicio de sincronización | Microsoft Azure"
	description="Esta página documenta las consideraciones especiales para instancias de Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/10/2016"
	ms.author="andkjell"/>

# Azure AD Connect: consideraciones especiales para instancias
Azure AD Connect se utiliza habitualmente con la instancia mundial de Azure AD y Office 365. Pero también hay otras instancias y estas tienen requisitos diferentes para las direcciones URL y otras consideraciones especiales.

## Microsoft Cloud Germany
[Microsoft Cloud Germany](http://www.microsoft.de/cloud-deutschland) es una nube soberana operada por un administrador de datos alemán.

Esta nube se encuentra actualmente en versión preliminar. Muchos de los escenarios que normalmente puede hacer por usted mismo, como comprobar los dominios, tiene que realizarlos el operador. Póngase en contacto con su representante local de Microsoft para más información acerca de cómo participar en la versión preliminar.

Direcciones URL para abrir en el servidor proxy |
--- |
*. microsoftonline.de |
*.windows.net |
\+Listas de revocación de certificados |

Al iniciar sesión en el directorio de Azure AD tiene que usar una cuenta en el dominio onmicrosoft.de.

Características que actualmente no están presentes en Microsoft Cloud Germany:

- Azure AD Connect Health no está disponible.
- Las actualizaciones automáticas no están disponibles.
- El servicio de escritura diferida de contraseñas no está disponible.

## Microsoft Azure Government Cloud
[Microsoft Azure Government Cloud](https://azure.microsoft.com/features/gov/) es una nube para el gobierno de Estados Unidos.

Esta nube ha sido compatible con versiones anteriores de DirSync. A partir de la compilación 1.1.180 de Azure AD Connect la próxima generación de la nube es compatible. Esta generación utiliza solo puntos de conexión basados en EE. UU. y tiene una lista de direcciones URL diferentes para abrir en el servidor proxy.

Direcciones URL para abrir en el servidor proxy |
--- |
*.microsoftonline.com |
*. gov.us.microsoftonline.com |
\+Listas de revocación de certificados |

Azure AD Connect no podrá detectar automáticamente que el directorio de Azure AD se encuentra en esta nube. En su lugar, tiene realizar las siguientes acciones al instalar Azure AD Connect.

1. Inicie la instalación de Azure AD Connect.
2. Tan pronto como vea la primera página en donde se supone que tiene que aceptar el CLUF, no continúe y deje que el Asistente para la instalación se continúe ejecutando.
3. Inicie regedit y cambie la clave del registro `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` al valor `2`.
4. Vuelva al Asistente para la instalación de Azure AD Connect, acepte el CLUF y continúe. Durante la instalación, asegúrese de usar la ruta de acceso de instalación de **configuración personalizada** (y no la instalación rápida). Luego continúe la instalación normalmente.

Características que actualmente no están presentes en Microsoft Azure Government Cloud:

- Azure AD Connect Health no está disponible.
- Las actualizaciones automáticas no están disponibles.
- El servicio de escritura diferida de contraseñas no está disponible.

## Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0518_2016-->