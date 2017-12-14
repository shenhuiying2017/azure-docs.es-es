---
title: "Azure Active Directory Domain Services: administración de un dominio administrado | Microsoft Docs"
description: "Administración de dominios administrados con Azure Active Directory Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4fdbc75-3e6b-4e20-8494-5dcc3bf2220a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: maheshu
ms.openlocfilehash: 7ffe3e54b891fd74e14666c136be49132d78590b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Administrar un dominio administrado de Azure Active Directory Domain Services
Este artículo muestra cómo administrar un dominio administrado con Servicios de dominio de Azure Active Directory (AD).

## <a name="before-you-begin"></a>Antes de empezar
Para realizar las tareas enumeradas en este artículo, necesita lo siguiente:

1. Una **suscripción de Azure**válida.
2. Un **directorio de Azure AD** : sincronizado con un directorio local o solo en la nube.
3. **Servicios de dominio de Azure AD** deben estar habilitado en el directorio de Azure AD. Si no lo ha hecho, siga todas las tareas descritas en [Servicios de dominio de Azure AD (vista previa): introducción](active-directory-ds-getting-started.md).
4. Una **máquina virtual unida a un dominio** , desde la que administrará el dominio administrado de los Servicios de dominio de Azure AD. Si no tiene una máquina virtual, siga todas las tareas descritas en el artículo [Unión de una máquina virtual de Windows Server a un dominio administrado](active-directory-ds-admin-guide-join-windows-vm.md).
5. Necesitará las credenciales de una **cuenta de usuario que pertenezca al grupo Administradores del controlador de dominio de AAD** en el directorio con el fin de administrar el dominio administrado.

<br>

## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Tareas administrativas que puede realizar en un dominio administrado
A los miembros del grupo "Administradores del controlador de dominio de AAD" se les conceden privilegios en el dominio administrado que les permiten realizar tareas como:

* Unir máquinas al dominio administrado.
* Configurar el GPO integrado para los contenedores "Equipos de AADDC" y "Usuarios de AADDC" en el dominio administrado.
* Administrar DNS en el dominio administrado.
* Crear y administrar unidades organizativas (OU) personalizadas en el dominio administrado.
* Obtener acceso administrativo a los equipos unidos al dominio administrado.

## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Privilegios de administrador que no tiene en un dominio administrado
Microsoft administra el dominio, incluidas actividades como aplicación de revisiones, supervisión, realizar copias de seguridad, etc. Por lo tanto, el dominio está bloqueado y no tiene privilegios para realizar ciertas tareas administrativas en él. A continuación se proporcionan algunos ejemplos de tareas que no puede realizar.

* No tiene privilegios de administrador de dominio ni de administrador de organización para el dominio administrado.
* No puede extender el esquema del dominio administrado.
* No puede conectarse al controlador de dominio para el dominio administrado mediante Escritorio remoto
* No puede agregar controladores de dominio al dominio administrado.

## <a name="task-1---provision-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Tarea 1: Aprovisionamiento de una máquina virtual de Windows Server unida a un dominio para administrar de forma remota el dominio administrado
Los dominios administrados con Servicios de dominio de Azure AD pueden administrarse mediante las conocidas herramientas administrativas de Active Directory, como el Centro de administración de Active Directory (ADAC) o AD PowerShell. Los administradores de inquilinos no tienen privilegios para conectarse a controladores de dominio en el dominio administrado mediante el Escritorio remoto. Por lo tanto, los miembros del grupo "Administradores del controlador de dominio de AAD" pueden administrar dominios administrados de forma remota mediante las herramientas administrativas de AD desde un equipo cliente/Windows Server que se ha unido al dominio administrado. Las herramientas administrativas de AD pueden instalarse como parte de la característica opcional Herramientas de administración remota del servidor (RSAT) en Windows Server y equipos cliente unidos al dominio administrado.

El primer paso es configurar una máquina virtual de Windows Server que se ha unido al dominio administrado. Para ver instrucciones, consulte el artículo [Unión de una máquina virtual de Windows Server a un dominio administrado](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Administración remota del dominio administrado desde un equipo cliente (por ejemplo, Windows 10)
En las instrucciones de este artículo se usa una máquina virtual Windows Server para administrar el dominio administrado con AAD-DS. Sin embargo, también puede elegir usar una máquina virtual de cliente Windows (por ejemplo, Windows 10) para hacerlo.

Puede [instalar Herramientas de administración remota del servidor (RSAT)](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) en una máquina virtual cliente de Windows siguiendo las instrucciones en TechNet.

## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Tarea 2: Instalación de herramientas de administración de Active Directory en la máquina virtual
Realice los pasos siguientes para instalar las herramientas de administración de Active Directory en la máquina virtual unida al dominio. Para más [información sobre la instalación y el uso de Herramientas de administración remota del servidor](https://technet.microsoft.com/library/hh831501.aspx).

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
9. En la página **Características**, haga clic para expandir el nodo **Herramientas de administración remota del servidor** y, después, haga clic para expandir el nodo **Herramientas de administración de roles**. Seleccione la característica **Herramientas de AD DS y AD LDS** en la lista de herramientas de administración de roles, como se muestra a continuación.

    ![Página Características](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)
10. En la página **Confirmación** haga clic en **Instalar** para instalar la característica de herramientas de AD y AD LDS en la máquina virtual. Cuando finalice correctamente la instalación de la característica, haga clic en **Cerrar** para salir del **Asistente para agregar roles y características**.

    ![Página de confirmación](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)

## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Tarea 3: Conexión con y exploración del dominio administrado
Ahora que las herramientas administrativas de AD están instaladas en la máquina virtual unida a dominio, puede usarlas para explorar y administrar una unidad organizativa en el dominio administrado.

> [!NOTE]
> Tendrá que ser miembro del grupo Administradores del controlador de dominio de AAD para administrar el dominio administrado.
>
>

1. En la pantalla Inicio, haga clic en **Herramientas administrativas**. Debería ver las herramientas administrativas de AD instaladas en la máquina virtual.

    ![Herramientas administrativas instaladas en el servidor](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Haga clic en **Centro de administración de Active Directory**.

    ![Centro de administración de Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Para explorar el dominio, haga clic en el nombre de dominio en el panel izquierdo (por ejemplo, "contoso100.com"). Observe dos contenedores denominados "AADDC equipos" y "Usuarios de AADDC" respectivamente.

    ![ADAC: ver dominio](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)
4. Haga clic en el contenedor **Usuarios de AADDC** para ver todos los usuarios y grupos que pertenecen al dominio administrado. Debería ver las cuentas de usuario y grupos de su inquilino de Azure AD en este contenedor. Observe que en este ejemplo hay una cuenta de usuario para el usuario "bob" y un grupo llamado "Administradores del controlador de dominio de AAD" disponibles en este contenedor.

    ![ADAC: usuarios del dominio](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)
5. Haga clic en el contenedor **Equipos de AADDC** para ver los equipos unidos a este dominio administrado. Debería ver una entrada para la máquina virtual actual, que está unida al dominio. Las cuentas de equipo de todos los equipos unidos al dominio administrado con Servicios de dominio de Azure AD aparecerán en este contenedor "AADDC equipos".

    ![ADAC: equipos unidos al dominio](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Contenido relacionado
* [Introducción a Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Unión de una máquina virtual de Windows Server a un dominio administrado](active-directory-ds-admin-guide-join-windows-vm.md)
* [Implementar herramientas de administración remota del servidor](https://technet.microsoft.com/library/hh831501.aspx)
