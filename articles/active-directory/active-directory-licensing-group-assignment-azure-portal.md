---
title: "Asignación de licencias a un grupo en Azure Active Directory | Microsoft Docs"
description: "Procedimiento de asignación de licencias a los usuarios con las licencias de grupos de Azure Active Directory"
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
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42b18eab9cb419e6ada72ba72dc8be8d7f7b2eed
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Asignación de licencias a usuarios según su pertenencia a un grupo en Azure Active Directory

Este artículo le guiará a través del proceso de asignación de licencias de producto a un grupo de usuarios en Azure Active Directory (Azure AD) y, a continuación, comprobará que las licencias se han asignado correctamente.

En este ejemplo, el inquilino contiene un grupo de seguridad llamado **HR Department**. Este grupo incluye a todos los miembros del departamento de recursos humanos (en este caso, alrededor de 1000 usuarios). Desea asignar licencias de Office 365 Enterprise E3 a todo el departamento. El servicio Yammer Enterprise que se incluye en el producto se debe deshabilitar temporalmente hasta que el departamento esté listo para empezar a usarlo. También desea implementar licencias de Enterprise Mobility + Security para el mismo grupo de usuarios.

> [!NOTE]
> Algunos servicios de Microsoft no están disponibles en todas las ubicaciones. Para poder asignar una licencia a un usuario, el administrador tiene que especificar la propiedad Ubicación de uso en el usuario.

> En el caso de la asignación de licencias de grupo, cualquier usuario sin una ubicación de uso especificada heredará la ubicación del directorio. Si hay usuarios en varias ubicaciones, se recomienda establecer la ubicación de uso siempre como parte del flujo de creación de usuarios en Azure AD (por ejemplo, mediante la configuración de AAD Connect), que garantizará que el resultado de la asignación de licencias siempre es correcto y que los usuarios no reciben los servicios en ubicaciones que no están permitidas.

## <a name="step-1-assign-the-required-licenses"></a>Paso 1: Asignación de las licencias necesarias

1. Inicie sesión en [**Azure Portal**](https://portal.azure.com) con una cuenta de administrador. Para administrar las licencias, la cuenta necesita el rol de administrador global o de administrador de cuentas de usuario.

2. Seleccione **Más servicios** en el panel de navegación izquierdo y seleccione **Azure Active Directory**. Puede agregar esta hoja a Favoritos o anclarla al panel del portal.

3. En la hoja **Azure Active Directory**, seleccione **Licencias**. Se abre una hoja donde puede ver y administrar todos los productos con licencia que hay en el inquilino.

4. En **Todos los productos**, seleccione los nombres de producto de Office 365 Enterprise E3 y Enterprise Mobility + Security para seleccionarlos. Para iniciar la asignación, seleccione **Asignar** en la parte superior de la hoja.

   ![Todos los productos, asignar licencias](media/active-directory-licensing-group-assignment-azure-portal/all-products-assign.png)

5. En la hoja **Asignar licencias**, haga clic en **Usuarios y grupos** para abrir la hoja **Usuarios y grupos**. Busque el nombre del grupo *HR Department* (Departamento de recursos humanos), seleccione el grupo y, a continuación, asegúrese de confirmar; para ello, haga clic **Seleccionar** en la parte inferior de la hoja.

   ![Selección de un grupo](media/active-directory-licensing-group-assignment-azure-portal/select-a-group.png)

6. En la hoja **Asignar licencias**, haga clic en **Opciones de asignación (opcionales)** que muestra todos los planes de servicio incluidos en los dos productos que hemos seleccionado anteriormente. Busque **Yammer Enterprise** y establézcalo en **Desactivar** para deshabilitar el servicio de la licencia de producto. Para confirmar, haga clic en **Aceptar** en la parte inferior de **Opciones de asignación**.

   ![Opciones de asignación](media/active-directory-licensing-group-assignment-azure-portal/assignment-options.png)

7. Para completar la asignación, en la hoja **Asignar licencia**, haga clic en **Asignar** en la parte inferior de la hoja.

8. Se muestra una notificación en la esquina superior derecha con el estado y el resultado del proceso. Si no se pudo completar la asignación al grupo (por ejemplo, porque ya existían licencias en el grupo), haga clic en la notificación para ver los detalles del error.

Ahora, hemos especificado una plantilla de licencia en el grupo del departamento de recursos humanos. Se inició un proceso en segundo plano en Azure AD para procesar todos los miembros existentes de ese grupo. Esta operación inicial podría tardar algún tiempo, según el tamaño actual del grupo. En el paso siguiente, vamos a describir cómo comprobar si el proceso se ha completado y si es necesario hacer algo más para resolver los problemas.

> [!NOTE]
> La misma asignación se puede iniciar desde una ubicación alternativa: **Usuarios y grupos** en Azure AD. Vaya a **Azure Active Directory** > **Usuarios y grupos** > **Todos los grupos**. Después, busque el grupo, selecciónelo y vaya a la pestaña **Licencias**. El botón **Asignar** en la parte superior de la hoja abre la hoja de asignación de licencias.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Paso 2: Comprobación de que se ha completado la asignación inicial

1. Vaya a **Azure Active Directory** > **Usuarios y grupos** > **Todos los grupos**. Luego, busque el grupo **HR Department** al que se asignaron las licencias.

2. En la hoja del grupo **HR Department**, seleccione **Licencias**. Esto le permite confirmar rápidamente si las licencias se han asignado completamente a los usuarios y si hay errores que requieran atención. Está disponible la siguiente información:

   - Lista de licencias de producto que están asignadas actualmente al grupo. Seleccione una entrada para mostrar los servicios específicos que se han habilitado y para realizar cambios.

   - Estado de los últimos cambios de licencia realizados en el grupo: si los cambios se están procesando o si se ha completado el procesamiento en todos los usuarios miembros.

   - Información acerca de los usuarios que están en estado de error porque no se les pudo asignar licencias.

   ![Opciones de asignación](media/active-directory-licensing-group-assignment-azure-portal/assignment-errors.png)

3. Encontrará información más detallada sobre el procesamiento de licencias en **Azure Active Directory** > **Usuarios y grupos** > *nombre de grupo* > **Registros de auditoría**. Tenga en cuenta las siguientes actividades:

   - Actividad: **Empezar a aplicar licencias basadas en grupo a los usuarios**. Se registra cuando el sistema recoge el cambio de asignación de licencias en el grupo y empieza a aplicarlo a todos los usuarios miembros. Contiene información sobre el cambio que se realizó.

   - Actividad: **Empezar a aplicar las licencias basadas en grupo a los usuarios**. Se registra cuando el sistema termina de procesar todos los usuarios del grupo. Contiene un resumen de cuántos usuarios se han procesado correctamente y el número de usuarios a los que no se pudieron asignar las licencias de grupo.

   [Lea esta sección](./active-directory-licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) para obtener más información sobre cómo se pueden usar los registros de auditoría para analizar los cambios realizados por las licencias basadas en grupos.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Paso 3: Comprobación y resolución de problemas de licencias

1. Vaya a **Azure Active Directory**  > **Usuarios y grupos** > **Todos los grupos** y busque el grupo **HR Department** al que se van a asignar las licencias.
2. En la hoja del grupo **HR Department**, seleccione **Licencias**. La notificación en la parte superior de la hoja muestra que hay 10 usuarios a los que no se pudieron asignar licencias. Al hacer clic en ella, se abre una lista con todos los usuarios en estado de error de licencia para este grupo.
3. La columna **Asignaciones erróneas** indica que no se pudo asignar ninguna de las licencias de producto a los usuarios. La columna **Motivo principal del error** contiene la causa del error. En este caso, es **Planes de servicio en conflicto**.

   ![Asignaciones erróneas](media/active-directory-licensing-group-assignment-azure-portal/failed-assignments.png)

4. Seleccione un usuario para abrir la hoja **Licencias**. Esta hoja muestra todas las licencias que están asignadas actualmente al usuario. En este ejemplo, el usuario tiene la licencia de Office 365 Enterprise E1 que heredó del grupo **Kiosk users**. Esto entra en conflicto con la licencia E3 que el sistema intentó aplicar desde el grupo **HR Department**. Como resultado, ninguna de las licencias de dicho grupo se ha asignado al usuario.

   ![Ver las licencias de un usuario](media/active-directory-licensing-group-assignment-azure-portal/user-license-view.png)

5. Para resolver este problema, quite el usuario del grupo **Kiosk users**. Una vez que Azure AD procesa el cambio, las licencias de **HR Department** se asignan correctamente.

   ![Licencia asignada correctamente](media/active-directory-licensing-group-assignment-azure-portal/license-correctly-assigned.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el conjunto de características de administración de licencias mediante grupos, consulte los artículos siguientes:

* [¿En qué consisten las licencias basadas en grupos de Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Identificación y resolución de problemas de licencias de un grupo en Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Migración de usuarios individuales con licencia a licencias basadas en grupos en Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Escenarios adicionales de licencias basadas en grupos de Azure Active Directory](active-directory-licensing-group-advanced.md)
