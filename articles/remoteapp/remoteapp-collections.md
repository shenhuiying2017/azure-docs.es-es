---
title: "¿Qué tipo de colección necesita para Azure RemoteApp? | Microsoft Docs"
description: "Obtenga información sobre los tipos de colecciones disponibles con RemoteApp de Azure."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: c13ec78d-07e9-4646-8194-cf3efafc1760
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 7891ce3b04cd5978440dd8af73dc95d1d123ed92
ms.lasthandoff: 03/31/2017


---
# <a name="what-kind-of-collection-do-you-need-for-azure-remoteapp"></a>¿Qué tipo de colección necesita para Azure RemoteApp?
> [!IMPORTANT]
> Azure RemoteApp dejará de estar disponible el 31 de agosto de 2017. Para obtener más información, lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

RemoteApp de Azure permite compartir aplicaciones y recursos con usuarios de cualquier dispositivo. Para ello se crean colecciones para contener las aplicaciones y los recursos y, a continuación, se comparten dichas colecciones con los usuarios. Existen 2 opciones de colecciones distintas con diferentes opciones de autenticación y red. ¿Cuál es la que más le conviene?

Vamos a examinar las distintas consideraciones y opciones que deberá tener en cuenta para sacar el máximo partido a su colección de RemoteApp de Azure. 

## <a name="quick-differences-between-the-collection-types"></a>Diferencias rápidas entre los tipos de colecciones
|  | Nube | Híbrida |
| --- | --- | --- |
| Uso de una red virtual existente |Sí |Sí |
| Requiere cuentas conectadas a AD (DirSync) |No |Sí |
| Requiere la unión a un dominio |No |Sí |
| Requiere un controlador de dominio disponible para la red virtual |No |Sí |

## <a name="cloud-collections"></a>Colecciones de nube
* Rapidez de creación: la colección se aprovisiona rápidamente, lo que significa que le resultará más fácil hacer llegar las aplicaciones a los usuarios.
* Ofrecer sus propias aplicaciones o compartir las nuestras. Puede usar una imagen personalizada (creada desde una máquina virtual de Azure) o una de las imágenes incluidas con su suscripción.
* No es necesario configurar ninguna conexión entre la colección y su dominio local.
* Sin embargo, también puede usar su propia red virtual de Azure para proporcionar acceso a su entorno local para compartir datos o usar la autenticación que no es de Windows en recursos como SQL Server (mediante autenticación de base de datos).

¿Cómo crear una?

* ¿Solo en la nube? Créela con la opción **Creación rápida** del portal.
* ¿Nube + red virtual? Créelas mediante la opción **Crear con red virtual** , pero no seleccione la opción de unirse a un dominio.

## <a name="hybrid-collections"></a>Colecciones híbridas
* Proporcione acceso total a la red local + red virtual de Azure.
* Incluye el acceso conjunto de dominio para las aplicaciones y los datos. Las aplicaciones remotas se pueden autenticar con Active Directory local. A continuación, podrán tener acceso a los recursos del dominio.
* Habilitar la supervisión y administración avanzadas con las soluciones de System Center existentes y las directivas de grupo de Windows (mediante una imagen personalizada basada en Windows Server 2012 R2)
* Compatibilidad con [ExpressRoute](https://azure.microsoft.com/services/expressroute/) para conectar la red virtual de Azure a la red virtual local.

Créela mediante la opción **Crear con red virtual** y seleccione la opción de unirse a un dominio.

## <a name="authentication-options"></a>Opciones de autenticación
RemoteApp de Azure admite tanto cuentas de Microsoft como cuentas de Azure Active Directory; aunque no todas las colecciones admiten todos los métodos. 

| Tipo de cuenta |  | Nube | Nube + red virtual | Híbrida |
| --- | --- | --- | --- | --- |
| Cuenta Microsoft | |Sí |Sí |No |
| Azure Active Directory (Azure AD) | | | | |
| Solo Azure AD |Sí |Sí |No | |
| AD Connect con sincronización de contraseñas |Sí |Sí |Sí | |
| AD Connect sin sincronización de contraseñas |Sí |Sí |No | |
| AD Connect con AD FS |Sí |Sí |Sí | |
| Proveedores de identidades de terceros compatibles con Azure (por ejemplo, Ping) |Sí |Sí |Sí | |
| Multi-Factor Authentication | |Sí |Sí |Sí |

### <a name="cloud-and-cloud--vnet"></a>Nube y nube + red virtual
Con las colecciones de la nube, puede usar cuentas de Microsoft, cuentas de Azure AD o una combinación de ambas. Use las cuentas que funcionan mejor para los usuarios.

No hay ningún requisito específico en cuanto al uso de cuentas Microsoft. 

Si desea usar cuentas de Azure AD, deberá asegurarse de que el inquilino de Azure AD coincide con el asociado a su suscripción. Cuando se creó la suscripción de RemoteApp de Azure, el inquilino de Azure AD que estaba usando se asocia automáticamente a su suscripción. Los usuarios de Azure AD a los que conceda permiso deberá usar el mismo inquilino. Si es necesario, puede [cambiar el inquilino de Azure AD](remoteapp-changetenant.md) asociado a su suscripción.

### <a name="hybrid-or-cloud--azure-ad--ad"></a>Híbrida (o nube + Azure AD + AD)
El uso de Azure AD + Active Directory local es un requisito previo para las colecciones híbridas. Debe usar AD Connect para integrar los dos directorios. Sin embargo, tendrá algunas opciones en cuanto a la configuración de AD Connect. 

Existen 2 escenarios de AD Connect: uso de sincronización de contraseñas o uso de la federación de AD. Consulte la [Información de AD Connect](../active-directory/active-directory-aadconnect.md) para averiguar cuál de estas opciones es la que más le conviene.

También puede usar Azure AD + AD con una colección de nube. Asegúrese de que seguir los mismos pasos de configuración.

Consulte [Requisitos de Azure AD + Active Directory para RemoteApp de Azure](remoteapp-ad.md) para obtener información sobre los pasos necesarios para configurar Azure AD y Active Directory.

## <a name="go-create-your-collection"></a>¡Ya está preparado para crear su colección!
Bueno, creo que ya lo tenemos claro. Solo queda una cosa por hacer: crear la primera colección de RemoteApp de Azure.

[Crear una colección de nube](remoteapp-create-cloud-deployment.md) o [crear una colección híbrida](remoteapp-create-hybrid-deployment.md): ya está preparado para crear su colección.


