---
title: "Active Directory Domain Services: guía de administración | Microsoft Docs"
description: "Creación de una unidad organizativa (OU) en dominios administrados de Servicios de dominio de Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 197696d737e56cbdc9fe925b6fa5b9e4134e1539
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Creación de una unidad organizativa en un dominio administrado de Servicios de dominio de Azure AD
Los dominios administrados de Servicios de dominio de Azure AD incluyen dos contenedores integrados denominados "AADDC Computers" y "AADDC Users", respectivamente. El contenedor "AADDC Computers" tiene objetos de equipo para todos los equipos que están unidos al dominio administrado. El contenedor "AADDC Users" incluye los usuarios y grupos del inquilino de Azure AD. En ocasiones, puede ser necesario crear cuentas de servicio en el dominio administrado para implementar las cargas de trabajo. Para ello, puede crear una unidad organizativa personalizada en el dominio administrado y crear cuentas de servicio dentro de esa unidad organizativa. En este artículo se muestra cómo crear una unidad organizativa en el dominio administrado.

## <a name="before-you-begin"></a>Antes de empezar
Para realizar las tareas enumeradas en este artículo, necesita lo siguiente:

1. Una **suscripción de Azure**válida.
2. Un **directorio de Azure AD** : sincronizado con un directorio local o solo en la nube.
3. **Servicios de dominio de Azure AD** deben estar habilitado en el directorio de Azure AD. Si no lo ha hecho, siga todas las tareas descritas en [Servicios de dominio de Azure AD (vista previa): introducción](active-directory-ds-getting-started.md).
4. Una máquina virtual unida a un dominio, desde la que administrará el dominio administrado de los Servicios de dominio de Azure AD. Si no tiene una máquina virtual, siga todas las tareas descritas en el artículo [Unión de una máquina virtual de Windows Server a un dominio administrado](active-directory-ds-admin-guide-join-windows-vm.md).
5. Necesitará las credenciales de una **cuenta de usuario que pertenezca al grupo "Administradores del controlador de dominio de AAD"** en el directorio con el fin de crear una unidad organizativa personalizada en el dominio administrado.

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Instalación de herramientas de administración de AD en una máquina virtual unida a dominio para la administración remota
Los dominios administrados con Servicios de dominio de Azure AD se pueden controlar de forma remota mediante las conocidas herramientas administrativas de Active Directory, como el Centro de administración de Active Directory (ADAC) o AD PowerShell. Los administradores de inquilinos no tienen privilegios para conectarse a controladores de dominio en el dominio administrado a través del Escritorio remoto. Para administrar el dominio administrado, instale la característica de herramientas de administración de AD en una máquina virtual unida al dominio administrado. Consulte las instrucciones en el artículo [Administración de un dominio administrado con Servicios de dominio de Azure AD](active-directory-ds-admin-guide-administer-domain.md) .

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Creación de una unidad organizativa en un dominio administrado
Ahora que se han instalado las herramientas administrativas de AD en la máquina virtual unida a dominio, podemos usarlas para crear una unidad organizativa en el dominio administrado. Lleve a cabo los siguiente pasos:

> [!NOTE]
> Los miembros del grupo AAD DC Administrators son los únicos que tienen los privilegios necesarios para crear una unidad organizativa personalizada. Asegúrese de seguir estos pasos como usuario que pertenece a este grupo.
>
>

1. En la pantalla Inicio, haga clic en **Herramientas administrativas**. Debería ver las herramientas administrativas de AD instaladas en la máquina virtual.

    ![Herramientas administrativas instaladas en el servidor](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Haga clic en **Centro de administración de Active Directory**.

    ![Centro de administración de Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Para ver el dominio, haga clic en el nombre de dominio en el panel izquierdo (por ejemplo, contoso100.com).

    ![ADAC: ver dominio](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. En el panel **Tareas** de la derecha, haga clic en la opción **Nuevo** del nodo del nombre de dominio. En este ejemplo, vamos a hacer clic en la opción **Nuevo** del nodo contoso100(local) del panel **Tareas** de la derecha.

    ![ADAC: nueva unidad organizativa](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. Debería ver la opción para crear una unidad organizativa. Haga clic en **Unidad organizativa** para abrir el cuadro de diálogo **Create Organizational Unit** (Crear unidad organizativa).
6. En el cuadro de diálogo **Create Organizational Unit** (Crear unidad organizativa), especifique un **Nombre** para la nueva unidad organizativa. Proporcione una breve descripción de la unidad organizativa. También puede establecer el campo **Administrado por** para la unidad organizativa. Haga clic en **Aceptar**para crear la unidad organizativa personalizada.

    ![ADAC: cuadro de diálogo de creación de unidad organizativa](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. La unidad organizativa recién creada debe aparecer ahora en el Centro de administración de AD.

    ![ADAC: unidad organizativa creada](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>Permisos/seguridad para unidades organizativas recién creadas
De forma predeterminada, al usuario (miembro del grupo AAD DC Administrators) que ha creado la unidad organizativa personalizada, se le conceden privilegios administrativos (control total) sobre dicha unidad. El usuario puede así seguir adelante y conceder privilegios a otros usuarios o al grupo "AAD DC Administrators" como desee. Tal como se muestra en la siguiente captura de pantalla, el usuario "bob@domainservicespreview.onmicrosoft.com", que ha creado la nueva unidad organizativa "MyCustomOU", ha recibido control total sobre ella.

 ![ADAC: seguridad de nueva unidad organizativa](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>Notas acerca de cómo administrar unidades organizativas personalizadas
Ahora que ha generado una unidad organizativa personalizada, puede seguir y crear en ella usuarios, grupos, equipos y cuentas de servicio. No podrá mover usuarios o grupos desde la unidad organizativa "AADDC Users" a unidades organizativas personalizadas.

> [!WARNING]
> Las cuentas de usuario, los grupos, las cuentas de servicio y los objetos de equipo que se creen en unidades organizativas personalizadas no estarán disponibles en el inquilino de Azure AD. En otras palabras: estos objetos no se mostrarán con la API Graph de Azure AD ni en la interfaz de usuario de Azure AD. Solo estarán disponibles en el dominio administrado de Azure AD Domain Services.
>
>

## <a name="related-content"></a>Contenido relacionado
* [Administer an Azure AD Domain Services managed domain (Administración de un dominio administrado con Servicios de dominio de Azure AD)](active-directory-ds-admin-guide-administer-domain.md)
* [Administración de directiva de grupo en un dominio administrado de Azure AD Domain Services](active-directory-ds-admin-guide-administer-group-policy.md)
* [Centro de administración de Active Directory: introducción](https://technet.microsoft.com/library/dd560651.aspx)
* [Guía paso a paso de las cuentas de servicio](https://technet.microsoft.com/library/dd548356.aspx)
