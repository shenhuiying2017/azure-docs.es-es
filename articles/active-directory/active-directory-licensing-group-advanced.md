---

title: Escenarios adicionales de licencias basadas en grupos de Azure Active Directory | Microsoft Docs
description: "Más escenarios de licencias basadas en grupos de Azure Active Directory"
services: active-directory
keywords: Licencias de Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: piotrci
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/02/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 9b5b87574edc828a41b421a64f543fc34742c5a3
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017


---

# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Escenarios, limitaciones y problemas conocidos del uso de grupos para administrar las licencias en Azure Active Directory

Use la información y los ejemplos siguientes para obtener una descripción más avanzada de las licencias basadas en grupos de Azure Active Directory (Azure AD).

## <a name="usage-location"></a>Ubicación de uso

Algunos servicios de Microsoft no están disponibles en todas las ubicaciones. Antes de poder asignar una licencia a un usuario, el administrador tiene que especificar la propiedad **Ubicación de uso** en el usuario. En [Azure Portal](https://portal.azure.com), puede especificar los datos de **Usuario** &gt; **Perfil** &gt; **Configuración**.

En el caso de la asignación de licencias de grupo, cualquier usuario sin una ubicación de uso especificada heredará la ubicación del directorio. Si hay usuarios en varias ubicaciones, asegúrese de reflejarlo correctamente en los objetos de usuario antes de agregar usuarios a grupos con licencias.

> [!NOTE]
> La asignación de licencias de grupo no modificará nunca un valor de ubicación de uso existente para un usuario. Se recomienda establecer la ubicación de uso siempre como parte del flujo de creación de usuarios en Azure AD (por ejemplo, mediante la configuración de AAD Connect), que garantizará que el resultado de la asignación de licencias siempre sea correcto y que los usuarios no reciban los servicios en ubicaciones que no están permitidas.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Uso de licencias basadas en grupos con grupos dinámicos

Puede usar licencias basadas en grupos con cualquier grupo de seguridad, lo que significa que se pueden combinar con grupos dinámicos de Azure AD. Los grupos dinámicos ejecutan reglas en los atributos de objeto de usuario para agregar y quitar usuarios de grupos automáticamente.

Por ejemplo, puede crear un grupo dinámico para un conjunto de productos que desee asignar a los usuarios. Cada grupo se rellena con una regla agregando los usuarios con sus atributos, y a cada grupo se le asignan las licencias que se desea que reciban. Puede asignar el atributo en el entorno local y sincronizarlo con Azure AD, o bien puede administrar el atributo directamente en la nube.

Las licencias se asignan al usuario poco después de haberlas agregado al grupo. Cuando se cambia el atributo, el usuario sale de los grupos y se quitan las licencias.

### <a name="example"></a>Ejemplo

Considere un ejemplo de solución local de administración de identidades que decide qué usuarios deben tener acceso a los servicios web de Microsoft. Usa **extensionAttribute1** para almacenar un valor de cadena que representa las licencias que el usuario debe tener. Azure AD Connect lo sincroniza con Azure AD.

Los usuarios podrían necesitar una licencia pero no otra, o bien podrían tener ambas. Este es un ejemplo, en el que va a distribuir licencias de Office 365 Enterprise E5 y Enterprise Mobility + Security (EMS) para los usuarios de los grupos:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: servicios básicos

![Captura de pantalla de los servicios básicos de Office 365 Enterprise E5](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: usuarios con licencia

![Captura de pantalla de los usuarios con licencia de Enterprise Mobility + Security](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

En este ejemplo, modifique un usuario y establezca el valor de extensionAttribute1 en `EMS;E5_baseservices;`, si desea que el usuario tenga ambas licencias. Puede hacer esta modificación en el entorno local. Después de sincronizar el cambio con la nube, el usuario se agrega automáticamente a ambos grupos y se asignan las licencias.

![Captura de pantalla que muestra cómo establecer el extensionAttribute1 del usuario](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

### <a name="modify-a-dynamic-group-membership-rule"></a>Modificación de una regla de pertenencia a un grupo dinámico

Tenga cuidado al modificar la regla de pertenencia de un grupo existente. Cuando se cambia una regla, se quitan todos los usuarios del grupo. La regla se evalúa y, luego, se agregan usuarios al grupo en virtud de las condiciones nuevas.

Si el grupo tiene licencias asignadas, durante el proceso se quitan esas licencias a todos los usuarios. Las licencias nuevas se aplican solo después de que se evalúa la regla nueva y se agregan nuevamente los usuarios. Los usuarios pueden sufrir una pérdida del servicio o, en algunos casos, la pérdida de datos.

Se recomienda que no cambie la regla de pertenencia a un grupo que se usa para la asignación de licencias. En su lugar, cree un grupo con una regla de pertenencia nueva y especifique la misma configuración de licencias que en el grupo original. Espere hasta que se agreguen todos los miembros y las licencias se apliquen a todos los usuarios. Solo entonces deberá eliminar el grupo original. Este enfoque garantiza una transición segura y gradual a una regla de pertenencia nueva, sin que los usuarios pierdan el acceso o los datos.


## <a name="multiple-groups-and-multiple-licenses"></a>Varios grupos y varias licencias

Un usuario puede ser miembro de varios grupos con licencias. Estos son algunos aspectos que hay que tener en cuenta:

- Se pueden superponer varias licencias para el mismo producto y, como resultado, todos los servicios habilitados se aplican al usuario. El ejemplo siguiente muestra dos grupos de licencias. *E3: servicios básicos*, que contiene los servicios fundamentales que se implementarán en primer lugar para todos los usuarios. Y, además, *E3: servicios extendidos*, que contiene los servicios adicionales (Sway y Planner) que se van a implementar solo en algunos usuarios. En este ejemplo, el usuario se agregó a los dos grupos:

  ![Captura de pantalla de los servicios habilitados](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  Como resultado, el usuario tiene habilitados 7 de los 12 servicios del producto, mientras que solo usa una licencia para este producto.

- Al seleccionar la licencia *E3* se muestran más detalles, incluida la información acerca de qué grupos hicieron que se habilitaran determinados servicios para el usuario.

  ![Captura de pantalla de los servicios habilitados por grupo](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>Coexistencia de licencias directas con licencias de grupo

Cuando un usuario hereda una licencia de un grupo, no se puede quitar ni modificar esa asignación de licencias en las propiedades del usuario. Los cambios se deben realizar en el grupo y, después, propagarse para todos los usuarios.

Sin embargo, es posible asignar la misma licencia de producto directamente al usuario además de la licencia heredada. Puede habilitar servicios adicionales del producto solo para un usuario, sin que afecten a otros.

Las licencias asignadas directamente se pueden quitar sin que afecten a las licencias heredadas. Considere un usuario que hereda una licencia de Office 365 Enterprise E3 de un grupo.

1. Inicialmente, el usuario hereda la licencia solo del grupo *E3: servicios básicos*, lo que habilita los siguientes cuatro planes de servicio:

  ![Captura de pantalla de los servicios de E3 habilitados por grupo](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. Puede seleccionar **Asignar** para asignar directamente una licencia E3 al usuario. En este caso, deshabilitará todos los planes de servicio, excepto Yammer Enterprise:

  ![Captura de pantalla sobre cómo asignar una licencia directamente a un usuario](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. Como resultado, el usuario sigue usando una sola licencia del producto E3, pero la asignación directa habilita el servicio de Yammer Enterprise solo para ese usuario. Puede ver qué servicios se habilitan por la pertenencia al grupo frente a la asignación directa:

  ![Captura de pantalla de la asignación heredada frente a la asignación directa](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. Cuando usa la asignación directa se permiten las siguientes operaciones:

  - Yammer Enterprise se puede desactivar directamente en el objeto de usuario. El botón de alternancia **Activado/Desactivado** de la ilustración se habilitó en este servicio, al revés de lo que ocurre con los botones de alternancia de otros servicios. Dado que el servicio se habilitó directamente en el usuario, se puede modificar.
  - Otros servicios pueden estar habilitados también, como parte de la licencia asignada directamente.
  - El botón **Quitar** puede utilizarse para quitar la licencia directa del usuario. Puede ver que el usuario ahora solo tiene la licencia de grupo heredada y solo los servicios originales permanecen habilitados:

    ![Captura de pantalla que muestra cómo quitar la asignación directa](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="managing-new-services-added-to-products"></a>Administración de servicios nuevos agregados a productos
Cuando Microsoft agrega un servicio nuevo a un producto, se habilitará de forma predeterminada en todos los grupos a los que se haya asignado la licencia del producto. Los usuarios de su inquilino que se suscriban a las notificaciones sobre cambios en el producto recibirán con antelación mensajes de correo electrónico que les informan sobre las próximas adiciones en los servicios.

Como administrador, puede revisar todos los grupos afectados por el cambio y realizar acciones como deshabilitar el nuevo servicio en cada grupo. Por ejemplo, si creó grupos destinados solo a servicios específicos para la implementación, puede volver a visitar esos grupos y asegurarse de que cualquier servicio recién agregado está deshabilitado.

Este es un ejemplo cómo podría ser este proceso:

1. Originalmente, asignó el producto *Office 365 Enterprise E5* a varios grupos. Uno de esos grupos, denominado *Office 365 E5 (solo Exchange)*, se diseñó para habilitar solamente el servicio *Exchange Online (Plan 2)* para sus miembros.

2. Recibió una notificación de Microsoft sobre que se extenderá el producto E5 con un nuevo servicio: *Microsoft Stream*. Cuando el servicio esté disponible en el inquilino, puede hacer lo siguiente:

3. Vaya a la hoja [ **Azure Active Directory > Licencias > Todos los productos** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) y seleccione *Office 365 Enterprise E5*; a continuación, seleccione **Licensed Groups** (Grupos con licencia) para ver una lista de todos los grupos con ese producto.

4. Haga clic en el grupo que desea revisar (en este caso, *O365 E5 - Exchange only*). Se abrirá la pestaña **Licencias**. Al hacer clic en la licencia E5, se abrirá una hoja en la que se enumeran todos los servicios habilitados.
> [!NOTE]
> El servicio *Microsoft Stream* se ha agregado automáticamente y se ha habilitado en este grupo, además del servicio *Exchange Online*:

  ![Captura de pantalla del nuevo servicio agregado a una licencia de grupo](media/active-directory-licensing-group-advanced/manage-new-services.png)

5. Si desea deshabilitar el nuevo servicio en este grupo, haga clic en el botón de alternancia **Activado/Desactivado** junto al servicio y haga clic en el botón **Guardar** para confirmar el cambio. Azure AD ahora procesará todos los usuarios del grupo para aplicar el cambio; los nuevos usuarios agregados al grupo no tendrán habilitado el servicio *Microsoft Stream*.

  > [!NOTE]
  > Los usuarios todavía pueden tener el servicio habilitado a través de otra asignación de licencias (de otro grupo del que sean miembros o con una asignación directa de licencia).

6. Si es necesario, realice los mismos pasos para otros grupos con este producto asignado.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Uso de PowerShell para ver quién tiene licencias directas y heredadas
Mientras la asignación de licencias basada en grupos se encuentre en la versión preliminar pública, PowerShell no puede usarse para controlar totalmente las asignaciones de licencias a grupos. Sin embargo, se puede usar para detectar información básica sobre el estado de los usuarios y comprobar si las licencias se heredan de un grupo o se asignan directamente. El siguiente ejemplo de código muestra cómo un administrador puede generar un informe básico sobre las asignaciones de licencias.

1. Ejecute el cmdlet `connect-msolservice` para autenticarse y conectar con el inquilino.

2. `Get-MsolAccountSku` se puede usar para detectar todas las licencias de producto aprovisionadas en el inquilino.

  ![Captura de pantalla del cmdlet Get-Msolaccountsku](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. En este ejemplo, desea averiguar qué usuarios tienen la licencia de Enterprise Mobility + Security asignada directamente, mediante un grupo, o ambas. Puede usar el siguiente script:

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
              \#If the collection contains the ID of the user object, this means the license is assigned directly
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

5. La salida completa del script es similar a este ejemplo:

  ![Captura de pantalla de la salida del script de PowerShell](media/active-directory-licensing-group-advanced/powershell-script-output.png)

## <a name="limitations-and-known-issues"></a>Limitaciones y problemas conocidos

Si usa licencias basadas en grupo, se recomienda que se familiarice con la siguiente lista de limitaciones y problemas conocidos.

- Actualmente, las licencias basadas en grupos no admiten grupos anidados que contengan otros grupos. Si aplica una licencia a un grupo anidado, solo se aplican a los usuarios miembros del primer nivel inmediato del grupo.

- La característica solo se puede utilizar con grupos de seguridad. Los grupos de Office no se admiten en este momento y no podrá utilizarlos en el proceso de asignación de licencias.

- Actualmente, el [portal de administración de Office 365](https://portal.office.com ) no admite licencias basada en grupos. Si un usuario hereda una licencia de un grupo, esta licencia aparece en el portal de administración de Office como una licencia de usuario normal. Si intenta modificar esa licencia o quitarla, el portal devuelve un mensaje de error. Las licencias de grupo heredadas no se pueden modificar directamente en un usuario.

- Cuando se quita un usuario de un grupo y pierde la licencia, los planes de servicio de esa licencia (por ejemplo, SharePoint Online) pasan a un estado **Suspendido**. Los planes de servicio no pasan a un estado deshabilitado final. Esta precaución puede evitar la eliminación accidental de los datos de usuario, si un administrador comete un error en la administración de la pertenencia a un grupo.

- Cuando se asignan o modifican licencias para un grupo grande (por ejemplo, con más de 100 000 usuarios), podría afectar el rendimiento. En concreto, el volumen de cambios generado por la automatización de Azure AD podría afectar negativamente el rendimiento de la sincronización de directorios entre Azure AD y los sistemas locales.

- La automatización de la administración de licencias no reacciona automáticamente a todos los tipos de cambios en el entorno. Por ejemplo, es posible que se quede sin licencias, lo que haría que algunos usuarios tengan un estado de error. Para liberar el número de puestos disponibles, puede quitar algunas licencias asignadas directamente a otros usuarios. Sin embargo, el sistema no reacciona automáticamente a este cambio ni corrige el estado de error de los usuarios.

  Como alternativa a estos tipos de limitaciones, puede ir a la hoja **Grupo** en Azure AD y hacer clic en **Reprocesar**. Este comando procesa a todos los usuarios de ese grupo y resuelve los estados de error, si es posible.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre otros escenarios de administración de licencias basadas en grupos, consulte:

* [¿En qué consisten las licencias basadas en grupos de Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Asignación de licencias a un grupo en Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identificación y resolución de problemas de licencias de un grupo en Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Migración de usuarios individuales con licencia a licencias basadas en grupos en Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)

