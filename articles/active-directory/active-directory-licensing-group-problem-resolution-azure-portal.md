---

title: "Identificación y resolución de problemas de licencias de un grupo en Azure Active Directory | Microsoft Docs"
description: "Identificación y resolución de problemas de asignación de licencias basadas en grupos mediante Azure Active Directory"
services: active-directory
keywords: Licencias de Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 6a9cebafd1ad8f513bfab897970241f7b82b2a53
ms.openlocfilehash: 9a434cf35d7934dc5eb759851fb65ad2a9f06eef
ms.lasthandoff: 02/22/2017


---

# <a name="identifying-and-resolving-license-problems-for-a-group-in-azure-active-directory"></a>Identificación y resolución de problemas de licencias de un grupo en Azure Active Directory


La licencia basada en grupos de Azure Active Directory (Azure AD) introduce el concepto del estado de error de licencias de usuarios. En este artículo, se explica por qué los usuarios pueden tener este estado. Cuando se asignan licencias directamente a usuarios individuales, sin usar las licencias basadas en grupos, la operación de asignación puede generar errores. Por ejemplo, cuando el administrador ejecuta el cmdlet de PowerShell `Set-MsolUserLicense` en un usuario, el cmdlet puede producir un error por una serie de motivos relacionados con la lógica de negocios, como un número insuficiente de licencias o un conflicto entre dos planes de servicio que no se pueden asignar al mismo tiempo. Al usuario que ejecuta el comando se le informa inmediatamente del problema.

Si se usan las licencias basadas en grupos, pueden ocurrir los mismos errores, pero se producirán en segundo plano mientras el servicio Azure AD está asignando licencias; por este motivo, no se puede informar inmediatamente al administrador. En su lugar, los errores se registran en el objeto de usuario y se notifican a través del portal de administración. Nunca se pierde la intención original de asignar licencias al usuario, pero se puede aplicar en estado activo o registrarse en estado de error a efectos de futuras investigaciones y resoluciones.

Para buscar usuarios con estado de error en cada grupo, abra la hoja de cada grupo y, en **Licencias**, aparecerá una notificación si hay algún usuario con estado de error. Seleccione la notificación para abrir una ventana en la que se indiquen todos los usuarios afectados, que pueden consultarse uno por uno para comprender el problema subyacente. En este artículo, se va a describir cada posible problema y la forma de resolverlo.

## <a name="not-enough-licenses"></a>No hay suficientes licencias

No hay suficientes licencias disponibles para uno de los productos especificados en el grupo. Necesita adquirir más licencias para el producto o liberar las licencias sin usar de otros usuarios o grupos.

Para ver cuántas licencias están disponibles, vaya a **Azure Active Directory &gt; Licencias &gt; Todos los productos**.

Para ver qué usuarios y grupos consumen licencias, haga clic en un producto. En **Usuarios con licencias** verá todos los usuarios a los que se han asignado licencias directamente o a través de uno o varios grupos. En **Grupos con licencias** verá todos los grupos que tienen ese producto asignado.

## <a name="conflicting-service-plans"></a>Planes de servicio en conflicto

Uno de los productos especificados en el grupo contiene un plan de servicio que entra en conflicto con otro plan de servicio que ya está asignado al usuario a través de un producto diferente. Algunos planes de servicio se configuran de tal forma que no puedan asignarse al mismo usuario como otro plan de servicio relacionado.

Tenga en cuenta el ejemplo siguiente: un usuario tiene una licencia de Office 365 Enterprise **E1** asignada directamente, con todos los planes habilitados. Se ha agregado el usuario a un grupo que tiene asignado el producto Office 365 Enterprise **E3**. Este producto contiene planes de servicio que no pueden superponerse entre E1 y E3, por lo que la asignación de licencia de grupo generará el error “Planes de servicio en conflicto”. En este ejemplo, los planes de servicio en conflicto son:

-   SharePoint Online (Plan 2) entra en conflicto con SharePoint Online (Plan 1)

-   Exchange Online (Plan 2) entra en conflicto con Exchange Online (Plan 1)

Para resolver este conflicto, debe deshabilitar esos dos planes en la licencia de E1 directamente asignada al usuario, o bien modificar la asignación de licencia de grupo completa y deshabilitar los planes de la licencia de E3. Como alternativa, puede quitar la licencia de E1 al usuario si es redundante en el contexto de la licencia de E3.

El administrador es la única persona competente para decidir cómo resolver el conflicto entre las licencias de productos. Por tanto, Azure AD no resolverá automáticamente los conflictos de licencia.

## <a name="other-products-depend-on-this-license"></a>Otros productos dependen de esta licencia

Uno de los productos especificados en el grupo contiene un plan de servicio que debe habilitarse para que otro plan de servicio, de otro producto, funcione. Este error se produce cuando Azure AD intenta quitar el plan de servicio subyacente, por ejemplo, como resultado de quitar al usuario del grupo.

## <a name="usage-location-not-allowed"></a>No se permite la ubicación de uso

Algunos servicios de Microsoft no están disponibles en todas las ubicaciones por las leyes y los reglamentos locales. Antes de poder asignar una licencia a un usuario, el administrador tiene que especificar la propiedad "Ubicación de uso" para el usuario. Esto se puede hacer en la sección **Usuario &gt; Perfil &gt; Configuración** de Azure Portal.

Cuando se utiliza la asignación de licencias de grupo, los usuarios sin ubicación de uso especificada heredarán la ubicación del directorio. Si hay usuarios en ubicaciones donde los planes no están disponibles, piense en modificar la asignación de licencias a nivel de grupo para deshabilitar los planes afectados. De forma alternativa, puede mover dichos usuarios a un grupo diferente cuyas asignaciones de licencia no entren en conflicto con la ubicación.

Si hay usuarios en distintas ubicaciones, asegúrese de reflejarlo correctamente en los objetos de usuario antes de agregar usuarios a grupos con licencias.

## <a name="what-happens-when-there-is-more-than-1-product-license-on-a-group"></a>¿Qué ocurre si hay más de una licencia de producto en un grupo?

Puede asignar más de una licencia de producto a un grupo. Por ejemplo, podría asignar Office 365 Enterprise E3 y Enterprise Mobility + Security a un grupo para habilitar fácilmente todos los servicios incluidos para los usuarios.

Azure AD intentará asignar todas las licencias especificadas en el grupo a cada usuario. Si no se puede asignar uno de los productos debido a problemas de lógica de negocios (por ejemplo, no hay suficientes licencias o en conflicto con otros servicios habilitados para el usuario), tampoco se asignarán las demás licencias del grupo.

Podrá ver los usuarios con los que se han producido errores de asignación y a qué productos ha afectado esta situación.

## <a name="how-to-force-processing-of-licenses-in-a-group-to-resolve-errors"></a>¿Cómo se puede forzar el procesamiento de licencias de un grupo para resolver errores?

Dependiendo de qué pasos se han realizado para resolver los errores, puede ser necesario desencadenar manualmente el procesamiento de un grupo para actualizar el estado de usuario.

Por ejemplo, si ha adquirido más licencias para cubrir a todos los usuarios, debe desencadenar el procesamiento de grupos con los que anteriormente se produjeron errores por asignar licencias íntegramente a todos los miembros. Para ello, busque la hoja del grupo, abra **Licencias** y seleccione el botón **Reprocesar** en la barra de herramientas.


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre otros escenarios de administración de licencias a través de grupos, vea

* [Assigning licenses to a group in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) (Asignación de licencias a un grupo en Azure Active Directory)
* [¿En qué consisten las licencias basadas en grupos de Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Migración de usuarios individuales con licencia a licencias basadas en grupos en Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory group-based licensing additional scenarios](active-directory-licensing-group-advanced.md) (Escenarios adicionales de licencias basadas en grupos de Azure Active Directory)

