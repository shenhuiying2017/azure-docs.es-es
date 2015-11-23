<properties
	pageTitle="Procedimientos recomendados para cambiar la configuración predeterminada | Microsoft Azure"
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
	ms.date="11/11/2015"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect Sync: procedimientos recomendados de cambio de la configuración predeterminada

El propósito de este tema es describir los cambios admitidos y no admitidos en Azure AD Connect Sync.

La configuración creada por Azure AD Connect funciona "como está" para la mayoría de entornos que sincronizan Active Directory local con Azure AD. Sin embargo, en algunos casos, es necesario aplicar algunos cambios a una configuración para satisfacer un requisito o una necesidad en particular.

## Cambios en la cuenta de servicio
Azure AD Connect Sync se está ejecutando con una cuenta de servicio creada por el asistente para la instalación. Esta cuenta de servicio contiene las claves de cifrado para la base de datos usada en la sincronización. Se crea con una contraseña de 127 caracteres, que se configura para que no caduque.

- **No se admite** cambiar o restablecer la contraseña de la cuenta de servicio. Si lo hace, se destruirán las claves de cifrado y el servicio no podrá tener acceso a la base de datos ni se podrá iniciar.

## Cambios realizados en el programador
Azure AD Connect Sync está configurado para sincronizar los datos de identidad cada tres horas. Durante la instalación se crea una tarea programada que se ejecuta con una cuenta de servicio con permisos para usar el servidor de sincronización.

- **No se admite** realizar cambios en la tarea programada. No se conoce la contraseña de la cuenta de servicio. Consulte [Cambios en la cuenta de servicio](#changes-to-the-service-account)
- **No se admite** realizar sincronizaciones con una frecuencia superior al valor predeterminado de tres horas.

## Cambios en las reglas de sincronización

El Asistente para la instalación proporciona una configuración que se supone que funciona en la mayoría de los casos. En caso de que tenga que realizar cambios en la configuración, debe seguir entonces estas reglas para disponer de una configuración admitida.

- El único cambio admitido en una regla de sincronización lista para usar es deshabilitarla. Los demás cambios podrían perderse en una actualización.
- Si tiene que realizar algún otro cambio en una regla lista para usar, realice una copia de ella y deshabilite la regla original. El Editor de reglas de sincronización le ayudará con este paso.
- Exporte las reglas de sincronización personalizadas mediante el editor de reglas de sincronización. De este modo, obtiene un script de PowerShell que puede utilizar fácilmente para volver a crearlas en un posible escenario de recuperación ante desastres.

>[AZURE.WARNING]Las reglas de sincronización listas para usar tienen una huella digital. Si realiza un cambio en estas reglas, la huella digital ya no coincidirá y puede que tenga problemas en el futuro cuando intente aplicar una nueva versión de Azure AD Connect. Realiza únicamente cambios cómo se describe en este artículo.

### Eliminación de una regla de sincronización no deseada
No elimine una regla de sincronización lista para usar; se volverá crear durante la siguiente actualización.

En algunos casos, el asistente de instalación ha generado una configuración que no funciona en su topología. Por ejemplo, si tiene una topología de bosque de cuentas-recursos pero ha ampliado el esquema en el bosque de cuentas con el esquema de Exchange, entonces se crearán reglas para Exchange tanto para el bosque de cuentas como para el bosque de recursos. En este caso, tenemos que deshabilitar la regla de sincronización para Exchange.

![Regla de sincronización deshabilitada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

En la imagen anterior, el Asistente para la instalación ha encontrado un esquema antiguo de Exchange 2003 en el bosque de cuentas. Este se agregó antes de que el bosque de recursos se introdujera en el entorno de Fabrikam. Para asegurarse de que ningún atributo de la implementación antigua de Exchange se sincronice, la regla de sincronización se debe deshabilitar como se muestra.

### Cambio de reglas listas para usar
Si tiene que realizar cambios en una regla lista para usar, debe realizar una copia de ella y deshabilitar la regla original. A continuación, realice los cambios en la regla clonada. El Editor de reglas de sincronización le ayudará con este paso. Cuando se abre una regla lista para usar se presenta este cuadro de diálogo:

![Regla lista para usar de advertencia](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Seleccione **Sí** para crear una copia de la regla. A continuación, se abre la regla clonada.

![Regla clonada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

En esta regla clonada, realice los cambios necesarios en el ámbito, la combinación y las transformaciones.

### No pasar atributos
Existen dos maneras de pasar un atributo. El primero está disponible en el Asistente para la instalación y le permite [quitar atributos seleccionados](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Esta opción funciona si nunca ha sincronizado el atributo antes. Sin embargo si ha comenzado a sincronizar este atributo y después lo quita con esta característica, el motor de sincronización dejará de administrar el atributo y los valores existentes se quedarán en Azure AD.

Si quiere quitar el valor de un atributo y asegurarse de no pasarlo en el futuro, debe crear en su lugar una regla personalizada.

En Fabrikam no hemos dado cuenta de que algunos de los atributos sincronizamos con la nube no deberían estar allí. Queremos asegurarse de que estos atributos se quitan de Azure AD.

![Atributos de extensión](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/badextensionattribute.png)

- Cree una nueva regla de sincronización de entrada y rellene la descripción ![Descripciones](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruledescription.png)
- Cree flujos de atributos de tipo **Expression** y con el origen **AuthoritativeNull**. El literal **AuthoritativeNull** indica que el valor debe estar vacío en la máquina virtual incluso si una regla de sincronización de prioridad inferior intenta rellenar el valor. ![Atributos de extensión](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruletransformations.png)
- Guarde la regla de sincronización. Inicie el **Servicio de sincronización**, busque el conector y seleccione **Ejecutar** y **Sincronización completa**. Esta acción hará que se vuelvan a calcular todos los flujos de atributos.
- Compruebe que los cambios deseados están a punto de exportarse; para ello, busque en el espacio de conector. ![Eliminación por fases](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/deletetobeexported.png)

## Pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=Nov15_HO3-->