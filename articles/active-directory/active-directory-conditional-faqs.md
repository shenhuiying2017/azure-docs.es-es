<properties
	pageTitle="Preguntas más frecuentes sobre acceso condicional | Microsoft Azure"
	description="Preguntas más frecuentes sobre el acceso condicional"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2016"
	ms.author="femila"/>

# Preguntas más frecuentes sobre acceso condicional

## ¿Qué aplicaciones funcionan con directivas de acceso condicional?
Consulte el tema [Conditional access support for applications](active-directory-conditional-access-supported-apps.md) (Compatibilidad con acceso condicional para las aplicaciones).

## ¿Se aplican directivas de acceso condicional a usuarios de colaboración B2B o invitados?
Se aplican directivas a usuarios de colaboración B2B. Sin embargo, en ocasiones, puede que el usuario no pueda satisfacer los requisitos de la directiva si, por ejemplo, su organización no admite la autenticación multifactor. Actualmente, no se aplica una directiva a usuarios invitados de SharePoint. La relación de invitado se mantiene en SharePoint, así que las cuentas de usuarios invitados no están sujetas a las directivas de acceso en el servidor de autenticación. El acceso de invitado se puede administrar en SharePoint.

## ¿La directiva de SharePoint Online se aplica también a OneDrive para la Empresa?
Sí.
 
## ¿Por qué no puede configurar directivas en aplicaciones cliente, como Word o Outlook?
La directiva de acceso condicional establece los requisitos de acceso a un servicio y se aplica cuando se realiza la autenticación en ese servicio. En este sentido, la directiva no se establece directamente en una aplicación cliente, sino que se aplica cuando se llama a un servicio. Por ejemplo, la directiva definida en SharePoint se aplicará a los clientes que llaman a SharePoint y la directiva establecida en Exchange se aplicará a Outlook.

<!---HONumber=AcomDC_0615_2016-->