---
title: "Publicación de aplicaciones en redes y ubicaciones independientes mediante grupos de conectores en el proxy de aplicación de Azure AD | Microsoft Docs"
description: "Explica cómo crear y administrar grupos de conectores en el Proxy de aplicación de Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 39049c7a1e2a4d61ef62bd06cda9ef1bb2c50c0b
ms.contentlocale: es-es
ms.lasthandoff: 05/05/2017

---

# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publicación de aplicaciones en redes independientes y ubicaciones mediante grupos de conectores
> [!div class="op_single_selector"]
> * [Portal de Azure](active-directory-application-proxy-connectors-azure-portal.md)
> * [Portal de Azure clásico](active-directory-application-proxy-connectors.md)
>

## <a name="azure-ad-application-proxy-and-connector-groups"></a>Grupos de conectores y proxy de aplicación de Azure AD

Los clientes usan el proxy de aplicación de Azure AD en cada vez más escenarios y aplicaciones. Por este motivo, hemos habilitado más topologías para que sea más flexible. Puede crear grupos de conectores del proxy de aplicación: una nueva funcionalidad que tiene como fin asignar conectores específicos para atender a aplicaciones concretas. Además, gracias a ella, ahora el proxy de aplicación puede usarse para realizar tareas que antes no eran posibles. 

El concepto básico es que cada conector del proxy de aplicación se asigne a un grupo de conectores. Todos los conectores que pertenecen al mismo grupo funcionan como un grupo independiente de alta disponibilidad y equilibrio de carga. De manera predeterminada, todos los conectores pertenecen a un grupo predeterminado. El administrador puede crear nuevos y cambiar estas asignaciones en Azure Portal. 

De forma predeterminada, todas las aplicaciones se asignan a un grupo de conectores predeterminado. Si el administrador no cambia nada, el sistema se sigue comportando de la misma manera. Si es el usuario quien no cambia nada, todas las aplicaciones asignadas al grupo de conector predeterminado incluirán todos los conectores. Sin embargo, si organiza los conectores en grupos, puede establecer que cada aplicación funcione con un grupo de conectores concreto. En este caso, solamente los conectores de ese grupo atenderán a la aplicación previa solicitud.


>[!NOTE] 
>Como los nuevos conectores se asignan automáticamente a un grupo de conectores predeterminado, para implementaciones a gran escala recomendamos no tener aplicaciones asignadas al grupo predeterminado. Por lo tanto, una vez instalados, los nuevos conectores no recibirán tráfico real. Solo después de asignar el conector a uno de los grupos activos, pueden empezar a obtener tráfico real. Gracias a esto, puede poner los conectores en un modo inactivo para realizar tareas de mantenimiento.
>

## <a name="prerequisite-create-your-connector-groups"></a>Requisito previo: Crear grupos de conectores
Para agrupar los conectores, debe asegurarse de [tener instalados varios conectores](active-directory-application-proxy-enable.md). Cuando se instala un nuevo conector, se une automáticamente al grupo de conectores **predeterminado** .

## <a name="step-1-create-connector-groups"></a>Paso 1: Creación de grupos de conectores
Puede crear tantos grupos de conectores como quiera. La creación de grupos de conectores se lleva a cabo en [Azure Portal](https://portal.azure.com).

1. Seleccione **Azure Active Directory** para ir al panel de administración de su directorio. Ahí, seleccione **Aplicaciones empresariales** > **Proxy de la aplicación**.
2. Seleccione el botón **Connector Groups** (Grupos de conectores). Aparece la hoja New Connector Group (Nuevo grupo de conectores).
3. Asigne un nombre al nuevo grupo de conectores y use luego el menú deplegable para seleccionar qué conectores pertenecen a este grupo.
4. Cuando el grupo de conectores esté completo, seleccione **Guardar** .

## <a name="step-2-assign-applications-to-your-connector-groups"></a>Paso 2: Asignación de aplicaciones a los grupos de conectores
El último paso es establecer cada aplicación en el grupo de conectores que la servirá.

1. En el panel de administración del directorio, seleccione **Aplicaciones empresariales** > **Todas las aplicaciones** > la aplicación que desea asignar a un grupo de conectores > **Proxy de la aplicación**.
2. En **Grupo de conectores**, use el menú desplegable para seleccionar el grupo que quiere que use la aplicación.
3. Seleccione **Guardar** para aplicar el cambio.

## <a name="use-cases-for-connector-groups"></a>Casos de uso de grupos de conectores 

Los grupos de conectores son útiles para diversos escenarios, por ejemplo:

###<a name="sites-with-multiple-interconnected-datacenters"></a>Sitios con varios centros de datos interconectados.

Muchas organizaciones tienen muchos centros de datos interconectados. En este caso, desea mantener el mayor tráfico posible dentro del centro de datos, ya que los vínculos entre centros de datos son costosos y lentos. Puede implementar conectores en cada centro de datos para atender solamente las aplicaciones que se encuentran en él. Con este enfoque se minimizan los vínculos entre centros de datos y se proporciona una experiencia totalmente transparente a los usuarios.

### <a name="applications-installed-on-isolated-networks"></a>Aplicaciones instaladas en redes aisladas

Las aplicaciones pueden hospedarse en redes que no forman parte de la red corporativa principal. Puede usar grupos de conectores para instalar conectores específicos en redes aisladas y así permitir también el aislamiento de aplicaciones en la red. Esto suele suceder cuando un proveedor de terceros mantiene una aplicación concreta para su organización. 

Los grupos de conectores permiten instalar conectores específicos en aquellas redes que solo publican determinadas aplicaciones, con lo que resulta más sencillo y seguro externalizar la administración de aplicaciones a proveedores de terceros.

### <a name="applications-installed-on-iaas"></a>Aplicaciones instaladas en IaaS 

En lo que respecta a las aplicaciones instaladas en IaaS para el acceso a la nube, los grupos de conectores proporcionan un servicio común para proteger el acceso a todas las aplicaciones. Los grupos de conectores no crean dependencia adicional en la red corporativa ni fragmentan la experiencia de la aplicación. Los conectores se pueden instalarse en cada centro de datos en la nube y servir solo las aplicaciones que residen en esta red. Puede instalar varios conectores para lograr alta disponibilidad.

En este caso, la organización tiene numerosas máquinas virtuales conectadas a su propia red virtual hospedada en IaaS. Para permitir que los empleados usen estas aplicaciones, estas redes privadas están conectadas a la red corporativa mediante VPN de sitio a sitio. De este modo, los empleados que trabajan en un entorno local podrán usarlas sin ningún problema. Sin embargo, puede que este enfoque no sea el ideal para los empleados remotos, ya que requiere contar con otra infraestructura local, como puede ver en el diagrama siguiente:

![Red Iaas de Azure AD](./media/application-proxy-publish-apps-separate-networks/application-proxy-iaas-network.png)
  
Esto puede convertirse en un problema a medida que muchas organizaciones usen varios proveedores de nube, ya que sus aplicaciones se encuentran en varios centros de datos. Con los grupos de conectores de proxy de aplicación de Azure AD, puede habilitar un servicio común para proteger el acceso a todas las aplicaciones sin necesidad de crear otra dependencia de la red corporativa:

![Varios proveedores de nube (IaaS de Azure AD)](./media/application-proxy-publish-apps-separate-networks/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Varios bosques: grupos de conectores diferentes para cada bosque

La mayoría de los clientes que han implementado el proxy de aplicación usan sus funcionalidades de inicio de sesión único (SSO) mediante la delegación restringida de kerberos (KCD). Para tal fin, las máquinas del conector deben estar unidas a un dominio que pueda delegar los usuarios a la aplicación. KCD es compatible con las funcionalidades de varios bosques. No obstante, en el caso de las empresas que tienen distintos entornos de varios bosques sin ninguna relación de confianza entre ellos, no se puede usar un solo conector para todos los bosques. 

Entonces, pueden implementarse conectores específicos por cada bosque y establecer que se atiendan a las aplicaciones que se publicaron para dar servicio solo a los usuarios de ese bosque concreto. Cada grupo de conectores representa un bosque diferente. Aunque el inquilino y la mayor parte de la experiencia quedará unificada en todos los bosques, pueden asignarse usuarios a sus aplicaciones de bosque mediante los grupos de Azure AD.
 
### <a name="disaster-recovery-sites"></a>Sitios de recuperación ante desastres

Hay dos enfoques diferentes que puede adoptar con un sitio de recuperación ante desastres, dependiendo de cómo estén implementados sus sitios:

* Si el sitio de recuperación ante desastres se basa en el modo activo-activo, que es exactamente igual que el sitio principal, con la misma red y configuración de AD, puede crear los conectores del sitio de recuperación ante desastres en el mismo grupo de conectores que el sitio principal. De esta forma, Azure AD podrá detectar automáticamente las conmutaciones por error.
* Si el sitio de recuperación ante desastres es independiente del principal, puede crear un grupo de conectores diferente en el sitio de recuperación ante desastres, y 1) tener más aplicaciones o 2) desviar manualmente la aplicación existente al grupo de conectores de recuperación ante desastres según sea necesario.
 
### <a name="serve-multiple-companies-from-a-single-tenant"></a>Servicio a varias empresas desde un solo inquilino

Hay muchas maneras de implementar un modelo en el que un solo proveedor de servicio implemente y mantenga servicios relacionados con Azure AD para varias empresas. Los grupos de conectores ayudan al administrador a separar los conectores y las aplicaciones en grupos distintos. Un método, que es adecuado para las pequeñas empresas, consiste en contar con un solo inquilino de Azure AD mientras que las diferentes empresas tienen sus propias redes y su propio nombre de dominio. También es válido para los escenarios de fusión y adquisición, donde una sola división de TI presta servicios a varias empresas por motivos normativos o empresariales. 

## <a name="sample-configurations"></a>Configuraciones de ejemplo

Algunos ejemplos que puede implementar incluyen los siguientes conectores.
 
### <a name="default-configuration--no-use-for-connector-groups"></a>Configuración predeterminada: ningún uso para los grupos de conectores

Si no usa grupos de conectores, la configuración tendría un aspecto similar al siguiente:

![Sin grupos de conectores de Azure AD](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-1.png)
 
Esta configuración es suficiente para pruebas e implementaciones de menor envergadura. También funcionará bien si su organización tiene una topología de red plana.
 
### <a name="default-configuration-and-an-isolated-network"></a>Configuración predeterminada y red aislada

Esta configuración es una evolución de la predeterminada, en la que hay una aplicación específica que se ejecuta en una red aislada, como la red virtual IaaS: 

![Sin grupos de conectores de Azure AD](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-2.png)
 
### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Configuración recomendada: varios grupos específicos y un grupo predeterminado para conectores inactivos

La configuración recomendada para organizaciones grandes y complejas consiste en que el grupo de conectores predeterminado actúe como un grupo que no atienda a ninguna aplicación y que se use para los conectores inactivos o recién instalados. Las aplicaciones se atienden con grupos de conectores personalizados. Así es como se genera toda la complejidad de los escenarios descritos anteriormente.

En el ejemplo siguiente, la empresa tiene dos centros de datos, A y B, con dos conectores que atienden a cada sitio. Cada uno de los sitios tienen distintas aplicaciones que se ejecutan en él. 

![Sin grupos de conectores de Azure AD](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-3.png)
 
## <a name="next-steps"></a>Pasos siguientes
* [Habilitación del proxy de la aplicación](active-directory-application-proxy-enable.md)
* [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
* [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)
* [Solucionar los problemas que tiene con el Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)


