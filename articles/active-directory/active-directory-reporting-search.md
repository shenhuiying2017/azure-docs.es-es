<properties
	pageTitle="Búsqueda de informes de Azure Active Directory"
	description="Búsqueda de informes de auditoría, actividad y seguridad de Azure Active Directory"
	services="active-directory"
	documentationCenter=""
	authors="dhanyahk"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="03/07/2016"
	ms.author="dhanyahk"/>

# Búsqueda de informes de Azure Active Directory

*Esta documentación forma parte de la [guía de informes de Azure Active Directory](active-directory-reporting-guide.md).*

Azure Active Directory (Azure AD) proporciona a los administradores de directorio la capacidad de buscar eventos de auditoría, actividad y seguridad del usuario entre varios informes.

Para encontrar el panel de búsqueda, vaya al **Portal de Azure clásico -> Su directorio de Azure AD -> Informes**. El panel puede encontrarse en la parte superior de la lista de informes.

Para buscar eventos de actividad o de auditoría para un determinado usuario, seleccione un intervalo de fechas en los campos De y Para, escriba el nombre para mostrar o el UPN del usuario y seleccione el botón de marca de verificación (Aceptar).

## Informes incluidos en la búsqueda

No todos los informes se incluyen en los resultados de la búsqueda aún. Esta tabla indica qué informes se incluyen.

Informe | Se incluye
--------------------------------------------------- | --------
Inicios de sesión desde orígenes desconocidos | No
Inicios de sesión tras varios errores | No
Inicios de sesión desde varias ubicaciones geográficas | No
Inicios de sesión desde direcciones IP con actividad sospechosa | No
Inicios de sesión desde dispositivos posiblemente infectados | No
Actividad de inicio de sesión irregular | No
Usuarios con actividad de inicio de sesión anómala | No
Usuarios con credenciales perdidas | No
Informe de auditoría | Sí
Actividad de restablecimiento de contraseña | Sí
Actividad de registro de restablecimiento de contraseñas | Sí
Actividad de los grupos de autoservicio | Sí
Uso de la aplicación | No
Actividad de aprovisionamiento de cuentas | Sí
Estado de la sustitución de contraseña | No
Errores de aprovisionamiento de cuentas | No
Uso de RMS | No
Usuarios de RMS más activos | No
Uso de dispositivos RMS | No


## Más información

 - [Informes de Azure Active Directory](active-directory-view-access-usage-reports.md)
 - [Eventos de auditoría de informes de Azure Active Directory](active-directory-reporting-audit-events.md)

<!---HONumber=AcomDC_0309_2016-->