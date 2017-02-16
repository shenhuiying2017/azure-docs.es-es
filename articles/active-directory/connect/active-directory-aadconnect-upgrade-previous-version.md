---
title: "Azure AD Connect: actualización desde una versión anterior | Microsoft Docs"
description: "Se explican los diferentes métodos para actualizar a la versión más reciente de Azure Active Directory Connect, como la actualización local y la migración oscilante."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 11/01/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 28b5da6098316f8fbe84966e0dac88f5b7d2cb1d
ms.openlocfilehash: d8818035349c8e78c1f2bb39a05e1afb2c854cc9


---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: actualización de una versión anterior a la versión más reciente
En este tema se describen los distintos métodos que puede utilizar para actualizar la instalación de Azure AD Connect a la versión más reciente. Le recomendamos mantenerse al día con las versiones de Azure AD Connect. Los pasos descritos en la [migración oscilante](#swing-migration) también se utilizan al realizar un cambio de configuración considerable.

Si quiere actualizar desde DirSync, consulte en su lugar [Azure AD Connect: actualización de Windows Azure Active Directory Sync (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

Hay algunas estrategias distintas para actualizar Azure AD Connect.

| Método | Description |
| --- | --- |
| [Actualización automática](active-directory-aadconnect-feature-automatic-upgrade.md) |Para clientes con una instalación rápida, este es el método más fácil. |
| [Actualización local](#in-place-upgrade) |Si tiene un solo servidor, actualice la instalación en contexto en el mismo servidor. |
| [Migración oscilante](#swing-migration) |Con dos servidores, puede preparar uno de ellos con la nueva versión o configuración y cambiar el servidor activo cuando esté listo. |

Para conocer los permisos necesarios, consulte los [permisos necesarios para la actualización](active-directory-aadconnect-accounts-permissions.md#upgrade).

## <a name="in-place-upgrade"></a>Actualización local
Una actualización local sirve para migrar de Azure AD Sync o Azure AD Connect. No sirve para DirSync ni para una solución con FIM + el Conector de Azure AD.

Este método es adecuado cuando tiene un único servidor y menos de unos 100 000 objetos. Si hay cambios en las reglas de sincronización de fábrica, después de la actualización, se producen una importación y una sincronización completas. De esta manera se garantiza que la nueva configuración se aplica a todos los objetos existentes en el sistema. Esta operación puede tardar unas horas según el número de objetos en el ámbito del motor de sincronización. La sincronización delta normal programada (cada 30 minutos de forma predeterminada) se suspende pero continúa la sincronización de contraseñas. Puede plantearse la posibilidad de realizar la actualización local durante un fin de semana. Si no hay ningún cambio en la configuración de fábrica con la nueva versión de Azure AD Connect, se iniciará una importación y sincronización delta normales.  
![Actualización local](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Si ha realizado cambios en las reglas de sincronización listas para usar, estas volverán a la configuración predeterminada tras la actualización. Para asegurarse de que la configuración se mantenga entre una actualización y otra, compruebe que los cambios se realizan como se describe en [Azure AD Connect Sync: procedimientos recomendados de cambio de la configuración predeterminada](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

## <a name="swing-migration"></a>Migración oscilante
Si tiene una implementación compleja o muchos objetos, puede que realizar una actualización local en el sistema activo no resulte práctico. Esta operación podría tardar varios días para algunos clientes y durante este tiempo no se procesará ningún cambio delta. Este método también se utiliza cuando planea realizar cambios sustanciales en la configuración y desea probarlos antes de que se inserten en la nube.

El método recomendado para estos escenarios es usar una migración oscilante. Necesitará (al menos) dos servidores, uno activo y otro de ensayo. El servidor activo (líneas azules continuas en la siguiente imagen) es responsable de la carga de producción activa. El servidor de ensayo (líneas púrpuras discontinuas en la siguiente imagen) está preparado con la nueva versión o configuración y, cuando esté completamente listo, se pondrá en activo. El servidor activo anterior, ahora con la versión o configuración anterior instalada, es el servidor de ensayo y se actualiza.

Los dos servidores pueden usar versiones diferentes. Por ejemplo, el servidor activo que tiene previsto retirar puede utilizar Sincronización de Azure AD y el nuevo servidor de ensayo puede usar Azure AD Connect. Si usa la migración oscilante para desarrollar una nueva configuración, es una buena idea tener las mismas versiones en los dos servidores.  
![Servidor provisional](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

Nota: Se ha observado que algunos clientes prefieren tener tres o cuatro servidores para este escenario. Cuando el servidor de ensayo se está actualizando, no tendrá un servidor de copia de seguridad para el caso de una [recuperación ante desastres](active-directory-aadconnectsync-operations.md#disaster-recovery). Con tres o cuatro servidores, se puede preparar un conjunto de servidores primarios o en espera con la nueva versión y asegurarse de que siempre hay un servidor de ensayo preparado para tomar el control.

Estos pasos también sirven para pasar de Azure AD Sync o de una solución con FIM + Conector de Azure AD. Estos pasos no sirven para DirSync, pero el mismo método de migración oscilante (también llamada implementación paralela) con los pasos para DirSync se pueden encontrar en [Azure AD Connect: actualización de Azure Active Directory Sync (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="swing-migration-steps"></a>Pasos de la migración oscilante
1. Si utiliza Azure AD Connect en ambos servidores y planea realizar solo un cambio de configuración, asegúrese de que tanto el servidor activo como el servidor de ensayo estén utilizando la misma versión. Esto facilitará la posterior comparación para detectar diferencias. Si va a actualizar desde Sincronización de Azure AD, estos servidores tienen diferentes versiones. Si está actualizando desde una versión anterior de Azure AD Connect, es una buena idea comenzar con los dos servidores con la misma versión, pero no es necesario.
2. Si ha realizado una configuración personalizada y su servidor provisional no la tiene, siga los pasos que se indican en [Traslado de la configuración personalizada del servidor activo al servidor provisional](#move-custom-configuration-from-active-to-staging-server).
3. Si está actualizando desde una versión anterior de Azure AD Connect, actualice el servidor provisional a la versión más reciente. Si va a mover desde Sincronización de Azure AD, instale Azure AD Connect en el servidor provisional.
4. Permita que el motor de sincronización ejecute la importación y la sincronización completas en el servidor provisional.
5. Compruebe que la nueva configuración no ha provocado ningún cambio inesperado usando los pasos que se indican en **Verificar** en [Comprobación de la configuración de un servidor](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Si algo no está como se esperaba, corríjalo, ejecute la importación y la sincronización y compruebe hasta que los datos sean correctos. Estos pasos se pueden encontrar en el tema vinculado.
6. Cambie el servidor de ensayo para que sea el servidor activo. Este es el paso final: **Cambio de servidor activo** en [Comprobación de la configuración de un servidor](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Si va a actualizar Azure AD Connect, actualice el servidor que está actualmente en modo provisional a la versión más reciente. Siga los mismos pasos que antes para actualizar los datos y la configuración. Si va a actualizar desde Azure AD Sync, puede desactivar y retirar el servidor anterior.

### <a name="move-custom-configuration-from-active-to-staging-server"></a>Traslado de la configuración personalizada del servidor activo al servidor provisional
Si ha realizado cambios en la configuración del servidor activo, debe asegurarse de que se aplican los mismos cambios en el servidor de ensayo.

Las reglas de sincronización personalizadas que haya creado se pueden mover con PowerShell. Otros cambios se deben aplicar de la misma forma en ambos sistemas y no se pueden migrar.

Debe asegurarse de que la configuración sea la misma en ambos servidores:

* Conexión a los mismos bosques.
* Filtrado por dominio y unidad organizativa.
* Mismas características opcionales, como la sincronización de contraseña y la escritura diferida de contraseñas.

**Migración de las reglas de sincronización**  
 Para mover una regla de sincronización personalizada, haga lo siguiente:

1. Abra el **Editor de reglas de sincronización** en el servidor activo.
2. Seleccione la regla personalizada. Haga clic en **Exportar**. Se abre una ventana del Bloc de notas. Guarde el archivo temporal con una extensión PS1. Lo convierte en un script de PowerShell. Copie el archivo ps1 en el servidor provisional.  
   ![Exportación de reglas de sincronización](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. El GUID del conector será diferente en el servidor de ensayo y debe cambiarse. Para obtener el GUID, inicie el **Editor de reglas de sincronización**, seleccione una de las reglas listas para usar que representa el mismo sistema conectado y haga clic en **Exportar**. Reemplace el GUID del archivo PS1 por el GUID del servidor de ensayo.
4. En un símbolo del sistema de PowerShell, ejecute el archivo PS1. Esta acción creará la regla de sincronización personalizada en el servidor de ensayo.
5. Si tiene varias reglas personalizadas, repita lo mismo con todas ellas.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).




<!--HONumber=Dec16_HO3-->


