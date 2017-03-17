---

title: Escenarios adicionales de licencias basadas en grupos de Azure Active Directory | Microsoft Docs
description: "Más escenarios de licencias basadas en grupos de Azure Active Directory"
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
ms.date: 03/03/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 09f0ed3f7624bb242c40868710fb3eae49cda906
ms.openlocfilehash: a739e2320f8ea42912d169353a956e2b2f551619
ms.lasthandoff: 03/01/2017


---

# <a name="scenarios-limitations-and-known-issues-with-using-groups-to-manage-licensing-in-azure-active-directory"></a>Escenarios, limitaciones y problemas conocidos con el uso de grupos para administrar las licencias en Azure Active Directory 

## <a name="group-based-licensing-using-dynamic-groups"></a>Administración de licencias basadas en grupos mediante grupos dinámicos

Las licencias basadas en grupos se pueden usar con cualquier grupo de seguridad, lo que significa que se pueden combinar con grupos dinámicos de Azure AD. Los grupos dinámicos ejecutan reglas en los atributos de objeto de usuario para agregar y quitar usuarios de grupos automáticamente.

Por ejemplo, podría crear varios grupos dinámicos, uno por cada conjunto de productos que desea asignar a los usuarios. Cada grupo contiene una regla para un atributo determinado de un usuario, que describe el conjunto de licencias que deben recibir. El atributo se puede asignar localmente y sincronizar con Azure AD, o se puede administrar directamente en la nube.

Una vez especificado el atributo, el usuario se agrega a uno o varios de estos grupos de licencias dinámicos. Las licencias se asignarán al usuario poco después. Cuando se quita el atributo, el usuario sale de los grupos y las licencias se retiran.

### <a name="example"></a>Ejemplo

En este ejemplo, tengo una solución de administración de identidades local que decide qué usuarios deben tener acceso a Microsoft Online Services. Usa extensionAttribute1 para almacenar un valor de cadena que representa las licencias que el usuario debe tener, y Azure AD Connect lo sincroniza con Azure AD. Quiero distribuir licencias de Office 365 E5 y EMS a mis usuarios. En Azure AD, creé dos grupos dinámicos, uno para cada producto, porque algunos usuarios pueden necesitar uno de los productos pero no el otro. Especifiqué la configuración de licencias y la regla de pertenencia dinámica en cada grupo. Este es su aspecto:

#### <a name="o365-e5--base-services"></a>O365 E5: servicios básicos

![Servicios básicos de Office&365; E5](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="ems-e5--licensed-users"></a>EMS E5: usuarios con licencia

![Usuarios con licencia de O365 E5](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

De forma local, modifiqué un usuario y establecí extensionAttribute1 en el valor de `EMS;E5_baseservices;`, porque quiero que tengan ambas licencias. Después de sincronizar el cambio con la nube, el usuario se agregó automáticamente a ambos grupos y, después, se asignaron las licencias.

![Establecimiento del atributo extensionAttribute1 del usuario](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

### <a name="modifying-a-dynamic-group-membership-rule"></a>Modificación de una regla de pertenencia a grupo dinámico

Hay que tener cuidado al modificar regla de pertenencia a un grupo existente. Cuando se cambia una regla, se quitan todos los usuarios del grupo, la regla se evalúa y los usuarios se agregan al grupo en función de las nuevas condiciones.

Si el grupo tiene licencias asignadas, durante el proceso se quitarán esas licencias a todos los usuarios. Después de evaluar la nueva regla y de volver a agregar los usuarios, se aplicarán nuevas licencias. Esto significa que los usuarios pueden sufrir una pérdida del servicio o, en algunos casos, la pérdida de datos.

Se recomienda que no cambie la regla de pertenencia a un grupo que se usa para la asignación de licencias. En su lugar, cree un nuevo grupo con la nueva regla y especifique la misma configuración de licencias que en el grupo original. Espere hasta que se hayan agregado los miembros y se hayan aplicado las licencias a todos los usuarios. Entonces ya puede eliminar el grupo original. Este método garantiza una transición segura y gradual a la nueva regla de pertenencia sin que los usuarios pierdan el acceso o los datos.


## <a name="multiple-groups-and-multiple-licenses"></a>Varios grupos y varias licencias

Un usuario puede ser miembro de varios grupos con licencias. Estos son algunos aspectos que hay que tener en cuenta:

1. Se pueden superponer varias licencias para el mismo producto y, como resultado, todos los servicios habilitados se aplican al usuario. Por ejemplo, hemos creado dos grupos de licencias: *E3: servicios básicos*, que contiene los servicios básicos que se van a implementar en primer lugar para todos los usuarios; *E3: servicios ampliados*, que contiene los servicios adicionales (Sway y Planner) que se van a probar con algunos usuarios, mientras que Yammer permanece deshabilitado para implementaciones futuras. En este ejemplo, el usuario se agregó a los dos grupos:

  ![ver servicios habilitados](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  Como resultado, tienen habilitados 7 de los 12 servicios en el producto pero solo utilizan una licencia para este producto.

2. Al seleccionar la licencia *E3* se muestran más detalles, incluida la información acerca de qué grupos hicieron que se habilitaran determinados servicios para el usuario.

  ![ver servicios habilitados por grupo](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexisting-with-group-licenses"></a>Coexistencia de licencias directas con licencias de grupo

Cuando un usuario hereda una licencia de un grupo, no es posible quitar o modificar esa asignación de licencias directamente en el objeto de usuario. En su lugar, los cambios se deben realizar en el grupo y, después, propagarse por el sistema para todos los usuarios.

Sin embargo, se puede asignar la misma licencia de SKU directamente al usuario además de la licencia heredada. Esto puede usarse, por ejemplo, para habilitar los servicios adicionales de la SKU solo para un usuario, sin que afecten a otros usuarios.

Las licencias asignadas directamente se pueden quitar sin que afecten a las licencias heredadas. Veamos el siguiente usuario que hereda una licencia de *Office 365 Enterprise E3* de un grupo, lo que habilita una serie de servicios.

1. Inicialmente, el usuario hereda la licencia solo del grupo *E3: servicios básicos*. Esto habilita los 4 planes de servicio que se muestran a continuación:

  ![Servicios habilitados por el grupo E3](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. Al hacer clic en el botón **Asignar**, podemos asignar una licencia E3 directamente al usuario. En este caso, vamos a deshabilitar todos los planes de servicio excepto Yammer Enterprise.

  ![asignar licencias a un usuario](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. Como resultado, el usuario sigue consumiendo solo una licencia del producto E3; sin embargo, la asignación directa habilita el servicio *Yammer Enterprise* solo para ese usuario. En la lista puede ver qué servicios se habilitan por la pertenencia al grupo frente a la asignación directa:

  ![herencia frente a asignación directa](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. Se permiten las siguientes operaciones mediante asignación directa:

  a. *Yammer Enterprise* puede desactivarse en el objeto de usuario directamente. Tenga en cuenta que el control de alternancia Activar/Desactivar está habilitado a diferencia de otros servicios; el motivo es que este servicio está habilitado en el usuario y, por lo tanto, se puede modificar.

  b. Otros servicios pueden estar habilitados también, como parte de la licencia asignada directamente.

  c. El botón **Quitar** puede utilizarse para quitar la licencia directa del usuario. Puede ver que el usuario ahora solo tiene la licencia de grupo heredada y solo los servicios originales permanecen habilitados.

    ![quitar asignación directa](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="usage-location"></a>Ubicación de uso

Algunos servicios de Microsoft no están disponibles en todas las ubicaciones; antes de asignar una licencia a un usuario, el administrador debe especificar la propiedad "Ubicación de uso" del usuario. Esto puede hacerse en la sección Usuario &gt; Perfil &gt; Configuración de [Azure Portal](https://portal.azure.com).

Cuando se utiliza la asignación de licencias de grupo, los usuarios sin ubicación de uso especificada heredarán la ubicación del directorio. Si hay usuarios en distintas ubicaciones, asegúrese de reflejarlo correctamente en los objetos de usuario antes de agregar usuarios a grupos con licencias.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Uso de PowerShell para ver quién tiene licencias directas y heredadas

Durante la versión preliminar pública, PowerShell no puede usarse para controlar totalmente las asignaciones de licencias a grupos. Sin embargo, se puede usar para detectar información básica sobre el estado de los usuarios y las licencias son heredadas de un grupo o asignadas directamente. El siguiente código de ejemplo muestra cómo un administrador puede generar un informe básico acerca de las asignaciones de licencias.

1. Ejecute el cmdlet `connect-msolservice` para autenticarse y conectar con su inquilino.

2. `Get-MsolAccountSku` puede usarse para detectar todas las licencias de SKU aprovisionadas en el inquilino.

  ![uso del cmdlet Get-Msolaccountsku](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. En este ejemplo, queremos averiguar qué usuarios tienen la licencia *EMS* asignada directamente, mediante un grupo, o ambas. Se usará un script de PowerShell que contiene dos funciones que pueden responder a esta pregunta para un objeto de usuario y una SKU
  ```
  # Returns TRUE if the user has the license assigned directly

  function UserHasLicenseAssignedDirectly
  {
      Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

      foreach($license in $user.Licenses)
      {
          # we look for the specific license SKU in all licenses assigned to the user
          if ($license.AccountSkuId -ieq $skuId)
          {
              # GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              # This could be a group object or a user object (contrary to what the name suggests)
              # If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past

              if ($license.GroupsAssigningLicense.Count -eq 0)
              {
                  return $true
              }
              # If the collection contains the ID of the user object, this means the license is assigned directly
              # Note: the license may also be assigned through one or more groups in addition to being assigned directly
              foreach ($assignmentSource in $license.GroupsAssigningLicense)
              {
                  if ($assignmentSource -ieq $user.ObjectId)
                  {
                      return $true
                  }

              }
              return $false
          }
      }
      return $false
  }
  # Returns TRUE if the user is inheriting the license from a group
  function UserHasLicenseAssignedFromGroup
  {
      Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
      foreach($license in $user.Licenses)
      {
          # we look for the specific license SKU in all licenses assigned to the user
          if ($license.AccountSkuId -ieq $skuId)
          {
              # GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              # This could be a group object or a user object (contrary to what the name suggests)
              foreach ($assignmentSource in $license.GroupsAssigningLicense)
              {
                  # If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                  # Note: the license may also be assigned directly in addition to being inherited
                  if ($assignmentSource -ine $user.ObjectId)
                  {
                      return $true
                  }
              }
              return $false
          }
      }
      return $false
  }
  ```
4. El resto del script obtiene todos los usuarios y ejecuta estas funciones en cada usuario; después, da formato a la salida en una tabla.

  ```
  # the license SKU we are interested in
  $skuId = "reseller-account:EMS"
  # find all users that have the SKU license assigned
  Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses.AccountSKUID -eq $skuId} | select `
      ObjectId, `
      @{Name="SkuId";Expression={$skuId}}, `
      @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly $_ $skuId)}}, `
      @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup $_ $skuId)}}
  ```

5. La salida del script completo tiene el siguiente aspecto:

  ![Salida del script de PowerShell](media/active-directory-licensing-group-advanced/powershell-script-output.png)

## <a name="limitations-and-known-issues"></a>Limitaciones y problemas conocidos

1. Actualmente, las licencias basadas en grupos no admiten "grupos anidados" (grupos que contienen otros grupos). Si aplica una licencia a un grupo anidado, solo se aplicarán a los usuarios miembros del primer nivel inmediato del grupo.

2. Actualmente, las licencias basadas en grupos solo están disponibles a través de [Azure Portal](https://portal.azure.com). En este momento, no se puede usar PowerShell para establecer o modificar las licencias en grupos.

3. Actualmente, el [portal de administración de Office 365](https://portal.office.com ) no admite licencias basada en grupos. Si un usuario hereda una licencia de un grupo, esta licencia se mostrará en el portal de administración de Office como una licencia de usuario normal. Si intenta modificar esa licencia (por ejemplo, para deshabilitar un servicio en la licencia, o intentar quitar la licencia) el portal devolverá un mensaje de error (porque las licencias heredadas del grupo no se pueden modificar directamente en un usuario).

  `One or more of the licenses could not be modified because they are inherited from a group membership. To view or modify group based licenses visit the Azure admin portal.`

4. Cuando se quita un usuario de un grupo y pierde la licencia, los planes de servicio de esa licencia (por ejemplo, Exchange Online o SharePoint Online) pasan a un estado "suspendido" en lugar de un estado deshabilitado final. Esto sirve como medida de precaución para evitar la eliminación accidental de datos de usuario si un administrador comete un error en la administración de la pertenencia a un grupo.

  Vamos a implementar un flujo de trabajo en el que esos planes de servicio finalmente se deshabilitarán por completo para esos usuarios. Hasta que eso esté disponible, algunos servicios pueden seguir funcionando para los usuarios que se quitaron de un grupo y ya no tienen licencia.

5. Cuando se asignan o se modifican licencias en un grupo muy grande de usuarios (por ejemplo, 100 000 usuarios), el gran número de cambios generado por la automatización de Azure AD puede afectar negativamente al rendimiento de la sincronización de directorios entre Azure AD y los sistemas locales. Esto podría provocar retrasos de sincronización de directorios en su entorno.

6. La automatización de la administración de licencias no reacciona automáticamente a todos los tipos de cambios en el entorno. Por ejemplo, se queda sin licencias y algunos usuarios están en estado de error "No hay suficientes licencias". Puede quitar a otros usuarios algunas licencias asignadas directamente para liberar el número de puestos disponibles. Sin embargo, el sistema no reacciona automáticamente a este cambio ni corrige el estado de error de los usuarios.

  Como alternativa a estos tipos de limitaciones, puede ir a la hoja de grupo en Azure AD y hacer clic en el botón **Volver a procesar**. Esto procesará a todos los usuarios de ese grupo y resolverá los estados de error, si es posible.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre otros escenarios de administración de licencias basadas en grupos, consulte:

* [¿En qué consisten las licencias basadas en grupos de Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Asignación de licencias a un grupo en Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identificación y resolución de problemas de licencias de un grupo en Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Migración de usuarios individuales con licencia a licencias basadas en grupos en Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)

