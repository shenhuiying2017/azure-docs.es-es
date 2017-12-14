---
title: "Azure Active Directory Domain Services: introducción | Microsoft Docs"
description: "Habilitación de Azure Active Directory Domain Services mediante Azure Portal"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: 2a171490faf9804196c93d33c5ee74d22533c044
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Habilitación de Azure Active Directory Domain Services mediante Azure Portal


## <a name="task-3-configure-administrative-group"></a>Tarea 3: Configuración de un grupo administrativo
En esta tarea de configuración, se crea un grupo administrativo en el directorio de Azure AD. Este grupo administrativo especial se llama *Administradores de DC de AAD*. A los miembros de este grupo se les conceden permisos administrativos en máquinas que estén unidas al domino administrado. En equipos unidos a un dominio, este grupo se agrega al grupo de administradores. Además, los miembros de este grupo también podrá usar Escritorio remoto para conectarse de forma remota a las máquinas unidas a un dominio.

> [!NOTE]
> No tiene permisos de administrador de dominio o administrador de organización en el dominio administrado creado mediante Azure Active Directory Domain Services. En los dominios administrados, estos permisos están reservados por el servicio y no están disponibles para los usuarios del inquilino. Sin embargo, puede usar el grupo administrativo especial creado en esta tarea de configuración para realizar algunas operaciones con privilegios. Estas operaciones incluyen unir equipos al dominio, formar parte del grupo de administradores en los equipos unidos a un dominio y configurar una directiva de grupo.
>

El asistente crea automáticamente el grupo administrativo en el directorio de Azure AD. Este grupo se llama "Administradores de DC de AAD". Si tiene un grupo existente con este nombre en el directorio de Azure AD, el asistente lo selecciona. Puede configurar la pertenencia a grupos mediante la página **Grupo de administradores** del asistente.

1. Para configurar la pertenencia a grupos, haga clic en **Administradores de DC de AAD**.

    ![Configuración de la pertenencia a grupos](./media/getting-started/domain-services-blade-admingroup.png)

2. Haga clic en el botón **Agregar miembros** para agregar usuarios del directorio de Azure AD al grupo de administradores.

3. Cuando haya terminado, haga clic en **Aceptar** para ir a la página **Resumen** del asistente.

4. En la página **Resumen** del asistente, revise la configuración del dominio administrado. Puede volver a cualquier paso del asistente para realizar cambios, si es necesario. Cuando haya terminado, haga clic en **Aceptar** para crear el dominio administrado.

    ![Resumen](./media/getting-started/domain-services-blade-summary.png)

5. Verá una notificación que muestra el progreso de la implementación de la instancia de Azure AD Domain Services. Haga clic en la notificación para ver el progreso detallado de la implementación.

    ![Notificación: implementación en curso](./media/getting-started/domain-services-blade-deployment-in-progress.png)


## <a name="provision-your-managed-domain"></a>Aprovisionamiento del dominio administrado
El proceso de aprovisionamiento del dominio administrado puede tardar hasta una hora.

1. Mientras la implementación está en curso, puede buscar "servicios de dominio" en el cuadro de búsqueda **buscar recursos**. En el resultado de la búsqueda, seleccione **Azure AD Domain Services**. La hoja **Azure AD Domain Services** muestra el dominio administrado que se está aprovisionando.

    ![Búsqueda del dominio administrado que se va aprovisionar](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Haga clic en el nombre del dominio administrado (por ejemplo, "contoso100.com") para ver más detalles sobre el dominio administrado.

    ![Domain Services: estado de aprovisionamiento](./media/getting-started/domain-services-provisioning-state.png)

3. La pestaña **Información** muestra que el dominio administrado se está aprovisionando. No se puede configurar el dominio administrado hasta que está aprovisionado por completo. El dominio administrado puede tardar una hora en aprovisionarse por completo.

    ![Domain Services: pestaña Información general durante el estado de aprovisionamiento ](./media/getting-started/domain-services-provisioning-state-details.png)

4. Cuando el dominio administrado está aprovisionado por completo, la pestaña **Información general** muestra el estado del dominio como **En ejecución**.

    ![Domain Services: pestaña Información general después del aprovisionamiento completo](./media/getting-started/domain-services-provisioned.png)

5. En la pestaña **Propiedades**, se ven dos direcciones IP en las que hay controladores de dominio disponibles para Virtual Network.

    ![Domain Services: pestaña Propiedades después del aprovisionamiento completo](./media/getting-started/domain-services-provisioned-properties.png)


## <a name="need-help"></a>¿Necesita ayuda?
Puede tardar una hora o dos para ambos controladores de dominio para aprovisionar el dominio administrado. Si se produjo un error en la implementación o se detuvo en el estado "Pendiente" durante más de dos horas, [póngase en contacto con el equipo del producto para obtener ayuda](active-directory-ds-contact-us.md).


## <a name="next-step"></a>Paso siguiente
[Tarea 4: Actualización de la configuración DNS en Azure Virtual Network](active-directory-ds-getting-started-dns.md)
