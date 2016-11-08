---
title: Asignación de roles de administrador en Azure Active Directory | Microsoft Docs
description: Explica qué roles de administrador están disponibles con Azure Active Directory y cómo asignarlos.
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: curtand

---
# Asignación de roles de administrador en Azure Active Directory
Con Azure Active Directory (Azure AD), puede designar administradores independientes que desempeñen distintas funciones. Esos administradores tendrán acceso a varias características del portal de Azure o el portal de Azure clásico y, según el rol que tengan, podrán crear o editar usuarios, asignar roles administrativos a otros, restablecer contraseñas de usuario o administrar licencias de usuario y dominios, entre otras funciones. Un usuario al que se le haya asignado un rol de administrador tendrá los mismos permisos en todos los servicios en la nube a los que se haya suscrito la organización, independientemente de si se ha asignado el rol en el portal de Office 365, en el Portal de Azure clásico o por medio del módulo de Azure AD para Windows PowerShell.

Los roles de administrador disponibles son los siguientes:

* **Administrador de facturación**: hace compras, administra suscripciones, administra incidencias de soporte técnico y supervisa el estado del servicio.
* **Administrador global/administrador de la compañía**: tiene acceso a todas las características administrativas. La persona que se suscribe a la cuenta de Azure se convierte en un administrador global. Los administradores globales son los únicos que pueden asignar otros roles de administrador. Puede haber más de un administrador global en su empresa.
  
  > [!NOTE]
  > En la API Graph de Microsoft, la API de Azure AD Graph, y Azure AD PowerShell, este rol se identifica como "administrador de la compañía". Es "administrador Global" en [Azure Portal](https://portal.azure.com).
  > 
  > 
* **Administrador de cumplimiento**:
* **Administrador del servicio CRM**: los usuarios con este rol tienen permisos globales en Microsoft CRM Online, cuando el servicio está presente. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=es-ES&rs=es-ES&ad=US).
* **Aprobador del acceso a la Caja de seguridad del cliente**: cuando el servicio LockBox está habilitado, los usuarios con este rol pueden aprobar solicitudes para el acceso de ingenieros de Microsoft a información de la empresa. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=es-ES&rs=es-ES&ad=US).
* **Administradores de dispositivos**: los usuarios con este rol se convierten en administradores en todos los dispositivos de Windows 10 que están unidos a Azure Active Directory.
* **Lectores de directorio**: se trata de un rol heredado que se asigna a las aplicaciones que no admiten el [marco de consentimiento](active-directory-integrating-applications.md). No se debe asignar a ningún usuario.
* **Cuentas de sincronización de directorio**: no se usan. Este rol se asigna automáticamente al servicio de Azure AD Connect y no está previsto ni se admite para ningún otro uso.
* **Lectores de directorio**: es un rol heredado que se asigna a las aplicaciones que no admiten el [marco de consentimiento](active-directory-integrating-applications.md). No se debe asignar a ningún usuario.
* **Administrador de servicios de Exchange**: los usuarios con este rol tienen permisos globales en Microsoft Exchange Online, cuando el servicio está presente. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=es-ES&rs=es-ES&ad=US).
* **Administrador de servicios de Intune**: los usuarios con este rol tienen permisos globales en Microsoft Intune Online, cuando el servicio está presente. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=es-ES&rs=es-ES&ad=US).
* **Administrador del servicio Skype Empresarial**: los usuarios con este rol tienen permisos globales en Microsoft Skype Empresarial, cuando el servicio está presente. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=es-ES&rs=es-ES&ad=US). Este rol se conocía antes como **administrador de servicios de Lync**.
* **Administrador de contraseñas/administrador de departamento de soporte técnico**: restablece las contraseñas, administra solicitudes de servicio y supervisa el estado del servicio. Los administradores de contraseñas pueden restablecer contraseñas solo para los usuarios y otros administradores de contraseñas.
  
  > [!NOTE]
  > En la API Graph de Microsoft, la API Graph de Azure AD y Azure AD PowerShell, este rol se identifica como "Administrador de soporte técnico".
  > 
  > 
* **Administrador del servicio SharePoint**: los usuarios con este rol tienen permisos globales en Microsoft SharePoint Online, cuando el servicio está presente. Más información en [Acerca de los roles de administrador de Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=es-ES&rs=es-ES&ad=US).
* **Administrador de servicios**: administra las solicitudes de servicio y supervisa el estado del servicio.
  
  > [!NOTE]
  > Para asignar el rol de administrador de servicios a un usuario, el administrador global debe asignar primero permisos administrativos al usuario en el servicio, como Exchange Online, y después asignar el rol de administrador de servicios al usuario en el portal de Azure clásico.
  > 
  > 
* **Administrador de cuentas de usuario**: restablece contraseñas, supervisa el estado del servicio y administra cuentas de usuario, grupos de usuarios y solicitudes de servicio. Existen algunas limitaciones en los permisos de un administrador de usuarios. Por ejemplo, este no puede eliminar a un administrador global ni puede crear otros administradores. Tampoco puede restablecer las contraseñas de los administradores de facturación, globales y de servicio.
* **Lector de seguridad**: acceso de solo lectura a un número de características de seguridad de Identity Protection Center, Privileged Identity Management, Supervisión de estado de servicio de Office 365 y Centro de seguridad y cumplimiento de Office 365.
* **Administrador de seguridad**: todos los permisos de solo lectura del rol **Lector seguridad**, además de una serie de permisos administrativos adicionales para los mismos servicios: Identity Protection Center, Privileged Identity Management, Supervisión de estado de servicio de Office 365 y Centro de seguridad y cumplimiento de Office 365.

## Permisos de administrador
### Administrador de facturación
| Puede hacer | No puede hacer |
| --- | --- |
| <p>Ver información de empresas y usuarios</p><p>Administrar incidencias de soporte técnico de Office</p><p>Hacer operaciones de facturación y compra para los productos de Office</p> |<p>Restablecer contraseñas de usuario</p><p>Crear y administrar vistas de usuario</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuario</p><p>Administrar dominios</p><p>Administrar información de la empresa</p><p>Delegar roles administrativos a otros</p><p>Utilizar la sincronización de directorios</p><p>Ver informes</p> |

### Administrador global
| Puede hacer | No puede hacer |
| --- | --- |
| <p>Ver información de empresas y usuarios</p><p>Administrar incidencias de soporte técnico de Office</p><p>Realizar operaciones de facturación y compra de productos de Office</p> <p>Restablecer contraseñas de usuario</p><p>Crear y administrar vistas de usuario</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuario</p><p>Administrar dominios</p><p>Administrar información de la empresa</p><p>Delegar roles administrativos a otros</p><p>Usar la sincronización de directorios</p><p>Habilitar o deshabilitar Multi-Factor Authentication</p><p>Ver informes</p> |N/D |

### Administrador de contraseñas
| Puede hacer | No puede hacer |
| --- | --- |
| <p>Ver información de empresas y usuarios</p><p>Administrar incidencias de soporte técnico de Office</p><p>Restablecer contraseñas de usuario</p> |<p>Realizar operaciones de facturación y compra de productos de Office</p><p>Crear y administrar vistas de usuarios</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuario</p><p>Administrar dominios</p><p>Administrar información de la empresa</p><p>Delegar roles administrativos a otros</p><p>Utilizar la sincronización de directorios</p><p>Ver informes</p> |

### Administrador de servicios
| Puede hacer | No puede hacer |
| --- | --- |
| <p>Ver información de empresas y usuarios</p><p>Administrar incidencias de soporte técnico de Office</p> |<p>Restablecer contraseñas de usuario</p><p>Realizar operaciones de facturación y compra de productos de Office</p><p>Crear y administrar vistas de usuarios</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuario</p><p>Administrar dominios</p><p>Administrar información de la empresa</p><p>Delegar roles administrativos a otros</p><p>Utilizar la sincronización de directorios</p><p>Ver informes</p> |

### Administrador de usuarios
| Puede hacer | No puede hacer |
| --- | --- |
| <p>Ver información de empresas y usuarios</p><p>Administrar incidencias de soporte técnico de Office</p><p>Restablecer contraseñas de usuarios, con limitaciones. No puede restablecer las contraseñas para los administradores de facturación, globales y de servicios</p><p>Crear y administrar vistas de usuarios</p><p>Crear, editar y eliminar usuarios y grupos, y administrar licencias de usuarios, con limitaciones. No puede eliminar un administrador global ni crear otros administradores.</p> |<p>Realizar operaciones de facturación y compra de productos de Office</p><p>Administrar dominios</p><p>Administrar información de la empresa</p><p>Delegar roles administrativos a otros</p><p>Usar la sincronización de directorios</p><p>Habilitar o deshabilitar Multi-Factor Authentication</p><p>Ver informes</p> |

### Lector de seguridad
| En el | Puede hacer |
| --- | --- |
| Identity Protection Center |Leer todos los informes de seguridad y la información de configuración de las características de seguridad<ul><li>Filtro de correo no deseado<li>Cifrado<li>Prevención de pérdida de datos<li>Antimalware<li>Protección contra amenazas avanzada<li>Protección contra suplantación de identidad (anti-phishing)<li>Reglas de flujo de correo |
| Privileged Identity Management |<p>Tiene acceso de solo lectura a toda la información que aparece en PIM de Azure: directivas e informes para las asignaciones de roles de Azure AD, revisiones de seguridad y, en el futuro, acceso de lectura a datos e informes de directivas en escenarios distintos a la asignación de roles de Azure AD.<p>**No puede** registrarse para PIM de Azure AD ni realizar ningún cambio en esta característica. En el portal de PIM o a través de PowerShell, alguien con este rol puede activar roles adicionales (por ejemplo, Administrador Global o Administrador de rol con privilegios), si el usuario es un candidato para ellos. |
| <p>Supervisión de estado de servicio de Office 365</p><p>Centro de cumplimiento y seguridad de Office 365</p> |<ul><li>Leer y administrar alertas<li>Leer directivas de seguridad<li>Leer información sobre amenazas, Cloud App Discovery y Cuarentena en Search and Investigar (Buscar e investigar)<li>Leer todos los informes |

### Administrador de seguridad
| En el | Puede hacer |
| --- | --- |
| Identity Protection Center |<ul><li>Todos los permisos del rol Lector de seguridad.<li>Además, la posibilidad de realizar todas las operaciones de IPC, excepto la de restablecer contraseñas. |
| Privileged Identity Management |<ul><li>Todos los permisos del rol Lector de seguridad.<li>**No puede** administrar la configuración de roles de Azure AD o la pertenencia a ellos. |
| <p>Supervisión de estado de servicio de Office 365</p><p>Centro de cumplimiento y seguridad de Office 365 |<ul><li>Todos los permisos del rol Lector de seguridad.<li>Puede configurar todos los valores de la característica Protección frente a amenazas avanzada (malware y protección antivirus, configuración de direcciones URL malintencionadas, seguimiento de URL, etc.). |

## Detalles acerca del rol de administrador global
El administrador global tiene acceso a todos los roles administrativos. De forma predeterminada, a la persona que se suscribe a una suscripción de Azure se le asigna el rol de administrador global para el directorio. Los administradores globales son los únicos que pueden asignar otros roles de administrador.

## Asignación o eliminación de roles de administrador
1. En el [Portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Active Directory** y luego en el nombre del directorio de su organización.
2. En la página **Usuarios**, haga clic en el nombre para mostrar del usuario que desee editar.
3. En la lista **Rol organizativo**, seleccione el rol de administrador que quiera asignar a este usuario o **Usuario** si quiere quitar un rol de administrador existente.
4. En el cuadro **Dirección de correo electrónico alternativa**, escriba una dirección de correo electrónico. Esta dirección de correo electrónico se usa para notificaciones importantes, incluido el restablecimiento automático de contraseña, por lo que el usuario debe poder tener acceso a la cuenta de correo electrónico independientemente de si tiene acceso a Azure.
5. Seleccione **Permitir** o **Bloquear** para especificar si se permite al usuario iniciar sesión y tener acceso a servicios.
6. Especifique una ubicación en la lista desplegable **Ubicación de uso**.
7. Cuando haya terminado, haga clic en **Guardar**.

## Pasos siguientes
* Para más información acerca de cómo cambiar los administradores de una suscripción de Azure, consulte [Incorporación o cambio de roles de administrador de Azure](../billing-add-change-azure-subscription-administrator.md)
* Para más información sobre cómo se controla el acceso a los recursos en Microsoft Azure, consulte [Descripción de acceso a los recursos de Azure](active-directory-understanding-resource-access.md).
* Para más información sobre cómo se relaciona Azure Active Directory con la suscripción de Azure, consulte [Asociación de las suscripciones de Azure con Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).
* [Administrar usuarios](active-directory-create-users.md)
* [Administrar contraseñas](active-directory-manage-passwords.md)
* [Administrar grupos](active-directory-manage-groups.md)

<!---HONumber=AcomDC_0921_2016-->