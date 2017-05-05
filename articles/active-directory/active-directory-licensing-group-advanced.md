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
ms.date: 03/20/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: a7240c52e9351a60e3cf577d8112862c7dc8d913
ms.lasthandoff: 03/14/2017


---

# <a name="scenarios-limitations-and-known-issues-with-using-groups-to-manage-licensing-in-azure-active-directory"></a>Escenarios, limitaciones y problemas conocidos con el uso de grupos para administrar las licencias en Azure Active Directory

Use la información y los ejemplos siguientes para obtener una descripción más avanzada de las licencias basadas en grupos de Azure Active Directory (Azure AD).

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Uso de licencias basadas en grupos con grupos dinámicos

Puede usar licencias basadas en grupos con cualquier grupo de seguridad, lo que significa que se pueden combinar con grupos dinámicos de Azure AD. Los grupos dinámicos ejecutan reglas en los atributos de objeto de usuario para agregar y quitar usuarios de grupos automáticamente.

Por ejemplo, podría crear varios grupos dinámicos, uno por cada conjunto de productos que desea asignar a los usuarios. Cada grupo contiene una regla para un atributo determinado de un usuario, que describe el conjunto de licencias que deben recibir. Puede asignar el atributo en el entorno local y sincronizarlo con Azure AD, o bien puede administrar el atributo directamente en la nube.

Cuando especifica el atributo, el usuario se agrega a uno o varios de estos grupos de licencias dinámicos. Las licencias se asignan al usuario poco después. Cuando se quita el atributo, el usuario sale de los grupos y se quitan las licencias.

### <a name="example"></a>Ejemplo

Considere una solución de administración de identidades local que decide qué usuarios deben tener acceso a qué servicios web de Microsoft. Usa **extensionAttribute1** para almacenar un valor de cadena que representa las licencias que el usuario debe tener. Azure AD Connect lo sincroniza con Azure AD.

En este ejemplo, el objetivo es distribuir licencias de Office 365 Enterprise E5 y Enterprise Mobility + Security a los usuarios. En Azure AD, cree dos grupos dinámicos, uno para cada producto. Esto se debe a que es posible que algunos usuarios necesiten uno de los productos y no el otro. Luego, especifique la configuración de licencias y la regla de pertenencia dinámica en cada grupo. Este es su aspecto:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: servicios básicos

![Captura de pantalla de los servicios básicos de Office 365 Enterprise E5](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: usuarios con licencia

![Captura de pantalla de los usuarios con licencia de Enterprise Mobility + Security](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

En este ejemplo, modifique un usuario y establezca el valor de extensionAttribute1 en `EMS;E5_baseservices;`. Esto se debe a que desea que este usuario tenga ambas licencias. Tenga en cuenta que puede hacer esta modificación en el entorno local. Después de sincronizar el cambio con la nube, el usuario se agrega automáticamente a ambos grupos y se asignan las licencias.

![Captura de pantalla que muestra cómo establecer el extensionAttribute1 del usuario](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

### <a name="modify-a-dynamic-group-membership-rule"></a>Modificación de una regla de pertenencia a un grupo dinámico

Tenga cuidado al modificar la regla de pertenencia de un grupo existente. Cuando se cambia una regla, se quitan todos los usuarios del grupo. La regla se evalúa y, luego, se agregan usuarios al grupo en virtud de las condiciones nuevas.

Si el grupo tiene licencias asignadas, durante el proceso se quitan esas licencias a todos los usuarios. Las licencias nuevas se aplican solo después de que se evalúa la regla nueva y se agregan nuevamente los usuarios. Esto significa que los usuarios pueden sufrir una pérdida del servicio o, en algunos casos, la pérdida de datos.

Se recomienda que no cambie la regla de pertenencia a un grupo que se usa para la asignación de licencias. En su lugar, cree un nuevo grupo con la nueva regla y especifique la misma configuración de licencias que en el grupo original. Espere hasta que se hayan agregado los miembros y se hayan aplicado las licencias a todos los usuarios. Solo entonces puede eliminar el grupo original. Este enfoque garantiza una transición segura y gradual a la nueva regla de pertenencia sin que los usuarios pierdan el acceso o los datos.


## <a name="multiple-groups-and-multiple-licenses"></a>Varios grupos y varias licencias

Un usuario puede ser miembro de varios grupos con licencias. Estos son algunos aspectos que hay que tener en cuenta:

- Se pueden superponer varias licencias para el mismo producto y, como resultado, todos los servicios habilitados se aplican al usuario. El ejemplo siguiente muestra dos grupos de licencias. *E3: servicios básicos*, que contiene los servicios fundamentales que se implementarán en primer lugar para todos los usuarios. Y, además, *E3: servicios extendidos*, que contiene los servicios adicionales (Sway y Planner) que se van a implementar solo en algunos usuarios. Tenga en cuenta que Yammer permanece deshabilitado para implementaciones futuras. En este ejemplo, el usuario se agregó a los dos grupos:

  ![Captura de pantalla de los servicios habilitados](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  Como resultado, el usuario tiene habilitados 7 de los 12 servicios del producto, mientras que solo usa una licencia para este producto.

- Al seleccionar la licencia *E3* se muestran más detalles, incluida la información acerca de qué grupos hicieron que se habilitaran determinados servicios para el usuario.

  ![Captura de pantalla de los servicios habilitados por grupo](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>Coexistencia de licencias directas con licencias de grupo

Cuando un usuario hereda una licencia de un grupo, no es posible quitar o modificar esa asignación de licencias directamente en el objeto de usuario. En su lugar, los cambios se deben realizar en el grupo y, después, propagarse por el sistema para todos los usuarios.

Sin embargo, es posible asignar la misma licencia de producto directamente al usuario además de la licencia heredada. Esto puede usarse, por ejemplo, para habilitar los servicios adicionales del producto solo para un usuario, sin que afecten a otros usuarios.

Las licencias asignadas directamente se pueden quitar sin que afecten a las licencias heredadas. Por ejemplo, considere al usuario siguiente que hereda una licencia de Office 365 Enterprise E3 de un grupo.

1. Inicialmente, el usuario hereda la licencia solo del grupo *E3: servicios básicos*. Esto habilita 4 planes de servicio, tal como se muestra en la imagen siguiente.

  ![Captura de pantalla de los servicios de E3 habilitados por grupo](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. Si hace clic en **Asignar**, puede asignar directamente una licencia de E3 al usuario. En este caso, deshabilitará todos los planes de servicio, excepto Yammer Enterprise.

  ![Captura de pantalla sobre cómo asignar una licencia directamente a un usuario](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. Como resultado, el usuario sigue usando una sola licencia del producto E3, pero la asignación directa habilita el servicio de Yammer Enterprise solo para ese usuario. En la imagen siguiente se muestran los servicios habilitados por la pertenencia al grupo frente a la asignación directa.

  ![Captura de pantalla de la asignación heredada frente a la asignación directa](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. Cuando usa la asignación directa se permiten las siguientes operaciones:

  a. Yammer Enterprise se puede desactivar directamente en el objeto de usuario. Tenga en cuenta que el botón de alternancia **Activado/desactivado** está habilitado en este servicio, al revés de lo que ocurre con los botones de alternancia de otros servicios. Esto se debe a que este servicio se habilita directamente en el usuario y, por lo tanto, se puede modificar.

  b. Otros servicios pueden estar habilitados también, como parte de la licencia asignada directamente.

  c. El botón **Quitar** puede utilizarse para quitar la licencia directa del usuario. Puede ver que el usuario ahora solo tiene la licencia de grupo heredada y solo los servicios originales permanecen habilitados.

    ![Captura de pantalla que muestra cómo quitar la asignación directa](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="usage-location"></a>Ubicación de uso

Algunos servicios de Microsoft no están disponibles en todas las ubicaciones. Antes de poder asignar una licencia a un usuario, el administrador tiene que especificar la propiedad **Ubicación de uso** en el usuario. Esto se puede hacer en [Azure Portal](https://portal.azure.com), en **Usuario** &gt; **Perfil** &gt; **Configuración**.

En el caso de la asignación de licencias de grupo, cualquier usuario sin una ubicación de uso especificada heredará la ubicación del directorio. Si hay usuarios en distintas ubicaciones, asegúrese de reflejarlo correctamente en los objetos de usuario antes de agregar usuarios a grupos con licencias.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Uso de PowerShell para ver quién tiene licencias directas y heredadas

Durante el período de versión preliminar de una versión determinada de Azure AD, no se puede usar PowerShell para controlar totalmente las asignaciones de licencias de grupo. Sin embargo, se puede usar para detectar información básica sobre el estado de los usuarios y para determinar si las licencias son heredadas de un grupo o asignadas directamente. El siguiente ejemplo de código muestra cómo un administrador puede generar un informe básico sobre las asignaciones de licencias.

1. Ejecute el cmdlet `connect-msolservice` para autenticarse y conectar con el inquilino.

2. `Get-MsolAccountSku` se puede usar para detectar todas las licencias de producto aprovisionadas en el inquilino.

  ![Captura de pantalla del cmdlet Get-Msolaccountsku](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. En este ejemplo, desea averiguar qué usuarios tienen la licencia de Enterprise Mobility + Security asignada directamente, mediante un grupo, o ambas. Puede usar el siguiente script de PowerShell.
  
  ```
  #Returns TRUE if the user has the license assigned directly
  function UserHasLicenseAssignedDirectly
  {
      Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
      foreach($license in $user.Licenses)
      {
          #we look for the specific license SKU in all licenses assigned to the user
          if ($license.AccountSkuId -ieq $skuId)
          {
              #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              #This could be a group object or a user object (contrary to what the name suggests)
              #If the collection is empty, this means the license is assigned directly. This is the case for users who have never been licensed via groups in the past
              if ($license.GroupsAssigningLicense.Count -eq 0)
              {
                  return $true
              }
              #If the collection contains the ID of the user object, this means the license is assigned directly
              #Note: the license may also be assigned through one or more groups in addition to being assigned directly
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
  #Returns TRUE if the user is inheriting the license from a group
  function UserHasLicenseAssignedFromGroup
  {
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
     foreach($license in $user.Licenses)
     {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
          #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
          #This could be a group object or a user object (contrary to what the name suggests)
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
          {
                  #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                  #Note: the license may also be assigned directly in addition to being inherited
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

4. El resto del script obtiene a todos los usuarios y ejecuta estas funciones en cada uno de ellos. Luego, da formato a la salida como una tabla.
    
  ```
  #the license SKU we are interested in
  $skuId = "reseller-account:EMS"
  #find all users that have the SKU license assigned
  Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses.AccountSKUID -eq $skuId} | select `
      ObjectId, `
      @{Name="SkuId";Expression={$skuId}}, `
      @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly $_ $skuId)}}, `
      @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup $_ $skuId)}}
  ```

5. La salida del script completo tiene el siguiente aspecto:

  ![Captura de pantalla de la salida del script de PowerShell](media/active-directory-licensing-group-advanced/powershell-script-output.png)

## <a name="limitations-and-known-issues"></a>Limitaciones y problemas conocidos

Si usa licencias basadas en grupo, se recomienda que se familiarice con la siguiente lista de limitaciones y problemas conocidos.

- Actualmente, las licencias basadas en grupos no admiten "grupos anidados" (grupos que contienen otros grupos). Si aplica una licencia a un grupo anidado, solo se aplican a los usuarios miembros del primer nivel inmediato del grupo.

- No se pudieron modificar una o varias de las licencias porque se heredan de una pertenencia a un grupo. Para ver o modificar las licencias basadas en grupo, visite el portal de administración de Azure.

- Actualmente, el [portal de administración de Office 365](https://portal.office.com ) no admite licencias basada en grupos. Si un usuario hereda una licencia de un grupo, esta licencia aparece en el portal de administración de Office como una licencia de usuario normal. Si intenta modificar esa licencia (por ejemplo, para deshabilitar un servicio en la licencia) o intenta quitar la licencia, el portal devolverá un mensaje de error. Las licencias de grupo heredadas no se pueden modificar directamente en un usuario.

- Cuando se quita un usuario de un grupo y pierde la licencia, los planes de servicio de esa licencia (por ejemplo, Exchange Online o SharePoint Online) pasan a un estado "suspendido". Los planes de servicio no pasan a un estado deshabilitado final. Esto sirve como precaución para evitar la eliminación accidental de datos de usuario si un administrador comete un error en la administración de la pertenencia a un grupo.

- Cuando se asignan o modifican licencias para un grupo considerablemente grande (por ejemplo, con más de 100 000 usuarios), podría afectar el rendimiento. En concreto, el volumen de cambios generado por la automatización de Azure AD podría afectar negativamente el rendimiento de la sincronización de directorios entre Azure AD y los sistemas locales. Esto podría provocar retrasos de sincronización de directorios en su entorno.

- La automatización de la administración de licencias no reacciona automáticamente a todos los tipos de cambios en el entorno. Por ejemplo, es posible que se quede sin licencias, lo que haría que algunos usuarios tengan un estado de error. Para liberar el número de puestos disponibles, puede quitar algunas licencias asignadas directamente a otros usuarios. Sin embargo, el sistema no reacciona automáticamente a este cambio ni corrige el estado de error de los usuarios.

  Como alternativa a estos tipos de limitaciones, puede ir a la hoja **Grupo** en Azure AD y hacer clic en **Reprocesar**. Esto procesa a todos los usuarios de ese grupo y resuelve los estados de error, si es posible.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre otros escenarios de administración de licencias basadas en grupos, consulte:

* [¿En qué consisten las licencias basadas en grupos de Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Asignación de licencias a un grupo en Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identificación y resolución de problemas de licencias de un grupo en Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Migración de usuarios individuales con licencia a licencias basadas en grupos en Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)

