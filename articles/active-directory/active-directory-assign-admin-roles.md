---
title: "Asignación de roles de administrador en Azure Active Directory | Microsoft Docs"
description: "Explica qué roles de administrador están disponibles con Azure Active Directory y cómo asignarlos."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7fc27e8e-b55f-4194-9b8f-2e95705fb731
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: ba3690084439aac83c91a1b4cfb7171b74c814f8
ms.openlocfilehash: 9107ae03c15b607e1d2d867a1e06e654c365cca5


---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Asignación de roles de administrador en Azure Active Directory
Con Azure Active Directory (Azure AD), puede designar administradores independientes que desempeñen distintas funciones. Esos administradores tendrán acceso a varias características del portal de Azure o el portal de Azure clásico y, según el rol que tengan, podrán crear o editar usuarios, asignar roles administrativos a otros, restablecer contraseñas de usuario o administrar licencias de usuario y dominios, entre otras funciones. Un usuario al que se le haya asignado un rol de administrador tendrá los mismos permisos en todos los servicios en la nube a los que se haya suscrito la organización, independientemente de si se ha asignado el rol en el portal de Office 365, en el Portal de Azure clásico o por medio del módulo de Azure AD para Windows PowerShell.

Los roles de administrador disponibles son los siguientes:

* **Administrador de facturación**: hace compras, administra suscripciones, administra incidencias de soporte técnico y supervisa el estado del servicio.
* **Administrador global/administrador de la compañía**: tiene acceso a todas las características administrativas. La persona que se suscribe a la cuenta de Azure se convierte en un administrador global. Los administradores globales son los únicos que pueden asignar otros roles de administrador. Puede haber más de un administrador global en su empresa.

  > [!NOTE]
  > En la API Graph de Microsoft, la API de Azure AD Graph, y Azure AD PowerShell, este rol se identifica como "administrador de la compañía". Es "administrador Global" en [Azure Portal](https://portal.azure.com).
  >
  >
* **Administrador de cumplimiento**:
* **Administrador del servicio CRM**: los usuarios con este rol tienen permisos globales en Microsoft CRM Online, cuando el servicio está presente. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-US&ad=US).
* **Aprobador del acceso a la Caja de seguridad del cliente**: cuando el servicio LockBox está habilitado, los usuarios con este rol pueden aprobar solicitudes para el acceso de ingenieros de Microsoft a información de la empresa. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-US&ad=US).
* **Administradores de dispositivos**: los usuarios con este rol se convierten en administradores en todos los dispositivos de Windows 10 que están unidos a Azure Active Directory.
* **Lectores de directorio**: se trata de un rol heredado que se asigna a las aplicaciones que no admiten el [marco de consentimiento](active-directory-integrating-applications.md). No se debe asignar a ningún usuario.
* **Cuentas de sincronización de directorio**: no se usan. Este rol se asigna automáticamente al servicio de Azure AD Connect y no está previsto ni se admite para ningún otro uso.
* **Lectores de directorio**: es un rol heredado que se asigna a las aplicaciones que no admiten el [marco de consentimiento](active-directory-integrating-applications.md). No se debe asignar a ningún usuario.
* **Administrador de servicios de Exchange**: los usuarios con este rol tienen permisos globales en Microsoft Exchange Online, cuando el servicio está presente. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-US&ad=US).
* **Administrador de servicios de Intune**: los usuarios con este rol tienen permisos globales en Microsoft Intune Online, cuando el servicio está presente. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-US&ad=US).
* **Administrador del servicio Skype Empresarial**: los usuarios con este rol tienen permisos globales en Microsoft Skype Empresarial, cuando el servicio está presente. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-US&ad=US). Este rol se conocía antes como **administrador de servicios de Lync** .
* **Autor de invitaciones de invitados**: los usuarios de este rol pueden administrar las invitaciones de los invitados. No incluye otros permisos.
* **Administrador de contraseñas/administrador de departamento de soporte técnico**: restablece las contraseñas, administra solicitudes de servicio y supervisa el estado del servicio. Los administradores de contraseñas pueden restablecer contraseñas solo para los usuarios y otros administradores de contraseñas.

  > [!NOTE]
  > En la API Graph de Microsoft, la API Graph de Azure AD y Azure AD PowerShell, este rol se identifica como "Administrador de soporte técnico".
  >
  >
* **Administrador del servicio SharePoint**: los usuarios con este rol tienen permisos globales en Microsoft SharePoint Online, cuando el servicio está presente. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-US&ad=US).
* **Administrador de servicios**: administra las solicitudes de servicio y supervisa el estado del servicio.

  > [!NOTE]
  > Para asignar el rol de administrador de servicios a un usuario, el administrador global debe asignar primero permisos administrativos al usuario en el servicio, como Exchange Online, y después asignar el rol de administrador de servicios al usuario en el portal de Azure clásico.
  >
  >
* **Administrador de cuentas de usuario**: restablece contraseñas, supervisa el estado del servicio y administra cuentas de usuario, grupos de usuarios y solicitudes de servicio. Existen algunas limitaciones en los permisos de un administrador de usuarios. Por ejemplo, este no puede eliminar a un administrador global ni puede crear otros administradores. Tampoco puede restablecer las contraseñas de los administradores de facturación, globales y de servicio.
* **Lector de seguridad**: acceso de solo lectura a varias características de seguridad de Identity Protection Center, Privileged Identity Management, Supervisión de estado del servicio de Office 365 y Centro de seguridad y cumplimiento de Office 365.
* **Administrador de seguridad**: todos los permisos de solo lectura del rol **Lector seguridad**, además de varios permisos administrativos adicionales para los mismos servicios: Identity Protection Center, Privileged Identity Management, Supervisión de estado del servicio de Office 365 y Centro de seguridad y cumplimiento de Office 365.

## <a name="administrator-permissions"></a>Permisos de administrador
### <a name="billing-administrator"></a>Administrador de facturación
| Puede hacer | No puede hacer |
| --- | --- |
| <p>Ver información de usuario y de la compañía</p><p>Administrar incidencias de soporte técnico de Office</p><p>Realizar operaciones de facturación y compra productos de Office</p> |<p>Restablecer las contraseñas de los usuarios</p><p>Crear y administrar vistas de usuario</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuario</p><p>Administrar dominios</p><p>Administrar información de la compañía</p><p>Delegar roles administrativos a otros usuarios</p><p>Usar la sincronización de directorios</p><p>Ver informes</p> |

### <a name="global-administrator"></a>Administrador global
| Puede hacer | No puede hacer |
| --- | --- |
| <p>Ver información de usuario y de la compañía</p><p>Administrar incidencias de soporte técnico de Office</p><p>Realizar operaciones de facturación y compra productos de Office</p> <p>Restablecer las contraseñas de los usuarios</p><p>Crear y administrar vistas de usuario</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuario</p><p>Administrar dominios</p><p>Administrar información de la compañía</p><p>Delegar roles administrativos a otros usuarios</p><p>Usar la sincronización de directorios</p><p>Habilitar o deshabilitar Multi-Factor Authentication</p><p>Ver informes</p> |N/D |

### <a name="password-administrator"></a>Administrador de contraseñas
| Puede hacer | No puede hacer |
| --- | --- |
| <p>Ver información de usuario y de la compañía</p><p>Administrar incidencias de soporte técnico de Office</p><p>Restablecer las contraseñas de los usuarios</p> |<p>Realizar operaciones de facturación y compra productos de Office</p><p>Crear y administrar vistas de usuario</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuario</p><p>Administrar dominios</p><p>Administrar información de la compañía</p><p>Delegar roles administrativos a otros usuarios</p><p>Usar la sincronización de directorios</p><p>Ver informes</p> |

### <a name="service-administrator"></a>Administrador de servicios
| Puede hacer | No puede hacer |
| --- | --- |
| <p>Ver información de usuario y de la compañía</p><p>Administrar incidencias de soporte técnico de Office</p> |<p>Restablecer las contraseñas de los usuarios</p><p>Realizar operaciones de facturación y compra productos de Office</p><p>Crear y administrar vistas de usuario</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuario</p><p>Administrar dominios</p><p>Administrar información de la compañía</p><p>Delegar roles administrativos a otros usuarios</p><p>Usar la sincronización de directorios</p><p>Ver informes</p> |

### <a name="user-administrator"></a>Administrador de usuarios
| Puede hacer | No puede hacer |
| --- | --- |
| <p>Ver información de usuario y de la compañía</p><p>Administrar incidencias de soporte técnico de Office</p><p>Restablecer contraseñas de usuarios, con limitaciones. No puede restablecer las contraseñas para los administradores de facturación, globales y de servicios</p><p>Crear y administrar vistas de usuario</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuarios, con limitaciones. No puede eliminar un administrador global ni crear otros administradores.</p> |<p>Realizar operaciones de facturación y compra productos de Office</p><p>Administrar dominios</p><p>Administrar información de la compañía</p><p>Delegar roles administrativos a otros usuarios</p><p>Usar la sincronización de directorios</p><p>Habilitar o deshabilitar Multi-Factor Authentication</p><p>Ver informes</p> |

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

## <a name="details-about-the-global-administrator-role"></a>Detalles acerca del rol de administrador global
El administrador global tiene acceso a todos los roles administrativos. De forma predeterminada, a la persona que se suscribe a una suscripción de Azure se le asigna el rol de administrador global para el directorio. Los administradores globales son los únicos que pueden asignar otros roles de administrador.

## <a name="assign-or-remove-administrator-roles"></a>Asignación o eliminación de roles de administrador
1. En el [Portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Active Directory**y luego en el nombre del directorio de su organización.
2. En la página **Usuarios** , haga clic en el nombre para mostrar del usuario que desee editar.
3. En la lista **Rol organizativo**, seleccione el rol de administrador que desee asignar a este usuario, o bien seleccione **Usuario** si desea quitar un rol de administrador existente.
4. En el cuadro **Dirección de correo electrónico alternativa** , escriba una dirección de correo electrónico. Esta dirección de correo electrónico se usa para notificaciones importantes, incluido el restablecimiento automático de contraseña, por lo que el usuario debe poder tener acceso a la cuenta de correo electrónico independientemente de si tiene acceso a Azure.
5. Seleccione **Permitir** o **Bloquear** para especificar si se permite al usuario iniciar sesión y acceder a los servicios.
6. Especifique una ubicación en la lista desplegable **Ubicación de uso** .
7. Cuando haya terminado, haga clic en **Guardar**.

## <a name="next-steps"></a>Pasos siguientes
* Para más información acerca de cómo cambiar los administradores de una suscripción de Azure, consulte [Incorporación o cambio de roles de administrador de Azure](../billing-add-change-azure-subscription-administrator.md)
* Para más información sobre cómo se controla el acceso a los recursos en Microsoft Azure, consulte [Descripción de acceso a los recursos de Azure](active-directory-understanding-resource-access.md)
* Para más información sobre cómo se relaciona Azure Active Directory con la suscripción de Azure, consulte [Asociación de las suscripciones de Azure con Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Administrar usuarios](active-directory-create-users.md)
* [Administrar contraseñas](active-directory-manage-passwords.md)
* [Administrar grupos](active-directory-manage-groups.md)



<!--HONumber=Nov16_HO3-->


