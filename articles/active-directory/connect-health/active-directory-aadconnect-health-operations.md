---
title: Operaciones de Azure Active Directory Connect Health
description: "Este artículo describe las operaciones adicionales que pueden realizarse una vez que haya implementado Azure AD Connect Health."
services: active-directory
documentationcenter: 
author: karavar
manager: femila
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 06afc6b4149ea1590a2994d1638d6979a89035e3
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-active-directory-connect-health-operations"></a>Operaciones de Azure Active Directory Connect Health
Este tema describe las distintas operaciones que se pueden realizar mediante Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Habilitación de notificaciones de correo electrónico
Puede configurar el servicio de Azure AD Connect Health para enviar notificaciones por correo electrónico cuando las alertas indiquen que el estado de la infraestructura de identidad no es correcto. Esto se produce cuando se genera una alerta y cuando se resuelve.

![Captura de pantalla de la configuración de notificaciones de correo electrónico de Azure AD Connect Health](./media/active-directory-aadconnect-health/email_noti_discover.png)

> [!NOTE]
> Las notificaciones de correo electrónico están habilitadas de forma predeterminada.
>
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Para habilitar las notificaciones de correo electrónico de Azure AD Connect Health
1. Abra la hoja **Alertas** del servicio para el que desea recibir una notificación de correo electrónico.
2. Haga clic en el botón **Configuración de notificaciones** de la barra de acciones.
3. En el modificador de la opción Notificación de correo electrónico, seleccione **ON** (Activado).
4. Seleccione la casilla si desea que todos los administradores globales reciban notificaciones de correo electrónico.
5. Si desea recibir notificaciones de correo electrónico en otras direcciones de correo electrónico, puede especificarlas en el cuadro **Destinatario de correo electrónico adicional**. Para quitar una dirección de correo electrónico de esta lista, haga clic con el botón derecho en la entrada y seleccione **Eliminar**.
6. Para finalizar los cambios, haga clic en **Guardar**. Los cambios surtirán efecto después de guardar.

## <a name="delete-a-server-or-service-instance"></a>Eliminación de una instancia de servidor o servicio

En algunos casos, es posible que desee dejar de supervisar un servidor. Siga estas instrucciones para quitar un servidor del Servicio de Azure AD Connect Health.

Cuando elimine un servidor, tenga en cuenta lo siguiente:

* Esta acción detiene cualquier recopilación de datos de ese servidor. Este servidor se quitará del servicio de supervisión. Después de esta acción, no podrá ver nuevas alertas ni datos de análisis de uso o supervisión de este servidor.
* Esta acción no desinstala el agente de mantenimiento del servidor. Si no ha desinstalado el agente de mantenimiento antes de realizar este paso, es posible que vea eventos de error en el servidor relacionados con el agente de mantenimiento.
* Esta acción no elimina los datos que ya se recopilaron desde este servidor. Esos datos se eliminan de acuerdo con la directiva de retención de datos de Azure.
* Tras realizar esta acción, si desea empezar la supervisión del mismo servidor de nuevo, desinstale el agente de estado y vuelva a instalarlo en este servidor.

### <a name="to-delete-a-server-from-the-azure-ad-connect-health-service"></a>Para eliminar un servidor del Servicio de Azure AD Connect Health
Azure AD Connect Health para Servicios de federación de Active Directory (AD FS) y Azure AD Connect (Sync):

1. Seleccione el nombre del servidor que se va a quitar para abrir la hoja **Servidor** en la hoja **Lista de servidores**.
2. En la hoja **Servidor**, en la barra de acciones, haga clic en **Eliminar**.
3. Confirme el nombre del servidor; para ello, escríbalo en el cuadro de confirmación.
4. Hacer clic en **Eliminar**.

Azure AD Connect Health para Azure Active Directory Domain Services:

1. Abra el panel de **controladores de dominio**.
2. Seleccione el controlador de dominio que va a quitar.
3. En la barra de acciones, haga clic en **Eliminar selección**.
4. Confirme la acción para eliminar el servidor.
5. Hacer clic en **Eliminar**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Eliminación de una instancia de servicio del Servicio de Azure AD Connect Health
En algunos casos, es posible que desee quitar una instancia de servicio. Siga estas instrucciones para quitar una instancia de servicio del Servicio de Azure AD Connect Health.

Cuando elimine una instancia de servicio, tenga en cuenta lo siguiente:

* Esta acción quitará la instancia de servicio actual del servicio de supervisión.
* Esta acción no desinstalará ni quitará el agente de mantenimiento de ninguno de los servidores que se supervisaron como parte de esta instancia de servicio. Si no ha desinstalado el agente de mantenimiento antes de realizar este paso, es posible que vea eventos de error en los servidores relacionados con el agente de mantenimiento.
* Todos los datos de esta instancia de servicio se eliminarán según lo estipulado en la directiva de retención de datos de Azure.
* Tras realizar esta acción, si desea empezar a supervisar el servicio, desinstale el agente de estado y vuelva a instalarlo en todos los servidores. Tras realizar esta acción, si desea empezar la supervisión del mismo servidor de nuevo, desinstale el agente de estado y regístrelo en ese servidor.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Eliminación de una instancia de servicio del Servicio de Azure AD Connect Health
1. Seleccione el identificador del servicio (nombre de la granja) que desea quitar para abrir la hoja **Servicio** en la hoja **Lista de Servicios**.
2. En la hoja **Servidor**, en la barra de acciones, haga clic en **Eliminar**.
3. Confirme el nombre del servicio. Para ello, escríbalo en el cuadro de confirmación (por ejemplo: sts.contoso.com).
4. Hacer clic en **Eliminar**.
   <br><br>

[//]: # (Start of RBAC section)
## <a name="manage-access-with-role-based-access-control"></a>Administración de accesos con el control de acceso basado en rol
El [control de acceso basado en rol (RBAC)](../role-based-access-control-configure.md) para Azure AD Connect Health proporciona acceso a usuarios y/o grupos distintos a los administradores globales. RBAC asigna roles a los usuarios y/o grupos previstos y proporciona un mecanismo para limitar los administradores globales dentro del directorio.

### <a name="roles"></a>Roles
Azure AD Connect Health admite los siguientes roles integrados:

| Rol | Permisos |
| --- | --- |
| Propietario |Los propietarios pueden *administrar el acceso* (por ejemplo, asignar roles a un usuario o grupo), *ver toda la información* (por ejemplo, ver las alertas) desde el portal y *cambiar la configuración* (por ejemplo, notificaciones de correo electrónico) dentro de Azure AD Connect Health. <br>De forma predeterminada, a los administradores globales de Azure AD se les asigna este rol y esto no se puede cambiar. |
| Colaborador |Los colaboradores pueden *ver toda la información* (por ejemplo, ver las alertas) desde el portal y *cambiar la configuración* (por ejemplo, notificaciones de correo electrónico) dentro de Azure AD Connect Health. |
| Lector |Los lectores pueden *ver toda la información* (por ejemplo, ver las alertas) desde el portal dentro de Azure AD Connect Health. |

Todos los demás roles (como "Administradores de acceso de usuario" o "Usuarios del laboratorio DevTest"), incluso si están disponibles en la experiencia del portal, no afectan al acceso dentro de Azure AD Connect Health.

### <a name="access-scope"></a>Ámbito de acceso
Azure AD Connect Health admite la administración de acceso a dos niveles:

* **Todas las instancias de servicio**: esta es la ruta de acceso recomendada en la mayoría de los casos. Controla el acceso a todas las instancias de servicio (por ejemplo, a una granja de servidores de AD FS) en todos los tipos de rol que se estén supervisando mediante Azure AD Connect Health.
* **Instancia de servicio**: en algunos casos, puede que necesite separar el acceso según los tipos de rol o por una instancia de servicio. En este caso, puede administrar el acceso en el nivel de instancia de servicio.  

El permiso se concede si un usuario final tiene acceso al nivel de directorio o de instancia de servicio.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Cómo permitir el acceso a los usuarios o grupos a Azure AD Connect Health
Se muestra cómo hacerlo en los pasos siguientes.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Paso 1: Seleccionar el ámbito de acceso adecuado
Para permitir a un usuario acceder al nivel de *todas las instancias de servicio* dentro de Azure AD Connect Health, abra la hoja principal en Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Paso 2: Agregar usuarios, grupos y asignar roles
1. En la sección **Configurar**, haga clic en **Usuarios**.<br>
   ![Captura de pantalla de la hoja principal del control de acceso basado en rol de Azure AD Connect Health, con usuarios resaltados](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Seleccione **Agregar**.
3. En el panel **Seleccionar rol**, seleccione un rol (por ejemplo, **Propietario**).<br>
   ![Captura de pantalla de la ventana Usuarios del control de acceso basado en rol de Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Escriba el nombre o identificador del usuario o grupo de destino. Puede seleccionar uno o más usuarios o grupos al mismo tiempo. Haga clic en **Seleccionar**.
   ![Captura de pantalla de la ventana Usuarios del control de acceso basado en rol de Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Seleccione **Aceptar**.<br>
6. Después de finalizar la asignación de roles, los usuarios y grupos aparecerán en la lista.<br>
   ![Captura de pantalla de la ventana Usuarios del control de acceso basado en rol de Azure AD Connect Health, con nuevos usuarios resaltados](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Ahora los usuarios y grupos de la lista tienen acceso, según sus roles asignados.

> [!NOTE]
> * Los administradores globales siempre tienen acceso total a todas las operaciones, pero las cuentas de administrador global no figurarán en la lista anterior.
> * No se admite la característica "Invitar a usuarios" en Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Paso 3: Compartir la ubicación de la hoja con usuarios o grupos
1. Después de asignar permisos, un usuario puede acceder a Azure AD Connect Health yendo [aquí](http://aka.ms/aadconnecthealth).
2. Una vez en la hoja, el usuario puede anclar dicha hoja o diferentes partes de esta al panel. Solo tiene que hacer clic en el icono **Anclar al panel**.<br>
   ![Captura de pantalla de la hoja del control de acceso basado en rol de Azure AD Connect Health, con el icono de anclaje resaltado](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)

> [!NOTE]
> Un usuario con el rol de "Lector" asignado no puede obtener la extensión de Azure AD Connect Health de Azure Marketplace. El usuario no puede realizar la operación "crear" necesaria para realizar esta acción. Aún así, este usuario todavía puede obtener la hoja visitando el vínculo anterior. Para usos posteriores, el usuario puede anclar la hoja en el panel.
>
>

### <a name="remove-users-or-groups"></a>Eliminación de usuarios o grupos
Puede quitar un usuario o un grupo agregado al control de acceso basado en rol de Azure AD Connect Health. Simplemente haga clic con el botón derecho en el usuario o grupo y seleccione **Quitar**.<br>
![Captura de pantalla de la ventana Usuarios del control de acceso basado en rol de Azure AD Connect Health, con la opción Quitar resaltada](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (End of RBAC section)

## <a name="next-steps"></a>Pasos siguientes
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalación del Agente de Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Uso de Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso de Azure AD Connect Health para sincronización](active-directory-aadconnect-health-sync.md)
* [Uso de Azure AD Connect Health con AD DS](active-directory-aadconnect-health-adds.md)
* [Preguntas más frecuentes de Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Historial de versiones de Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
