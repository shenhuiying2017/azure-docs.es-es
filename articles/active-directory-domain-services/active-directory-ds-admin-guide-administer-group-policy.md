---
title: "Azure Active Directory Domain Services: administración de directiva de grupo en dominios administrados | Microsoft Docs"
description: "Administración de directiva de grupo en dominios administrados de Azure Active Directory Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: 316ddc2cbd67cfafaf44318c5baebcd8da366f93
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Administración de directiva de grupo en un dominio administrado de Azure AD Domain Services
Azure Active Directory Domain Services incluye objetos de directiva de grupo (GPO) integrados para los contenedores "Usuarios del controlador de dominio de AAD" Y "Equipos del controlador de dominio de AAD". Puede personalizar estos GPO integrados para configurar la directiva de grupo en el dominio administrado. Además, los miembros del grupo "Administradores de DC de AAD" pueden crear sus propias unidades organizativas personalizadas en el dominio administrado. También pueden crear GPO personalizados y vincularlos a estas unidades organizativas personalizadas. A los usuarios que pertenecen al grupo "Administradores del controlador de dominio de AAD" se les conceden privilegios de administración de directiva de grupo en el dominio administrado.

## <a name="before-you-begin"></a>Antes de empezar
Para realizar las tareas enumeradas en este artículo, necesita lo siguiente:

1. Una **suscripción de Azure**válida.
2. Un **directorio de Azure AD** : sincronizado con un directorio local o solo en la nube.
3. **Servicios de dominio de Azure AD** deben estar habilitado en el directorio de Azure AD. Si no lo ha hecho, siga todas las tareas descritas en [Servicios de dominio de Azure AD (vista previa): introducción](active-directory-ds-getting-started.md).
4. Una **máquina virtual unida a un dominio** , desde la que administrará el dominio administrado de los Servicios de dominio de Azure AD. Si no tiene una máquina virtual, siga todas las tareas descritas en el artículo [Unión de una máquina virtual de Windows Server a un dominio administrado](active-directory-ds-admin-guide-join-windows-vm.md).
5. Necesitará las credenciales de una **cuenta de usuario que pertenezca al grupo "Administradores del controlador de dominio de AAD"** en el directorio con el fin de administrar la directiva de grupo en el dominio administrado.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>Tarea 1: Aprovisionamiento de una máquina virtual unida a un dominio para administrar de forma remota la directiva de grupo para el dominio administrado
Los dominios administrados con Servicios de dominio de Azure AD se pueden controlar de forma remota mediante las conocidas herramientas administrativas de Active Directory, como el Centro de administración de Active Directory (ADAC) o AD PowerShell. De forma similar, la directiva de grupo para el dominio administrado se puede administrar remotamente con las herramientas de administración de directiva de grupo.

Los administradores de su directorio de Azure AD no tienen privilegios para conectarse a controladores de dominio en el dominio administrado a través del Escritorio remoto. Los miembros del grupo "Administradores del controlador de dominio de AAD" pueden administrar la directiva de grupo para dominios administrados de forma remota. Pueden usar herramientas de directiva de grupo en un servidor de Windows Server o un equipo cliente que esté unido al dominio administrado. Las herramientas de directiva de grupo pueden instalarse como parte de la característica opcional Administración de directivas de grupo en Windows Server y en equipos cliente unidos al dominio administrado.

El primer paso consiste en configurar una máquina virtual Windows Server que esté unida al dominio administrado. Para ver instrucciones, consulte el artículo [Unión de una máquina virtual de Windows Server a un dominio administrado](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>Tarea 2: Instalar herramientas de directiva de grupo en la máquina virtual
Realice los pasos siguientes para instalar las herramientas de Administración de directivas de grupo en la máquina virtual unida al dominio.

1. Acceda a Azure Portal. Haga clic en **Todos los recursos** en el panel izquierdo. Busque y haga clic en la máquina virtual que creó en la tarea 1.
2. Haga clic en el botón **Conectar** en la pestaña Información general. Se crea y se descarga un archivo de Protocolo de Escritorio remoto (.rdp).

    ![Conexión a máquina virtual de Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Para conectarse a la máquina virtual, abra el archivo RDP descargado. Cuando se le solicite, haga clic en **Conectar**. En el aviso de inicio de sesión, utilice las credenciales de un usuario que pertenezca al grupo "Administradores del controlador de dominio de AAD". Por ejemplo, en nuestro caso usamos "bob@domainservicespreview.onmicrosoft.com". Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Haga clic en Sí o en Continuar para continuar con la conexión.
4. En la pantalla Inicio, abra **Administrador del servidor**. Haga clic en **Agregar roles y características** en el panel central de la ventana Administrador del servidor.

    ![Inicio del Administrador del servidor en la máquina virtual](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. En la página **Antes de comenzar** del **Asistente para agregar roles y características**, haga clic en **Siguiente**.

    ![Página Antes de comenzar](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. En la página **Tipo de instalación**, deje la opción **Instalación basada en características o en roles** activada y haga clic en **Siguiente**.

    ![Página Tipo de instalación](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. En la página **Selección del servidor**, seleccione la máquina virtual actual del grupo de servidores y haga clic en **Siguiente**.

    ![Página Selección de servidor](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. En la página **Roles de servidor**, haga clic en **Siguiente**. Se omitirá esta página ya que no se van a instalar roles en el servidor.
9. En la página **Características**, seleccione la **Administración de directivas de grupo**.

    ![Página Características](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. En la página **Confirmación**, haga clic en **Instalar** para instalar la característica Administración de directivas de grupo en la máquina virtual. Cuando finalice correctamente la instalación de la característica, haga clic en **Cerrar** para salir del **Asistente para agregar roles y características**.

    ![Página de confirmación](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>Tarea 3: Iniciar la consola de administración de directivas de grupo para administrar la directiva de grupo
Puede usar la consola de administración de directivas de grupo en la máquina virtual unida a dominio para administrar la directiva de grupo en el dominio administrado.

> [!NOTE]
> Tendrá que ser miembro del grupo "Administradores del controlador de dominio de AAD" para administrar la directiva de grupo en el dominio administrado.
>
>

1. En la pantalla Inicio, haga clic en **Herramientas administrativas**. Debería ver la consola de **Administración de directivas de grupo** instalada en la máquina virtual.

    ![Inicio de la Administración de directivas de grupo](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Haga clic en **Administración de directivas de grupo** para iniciar la consola de Administración de directivas de grupo.

    ![Consola de directiva de grupo](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>Tarea 4: Personalización de objetos de directiva de grupo integrados
Hay dos objetos de directiva de grupo (GPO) integrados: uno para el contenedor "Equipos del controlador de dominio de AAD" y otro para "Usuarios del controlador de dominio de AAD" del dominio administrado. Puede personalizar estos GPO para configurar la directiva de grupo en el dominio administrado.

1. En la consola **Administración de directivas de grupo**, haga clic para expandir los nodos **Forest: contoso100.com** y **Domains** para ver las directivas de grupo para el dominio administrado.

    ![GPO integrados](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Puede personalizar estos GPO integrados para configurar las directivas de grupo en el dominio administrado. Haga clic en el GPO y haga clic en **Editar...**  para personalizar el GPO integrado. La herramienta Editor de administración de directivas de grupo le permite personalizar el GPO.

    ![Editar GPO integrado](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Ahora puede usar la consola del **Editor de administración de directivas de grupo** para editar el GPO integrado. Por ejemplo, la captura de pantalla siguiente muestra cómo personalizar el GPO "Equipos del controlador de dominio de AAD" integrado.

    ![Personalizar GPO](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>Tarea 5: Creación de un objeto de directiva de grupo (GPO) personalizado
Puede crear o importar sus propios objetos de directiva de grupo personalizados. También puede vincular GPO personalizados a una unidad organizativa personalizada que haya creado en el dominio administrado. Para más información sobre la creación de unidades organizativas personalizadas, consulte [Creación de una unidad organizativa en un dominio administrado](active-directory-ds-admin-guide-create-ou.md).

> [!NOTE]
> Tendrá que ser miembro del grupo "Administradores del controlador de dominio de AAD" para administrar la directiva de grupo en el dominio administrado.
>
>

1. En la consola **Administración de directivas de grupo**, haga clic para seleccionar su unidad organizativa (OU) personalizada. Haga clic con el botón derecho en la unidad organizativa y haga clic en **Crear un GPO en este dominio y vincularlo aquí**.

    ![Crear un GPO personalizado](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Especifique un nombre para el nuevo GPO y haga clic en **Aceptar**.

    ![Especificar un nombre para el GPO](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Se crea un GPO y se vincula a la OU personalizada. Haga clic con el botón derecho en el GPO y haga clic en **Editar...** en el menú.

    ![Un GPO recién creado](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Puede personalizar el GPO recién creado con el **Editor de administración de directivas de grupo**.

    ![Personalizar el nuevo GPO](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


Hay más información sobre el uso de la [Consola de administración de directivas de grupo](https://technet.microsoft.com/library/cc753298.aspx) disponible en Technet.

## <a name="related-content"></a>Contenido relacionado
* [Introducción a Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Unión de una máquina virtual de Windows Server a un dominio administrado](active-directory-ds-admin-guide-join-windows-vm.md)
* [Administer an Azure AD Domain Services managed domain (Administración de un dominio administrado con Servicios de dominio de Azure AD)](active-directory-ds-admin-guide-administer-domain.md)
* [Consola de administración de directivas de grupo](https://technet.microsoft.com/library/cc753298.aspx)
