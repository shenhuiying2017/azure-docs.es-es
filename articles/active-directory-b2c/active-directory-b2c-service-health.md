<properties
	pageTitle="Versión preliminar de Azure Active Directory B2C: mantenimiento del servicio | Microsoft Azure"
	description="Notificaciones sobre problemas, estado y mitigaciones menores de Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="swkrish"/>

# Versión preliminar de Azure Active Directory B2C: mantenimiento del servicio

En esta página se proporcionan notificaciones sobre problemas de servicio, estado y mitigaciones menores. Puede marcar esta página para consultarla en el futuro. Continúe también con la supervisión del [panel de estado de Azure](https://azure.microsoft.com/status/).

### 22/3/2016: error de SSO en inquilinos de B2C

Se incorporó un error de SSO (inicio de sesión único) a la 1 p. m. (hora estándar del Pacífico) el 17/3/2016 y se mitigó a las 10 a. m. (hora estándar del Pacífico) el 18/3/2016. Durante este período, se vieron afectados menos de 100 usuarios. Seguimos supervisando este asunto de cerca. Condiciones para que el usuario haya experimentado este error:

1. Ha configurado una directiva de inicio de sesión con "cuentas locales" como único proveedor de identidades.
2. Un usuario inicia sesión en Azure AD B2C correctamente la primera vez.
3. El usuario intenta volver a iniciar sesión pero, en lugar de obtener el SSO, se muestra un mensaje de error.

La única solución para el usuario (después del paso 3) era cerrar y volver a abrir el explorador y autenticarse de nuevo.

<!---HONumber=AcomDC_0323_2016-->