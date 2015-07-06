<properties 
	pageTitle="Administración de Azure AD Connect" 
	description="Aprenda a ampliar la configuración predeterminada y las tareas operativas de Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Administración de Azure AD Connect 


<center><div class="dev-center-tutorial-selector sublanding">
<a href="/es-es/documentation/articles/active-directory-aadconnect/" title="¿Qué es?" class="current">¿Qué es?</a>
<a href="/es-es/documentation/articles/active-directory-aadconnect-how-it-works/" title="Cómo funciona">Cómo funciona</a>
<a href="/es-es/documentation/articles/active-directory-aadconnect-get-started/" title="Introducción">Introducción</a>
<a href="/es-es/documentation/articles/active-directory-aadconnect-get-manage/" title="Administrar">Administrar</a></div></center>


Los temas tratan cuestiones operativas avanzadas que le permiten personalizar Azure Active Directory Connect para satisfacer las necesidades y los requisitos de sus organizaciones.  

## Cambio de la configuración predeterminada
La configuración predeterminada de Azure AD Connect en la mayoría de casos es suficiente para ampliar fácilmente los directorios locales llevándolos a la nube. Sin embargo, hay ciertas instancias en las que necesita modificar el valor predeterminado y adaptarlo a su lógica de negocios de sus organizaciones. En estos casos, puede modificar la configuración predeterminada; sin embargo, hay algunos aspectos que debe tener en cuenta antes de hacerlo.

Si va a actualizar Sincronización de Azure AD o DirSync o va a moverse desde estas aplicaciones, tenga en cuenta lo siguiente:

- Después de actualizar Sincronización de Azure AD a una versión más reciente, la mayoría de las opciones de configuración se restablecerán a sus valores predeterminados.
- Después de haber aplicado una actualización, se perderán los cambios en las reglas de sincronización listas para su aplicación.
- Las reglas de sincronización listas para su aplicación se vuelven a crear durante la actualización a una versión más reciente.
- Las reglas de sincronización personalizadas que haya creado permanecen sin modificar cuando se ha aplicado una actualización a una versión más reciente.

Si necesita cambiar la configuración predeterminada, haga lo siguiente:

- Si necesita modificar un flujo de atributos de una regla de sincronización lista para su aplicación, no lo cambie directamente. En su lugar, cree una nueva regla de sincronización con una prioridad más alta (valor numérico más bajo) que contenga el flujo de atributos necesario.
- Exporte las reglas de sincronización personalizadas mediante el editor de reglas de sincronización. De este modo, obtiene un script de PowerShell que puede utilizar fácilmente para volver a crearlas en un posible escenario de recuperación ante desastres.
- Si necesita cambiar el ámbito o la configuración de unión de una regla de sincronización lista para su aplicación, documéntelo y vuelva a aplicar el cambio tras actualizar a una versión más reciente de Azure AD Connect.






 

## Uso del editor de reglas de sincronización

En Azure AD Connect, puede configurar y ajustar el flujo de objetos y atributos entre Azure AD y sus directorios locales mediante la configuración de reglas de sincronización. Las reglas de sincronización pueden configurarse mediante el editor de reglas de sincronización. El editor de reglas de sincronización se instala junto con Azure AD Connect.  Para poder usar el editor debe ser miembro del grupo ADSyncAdmins o del grupo de administradores que especificó durante la instalación de Azure AD Connect. 

En la siguiente captura de pantalla verá todas las reglas de sincronización creadas para su configuración al instalar Azure AD Connect con la instalación rápida. Cada línea de la tabla es una regla de sincronización. A la izquierda, en Tipos de regla, se muestran los dos tipos diferentes: entrantes y salientes. La perspectiva entrante y saliente corresponde al punto de vista del metaverso. Es decir, aportamos información desde nuestros directorios en el metaverso. La perspectiva de salida hace referencia a las reglas por las que se envía información y atributos en dirección a nuestros directorios, como Active Directory local o Azure AD. 

<center>![Editor de reglas de sincronización](./media/active-directory-aadconnect-manage/Synch_Rule.png)
</center>

Para crear una nueva regla, tendría que seleccionar Agregar nueva regla y, a continuación, configurar la regla. Por ejemplo, supongamos que deseamos crear una regla de unión por la que cualquier usuario de nuestro directorio local se uniría al objeto de metaverso que tiene el mismo número de teléfono. Para ello, cree la nueva regla y especifique el sistema conectado (en nuestro caso contoso.com), el tipo de objeto del sistema conectado, el usuario, el tipo de objeto de metaverso, la persona y el tipo de vínculo de la unión.

<center>![Crear regla de sincronización](./media/active-directory-aadconnect-manage/synch2.png)
</center>


A continuación, en la pantalla de reglas de unión, especifique telephoneNumber bajo el atributo Source y telephoneNumber bajo el atributo Target.  Y eso es todo.  Ya ha creado correctamente una regla de unión.

<center>![Regla de unión](./media/active-directory-aadconnect-manage/synch3.png)
</center>

Puede utilizar el Editor de reglas de sincronización para aplicar la lógica de negocios adicional fuera de la configuración predeterminada y adaptarla a las necesidades de sus organizaciones. Para obtener información adicional sobre el editor de reglas de sincronización, consulte [Descripción de la configuración predeterminada](https://msdn.microsoft.com/library/azure/dn800963.aspx).


## Uso del aprovisionamiento declarativo 
El aprovisionamiento declarativo es un aprovisionamiento "sin código" que se puede instalar y configurar mediante el editor de reglas de sincronización. Puede utilizar el editor para configurar y crear sus propias reglas de aprovisionamiento.

Una parte esencial del aprovisionamiento declarativo es el lenguaje de expresiones que se usa en flujos de atributos. El lenguaje usado es un subconjunto de Microsoft® Visual Basic® para Aplicaciones. Este lenguaje se usa en Microsoft Office, y los usuarios con experiencia en VBScript también lo reconocerán. El lenguaje de expresiones de aprovisionamiento declarativo solo utiliza funciones y no es un lenguaje estructurado; no hay métodos ni instrucciones. Las funciones se anidarán en su lugar en el flujo de programa rápido.

Para obtener más información sobre el lenguaje de expresiones, vea [Descripción de las expresiones de aprovisionamiento declarativo](https://msdn.microsoft.com/library/azure/dn801048.aspx).

## Documentación adicional
Algunos de los documentos que se crearon para Sincronización de Azure AD siguen siendo válidos y se aplican a Azure AD Connect.  Aunque se está poniendo todo el empeño en trasladar esa documentación a Azure.com, parte de la misma sigue residiendo en la biblioteca del ámbito de MSDN.  Para obtener documentación adicional, consulte [Azure AD Connect en MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) y [Sincronización de Azure AD en MSDN](https://msdn.microsoft.com/library/azure/dn790204.aspx).

**Recursos adicionales**

* [Uso de la infraestructura de identidad local en la nube](../active-directory-aadconnect-whatis.md)
* [Cómo funciona Azure AD Connect](../active-directory-aadconnect-howitworks.md)
* [Introducción a Azure AD Connect](../active-directory-aadconnect-getstarted.md)
* [Administración de Azure AD Connect](active-directory-aadconnect-manage.md)
* [Azure AD Connect en MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)
 

<!------HONumber=58_postMigration-->