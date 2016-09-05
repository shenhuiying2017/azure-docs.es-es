<properties
	pageTitle="Sincronización de Azure AD Connect: cómo realizar un cambio en la configuración predeterminada | Microsoft Azure"
	description="Tutorial cómo realizar un cambio en la configuración de la sincronización de Azure AD Connect."
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
	ms.date="08/23/2016"
	ms.author="andkjell"/>


# Sincronización de Azure AD Connect: cómo realizar un cambio en la configuración predeterminada
El objetivo de este tema es guiarle para realizar cambios en la configuración predeterminada en la sincronización de Azure AD Connect. Proporciona las instrucciones para algunos escenarios comunes. Con este conocimiento, podrá realizar algunos cambios sencillos en su propia configuración en función de sus propias reglas de negocio.

## Editor de reglas de sincronización
El editor de reglas de sincronización se utiliza para ver y cambiar la configuración predeterminada. Puede encontrarlo en el menú Inicio en el grupo **Azure AD Connect**. ![Menú Inicio con el Editor de reglas de sincronización](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Cuando lo abra, verá las reglas de serie predeterminadas.

![Editor de reglas de sincronización](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### Navegación por el editor
Las listas desplegables en la parte superior del editor le permiten encontrar rápidamente una regla determinada. Por ejemplo, si desea ver las reglas donde se incluye el atributo proxyAddresses, cambiaría las listas desplegables a la siguiente: ![Filtrado SRE](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png) Para restablecer el filtrado y cargar una nueva configuración, presione **F5** en el teclado.

En la parte superior derecha, verá el botón **Add new rule** (Agregar nueva regla). Este botón se utiliza para crear su propia regla personalizada.

En la parte inferior, tiene botones para actuar sobre una regla de sincronización seleccionada. Los botones **Edit** (Editar) y **Delete** (Eliminar) hacen lo que espera de ellos. **Export** (Exportar) genera un script de PowerShell para volver a crear la regla de sincronización. Este procedimiento permite mover una regla de sincronización de un servidor a otro.

## Creación de su primera regla personalizada
El cambio más frecuente son los cambios en los flujos de atributo. Es posible que los datos del directorio de origen no sean los que quiere en Azure AD. En el ejemplo de esta sección, quiere asegurarse de que el nombre de un usuario especificado siempre está en **mayúsculas o minúsculas**.

### Deshabilitación del programador
De forma predeterminada, el [programador](active-directory-aadconnectsync-feature-scheduler.md) se ejecuta cada 30 minutos. Quiere asegurarse de que no se inicia mientras realiza cambios y soluciona problemas en las nuevas reglas. Para deshabilitar temporalmente el programador, inicie PowerShell y ejecute `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Deshabilitación del programador](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)

### Creación de la regla

1. Haga clic en **Agregar nueva regla**.
2. En la página **Descripción**, escriba lo siguiente: ![Regla de filtrado de entrada](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)
	- Name (Nombre): asigne un nombre descriptivo a la regla.
	- Description (Descripción): alguna aclaración para que cualquier otro usuario entienda para qué sirve la regla.
	- Connected system (Sistema conectado): el sistema donde se puede encontrar el objeto. En este caso, se selecciona Conector Active Directory.
	- Connected System/Metaverse Object Type (Sistema conectado/Tipo de objeto de metaverso): seleccione **User** y **Person**, respectivamente.
	- Link Type (Tipo de vínculo): cambie este valor a **Join**.
	- Precedence (Prioridad): proporcione un valor que sea único en el sistema. Un valor numérico inferior indica una mayor prioridad.
	- Tag (Etiqueta): deje este campo en blanco. Solo las reglas de serie de Microsoft tienen esta casilla rellena con un valor.
3. En la página **Scoping filter** (Filtro de ámbito), escriba **givenName ISNOTNULL**. ![Filtro del ámbito de la regla entrada](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png) Esta sección se utiliza para definir los objetos a los que debería aplicarse la regla. Si se deja vacía, la regla se aplica a todos los objetos de usuario. Pero también incluiría a salas de conferencias, cuentas de servicio y otros objetos de usuario que no son personas.
4. En el campo **Join rules** (Reglas de unión), déjelo vacío.
5. En la página **Transformations** (Transformations), cambie FlowType por **Expression**. Seleccione el atributo de destino **giveName** y, en el origen, escriba `PCase([givenName])`. ![Transformaciones de la regla de entrada](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png) El motor de sincronización distingue mayúsculas de minúsculas tanto en el nombre de función como en el nombre del atributo. Si escribe algo incorrecto, verá una advertencia al agregar la regla. El editor permite guardar y continuar, por lo que deberá volver a abrir la regla y corregir la regla.
6. Haga clic en **Agregar** para guardar la regla.

La nueva regla personalizada debe aparecer con las demás reglas de sincronización en el sistema.

### Comprobación del cambio
Con este nuevo cambio, quiere asegurarse de que funciona según lo previsto y no se producen errores. Dependiendo del número de objetos que tenga, hay dos maneras diferentes de realizar este paso.

1. Ejecute una sincronización completa en todos los objetos.
2. Ejecute una vista previa y una sincronización completa en un solo objeto.

Inicie el **Servicio de sincronización** desde el menú Inicio. Los pasos de esta sección se encuentran en esta herramienta.

1. **Sincronización completa en todos los objetos** Seleccione **Conectores** en la parte superior. Identifique el conector en el que ha realizado un cambio en la sección anterior, en este caso Servicios de dominio de Active Directory, y selecciónelo. Seleccione **Ejecutar** en Acciones y seleccione **Sincronización completa** y **Aceptar**. ![Sincronización completa](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png) Los objetos se actualizan ahora en el metaverso. Ahora desea ver el objeto en el metaverso.

2. **Vista previa y sincronización completa en un solo objeto** Seleccione **Conectores** en la parte superior. Identifique el conector en el que ha realizado un cambio en la sección anterior, en este caso Servicios de dominio de Active Directory, y selecciónelo. Seleccione **Search Connector Space** (Buscar espacio de conector). Use el ámbito para encontrar un objeto que se vaya a utilizar para probar el cambio. Seleccione el objeto y haga clic en **Vista previa**. En la nueva pantalla, seleccione **Vista previa de confirmación**. ![Vista previa de confirmación](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png) Ahora, el cambio se confirma en el metaverso.

**Visualización del objeto en el metaverso** Ahora quiere seleccionar algunos objetos de ejemplo para asegurarse de que el valor es el previsto y que se aplica la regla. Seleccione **Metaverse Search** (Búsqueda de metaverso) en la parte superior. Agregue cualquier filtro que necesite para buscar los objetos pertinentes. En los resultados de la búsqueda, abra un objeto. Consulte los valores de atributo y compruebe también que en la columna **Sync Rules** (Reglas de sincronización) se aplica la regla de la forma prevista. ![Búsqueda de metaverso](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)
### Habilitación del programador
Si todo es como se esperaba, vuelva a habilitar el programador. En PowerShell, ejecute `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## Otros cambios de flujo de atributo comunes
La sección anterior describe cómo realizar cambios en un flujo de atributo. En esta sección, se proporcionan algunos ejemplos adicionales. Los pasos para crear la regla de sincronización están abreviados, pero encontrará los pasos completos en la sección anterior.

### Uso de otro atributo distingo al valor predeterminado
En Fabrikam hay un bosque donde se usa el alfabeto local en el nombre, el apellido y el nombre para mostrar dados. La representación de caracteres latinos de estos atributos se encuentra en los atributos de extensión. Al generar la lista global de direcciones en Azure AD y Office 365, la organización quiere usar en su lugar estos atributos.

Con una configuración predeterminada, un objeto del bosque local tiene el siguiente aspecto:![Flujo de atributos 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Para crear una regla con otros flujos de atributo, haga lo siguiente:

- Inicie el **Editor de reglas de sincronización** en el menú Inicio.
- Con **Entrante** aún seleccionado a la izquierda, haga clic en el botón **Agregar nueva regla**.
- Asigne a la regla un nombre y una descripción. Seleccione Active Directory local y los tipos de objeto correspondientes. En **Tipo de vínculo**, seleccione **Unir**. Para establecer la precedencia, seleccione un número que no se use en otra regla. Las reglas listas para usar comienzan con 100, así que en este ejemplo se puede usar el valor 50. ![Flujo de atributos 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
- Deje el ámbito vacío (es decir, se debe aplicar a todos los objetos de usuario del bosque).
- Deje las reglas de unión vacías (es decir, permita que la regla lista para usar controle las uniones).
- En Transformaciones, cree los siguientes flujos. ![Flujo de atributos 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
- Haga clic en **Agregar** para guardar la regla.
- Vaya a **Synchronization Service Manager**. En **Conectores**, seleccione el conector donde agregamos la regla. Seleccione **Ejecutar** y **Sincronización completa**. Una sincronización completa vuelve a calcular todos los objetos con las reglas actuales.

Este es el resultado para el mismo objeto con esta regla personalizada: ![Flujo de atributos 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### Longitud de los atributos
Los atributos de cadena se establecen de forma predeterminada para que puedan indexarse y tengan una longitud máxima de 448 caracteres. Si está trabajando con atributos de cadena que podrían contener más, asegúrese de incluir lo siguiente en el flujo de atributos: `attributeName` <- `Left([attributeName],448)`

### Cambio de userPrincipalSuffix
Los usuarios no siempre conocen el atributo userPrincipalName de Active Directory y podría no resultar adecuado como identificador de inicio de sesión. El Asistente para la instalación de Azure AD Connect Sync permite la selección de un atributo diferente, por ejemplo, correo. Sin embargo, en algunos casos, debe calcularse el atributo. Por ejemplo, la empresa Contoso tiene dos directorios de Azure AD, uno para producción y otro para pruebas. Quieren que los usuarios de su inquilino de prueba solo cambien el sufijo del identificador de inicio de sesión. `userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

En esta expresión, se toma todo de la izquierda en el primer @-sign (Word) y se concatena con una cadena fija.

### Conversión de varios valores en un valor único
Algunos atributos de Active Directory tienen varios valores en el esquema aunque parezca que tienen un valor en Usuarios y equipos de Active Directory. Un ejemplo es el atributo description. `description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

En esta expresión, en caso de que el atributo tenga un valor, tomamos el primer elemento (Elemento) en el atributo, quitamos los espacios iniciales y finales (Recorte) y, a continuación, conservamos los primeros 448 caracteres (Izquierda) en la cadena.

### No pasar atributos
Para más información sobre el escenario de esta sección, consulte [Control del proceso de flujo de atributo](#control-the-attribute-flow-process).

Existen dos maneras de pasar un atributo. El primero está disponible en el Asistente para la instalación y le permite [quitar atributos seleccionados](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Esta opción funciona si nunca ha sincronizado el atributo antes. Sin embargo si ha comenzado a sincronizar este atributo y después lo quita con esta característica, el motor de sincronización deja de administrar el atributo y los valores existentes se quedan en Azure AD.

Si quiere quitar el valor de un atributo y asegurarse de no pasarlo en el futuro, debe crear en su lugar una regla personalizada.

En Fabrikam no hemos dado cuenta de que algunos de los atributos sincronizamos con la nube no deberían estar allí. Queremos asegurarse de que estos atributos se quitan de Azure AD. ![Atributos de extensión incorrecta](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

- Cree una nueva regla de sincronización de entrada y rellene la descripción ![Descripciones](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
- Cree flujos de atributos de tipo **Expression** y con el origen **AuthoritativeNull**. El literal **AuthoritativeNull** indica que el valor debe estar vacío en la máquina virtual incluso si una regla de sincronización de prioridad inferior intenta rellenar el valor. ![Transformación de atributos de extensión](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
- Guarde la regla de sincronización. Inicie el **Servicio de sincronización**, busque el conector y seleccione **Ejecutar** y **Sincronización completa**. Este paso vuelve a calcular todos los flujos de atributo.
- Compruebe que los cambios deseados están a punto de exportarse; para ello, busque en el espacio de conector. ![Eliminación por fases](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## Conceptos avanzados

### Control del proceso de flujo de atributo
Cuando se configuran varias reglas de sincronización de entrada para contribuir al mismo atributo de metaverso, se utiliza la prioridad para determinar al ganador. La regla de sincronización con prioridad más alta (cuyo valor numérico es el más bajo) va a aportar el valor. Lo mismo sucede para las reglas de salida. La regla de sincronización de prioridad más alta es la que gana y aporta el valor al directorio conectado.

En algunos casos, en lugar de aportar un valor, la regla de sincronización determina cómo deben comportarse las otras reglas. Hay algunos literales especiales que se utilizan para este caso.

Para las reglas de sincronización de entrada, se puede usar el literal **NULL** para indicar que el flujo no tiene ningún valor para aportar. Otra regla con una prioridad más baja puede aportar un valor. Si no hay ninguna regla que aporte un valor, se quita el atributo metaverse. Para una regla de salida, si **NULL** es el valor final después de que se han procesado todas las reglas de sincronización, se quita el valor en el directorio conectado.

El literal **AuthoritativeNull** es similar a **NULL** pero con la diferencia de que ninguna regla de prioridad inferior puede aportar un valor.

Un flujo de atributo también puede usar el atributo **IgnoreThisFlow**. Es similar a NULL en el sentido de que indica que no hay nada que aportar. La diferencia es que no se quita ningún valor existente en el destino. Es como si el flujo de atributo nunca hubiera estado allí.

Este es un ejemplo:

En *Out to AD - User Exchange hybrid* se puede encontrar el siguiente flujo: `IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)` Esta expresión se debe leer como: si el buzón del usuario se encuentra en Azure AD, el flujo de atributo va de Azure AD a AD. Si no es así, no vuelve nada a Active Directory. En este caso, se mantiene el valor existente en AD.

### ImportedValue
La función ImportedValue es diferente de todas las demás funciones, ya que el nombre del atributo debe incluirse entre comillas, en lugar de corchetes: `ImportedValue("proxyAddresses")`.

Normalmente, un atributo usa el valor esperado durante la sincronización, incluso si no se ha exportado todavía o se recibió un error durante la exportación ("parte superior de la torre"). Una sincronización entrante supone que un atributo que todavía no ha llegado a un directorio conectado lo alcanzará en algún momento. En algunos casos, es importante sincronizar únicamente un valor confirmado por el directorio conectado ("torre de importación delta y holograma").

Un ejemplo de esta función se puede encontrar en la regla de sincronización de serie *In from AD – User Common from Exchange*. En Exchange híbrido, el valor agregado por Exchange Online solo se debe sincronizar cuando se ha confirmado que el valor se exportó correctamente: `proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## Pasos siguientes

Obtenga más información sobre las [expresiones de aprovisionamiento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) usadas para los flujos de atributo.

Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0824_2016-->