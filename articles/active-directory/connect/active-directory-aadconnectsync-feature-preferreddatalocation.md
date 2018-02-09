---
title: "Sincronización de Azure AD Connect: Configuración de la ubicación de datos preferida para las capacidades multigeográficas de Office 365 | Microsoft Docs"
description: "Describe cómo poner los recursos de usuario de Office 365 cerca del usuario con la sincronización de Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: billmath
ms.openlocfilehash: 8a36fc45334a2f1d12e6eabbfb16731ccc9998bf
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="azure-ad-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Sincronización de Azure AD Connect: Configuración de la ubicación de datos preferida para los recursos de Office 365
El objetivo de este tema es guiarle en la configuración de PreferredDataLocation en la sincronización de Azure AD Connect. Cuando un cliente usa las capacidades multigeográficas de Office 365, ese atributo se usa para designar la ubicación geográfica de los datos de Office 365 del usuario.

> [!IMPORTANT]
> Actualmente, las capacidades multigeográficas se encuentran en versión preliminar. Si desea participar en el programa de versión preliminar, póngase en contacto con su representante de Microsoft.
>
>

## <a name="enable-synchronization-of-preferreddatalocation"></a>Habilitar la sincronización de PreferredDataLocation
De forma predeterminada, los recursos de Office 365 para sus usuarios se encuentran en la misma región que el inquilino de Azure AD. Por ejemplo, si el inquilino se encuentra en Estados Unidos, los buzones de Exchange de los usuarios también se encontrarán allí. Esto podría no ser adecuado para una organización multinacional. Puede definirse la región del usuario si se establece el atributo preferredDataLocation.

Con la configuración de este atributo, puede tener los recursos del usuario de Office 365, como el buzón de correo y OneDrive, en la misma región que el usuario y, aún así, tener un inquilino para toda la organización.

> [!IMPORTANT]
> Para poder optar a las capacidades multigeográficas, debe disponer de al menos 5000 puestos en la suscripción de Office 365.
>
>

Las regiones de Office 365 disponibles para las capacidades multigeográficas son:

| Region | DESCRIPCIÓN |
| --- | --- |
| NAM | Norteamérica |
| EUR | Europa |
| APC | Asia Pacífico |
| JPN | Japón |
| AUS | Australia |
| CAN | Canadá |
| GBR | Gran Bretaña |
| LAM | América Latina |

No todas las cargas de trabajo de Office 365 admiten la configuración de una región del usuario.

Azure AD Connect admite la sincronización del atributo **PreferredDataLocation** para objetos **User** en la versión 1.1.524.0 y posteriores. Más concretamente, se introdujeron los cambios siguientes:

* El esquema del tipo de objeto **User** de Conector de Azure AD se extiende para incluir el atributo PreferredDataLocation, que es de tipo cadena de un solo valor.
* El esquema del tipo de objeto **Person** del metaverso se extiende para incluir el atributo PreferredDataLocation, que es de tipo cadena y tiene un solo valor.

De forma predeterminada, el atributo PreferredDataLocation no está habilitado para la sincronización. Esta característica está pensada para organizaciones grandes y no todos se pueden beneficiar de ella. También debe identificar un atributo para que contenga la región de Office 365 para los usuarios ya que no hay ningún atributo PreferredDataLocation en la instancia local de Active Directory. Esto será diferente para cada organización.

> [!IMPORTANT]
> Actualmente, Azure AD permite que el atributo PreferredDataLocation de los objetos User sincronizados y en la nube se configure directamente con Azure AD PowerShell. Una vez habilitada la sincronización del atributo PreferredDataLocation, debe detener el uso de Azure AD PowerShell para configurar el atributo en **objetos User sincronizados** porque Azure AD Connect los reemplazará según los valores de atributo de origen en la instancia local de Active Directory.

> [!IMPORTANT]
> Desde el 1 de septiembre de 2017, Azure AD ya no permite que el atributo PreferredDataLocation de los **objetos User sincronizados** se configure directamente con Azure AD PowerShell. Para configurar el atributo PreferredLocation de los objetos User sincronizados, debe usar Azure AD Connect.

Antes de habilitar la sincronización del atributo PreferredDataLocation:

* En primer lugar, debe decidir qué atributo local de Active Directory se usará como atributo de origen. Debería ser de tipo **cadena de un solo valor**. En los pasos siguientes se usa uno de los extensionAttributes.
* Si ha configurado previamente el atributo PreferredDataLocation en objetos User sincronizados existentes de Azure AD con Azure AD PowerShell, deberá **restituir** los valores de atributo a los objetos User correspondientes de la instancia local de Active Directory.

    > [!IMPORTANT]
    > Si no restituye los valores de atributo a los objetos User correspondientes de la instancia local de Active Directory, Azure AD Connect quitará los valores de atributo existentes en Azure AD cuando se habilite la sincronización para el atributo PreferredDataLocation.

* Se recomienda configurar el atributo de origen en al menos un par de objetos User locales de AD ahora para que puedan usarse para la comprobación más tarde.

Los pasos para habilitar la sincronización del atributo PreferredDataLocation se pueden resumir en:

1. Deshabilitar el programador de sincronización y comprobar que no hay ninguna sincronización en curso
2. Agregar el atributo de origen al esquema de conector de ADDS local
3. Agregar PreferredDataLocation al esquema de conector de Azure AD
4. Crear una regla de sincronización de entrada para enviar el valor del atributo desde la instancia local de Active Directory
5. Crear una regla de sincronización de salida para enviar el valor del atributo a Azure AD
6. Iniciar el ciclo de sincronización completo
7. Habilitar el programador de sincronización
8. Comprobar el resultado

> [!NOTE]
> El resto de esta sección trata estos pasos detalladamente. Se describen en el contexto de una implementación de Azure AD con topología de bosque único y sin reglas de sincronización personalizadas. Si tiene una topología de varios bosques, reglas de sincronización personalizadas configuradas o un servidor de ensayo, debe ajustar los pasos según corresponda.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Paso 1: Deshabilitar el programador de sincronización y comprobar que no hay ninguna sincronización en curso
Asegúrese de que no realiza ninguna sincronización mientras está actualizando reglas de sincronización para evitar que se exporten cambios no deseados a Azure AD. Para deshabilitar el programador de sincronización integrado:

1. Inicie una sesión de PowerShell en el servidor de Azure AD Connect.
2. Deshabilite la sincronización programada mediante la ejecución del cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Inicie **Synchronization Service Manager**. Para ello, vaya a **INICIO** > **Servicio de sincronización**.
4. Vaya a la pestaña **Operaciones** y confirme que no hay ninguna operación cuyo estado sea *en curso*.

![Synchronization Service Manager: comprobar que no hay ninguna operación en curso](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema"></a>Paso 2: Agregar el atributo de origen al esquema de conector de ADDS local
No todos los atributos de AD se importan en el espacio de Conector AD local. Si ha seleccionado utilizar un atributo no sincronizado de forma predeterminada, deberá importarlo. Para agregar el atributo de origen a la lista de atributos importados:

1. Vaya a la pestaña **Connectors** (Conectores) de Synchronization Service Manager.
2. Haga clic con el botón derecho en **Conector de AD local** y seleccione **Propiedades**.
3. En el cuadro de diálogo emergente, vaya a la pestaña **Seleccionar atributos**.
4. Asegúrese de que el atributo de origen que seleccionó está activado en la lista de atributos. Si no ve el atributo, haga clic en la casilla "Mostrar todos".
5. Haga clic en **Aceptar** para guardarlo.

![Agregar el atributo de origen al esquema de Conector AD local](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Paso 3: Agregar PreferredDataLocation al esquema de conector de Azure AD
De forma predeterminada, el atributo PreferredDataLocation no se importa en el espacio del conector de Azure AD. Para agregar el atributo PreferredDataLocation a la lista de atributos importados:

1. Vaya a la pestaña **Connectors** (Conectores) de Synchronization Service Manager.
2. Haga clic con el botón derecho en **Conector de Azure AD** y seleccione **Propiedades**.
3. En el cuadro de diálogo emergente, vaya a la pestaña **Seleccionar atributos**.
4. Seleccione el atributo preferredDataLocation en la lista de atributos.
5. Haga clic en **Aceptar** para guardarlo.

![Agregar el atributo de origen al esquema de Conector Azure AD](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Paso 4: Crear una regla de sincronización de entrada para enviar el valor del atributo desde la instancia local de Active Directory
La regla de sincronización de entrada permite enviar el valor del atributo desde el atributo de origen de la instancia local de Active Directory al metaverso:

1. Inicie el **Editor de reglas de sincronización**. Para ello, vaya a **INICIO** > **Editor de reglas de sincronización**.
2. Establezca el filtro de búsqueda **Dirección** como **Entrada**.
3. Haga clic en el botón **Agregar nueva regla** para crear una nueva regla de entrada.
4. En la pestaña **Descripción**, proporcione la configuración siguiente:

    | Atributo | Valor | Detalles |
    | --- | --- | --- |
    | NOMBRE | *Proporcione un nombre*. | Por ejemplo, *"In from AD – User PreferredDataLocation"* |
    | DESCRIPCIÓN | *Proporcione una descripción personalizada* |  |
    | Sistema conectado | *Elija Conector AD local* |  |
    | Tipo de objeto de sistema conectado | **User** |  |
    | Propiedades de objeto de metaverso | **Person** |  |
    | Tipo de vínculo | **Join** |  |
    | Prioridad | *Elija un número entre 1 y 99* | El intervalo de 1 a 99 se reserva para las reglas de sincronización personalizadas. No elija ningún valor que use otra regla de sincronización. |

5. Mantenga el **Filtro de ámbito** vacío para incluir todos los objetos. Puede que necesite retocar el filtro de ámbito en función de su implementación de Azure AD Connect.
6. Vaya a la pestaña **Transformación** e implemente la siguiente regla de transformación:

    | Tipo de flujo | Atributo de destino | Origen | Aplicar una vez | Tipo de combinación |
    | --- | --- | --- | --- | --- |
    |Directo | PreferredDataLocation | Elegir el atributo de origen | No activado | Actualizar |

7. Haga clic en **Agregar** para crear la regla de entrada.

![Crear regla de sincronización de entrada](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Paso 5: Crear una regla de sincronización de salida para enviar el valor de atributo a Azure AD
La regla de sincronización de salida permite enviar el valor del atributo desde el metaverso al atributo PreferredDataLocation en Azure AD:

1. Vaya al Editor de **Reglas de sincronización**.
2. Establezca el filtro de búsqueda **Dirección** como **Salida**.
3. Haga clic en el botón **Agregar nueva regla**.
4. En la pestaña **Descripción**, proporcione la configuración siguiente:

    | Atributo | Valor | Detalles |
    | ----- | ------ | --- |
    | NOMBRE | *Proporcione un nombre*. | Por ejemplo, "Out to AAD – User PreferredDataLocation". |
    | DESCRIPCIÓN | *Proporcione una descripción* ||
    | Sistema conectado | *Seleccione el conector AAD* ||
    | Tipo de objeto de sistema conectado | Usuario ||
    | Propiedades de objeto de metaverso | **Person** ||
    | Tipo de vínculo | **Join** ||
    | Prioridad | *Elija un número entre 1 y 99* | El intervalo de 1 a 99 se reserva para las reglas de sincronización personalizadas. No elija ningún valor que use otra regla de sincronización. |

5. Vaya a la pestaña **Filtro de ámbito** y agregue un **solo grupo de filtro de ámbito con dos cláusulas**:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Usuario |
    | cloudMastered | NOTEQUAL | True |

    El filtro de ámbito determina a qué objetos de Azure AD se aplica esta regla de sincronización de salida. En este ejemplo, se utiliza el mismo filtro de ámbito de la regla de sincronización de OOB "Out to AD – User Identity". Impide que la regla de sincronización se aplique a los objetos User que no se sincronizan desde la instancia local de Active Directory. Puede que necesite retocar el filtro de ámbito en función de su implementación de Azure AD Connect.

6. Vaya a la pestaña **Transformación** e implemente la siguiente regla de transformación:

    | Tipo de flujo | Atributo de destino | Origen | Aplicar una vez | Tipo de combinación |
    | --- | --- | --- | --- | --- |
    | Directo | PreferredDataLocation | PreferredDataLocation | No activado | Actualizar |

7. Haga clic en **Agregar** para crear la regla de salida.

![Crear regla de sincronización de salida](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>Paso 6: Ejecutar el ciclo de sincronización completo
En general, se requiere el ciclo de sincronización completo porque hemos agregado nuevos atributos a los esquemas de los Conectores Azure AD y AD y hemos introducido reglas de sincronización personalizadas. Se recomienda que compruebe los cambios antes de exportarlos a Azure AD. Puede usar los pasos siguientes para comprobar los cambios mientras sigue los pasos que forman un ciclo de sincronización completo manualmente.

1. Ejecute el paso **Importación completa** en **on-premises AD Connector** (Conector AD local):

   1. Vaya a la pestaña **Operations** (Operaciones) de Synchronization Service Manager.
   2. Haga clic con el botón derecho en **Conector de AD local** y seleccione **Ejecutar...**.
   3. En el cuadro de diálogo emergente, seleccione **Importación completa** y haga clic en **Aceptar**.
   4. Espere a que la operación finalice.

    > [!NOTE]
    > Puede omitir la importación completa en Conector AD local si el atributo de origen ya está incluido en la lista de atributos importados. En otras palabras, no tuvo que realizar ningún cambio durante el [Paso 2: Agregar el atributo de origen al esquema de Conector AD local](#step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema).

2. Ejecute el paso **Importación completa** en **Azure AD Connector** (Conector Azure AD):

   1. Haga clic con el botón derecho en **Azure AD Connector** (Conector Azure AD) y seleccione **Ejecutar...**
   2. En el cuadro de diálogo emergente, seleccione **Importación completa** y haga clic en **Aceptar**.
   3. Espere a que la operación finalice.

3. Compruebe los cambios de la regla de sincronización en un objeto User existente:

El atributo de origen de la instancia local de Active Directory y PreferredDataLocation de Azure AD se han importado en el espacio del conector correspondiente. Antes de continuar con el paso Sincronización completa, se recomienda que realice una **versión preliminar** de un objeto User existente en el espacio del conector de AD local. El objeto que ha seleccionado debe tener el atributo de origen rellenado. Una **vista previa** correcta con el valor PreferredDataLocation rellenado en el metaverso es un buen indicador de que ha configurado las reglas de sincronización correctamente. Para obtener información acerca de cómo llevar a cabo una **vista previa**, consulte la sección [Comprobar el cambio](active-directory-aadconnectsync-change-the-configuration.md#verify-the-change).

4. Realice el paso **Sincronización completa** en **on-premises AD Connector** (Conector AD local):

   1. Haga clic con el botón derecho en **Conector de AD local** y seleccione **Ejecutar...**.
   2. En el cuadro de diálogo emergente, seleccione **Sincronización completa** y haga clic en **Aceptar**.
   3. Espere a que la operación finalice.

5. Compruebe las **exportaciones pendientes** en Azure AD:

   1. Haga clic con el botón derecho en **Conector de Azure AD** y seleccione **Espacio del conector de búsqueda**.
   2. En el cuadro de diálogo emergente Search Connector Space (Espacio del conector de búsqueda):

      1. Establezca el **ámbito** en **Pending Export** (Exportación pendiente).
      2. Active las tres casillas, incluidas **Agregar, Modificar y Eliminar**.
      3. Haga clic en el botón **Buscar** para obtener la lista de objetos con los cambios que se exportarán. Para examinar los cambios de un determinado objeto, haga doble clic en el objeto.
      4. Compruebe que los cambios estén previstos.

6. Ejecute el paso **Exportación** en **Azure AD Connector** (Conector Azure AD):

   1. Haga clic con el botón derecho en **Conector de Azure AD** y seleccione **Ejecutar...**.
   2. En el cuadro de diálogo emergente Run Connector (Ejecutar conector), seleccione el paso **Exportar** y haga clic en **Aceptar**.
   3. Espere a que la exportación a Azure AD se complete.

> [!NOTE]
> Puede que observe que no se incluye el paso de Sincronización completa en el conector de Azure AD y Exportar en el conector de AD. Estos pasos no son necesarios, ya que los valores de atributo solo se envían desde la instancia local de Active Directory a Azure AD.

## <a name="step-7-re-enable-sync-scheduler"></a>Paso 7: Volver a habilitar el programador de sincronización
Vuelva a habilitar el programador de sincronización integrado:

1. Inicie la sesión de PowerShell.
2. Vuelva a habilitar la sincronización programada mediante la ejecución del cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>Paso 8: Comprobar el resultado
Ahora es el momento de comprobar la configuración y habilitarla para los usuarios.

1. Agregue la región al atributo seleccionado en un usuario. La lista de regiones disponibles se puede encontrar en [esta tabla](#enable-synchronization-of-preferreddatalocation).  
![Atributo de AD agregado a un usuario](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Espere a que el atributo se sincronice con Azure AD.
3. Con Exchange Online PowerShell, compruebe que la región del buzón de correo se ha configurado correctamente.  
![Región del buzón de correo configurada en un usuario de Exchange Online](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Siempre y cuando se haya marcado el inquilino para que pueda usar esta característica, el buzón de correo se moverá a la región correcta. Esto se puede comprobar examinando el nombre del servidor donde se encuentra el buzón.
4. Para comprobar que esta configuración ha sido efectiva sobre muchos buzones, utilice el script de la [galería de Technet](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e). Este script también tiene una lista de todos los prefijos de servidor de los centros de datos de Office 365 y la región en la que se encuentran. Se puede utilizar como una referencia en el paso anterior para comprobar la ubicación del buzón de correo.

## <a name="next-steps"></a>pasos siguientes

**Más información sobre las capacidades multigeográficas de Office 365:**

* Sesiones sobre capacidades multigeográficas en Ignite: https://aka.ms/MultiGeoIgnite
* Capacidades multigeográficas en OneDrive: https://aka.ms/OneDriveMultiGeo
* Capacidades multigeográficas en SharePoint Online: https://aka.ms/SharePointMultiGeo

**Más información sobre el modelo de configuración del motor de sincronización:**

* Obtenga más información sobre el modelo de configuración en el artículo de información sobre el [aprovisionamiento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Consulte más detalles sobre el lenguaje de expresiones en el artículo [Descripción de las expresiones de aprovisionamiento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Temas de introducción**

* [Sincronización de Azure AD Connect: comprender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
