---
title: "Sincronización de Azure AD Connect: evitar eliminaciones accidentales | Microsoft Docs"
description: "En este tema se describe la característica para evitar eliminaciones accidentales en Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 57ce7b2fcece751b1386ef1d57762ad8d1c27c62
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Sincronización de Azure AD Connect: cómo evitar eliminaciones accidentales
En este tema se describe la característica para evitar eliminaciones accidentales en Azure AD Connect.

Al instalar Azure AD Connect, la característica para evitar eliminaciones accidentales se habilitará de forma predeterminada y se configurará para que no permita ninguna exportación con más de 500 eliminaciones. Esta característica está diseñada para protegerle de los cambios de configuración accidentales y de los cambios en el directorio local que afectarían a un gran número de usuarios y demás objetos.

## <a name="what-is-prevent-accidental-deletes"></a>Qué significa evitar eliminaciones accidentales
Algunos de los escenarios comunes en los que puede observar esto son los siguientes:

* Cambios en el [filtrado](active-directory-aadconnectsync-configure-filtering.md) donde se anula la selección de una [unidad organizativa](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) o un [dominio](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) completos.
* Se eliminan todos los objetos de una unidad organizativa.
* Se cambia el nombre de una unidad organizativa, así que se considera que todos los objetos están fuera del ámbito de la sincronización.

El valor predeterminado de 500 objetos puede cambiarse con PowerShell mediante `Enable-ADSyncExportDeletionThreshold`, que forma parte del módulo de sincronización de AD que se instala con Azure Active Directory Connect. Debe configurar este valor para ajustar el tamaño de su organización. Dado que el programador de sincronización se ejecutará cada 30 minutos, el valor es el número de eliminaciones que hemos visto en 30 minutos.

Si hay demasiadas eliminaciones almacenadas provisionalmente para exportarse a Azure AD, la exportación no continuará y recibirá un mensaje de correo electrónico similar al siguiente:

![Evitar eliminaciones accidentales del correo electrónico](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Hola (contacto técnico). A veces el servicio de sincronización de identidades detecta que el número de eliminaciones supera el umbral de eliminación configurado para (nombre de la organización). En esta sincronización de identidades, se envía un total de (número) objetos para su eliminación. Este número cumple o supera el valor del umbral de eliminación configurado de (número) objetos. Es necesario que confirme que estas eliminaciones deben procesarse para que podamos continuar. Para más detalles sobre el error que aparece en este mensaje de correo electrónico, consulte la información sobre la prevención de eliminaciones accidentales .*
>
> 

También puede ver el estado `stopped-deletion-threshold-exceeded` cuando observa la interfaz de usuario **Synchronization Service Manager** para el perfil de exportación.
![Evitar eliminaciones accidentales: Interfaz de usuario de Synchronization Service Manager](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

Si no es lo esperado, investigue y tome las medidas correctivas oportunas. Para ver los objetos que se van a eliminar, haga lo siguiente:

1. Inicie el **Servicio de sincronización** desde el menú Inicio.
2. Vaya a **Conectores**.
3. Seleccione el conector con el tipo **Azure Active Directory**.
4. A la derecha, en **Acciones**, seleccione **Espacio del conector de búsqueda**.
5. En la ventana emergente, en **Ámbito**, seleccione **Desconectado desde** y elija una hora en el pasado. Haga clic en **Buscar**. Esta página ofrece una vista de todos los objetos que se van a eliminar. Al hacer clic en cada elemento, puede obtener información adicional sobre el objeto. También puede hacer clic en **Valor de la columna** para agregar atributos adicionales para que sean visibles en la cuadrícula.

![Espacio del conector de búsqueda](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Si se desean todas las eliminaciones, haga lo siguiente:

1. Para recuperar el umbral de eliminación actual, ejecute el cmdlet de PowerShell `Get-ADSyncExportDeletionThreshold`. Proporcione una cuenta y una contraseña de administrador global de Azure AD. El valor predeterminado es 500.
2. Para deshabilitar temporalmente esta protección y permitir realizar estas eliminaciones, ejecute el cmdlet de PowerShell: `Disable-ADSyncExportDeletionThreshold`. Proporcione una cuenta y una contraseña de administrador global de Azure AD.
   ![Credenciales](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
3. Con el Conector de Azure Active Directory aún seleccionado, seleccione la acción **Ejecutar** y **Exportar**.
4. Para volver a habilitar la protección, ejecute el cmdlet de PowerShell: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Reemplace 500 con el valor observado al recuperar el umbral de eliminación actual. Proporcione una cuenta y una contraseña de administrador global de Azure AD.

## <a name="next-steps"></a>pasos siguientes
**Temas de introducción**

* [Sincronización de Azure AD Connect: comprender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
