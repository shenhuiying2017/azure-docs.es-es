---
title: "Topologías admitidas de Azure AD Connect | Microsoft Docs"
description: "En este tema se detallan las topologías admitidas y no admitidas de Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 50cf58c7d2d9be4644ada4feae02d0d5219a3fd6
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="topologies-for-azure-ad-connect"></a>Topologías de Azure AD Connect
En este artículo se describen diversas topologías locales y de Azure Active Directory (Azure AD) que usan Azure AD Connect Sync como solución de integración de claves. En este artículo se describen tanto las configuraciones admitidas como las no admitidas.

Esta es la leyenda de las imágenes del artículo:

| DESCRIPCIÓN | Símbolo |
| --- | --- |
| Bosque local de Active Directory |![Bosque local de Active Directory](./media/active-directory-aadconnect-topologies/LegendAD1.png) |
| Instancia local de Active Directory con importación filtrada |![Active Directory con importación filtrada](./media/active-directory-aadconnect-topologies/LegendAD2.png) |
| Servidor de Azure AD Connect Sync |![Servidor de Azure AD Connect Sync](./media/active-directory-aadconnect-topologies/LegendSync1.png) |
| "Modo de ensayo" del servidor de Azure AD Connect Sync |!["Modo de ensayo" del servidor de Azure AD Connect Sync](./media/active-directory-aadconnect-topologies/LegendSync2.png) |
| GALSync con Forefront Identity Manager (FIM) 2010 o Microsoft Identity Manager (MIM) 2016 |![GALSync con FIM 2010 o MIM 2016](./media/active-directory-aadconnect-topologies/LegendSync3.png) |
| Servidor de Azure AD Connect Sync, información detallada |![Servidor de Azure AD Connect Sync, información detallada](./media/active-directory-aadconnect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/active-directory-aadconnect-topologies/LegendAAD.png) |
| Escenario no admitido |![Escenario no admitido](./media/active-directory-aadconnect-topologies/LegendUnsupported.png) |

## <a name="single-forest-single-azure-ad-tenant"></a>Un único bosque, un único inquilino de Azure AD
![Topología para un único bosque y un solo inquilino](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

La topología más común es un único bosque local, con uno o varios dominios y un único inquilino de Azure AD. Para la autenticación de Azure AD se utiliza la sincronización de contraseña. La instalación rápida de Azure AD Connect solo es compatible con esta topología.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Bosque único, varios servidores de sincronización con un inquilino de Azure AD
![Topología no admitida, filtrada para un único bosque](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

No se pueden tener varios servidores de Azure AD Connect Sync conectados al mismo inquilino de Azure AD (excepto si se trata de un [servidor de ensayo](#staging-server)). No se admite aunque los servidores estén configurados para sincronizarse con un conjunto de objetos que se excluyen mutuamente. Quizás haya pensado en esta topología si no puede acceder a todos los dominios del bosque desde un único servidor, o si desea distribuir la carga entre varios servidores.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Varios bosques, un único inquilino de Azure AD
![Topología para varios bosques y un solo inquilino](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Muchas organizaciones tienen entornos con varios bosques de Active Directory locales. Existen varias razones para tener más de un bosque de Active Directory local. Los ejemplos más habituales son los diseños con bosques de cuenta-recurso o los que resultan de una fusión o adquisición.

Cuando hay varios bosques, todos los bosques deben ser accesibles mediante un único servidor de Azure AD Connect Sync. No es necesario unir el servidor a un dominio. Si es necesario para llegar a todos los bosques, puede colocar el servidor en una red perimetral (también conocida como DMZ, zona desmilitarizada y subred filtrada).

El asistente para la instalación de Azure AD Connect ofrece varias opciones para consolidar los usuarios representados en varios bosques. El objetivo es que un usuario esté representado solo una vez en Azure AD. Hay algunas topologías habituales que puede configurar en la ruta de acceso de instalación personalizada del Asistente para instalación. En la página **Identificación de forma exclusiva de usuarios**, seleccione la opción correspondiente que representa su topología. La consolidación solo se configura para los usuarios. Los grupos duplicados no se consolidan con la configuración predeterminada.

Las topologías comunes se describen en las siguientes secciones: [Topologías independientes](#multiple-forests-separate-topologies), [Malla completa](#multiple-forests-full-mesh-with-optional-galsync) y [Cuenta-recurso](#multiple-forests-account-resource-forest).

En la configuración predeterminada de Azure AD Connect Sync se supone que:

* Los usuarios tienen solo una cuenta habilitada y el bosque donde se encuentra esta cuenta se usa para la autenticación del usuario. Esta suposición es válida para la sincronización de contraseñas y para la federación. Los valores UserPrincipalName y sourceAnchor/immutableID proceden de este bosque.
* Cada usuario tiene un solo buzón de correo.
* El bosque que aloja el buzón de un usuario tiene la mejor calidad de datos para los atributos visibles en la lista global de direcciones (GAL) de Exchange. Si no hay ningún buzón para el usuario, se puede usar cualquiera de los bosques para aportar estos valores de atributo.
* Si tiene un buzón vinculado, también hay otra cuenta en otro bosque que se usa para el inicio de sesión.

Si su entorno no se ajusta a estos supuestos, ocurrirá lo siguiente:

* Si tiene más de una cuenta activa o más de un buzón, el motor de sincronización elegirá uno y pasará por alto los demás.
* No se exporta un buzón vinculado sin ninguna otra cuenta activa a Azure AD. La cuenta de usuario no se representa como miembro de ningún grupo. Un buzón vinculado en DirSync siempre se representa como un buzón normal. Este cambio es un comportamiento diferente intencionado para admitir mejor los escenarios de varios bosques.

Para más información, consulte la [descripción de la configuración predeterminada](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Varios bosques, varios servidores de sincronización con un inquilino de Azure AD
![Topología no admitida para varios bosques y varios servidores de sincronización](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

No se puede tener más de un servidor de Azure AD Connect Sync conectado a un único inquilino de Azure AD. La excepción es el uso de un [servidor provisional](#staging-server).

### <a name="multiple-forests-separate-topologies"></a>Varios bosques: topologías independientes
![Opción para representar los usuarios una sola vez en todos los directorios](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Representación de varios bosques y topologías independientes](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

En este entorno, todos los bosques locales se tratan como entidades independientes. Ningún usuario está presente en ningún otro bosque. Cada bosque tiene su propia organización de Exchange y no se produce la sincronización de las GAL entre los bosques. Esta topología podría darse después de una fusión o adquisición, o en una organización donde cada unidad de negocio funciona de forma independiente. En Azure AD, estos bosques estarán en la misma organización y aparecerán con una GAL unificada. En la imagen anterior, todos los objetos de cada bosque se representan una vez en el metaverso y se agregan al inquilino de Azure AD de destino.

### <a name="multiple-forests-match-users"></a>Varios bosques: coincidencia de usuarios
Algo que es común a todos estos escenarios es que los grupos de distribución y de seguridad pueden contener una combinación de usuarios, contactos y entidades de seguridad externas (FSP). Las FSP se usan en Active Directory Domain Services para representar a miembros de otros bosques en un grupo de seguridad. Todas las FSP se resuelven en el objeto real en Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Varios bosques: malla completa con GALSync opcional
![Opción para usar el atributo de correo electrónico para buscar coincidencias cuando existen identidades de usuario en varios directorios](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Topología de malla completa para varios bosques](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Una topología de malla completa permite encontrar a los usuarios y recursos de cualquier bosque. Normalmente, hay una relación de confianza bidireccional entre los bosques.

Si Exchange está presente en más de un bosque, podría haber una solución GALSync local (opcional). Cada usuario se representaría como un contacto en todos los demás bosques. GALSync normalmente se implementa mediante FIM 2010 o MIM 2016. No se puede usar Azure AD Connect en GALSync local.

En este escenario, los objetos de identidad se unen mediante el atributo mail. Un usuario con un buzón en un bosque se une a los contactos de los demás bosques.

### <a name="multiple-forests-account-resource-forest"></a>Varios bosques: bosque de cuenta-recurso
![Opción para usar los atributos ObjectSID y msExchMasterAccountSID para coincidencia cuando existen identidades de usuario en varios directorios](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Topología de bosque de cuenta-recurso para varios bosques](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

En una topología de bosque de cuenta-recurso, tiene uno o más bosques de *cuentas* con cuentas de usuario activas. También tiene uno o más bosques de *recursos* con las cuentas deshabilitadas.

En este escenario uno (o más) bosques de recursos confían en todos los bosques de cuentas. Normalmente, el bosque de recursos tiene un esquema de Active Directory extendido con Exchange y Lync. Todos los servicios de Exchange y Lync, así como otros servicios compartidos, se encuentran en este bosque. Los usuarios tienen una cuenta de usuario deshabilitada en este bosque y el buzón está vinculado al bosque de cuenta.

## <a name="office-365-and-topology-considerations"></a>Consideraciones sobre la topología y Office 365
Algunas cargas de trabajo de Office 365 presentan ciertas restricciones en cuanto a las topologías compatibles:

| Carga de trabajo | Restricciones |
--------- | ---------
| Exchange Online | Para más información sobre las topologías híbridas que se admiten en Exchange Online, consulte [Implementaciones híbridas con varios bosques de Active Directory](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype Empresarial | Cuando se usan varios bosques locales, solo se admite la topología de bosque de cuenta-recurso. Para más información, consulte los [requisitos de entorno de Skype para Business Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |


## <a name="staging-server"></a>Servidor provisional
![Servidor de ensayo en una topología](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect admite la instalación de un segundo servidor en *modo de ensayo*. Un servidor en este modo puede leer los datos de todos los directorios conectados, pero no puede escribir nada en ellos. Utiliza el ciclo de sincronización normal y, por lo tanto, tiene una copia actualizada de los datos de identidades.

En caso de error del servidor principal, puede conmutar por error al servidor de ensayo. Puede hacerlo en el Asistente de Azure AD Connect. Este segundo servidor se puede ubicar preferiblemente en un centro de datos diferente porque no comparte ninguna infraestructura con el servidor principal. Cualquier cambio de configuración realizado en el servidor principal debe copiarlo manualmente en el segundo servidor.

Un servidor de ensayo se puede usar también para probar una nueva configuración personalizada y el efecto que tendrá en los datos. En este sentido, puede obtener una vista previa de los cambios y ajustar la configuración. Cuando esté satisfecho con la nueva configuración, puede hacer del servidor de ensayo el servidor activo y establecer el antiguo servidor activo en modo de ensayo.

Este método también se puede usar para reemplazar el servidor de sincronización activo. Prepare el nuevo servidor y establézcalo en modo de ensayo. Asegúrese de que está en buen estado, deshabilite el modo de ensayo (para activar el servidor) y apague el servidor actualmente activo.

Es posible tener más de un servidor provisional si desea tener varias copias de seguridad en distintos centros de datos.

## <a name="multiple-azure-ad-tenants"></a>Varios inquilinos de Azure AD
Recomendamos tener un único inquilino en Azure AD para una organización.
Antes de planear el uso de varios inquilinos de Azure AD, vea el artículo [Administración de unidades administrativas en Azure AD](../active-directory-administrative-units-management.md). Se ocupa de los escenarios comunes donde puede usar un solo inquilino.

![Topología para varios bosques y varios inquilinos](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Existe una relación 1:1 entre un servidor de Azure AD Connect Sync y un inquilino de Azure AD. Para cada inquilino de Azure AD, necesita una instalación de servidor de Azure AD Connect Sync. Las instancias del inquilino de Azure AD están aisladas por diseño. Es decir, los usuarios de un inquilino no pueden ver los usuarios del otro inquilino. Si desea esta separación, esta es una configuración admitida. En caso contrario, debe usar el modelo de un único inquilino de Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Cada objeto se incluye una sola vez en un inquilino de Azure AD
![Topología filtrada para un único bosque](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

En esta topología, un servidor de Azure AD Connect Sync está conectado a cada inquilino de Azure AD. Los servidores de Azure AD Connect Sync deben estar configurados para el filtrado, de modo que cada uno tenga un conjunto de objetos mutuamente excluyente sobre el que operar. Puede, por ejemplo, definir el ámbito de cada servidor en un dominio o unidad organizativa concretos.

Un dominio DNS solo se puede registrar en un único inquilino de Azure AD. Los UPN de los usuarios de la instancia de Active Directory local deben usar también espacios de nombres independientes. Por ejemplo, en la imagen anterior, se registran tres sufijos UPN independientes en la instancia de Active Directory local: contoso.com, fabrikam.com y wingtiptoys.com. Los usuarios de cada dominio de Active Directory local usan un espacio de nombres diferente.

No se sincronizan las GAL de las distintas instancias de inquilino de Azure AD. La libreta de direcciones de Exchange Online y Skype Empresarial solo muestra los usuarios del mismo inquilino.

Esta topología presenta las siguientes restricciones en escenarios que, por lo demás, se admiten:

* Solo uno de los inquilinos de Azure AD puede habilitar una implementación híbrida de Exchange con la instancia local de Active Directory.
* Los dispositivos de Windows 10 solo pueden asociarse con un inquilino de Azure AD.
* La opción de inicio de sesión único (SSO) para la sincronización de contraseñas y la autenticación de paso a través solo puede utilizarse con un inquilino de Azure AD.

El requisito de un conjunto de objetos mutuamente excluyente también se aplica a la escritura diferida. Algunas características de escritura diferida no se admiten con esta topología, porque estas asumen una sola configuración local. Estas características son:

* Escritura diferida de grupos con la configuración predeterminada.
* Escritura diferida de dispositivos.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Cada objeto se incluye varias veces en un inquilino de Azure AD
![Topología no admitida para un bosque único y varios inquilinos](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Topología no admitida para un bosque único y varios conectores](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

No se admiten las siguientes tareas:

* Sincronizar el mismo usuario con varios inquilinos de Azure AD.
* Realizar un cambio de configuración para que los usuarios de un directorio de Azure AD aparezcan como contactos en otro inquilino de Azure AD.
* Modificar el servidor de Azure AD Connect Sync para conectarse a varios inquilinos de Azure AD.

### <a name="galsync-by-using-writeback"></a>GALSync mediante escritura diferida
![Topología no admitida para varios bosques y varios directorios, con GALSync centrado en Azure AD](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Topología no admitida para varios bosques y varios directorios, con GALSync centrado en una instancia de Azure AD](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Los inquilinos de Azure AD están aislados por diseño. No se admiten las siguientes tareas:

* Cambiar la configuración del servidor de Azure AD Connect Sync para leer datos de otro inquilino de Azure AD.
* Exportar usuarios como contactos a otro directorio local de Active Directory mediante Azure AD Connect Sync.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync con servidor de sincronización local
![GALSync en una topología para varios bosques y varios directorios](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

Puede usar FIM 2010 o MIM 2016 local para sincronizar los usuarios (mediante GALSync) entre dos organizaciones de Exchange. Los usuarios de una organización se mostrarán como usuarios o contactos externos en la otra organización. Estas instancias de Active Directory locales se pueden sincronizar después con sus propios inquilinos de Azure AD.

## <a name="next-steps"></a>pasos siguientes
Para más información sobre cómo instalar Azure AD Connect en estos escenarios, consulte [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Obtenga más información sobre la [integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
