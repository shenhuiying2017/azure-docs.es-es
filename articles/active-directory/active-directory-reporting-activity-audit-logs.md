---
title: "Informes de actividad de auditoría en el portal de Azure Active Directory (versión preliminar) | Microsoft Docs"
description: "Introducción a los informes de actividad de auditoría en la versión preliminar del portal de Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: d88f8bed0cbd14ee49986d6749396731a810034b
ms.lasthandoff: 04/18/2017


---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal---preview"></a>Informes de actividad de auditoría en el portal de Azure Active Directory (versión preliminar)

Con los informes de la [versión preliminar](active-directory-preview-explainer.md) de Azure Active Directory, obtendrá toda la información que necesita para determinar cómo marcha el entorno.

La arquitectura de los informes de Azure Active Directory consta de los siguientes componentes:

- **Actividad** 
    - **Actividades de inicio de sesión** : información sobre el uso de las aplicaciones administradas y las actividades de inicio de sesión de usuario
    - **Registros de auditoría**: información de la actividad del sistema sobre los usuarios y la administración de grupos, sus aplicaciones administradas y actividades de directorio.
- **Seguridad** 
    - **Inicios de sesión peligrosos**: un inicio de sesión peligroso es un indicador de un intento de inicio de sesión que puede haber realizado alguien que no es el propietario legítimo de una cuenta de usuario. Para más información, consulte Inicios de no seguros.
    - **Usuarios marcados en riesgo**: un usuario en peligro es un indicador de una cuenta de usuario que puede haber estado en peligro. Para más información, consulte la sección Usuarios marcados en riesgo.

Este tema ofrece una visión general de las actividades de auditoría.
 


## <a name="audit-logs"></a>Registros de auditoría

Los registros de auditoría de Azure Active Directory proporcionan registros de las actividades del sistema de cara al cumplimiento.  
El primer punto de entrada a todos los datos de auditoría es **Registros de auditoría** en la sección **Actividad** de **Azure Active Directory**.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/61.png "Registros de auditoría")

Un registro de auditoría tiene una vista de lista predeterminada que muestra:

- la fecha y hora de la repetición
- el iniciador/actor (*quién*) de una actividad 
- la actividad (*qué*) 
- el destino

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/18.png "Registros de auditoría")

Puede personalizar la vista de lista, haga clic en **Columnas** en la barra de herramientas.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/19.png "Registros de auditoría")

Esto le permite mostrar los campos adicionales o quitar los campos que ya se están mostrando.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/21.png "Registros de auditoría")


Si hace clic en un elemento de la vista de lista, puede obtener todos los detalles disponibles sobre él.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/22.png "Registros de auditoría")


## <a name="filtering-audit-logs"></a>Filtrado de registros de auditoría

Para restringir los datos del informe a un nivel que se adapte a sus necesidades, puede filtrar los datos de auditoría con los siguientes campos:

- Intervalo de fechas
- Iniciado por (actor)
- Categoría
- Tipo de recurso de actividad
- Actividad

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/23.png "Registros de auditoría")


El filtro **Intervalo de fechas** permite definir un período de tiempo para los datos devueltos.  
Los valores posibles son:

- 1 mes
- 7 días
- 24 horas
- Personalizado

Cuando se selecciona un intervalo de tiempo personalizado, puede configurar una hora de inicio y una hora de finalización.

El filtro **Iniciado por** le permite definir el nombre de un actor o su nombre principal universal (UPN).

El filtro **Categoría** le permite seleccionar uno de los filtros siguientes:

- Todo
- Core category (Categoría principal)
- Core Directory (Directorio principal)
- Self-service Password Management (Administración de contraseñas de autorservicio)
- Administración de grupos de autoservicio
- Aprovisionamiento de cuentas
- Automated Password Rollover (Sustitución automática de contraseña)
- Invited users (Usuarios invitados)
- MIM service (Servicio MIM)

El filtro **Tipo de recurso de actividad** le permite seleccionar uno de los filtros siguientes:

- Todo 
- Grupo
- Directorio
- Usuario
- Application
- Directiva
- Dispositivo
- Otros

Cuando se selecciona **Grupo** como **Tipo de recurso de actividad**, obtendrá una categoría de filtro adicional que le permite proporcionar también un **Origen**:

- Azure AD
- O365




El filtro **Actividad** se basa en la selección de categoría y de tipo de recurso de actividad que realice. Puede seleccionar la actividad específica que desea ver o elegir todas. 

| Categoría de actividad| Tipo de recurso de actividad| Actividad |
| :-- | :-: | :-- |
| Core Directory (Directorio principal)| Grupo| Eliminar configuración de grupo|
| Core Directory (Directorio principal)| Directorio| Actualizar dominio|
| Core Directory (Directorio principal)| Directorio| Quitar asociado de compañía|
| Core Directory (Directorio principal)| Usuario| Actualizar rol|
| Core Directory (Directorio principal)| Usuario| Agregar rol desde plantilla|
| Core Directory (Directorio principal)| Grupo| Agregar asignación de roles de aplicación al grupo|
| Core Directory (Directorio principal)| Grupo| Empezar a aplicar licencias basadas en grupo a los usuarios|
| Core Directory (Directorio principal)| Application| Agregar entidad de servicio|
| Core Directory (Directorio principal)| Directiva| Actualizar directiva|
| Core Directory (Directorio principal)| Directiva| Agregar directiva a entidad de servicio|
| Core Directory (Directorio principal)| Dispositivo| Agregar propietario registrado a dispositivo|
| Core Directory (Directorio principal)| Dispositivo| Agregar usuarios registrados a dispositivo|
| Core Directory (Directorio principal)| Dispositivo| Actualizar configuración de dispositivo|
| Self-service Password Management (Administración de contraseñas de autorservicio)| Usuario| Restablecer contraseña (autoservicio)|
| Self-service Password Management (Administración de contraseñas de autorservicio)| Usuario| Desbloquear cuenta de usuario (autoservicio)|
| Self-service Password Management (Administración de contraseñas de autorservicio)| Usuario| Restablecer contraseña (de parte del administrador)|
| Self-service Group Management (Administración de grupos de autoservicio)| Grupo| Eliminar una solicitud pendiente para unirse a un grupo|
| Account Provisioning (Aprovisionamiento de cuentas)| Application| Procesar custodia|
| Automated Password Rollover (Sustitución automática de contraseña)| Application| Automated Password Rollover (Sustitución automática de contraseña)|
| Invited users (Usuarios invitados)| Otros| Invitaciones por lotes procesadas|
| Core Directory (Directorio principal)| Directorio| Quitar dominio comprobado|
| Core Directory (Directorio principal)| Directorio| Agregar dominio sin comprobar|
| Core Directory (Directorio principal)| Directorio| Agregar dominio comprobado|
| Core Directory (Directorio principal)| Directorio| Establecer característica de directorio en inquilino|
| Core Directory (Directorio principal)| Directorio| Establecer marca DirSyncEnabled|
| Core Directory (Directorio principal)| Directorio| Crear configuración de compañía|
| Core Directory (Directorio principal)| Directorio| Actualizar configuración de compañía|
| Core Directory (Directorio principal)| Directorio| Eliminar configuración de compañía|
| Core Directory (Directorio principal)| Directorio| Establecer ubicación de datos permitida de compañía|
| Core Directory (Directorio principal)| Directorio| Establecer característica de compañía multinacional habilitada|
| Core Directory (Directorio principal)| Usuario| Actualizar usuario|
| Core Directory (Directorio principal)| Usuario| Eliminar usuario|
| Core Directory (Directorio principal)| Grupo| Quitar miembro de grupo|
| Core Directory (Directorio principal)| Grupo| Establecer licencia de grupo|
| Core Directory (Directorio principal)| Grupo| Crear configuración de grupo|
| Core Directory (Directorio principal)| Application| Actualizar entidad de servicio|
| Core Directory (Directorio principal)| Application| Eliminar aplicación|
| Core Directory (Directorio principal)| Application| Actualizar aplicación|
| Core Directory (Directorio principal)| Application| Quitar entidad de servicio|
| Core Directory (Directorio principal)| Application| Agregar credenciales de la entidad de servicio|
| Core Directory (Directorio principal)| Application| Quitar la asignación de rol de aplicación de entidad de servicio|
| Core Directory (Directorio principal)| Application| Quitar propietario de aplicación|
| Core Directory (Directorio principal)| Dispositivo| Quitar propietario registrado de dispositivo|
| Self-service Password Management (Administración de contraseñas de autorservicio)| Usuario| Progreso de la actividad del flujo de restablecimiento de contraseña de autoservicio|
| Account Provisioning (Aprovisionamiento de cuentas)| Application| Administración|
| Account Provisioning (Aprovisionamiento de cuentas)| Application| Operación de directorio|
| MIM service (Servicio MIM)| Grupo| Quitar miembro|
| Core Directory (Directorio principal)| Directiva| Eliminar directiva|
| Invited users (Usuarios invitados)| Usuario| Creación de inquilinos viral|
| Core Directory (Directorio principal)| Directorio| Actualizar secretos externos|
| Core Directory (Directorio principal)| Directorio| Establecer propiedades de Rights Management|
| Core Directory (Directorio principal)| Directorio| Actualizar compañía|
| Core Directory (Directorio principal)| Usuario| Agregar usuario|
| Core Directory (Directorio principal)| Usuario| Convertir usuario federado en administrado|
| Core Directory (Directorio principal)| Usuario| Crear contraseña de aplicaciones para usuario|
| Core Directory (Directorio principal)| Grupo| Agregar miembro a grupo|
| Core Directory (Directorio principal)| Grupo| Agregar grupo|
| Core Directory (Directorio principal)| Application| Da consentimiento a la aplicación|
| Core Directory (Directorio principal)| Application| Agregar aplicación|
| Core Directory (Directorio principal)| Application| Agregar propietario a entidad de servicio|
| Core Directory (Directorio principal)| Application| Quitar Oauth2Permissiongrant|
| Core Directory (Directorio principal)| Directiva| Quitar credenciales de directiva|
| Core Directory (Directorio principal)| Dispositivo| Eliminar configuración de dispositivo|
| Self-service Group Management (Administración de grupos de autoservicio)| Grupo| Establecer propiedades de grupo dinámico|
| Self-service Group Management (Administración de grupos de autoservicio)| Grupo| Actualizar directiva de administración del ciclo de vida|
| Account Provisioning (Aprovisionamiento de cuentas)| Application| Acción de reglas de sincronización|
| Invited users (Usuarios invitados)| Otros| Invitaciones por lotes cargadas|
| MIM service (Servicio MIM)| Grupo| Agregar miembro|
| Core Directory (Directorio principal)| Usuario| Establecer propiedades de licencia|
| Core Directory (Directorio principal)| Usuario| Restaurar usuario|
| Core Directory (Directorio principal)| Usuario| Quitar miembro de rol|
| Core Directory (Directorio principal)| Usuario| Quitar la asignación de rol de aplicación de usuario|
| Core Directory (Directorio principal)| Usuario| Quitar miembro con ámbito de rol|
| Core Directory (Directorio principal)| Grupo| Actualizar grupo|
| Core Directory (Directorio principal)| Grupo| Agregar propietario a grupo|
| Core Directory (Directorio principal)| Grupo| Finalizar a aplicar licencias basadas en grupo a los usuarios|
| Core Directory (Directorio principal)| Grupo| Quitar la asignación de rol de aplicación de grupo|
| Core Directory (Directorio principal)| Grupo| Establecer grupo que va a administrar el usuario|
| Core Directory (Directorio principal)| Application| Agregar Oauth2Permissiongrant|
| Core Directory (Directorio principal)| Application| Agregar la asignación de rol de aplicación a entidad de servicio|
| Core Directory (Directorio principal)| Application| Quitar credenciales de entidad de servicio|
| Core Directory (Directorio principal)| Directiva| Quitar directiva de entidad de servicio|
| Core Directory (Directorio principal)| Dispositivo| Actualizar dispositivo|
| Core Directory (Directorio principal)| Dispositivo| Agregar dispositivo|
| Core Directory (Directorio principal)| Dispositivo| Agregar configuración de dispositivo|
| Self-service Password Management (Administración de contraseñas de autorservicio)| Usuario| Cambiar contraseña (autoservicio)|
| Self-service Password Management (Administración de contraseñas de autorservicio)| Usuario| Usuario registrado para el restablecimiento de contraseña de autoservicio|
| Self-service Group Management (Administración de grupos de autoservicio)| Grupo| Aprobar una solicitud pendiente para unirse a un grupo|
| Core Directory (Directorio principal)| Directorio| Quitar dominio sin comprobar|
| Core Directory (Directorio principal)| Directorio| Comprobar dominio|
| Core Directory (Directorio principal)| Directorio| Establecer la autenticación de dominio|
| Core Directory (Directorio principal)| Directorio| Establecer directiva de contraseñas|
| Core Directory (Directorio principal)| Directorio| Agregar asociado a la compañía|
| Core Directory (Directorio principal)| Directorio| Aumentar nivel de compañía al asociado|
| Core Directory (Directorio principal)| Directorio| Establecer asociación|
| Core Directory (Directorio principal)| Directorio| Establecer umbral de eliminación accidental|
| Core Directory (Directorio principal)| Directorio| Disminuir nivel de asociado|
| Invited users (Usuarios invitados)| Usuario| Invitar a usuario externo|
| Account Provisioning (Aprovisionamiento de cuentas)| Application| Importar|
| Core Directory (Directorio principal)| Application| Quitar propietario de entidad de servicio|
| Core Directory (Directorio principal)| Dispositivo| Quitar usuarios registrados de dispositivo|
| Core Directory (Directorio principal)| Directorio| Establecer información sobre la compañía|
| Core Directory (Directorio principal)| Directorio| Establecer configuración de federación en el dominio|
| Core Directory (Directorio principal)| Directorio| Crear compañía|
| Core Directory (Directorio principal)| Directorio| Purgar propiedades de Rights Management|
| Core Directory (Directorio principal)| Directorio| Establecer característica DirSync|
| Core Directory (Directorio principal)| Directorio| Comprobar dominio verificado por correo electrónico|
| Core Directory (Directorio principal)| Usuario| Cambiar licencia de usuario|
| Core Directory (Directorio principal)| Usuario| Cambiar contraseña de usuario|
| Core Directory (Directorio principal)| Usuario| Restablecer contraseña de usuario|
| Core Directory (Directorio principal)| Usuario| Agregar asignación de roles de aplicación al usuario|
| Core Directory (Directorio principal)| Usuario| Agregar miembro a rol|
| Core Directory (Directorio principal)| Usuario| Eliminar contraseña de aplicaciones para usuario|
| Core Directory (Directorio principal)| Usuario| Actualizar credenciales de usuario|
| Core Directory (Directorio principal)| Usuario| Establecer administrador de usuarios|
| Core Directory (Directorio principal)| Usuario| Agregar miembro con ámbito agregado a rol|
| Core Directory (Directorio principal)| Grupo| Eliminar grupo|
| Core Directory (Directorio principal)| Grupo| Quitar propietario de grupo|
| Core Directory (Directorio principal)| Grupo| Actualizar configuración de grupo|
| Core Directory (Directorio principal)| Application| Agregar propietario a aplicación|
| Core Directory (Directorio principal)| Application| Revocar consentimiento|
| Core Directory (Directorio principal)| Directiva| Agregar directiva|
| Core Directory (Directorio principal)| Dispositivo| Eliminar dispositivo|
| Self-service Password Management (Administración de contraseñas de autorservicio)| Usuario| Bloqueado para el restablecimiento de contraseña de autoservicio|
| Self-service Group Management (Administración de grupos de autoservicio)| Grupo| Solicitar unirse a un grupo|
| Self-service Group Management (Administración de grupos de autoservicio)| Grupo| Crear directiva de administración del ciclo de vida|
| Self-service Group Management (Administración de grupos de autoservicio)| Grupo| Rechazar una solicitud pendiente para unirse a un grupo|
| Self-service Group Management (Administración de grupos de autoservicio)| Grupo| Cancelar una solicitud pendiente para unirse a un grupo|
| Self-service Group Management (Administración de grupos de autoservicio)| Grupo| Renovar grupo|
| Account Provisioning (Aprovisionamiento de cuentas)| Application| Exportación|
| Account Provisioning (Aprovisionamiento de cuentas)| Application| Otros|
| Invited users (Usuarios invitados)| Usuario| Canjear invitación a usuario externo|
| Invited users (Usuarios invitados)| Usuario| Creación de usuarios viral|
| Invited users (Usuarios invitados)| Usuario| Asignar usuario externo a la aplicación|




## <a name="audit-logs-shortcuts"></a>Métodos abreviados de los registros de auditoría

Además de **Azure Active Directory**, Azure Portal proporciona dos puntos de entrada adicionales para auditar datos:

- Usuarios y grupos
- Aplicaciones empresariales

Para obtener una lista completa de las actividades de informe de auditoría, consulte la [lista de eventos de informe de auditoría](active-directory-reporting-audit-events.md#list-of-audit-report-events).


### <a name="users-and-groups-audit-logs"></a>Registros de auditoría de los usuarios y grupos

Con los informes de auditoría basadas en grupos y usuarios, puede obtener respuestas a preguntas como:

- ¿Qué tipos de actualizaciones se han aplicado a los usuarios?

- ¿Cuántos usuarios han cambiado?

- ¿Cuántas contraseñas han cambiado?

- ¿Qué ha hecho un administrador en un directorio?

- ¿Cuáles son los grupos que se han agregado?

- ¿Hay grupos con cambios de pertenencia?

- ¿Se han cambiado los propietarios del grupo?

- ¿Qué licencias se han asignado a un grupo o un usuario?

Si desea revisar los datos de auditoría relacionados con usuarios y grupos, puede buscar una vista filtrada en **Registros de auditoría** en la sección **Actividad** de **Usuarios y grupos**. Este punto de entrada tiene **Usuarios y grupos** como **Tipo de recurso de actividad** preseleccionado.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/93.png "Registros de auditoría")

### <a name="enterprise-applications-audit-logs"></a>Registros de auditoría de aplicaciones empresariales

Con los informes de auditoría basadas en aplicaciones, puede obtener respuestas a preguntas tales como:

* ¿Cuáles son las aplicaciones que se han agregado o actualizado?
* ¿Cuáles son las aplicaciones que se han quitado?
* ¿Ha cambiado el principal de servicio para una aplicación?
* ¿Se han cambiado los nombres de las aplicaciones?
* ¿Quién dio el consentimiento a una aplicación?

Si desea revisar los datos de auditoría relacionados con las aplicaciones, puede buscar una vista filtrada en **Registros de auditoría** en la sección **Actividad** de la hoja **Aplicaciones empresariales**. Este punto de entrada tiene **Aplicaciones empresariales** como **Tipo de recurso de actividad** preseleccionado.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/134.png "Registros de auditoría")

Puede filtrar aún más esta vista hasta simplemente **grupos** o simplemente **usuarios**.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/25.png "Registros de auditoría")


## <a name="next-steps"></a>Pasos siguientes
Consulte la [guía de informes de Azure Active Directory](active-directory-reporting-guide.md).


