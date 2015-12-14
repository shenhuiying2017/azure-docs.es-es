<properties
	pageTitle="Directivas de retención de informes de Azure Active Directory | Microsoft Azure"
	description="Directivas de retención de datos de informes en Azure Active Directory"
	services="active-directory"
	documentationCenter=""
	authors="kenhoff"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="11/30/2015"
	ms.author="kenhoff"/>

# Directivas de retención de informes de Azure Active Directory

## Artículos de documentación de informes

 - [API de Informes](active-directory-reporting-api-getting-started.md)
 - [Eventos de auditoría](active-directory-reporting-audit-events.md)
 - [Retención](active-directory-reporting-retention.md)
 - [Vistas previas](active-directory-reporting-previews.md)
 - [Search](active-directory-reporting-search.md)
 - [Reposición](active-directory-reporting-backfill.md)
 - [Latencias](active-directory-reporting-latencies.md)
 - [Evento "Actor desconocido"](active-directory-reporting-unknown-actor.md)

Los informes de Azure AD conservan los datos durante un número determinado de días, que indicamos a continuación.

Informe | Descripción
------------------------------------------------------- | -----------
Inicios de sesión desde orígenes desconocidos | 30 días
Inicios de sesión tras varios errores | 30 días
Inicios de sesión desde varias ubicaciones geográficas | 30 días
Inicios de sesión desde direcciones IP con actividad sospechosa | 30 días
Inicios de sesión desde dispositivos posiblemente infectados | 30 días
Actividad de inicio de sesión irregular | 30 días
Usuarios con actividad de inicio de sesión erróneo. | 30 días
Usuarios con credenciales perdidas | 30 días
Informe de auditoría | 180 días
Actividad de restablecimiento de contraseña (Azure AD) | 30 días
Actividad de restablecimiento de contraseña (Identity Manager) | 30 días
Actividad de registro de restablecimiento de contraseña (Azure AD) | 30 días
Actividad de registro de restablecimiento de contraseña (Identity Manager) | 30 días
Actividad de los grupos de autoservicio (Azure AD) | 30 días
Actividad de los grupos de autoservicio (Identity Manager) | 30 días
Uso de la aplicación | 30 días
Actividad de aprovisionamiento de cuentas | 30 días
Estado de la sustitución de contraseña | 30 días
Errores de aprovisionamiento de cuentas | 30 días
Uso de RMS | 30 días
Usuarios de RMS más activos | 30 días
Uso de dispositivos RMS | 30 días
Uso de aplicaciones habilitadas para RMS | 30 días

<!---HONumber=AcomDC_1203_2015-->