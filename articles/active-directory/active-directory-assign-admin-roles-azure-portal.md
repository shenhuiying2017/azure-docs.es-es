---
title: "Asignación de roles de administrador en Azure Active Directory | Microsoft Docs"
description: "Un rol de administrador puede agregar usuarios, asignar roles administrativos, restablecer contraseñas de usuario, administrar licencias de usuario o administrar dominios. Un usuario que tiene asignado el rol de administrador tiene los mismos permisos en todos los servicios en la nube a la que se ha suscrito su organización."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 7fc27e8e-b55f-4194-9b8f-2e95705fb731
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro;
ms.openlocfilehash: 918e1c535ea8779a8aff3d94c6f1ef2cb2a17fd5
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Asignación de roles de administrador en Azure Active Directory

Con Azure Active Directory (Azure AD), puede designar administradores independientes que desempeñen distintas funciones. Los administradores tienen acceso a varias características de Azure Portal y, según el rol que tienen, pueden crear o editar usuarios, asignar roles administrativos a otros, restablecer contraseñas de usuario o administrar licencias de usuario y dominios, entre otras funciones. Un usuario al que se haya asignado un rol de administrador tendrá los mismos permisos en todos los servicios en la nube a los que se haya suscrito la organización, independientemente de si ha asignado el rol en el Portal de Office 365, en Azure Portal o por medio del módulo de Azure AD para Windows PowerShell.

## <a name="details-about-the-global-administrator-role"></a>Detalles acerca del rol de administrador global
El administrador global tiene acceso a todos los roles administrativos. De forma predeterminada, a la persona que se suscribe a una suscripción de Azure se le asigna el rol de administrador global para el directorio. Los administradores globales son los únicos que pueden asignar otros roles de administrador.

## <a name="assign-or-remove-administrator-roles"></a>Asignación o eliminación de roles de administrador
Para obtener información sobre cómo asignar roles administrativos a un usuario en Azure Active Directory, consulte [Assign a user to administrator roles in Azure Active Directory](active-directory-users-assign-role-azure-portal.md) (Asignación de roles de administrador a un usuario en Azure Active Directory).

## <a name="available-roles"></a>Roles disponibles
Los roles de administrador disponibles son los siguientes:

* **Administrador de facturación**: hace compras, administra suscripciones, administra incidencias de soporte técnico y supervisa el estado del servicio.

* **Administrador de cumplimiento**: los usuarios con este rol tienen permisos de administración en el Centro de cumplimiento y seguridad de Office 365 y el Centro de administración de Exchange. Más información en "[Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)".

* **Administrador de acceso condicional**: los usuarios con este rol tienen la capacidad de administrar la configuración de acceso condicional de Azure Active Directory.
  > [!NOTE]
  > Para implementar la directiva de acceso condicional de Exchange ActiveSync en Azure, el usuario también debe ser administrador global.
  
* **Administrador del servicio CRM**: los usuarios con este rol tienen permisos globales en Microsoft CRM Online, cuando el servicio está presente, así como también la capacidad de administrar incidencias de soporte técnico y supervisar el estado del servicio. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administradores de dispositivos**: los usuarios con este rol se convierten en administradores de la máquina virtual en todos los dispositivos de Windows 10 que están unidos a Azure Active Directory. No tienen la capacidad de administrar objetos de dispositivos en Azure Active Directory.

* **Lectores de directorio**: se trata de un rol heredado que se asigna a las aplicaciones que no admiten el [marco de consentimiento](active-directory-integrating-applications.md). No se debe asignar a ningún usuario.

* **Cuentas de sincronización de directorio**: no se usan. Este rol se asigna automáticamente al servicio de Azure AD Connect y no está previsto ni se admite para ningún otro uso.

* **Lectores de directorio**: es un rol heredado que se asigna a las aplicaciones que no admiten el [marco de consentimiento](active-directory-integrating-applications.md). No se debe asignar a ningún usuario.

* **Administrador de servicios de Exchange**: los usuarios con este rol tienen permisos globales en Microsoft Exchange Online, cuando el servicio está presente. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrador global/administrador de la compañía**: los usuarios con este rol tienen acceso a todas las características administrativas en Azure Active Directory, así como también los servicios que se federan con Azure Active Directory, como Exchange Online, SharePoint Online y Skype Empresarial Online. La persona que se suscribe al inquilino de Azure Active Directory se convierte en un administrador global. Los administradores globales son los únicos que pueden asignar otros roles de administrador. Puede haber más de un administrador global en su empresa. Los administradores globales pueden restablecer la contraseña de todos los usuarios y de todos los demás administradores.

  > [!NOTE]
  > En Microsoft Graph API, Graph API de Azure AD, y Azure AD PowerShell, este rol se identifica como "administrador de la compañía". Es "administrador Global" en [Azure Portal](https://portal.azure.com).
  >
  >

* **Invitador de usuarios**: los usuarios con este rol pueden administrar las invitaciones de usuarios invitados de Azure Active Directory B2B cuando la configuración de usuario "Los miembros pueden invitar a otras personas" está establecida en No. Más información sobre la colaboración B2B en [Información sobre la versión preliminar de colaboración B2B de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). No incluye otros permisos.

* **Administrador de Information Protection**: los usuarios con este rol pueden acceder a Azure Information Protection en Azure Portal. Pueden configurar las etiquetas de la directiva de Azure Information Protection, administrar plantillas de protección y activar la protección.

* **Administrador de servicios de Intune**: los usuarios con este rol tienen permisos globales en Microsoft Intune Online, cuando el servicio está presente. Además, este rol contiene la capacidad de administrar usuarios y dispositivos para asociar una directiva, así como también para crear y administrar grupos.

* **Administrador de buzones de correo**: este rol solo se usa como parte de la compatibilidad con el correo electrónico de Exchange Online para dispositivos RIM Blackberry. Si su organización no usa el correo electrónico de Exchange Online en dispositivos RIM Blackberry, no utilice este rol.

* **Compatibilidad con el nivel 1 de asociado**: no lo utilice. Este rol está en desuso y se quitará de Azure AD en el futuro. Este rol está diseñado para que lo usen un pequeño número de asociados de reventa de Microsoft, no para un uso general.

* **Compatibilidad con el nivel 2 de asociado**: no lo utilice. Este rol está en desuso y se quitará de Azure AD en el futuro. Este rol está diseñado para que lo usen un pequeño número de asociados de reventa de Microsoft, no para un uso general.

* **Administrador de contraseñas/Administrador del departamento de soporte técnico**: los usuarios con este rol pueden cambiar contraseñas, administrar solicitudes de servicio y supervisar el estado del servicio. Los administradores del departamento de soporte técnico pueden cambiar contraseñas solo para los usuarios y otros administradores del mismo departamento. 

  > [!NOTE]
  > En Microsoft Graph API, Graph API de Azure AD y Azure AD PowerShell, este rol se identifica como "Administrador de soporte técnico". En "Administrador de contraseñas" en [Azure Portal](https://portal.azure.com/).
  >
  >
  
* **Administrador del servicio Power BI**: los usuarios con este rol tienen permisos globales en Microsoft Power BI, cuando el servicio está presente, así como también la capacidad de administrar incidencias de soporte técnico. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-001&ad=US).

* **Administrador de roles con privilegios**: los usuarios con este rol pueden administrar asignaciones de rol en Azure Active Directory, así como también dentro de Azure AD Privileged Identity Management. Además, este rol permite administrar todos los aspectos de Privileged Identity Management.

* **Lector de informes**: los usuarios con este rol pueden ver datos de informes de uso y el panel de informes en el centro de administración de Office 365 y el paquete del contexto de adopción en PowerBI. Además, el rol proporciona acceso a informes de inicio de sesión y a actividad de Azure AD y a los datos devueltos por la API de informes de Microsoft Graph. Un usuario asignado al rol de lector de informes puede acceder solo a métricas de uso y adopción pertinentes. No tienen permisos de administrador para configurar valores ni acceder a los centros de administración específicos de productos como Exchange. 

* **Administrador de seguridad**: los usuarios con este rol tienen todos los permisos de solo lectura del rol Lector de seguridad más la capacidad de administrar la configuración de servicios relacionados con la seguridad: Azure Active Directory Identity Protection, Azure Information Protection, Privileged Identity Management y Centro de seguridad y cumplimiento de Office 365. Encuentre más información disponible sobre los permisos de Office 365 en [Permisos del Centro de seguridad y cumplimiento de Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Lector de seguridad**: los usuarios con este rol tienen acceso global de solo lectura, incluida toda información en Azure Active Directory, Identity Protection, Privileged Identity Management, así como también la capacidad de leer registros de auditoría e informes de inicio de sesión de Azure Active Directory. El rol también concede permiso de solo lectura en el Centro de seguridad y cumplimiento de Office 365. Encuentre más información disponible sobre los permisos de Office 365 en [Permisos del Centro de seguridad y cumplimiento de Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Administrador de soporte técnico de servicios**: los usuarios con este rol pueden abrir incidencias de soporte técnico con Microsoft para servicios de Azure y Office 365 y consultan el centro de mensajes y el panel de servicios en Azure Portal y el portal de administración de Office 365. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrador del servicio SharePoint**: los usuarios con este rol tienen permisos globales en Microsoft SharePoint Online, cuando el servicio está presente, así como también la capacidad de administrar incidencias de soporte técnico y supervisar el estado del servicio. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrador del servicio Skype Empresarial/Lync**: los usuarios con este rol tienen permisos globales en Microsoft Skype Empresarial, cuando el servicio está presente, así como también pueden administrar los atributos de usuario específicos de Skype en Azure Active Directory. Además, este rol concede la capacidad de administrar incidencias de soporte técnico y supervisar el estado del servicio. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  > [!NOTE]
  > En Microsoft Graph API, Graph API de Azure AD y Azure AD PowerShell, este rol se identifica como "Administrador de servicio de Lync". Es "Administrador del servicio Skype Empresarial" en [Azure Portal](https://portal.azure.com/).
  >
  >

* **Administrador de cuentas de usuario**: los usuarios con este rol pueden crear y administrar todos los aspectos de usuarios y grupos. Además, este rol incluye la capacidad de administrar incidencias de soporte técnico y supervisar el estado del servicio. Se aplican algunas restricciones. Por ejemplo, este rol no permite eliminar un administrador global. Los administradores de cuentas de usuario solo pueden cambiar las contraseñas de los usuarios, de los administradores del departamento de soporte técnico y de otros administradores de cuentas de usuario.

## <a name="administrator-permissions"></a>Permisos de administrador

### <a name="billing-administrator"></a>Administrador de facturación

| Puede hacer | No puede hacer |
| --- | --- |
|<p>Ver información de usuario y de la compañía</p><p>Administrar incidencias de soporte técnico de Office</p><p>Realizar operaciones de facturación y compra productos de Office</p> |<p>Restablecer las contraseñas de los usuarios</p><p>Crear y administrar vistas de usuario</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuario</p><p>Administrar dominios</p><p>Administrar información de la compañía</p><p>Delegar roles administrativos a otros usuarios</p><p>Usar la sincronización de directorios</p><p>Visualización de registros de auditoría</p>|

### <a name="conditional-access-administrator"></a>Administrador de acceso condicional

| Puede hacer | No puede hacer |
| --- | --- |
|<p>Ver información de usuario y de la compañía</p><p>Administrar la configuración de acceso condicional</p> |<p>Restablecer las contraseñas de los usuarios</p><p>Crear y administrar vistas de usuario</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuario</p><p>Administrar dominios</p><p>Administrar información de la compañía</p><p>Delegar roles administrativos a otros usuarios</p><p>Usar la sincronización de directorios</p><p>Visualización de registros de auditoría</p>|

### <a name="global-administrator"></a>Administrador global
| Puede hacer | No puede hacer |
| --- | --- |
|<p>Ver información de usuario y de la compañía</p><p>Administrar incidencias de soporte técnico de Office</p><p>Realizar operaciones de facturación y compra productos de Office</p><p>Restablecer las contraseñas de los usuarios</p><p>Restablecer las contraseñas de otro administrador</p> <p>Crear y administrar vistas de usuario</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuario</p><p>Administrar dominios</p><p>Administrar información de la compañía</p><p>Delegar roles administrativos a otros usuarios</p><p>Usar la sincronización de directorios</p><p>Habilitar o deshabilitar Multi-Factor Authentication</p><p>Visualización de registros de auditoría</p> |N/D |

### <a name="password-administrator--helpdesk-administrator"></a>Administrador de contraseñas/Administrador del departamento de soporte técnico
| Puede hacer | No puede hacer |
| --- | --- |
| <p>Ver información de usuario y de la compañía</p><p>Administrar incidencias de soporte técnico de Office</p><p>Cambiar las contraseñas de los usuarios y de otros administradores del departamento de soporte técnico solamente</p>|<p>Realizar operaciones de facturación y compra productos de Office</p><p>Crear y administrar vistas de usuario</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuario</p><p>Administrar dominios</p><p>Administrar información de la compañía</p><p>Delegar roles administrativos a otros usuarios</p><p>Usar la sincronización de directorios</p><p>Ver informes</p>|

### <a name="information-protection-administrator"></a>Administrador de Information Protection 
En el | Puede hacer
-------- | ---------
Azure Information Protection | * Configurar etiquetas y directivas de ámbito global<br>* Administrar, configurar y actualizar plantillas de protección<br>* Habilitar la activación de la protección para su uso 
Privileged Identity Management | * Permisos de lectura<br>* No puede administrar la configuración de roles de Azure AD ni la pertenencia a ellos
 
### <a name="reports-reader"></a>Lector de informes 
Puede hacer | No puede hacer
------ | ----------
Ver registros de auditoría e informes de inicio de sesión de Azure AD<br>Ver información de usuario y de la compañía<br>Acceder al panel de uso de Office 365 | Crear y administrar vistas de usuario<br>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuario<br>Delegar roles administrativos a otros usuarios<br>Administrar información de la compañía

### <a name="security-reader"></a>Lector de seguridad
| En el | Puede hacer |
| --- | --- |
| Identity Protection Center |Leer todos los informes de seguridad y la información de configuración de las características de seguridad<ul><li>Filtro de correo no deseado<li>Cifrado<li>Prevención de la pérdida de datos<li>Antimalware<li>Protección contra amenazas avanzada<li>Protección contra suplantación de identidad (anti-phishing)<li>Reglas de flujo de correo |
| Privileged Identity Management |<p>Tiene acceso de solo lectura a toda la información que aparece en PIM de Azure AD: directivas e informes para las asignaciones de roles de Azure AD, revisiones de seguridad y, en el futuro, acceso de lectura a datos e informes de directivas en escenarios distintos a la asignación de roles de Azure AD.<p>**No puede** registrarse para PIM de Azure AD ni realizar ningún cambio en esta característica. En el portal de PIM o a través de PowerShell, alguien con este rol puede activar roles adicionales (por ejemplo, Administrador Global o Administrador de rol con privilegios), si el usuario es un candidato para ellos. |
| <p>Supervisión de estado del servicio de Office 365</p><p>Centro de cumplimiento y seguridad de Office 365</p> |<ul><li>Leer y administrar alertas<li>Leer directivas de seguridad<li>Leer inteligencia de amenazas, Cloud App Discovery y cuarentena en búsqueda e investigación<li>Leer todos los informes |

### <a name="security-administrator"></a>Administrador de seguridad
| En el | Puede hacer |
| --- | --- |
| Identity Protection Center |<ul><li>Todos los permisos del rol Lector de seguridad.<li>Además, la posibilidad de realizar todas las operaciones de IPC, excepto la de restablecer contraseñas. |
| Privileged Identity Management |<ul><li>Todos los permisos del rol Lector de seguridad.<li>**No puede** administrar la configuración de roles de Azure AD o la pertenencia a ellos. |
| <p>Supervisión de estado del servicio de Office 365</p><p>Centro de cumplimiento y seguridad de Office 365 |<ul><li>Todos los permisos del rol Lector de seguridad.<li>Puede configurar todos los valores de la característica Protección frente a amenazas avanzada (malware y protección antivirus, configuración de direcciones URL malintencionadas, seguimiento de URL, etc.). |

### <a name="service-administrator"></a>Administrador de servicios
| Puede hacer | No puede hacer |
| --- | --- |
| <p>Ver información de usuario y de la compañía</p><p>Administrar incidencias de soporte técnico de Office</p> |<p>Restablecer las contraseñas de los usuarios</p><p>Realizar operaciones de facturación y compra productos de Office</p><p>Crear y administrar vistas de usuario</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuario</p><p>Administrar dominios</p><p>Administrar información de la compañía</p><p>Delegar roles administrativos a otros usuarios</p><p>Usar la sincronización de directorios</p><p>Visualización de registros de auditoría</p> |

### <a name="user-account-administrator"></a>Administrador de cuentas de usuario
| Puede hacer | No puede hacer |
| --- | --- |
| <p>Ver información de usuario y de la compañía</p><p>Administrar incidencias de soporte técnico de Office</p><p>Cambiar solo las contraseñas de los usuarios, de los administradores del departamento de soporte técnico y de otros administradores de cuentas de usuario</p><p>Crear y administrar vistas de usuario</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuarios, con limitaciones. No puede eliminar un administrador global ni crear otros administradores.</p> |<p>Realizar operaciones de facturación y compra productos de Office</p><p>Administrar dominios</p><p>Administrar información de la compañía</p><p>Delegar roles administrativos a otros usuarios</p><p>Usar la sincronización de directorios</p><p>Habilitar o deshabilitar Multi-Factor Authentication</p><p>Visualización de registros de auditoría</p> |

### <a name="to-add-a-colleague-as-a-global-administrator"></a>Para agregar un compañero como administrador global

1. Inicie sesión en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio de inquilinos.

   ![Abrir el Centro de administración de Azure AD](./media/active-directory-assign-admin-roles-azure-portal/active-directory-admin-center.png)

2. Seleccione **Usuarios y grupos &gt; Todos los usuarios**.

3. Busque el usuario al que quiera designar como administrador global y abra la hoja de ese usuario.

4. En la hoja del usuario, seleccione **Rol de directorio**.
 
5. En la hoja de rol de directorio, seleccione el rol **Administrador global** y guárdelo.

## <a name="deprecated-roles"></a>Roles en desuso

Los siguientes roles no deben usarse. Están en desuso y se eliminarán de Azure AD más adelante.

* Administrador de licencias ad hoc
* Creador de usuarios comprobados de correo electrónico
* Combinación de dispositivos
* Administradores de dispositivos
* Usuarios de dispositivos
* Combinación de dispositivos de área de trabajo

## <a name="next-steps"></a>pasos siguientes

* Para más información acerca de cómo cambiar los administradores de una suscripción de Azure, consulte [Incorporación o cambio de roles de administrador de Azure](../billing-add-change-azure-subscription-administrator.md)
* Para más información sobre cómo se controla el acceso a los recursos en Microsoft Azure, consulte [Descripción de acceso a los recursos de Azure](active-directory-understanding-resource-access.md)
* Para más información sobre cómo se relaciona Azure Active Directory con la suscripción de Azure, consulte [Asociación de las suscripciones de Azure con Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Administrar usuarios](active-directory-create-users.md)
* [Administrar contraseñas](active-directory-manage-passwords.md)
* [Administrar grupos](active-directory-manage-groups.md)
