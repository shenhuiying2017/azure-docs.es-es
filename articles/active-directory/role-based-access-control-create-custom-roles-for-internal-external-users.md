---
title: Crear roles personalizados de control de acceso basado en roles y asignarlos a usuarios internos y externos en Azure | Microsoft Docs
description: Asignar roles personalizados de RBAC creados con PowerShell y la CLI para usuarios internos y externos
services: active-directory
documentationcenter: 
author: andreicradu
manager: mtillman
editor: kgremban
ms.assetid: 
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/06/2017
ms.author: a-crradu
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: b3b65812d453a9f7d93ee4381c4261e685a60376
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="intro-on-role-based-access-control"></a>Introducción al control de acceso basado en roles

El control de acceso basado en roles es una característica de Azure Portal que permite a los propietarios de una suscripción asignar roles granulares a otros usuarios que pueden administrar ámbitos de recursos específicos en su entorno.

RBAC permite una mejor administración de seguridad para organizaciones grandes y para PYMES que trabajan con colaboradores externos, proveedores o autónomos que necesitan tener acceso a recursos específicos de su entorno, pero no necesariamente a toda la infraestructura ni a los ámbitos relacionados con la facturación. RBAC proporciona la flexibilidad de tener una suscripción de Azure administrada por el administrador de la cuenta (rol de administrador de servicio en el nivel de suscripción) y tener múltiples usuarios invitados que trabajen con la misma suscripción pero sin tener derechos administrativos en ella. Desde una perspectiva de facturación y administración, la característica RBAC resulta ser una opción de administración eficaz y rápida para el uso de Azure en distintos escenarios.

## <a name="prerequisites"></a>Requisitos previos
El uso de RBAC en el entorno de Azure requiere:

* Tener una suscripción de Azure independiente asignada al usuario como propietario (rol de suscripción)
* Tener el rol de propietario de la suscripción de Azure
* Tener acceso a [Azure Portal](https://portal.azure.com)
* Asegúrese de tener los siguientes proveedores de recursos registrados para la suscripción del usuario: **Microsoft.Authorization**. Para más información sobre cómo registrar los proveedores de recursos, consulte [Proveedores del Administrador de recursos, regiones, versiones de API y esquemas](../azure-resource-manager/resource-manager-supported-services.md).

> [!NOTE]
> Las suscripciones de Office 365 y las licencias de Azure Active Directory (por ejemplo, Acceso a Azure Active Directory) aprovisionadas desde el Centro de administración de Office 365 no permiten el uso de RBAC.

## <a name="how-can-rbac-be-used"></a>Uso de RBAC
RBAC puede aplicarse en tres ámbitos diferentes de Azure. Del ámbito más alto al más bajo, son los siguientes:

* Suscripción (más alto)
* Grupos de recursos
* Ámbito de recurso (el nivel más bajo de acceso, que ofrece permisos destinados al ámbito de un recurso individual de Azure)

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Asignación de roles RBAC en el ámbito de la suscripción
Hay dos ejemplos comunes (entre otros) en los que se utiliza RBAC:

* Existencia de usuarios externos a las organizaciones (no forman parte del inquilino de Azure Active Directory del usuario administrador) invitados a administrar determinados recursos o la suscripción completa
* Existencia de usuarios dentro de la organización (forman parte del inquilino de Azure Active Directory del usuario administrador) que son parte de equipos o grupos distintos y necesitan acceso granular bien a la suscripción completa o a determinados grupos de recursos o ámbitos de recursos del entorno.

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Conceder acceso en el nivel de suscripción para un usuario fuera de Azure Active Directory
Solo los **Propietarios** de la suscripción pueden conceder roles RBAC, por tanto, el usuario administrador debe haber iniciado sesión con un nombre de usuario que tiene este rol asignado previamente o que ha creado la suscripción de Azure.

En Azure Portal, una vez que inicie sesión como administrador, seleccione "Suscripciones" y seleccione la suscripción deseada.
![Hoja Suscripciones en Azure Portal](./media/role-based-access-control-create-custom-roles-for-internal-external-users/0.png) De forma predeterminada, si el usuario administrador adquirió la suscripción de Azure, el usuario aparecerá como **Administrador de la cuenta**, siendo este el rol de la suscripción. Para más detalles sobre los roles de la suscripción de Azure, consulte [Adición o cambio de roles de administrador de Azure que administran la suscripción o los servicios](/billing/billing-add-change-azure-subscription-administrator.md).

En este ejemplo, el usuario "alflanigan@outlook.com" es el **Propietario** de la suscripción "Free Trial" en el inquilino de AAD "Default tenant Azure". Puesto que este usuario es el creador de la suscripción de Azure con la cuenta de Microsoft "Outlook" inicial (Microsoft Account = Outlook, Live, etc.), el nombre de dominio predeterminado para todos los demás usuarios agregados en este inquilino será **"@alflaniganuoutlook.onmicrosoft.com"**. Por diseño, la sintaxis del nuevo dominio se forma uniendo el nombre de usuario y el nombre de dominio del usuario que creó el inquilino y agregando la extensión **".onmicrosoft.com"**.
Además, los usuarios pueden iniciar sesión con un nombre de dominio personalizado en el inquilino después de agregarlo y comprobarlo para el nuevo inquilino. Para más información sobre cómo comprobar un nombre de dominio personalizado en un inquilino de Azure Active Directory, consulte [Agregar un nombre de dominio personalizado a su directorio](/active-directory/active-directory-add-domain).

En este ejemplo, el directorio "Default tenant Azure" contiene solo usuarios con el nombre de dominio "@alflanigan.onmicrosoft.com".

Después de seleccionar la suscripción, el usuario administrador debe hacer clic en **Control de acceso (IAM)** y, a continuación, en **Agregar un nuevo rol**.





![Característica de control de acceso IAM en Azure Portal](./media/role-based-access-control-create-custom-roles-for-internal-external-users/1.png)





![Agregar un nuevo usuario en la característica de control de acceso IAM en Azure Portal](./media/role-based-access-control-create-custom-roles-for-internal-external-users/2.png)

El siguiente paso es seleccionar el rol que se va a asignar y el usuario al que se va a asignar el rol de RBAC. En el menú desplegable **Rol** el usuario administrador ve únicamente los roles RBAC integrados que están disponibles en Azure. Para obtener explicaciones detalladas de cada rol y sus ámbitos asignables, consulte [Roles integrados en el control de acceso basado en roles de Azure](role-based-access-built-in-roles.md).

El usuario administrador, a continuación, debe agregar la dirección de correo electrónico del usuario externo. El comportamiento esperado para el usuario externo es que no aparezca en el inquilino existente. Cuando el usuario externo haya sido invitado, será visible en **Suscripciones > Control de acceso (IAM)** con todos los usuarios que están asignados actualmente a un rol de RBAC en el ámbito de la suscripción.





![Agregar permisos al nuevo rol de RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/3.png)





![Lista de roles RBAC en el nivel de suscripción](./media/role-based-access-control-create-custom-roles-for-internal-external-users/4.png)

El usuario "chessercarlton@gmail.com" ha sido invitado como **Propietario** de la suscripción "Free Trial". Después de enviar la invitación, el usuario externo recibirá una confirmación por correo electrónico con un enlace de activación.
![invitación por correo electrónico para el rol de RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/5.png)

Al ser un usuario externo a la organización, el nuevo usuario no tiene ningún atributo en el directorio "Default tenant Azure". Estos se crearán después de que el usuario externo haya dado su consentimiento para ser registrado en el directorio asociado con la suscripción para la que se le ha asignado el rol.





![Mensaje de invitación por correo electrónico para el rol de RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/6.png)

El usuario externo aparece en el inquilino de Azure Active Directory a partir de ahora como un usuario externo, y se puede ver en Azure Portal.





![Hoja de usuarios de Azure Active Directory en Azure Portal](./media/role-based-access-control-create-custom-roles-for-internal-external-users/7.png)



En la vista **Usuarios**, los usuarios externos pueden reconocerse por el tipo de icono diferente en Azure Portal.

Sin embargo, conceder acceso como **Propietario** o **Colaborador** a un usuario externo en el ámbito de la **Suscripción** no permite el acceso al directorio del usuario administrador, a menos que el **Administrador global** lo permita. En las propiedades del usuario, el **Tipo de usuario**, que tiene dos parámetros comunes, se pueden identificar **Miembro** e **Invitado**. Un miembro es un usuario que está registrado en el directorio, mientras que un invitado es un usuario invitado al directorio desde un origen externo. Para más información, consulte [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](active-directory-b2b-admin-add-users.md)

> [!NOTE]
> Asegúrese de que, después de escribir las credenciales en el portal, el usuario externo selecciona el directorio correcto en el que se iniciará sesión. El mismo usuario puede tener acceso a varios directorios y puede seleccionar cualquiera de ellos haciendo clic en el nombre de usuario en la parte superior derecha de Azure Portal y, a continuación, seleccionando el directorio adecuado en la lista desplegable.

Al ser un invitado en el directorio, el usuario externo puede administrar todos los recursos de la suscripción de Azure, pero no tiene acceso al directorio.





![Acceso restringido a Azure Active Directory en Azure Portal](./media/role-based-access-control-create-custom-roles-for-internal-external-users/9.png)

Azure Active Directory y una suscripción de Azure no tienen una relación de primario-secundario como tienen otros recursos de Azure (por ejemplo: las máquinas virtuales, las redes virtuales, las aplicaciones web, el almacenamiento, etc.) en una suscripción de Azure. Todo lo anterior se crea, administra y factura en una suscripción de Azure, mientras que se usa una suscripción de Azure para administrar el acceso a un directorio de Azure. Para más información, consulte [Cómo se relaciona una suscripción de Azure a Azure AD](/active-directory/active-directory-how-subscriptions-associated-directory).

De todos los roles integrados de RBAC, **Propietario** y **Colaborador** ofrecen acceso administrativo completo a todos los recursos del entorno, la diferencia radica en que un colaborador no puede crear y eliminar nuevos roles RBAC. Otros roles integrados, como **Colaborador de la máquina virtual**, ofrecen acceso de administración completa solo a los recursos indicados por el nombre, con independencia del **Grupo de recursos** en el que se creen.

Asignar el rol integrado de RBAC de **Colaborador de la máquina virtual** en el nivel de suscripción significa que el usuario al que se le ha asignado el rol:

* Puede ver todas las máquinas virtuales con independencia de su fecha de implementación y del grupo de recursos del que forman parte.
* Tiene acceso completo de administración a las máquinas virtuales de la suscripción.
* No puede ver ningún otro tipo de recursos de la suscripción.
* No puede realizar ningún cambio desde la perspectiva de facturación.

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Asignación de un rol integrado de RBAC a un usuario externo
Para un escenario diferente en esta prueba, el usuario externo "alflanigan@gmail.com" se agrega como un **Colaborador de la máquina virtual**.




![Rol integrado de colaborador de la máquina virtual](./media/role-based-access-control-create-custom-roles-for-internal-external-users/11.png)

El comportamiento normal para un usuario externo con este rol integrado es ver y administrar máquinas virtuales y solo sus recursos adyacentes necesarios de Resource Manager durante la implementación. Por diseño, estas roles limitados ofrecen acceso únicamente a sus recursos correspondientes creados en Azure Portal.



![Información general sobre el rol de colaborador de la máquina virtual en Azure Portal](./media/role-based-access-control-create-custom-roles-for-internal-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Concesión de acceso en el nivel de suscripción a un usuario del mismo directorio
El flujo del proceso es idéntico al agregar un usuario externo, tanto desde la perspectiva del administrador que concede el rol de RBAC como del usuario al que se concede el acceso al rol. La diferencia es que el usuario invitado no recibe ninguna invitación por correo electrónico ya que los ámbitos de recursos de la suscripción estarán disponibles en el panel después de iniciar sesión.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Asignación de roles de RBAC en el ámbito del grupo de recursos
El proceso de asignación de un rol de RBAC en un ámbito **Grupo de recursos** es idéntico al de asignar el rol en el nivel de suscripción para ambos tipos de usuarios: internos o externos (parte del mismo directorio). Los usuarios a los que se les ha asignado del rol de RBAC verán en su entorno únicamente el grupo de recursos al que se les asignó acceso desde el icono **Grupo de recursos** en Azure Portal.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Asignación de roles de RBAC en el ámbito del recurso
El proceso de asignación de un rol de RBAC en el ámbito del recurso en Azure es idéntico al de asignar un rol en el nivel de suscripción o de grupo de recursos, y se sigue el mismo flujo de trabajo en ambos escenarios. Del mismo modo, los usuarios a los que se ha asignado el rol de RBAC solo pueden ver los elementos a los que se les ha asignado acceso, tanto en la pestaña **Todos los recursos** como directamente en su panel.

Un aspecto importante de RBAC tanto en el ámbito de un grupo de recursos como en el de un recurso es asegurarse de que los usuarios inician sesión en el directorio correcto.





![Inicio de sesión en un directorio en Azure Portal](./media/role-based-access-control-create-custom-roles-for-internal-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Asignación de roles de RBAC en un grupo de Azure Active Directory
Todos los escenarios que usan RBAC en los tres ámbitos distintos de Azure ofrecen el privilegio de administrar e implementar diversos recursos como usuario asignado sin necesidad de administrar una suscripción personal. Aunque el rol de RBAC se asigna para un ámbito de suscripción, grupo de recursos o recurso, todos los recursos creados por los usuarios asignados se facturan a una suscripción de Azure a la que los usuarios tienen acceso. De este modo, los usuarios que tienen permisos de administrador de facturación para la suscripción de Azure tienen una información general completa del consumo, independientemente de quién administra los recursos.

Para organizaciones de mayor tamaño, los roles de RBAC se pueden aplicar del mismo modo para grupos de Azure Active Directory, dando por hecho que el usuario administrador quiera conceder acceso granular a equipos o departamentos enteros y no individualmente a cada usuario, considerándose así una opción muy eficiente en tiempo y administración. Para ilustrar este ejemplo, el rol **Colaborador** se ha agregado a uno de los grupos en el inquilino en el nivel de suscripción.





![Agregar rol de RBAC para grupos de AAD](./media/role-based-access-control-create-custom-roles-for-internal-external-users/14.png)

Estos grupos son grupos de seguridad que se aprovisionan y administran únicamente dentro de Azure Active Directory.

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-powershell"></a>Creación de un rol de RBAC personalizado para abrir solicitudes de soporte técnico con PowerShell
Los roles de RBAC integrados disponibles en Azure aseguran ciertos niveles de permisos en función de los recursos disponibles en el entorno. Sin embargo, si ninguno de estos roles se ajusta a las necesidades del usuario administrador, existe la opción de limitar aún más el acceso mediante la creación de roles de RBAC personalizados.

La creación de un rol de RBAC personalizado implica tomar un rol integrado, editarlo e importarlo de vuelta en el entorno. La descarga y la carga del rol se administran mediante PowerShell o la CLI.

Es importante entender los requisitos previos para la creación de un rol personalizado que pueda conceder acceso granular en el nivel de suscripción y también dar al usuario invitado la flexibilidad de abrir solicitudes de soporte técnico.

En este ejemplo, el rol integrado **Lector**, que otorga a los usuarios acceso para ver todos los ámbitos de recurso pero no editarlos ni crear nuevos, ha sido personalizado para permitir al usuario la opción de abrir solicitudes de soporte técnico.

La primera acción de exportación del rol **Lector** debe realizarse en PowerShell ejecutado con permisos elevados como administrador.

```
Login-AzureRMAccount

Get-AzureRMRoleDefinition -Name "Reader"

Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json

```





![Captura de pantalla de PowerShell para el rol Lector de RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/15.png)

A continuación, debe extraer la plantilla JSON del rol.





![Plantilla JSON para el rol personalizado Lector de RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/16.png)

Un rol de RBAC típico se compone de tres secciones principales, **Actions**, **NotActions** y **AssignableScopes**.

En la sección **Action** se enumeran todas las operaciones permitidas para este rol. Es importante comprender que cada acción se asigna desde un proveedor de recursos. En este caso, para la creación de incidencias de soporte técnico, el proveedor de recursos **Microsoft.Support** debe aparecer enumerado.

Para poder ver todos los proveedores de recursos disponibles y registrados en su suscripción, puede usar PowerShell.
```
Get-AzureRMResourceProvider

```
Además, puede comprobar todos los cmdlets de PowerShell disponibles para administrar proveedores de recursos.
    ![Captura de pantalla de PowerShell para la administración de proveedores de recursos](./media/role-based-access-control-create-custom-roles-for-internal-external-users/17.png)

Para restringir todas las acciones de un rol determinado de RBAC, los proveedores de recursos aparecen en la sección **NotActions**.
Por último, es obligatorio del rol de RBAC contenga los identificadores explícitos de suscripción en las que es utilizado. Los identificadores de suscripción aparecen en la sección **AssignableScopes**, en caso contrario, no podrá importar el rol en su suscripción.

Después de crear y personalizar el rol de RBAC, debe importarse de vuelta en el entorno.

```
New-AzureRMRoleDefinition -InputFile "C:\rbacrole2.json"

```

En este ejemplo, el nombre personalizado para este rol de RBAC es "Reader support tickets access level" y permite al usuario ver todo en la suscripción y también para abrir solicitudes de soporte técnico.

> [!NOTE]
> Los dos únicos roles integrados de RBAC que permiten la acción de apertura de solicitudes de soporte técnico son **Propietario** y **Colaborador**. Para que un usuario sea capaz de abrir solicitudes de soporte técnico, se le debe asignar un rol de RBAC solo en el ámbito de la suscripción, ya que todas las solicitudes de soporte técnico se crean en función de una suscripción de Azure.

Este nuevo rol personalizado se ha asignado a un usuario del mismo directorio.





![Captura de pantalla de un rol personalizado de RBAC importado en Azure Portal](./media/role-based-access-control-create-custom-roles-for-internal-external-users/18.png)





![Captura de pantalla de la asignación de un rol importado personalizado de RBAC a un usuario del mismo directorio](./media/role-based-access-control-create-custom-roles-for-internal-external-users/19.png)





![Captura de pantalla de los permisos de un rol importado personalizado de RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/20.png)

Este ejemplo se ha detallado más para destacar los límites de este rol de RBAC personalizado, como se indica a continuación:
* Puede crear nuevas solicitudes de soporte técnico
* No puede crear nuevos ámbitos de recursos (por ejemplo: máquinas virtuales)
* No puede crear nuevos grupos de recursos





![Captura de pantalla de un rol personalizado de RBAC que puede crear solicitudes de soporte técnico](./media/role-based-access-control-create-custom-roles-for-internal-external-users/21.png)





![Captura de pantalla de un rol personalizado de RBAC que no puede crear máquinas virtuales](./media/role-based-access-control-create-custom-roles-for-internal-external-users/22.png)





![Captura de pantalla de un rol personalizado de RBAC que no puede crear nuevos grupos de recursos](./media/role-based-access-control-create-custom-roles-for-internal-external-users/23.png)

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-azure-cli"></a>Creación de un rol personalizado de RBAC para abrir solicitudes de soporte técnico con la CLI de Azure
Trabajando en un Mac y sin tener acceso a PowerShell, la CLI de Azure es la mejor opción.

Los pasos para crear un rol personalizado son los mismos, con la única excepción de que con la CLI el rol no se puede descargar en una plantilla JSON, pero puede verse en la CLI.

En este ejemplo se ha elegido el rol integrado **Lector de copia de seguridad**.

```

azure role show "backup reader" --json

```





![Captura de pantalla de la CLI que muestra un rol de lector de copia de seguridad](./media/role-based-access-control-create-custom-roles-for-internal-external-users/24.png)

Al editar el rol en Visual Studio después de copiar las propiedades en una plantilla JSON, el proveedor de recursos **Microsoft.Support** se ha agregado en la sección **Acciones** para que este usuario pueda abrir solicitudes de soporte técnico mientras sigue siendo un lector para los almacenes de copia de seguridad. De nuevo, es necesario agregar el identificador de suscripción en la que se usará este rol en la sección **AssignableScopes**.

```

azure role create --inputfile <path>

```





![Captura de pantalla de la CLI con la importación de un rol personalizado de RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/25.png)

El nuevo rol ahora está disponible en Azure Portal y el proceso de asignación es el mismo que en los ejemplos anteriores.





![Captura de pantalla de Azure Portal de un rol personalizado de RBAC creado con la CLI 1.0](./media/role-based-access-control-create-custom-roles-for-internal-external-users/26.png)

A partir de la última compilación de 2017, Azure Cloud Shell está disponible con carácter general. Azure Cloud Shell es un complemento del IDE y de Azure Portal. Con este servicio, tiene un shell basado en explorador, autenticado y hospedado en Azure, que puede usar en lugar de la CLI instalada en su equipo.





![Azure Cloud Shell](./media/role-based-access-control-create-custom-roles-for-internal-external-users/27.png)
