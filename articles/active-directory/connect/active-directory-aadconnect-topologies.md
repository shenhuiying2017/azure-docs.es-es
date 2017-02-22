---
title: "Topologías admitidas de Azure AD Connect | Microsoft Docs"
description: "En este tema se detallan las topologías admitidas y no admitidas de Azure AD Connect."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: article
ms.date: 11/01/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 28b5da6098316f8fbe84966e0dac88f5b7d2cb1d
ms.openlocfilehash: e5983496cda061b28418dcf86c38fbe80c611503


---
# <a name="topologies-for-azure-ad-connect"></a>Topologías de Azure AD Connect
El objetivo de este tema es describir diferentes topologías locales y de Azure AD con la sincronización de Azure AD Connect como la solución de integración clave. Se describen tanto las configuraciones admitidas como las no admitidas.

Leyenda de imágenes en el documento:

| Description | Icono |
| --- | --- |
| Bosque local de Active Directory |![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png) |
| Active Directory con importación filtrada |![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png) |
| Servidor de Azure AD Connect Sync |![Sync](./media/active-directory-aadconnect-topologies/LegendSync1.png) |
| "Modo provisional" del servidor de Azure AD Connect Sync |![Sync](./media/active-directory-aadconnect-topologies/LegendSync2.png) |
| GALSync con FIM2010 o MIM2016 |![Sync](./media/active-directory-aadconnect-topologies/LegendSync3.png) |
| Servidor de Azure AD Connect Sync, información detallada |![Sync](./media/active-directory-aadconnect-topologies/LegendSync4.png) |
| Directorio de Azure AD |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png) |
| Escenario no admitido |![No compatible](./media/active-directory-aadconnect-topologies/LegendUnsupported.png) |

## <a name="single-forest-single-azure-ad-tenant"></a>Un único bosque, un único inquilino de Azure AD
![Un único bosque, un único inquilino](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

La topología más común es un único bosque local, con uno o varios dominios y un único inquilino de Azure AD. Para la autenticación de Azure AD se utiliza la sincronización de contraseña. La instalación rápida de Azure AD Connect solo es compatible con esta topología.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Bosque único, varios servidores de sincronización con un inquilino de Azure AD
![Un único bosque, filtrado no compatible](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

No se puede tener más de un servidor de Azure AD Connect Sync conectado al mismo inquilino de Azure AD (excepto si se trata de un [servidor provisional](#staging-server)). No es compatible incluso si están configurados para sincronizar conjuntos de objetos que se excluyen mutuamente. Debe tener esto en cuenta si no puede acceder a todos los dominios del bosque desde un único servidor o distribuir la carga entre varios servidores.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Varios bosques, un único inquilino de Azure AD
![Varios bosques, un único inquilino](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Muchas organizaciones tienen entornos con varios bosques de Active Directory locales. Existen varias razones para tener más de un bosque de Active Directory local. Los ejemplos más habituales son los diseños con bosques de cuenta-recurso o los que resultan de una fusión o adquisición.

Cuando hay varios bosques, todos los bosques deben ser accesibles mediante un único servidor de Azure AD Connect Sync. No es necesario combinar el servidor con un dominio. Si es preciso acceder a todos los bosques, el servidor puede colocarse en una red DMZ.

El Asistente para instalación de Azure AD Connect ofrece varias opciones para consolidar los usuarios representados en varios bosques. El objetivo es que un usuario esté representado solo una vez en Azure AD. Hay algunas topologías habituales que puede configurar en la ruta de acceso de instalación personalizada del Asistente para instalación. Seleccione la opción correspondiente que representa a la topología en la página **Identificación de forma exclusiva de usuarios**. La consolidación solo se configura para usuarios. Los grupos duplicados no se consolidan con la configuración predeterminada.

Las topologías comunes se describen en las siguientes secciones: [Topologías independientes](#multiple-forests-separate-topologies), [Malla completa](#multiple-forests-full-mesh-with-optional-galsync) y [Cuenta-recurso](#multiple-forests-account-resource-forest).

En la configuración predeterminada de Azure AD Connect Sync se supone que:

1. Los usuarios tienen solo una cuenta habilitada y el bosque donde se encuentra esta cuenta se usa para la autenticación del usuario. Esta suposición es válida para la sincronización de contraseña y para la federación. Los valores UserPrincipalName y sourceAnchor/immutableID proceden de este bosque.
2. Los usuarios tienen solo un buzón.
3. El bosque que aloja el buzón de un usuario tiene la mejor calidad de datos para los atributos visibles en la lista global de direcciones (GAL) de Exchange. Si no hay ningún buzón en el usuario, puede usarse cualquiera de los bosques para aportar estos valores de atributo.
4. Si tiene un buzón vinculado, hay también otra cuenta en otro bosque que se usa para el inicio de sesión.

Si su entorno no se ajusta a estos supuestos, ocurre lo siguiente:

* Si tiene más de una cuenta activa o más de un buzón, el motor de sincronización elegirá uno y pasará por alto los demás.
* No se exporta un buzón vinculado sin ninguna otra cuenta activa a Azure AD. La cuenta de usuario no se representa como miembro de ningún grupo. Un buzón vinculado en DirSync siempre se representará como un buzón normal. Este cambio es intencionadamente un comportamiento diferente para admitir mejor los escenarios de varios bosques.

Para más información, consulte la [descripción de la configuración predeterminada](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Varios bosques, varios servidores de sincronización con un inquilino de Azure AD
![Varios bosques, sincronización múltiple no admitida](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

No se puede tener más de un servidor de Azure AD Connect Sync conectado a un único inquilino de Azure AD. La excepción es el uso de un [servidor provisional](#staging-server).

### <a name="multiple-forests--separate-topologies"></a>Varios bosques: topologías independientes
**Los usuarios solo se representan una vez en todos los directorios**

![Varios bosques, usuarios una vez](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Varios bosques, topologías independientes](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

En este entorno, todos los bosques locales se tratan como entidades independientes y ningún usuario estará presente en ningún otro bosque.
Cada bosque tiene su propia organización de Exchange y no hay ninguna GALSync entre los bosques. Esta topología podría ser la situación que se da después de una fusión o adquisición, o en una organización donde cada unidad de negocio funciona de forma aislada de las demás. En Azure AD, estos bosques estarán en la misma organización y aparecerán con una GAL unificada.
En esta imagen, todos los objetos de cada bosque se representarán una vez en el metaverso y se agregarán al inquilino de Azure AD de destino.

### <a name="multiple-forests--match-users"></a>Varios bosques: coincidencia de usuarios
**Existen identidades de usuario entre varios directorios**

Algo que es común a todos estos escenarios es que los grupos de distribución y de seguridad pueden contener una combinación de usuarios, contactos y FSP (entidades de seguridad externa).

Las FSP se usan en ADDS para representar a miembros de otros bosques en un grupo de seguridad. Todas las FSP se resuelven en el objeto real en Azure AD.

### <a name="multiple-forests--full-mesh-with-optional-galsync"></a>Varios bosques: malla completa con GALSync opcional
**Existen identidades de usuario entre varios directorios. Coincidencia mediante: atributo Mail**

![Varios bosques, correo de usuarios](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Varios bosques, malla completa](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Una topología de malla completa permite a los usuarios y los recursos ubicarse en cualquier bosque; normalmente habrá confianzas bidireccionales entre los bosques.

Si Exchange está presente en más de un bosque, podría haber opcionalmente una solución GALSync local. Cada usuario se representaría como un contacto en todos los otros bosques. GALSync se implementa normalmente mediante Forefront Identity Manager 2010 o Microsoft Identity Manager 2016. No se puede usar Azure AD Connect en GALSync local.

En este escenario, los objetos de identidad se combinan mediante el atributo mail. Un usuario con un buzón en un bosque se combina con los contactos en los otros bosques.

### <a name="multiple-forests--account-resource-forest"></a>Varios bosques: bosque de cuenta-recurso
**Existen identidades de usuario entre varios directorios. Coincidencia mediante: atributos ObjectSID y msExchMasterAccountSID**

![Varios bosques, ObjectSID de usuarios](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Varios bosques, AccountResource](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

En una topología de bosque de cuenta-recurso, tiene uno o más bosques de cuenta con cuentas de usuario activas. También tiene uno o más bosques de recursos con las cuentas deshabilitadas.

En este escenario uno (o más) **bosques de recursos** confían en todos los **bosques de cuentas**. Normalmente, el bosque de recursos tiene un esquema de AD extendido con Exchange y Lync. Todos los servicios de Exchange y Lync, así como otros servicios compartidos, se encuentran en este bosque. Los usuarios tienen una cuenta de usuario deshabilitada en este bosque y el buzón está vinculado al bosque de cuenta.

## <a name="office-365-and-topology-considerations"></a>Consideraciones sobre la topología y Office 365
Algunas cargas de trabajo de Office 365 presenta ciertas restricciones para las topologías compatibles. Si piensa utilizar alguna de ellas, lea el tema de las topologías admitidas para la carga de trabajo.

| Carga de trabajo |
| --- | --- |
| Exchange Online |
| Skype Empresarial |

## <a name="staging-server"></a>servidor provisional
![servidor provisional](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect admite la instalación de un segundo servidor en el **modo provisional**. Un servidor en este modo puede leer los datos de todos los directorios conectados, pero no puede escribir nada en ellos. Utiliza el ciclo de sincronización normal y, por tanto, tiene una copia actualizada de los datos de identidad. Ante un error en el servidor principal, puede conmutar por error al servidor provisional. Puede hacerlo en el Asistente de Azure AD Connect. Este segundo servidor se puede ubicar preferiblemente en un centro de datos diferente dado que no se comparte ninguna infraestructura con el servidor principal. Cualquier cambio de configuración realizado en el servidor principal debe copiarlo manualmente en el segundo servidor.

Un servidor provisional se puede usar también para probar una nueva configuración personalizada y el efecto que tendrá en los datos. En este sentido, puede obtener una vista previa de los cambios y ajustar la configuración. Cuando esté satisfecho con la nueva configuración, puede hacer del servidor provisional el servidor activo y establecer el antiguo servidor activo en modo provisional.

Este método también se puede utilizar para reemplazar el servidor de sincronización activo. Prepare el nuevo servidor y establézcalo en modo provisional. Asegúrese de que está en buen estado, deshabilite el modo provisional (activando el servidor) y apague el servidor actualmente activo.

Es posible tener más de un servidor provisional si desea tener varias copias de seguridad en distintos centros de datos.

## <a name="multiple-azure-ad-tenants"></a>Varios inquilinos de Azure AD
Microsoft recomienda tener un único inquilino en Azure AD para una organización.
Se recomienda la lectura de estos temas, que explican escenarios comunes que permiten usar un único inquilino, antes de considerar el uso de varios inquilinos de Azure AD.

| Tema. |
| --- | --- |
| Delegación mediante unidades administrativas |

![Varios bosques, varios inquilinos](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Existe una relación 1:1 entre un servidor de Azure AD Connect Sync y un inquilino de Azure AD. Para cada inquilino de Azure AD, necesita una instalación de servidor de Azure AD Connect Sync. Por diseño, las instancias de inquilino de Azure AD están aisladas y los usuarios de uno no podrán ver a los usuarios del otro inquilino. Si esta separación es intencionada, esta es una configuración admitida; en caso contrario, debe usar los modelos de inquilino único de Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Cada objeto se incluye una sola vez en un inquilino de Azure AD
![Un único bosque, filtrado](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

En esta topología, un servidor de Azure AD Connect Sync está conectado a cada inquilino de Azure AD. Los servidores de Azure AD Connect Sync deben estar configurados para el filtrado, de modo que cada uno tenga un conjunto de objetos mutuamente excluyente sobre el que operar. Puede, por ejemplo, definir el ámbito de cada servidor en un dominio o unidad organizativa concretos. Solo se puede registrar un dominio DNS en un único inquilino de Azure AD. Los UPN de los usuarios del directorio AD local deben usar también espacios de nombres independientes. Por ejemplo, en la imagen anterior tres sufijos UPN independientes se registran en el entorno local de AD: contoso.com, fabrikam.com y wingtiptoys.com. Los usuarios de cada dominio de Active Directory local usan otro espacio de nombres.

No hay ningún GALsync entre las instancias de inquilino de Azure AD. La libreta de direcciones de Exchange Online y Skype Empresarial solo muestra los usuarios del mismo inquilino.

Esta topología presenta las siguientes restricciones para escenarios, por lo demás, admitidos:

* Solo uno de los inquilinos de Azure AD puede habilitar la implementación híbrida de Exchange con la instancia de Active Directory local.
* Los dispositivos de Windows 10 solo pueden estar asociados a un inquilino de Azure AD.

El requisito del conjunto de objetos mutuamente excluyente también se aplica a la reescritura. Algunas características de reescritura no se admiten con esta topología, ya que estas asumen una sola configuración local.

* Escritura diferida de grupos con la configuración predeterminada
* Escritura diferida de dispositivos

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Cada objeto se incluye varias veces en un inquilino de Azure AD
![Un único bosque, varios inquilinos no admitidos](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Un único bosque, varios conectores no admitidos](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

* No se admite la sincronización del mismo usuario con varios inquilinos de Azure AD.
* No se admite realizar un cambio de configuración para que los usuarios de un directorio de Azure AD aparezcan como contactos en otro inquilino de Azure AD.
* Asimismo, no se admite la modificación del servidor de Azure AD Connect Sync para conectarse a varios inquilinos de Azure AD.

### <a name="galsync-by-using-writeback"></a>GALsync mediante reescritura
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Los inquilinos de Azure AD están aislados por diseño.

* No se admite el cambio de la configuración del servidor de Azure AD Connect Sync para leer datos de otro inquilino de Azure AD.
* No se admite la exportación de usuarios como contactos a otro directorio AD local mediante Azure AD Connect Sync.

### <a name="galsync-with-on-premises-sync-server"></a>GALsync con servidor de sincronización local
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

Se admite el uso de FIM2010/MIM2016 local para los usuarios de GALsync entre dos organizaciones de Exchange. Los usuarios de una organización se mostrarán como usuarios o contactos externos en la otra organización. Estos inquilinos de AD locales diferentes se pueden sincronizar luego con sus propios inquilinos de Azure AD.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre cómo instalar Azure AD Connect en estos escenarios, consulte [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Obtenga más información sobre la configuración de [sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).




<!--HONumber=Dec16_HO3-->


