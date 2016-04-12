<properties
	pageTitle="Sincronización de Azure AD Connect: interfaz de usuario de Synchronization Service Manager | Microsoft Azure"
	description="Conozca la pestaña Conectores de Synchronization Service Manager para Azure AD Connect."
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
	ms.date="03/03/2016"
	ms.author="andkjell"/>


# Sincronización de Azure AD Connect: Synchronization Service Manager

| [Operaciones](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Conectores](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Diseñador de metaverso](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Búsqueda de metaverso](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) |
| --- | --- | --- | --- |

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

La pestaña Conectores se usa para administrar todos los sistemas a los que está conectado el motor de sincronización.

## Acciones del conector

| Acción | Comentario |
| --- | --- |
| Crear | No usar. Para conectarse a los bosques de AD adicionales, use el Asistente para instalación. |
| Propiedades | Se usa para el filtrado por dominio y unidad organizativa. |
| Eliminar | Se usa para eliminar los datos en el espacio del conector o eliminar la conexión a un bosque. |
| Configurar perfiles de ejecución | A excepción del filtrado de dominio, no es necesario configure ninguna otra opción. |
| Ejecute | Se usa para iniciar una ejecución con definición de un perfil. |
| Detención | Detiene un conector que esté ejecutando un perfil. |
| Exportar conector | No usar. |
| Importar conector | No usar. |
| Actualizar conector | No usar. |
| Actualizar esquema | Actualiza el esquema en caché. Es preferible usar la opción en el asistente para la instalación, ya que también actualizará las reglas de sincronización.
| Espacio del conector de búsqueda | Se usa para buscar objetos y para realizar un [Seguimiento de un objeto y sus datos a través del sistema](#follow-an-object-and-its-data-through-the-system). |

### Eliminar
La acción de eliminación se usa para dos cosas distintas. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

Con la opción **Eliminar solo el espacio del conector** se eliminarán todos los datos, pero se mantendrá la configuración.

Con la opción **Eliminar el conector y el espacio del conector** se eliminarán todos los datos y toda la configuración. Se usa cuando no se desea volver a conectarse a un bosque.

Ambas opciones sincronizarán todos los objetos y actualizarán los objetos del metaverso. Se trata de una operación de larga duración.

### Espacio del conector de búsqueda
La acción del espacio del conector de búsqueda es útil para buscar objetos y solucionar problemas de datos.

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Empiece seleccionando un **ámbito**. Puede buscar según los datos (RDN, DN, delimitador, subárbol) o el estado del objeto (todas las demás opciones). ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png) Por ejemplo, si hace una búsqueda de un subárbol obtiene todos los objetos de una unidad organizativa. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png) A partir de aquí puede seleccionar un objeto, seleccionar las **propiedades** y realizar un [seguimiento](#follow-an-object-and-its-data-through-the-system) desde el espacio del conector de origen hasta el metaverso y el espacio del conector de destino.

## Seguimiento de un objeto y sus datos a través del sistema
Cuando esté solucionando un problema relacionado con datos, realice el seguimiento de un objeto desde el espacio del conector de origen hasta el metaverso y el espacio del conector de destino, que es un procedimiento clave para comprender por qué los datos no tienen los valores esperados.

### Propiedades de objeto del espacio del conector
**Importación** Al abrir un objeto cs, aparecen varias pestañas en la parte superior. La pestaña **Importación** muestra los datos que se almacenan provisionalmente después de una importación. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/csimport.png) En **Valor antiguo** se muestran los datos almacenados en el sistema y en **Nuevo valor**, los que se han recibido desde el sistema de origen y no se han aplicado todavía. En este caso, dado que no hay un error de sincronización, no se puede aplicar el cambio.

**Error** La página de error sólo está visible si se produce un problema con el objeto. Consulte los detalles de la página de operaciones para obtener más información acerca de cómo [solucionar los errores de sincronización](active-directory-aadconnectsync-service-manager-ui-operations.md#troubleshoot-errors-in-operations-tab).

**Linaje** La pestaña Linaje muestra cómo el objeto de espacio del conector está relacionado con el objeto de metaverso. Podemos ver cuándo fue la última vez que importamos un cambio desde el sistema conectado y qué reglas que se han aplicado para rellenar los datos en el metaverso. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineage.png) En la columna **Acción** vemos que hay una regla de sincronización **Entrante** con la acción **Aprovisionar**. Esto indica que, siempre que este objeto de espacio del conector esté presente, el objeto de metaverso permanecerá. En cambio, si la lista de reglas de sincronización muestra una regla de sincronización con la dirección **Saliente** y **Aprovisionar**, indica que este objeto se eliminará cuando se elimine el objeto de metaverso. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineageout.png) También podemos ver en la columna **PasswordSync** que el espacio del conector entrante puede contribuir a los cambios realizados en la contraseña debido a que una regla de sincronización tiene el valor **True**. Esta contraseña se envía a Azure AD a través de la regla de salida.

Desde la pestaña Linaje, puede acceder al metaverso haciendo clic en [Propiedades del objeto de metaverso](#metaverse-object-properties).

En la parte inferior de todas las pestañas, hay dos botones: **Vista previa** y **Registro**.

**Vista previa** La página de vista previa se usa para sincronizar un único objeto. Es útil si está solucionando problemas de algunas reglas de sincronización del cliente y desea ver el efecto de un cambio en un único objeto. Puede seleccionar entre **Sinc. completa** y **Sincronización delta**. También puede seleccionar **Generar vista previa**, que únicamente mantendrá el cambio en la memoria, y **Vista previa de confirmación**, que llevará a cabo todos los cambios en los espacios del conector de destino. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview1.png) Puede inspeccionar el objeto y la regla a la que se aplica un flujo de atributo particular. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview2.png)

**Registro** La página Registro se usa para ver el historial y el estado de sincronización de la contraseña; consulte [Solución de problemas de sincronización de contraseña](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization) para obtener más información.

### Propiedades del objeto de metaverso
**Atributos** En la pestaña Atributos puede ver los valores y a qué conector ayudan. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvattributes.png) **Conectores** La pestaña Conectores muestra todos los espacios del conector que tienen una representación del objeto. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvconnectors.png) Esta pestaña también permite navegar al [objeto de espacio del conector](#connector-space-object-properties).

## Pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0309_2016-->