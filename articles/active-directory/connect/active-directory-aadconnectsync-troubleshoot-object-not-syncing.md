---
title: "Solución de problemas de un objeto que no se ha sincronizado con Azure AD | Microsoft Docs"
description: "Solucione problemas de por qué un objeto no se sincroniza con Azure AD."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 491a920ceeaac62dd37b1def3f02234056aebfb0
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>Solución de problemas de un objeto que no se sincroniza con Azure AD

Si un objeto no se ha sincronizado como esperaba con Azure AD, puede ser debido a varias razones. Si ha recibido un mensaje de correo electrónico de error de Azure AD o si ve el error en Azure AD Connect Health, lea el artículo sobre cómo [solucionar errores de exportación](active-directory-aadconnect-troubleshoot-sync-errors.md). Sin embargo, si va a solucionar un problema en el que el objeto no está en Azure AD, este es el tema que tiene que leer. Describe cómo buscar errores en el componente local de sincronización de Azure AD Connect.

Para encontrar los errores, tendrá que examinar varios sitios en el orden siguiente:

1. Los [registros de operaciones](#operations) para buscar errores que ha identificado el motor de sincronización durante la importación y sincronización.
2. El [espacio del conector](#connector-space-object-properties) para buscar objetos que faltan y errores de sincronización.
3. El [metaverso](#metaverse-object-properties) para buscar problemas relacionados con los datos.

Inicie [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) antes de comenzar a realizar estos pasos.

## <a name="operations"></a>Operaciones
La pestaña de operaciones de Synchronization Service Manager es donde se deben comenzar la solucionar problemas. En la pestaña Operaciones se muestran los resultados de las operaciones más recientes.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/operations.png)  

La mitad superior muestra todas las ejecuciones en orden cronológico. De forma predeterminada, el registro de operaciones conservará información sobre los últimos 7 días, pero puede cambiar este parámetro con el [programador](active-directory-aadconnectsync-feature-scheduler.md). Debe buscar las ejecuciones que no muestran un estado de operación correcta. Para cambiar la ordenación, haga clic en los encabezados.

En la columna **Estado** se encuentra la información más importante, puesto que muestra el problema más grave de una ejecución. A continuación tiene un resumen rápido de los estados más comunes que debe analizar por orden de prioridad (donde * indica varias cadenas de error posibles).

| Estado | Comentario |
| --- | --- |
| stopped-* |No se ha podido completar la ejecución. Por ejemplo, si el sistema remoto está inactivo y no se puede conectar a él. |
| stopped-error-limit |Se han generado más de 5000 errores. La ejecución se ha detenido automáticamente debido al elevado número de errores. |
| completed-\*-errors |Se completa la ejecución, pero hay errores (menos de 5000) que deben investigarse. |
| completed-\*-warnings |La ejecución se ha completado, pero algunos datos no tienen el estado esperado. Si se producen errores, es posible que se trate únicamente de un síntoma. Le recomendamos que primero resuelva los errores y que luego investigue las advertencias. |
| Correcto |No hay ningún problema. |

Cuando seleccione una fila, la parte inferior se actualizará para mostrar los detalles de la ejecución. En el extremo izquierdo de la parte inferior, es posible que aparezca una lista con la información **Paso #**. Solo aparecerá si tiene varios dominios en el bosque; cada dominio estará representado por un paso. El nombre de dominio puede encontrarse bajo el encabezado **Partición**. En **Synchronization Statistics**(Estadísticas de sincronización) puede encontrar más información sobre el número de cambios que se han procesado. Puede hacer clic en los vínculos para obtener una lista de los objetos modificados. Si hay objetos con errores, estos se mostrarán en **Errores de sincronización**.

### <a name="troubleshoot-errors-in-operations-tab"></a>Solución de problemas en la pestaña Operaciones
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorsync.png)  
Cuando se producen errores, tanto el objeto del error como el propio error son vínculos que proporcionarán información adicional.

Empiece haciendo clic en la cadena de error (en la imagen anterior,**sync-rule-error-function-triggered** ). En primer lugar, aparecerá información general del objeto. Para ver el error real, haga clic en el botón **Seguimiento de la pila**. De esta forma, podrá consultar información de depuración del error.

Puede hacer clic con el botón derecho en la casilla **call stack information** (información de la pila de llamadas), hacer clic en **seleccionar todo** y, después, en **copiar**. A continuación, puede copiar la pila y buscar el error en el editor que prefiera, como el Bloc de notas.

* Si el error procede de **SyncRulesEngine**, la información de la pila de llamadas tendrá en primer lugar una lista de todos los atributos del objeto. Desplácese hacia abajo hasta que vea el encabezado **InnerException = >**.  
  ![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorinnerexception.png)  
  La línea posterior muestra el error. En la imagen anterior, el error procede de una regla de sincronización personalizada creada por Fabrikam.

Si el error no proporciona suficiente información, puede fijarse en los datos. Puede hacer clic en el vínculo con el identificador de objeto y continuar solucionando el problema con el [objeto importado del espacio conector](#cs-import).

## <a name="connector-space-object-properties"></a>Propiedades de objeto del espacio del conector
Si no encuentra errores en la pestaña [operaciones](#operations), el paso siguiente consiste en realizar un seguimiento del objeto del espacio de conector de Active Directory en el metaverso y en Azure AD. En esta ruta de acceso, debería encontrar dónde está el problema.

### <a name="search-for-an-object-in-the-cs"></a>Búsqueda de un objeto en el servidor de configuración

En **Synchronization Service Manager**, haga clic en **Conectores**, seleccione el conector de Active Directory y **Espacio del conector de búsqueda**.

En **Ámbito**, seleccione **RDN** (cuando quiera realizar una búsqueda en el atributo CN) o **DN o delimitador** (cuando desee buscar en el atributo distinguishedName). Escriba un valor y haga clic en **Buscar**.  
![Búsqueda del espacio conector](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearch.png)  

Si no encuentra el objeto que está buscando, podría haberse filtrado con un [filtrado basado en dominios](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) o uno [basado en la unidad organizativa](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering). Lea el tema sobre [configuración de filtrado](active-directory-aadconnectsync-configure-filtering.md) para comprobar que el filtrado se haya configurado según lo previsto.

Otra búsqueda útil consiste en seleccionar Azure AD Connector. En **Ámbito**, elija la opción de **importación pendiente**y active la casilla **Agregar**. Esta búsqueda proporciona todos los objetos sincronizados con Azure AD que no se pueden asociar con un objeto local.  
![Búsqueda de objetos huérfanos del espacio de conector](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearchorphan.png)  
Esos objetos lo han creado otro motor de sincronización o un motor de sincronización con una configuración de filtrado diferente. Esta vista es una lista de objetos **huérfanos** no administrados. Revise esta lista y considere la posibilidad de quitar estos objetos ejecutando los cmdlets de [PowerShell de Azure AD](http://aka.ms/aadposh).

### <a name="cs-import"></a>Importación del servidor de configuración
Al abrir un objeto cs, aparecen varias pestañas en la parte superior. La pestaña **Importación** muestra los datos que se almacenan provisionalmente después de una importación.  
![Objeto del servidor de configuración](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/csobject.png)    
En **Valor antiguo** se muestran los datos almacenados en Connect y en **Nuevo valor**, los que se han recibido desde el sistema de origen y no se han aplicado todavía. Si se produce un error en el objeto, los cambios no se procesan.

**Error**  
![Objeto del servidor de configuración](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssyncerror.png)  
La pestaña **Error de sincronización** solo está visible si se produce un problema con el objeto. Para obtener más información, consulte el artículo sobre cómo [solucionar errores de sincronización](#troubleshoot-errors-in-operations-tab).

### <a name="cs-lineage"></a>Linaje del servidor de configuración
La pestaña Linaje muestra cómo el objeto de espacio del conector está relacionado con el objeto de metaverso. Puede ver cuándo fue la última vez que el Conector importó un cambio desde el sistema conectado y qué reglas que se han aplicado para rellenar los datos en el metaverso.  
![Linaje del servidor de configuración](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineage.png)  
En la columna **Acción** vemos que hay una regla de sincronización **Entrante** con la acción **Aprovisionar**. Esto indica que, siempre que este objeto de espacio del conector esté presente, el objeto de metaverso permanecerá. En cambio, si la lista de reglas de sincronización muestra una regla de sincronización con la dirección **Saliente** y **Aprovisionar**, indica que este objeto se eliminará cuando se quite el objeto de metaverso.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineageout.png)  
También podemos ver en la columna **PasswordSync** que el espacio del conector entrante puede contribuir a los cambios realizados en la contraseña debido a que una regla de sincronización tiene el valor **True**. Esta contraseña se envía a Azure AD a través de la regla de salida.

Desde la pestaña Linaje, puede acceder al metaverso haciendo clic en [Metaverse Object Properties](#mv-attributes)(Propiedades del objeto de metaverso).

En la parte inferior de todas las pestañas, hay dos botones: **Vista previa** y **Registro**.

### <a name="preview"></a>Vista previa
La página de vista previa se usa para sincronizar un único objeto. Es útil si está solucionando problemas de algunas reglas de sincronización personalizadas del cliente y desea ver el efecto de un cambio en un único objeto. Puede seleccionar entre **Sincronización completa** y **Sincronización diferencial**. También puede elegir entre **Generate Preview** (Generar vista previa), que únicamente mantendrá el cambio en la memoria, y **Vista previa de confirmación**, que actualizó el metaverso y llevará a cabo todos los cambios en los espacios del conector de destino.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/preview.png)  
Puede inspeccionar el objeto y la regla a la que se aplica un flujo de atributo concreto.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/previewresult.png)

### <a name="log"></a>Registro
La página de registro se usa para ver el estado y el historial de la sincronización de contraseñas. Para obtener más información, consulte el artículo sobre la [sincronización de contraseñas](active-directory-aadconnectsync-troubleshoot-password-synchronization.md).

## <a name="metaverse-object-properties"></a>Propiedades de objetos del metaverso
Se recomienda iniciar la búsqueda desde el [espacio conector](#connector-space) de Active Directory de origen. Pero también puede iniciar la búsqueda en el metaverso.

### <a name="search-for-an-object-in-the-mv"></a>Búsqueda de un objeto en el metaverso
En **Synchronization Service Manager**, haga clic en **Búsqueda de metaverso**. Cree una consulta que sabrá que encontrará al usuario. Puede buscar atributos comunes, como accountName (sAMAccountName) y userPrincipalName. Para obtener más información, consulte [Búsqueda de metaverso](active-directory-aadconnectsync-service-manager-ui-mvsearch.md).
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvsearch.png)  

En la ventana **Resultados de la búsqueda**, haga clic en el objeto.

Si no se encuentra el objeto, significa que no ha alcanzado el metaverso. Continúe buscando el objeto en el [espacio conector](#connector-space-object-properties) de Active Directory. Podría haber un error de sincronización que impide que el objeto llegue al metaverso un filtro aplicado.

### <a name="mv-attributes"></a>Atributos del metaverso
En la pestaña Atributos puede ver los valores y qué conector los aportó.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvobject.png)  

Si un objeto no se está sincronizando, examine los atributos siguientes en el metaverso:
- ¿Está presente el atributo **cloudFiltered**y establecido en **True**? En caso afirmativo, significa que se ha filtrado según los pasos descritos en el [filtrado basado en atributos](active-directory-aadconnectsync-configure-filtering.md#attribute-based-filtering).
- ¿Está presente el atributo **sourceAnchor**? En caso negativo, ¿tiene una topología de bosque de cuenta-recurso? Si un objeto se identifica como un buzón vinculado (el atributo **msExchRecipientTypeDetails** tiene el valor 2), el valor de sourceAnchor lo proporcionará el bosque con una cuenta de Active Directory habilitada. Asegúrese de que la cuenta maestra se ha importado y sincronizado correctamente. La cuenta maestra debe aparecer en los [conectores](#mv-connectors) del objeto.

### <a name="mv-connectors"></a>Conectores del metaverso
La pestaña Conectores muestra todos los espacios del conector que tienen una representación del objeto.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvconnectors.png)  
Debe tener un conector para los siguientes elementos:

- Todos los bosques de Active Directory donde esté representado el usuario. Esta representación puede incluir foreignSecurityPrincipals y objetos de contacto.
- Un conector de Azure AD.

Si falta el conector en Azure AD, lea la sección de [atributos del metaverso](#MV-attributes) para comprobar los criterios de aprovisionamiento en Azure AD.

Esta pestaña también permite navegar al [objeto de espacio del conector](#connector-space-object-properties). Seleccione una fila y haga clic en **Propiedades**.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
