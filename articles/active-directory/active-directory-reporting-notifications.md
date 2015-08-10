<properties
	pageTitle="Notificaciones de informes de Azure Active Directory"
	description="Uso de las notificaciones de informes de Azure Active Directory para inicios de sesión sospechosos"
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="TerryLan"
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/24/2015"
	ms.author="kenhoff"/>

# Notificaciones de informes de Azure Active Directory

## Qué informes generan notificaciones de correo electrónico

En este momento, solo el informe de inicio actividad de inicio de sesión erróneo y el informe de usuarios con actividad de inicio de sesión erróneo usan el sistema de notificación de correo electrónico.

## ¿Qué desencadena el envío de la notificación de correo electrónico?

De forma predeterminada, Azure Active Directory se establece para enviar automáticamente notificaciones de correo electrónico a todos los administradores globales. El correo electrónico se envía en las siguientes condiciones para cada informe.

En el caso del informe de actividad de inicio de sesión erróneo.

- Orígenes desconocidos: 10 eventos
- Varios errores: 10 eventos
- Direcciones IP con actividad sospechosa: 10 eventos
- Dispositivos infectados: 10 eventos

En el caso del informe de usuarios con actividad de inicio de sesión erróneo:

- Orígenes desconocidos: 10 eventos
- Varios errores: 10 eventos
- Direcciones IP con actividad sospechosa: 10 eventos
- Dispositivos infectados: 5 eventos
- Informe de inicios de sesión erróneos: 15 eventos

El correo electrónico se envía si se cumple alguna de las condiciones anteriores en un período de 30 días o desde que se envió el último correo electrónico si es menos de 30 días.

Los inicios de sesión erróneos son aquellos que nuestros algoritmos de aprendizaje automático han identificado como "erróneos" debido a ubicaciones de inicio de sesión inesperadas, la hora del día y las ubicaciones o una combinación de ambas cosas. Esto puede indicar que un hacker ha intentado iniciar sesión con esta cuenta. Puede encontrar más información sobre el informe en la tabla anterior.

## ¿Quién recibe las notificaciones de correo electrónico?

El correo electrónico se envía a todos los administradores globales a los que se ha asignado una licencia de Active Directory Premium. Para asegurarnos de que se entrega, también lo enviamos a la dirección de correo electrónico alternativa de los administradores. Los administradores deben incluir aad-alerts-noreply@mail.windowsazure.com en su lista de remitentes seguros para no perder el correo electrónico.

## ¿Con qué frecuencia se envían los correos electrónicos?

Una vez que se envía un correo electrónico, el siguiente se enviará solo cuando se detecten 10 o más eventos de inicio de sesión erróneo en el período de 30 días tras el envío de dicho correo electrónico. ¿Cómo puedo tener acceso al informe mencionado en el correo electrónico?

Al hacer clic en el vínculo, se le redirigirá a la página del informe en el Portal de administración de Azure. Para tener acceso al informe, deberá ser:

- Administrador o coadministrador de su suscripción de Azure
- Administrador global en el directorio y tener asignada una licencia de Active Directory Premium. Para obtener más información, consulte Ediciones de Azure Active Directory.

## ¿Puedo desactivar los correos electrónicos?

Sí, para desactivar las notificaciones relacionadas con inicios de sesión erróneos dentro del Portal de administración de Azure, haga clic en **Configurar**, y, a continuación, seleccione **Deshabilitadas** en la sección **Notificaciones**.

## Pasos siguientes
- ¿Tiene curiosidad sobre qué informes de actividad, auditoría y seguridad están disponibles? Consulte [Informes de actividad, auditoría y seguridad de Azure AD](active-directory-view-access-usage-reports.md).
- [Introducción a Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Incorporación de la marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md)

<!---HONumber=July15_HO5-->