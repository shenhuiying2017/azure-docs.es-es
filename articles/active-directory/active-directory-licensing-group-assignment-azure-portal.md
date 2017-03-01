---

title: "Asignación de licencias a un grupo en Azure Active Directory | Microsoft Docs"
description: "Cómo asignar licencias con las licencias basadas en grupos de Azure Active Directory"
services: active-directory
keywords: Licencias de Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 6a9cebafd1ad8f513bfab897970241f7b82b2a53
ms.openlocfilehash: cea745934c36297f5fe0867f4335a797a3ae2515
ms.lasthandoff: 02/22/2017


---

# <a name="assigning-licenses-to-a-group-in-azure-active-directory"></a>Asignación de licencias a un grupo en Azure Active Directory

En este artículo, vamos a recorrer un escenario básico de asignación de licencias de producto a un grupo y comprobar que todos los miembros del grupo tienen las licencias adecuadas.

En este ejemplo, el inquilino contiene un grupo de seguridad llamado **HR Department** (Departamento de recursos humanos), que incluye todos los miembros del departamento de recursos humanos que, en este caso, son aproximadamente 1000 usuarios. El administrador desea asignar licencias de Office 365 Enterprise E3 a todo el departamento; el servicio Yammer Enterprise que se incluye en el producto se debe deshabilitar temporalmente hasta que el departamento esté listo para empezar a usarlo. El administrador también desea implementar licencias de Enterprise Mobility + Security para el mismo grupo de usuarios.

## <a name="step-1-assign-the-required-licenses"></a>Paso 1: Asignación de las licencias necesarias

1. Inicie sesión en [**Azure Portal**](https://portal.azure.com) con una cuenta de administrador. Para administrar las licencias, la cuenta necesita el rol de administrador global o el rol de administrador de cuentas de usuario.

2. Seleccione **Más servicios** en el panel de navegación izquierdo y seleccione **Azure Active Directory**. Puede guardar esta hoja en "Favoritos"; haga clic en el icono de estrella o ánclela al panel del portal.

3. En la hoja **Azure Active Directory**, seleccione **Licencias**. Se abre una hoja donde puede ver y administrar todos los productos con licencia en el inquilino.

4. En **Todos los productos**, seleccione los nombres de producto de Office 365 Enterprise E3 y Enterprise Mobility + Security para seleccionarlos. Seleccione **Asignar** en la parte superior de la hoja para iniciar la asignación.

  ![todos los productos, asignar licencias](media/active-directory-licensing-group-assignment-azure-portal/all-products-assign.png)

5. En la hoja **Asignar licencias**, haga clic en **Usuarios y grupos** para abrir la hoja de usuarios y grupos. Busque el nombre del grupo *HR Department* (Departamento de recursos humanos), seleccione el grupo y, a continuación, asegúrese de confirmar; para ello, haga clic **Seleccionar** en la parte inferior de la hoja.

  ![Selección de un grupo](media/active-directory-licensing-group-assignment-azure-portal/select-a-group.png)

6. En la hoja **Asignar licencias**, haga clic en **Opciones de asignación (opcionales)** que muestra todos los planes de servicio que incluyen los dos productos que hemos seleccionado anteriormente. Busque Yammer Enterprise y establézcalo en **Desactivar** para deshabilitar el servicio de la licencia de producto. Para confirmar, haga clic en **Aceptar** en la parte inferior de **Opciones de asignación**.

  ![opciones de asignación](media/active-directory-licensing-group-assignment-azure-portal/assignment-options.png)

7. Por último, en la hoja **Asignar licencias**, haga clic en **Asignar** en la parte inferior de la hoja para completar la asignación.

8. Se muestra una notificación en la esquina superior derecha con el estado y el resultado del proceso. Si no se pudo completar la asignación al grupo (por ejemplo, debido a que ya existían licencias en el grupo), haga clic en la notificación para ver los detalles del error.

Ahora, hemos especificado una plantilla de licencia en el grupo del departamento de recursos humanos. Se inició un proceso en segundo plano en Azure AD para procesar todos los miembros existentes de ese grupo. Esta operación inicial podría tardar algún tiempo, según el tamaño actual del grupo. En el paso siguiente, vamos a describir cómo comprobar si el proceso se ha completado y si es necesario hace algo más para resolver los problemas.

> [!NOTE]
> La misma asignación se puede iniciar desde una ubicación alternativa: **Usuarios y grupos** en Azure AD. Vaya a **Azure Active Directory &gt; Usuarios y grupos &gt; Todos los grupos,**; busque el grupo, selecciónelo y vaya a la pestaña **Licencias**. El botón **Asignar** en la parte superior de la hoja, abrirá la hoja de asignación de licencias.

## <a name="step-2-verify-that-the-initial-assignment-has-completed"></a>Paso 2: Comprobación de que se ha completado la asignación inicial

1. Vaya a **Azure Active Directory &gt; Usuarios y grupos &gt; Todos los grupos**; busque el grupo *HR Department* al que se van a asignar las licencias.

2. En la hoja del grupo *HR Department*, seleccione **Licencias** para confirmar rápidamente si las licencias se han asignado completamente a los usuarios y si hay errores que requieran atención, entre otros:

  - Licencias de producto que se han asignado al grupo. Seleccione una entrada para mostrar los servicios específicos que se han habilitado y para realizar cambios.

  - Estado de los últimos cambios realizados en la asignación de licencias: si los cambios se están procesando o si se ha completado el procesamiento en todos los usuarios miembros.

  - Si hubo errores, información acerca de los usuarios en estado de error para los que no se pudieron asignar las licencias.

  ![opciones de asignación](media/active-directory-licensing-group-assignment-azure-portal/assignment-errors.png)

3. Encontrará información más detallada sobre el procesamiento de licencias en **Azure Active Directory&gt; Usuarios y grupos &gt;
     *nombre de grupo* &gt; Registros de auditoría**:

  - Actividad: **Empezar a aplicar licencias basadas en grupo a los usuarios**. Se registra cuando el sistema recoge el cambio de asignación de licencias en el grupo y empieza a aplicarlo a todos los usuarios miembros. Contiene información sobre el cambio que se realizó.

  - Actividad: **Empezar a aplicar las licencias basadas en grupo a los usuarios**. Se registra cuando el sistema termina de procesar todos los usuarios del grupo. Contiene un resumen de cuántos usuarios se han procesado correctamente y el número de usuarios a los que no se pudieron asignar las licencias de grupo.

## <a name="step-3-checking-for-license-problems-and-resolving-them"></a>Paso 3: Comprobación y resolución de problemas de licencias

1. Vaya a **Azure Active Directory &gt; Usuarios y grupos &gt; Todos los grupos**; busque el grupo *HR Department* al que se van a asignar las licencias.

2. En la hoja del grupo **HR Department**, seleccione **Licencias**. La notificación en la parte superior de la hoja significa que hay 10 usuarios a los que no se pudieron asignar licencias. Se abre una lista con todos los usuarios en estado de error de licencia para este grupo.

3. La columna **Asignaciones erróneas** indica que no se pudieron asignar ambas licencias de producto a los usuarios. **Motivo principal del error** indica la causa del error, en este caso, **Planes del servicio en conflicto**.

  ![asignaciones erróneas](media/active-directory-licensing-group-assignment-azure-portal/failed-assignments.png)

4. Seleccione un usuario para abrir la hoja **Licencias** con todas las licencias asignadas actualmente al usuario. En este ejemplo, podemos ver que el usuario ha heredado la licencia de Office 365 Enterprise E1 del grupo **Kiosk users**. Esto entra en conflicto con la licencia de E3 que el sistema intenta aplicar desde el grupo **HR Department**; por tanto, no se asigna ninguna de las licencias de dicho grupo al usuario.

  ![ver las licencias de un usuario](media/active-directory-licensing-group-assignment-azure-portal/user-license-view.png)

5. Para resolver este problema, quitamos el usuario del grupo **Kiosk users**. Una vez que Azure AD procesa el cambio, las licencias de **HR Department** se asignan correctamente.

  ![licencia asignada correctamente](media/active-directory-licensing-group-assignment-azure-portal/license-correctly-assigned.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el conjunto de características de administración de licencias a través de grupos, vea

* [¿En qué consisten las licencias basadas en grupos de Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Identificación y resolución de problemas de licencias de un grupo en Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Migración de usuarios individuales con licencia a licencias basadas en grupos en Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Escenarios adicionales de licencias basadas en grupos de Azure Active Directory](active-directory-licensing-group-advanced.md)

