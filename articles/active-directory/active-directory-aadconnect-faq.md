<properties
	pageTitle="Preguntas frecuentes de Azure AD Connect | Microsoft Azure"
	description="Esta página contiene las preguntas más frecuentes sobre Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/10/2015"
	ms.author="billmath"/>

# Preguntas más frecuentes sobre Azure Active Directory Connect

## Instalación general
**P: ¿Funcionará la instalación si el administrador global de Azure AD tiene 2FA habilitado?**

La instalación no funcionará en este caso. El administrador global que desee instalar Azure AD Connect no debe tener MFA habilitado. Somos conscientes de esta limitación, que se solucionará en el futuro.

**P: ¿Existe alguna manera de instalar Azure AD Connect de manera desatendida?**

Solo se admite la instalación de Azure AD Connect mediante el Asistente para instalación. No se admite la instalación silenciosa y desatendida.

## Instalación rápida

## Instalación personalizada

## Red
**P: Tengo un firewall, dispositivo de red o algo más que limita las conexiones de tiempo máximo que pueden permanecer abiertas en mi red. ¿Cuál debería ser mi umbral de tiempo de espera del lado de cliente al usar Azure AD Connect?**

Todo el software de redes, dispositivos físicos o cualquier otra cosa que limite el tiempo máximo en que las conexiones permanecen abiertas deben usar un umbral de un mínimo de 5 minutos (300 segundos) para conectividad entre el servidor donde está instalado el cliente de Azure AD Connect y Azure Active Directory. Esto también se aplica a todas las herramientas de sincronización de Microsoft Identity publicadas anteriormente.

**P: ¿Qué hago si recibo un correo electrónico en el que se me solicita que renueve el certificado de Office 365?**

Utilice las instrucciones que se describen en el artículo para resolver [aquí](active-directory-aadconnect-o365-certs.md) la renovación del certificado.

## Solución de problemas

**P: ¿Cómo puedo obtener ayuda con Azure AD Connect?**

[Buscar en Microsoft Knowledge Base (KB)](https://www.microsoft.com/es-ES/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Busque en Microsoft Knowledge Base (KB) soluciones técnicas a los problemas de break-fix más comunes sobre soporte técnico de Azure AD Connect.

[Foros de Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/es-ES/home?forum=WindowsAzureAD)

- Puede buscar y examinar preguntas técnicas y sus respuestas en la comunidad o realizar su propia pregunta haciendo clic [aquí](https://social.msdn.microsoft.com/Forums/azure/es-ES/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).


[Asistencia al cliente de Azure AD Connect](https://manage.windowsazure.com/?getsupport=true)

- Use este vínculo para obtener soporte técnico mediante el Portal de Azure.

<!---HONumber=Nov15_HO3-->