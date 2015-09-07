<properties
	pageTitle="Procedimientos recomendados para cambiar la configuración predeterminada | Microsoft Azure"
	description="Proporciona procedimientos recomendados para cambiar la configuración predeterminada de Azure AD Connect Sync."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect Sync: procedimientos recomendados de cambio de la configuración predeterminada

El propósito de este tema es describir los cambios admitidos y no admitidos en Azure AD Connect Sync.

La configuración creada por Azure AD Connect funciona "como está" para la mayoría de entornos que sincronizan Active Directory local con Azure AD. Sin embargo, en algunos casos, es necesario aplicar algunos cambios a una configuración para satisfacer un requisito o una necesidad en particular.

## Cambios en la cuenta de servicio
Azure AD Connect Sync se está ejecutando con una cuenta de servicio creada por el asistente para la instalación. Esta cuenta de servicio contiene las claves de cifrado para la base de datos usada en la sincronización. Se crea con una contraseña de 127 caracteres, que se configura para que no caduque.

- **No se admite** cambiar o restablecer la contraseña de la cuenta de servicio. Si lo hace, se destruirán las claves de cifrado y el servicio no podrá tener acceso a la base de datos ni iniciarse.

## Cambios realizados en el programador
Azure AD Connect Sync está configurado para sincronizar los datos de identidad cada tres horas. Durante la instalación se crea una tarea programada que se ejecuta con una cuenta de servicio con permisos para usar el servidor de sincronización.

- **No se admite** realizar cambios en la tarea programada. No se conoce la contraseña de la cuenta de servicio. Consulte [Cambios en la cuenta de servicio](#changes-to-the-service-account)
- **No se admite** realizar sincronizaciones con una frecuencia superior al valor predeterminado de tres horas.

## Cambios en las reglas de sincronización

Aunque es posible aplicar cambios a la configuración de Azure AD Connect Sync, debe aplicarlos con cuidado porque se supone que Azure AD Connect Sync está tan cerca como es posible de un dispositivo.

La siguiente es una lista de comportamientos esperados:

- Después de actualizar Azure AD Connect a una versión más reciente, la mayoría de las opciones de configuración se restablecerán a sus valores predeterminados.
- Después de haber aplicado una actualización, se perderán los cambios en las reglas de sincronización listas para su aplicación.
- Las reglas de sincronización listas para su aplicación se vuelven a crear durante la actualización a una versión más reciente.
- Las reglas de sincronización personalizadas que haya creado permanecen sin modificar cuando se ha aplicado una actualización a una versión más reciente.



Si necesita cambiar la configuración predeterminada, haga lo siguiente:

- Si necesita modificar un flujo de atributos de una regla de sincronización lista para su aplicación, no lo cambie directamente. En su lugar, cree una nueva regla de sincronización con una prioridad más alta (valor numérico más bajo) que contenga el flujo de atributos necesario.
- Exporte las reglas de sincronización personalizadas mediante el editor de reglas de sincronización. De este modo, obtiene un script de PowerShell que puede utilizar fácilmente para volver a crearlas en un posible escenario de recuperación ante desastres.
- Si necesita cambiar el ámbito o la configuración de unión de una regla de sincronización lista para su aplicación, documéntelo y vuelva a aplicar el cambio tras actualizar a una versión más reciente de Sincronización de Azure AD.



## Recursos adicionales

* [Azure AD Connect Sync: personalización de las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->

<!---HONumber=August15_HO9-->