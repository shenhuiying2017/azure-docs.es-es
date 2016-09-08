<properties
	pageTitle="Procedimientos recomendados de cambio de la configuración predeterminada de sincronización de Azure AD Connect | Microsoft Azure"
	description="Proporciona procedimientos recomendados para cambiar la configuración predeterminada de Azure AD Connect Sync."
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
	ms.date="08/22/2016"
	ms.author="markvi;andkjell"/>


# Azure AD Connect Sync: procedimientos recomendados de cambio de la configuración predeterminada
El propósito de este tema es describir los cambios admitidos y no admitidos en la sincronización de Azure AD Connect.

La configuración creada por Azure AD Connect funciona "tal cual" para la mayoría de entornos que sincronizan Active Directory local con Azure AD. Sin embargo, en algunos casos, es necesario aplicar algunos cambios a una configuración para satisfacer un requisito o una necesidad en particular.

## Cambios en la cuenta de servicio
Azure AD Connect Sync se está ejecutando con una cuenta de servicio creada por el asistente para la instalación. Esta cuenta de servicio contiene las claves de cifrado para la base de datos usada en la sincronización. Se crea con una contraseña de 127 caracteres, que se configura para que no caduque.

- **No se admite** cambiar o restablecer la contraseña de la cuenta de servicio. Si lo hace, se destruyen las claves de cifrado y el servicio no puede tener acceso a la base de datos ni iniciarse.

## Cambios realizados en el programador
A partir de las versiones de compilación 1.1 (febrero de 2016) puede configurar el [Programador](active-directory-aadconnectsync-feature-scheduler.md) para que tenga un ciclo de sincronización diferente al del valor predeterminado de 30 minutos.

## Cambios en las reglas de sincronización
El Asistente para la instalación proporciona una configuración que se supone que funciona en la mayoría de los casos. En caso de que tenga que realizar cambios en la configuración, debe seguir entonces estas reglas para disponer de una configuración admitida.

- Puede [cambiar los flujos de atributo](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) si los flujos de atributo directos predeterminados no son adecuados para su organización.
- Si no desea [pasar un atributo](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) ni quitar valores de atributo existentes en Azure AD, deberá crear una regla para este escenario.
- [Deshabilite una regla de sincronización no deseada](#disable-an-unwanted-sync-rule) en lugar de eliminarla. Una regla eliminada se vuelve a crear durante una actualización.
- Para [cambiar una regla lista para usar](#change-an-out-of-box-rule), debe realizar una copia de la regla original y deshabilitar la regla lista para usar. Aparece el Editor de reglas de sincronización para brindarle ayuda.
- Exporte las reglas de sincronización personalizadas mediante el editor de reglas de sincronización. El editor le ofrece un script de PowerShell que puede utilizar para volver a crearlas fácilmente en un escenario de recuperación ante desastres.

>[AZURE.WARNING] Las reglas de sincronización listas para usar tienen una huella digital. Si realiza un cambio en estas reglas, la huella digital ya no coincidirá. Es posible que tenga problemas en el futuro cuando intente aplicar una nueva versión de Azure AD Connect. Realiza únicamente cambios cómo se describe en este artículo.

### Deshabilitar una regla de sincronización no deseada
No elimine una regla de sincronización lista para usar. Esta se vuelve a crear durante la siguiente actualización.

En algunos casos, el asistente de instalación ha generado una configuración que no funciona en su topología. Por ejemplo, si tiene una topología de bosque de cuentas-recursos pero ha ampliado el esquema en el bosque de cuentas con el esquema de Exchange, entonces se crean reglas para Exchange tanto para el bosque de cuentas como para el bosque de recursos. En este caso, tenemos que deshabilitar la regla de sincronización para Exchange.

![Regla de sincronización deshabilitada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

En la imagen anterior, el Asistente para la instalación ha encontrado un esquema antiguo de Exchange 2003 en el bosque de cuentas. Este esquema de extensión se agregó antes de que el bosque de recursos se introdujera en el entorno de Fabrikam. Para asegurarse de que ningún atributo de la implementación antigua de Exchange se sincronice, la regla de sincronización se debe deshabilitar como se muestra.

### Cambiar una regla lista para usar
Si tiene que realizar cambios en una regla lista para usar, debe realizar una copia de esta y deshabilitar la regla original. A continuación, realice los cambios en la regla clonada. El Editor de reglas de sincronización le ayuda con esos pasos. Cuando se abre una regla lista para usar, se presenta este cuadro de diálogo: ![Regla lista para usar de advertencia](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Seleccione **Sí** para crear una copia de la regla. A continuación, se abre la regla clonada. ![Regla clonada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

En esta regla clonada, realice los cambios necesarios en el ámbito, la combinación y las transformaciones.

## Pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0824_2016-->