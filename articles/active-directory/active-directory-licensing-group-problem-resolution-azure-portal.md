---
title: "Resolución de problemas de licencias para un grupo en Azure Active Directory | Microsoft Docs"
description: "Identificación y resolución de problemas de asignación de licencias cuando se utilizan licencias basadas en grupos con Azure Active Directory"
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
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 955efc9e6b209195935d1f7c13f96c6a42536b2a
ms.contentlocale: es-es
ms.lasthandoff: 09/27/2017

---

# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identificación y resolución de problemas de asignación de licencias de un grupo en Azure Active Directory

Las licencias basadas en grupos de Azure Active Directory (Azure AD) incorpora el concepto de usuarios en estado de error de licencias. En este artículo, se explica los motivos por los que los usuarios pueden terminar en este estado.

Cuando se asignan licencias directamente a usuarios individuales, sin usar las licencias basadas en grupos, la operación de asignación puede generar errores. Por ejemplo, al ejecutar el cmdlet de PowerShell `Set-MsolUserLicense` en un usuario, el cmdlet puede generar un error por diversos motivos relacionados con la lógica empresarial. Por ejemplo, podría haber un número insuficiente de licencias o un conflicto entre dos planes de servicio que no se puedan asignar al mismo tiempo. El problema se le notifica inmediatamente.

Cuando se usan licencias basadas en grupo, se pueden producir los mismos errores, pero se producen en segundo plano cuando el servicio de Azure AD está asignando las licencias. Por este motivo, los errores no se comunican inmediatamente. En su lugar, los errores se registran en el objeto de usuario y se notifican a través del portal de administración. Tenga en cuenta que nunca se pierde la intención original de asignar la licencia al usuario, pero se registra en estado de error a efectos de futuras investigaciones y resoluciones.

## <a name="how-to-find-license-assignment-errors"></a>Cómo buscar errores de asignación de licencias

1. Para buscar usuarios con un estado de error en un grupo específico, abra la hoja correspondiente al grupo. En **Licencias**, se muestra una notificación si hay usuarios en estado de error.

![Grupo, notificación de error](media/active-directory-licensing-group-problem-resolution-azure-portal/group-error-notification.png)

2. Haga clic en la notificación para abrir una lista de todos los usuarios afectados. Puede hacer clic individualmente en cada usuario para ver más detalles.

![Grupo, lista de usuarios con estado de error](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-users-with-errors.png)

3. Para buscar todos los grupos que contienen al menos un error, en la hoja **Azure Active Directory** seleccione **Licencias** y, luego, **Información general**. Si algunos grupos requieren atención, aparece un cuadro de información.

![Información general, información sobre los grupos con estado de error](media/active-directory-licensing-group-problem-resolution-azure-portal/group-errors-widget.png)

4. Haga clic en el cuadro para ver una lista de todos los grupos con errores. Puede hacer clic en cada grupo para más detalles.

![Información general, lista de grupos con errores](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-groups-with-errors.png)


A continuación se muestra una descripción de cada problema potencial y la manera de resolverlo.

## <a name="not-enough-licenses"></a>No hay suficientes licencias

**Problema:** no hay suficientes licencias disponibles para uno de los productos especificados en el grupo. Necesita adquirir más licencias para el producto o liberar las licencias sin usar de otros usuarios o grupos.

Para ver cuántas licencias hay disponibles, vaya a **Azure Active Directory** > **Licencias** > **Todos los productos**.

Para ver qué usuarios y grupos consumen las licencias, haga clic en un producto. En **Usuarios con licencias**, verá todos los usuarios a los que se han asignado licencias directamente o a través de uno o varios grupos. En **Grupos con licencias**, verá todos los grupos que tienen ese producto asignado.

**PowerShell:** Los cmdlets de PowerShell informan de este error como _CountViolation_.

## <a name="conflicting-service-plans"></a>Planes de servicio en conflicto

**Problema:** uno de los productos especificados en el grupo contiene un plan de servicio que entra en conflicto con otro plan de servicio que ya está asignado al usuario a través de un producto diferente. Algunos planes de servicio se configuran de tal forma que no puedan asignarse al mismo usuario como otro plan de servicio relacionado.

Considere el ejemplo siguiente. Un usuario tiene una licencia de Office 365 Enterprise **E1** asignada directamente, con todos los planes habilitados. Se ha agregado el usuario a un grupo que tiene asignado el producto Office 365 Enterprise **E3**. Este producto contiene planes de servicio que no pueden superponerse con los planes incluidos en E1, por lo que la asignación de licencia de grupo generará el error “Planes de servicio en conflicto”. En este ejemplo, los planes de servicio en conflicto son:

-   SharePoint Online (Plan 2) entra en conflicto con SharePoint Online (Plan 1).
-   Exchange Online (Plan 2) entra en conflicto con Exchange Online (Plan 1).

Para resolver este conflicto, debe deshabilitar los dos planes en la licencia de E1 que se asigna directamente al usuario. O bien, debe modificar toda la asignación de licencias de grupo y deshabilitar los planes de la licencia de E3. Como alternativa, puede quitar la licencia de E1 al usuario si es redundante en el contexto de la licencia de E3.

El administrador es la única persona competente para decidir cómo resolver el conflicto entre las licencias de productos. Azure AD no resuelve automáticamente los conflictos de licencias.

**PowerShell:** Los cmdlets de PowerShell informan de este error como _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Otros productos dependen de esta licencia

**Problema:** uno de los productos especificados en el grupo contiene un plan de servicio que debe habilitarse para que otro plan de servicio, de otro producto, funcione. Este error se produce cuando Azure AD intenta quitar el plan del servicio subyacente. Por ejemplo, esto puede ocurrir porque el usuario se ha quitado del grupo.

Para solucionar este problema, debe asegurarse de que el plan necesario todavía está asignado a los usuarios a través de algún otro método o que los servicios dependientes están deshabilitados para los usuarios. Después, puede quitar correctamente la licencia de grupo a esos usuarios.

**PowerShell:** Los cmdlets de PowerShell informan de este error como _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>No se permite la ubicación de uso

**Problema:** algunos servicios de Microsoft no están disponibles en todas las ubicaciones por las leyes y los reglamentos locales. Antes de poder asignar una licencia a un usuario, tendrá que especificar la propiedad "Ubicación de uso" para el usuario. Puede hacerlo en la sección **Usuario** > **Perfil** > **Configuración** de Azure Portal.

Cuando Azure AD intenta asignar una licencia de grupo a un usuario cuya ubicación de uso no se admite, se produce un error y este se registra en el usuario.

Para solucionar este problema, quite los usuarios de las ubicaciones no admitidas en el grupo con licencia. O bien, si los valores de ubicación de uso actual no representan la ubicación de los usuarios reales, puede modificarlos para que la próxima vez las licencias se asignen correctamente (si se admite la nueva ubicación).

**PowerShell:** Los cmdlets de PowerShell informan de este error como _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Cuando Azure AD asigna licencias de grupo, los usuarios sin ubicación de uso especificada heredan la ubicación del directorio. Se recomienda que los administradores establezcan valores de ubicación de uso correctos en los usuarios antes de utilizar licencias basadas en grupo para cumplir con la normativa y la legislación local.

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>¿Qué ocurre si hay más de una licencia de producto en un grupo?

Puede asignar más de una licencia de producto a un grupo. Por ejemplo, puede asignar Office 365 Enterprise E3 y Enterprise Mobility + Security a un grupo para habilitar fácilmente todos los servicios incluidos para los usuarios.

Azure AD intenta asignar todas las licencias especificadas en el grupo a cada usuario. Si Azure AD no puede asignar uno de los productos debido a problemas de lógica empresarial (por ejemplo, no hay suficientes licencias para todos o hay conflictos con otros servicios habilitados para el usuario), tampoco se asignarán las demás licencias del grupo.

Podrá ver los usuarios con los que se han producido errores de asignación y a qué productos ha afectado esta situación.

## <a name="how-to-manage-licenses-for-products-with-prerequisites"></a>¿Cómo administrar licencias de productos con requisitos previos?

Algunos productos de Microsoft Online que puede tener son considerados "complementos" y, antes de asignarlos, debe habilitar un plan de servicio de requisitos previos para que un usuario o grupo de usuarios puedan usarlos. Cuando se usan licencias basadas en grupo, el sistema le solicitará que mantenga en el mismo grupo los planes de servicios de requisitos previos y de complementos. Esto se hace para garantizar que los usuarios que se agregan al grupo puedan recibir el producto de trabajo completo. Vea el siguiente ejemplo:

*Microsoft Workplace Analytics* es un producto complementario. Contiene un plan de servicio único con el mismo nombre. Este plan de servicio solo se puede asignar a un usuario o grupo, cuando uno de los siguientes requisitos previos se asignan también:
- *Exchange Online (plan 1)*
- o *Exchange Online (plan 2)*

Si se intenta asignar este producto por sí solo a un grupo, el portal devolverá un error. Si hace clic en la notificación de error verá la siguiente información:

![Falta el requisito previo del grupo.](media/active-directory-licensing-group-problem-resolution-azure-portal/group-prerequisite-required.png)

Al hacer clic en los detalles,verá el mensaje de error siguiente:

*Se produjo un error en la operación de licencia. Asegúrese de que el grupo tiene los servicios necesarios antes de agregar o quitar un servicio dependiente. **Para poder activar también el servicio Microsoft Workplace Analytics, debe tener Exchange Online (plan 2).***

Para asignar esta licencia de complemento a un grupo, es necesario asegurarse de que el grupo también contiene el plan de servicio de requisitos previos. Por ejemplo, es posible actualizar un grupo existente que ya contenga el producto *Office 365 E3* al completo, y agregar al mismo el complemento.

También es posible crear un grupo independiente que contenga sólo los productos necesarios para que el complemento funcione; igualmente, se puede usar para proporcionar licencia a los usuarios seleccionados del producto complementario. En este ejemplo se han asignado los siguientes productos al mismo grupo:
- *Office 365 Enterprise E3*, con solo el plan de servicio *Exchange Online (plan 2)* habilitado.
- *Microsoft Workplace Analytics*

![El requisito previo del grupo está incluido.](media/active-directory-licensing-group-problem-resolution-azure-portal/group-addon-with-prerequisite.png)

De ahora en adelante, cualquier usuario que se agregue a este grupo utilizará una licencia del producto E3 y una licencia del producto Workplace Analytics. Al mismo tiempo, los usuarios pueden formar parte de otro grupo que les proporcione acceso a todo el producto E3, y solo tendrán que usar una licencia de ese producto.

> [!TIP]
> Puede crear varios grupos para cada plan de servicio de requisitos previos. Por ejemplo, si usa las versiones *Office 365 Enterprise **E1*** y *Office 365 Enterprise **E3*** de sus usuarios, podrá crear dos grupos para proporcionar licencias de *Microsoft Workplace Analytics*; una con E1 como requisito previo y la otra con E3. Gracias a esto, podrá distribuir el complemento a los usuarios de E1 y E3 sin tener que usar licencias adicionales.

## <a name="license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online"></a>Se produce un error en modo silencioso en la asignación de licencias para un usuario debido a direcciones proxy duplicadas en Exchange Online

Si se usa Exchange Online, es posible que algunos de los usuarios del inquilino no estén configurados correctamente con el mismo valor de dirección de proxy. Cuando la concesión de licencias basadas en grupo intenta asignar una licencia a un usuario de este tipo, se producirá un error y no se registrará (a diferencia de los demás casos de error descritos anteriormente): se trata de una limitación en la versión preliminar de esta característica que vamos a solucionar antes de la *disponibilidad general*.

> [!TIP]
> Si observa que algunos usuarios no han recibido una licencia y no hay ningún error registrado en esos usuarios, compruebe primero si tienen una dirección proxy duplicada.
> Puede hacerlo si ejecuta el siguiente cmdlet de PowerShell en Exchange Online:
```
Run Get-Recipient | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
```
> [Este artículo](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online) contiene más detalles sobre este problema, incluida la información sobre [cómo conectarse a Exchange Online mediante PowerShell remoto](https://technet.microsoft.com/library/jj984289.aspx).

Después de que se hayan resuelto los problemas de dirección proxy para los usuarios afectados, asegúrese de forzar el procesamiento de la licencia en el grupo para que ahora se puedan aplicar las licencias de nuevo.

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>¿Cómo se puede forzar el procesamiento de licencias de un grupo para resolver errores?

Dependiendo de qué pasos haya dado para resolver los errores, puede ser necesario desencadenar manualmente el procesamiento de un grupo para actualizar el estado del usuario.

Por ejemplo, si ha liberado algunas licencias quitando asignaciones de licencia directas de usuarios, debe desencadenar el procesamiento de grupos con los que anteriormente se produjeron errores por asignar licencias íntegramente a todos los miembros. Para ello, busque la hoja del grupo, abra **Licencias** y seleccione el botón **Reprocesar** en la barra de herramientas.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre otros escenarios de administración de licencias a través de grupos, consulte lo siguiente:

* [Assigning licenses to a group in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) (Asignación de licencias a un grupo en Azure Active Directory)
* [¿En qué consisten las licencias basadas en grupos de Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Migración de usuarios individuales con licencia a licencias basadas en grupos en Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Escenarios adicionales de licencias basadas en grupos de Azure Active Directory](active-directory-licensing-group-advanced.md)

