---
title: "Reglas y puntos de conexión del servicio de Virtual Network para Azure SQL Database | Microsoft Docs"
description: "Marque una subred como punto de conexión del servicio de Virtual Network. A continuación, defina el punto de conexión como una regla de red virtual a la ACL de su instancia de Azure SQL Database. Su instancia de SQL Database aceptará la comunicación de todas las máquinas virtuales y otros nodos de la subred."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: VNet Service endpoints
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 01/31/2018
ms.author: genemi
ms.openlocfilehash: d4179c590ef418633158dd5a5dbadbc8c20bcde7
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-sql-database"></a>Reglas y puntos de conexión del servicio de Virtual Network para Azure SQL Database

Las *reglas de red virtual* son una característica de firewall que controla si el servidor de Azure SQL Database acepta las comunicaciones que se envían desde subredes específicas en redes virtuales. En este artículo se explica por qué la característica de la regla de red virtual a veces es la mejor opción para permitir la comunicación de forma segura con Azure SQL Database.

Para crear una regla de red virtual, primero debe existir un [punto de conexión de servicio de red virtual] [ vm-virtual-network-service-endpoints-overview-649d] para la regla a la que hacer referencia.


#### <a name="how-to-create-a-virtual-network-rule"></a>Creación de una regla de red virtual

Si solo crea una regla de red virtual, puede ir directamente a los pasos y a la explicación que hay [más adelante en este artículo](#anchor-how-to-by-using-firewall-portal-59j).






<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminología y descripción

**Red virtual:** puede tener redes virtuales asociadas a su suscripción de Azure.

**Subred:** una red virtual contiene **subredes**. Cualquier máquina virtual (VM) de Azure que tenga se asignará a las subredes. Una subred puede contener varias máquinas virtuales u otros nodos de proceso. Los nodos de proceso que se encuentran fuera de la red virtual no pueden tener acceso a su red virtual a menos que configure la seguridad para que permita el acceso.

**Punto de conexión del servicio de Virtual Network:** un [punto de conexión del servicio de Virtual Network][vm-virtual-network-service-endpoints-overview-649d] es una subred cuyos valores de propiedad incluyen uno o más nombres formales de tipo de servicio de Azure. En este artículo nos interesa el nombre de tipo de **Microsoft.Sql**, que hace referencia al servicio de Azure denominado SQL Database.

**Regla de red virtual:** una regla de red virtual para el servidor SQL Database es una subred que se muestra en la lista de control de acceso (ACL) de su servidor SQL Database. Para estar en la ACL de su SQL Database, la subred debe contener el nombre de tipo **Microsoft.Sql**.

Una regla de red virtual indica a su servidor SQL Database que acepte las comunicaciones procedentes de todos los nodos que están en la subred.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Ventajas de una regla de red virtual

Hasta que no tome medidas, las máquinas virtuales de sus subredes no pueden comunicarse con SQL Database. Una acción que permite establecer la comunicación es la creación de una regla de red virtual. El motivo de la elección del enfoque de la regla de red virtual requiere un análisis de comparación y contraste relacionado con las opciones de seguridad competentes que ofrece el firewall.

#### <a name="a-allow-access-to-azure-services"></a>A. Permitir el acceso a servicios de Azure

El panel de firewall tiene el botón **Activado/Desactivado** con la etiqueta **Permitir el acceso a servicios de Azure**. La configuración **Activado** permite las comunicaciones desde todas las direcciones IP de Azure y todas las subredes de Azure. Es posible que estas IP o subredes de Azure no le pertenezcan. Esta configuración **Activado** es probablemente más abierta de lo que le gustaría que fuera su instancia de SQL Database. La característica de la regla de red virtual ofrece un control mucho más granular.

#### <a name="b-ip-rules"></a>B. Reglas IP

El firewall de SQL Database le permite especificar intervalos de direcciones IP desde los que se aceptan las comunicaciones en SQL Database. Este enfoque es preciso para las direcciones IP estables que están fuera de la red privada de Azure. Sin embargo, muchos nodos de dentro de la red privada de Azure se configuran con direcciones IP *dinámicas*. Es posible que las direcciones IP dinámicas cambien, por ejemplo, cuando se reinicia la máquina virtual. Sería una locura especificar una dirección IP dinámica en una regla de firewall, en un entorno de producción.

Para recuperar la opción de IP, puede obtener una dirección IP *estática* para la máquina virtual. Para obtener detalles, consulte [Configuración de direcciones IP privadas para una máquina virtual mediante Azure Portal][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Sin embargo, el enfoque de IP estática puede resultar difícil de administrar, y es costoso si se realiza a escala. Las reglas de red virtual son más sencillas de establecer y administrar.

#### <a name="c-cannot-yet-have-sql-database-on-a-subnet"></a>C. Aún no se puede disponer de SQL Database en una subred

Si su servidor de Azure SQL Database fuera un nodo de una subred de la red virtual, todos los nodos de la red virtual podrían comunicarse con su instancia de SQL Database. En este caso, las máquinas virtuales podrían comunicarse con SQL Database sin necesitar ninguna regla IP ni regla de red virtual.

No obstante, a partir de septiembre de 2017, el servicio de Azure SQL Database aún no se encuentra entre los servicios que se pueden asignar a una subred.






<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Detalles sobre las reglas de red virtual

En esta sección se describen varios detalles acerca de las reglas de red virtual.

#### <a name="only-one-geographic-region"></a>Solo una región geográfica

Cada punto de conexión del servicio de Virtual Network se aplica solo a una región de Azure. El punto de conexión no permite que otras regiones acepten la comunicación de la subred.

Cualquier regla de red virtual se limita a la región a la que se aplica su punto de conexión subyacente.

#### <a name="server-level-not-database-level"></a>Nivel de servidor y no de base de datos

Cada regla de red virtual se aplica a su servidor completo de Azure SQL Database y no solo a una base de datos determinada del servidor. En otras palabras, la regla de red virtual se aplica en el nivel de servidor y no en el nivel de base de datos.

- En cambio, se pueden aplicar reglas IP en cualquier nivel.

#### <a name="security-administration-roles"></a>Roles de administración de la seguridad

Existe una separación de los roles de seguridad en la administración de puntos de conexión del servicio de Virtual Network. Se requiere una acción de cada uno de los roles siguientes:

- **Administrador de red:**&nbsp; activar el punto de conexión.
- **Administrador de base de datos:**&nbsp; actualizar la lista de control de acceso (ACL) que se va a agregar a la subred proporcionada en el servidor SQL Database .

*Alternativa de RBAC:*

Las funciones de administrador de red y de base de datos tienen más capacidades de las que se necesitan para administrar las reglas de red virtual. Solo se necesita un subconjunto de sus capacidades.

Si quiere, puede optar por la opción de usar el [control de acceso basado en rol (RBAC)] [ rbac-what-is-813s] en Azure para crear un rol personalizado único que tenga solo el subconjunto necesario de capacidades. Se podría usar el rol personalizado en lugar del administrador de red o el administrador de la base de datos. El área expuesta de la exposición de seguridad es inferior si agrega un usuario a un rol personalizado, en lugar de agregar el usuario a los otros dos roles de administrador principales.

> [!NOTE]
> En algunos casos, Azure SQL Database y la subred de red virtual se encuentran en distintas suscripciones. En estos casos debe garantizar las siguientes configuraciones:
> - Ambas suscripciones deben estar en el mismo inquilino de Azure Active Directory.
> - El usuario tiene los permisos necesarios para iniciar operaciones como habilitar los puntos de conexión de servicio y agregar una subred de red virtual al servidor especificado.

## <a name="limitations"></a>Limitaciones

Para Azure SQL Database, la característica de las reglas de red virtual tiene las siguientes limitaciones:

- En la actualidad, una instancia de Azure Web App en una subred que tenga **puntos de conexión de servicio** activados aún no funciona según lo previsto. Estamos trabajando para habilitar esta funcionalidad.
    - Hasta que esta característica esté implementada totalmente, se recomienda que pase su instancia de Web App a una subred diferente que no tenga puntos de conexión de servicio activados para SQL.

- En el firewall de su instancia de SQL Database, cada regla de red virtual hace referencia a una subred. Todas estas subredes a las que se hace referencia deben estar hospedadas en la misma región geográfica que hospeda la base de datos SQL.

- Cada servidor de Azure SQL Database puede tener hasta 128 entradas de ACL para cualquier red virtual proporcionada.

- Las reglas de red virtual solo se aplican a las redes virtuales de Azure Resource Manager, y no a las redes del [modelo de implementación clásico][arm-deployment-model-568f].

- La activación de puntos de conexión de servicio de red virtual en Azure SQL Database también habilita los puntos de conexión para los servicios MySQL y PostGres de Azure. Sin embargo, con los puntos de conexión activados, se producirá un error al intentar conectarse desde los puntos de conexión con las instancias de MySQL o Postgres.
    - El motivo subyacente es que MySQL y PostGres no admiten ACLing actualmente.

- En el firewall, los intervalos de direcciones IP se aplican a los siguientes elementos de red, pero no las reglas de red virtual:
    - [Red privada virtual (VPN) de sitio a sitio (S2S)][vpn-gateway-indexmd-608y]
    - Entorno local a través de [ExpressRoute][expressroute-indexmd-744v]

#### <a name="expressroute"></a>ExpressRoute

Si la red está conectada a la red de Azure mediante el uso de [ExpressRoute][expressroute-indexmd-744v], cada circuito está configurado con dos direcciones IP públicas en Microsoft Edge. Las dos direcciones IP se utilizan para conectarse a servicios Microsoft, como a Azure Storage, mediante el uso del emparejamiento público de Azure.

Para permitir la comunicación desde el circuito a Azure SQL Database, debe crear reglas de red IP para direcciones IP públicas de los circuitos. Para encontrar las direcciones IP públicas del circuito de ExpressRoute, abra un vale de soporte con ExpressRoute mediante Azure Portal.


<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-all-azure-services"></a>Impacto de deshabilitar "Permitir todos los servicios de Azure"

Muchos usuarios desean deshabilitar la opción **Permitir todos los servicios de Azure** de sus servidores de Azure SQL y reemplazarla por una regla de firewall de red virtual.
Sin embargo, esto afecta a las siguientes características de Azure SQL Database:

#### <a name="import-export-service"></a>Import Export Service
Azure SQLDB Import Export Service se ejecuta en máquinas virtuales de Azure. Estas máquinas virtuales no están en la red virtual y, por tanto, obtienen una dirección IP de Azure al conectarse a la base de datos. Si deshabilita **Permitir todos los servicios de Azure** estas máquinas virtuales no podrán acceder a las bases de datos.
Puede solucionar el problema. Ejecute la importación o exportación de BACPAC directamente en el código mediante la API de DACFx. Asegúrese de que la API esté implementada en una máquina virtual que se encuentre en la subred de la red virtual para la cual ha establecido la regla de firewall.

#### <a name="sql-database-query-editor"></a>Editor de consultas de SQL Database
El editor de consultas de Azure SQL Database se implementa en máquinas virtuales de Azure. Estas máquinas virtuales no están en la red virtual. Por tanto, las máquinas virtuales obtienen una dirección IP de Azure cuando se conectan a la base de datos. Si deshabilita **Permitir todos los servicios de Azure** estas máquinas virtuales no podrán acceder a las bases de datos.

#### <a name="table-auditing"></a>Auditoría de tablas
En la actualidad hay dos maneras de habilitar la auditoría en SQL Database. La auditoría de tablas produce un error después de haber habilitado los puntos de conexión de servicio en el servidor de Azure SQL Server. La solución en este caso consiste en cambiar a la auditoría de blobs.


## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Efectos del uso de puntos de conexión de servicio de la red virtual con Azure Storage

Azure Storage ha implementado la misma característica que le permite limitar la conectividad con su cuenta de Storage.
Si decide usar esta característica con una cuenta de Storage que se está usando como un servidor de Azure SQL Server puede que se produzcan errores. A continuación, aparece una lista y una explicación de las características de Azure SQLDB que se ven afectadas por esto.

#### <a name="azure-sqldw-polybase"></a>Azure SQLDW PolyBase
PolyBase normalmente se usa para cargar datos en Azure SQLDW desde cuentas de Storage. Si la cuenta de Storage desde la que está cargando los datos limita el acceso a solo un conjunto de subredes de red virtual, se interrumpirá la conectividad de PolyBase a la cuenta.

#### <a name="azure-sqldb-blob-auditing"></a>Auditoría de blobs de Azure SQLDB
La auditoría de blobs inserta los registros de auditoría en su propia cuenta de almacenamiento. Si esta cuenta de almacenamiento usa la característica VENT de puntos de conexión de servicio, la conectividad entre Azure SQLDB y la cuenta de almacenamiento se interrumpirá.


## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Agregar una regla de firewall de VNET al servidor sin tener que activar los puntos de conexión de servicio

Mucho antes de mejorar esta característica, era necesario activar los puntos de conexión de servicio de VNET para poder implementar una regla dinámica de VNET en el firewall. Los punto de conexión relacionaban una subred de VNET determinada con una base de datos de Azure SQL Database. Pero a partir de enero de 2018, puede evitar este requisito si establece la marca **IgnoreMissingServiceEndpoint**.

Si solo establece una regla de firewall, no tendrá el servidor protegido. Por lo tanto, también debe activar los puntos de conexión de servicio de VNET para que la seguridad surta efecto. Al activar los puntos de conexión de servicio, la subred de VNET experimenta cierto tiempo de inactividad hasta que estos puntos se activan totalmente. Esto sucede especialmente en redes virtuales de gran tamaño. Puede usar la marca **IgnoreMissingServiceEndpoint** para reducir o eliminar el tiempo de inactividad durante la activación.

Puede establecer la marca **IgnoreMissingServiceEndpoint** mediante PowerShell. Para obtener más detalles, consulte [PowerShell to create a Virtual Network service endpoint and rule for Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d] (PowerShell para crear una regla y un punto de conexión del servicio de Virtual Network para Azure SQL Database).


## <a name="errors-40914-and-40615"></a>Errores 40914 y 40615

El error de conexión 40914 se relaciona con *reglas de red virtual*, tal y como se especifica en el panel Firewall de Azure Portal. El error 40615 es similar, excepto que se relaciona con *reglas de direcciones IP* del Firewall.

#### <a name="error-40914"></a>Error 40914

*Texto del mensaje:* No se puede abrir el servidor "*[nombre de servidor]*" solicitado por el inicio de sesión. Un cliente no puede acceder al servidor.

*Descripción del error:* el cliente está en una subred que tiene puntos de conexión de servidor de red virtual. Sin embargo, el servidor de Azure SQL Database no tiene ninguna regla de red virtual que conceda a la subred el derecho para comunicarse con la base de datos SQL.

*resolución de errores:* en el panel Firewall de Azure Portal, use el control de reglas de red virtual para [agregar una regla de red virtual](#anchor-how-to-by-using-firewall-portal-59j) para la subred.

#### <a name="error-40615"></a>Error 40615

*Texto del mensaje:* No se puede abrir el servidor "{0}" solicitado por el inicio de sesión. No está permitido que el cliente con la dirección IP '{1}' acceda al servidor.

*Descripción del error:* el cliente intenta conectarse desde una dirección IP que no tiene autorización para conectarse al servidor de Azure SQL Database. El firewall de servidor no tiene ninguna regla de dirección IP que permita que un cliente se comunique con la dirección IP dada a la instancia de SQL Database.

*Resolución de errores:* escriba la dirección IP del cliente como una regla de IP. Para ello, use el panel Firewall de Azure Portal.


Se puede encontrar información de una lista de varios mensajes de error de SQL Database [aquí][sql-database-develop-error-messages-419g].





<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Se puede crear una regla de red virtual en el portal

En esta sección se muestra cómo puede usar [Azure Portal][http-azure-portal-link-ref-477t] para crear una *regla de red virtual* en su instancia de Azure SQL Database. La regla indica a su instancia de SQL Database que acepte la comunicación procedente de una subred concreta que se ha etiquetado como *punto de conexión del servicio de Virtual Network*.

#### <a name="powershell-alternative"></a>Alternativa de PowerShell

Un script de PowerShell también puede crear reglas de red virtual. El cmdlet fundamental es **New-AzureRmSqlServerVirtualNetworkRule**. Si le interesa, consulte [PowerShell to create a Virtual Network service endpoint and rule for Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d] (PowerShell para crear una regla y un punto de conexión del servicio de Virtual Network para Azure SQL Database).

#### <a name="prerequisites"></a>requisitos previos

Ya debe tener una subred que esté etiquetada con el punto de conexión del servicio de Virtual Network *nombre de tipo* correspondiente a su instancia de Azure SQL Database.

- El nombre de tipo de punto de conexión pertinente es **Microsoft.Sql**.
- Si es posible que su subred no se pueda etiquetar con el nombre de tipo, consulte [Comprobar que su subred es un punto de conexión][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

### <a name="azure-portal-steps"></a>Pasos de Azure Portal

1. Inicie sesión en [Azure Portal][http-azure-portal-link-ref-477t].

2. A continuación, vaya al portal **Servidores SQL Server** &gt; **Firewall / Redes virtuales**.

3. Establezca el control **Permitir el acceso a los servicios de Azure** en Desactivado.

    > [!IMPORTANT]
    > Si deja el control establecido en Activado, el servidor de Azure SQL Database aceptará la comunicación desde cualquier subred. Si deja el control establecido en Activado, el número de accesos podría ser excesivo desde un punto de vista de seguridad. La característica de punto de conexión del servicio de Virtual Network de Microsoft Azure, junto con la característica de regla de red virtual de SQL Database, pueden reducir el área expuesta de seguridad.

4. Haga clic en el control **+ Agregar existente**, en la sección **Redes virtuales**.

    ![Haga clic en Agregar existente (punto de conexión de subred, como una regla SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. En el nuevo panel **Crear/Actualizar**, rellene los controles con los nombres de los recursos de Azure.

    > [!TIP]
    > Debe incluir el **prefijo de dirección** correcto de la subred. Puede encontrar el valor en el portal.
    > Vaya a **Todos los recursos**&gt;**Todos los tipos**&gt;**Redes virtuales**. El filtro muestra sus redes virtuales. Haga clic en su red virtual y, a continuación, haga clic en **Subredes**. La columna **INTERVALO DE DIRECCIONES** tiene el prefijo de dirección que necesita.

    ![Rellene los campos de la nueva regla.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Haga clic en el botón **Aceptar** situado cerca de la parte inferior del panel.

7.  Vea la regla de red virtual resultante en el panel de firewall.

    ![Vea la nueva regla en el panel de firewall.][image-portal-firewall-vnet-result-rule-30-png]


> [!NOTE]
> Se aplican los siguientes estados a las reglas:
> - **Listo:** indica que se ha realizado correctamente la operación que ha iniciado.
> - **Erróneo:** indica que se han producido errores en la operación que ha iniciado.
> - **Eliminado:** solo se aplica a la operación de eliminación e indica que se ha eliminado la regla y que ya no es aplicable.
> - **En curso:** indica que la operación está en curso. Se aplica la regla anterior mientras la operación está en este estado.


<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Artículos relacionados

- [Puntos de conexión del servicio Azure Virtual Network][vm-virtual-network-service-endpoints-overview-649d]
- [Reglas de firewall de nivel de servidor y de nivel de base de datos de Azure SQL Database][sql-db-firewall-rules-config-715d]

La característica de la regla de red virtual de Azure SQL Database empezó a estar disponible a finales de septiembre de 2017.

## <a name="next-steps"></a>pasos siguientes

- [Use PowerShell para crear un punto de conexión del servicio de Virtual Network y, a continuación, una regla de red virtual para Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]


<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png



<!-- Link references, to text, Within this same Github repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.md



<!-- Link references, to text, Outside this Github repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/




<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
