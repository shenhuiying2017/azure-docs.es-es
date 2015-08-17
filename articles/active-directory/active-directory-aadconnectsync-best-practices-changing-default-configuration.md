<properties
	pageTitle="Prácticas recomendadas de cambio de la configuración predeterminada"
	description="Proporciona las prácticas recomendadas de cambio de la configuración predeterminada de Sincronización de Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Sincronización de Azure AD Connect: prácticas recomendadas de cambio de la configuración predeterminada

La configuración creada por Azure AD Connect funciona “como está” para la mayoría de entornos que sincronizan Active Directory local con Azure AD.<br> Sin embargo, en algunos casos, es necesario aplicar algunos cambios a una configuración para satisfacer un requisito o una necesidad en particular.

Aunque es posible aplicar cambios en la configuración de Azure AD, debe aplicarlos con cuidado porque se supone que Azure AD debe estar lo más próximo posible de un dispositivo.

La siguiente es una lista de comportamientos esperados:

- Después de actualizar Azure AD Connect a una versión más reciente, la mayoría de las opciones de configuración se restablecerán a sus valores predeterminados.
- Después de haber aplicado una actualización, se perderán los cambios en las reglas de sincronización listas para su aplicación.
- Las reglas de sincronización listas para su aplicación se vuelven a crear durante la actualización a una versión más reciente.
- Las reglas de sincronización personalizadas que haya creado permanecen sin modificar cuando se ha aplicado una actualización a una versión más reciente.



Si necesita cambiar la configuración predeterminada, haga lo siguiente:

- Si necesita modificar un flujo de atributos de una regla de sincronización lista para su aplicación, no lo cambie directamente. En su lugar, cree una nueva regla de sincronización con una prioridad más alta (valor numérico más bajo) que contenga el flujo de atributos necesario.
- Exporte las reglas de sincronización personalizadas mediante el editor de reglas de sincronización. De este modo, obtiene un script de PowerShell que puede utilizar fácilmente para volver a crearlas en un posible escenario de recuperación ante desastres.
- Si necesita cambiar el ámbito o la configuración de unión de una regla de sincronización lista para su aplicación, documéntelo y vuelva a aplicar el cambio tras actualizar a una versión más reciente de Sincronización de Azure AD.



**Otras notas importantes:**

- Si tiene configurada la sincronización de contraseña y el filtrado basado en atributos, asegúrese de que solo los objetos sincronizados con Azure AD estén en el ámbito de la sincronización de contraseña. 





## Recursos adicionales

* [Sincronización de Azure AD Connect: personalización de las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=August15_HO6-->