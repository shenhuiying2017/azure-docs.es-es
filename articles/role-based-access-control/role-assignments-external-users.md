---
title: Administración de asignaciones de roles para los usuarios externos en Azure | Microsoft Docs
description: Administración del control de acceso basado en rol (RBAC) en Azure para los usuarios externos a una organización
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: kgremban
ms.assetid: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: dc0d91482637e3ade1147083b6d9c890e2798036
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34160383"
---
# <a name="manage-role-assignments-for-external-users"></a>Administración de asignaciones de roles para los usuarios externos

El control de acceso basado en rol (RBAC) permite una mejor administración de seguridad para organizaciones grandes y para PYMES que trabajan con colaboradores externos, proveedores o autónomos que necesitan tener acceso a recursos específicos de su entorno, pero no necesariamente a toda la infraestructura ni a los ámbitos relacionados con la facturación. RBAC proporciona la flexibilidad de tener una suscripción de Azure administrada por el administrador de la cuenta (rol de administrador de servicio en el nivel de suscripción) y tener múltiples usuarios invitados que trabajen con la misma suscripción pero sin tener derechos administrativos en ella.

> [!NOTE]
> Las suscripciones de Office 365 y las licencias de Azure Active Directory (por ejemplo, Acceso a Azure Active Directory) aprovisionadas desde el Centro de administración de Office 365 no permiten el uso de RBAC.

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Asignación de roles RBAC en el ámbito de la suscripción

Hay dos ejemplos comunes (entre otros) en los que se utiliza RBAC:

* Existencia de usuarios externos a las organizaciones (no forman parte del inquilino de Azure Active Directory del usuario administrador) invitados a administrar determinados recursos o la suscripción completa
* Existencia de usuarios dentro de la organización (forman parte del inquilino de Azure Active Directory del usuario) que son parte de equipos o grupos distintos y necesitan acceso granular bien a la suscripción completa o a determinados grupos de recursos o ámbitos de recursos del entorno.

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Conceder acceso en el nivel de suscripción para un usuario fuera de Azure Active Directory

Solo los **Propietarios** de la suscripción pueden conceder roles RBAC. Por lo tanto, el administrador debe iniciar sesión como un usuario que tenga este rol asignado previamente o que haya creado la suscripción de Azure.

En Azure Portal, una vez que inicie sesión como administrador, seleccione "Suscripciones" y seleccione la suscripción deseada.
![Hoja Suscripciones en Azure Portal](./media/role-assignments-external-users/0.png) De forma predeterminada, si el usuario administrador adquirió la suscripción de Azure, el usuario aparecerá como **Administrador de la cuenta**, siendo este el rol de la suscripción. Para más información sobre los roles de la suscripción de Azure, consulte [Adición o cambio de roles de administrador de Azure que administran la suscripción o los servicios](../billing/billing-add-change-azure-subscription-administrator.md).

En este ejemplo, el usuario "alflanigan@outlook.com" es el **Propietario** de la suscripción "Free Trial" en el inquilino de AAD "Default tenant Azure". Puesto que este usuario es el creador de la suscripción de Azure con la cuenta de Microsoft "Outlook" inicial (Microsoft Account = Outlook, Live, etc.), el nombre de dominio predeterminado para todos los demás usuarios agregados en este inquilino será **"@alflaniganuoutlook.onmicrosoft.com"**. Por diseño, la sintaxis del nuevo dominio se forma uniendo el nombre de usuario y el nombre de dominio del usuario que creó el inquilino y agregando la extensión **".onmicrosoft.com"**.
Además, los usuarios pueden iniciar sesión con un nombre de dominio personalizado en el inquilino después de añadirlo y comprobarlo para el nuevo inquilino. Para más información sobre cómo comprobar un nombre de dominio personalizado en un inquilino de Azure Active Directory, consulte [Agregar un nombre de dominio personalizado a su directorio](/active-directory/active-directory-add-domain).

En este ejemplo, el directorio "Default tenant Azure" contiene solo usuarios con el nombre de dominio "@alflanigan.onmicrosoft.com".

Después de seleccionar la suscripción, el usuario administrador debe hacer clic en **Control de acceso (IAM)** y, a continuación, en **Agregar un nuevo rol**.

![Característica de control de acceso IAM en Azure Portal](./media/role-assignments-external-users/1.png)

![Agregar un nuevo usuario en la característica de control de acceso IAM en Azure Portal](./media/role-assignments-external-users/2.png)

El siguiente paso es seleccionar el rol que se va a asignar y el usuario al que se va a asignar el rol de RBAC. En el menú desplegable **Rol**, el usuario administrador ve únicamente los roles RBAC integrados que están disponibles en Azure. Para obtener explicaciones detalladas de cada rol y sus ámbitos asignables, consulte [Roles integrados en el control de acceso basado en roles de Azure](built-in-roles.md).

El usuario administrador, a continuación, debe agregar la dirección de correo electrónico del usuario externo. El comportamiento esperado para el usuario externo es que no aparezca en el inquilino existente. Cuando el usuario externo haya sido invitado, será visible en **Suscripciones > Control de acceso (IAM)** con todos los usuarios que están asignados actualmente a un rol de RBAC en el ámbito de la suscripción.

![Agregar permisos al nuevo rol de RBAC](./media/role-assignments-external-users/3.png)

![Lista de roles RBAC en el nivel de suscripción](./media/role-assignments-external-users/4.png)

El usuario "chessercarlton@gmail.com" ha sido invitado como **Propietario** de la suscripción "Free Trial". Después de enviar la invitación, el usuario externo recibirá una confirmación por correo electrónico con un enlace de activación.
![invitación por correo electrónico para el rol de RBAC](./media/role-assignments-external-users/5.png)

Al ser un usuario externo a la organización, el nuevo usuario no tiene ningún atributo en el directorio "Default tenant Azure". Estos se crearán después de que el usuario externo haya dado su consentimiento para ser registrado en el directorio asociado con la suscripción para la que se le ha asignado el rol.

![Mensaje de invitación por correo electrónico para el rol de RBAC](./media/role-assignments-external-users/6.png)

El usuario externo aparece en el inquilino de Azure Active Directory a partir de ahora como un usuario externo, y se puede ver en Azure Portal.

![Hoja de usuarios de Azure Active Directory en Azure Portal](./media/role-assignments-external-users/7.png)

En la vista **Usuarios**, los usuarios externos pueden reconocerse por el tipo de icono diferente en Azure Portal.

Sin embargo, conceder acceso como **Propietario** o **Colaborador** a un usuario externo en el ámbito de la **Suscripción** no permite el acceso al directorio del usuario administrador, a menos que el **Administrador global** lo permita. En las propiedades del usuario, se puede identificar el **Tipo de usuario**, que tiene dos parámetros comunes, **Miembro** e **Invitado**. Un miembro es un usuario que está registrado en el directorio, mientras que un invitado es un usuario invitado al directorio desde un origen externo. Para más información, consulte [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](../active-directory/active-directory-b2b-admin-add-users.md)

> [!NOTE]
> Asegúrese de que, después de escribir las credenciales en el portal, el usuario externo selecciona el directorio correcto en el que se iniciará sesión. El mismo usuario puede tener acceso a varios directorios y puede seleccionar cualquiera de ellos haciendo clic en el nombre de usuario en la parte superior derecha de Azure Portal y, a continuación, seleccionando el directorio adecuado en la lista desplegable.

Al ser un invitado en el directorio, el usuario externo puede administrar todos los recursos de la suscripción de Azure, pero no tiene acceso al directorio.

![Acceso restringido a Azure Active Directory en Azure Portal](./media/role-assignments-external-users/9.png)

Azure Active Directory y una suscripción de Azure no tienen una relación de primario-secundario como tienen otros recursos de Azure (por ejemplo: las máquinas virtuales, las redes virtuales, las aplicaciones web, el almacenamiento, etc.) en una suscripción de Azure. Todo lo anterior se crea, administra y factura en una suscripción de Azure, mientras que se usa una suscripción de Azure para administrar el acceso a un directorio de Azure. Para más información, consulte [Cómo se relaciona una suscripción de Azure a Azure AD](/active-directory/active-directory-how-subscriptions-associated-directory).

De todos los roles integrados de RBAC, **Propietario** y **Colaborador** ofrecen acceso administrativo completo a todos los recursos del entorno, la diferencia radica en que un colaborador no puede crear y eliminar nuevos roles RBAC. Otros roles integrados, como **Colaborador de la máquina virtual**, ofrecen acceso de administración completa solo a los recursos indicados por el nombre, con independencia del **Grupo de recursos** en el que se creen.

Asignar el rol integrado de RBAC de **Colaborador de la máquina virtual** en el nivel de suscripción significa que el usuario al que se le ha asignado el rol:

* Puede ver todas las máquinas virtuales con independencia de su fecha de implementación y del grupo de recursos del que forman parte.
* Tiene acceso completo de administración a las máquinas virtuales de la suscripción.
* No puede ver ningún otro tipo de recursos de la suscripción.
* No puede realizar ningún cambio desde la perspectiva de facturación.

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Asignación de un rol integrado de RBAC a un usuario externo

Para un escenario diferente en esta prueba, el usuario externo "alflanigan@gmail.com" se agrega como un **Colaborador de la máquina virtual**.

![Rol integrado de colaborador de la máquina virtual](./media/role-assignments-external-users/11.png)

El comportamiento normal para un usuario externo con este rol integrado es ver y administrar máquinas virtuales y solo sus recursos adyacentes necesarios de Resource Manager durante la implementación. Por diseño, estas roles limitados ofrecen acceso únicamente a sus recursos correspondientes creados en Azure Portal.

![Información general sobre el rol de colaborador de la máquina virtual en Azure Portal](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Concesión de acceso en el nivel de suscripción a un usuario del mismo directorio

El flujo del proceso es idéntico al agregar un usuario externo, tanto desde la perspectiva del administrador que concede el rol de RBAC como del usuario al que se concede el acceso al rol. La diferencia es que el usuario invitado no recibe ninguna invitación por correo electrónico ya que los ámbitos de recursos de la suscripción estarán disponibles en el panel después de iniciar sesión.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Asignación de roles de RBAC en el ámbito del grupo de recursos

El proceso de asignación de un rol de RBAC en un ámbito **Grupo de recursos** es idéntico al de asignar el rol en el nivel de suscripción para ambos tipos de usuarios: internos o externos (parte del mismo directorio). Los usuarios a los que se les ha asignado del rol de RBAC verán en su entorno únicamente el grupo de recursos al que se les asignó acceso desde el icono **Grupo de recursos** en Azure Portal.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Asignación de roles de RBAC en el ámbito del recurso

El proceso de asignación de un rol de RBAC en el ámbito del recurso en Azure es idéntico al de asignar un rol en el nivel de suscripción o de grupo de recursos, y se sigue el mismo flujo de trabajo en ambos escenarios. Del mismo modo, los usuarios a los que se ha asignado el rol de RBAC solo pueden ver los elementos a los que se les ha asignado acceso, tanto en la pestaña **Todos los recursos** como directamente en su panel.

Un aspecto importante de RBAC tanto en el ámbito de un grupo de recursos como en el de un recurso es asegurarse de que los usuarios inician sesión en el directorio correcto.

![Inicio de sesión en un directorio en Azure Portal](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Asignación de roles de RBAC en un grupo de Azure Active Directory

Todos los escenarios que usan RBAC en los tres ámbitos distintos de Azure ofrecen el privilegio de administrar e implementar diversos recursos como usuario asignado sin necesidad de administrar una suscripción personal. Aunque el rol de RBAC se asigna para un ámbito de suscripción, grupo de recursos o recurso, todos los recursos creados por los usuarios asignados se facturan a una suscripción de Azure a la que los usuarios tienen acceso. De este modo, los usuarios que tienen permisos de administrador de facturación para la suscripción de Azure tienen una información general completa del consumo, independientemente de quién administra los recursos.

Para organizaciones de mayor tamaño, los roles de RBAC se pueden aplicar del mismo modo para grupos de Azure Active Directory, dando por hecho que el usuario administrador quiera conceder acceso granular a equipos o departamentos enteros y no individualmente a cada usuario, considerándose así una opción muy eficiente en tiempo y administración. Para ilustrar este ejemplo, el rol **Colaborador** se ha agregado a uno de los grupos en el inquilino en el nivel de suscripción.

![Agregar rol de RBAC para grupos de AAD](./media/role-assignments-external-users/14.png)

Estos grupos son grupos de seguridad que se aprovisionan y administran únicamente dentro de Azure Active Directory.

