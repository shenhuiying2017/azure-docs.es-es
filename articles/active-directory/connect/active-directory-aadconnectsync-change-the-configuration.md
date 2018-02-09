---
title: "Sincronización de Azure AD Connect: cambio en la configuración de la sincronización de Azure AD Connect | Microsoft Docs"
description: "Tutorial cómo realizar un cambio en la configuración de la sincronización de Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2018
ms.author: billmath
ms.openlocfilehash: e97d3e3e35ee87864c5d38e75e08e62088e25fdb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Sincronización de Azure AD Connect: Realización de un cambio en la configuración predeterminada
El objetivo de este artículo es guiarle para realizar cambios en la configuración predeterminada de la sincronización de Azure Active Directory (Azure AD) Connect. Proporciona las instrucciones para algunos escenarios comunes. Con este conocimiento, podrá realizar cambios sencillos en su propia configuración en función de sus propias reglas de negocio.

## <a name="synchronization-rules-editor"></a>Editor de reglas de sincronización
El editor de reglas de sincronización se utiliza para ver y cambiar la configuración predeterminada. Puede encontrarlo en el menú **Inicio** en el grupo **Azure AD Connect**.  
![Menú Inicio con el Editor de reglas de sincronización](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Cuando abra el editor, verá las reglas de serie predeterminadas.

![Editor de reglas de sincronización](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navegación por el editor
Las listas desplegables en la parte superior del editor le permiten encontrar rápidamente una regla determinada. Por ejemplo, si desea ver las reglas donde se incluye el atributo proxyAddresses, puede cambiar las listas desplegables a la siguiente:  
![Filtrado SRE](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
Para restablecer el filtrado y cargar una nueva configuración, presione F5 en el teclado.

En la esquina superior derecha se encuentra el botón **Agregar nueva regla**. Puede usar este botón para crear su propia regla personalizada.

En la parte inferior se encuentran los botones para actuar sobre una regla de sincronización seleccionada. Los botones **Edit** (Editar) y **Delete** (Eliminar) hacen lo que espera de ellos. El botón **Exportar** genera un script de PowerShell para volver a crear la regla de sincronización. Este procedimiento permite mover una regla de sincronización de un servidor a otro.

## <a name="create-your-first-custom-rule"></a>Creación de su primera regla personalizada
Los cambios más frecuentes son los cambios en los flujos de atributo. Es posible que los datos del directorio de origen no sean los mismos que los de Azure AD. En el ejemplo de esta sección, asegúrese de que el nombre de un usuario especificado siempre esté en *mayúsculas o minúsculas* según sea lo correcto.

### <a name="disable-the-scheduler"></a>Deshabilitación del programador
De forma predeterminada, el [programador](active-directory-aadconnectsync-feature-scheduler.md) se ejecuta cada 30 minutos. Asegúrese de que no se inicia mientras realiza cambios y soluciona problemas en las nuevas reglas. Para deshabilitar temporalmente el programador, inicie PowerShell y ejecute `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Deshabilitación del programador](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Creación de la regla
1. Haga clic en **Agregar nueva regla**.
2. En la página **Descripción**, escriba lo siguiente:  
   ![Regla de filtrado de entrada](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
   * **Name** (Nombre): asigne un nombre descriptivo a la regla.
   * **Description** (Descripción): facilite alguna aclaración para que cualquier otro usuario entienda para qué sirve la regla.
   * **Connected system** (Sistema conectado): el sistema donde se puede encontrar el objeto. En este caso, seleccione el **Conector Active Directory**.
   * **Connected System/Metaverse Object Type** (Sistema conectado/Tipo de objeto de metaverso): seleccione **User** y **Person**, respectivamente.
   * **Link Type** (Tipo de vínculo): cambie este valor a **Join**.
   * **Precedence** (Prioridad): proporcione un valor que sea único en el sistema. Un valor numérico inferior indica una mayor prioridad.
   * **Tag** (Etiqueta): deje esto en blanco. Solo las reglas de serie de Microsoft tienen esta casilla rellena con un valor.
3. En la página **Scoping filter** (Filtro de ámbito), escriba **givenName ISNOTNULL**.  
   ![Filtro del ámbito de la regla entrada](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
   Esta sección se utiliza para definir los objetos a los que debería aplicarse la regla. Si se deja vacía, la regla se aplica a todos los objetos de usuario. Sin embargo, eso también incluiría a salas de conferencias, cuentas de servicio y otros objetos de usuario que no son personas.
4. En la página **Join rules** (Reglas de unión), deje el campo vacío.
5. En la página **Transformations** (Transformations), cambie **FlowType** por **Expression**. En **Target Attribute** (Atributo de destino), seleccione **givenName**. Y en **Source** (Origen), especifique **PCase([givenName])**.
   ![Transformaciones de la regla de entrada](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
   El motor de sincronización distingue mayúsculas de minúsculas tanto en el nombre de función como en el nombre del atributo. Si escribe algo incorrecto, verá una advertencia al agregar la regla. Puede guardar y continuar, pero deberá volver a abrir y corregir la regla.
6. Haga clic en **Agregar** para guardar la regla.

La nueva regla personalizada debe aparecer con las demás reglas de sincronización en el sistema.

### <a name="verify-the-change"></a>Comprobación del cambio
Con este nuevo cambio, quiere asegurarse de que funciona según lo previsto y no se producen errores. Dependiendo del número de objetos que tenga, hay dos maneras de realizar este paso:

- Ejecute una sincronización completa en todos los objetos.
- Ejecute una vista previa y una sincronización completa en un solo objeto.

Abra el **Servicio de sincronización** desde el menú **Inicio**. Los pasos de esta sección se encuentran en esta herramienta.

**Sincronización completa en todos los objetos**  

   1. Seleccione **Conectores** en la parte superior. Identifique el conector que modificó en la sección anterior, en este caso Active Directory Domain Services, y selecciónelo. 
   2. En **Acciones**, seleccione **Ejecutar**.
   3. Seleccione **Sincronización completa**y, a continuación, seleccione **Aceptar**.
   ![Sincronización completa](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
   Los objetos se actualizan ahora en el metaverso. Compruebe los cambios examinando el objeto en el metaverso.

**Vista previa y sincronización completa en un solo objeto**  

   1. Seleccione **Conectores** en la parte superior. Identifique el conector que modificó en la sección anterior, en este caso Active Directory Domain Services, y selecciónelo.
   2. Seleccione **Search Connector Space**(Buscar espacio de conector). 
   3. Use el **ámbito** para encontrar un objeto que se vaya a utilizar para probar el cambio. Seleccione el objeto y haga clic en **Vista previa**. 
   4. En la nueva pantalla, seleccione **Vista previa de confirmación**.  
   ![Commit preview](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
   Ahora, el cambio se confirma en el metaverso.

**Visualización del objeto en el metaverso**  

1. Elija varios objetos de ejemplo para asegurarse de que el valor es el previsto y que se aplica la regla. 
2. Seleccione **Metaverse Search** (Búsqueda de metaverso) en la parte superior. Agregue cualquier filtro que necesite para buscar los objetos pertinentes. 
3. En los resultados de la búsqueda, abra un objeto. Consulte los valores de atributo y compruebe también que en la columna **Reglas de sincronización** se aplica la regla de la forma prevista.  
![Metaverse search](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Habilitación del programador
Si todo es como se esperaba, vuelva a habilitar el programador. En PowerShell, ejecute `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Otros cambios de flujo de atributo comunes
La sección anterior describe cómo realizar cambios en un flujo de atributo. En esta sección, se proporcionan algunos ejemplos adicionales. Los pasos para crear la regla de sincronización están abreviados, pero encontrará los pasos completos en la sección anterior.

### <a name="use-an-attribute-other-than-the-default"></a>Uso de otro atributo distinto al valor predeterminado
En este escenario de Fabrikam hay un bosque donde se usa el alfabeto local en el nombre, el apellido y el nombre para mostrar que se hayan especificado. La representación de caracteres latinos de estos atributos se encuentra en los atributos de extensión. Al generar una lista global de direcciones en Azure AD y Office 365, la organización quiere usar en su lugar estos atributos.

Con una configuración predeterminada, un objeto del bosque local tiene el siguiente aspecto:  
![Flujo de atributos 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Para crear una regla con otros flujos de atributo, haga lo siguiente:

1. Inicie el **Editor de reglas de sincronización** en el menú **Inicio**.
2. Con **Entrante** aún seleccionado a la izquierda, haga clic en el botón **Agregar nueva regla**.
3. Asigne a la regla un nombre y una descripción. Seleccione la instancia de Active Directory local y los tipos de objeto correspondientes. En **Tipo de vínculo**, seleccione **Unir**. Para establecer la **precedencia**, seleccione un número que no se use en otra regla. Las reglas listas para usar comienzan con 100, así que en este ejemplo se puede usar el valor 50.
  ![Flujo de atributos 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
4. Deje **Scoping filter** (Filtro de ámbito) vacío. (Es decir, se debería aplicar a todos los objetos de usuario del bosque).
5. Deje **Join rules** (Reglas de unión) vacío. (Es decir, permita que la regla lista para usar controle las uniones).
6. En **Transformaciones**, cree los siguientes flujos:  
  ![Flujo de atributos 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
7. Haga clic en **Agregar** para guardar la regla.
8. Vaya a **Synchronization Service Manager**. En **Conectores**, seleccione el conector donde agregó la regla. Seleccione **Ejecutar** y, a continuación, seleccione **Sincronización completa**. Una sincronización completa vuelve a calcular todos los objetos con las reglas actuales.

Este es el resultado para el mismo objeto con esta regla personalizada:   
![Flujo de atributos 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Longitud de los atributos
Los atributos de cadena se pueden indexar de forma predeterminada y tienen una longitud máxima de 448 caracteres. Si está trabajando con atributos de cadena que podrían contener más, asegúrese de incluir lo siguiente en el flujo de atributos:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Cambio de userPrincipalSuffix
Los usuarios no siempre conocen el atributo userPrincipalName de Active Directory y podría no resultar adecuado como identificador de inicio de sesión. El Asistente para la instalación de Azure AD Connect Sync permite la selección de un atributo diferente, por ejemplo, *correo*. Sin embargo, en algunos casos, debe calcularse el atributo.

Por ejemplo, la empresa Contoso tiene dos directorios de Azure AD, uno para producción y otro para pruebas. Quieren que los usuarios de su inquilino de prueba solo cambien el sufijo del identificador de inicio de sesión:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

En esta expresión, se toma todo de la izquierda en el primer @-sign (Word) y se concatena con una cadena fija.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Conversión de un atributo multivalor en un valor único
Algunos atributos de Active Directory tienen varios valores en el esquema aunque parezca que tienen un solo valor en Usuarios y equipos de Active Directory. Un ejemplo es el atributo description:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

En esta expresión, en caso de que el atributo tenga un valor, se toma el primer elemento (*Elemento*) en el atributo, se quitan los espacios iniciales y finales (*Recorte*) y, a continuación, se conservan los primeros 448 caracteres (*Izquierda*) en la cadena.

### <a name="do-not-flow-an-attribute"></a>No pasar atributos
Para más información sobre el escenario de esta sección, consulte [Control del proceso de flujo de atributo](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Existen dos maneras de pasar un atributo. La primera es usar el Asistente para la instalación para [quitar los atributos seleccionados](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Esta opción funciona si nunca ha sincronizado el atributo antes. Sin embargo si ha comenzado a sincronizar este atributo y después lo quita con esta característica, el motor de sincronización deja de administrar el atributo y los valores existentes se quedan en Azure AD.

Si quiere quitar el valor de un atributo y asegurarse de no pasarlo en el futuro, debe crear una regla personalizada.

En este escenario de Fabrikam, nos hemos dado cuenta de que algunos de los atributos que sincronizamos con la nube no deberían estar allí. Queremos asegurarse de que estos atributos se quitan de Azure AD.  
![Atributos de extensión incorrecta](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

1. Cree una nueva regla de sincronización de entrada y rellene la descripción.
  ![Descripciones](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
2. Cree flujos de atributos con **Expresión** para **FlowType** y con **AuthoritativeNull** para **Origen**. El literal **AuthoritativeNull** indica que el valor debe estar vacío en el metaverso incluso si una regla de sincronización de prioridad inferior intenta rellenar el valor.
  ![Transformación de atributos de extensión](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
3. Guarde la regla de sincronización. Inicie el **Servicio de sincronización**, busque el conector y seleccione **Ejecutar** y **Sincronización completa**. Este paso vuelve a calcular todos los flujos de atributo.
4. Compruebe que los cambios deseados están a punto de exportarse; para ello, busque en el espacio de conector.
  ![Eliminación por fases](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Creación de reglas con PowerShell
El uso del editor de reglas de sincronización es apropiado si solo tiene unos pocos cambios que realizar. Si tiene que realizar muchos cambios, PowerShell podría ser la mejor opción. Algunas características avanzadas solo están disponibles con PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Obtención del script de PowerShell para una regla integrada
Para ver el PowerShell de script que crea una regla integrada, seleccione la regla en el editor de reglas de sincronización y haga clic en **Exportar**. Esta acción le proporciona el script de PowerShell que crea la regla.

### <a name="advanced-precedence"></a>Precedencia avanzada
Las reglas de sincronización integradas comienzan con un valor de prioridad de 100. Si tiene varios bosques y necesita realizar muchos cambios personalizados, a continuación, 99 reglas de sincronización podrían no ser suficientes.

Puede indicar al motor de sincronización que desea insertar reglas adicionales antes de las reglas integradas. Para conseguirlo, siga estos pasos:

1. Marque la primera regla de sincronización integrada (**In from AD-User Join**) en el editor de reglas de sincronización y seleccione **Exportar**. Copie el valor del identificador de SR.  
![PowerShell antes del cambio](./media/active-directory-aadconnectsync-change-the-configuration/powershell1.png)  
2. Cree la nueva regla de sincronización. Puede usar el editor de reglas de sincronización para ello. Exporte la regla a un script de PowerShell.
3. En la propiedad **PrecedenceBefore**, inserte el valor del identificador de la regla integrada. Establezca el valor de **Precedencia** en **0**. Asegúrese de que el atributo de identificador es único y de que no está reutilizando un GUID de otra regla. Asegúrese también de que la propiedad **ImmutableTag** no está establecida. Esta propiedad solo se debe establecer para una regla integrada.
4. Guarde el script de PowerShell y ejecútelo. Como resultado, a la regla integrada se le agrega un valor de precedencia de 100 y se incrementan el resto de reglas rápidas.  
![PowerShell después del cambio](./media/active-directory-aadconnectsync-change-the-configuration/powershell2.png)  

Puede tener muchas reglas de sincronización personalizadas con el mismo valor **PrecedenceBefore** cuando sea necesario.

## <a name="enable-synchronization-of-usertype"></a>Habilite la sincronización de UserType
Azure AD Connect admite la sincronización del atributo **UserType** para objetos **User** en la versión 1.1.524.0 y posteriores. Más concretamente, se introdujeron los cambios siguientes:

- El esquema del tipo de objeto **User** del conector de Azure AD se extiende para incluir el atributo UserType, que es de tipo cadena y tiene un solo valor.
- El esquema del tipo de objeto **Person** del metaverso se extiende para incluir el atributo UserType, que es de tipo cadena y tiene un solo valor.

De forma predeterminada, el atributo UserType no está habilitado para la sincronización porque no hay ningún atributo UserType correspondiente en la instancia local de Active Directory. Debe habilitar manualmente la sincronización. Antes de hacerlo, vea los comportamientos siguientes que exige Azure AD:

- Azure AD solo acepta dos valores para el atributo UserType: **Miembro** e **Invitado**.
- Si el atributo UserType no está habilitado para la sincronización en Azure AD Connect, los usuarios de Azure AD que se crearon mediante la sincronización de directorios tendrían el atributo UserType establecido en **Miembro**.
- Azure AD no permite que Azure AD Connect cambie el atributo UserType en usuarios de Azure AD existentes. Solo puede establecerse durante la creación de los usuarios de Azure AD.

Antes de habilitar la sincronización del atributo UserType, primero debe decidir cómo se obtendrá el atributo UserType de Active Directory local. Los siguientes son los métodos más comunes:

- Designar un atributo de AD local sin usar (p. ej., extensionAttribute1) que se utilizará como atributo de origen. El atributo de AD local designado debería ser de tipo **cadena**, tener un solo valor y contener el valor **Miembro** o el valor **Invitado**. 

    Si elige este enfoque, debe asegurarse de que el atributo designado se rellena con el valor correcto para todos los objetos User existentes en Active Directory local que se sincronizan con Azure AD antes de que se habilite la sincronización del atributo UserType.

- Como alternativa, puede obtener el valor del atributo UserType de otras propiedades. Por ejemplo, desea sincronizar todos los usuarios como **Invitado** si su atributo de UserPrincipalName de AD local termina con la parte del dominio de *@partners.fabrikam123.org*. 

    Como se ha mencionado anteriormente, Azure AD Connect no permite que Azure AD Connect cambie el atributo UserType en usuarios de Azure AD existentes. Por lo tanto, debe asegurarse de que la lógica que haya decidido es coherente con cómo está ya configurado el atributo UserType para todos los usuarios existentes de Azure AD en el inquilino.

Los pasos para habilitar la sincronización del atributo UserType se pueden resumir en:

1.  Deshabilite el programador de sincronización y compruebe que no hay ninguna sincronización en curso.
2.  Agregue el atributo de origen al esquema de conector de AD local.
3.  Agregue UserType al esquema de conector de Azure AD.
4.  Cree una regla de sincronización de entrada para enviar el valor del atributo desde la instancia local de Active Directory.
5.  Cree una regla de sincronización de salida para enviar el valor del atributo a Azure AD.
6.  Ejecute un ciclo de sincronización completo.
7.  Habilite el programador de sincronizaciones.

>[!NOTE]
> El resto de esta sección tratará estos pasos. Se describen en el contexto de una implementación de Azure AD con topología de bosque único y sin reglas de sincronización personalizadas. Si tiene una topología de varios bosques, reglas de sincronización personalizadas configuradas o un servidor de ensayo, debe ajustar los pasos según corresponda.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Paso 1: deshabilite el programador de sincronizaciones y compruebe que no hay ninguna sincronización en curso.
Asegúrese de que no realiza ninguna sincronización mientras está actualizando reglas de sincronización para evitar que se exporten cambios no deseados a Azure AD. Para deshabilitar el programador de sincronización integrado:

 1. Inicie una sesión de PowerShell en el servidor de Azure AD Connect.
 2. Deshabilite la sincronización programada mediante la ejecución del cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 3. Abra Synchronization Service Manager. Para ello, vaya a **INICIO** > **Servicio de sincronización**.
 4. Vaya a la pestaña **Operaciones** y confirme que no hay ninguna operación cuyo estado sea *en curso*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Paso 2: Agregar el atributo de origen al esquema de Conector AD local
No todos los atributos de Azure AD se importan en el espacio de conector de AD local. Para agregar el atributo de origen a la lista de atributos importados:

 1. Vaya a la pestaña **Connectors** (Conectores) de Synchronization Service Manager.
 2. Haga clic con el botón derecho en Conector de AD local y seleccione **Propiedades**.
 3. En el cuadro de diálogo emergente, vaya a la pestaña **Seleccionar atributos**.
 4. Asegúrese de que el atributo de origen esté activado en la lista de atributos.
 5. Haga clic en **Aceptar** para guardarlo.
![Agregar el atributo de origen al esquema de Conector de AD local](./media/active-directory-aadconnectsync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>Paso 3: agregue UserType al esquema de conector de Azure AD.
De forma predeterminada, el atributo UserType no se importa en el espacio de Azure AD Connect. Para agregar el atributo UserType a la lista de atributos importados:

 1. Vaya a la pestaña **Connectors** (Conectores) de Synchronization Service Manager.
 2. Haga clic con el botón derecho en **Conector de Azure AD** y seleccione **Propiedades**.
 3. En el cuadro de diálogo emergente, vaya a la pestaña **Seleccionar atributos**.
 4. Asegúrese de que el atributo PreferredDataLocation esté activado en la lista de atributos.
 5. Haga clic en **Aceptar** para guardarlo.

![Agregar el atributo de origen al esquema de Conector Azure AD](./media/active-directory-aadconnectsync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Paso 4: Crear una regla de sincronización de entrada para enviar el valor del atributo desde la instancia local de Active Directory
La regla de sincronización de entrada permite enviar el valor del atributo desde el atributo de origen de la instancia local de Active Directory al metaverso:

1. Inicie el Editor de reglas de sincronización. Para ello, vaya a **INICIO** > **Editor de reglas de sincronización**.
2. Establezca el filtro de búsqueda **Dirección** como **Entrada**.
3. Haga clic en el botón **Agregar nueva regla** para crear una nueva regla de entrada.
4. En la pestaña **Descripción**, proporcione la configuración siguiente:

    | Atributo | Valor | Detalles |
    | --- | --- | --- |
    | NOMBRE | *Proporcione un nombre*. | Por ejemplo, *In from AD – User UserType* |
    | DESCRIPCIÓN | *Proporcione una descripción* |  |
    | Sistema conectado | *Elija Conector AD local* |  |
    | Tipo de objeto de sistema conectado | **User** |  |
    | Propiedades de objeto de metaverso | **Person** |  |
    | Tipo de vínculo | **Join** |  |
    | Prioridad | *Elija un número entre 1 y 99* | El intervalo de 1 a 99 se reserva para las reglas de sincronización personalizadas. No elija ningún valor que use otra regla de sincronización. |

5. Vaya a la pestaña **Filtro de ámbito** y agregue un **solo grupo de filtro de ámbito** con la siguiente cláusula:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Usuario\_ |

    El filtro de ámbito determina a qué objetos de AD locales se aplica esta regla de sincronización de entrada. En este ejemplo, utilizamos el mismo filtro de ámbito que se usó como regla de sincronización integrada *In from AD – User Common*, lo que impide que la regla de sincronización se aplique a los objetos User creados mediante la característica Reescritura de usuarios de Azure AD. Puede que necesite retocar el filtro de ámbito en función de su implementación de Azure AD Connect.

6. Vaya a la pestaña **Transformación** e implemente la regla de transformación que quiera. Por ejemplo, si ha designado un atributo de AD local sin usar (por ejemplo, extensionAttribute1) como atributo de origen para UserType, puede implementar un flujo de atributos directo:

    | Tipo de flujo | Atributo de destino | Origen | Aplicar una vez | Tipo de combinación |
    | --- | --- | --- | --- | --- |
    | Directo | UserType | extensionAttribute1 | No activado | Actualizar |

    En otro ejemplo, puede que desee obtener el valor del atributo UserType de otras propiedades. Por ejemplo, desea sincronizar todos los usuarios como Invitado si su atributo UserPrincipalName de AD local termina con la parte del dominio de *@partners.fabrikam123.org*. Puede implementar una expresión como esta:

    | Tipo de flujo | Atributo de destino | Origen | Aplicar una vez | Tipo de combinación |
    | --- | --- | --- | --- | --- |
    | Directo | UserType | IIF(IsPresent([userPrincipalName]),IIF(CBool(InStr(LCase([userPrincipalName]),"@partners.fabrikam123.org")=0),"Member","Guest"),Error("UserPrincipalName is not present to determine UserType")) | No activado | Actualizar |

7. Haga clic en **Agregar** para crear la regla de entrada.

![Crear regla de sincronización de entrada](./media/active-directory-aadconnectsync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Paso 5: Crear una regla de sincronización de salida para enviar el valor de atributo a Azure AD
La regla de sincronización de salida permite enviar el valor del atributo desde el metaverso al atributo PreferredDataLocation en Azure AD:

1. Vaya al Editor de Reglas de sincronización.
2. Establezca el filtro de búsqueda **Dirección** como **Salida**.
3. Haga clic en el botón **Agregar nueva regla**.
4. En la pestaña **Descripción**, proporcione la configuración siguiente:

    | Atributo | Valor | Detalles |
    | ----- | ------ | --- |
    | NOMBRE | *Proporcione un nombre*. | Por ejemplo, *Out to AAD – User UserType* |
    | DESCRIPCIÓN | *Proporcione una descripción* ||
    | Sistema conectado | *Seleccione el conector AAD* ||
    | Tipo de objeto de sistema conectado | **User** ||
    | Propiedades de objeto de metaverso | **Person** ||
    | Tipo de vínculo | **Join** ||
    | Prioridad | *Elija un número entre 1 y 99* | El intervalo de 1 a 99 se reserva para las reglas de sincronización personalizadas. No elija ningún valor que use otra regla de sincronización. |

5. Vaya a la pestaña **Filtro de ámbito** y agregue un **solo grupo de filtro de ámbito** con dos cláusulas:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Usuario |
    | cloudMastered | NOTEQUAL | True |

    El filtro de ámbito determina a qué objetos de Azure AD se aplica esta regla de sincronización de salida. En este ejemplo, se utiliza el mismo filtro de ámbito de la regla de sincronización integrada *Out to AD – User Identity*. Impide que la regla de sincronización se aplique a los objetos User que no se sincronizan desde la instancia local de Active Directory. Puede que necesite retocar el filtro de ámbito en función de su implementación de Azure AD Connect.

6. Vaya a la pestaña **Transformación** e implemente la siguiente regla de transformación:

    | Tipo de flujo | Atributo de destino | Origen | Aplicar una vez | Tipo de combinación |
    | --- | --- | --- | --- | --- |
    | Directo | UserType | UserType | No activado | Actualizar |

7. Haga clic en **Agregar** para crear la regla de salida.

![Crear regla de sincronización de salida](./media/active-directory-aadconnectsync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Paso 6: ejecute un ciclo de sincronización completo.
En general, se requiere el ciclo de sincronización completo porque hemos agregado nuevos atributos a los esquemas de los conectores Azure AD y AD, y hemos introducido reglas de sincronización personalizadas. Es posible que desee comprobar los cambios antes de exportarlos a Azure AD. 

Puede usar los pasos siguientes para comprobar los cambios mientras sigue los pasos que forman un ciclo de sincronización completo manualmente.

1. Ejecute una **importación completa** en el **conector de AD local**:

   1. Vaya a la pestaña **Operations** (Operaciones) de Synchronization Service Manager.
   2. Haga clic con el botón derecho en **Conector de AD local** y seleccione **Ejecutar**.
   3. En el cuadro de diálogo emergente, seleccione **Importación completa** y haga clic en **Aceptar**.
   4. Espere a que se complete la operación.

    > [!NOTE]
    > Puede omitir la importación completa en el conector de AD local si el atributo de origen ya está incluido en la lista de atributos importados. En otras palabras, no tuvo que realizar ningún cambio durante el [Paso 2: Agregar el atributo de origen al esquema de Conector AD local](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Ejecute una **importación completa** en el **conector de Azure AD**:

   1. Haga clic con el botón derecho en **Conector de Azure AD** y seleccione **Ejecutar**.
   2. En el cuadro de diálogo emergente, seleccione **Importación completa** y haga clic en **Aceptar**.
   3. Espere a que se complete la operación.

3. Compruebe los cambios de la regla de sincronización en un objeto User existente:

    El atributo de origen de la instancia local de Active Directory y UserType de Azure AD se han importado en el espacio del conector correspondiente. Antes de continuar con el paso de sincronización completa, se recomienda que realice una **vista previa** de un objeto User existente en el espacio local del conector de AD. El objeto que ha seleccionado debe tener el atributo de origen rellenado.
    
    Una **vista previa** correcta con el valor UserType rellenado en el metaverso es un buen indicador de que ha configurado las reglas de sincronización correctamente. Para más información acerca de cómo llevar a cabo una **vista previa**, consulte la sección [Comprobación del cambio](#verify-the-change).

4. Ejecute una **sincronización completa** en el **conector de AD local**:

   1. Haga clic con el botón derecho en **Conector de AD local** y seleccione **Ejecutar**.
   2. En el cuadro de diálogo emergente, seleccione **Sincronización completa** y haga clic en **Aceptar**.
   3. Espere a que se complete la operación.

5. Compruebe las **exportaciones pendientes** en Azure AD:

   1. Haga clic con el botón derecho en **Conector de Azure AD** y seleccione **Espacio del conector de búsqueda**.
   2. En el cuadro de diálogo emergente **Search Connector Space** (Buscar espacio conector):

      - Establezca el **ámbito** en **Pending Export** (Exportación pendiente).
      - Seleccione las tres casillas: **Agregar**, **Modificar** y **Eliminar**.
      - Haga clic en el botón **Buscar** para obtener la lista de objetos con los cambios que se exportarán. Para examinar los cambios de un determinado objeto, haga doble clic en el objeto.
      - Compruebe que los cambios sean los previstos.

6. Ejecute **Exportar** en el **conector de Azure AD**:

   1. Haga clic con el botón derecho en **Conector de Azure AD** y seleccione **Ejecutar**.
   2. En el cuadro de diálogo emergente **Run Connector** (Ejecutar conector), seleccione **Exportar** y haga clic en **Aceptar**.
   3. Espere hasta que la exportación a Azure AD se haya completado.

> [!NOTE]
> Estos pasos no incluyen los pasos de sincronización completa y exportación en el conector de Azure AD. Estos pasos no son necesarios, ya que los valores de atributo solo se envían desde la instancia local de Active Directory a Azure AD.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Paso 7: vuelva a habilitar el programador de sincronizaciones.
Vuelva a habilitar el programador de sincronización integrado:

1. Inicie una sesión de PowerShell.
2. Vuelva a habilitar la sincronización programada mediante la ejecución del cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $true`.


## <a name="next-steps"></a>pasos siguientes
* Obtenga más información sobre el modelo de configuración en el artículo de información sobre el [aprovisionamiento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Consulte más detalles sobre el lenguaje de expresiones en el artículo [Descripción de las expresiones de aprovisionamiento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Temas de introducción**

* [Sincronización de Azure AD Connect: comprender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
