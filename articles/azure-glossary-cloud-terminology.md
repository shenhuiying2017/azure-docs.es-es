<properties
    pageTitle="Glosario de Azure - Diccionario de Azure | Microsoft Azure"
    description="Utilice el glosario de Azure para comprender la terminología de la nube sobre la plataforma Azure. Este breve diccionario de Azure define algunos términos comunes de la nube para Azure."
    keywords="Diccionario de Azure, terminología de la nube, glosario de Azure, definiciones de terminología, términos de la nube"
    services="na"
    documentationCenter="na"
    authors="MonicaRush"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/04/2016"
    ms.author="monicar"/>


# Glosario de Microsoft Azure: un diccionario de terminología de la nube sobre la plataforma Azure

El glosario de Microsoft Azure es un breve diccionario de terminología de la nube para la plataforma Azure.

## Búsqueda de definiciones de servicios y otros términos sobre la nube

* Para obtener definiciones de los servicios de Azure y sus equivalentes de AWS, consulte [Microsoft Azure y Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).

* Para consultar términos generales del sector, visite [Términos de informática en la nube](https://azure.microsoft.com/overview/cloud-computing-dictionary/).

El glosario de Azure con las dos referencias anteriores proporciona una taxonomía integral de Azure y la industria de la nube.

## Lista del glosario de Azure


### <a name="account"></a>cuenta  
Una cuenta Microsoft profesional o educativa, o personal, que se utiliza para tener acceso a una suscripción de Azure y administrarla. Consulte también [Asociación de las suscripciones de Azure con Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).


### <a name="availabilityset"></a>conjunto de disponibilidad  
Una colección de máquinas virtuales que se administran de forma conjunta para proporcionar confiabilidad y redundancia a las aplicaciones. El uso de un conjunto de disponibilidad garantiza que durante un evento de mantenimiento planeado o no planeado, al menos una máquina virtual estará disponible. Consulte también [Administración de la disponibilidad de las máquinas virtuales de Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) o [Administración de la disponibilidad de las máquinas virtuales con Linux](./virtual-machines/virtual-machines-linux-manage-availability.md).


### <a name="classicmodel"></a>modelo de implementación clásica de Azure  
Uno de los dos [modelos de implementación](resource-manager-deployment-model.md) utilizados para implementar recursos en Azure (el nuevo modelo es Azure Resource Manager). Algunos recursos de Azure pueden implementarse solo en uno de los dos modelos, mientras que otros pueden hacerlo en cualquiera de ellos. Orientación sobre cada uno de los modelos con los que se pueden implementar los recursos de Azure.


### <a name="cli"></a>Interfaz de la línea de comandos (CLI) de Azure  
[Interfaz de la línea de comandos](xplat-cli-install.md) que se puede utilizar para administrar los servicios de Azure desde Windows, OSX y equipos con Linux.


### <a name="powershell"></a>Azure PowerShell.  
[Interfaz de la línea de comandos](powershell-install-configure.md) para administrar los servicios de Azure a través de una línea de comandos desde equipos con Windows. Algunos servicios o características de un servicio pueden administrarse solo a través de PowerShell o la CLI. En las guías de los recursos de Azure se detalla con qué modelo o modelos se puede implementar cada recurso. Consulte también [Instalación y configuración de Azure PowerShell](powershell-install-configure.md).


### <a name="armmodel"></a>Modelo de implementación de Azure Resource Manager  
Uno de los dos [modelos de implementación](resource-manager-deployment-model.md) utilizados para implementar recursos en Microsoft Azure (el otro es el modelo de implementación clásica). Algunos recursos de Azure pueden implementarse solo en uno de los dos modelos, mientras que otros pueden hacerlo en cualquiera de ellos. Orientación sobre cada uno de los modelos con los que se pueden implementar los recursos de Azure.


### <a name="fault-domain"></a>dominio de error  
La colección de máquinas virtuales de un conjunto de disponibilidad que posiblemente den error al mismo tiempo. Un ejemplo es un grupo de máquinas en bastidor que comparten una fuente de alimentación y un conmutador de red. En Azure, las máquinas virtuales de un conjunto de disponibilidad se separan automáticamente en varios dominios de error. Consulte también [Administración de la disponibilidad de las máquinas virtuales de Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) o [Administración de la disponibilidad de las máquinas virtuales con Linux](./virtual-machines/virtual-machines-linux-manage-availability.md).


### <a name="geo"></a>geoárea  
Un límite definido para la residencia de datos que normalmente contiene dos o más regiones. Los límites pueden situarse dentro o fuera de las fronteras nacionales y se ven afectados por la normativa fiscal. Cada geoárea tiene al menos una región. Ejemplos de geoáreas son Asia Pacífico y Japón. Este concepto está relacionado con la *geografía*. Consulte también [Regiones de Azure](best-practices-availability-paired-regions.md).


### <a name="georeplication"></a>replicación geográfica  
El proceso de replicación automática de contenido como blobs, tablas y colas entre zonas regionales emparejadas. Consulte también [Replicación geográfica activa para Base de datos SQL de Azure](./sql-database/sql-database-geo-replication-overview.md).


### <a name="image"></a>imagen  
Un archivo que contiene la configuración del sistema operativo y la aplicación que puede utilizarse para crear cualquier cantidad de máquinas virtuales. En Azure existen dos tipos de imágenes: imagen de máquina virtual e imagen de sistema operativo. Una imagen de máquina virtual incluye un sistema operativo y todos los discos conectados a una máquina virtual cuando se crea la imagen. Una imagen de sistema operativo contiene solo un sistema operativo generalizado sin configuraciones de disco de datos. Consulte también [Navegación y selección de las imágenes de máquina virtual Windows en Azure con PowerShell o CLI](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md).


### <a name="limits"></a>límites  
El número de recursos que se pueden crear o las pruebas comparativas de rendimiento que se pueden lograr. Los límites se suelen asociar a las suscripciones, los servicios y las ofertas. Consulte también [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](azure-subscription-service-limits.md).


### <a name="loadbalancer"></a>equilibrador de carga  
Un recurso que distribuye el tráfico entrante entre equipos en una red. En Azure, un equilibrador de carga distribuye el tráfico a las máquinas virtuales definidas en un conjunto de equilibrador de carga. Un [equilibrador de carga](./load-balancer/load-balancer-overview.md) puede ser accesible desde Internet o de uso interno.


### <a name="offer"></a>oferta  
Los precios, créditos y términos relacionados aplicables a una suscripción de Azure. Consulte la página [Detalles de las ofertas de Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/).


### <a name="portal"></a>portal  
El portal web seguro que se usa para implementar y administrar servicios de Azure. Hay dos portales: el [Portal de Azure](http://portal.azure.com/) y el [portal clásico](http://manage.windowsazure.com/). Algunos servicios están disponibles en los dos portales, mientras que otros solo están disponibles en uno u otro. La [Tabla de disponibilidad de los portales de Azure](https://azure.microsoft.com/features/azure-portal/availability/) enumera los servicios que están disponibles en cada uno de los portales.


### <a name="region"></a>región  
Un área dentro de una geoárea que no traspasa las fronteras nacionales y contiene uno o varios centros de datos. Los precios, los servicios regionales y los tipos de ofertas se exponen a nivel de región. Una región se empareja normalmente con otra, que puede estar a cientos de kilómetros de distancia, para formar una pareja regional. Las parejas regionales pueden utilizarse como un mecanismo para escenarios de alta disponibilidad y recuperación ante desastres. Además, este concepto está relacionado con la *ubicación*. Consulte también [Regiones de Azure](best-practices-availability-paired-regions.md).


### <a name="resource"></a>recurso  
Un elemento que forma parte de la solución de Azure. Cada servicio de Azure permite implementar diferentes tipos de recursos, como bases de datos o máquinas virtuales. Consulte también [Información general de Azure Resource Manager](resource-group-overview.md).


### <a name="resourcegroup"></a>grupo de recursos  
Un contenedor en Resource Manager que incluye los recursos relacionados de una aplicación. El grupo de recursos puede incluir todos los recursos de una aplicación o solo aquellos que se agrupan juntos lógicamente. Puede decidir cómo desea asignar los recursos a los grupos de recursos en función de lo que más convenga a su organización. Consulte también [Información general de Azure Resource Manager](resource-group-overview.md).


### <a name="rmtemplate"></a>plantilla de Resource Manager  
Un archivo JSON que define de forma declarativa uno o varios recursos de Azure y que define las dependencias entre los recursos implementados. La plantilla se puede usar para implementar los recursos de manera repetida y uniforme. Consulte también [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md).


### <a name="resourceprovider"></a>proveedor de recursos  
Un servicio que proporciona los recursos que puede implementar y administrar mediante Resource Manager. Cada proveedor de recursos ofrece operaciones para trabajar con los recursos que se implementan. Es posible acceder a los proveedores de recursos mediante el Portal de Azure, Azure PowerShell y varios SDK de programación. Consulte también [Información general de Azure Resource Manager](resource-group-overview.md).


### <a name="role"></a>rol  
Un medio para controlar el acceso que se puede asignar a usuarios, grupos y servicios. Los roles pueden realizar acciones como crear, administrar y leer en recursos de Azure. Consulte también [RBAC: Roles integrados](./active-directory/role-based-access-built-in-roles.md).


### <a name="sla"></a>contrato de nivel de servicio (SLA)  
El contrato que describe los compromisos de Microsoft en cuanto a tiempo de actividad y conectividad. Cada servicio de Azure tiene un acuerdo de nivel de servicio específico. Vea también [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).


### <a name="storageaccount"></a>cuenta de almacenamiento  
Una cuenta de almacenamiento que proporciona acceso a los servicios de Azure de Blob, Cola, Tabla y Archivo del Almacenamiento de Azure. La cuenta de almacenamiento ofrece el espacio de nombres exclusivo para los objetos de datos de Almacenamiento de Azure. Consulte también [Acerca de las cuentas de almacenamiento de Azure](./storage/storage-create-storage-account.md).


### <a name="subscription"></a>suscripción  
Contrato de un cliente con Microsoft que le permite obtener servicios de Azure. Los precios de la suscripción y los términos relacionados se rigen por la oferta elegida para la suscripción. Consulte [Contrato Microsoft Online Subscription](https://azure.microsoft.com/support/legal/subscription-agreement/). Consulte también [Asociación de las suscripciones de Azure con Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).


### <a name="tag"></a>etiqueta  
Un término de indexación que permite clasificar los recursos según sus requisitos de administración o facturación. Es recomendable usar etiquetas cuando se tiene un conjunto complejo de grupos de recursos y recursos y se necesita visualizar estos activos de la manera más conveniente. Por ejemplo, puede etiquetar recursos que cumplen una función similar en la organización o que pertenecen al mismo departamento. Consulte también [Uso de etiquetas para organizar los recursos de Azure](resource-group-using-tags.md).


### <a name="updatedomain"></a>dominio de actualización  
La colección de máquinas virtuales en un conjunto de disponibilidad que se actualizan al mismo tiempo. Las máquinas virtuales que se encuentran en el mismo dominio de actualización se reinician en conjunto durante el mantenimiento planeado. Azure no reinicia nunca más de un dominio de actualización a la vez. Así es como funcionan los dominios de actualización. Consulte también [Administración de la disponibilidad de las máquinas virtuales de Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) o [Administración de la disponibilidad de las máquinas virtuales con Linux](./virtual-machines/virtual-machines-linux-manage-availability.md).


### <a name="vm"></a>máquina virtual  
La implementación de software de un equipo físico que ejecuta un sistema operativo. Se pueden ejecutar varias máquinas virtuales a la vez en un mismo hardware. En Azure, hay máquinas virtuales disponibles en diferentes tamaños. Consulte también [Documentación sobre máquinas virtuales](https://azure.microsoft.com/documentation/services/virtual-machines/).


### <a name="vmextension"></a>extensión de máquina virtual  
Un recurso que implementa comportamientos o características que cooperan en el funcionamiento de otros programas o le permiten interactuar con un equipo en ejecución. Por ejemplo, podría utilizar la extensión de acceso a máquinas virtuales para restablecer o modificar los valores de acceso remoto en una máquina virtual de Azure. Consulte también [Acerca de las características y extensiones de las máquinas virtuales (Windows)](./virtual-machines/virtual-machines-windows-extensions-features.md) o [Acerca de las características y extensiones de las máquinas virtuales (Linux)](./virtual-machines/virtual-machines-linux-extensions-features.md).


### <a name="vnet"></a>red virtual  
Una red que proporciona conectividad entre los recursos de Azure que se encuentra aislada del resto de inquilinos de Azure. Se puede conectar a otras redes virtuales de Azure a través de una [Puerta de enlace de VPN de Azure](./vpn-gateway/vpn-gateway-about-vpngateways.md) y a la red local mediante [varias opciones](./vpn-gateway/vpn-gateway-cross-premises-options.md). Puede controlar por completo los bloques de direcciones IP, la configuración DNS, las directivas de seguridad y las tablas de rutas dentro de esta red. Consulte también [Información general sobre redes virtuales](./virtual-network/virtual-networks-overview.md).

###**Consulte también**  
- [Comience a usar Azure](https://azure.microsoft.com/get-started/)
- [Centro de recursos en la nube](https://azure.microsoft.com/resources/)  
- [Azure para aplicaciones empresariales](https://azure.microsoft.com/overview/business-apps-on-azure/)
- [Azure en su centro de datos](https://azure.microsoft.com/overview/business-apps-on-azure/) 

<!---HONumber=AcomDC_0601_2016-->