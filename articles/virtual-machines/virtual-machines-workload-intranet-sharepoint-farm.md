<properties
	pageTitle="Granja de SharePoint Server 2013 en Azure | Microsoft Azure"
	description="Obtenga información sobre el valor de una granja de SharePoint Server 2013 en Azure, configure un entorno de prueba e implemente una configuración de alta disponibilidad."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="josephd"/>

# Carga de trabajo de servicios de infraestructura de Azure: granja de SharePoint de intranet

[AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)]Modelo de implementación del Administrador de recursos.

Configure la primera o siguiente granja de SharePoint en Microsoft Azure y aprovéchese de la facilidad de configuración y de la capacidad ampliar rápidamente la granja de servidores para incluir la nueva capacidad o la optimización de funcionalidad clave. Muchas de las granjas de SharePoint aumentan a partir de una configuración estándar, de alta disponibilidad y de tres niveles para una granja con posiblemente una docena o más servidores optimizados para rendimiento o roles independientes, como almacenamiento en caché distribuido o búsqueda.

Con las máquinas virtuales y las características de la Red virtual de los Servicios de infraestructura de Azure, podrá implementar y ejecutar rápidamente una granja de SharePoint que se conecte de manera transparente a la red local. Por ejemplo, puede configurar la red siguiente.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

Dado que Red Virtual de Azure es una extensión de la red local con toda la nomenclatura correcta y el enrutamiento del tráfico en su lugar, los usuarios tendrán acceso a ella de la misma manera que si se encontrara en un centro de datos local.

Esta configuración permite ampliar fácilmente la granja de SharePoint mediante la adición de nuevas máquinas virtuales de Azure en las que los costos en curso tanto de mantenimiento como de hardware son menores que el ejecutar un conjunto equivalente del centro de datos.

El hospedaje de una granja de SharePoint de intranet en Servicios de infraestructura de Azure es un ejemplo de una aplicación de línea de negocio. Para obtener información general, consulte [Plano de arquitectura de aplicaciones de línea de negocio](http://msdn.microsoft.com/dn630664).

El siguiente paso es configurar una granja de SharePoint de intranet de desarrollo y pruebas hospedada en Azure.

> [AZURE.NOTE]Microsoft ha publicado la versión de vista previa de TI de SharePoint Server 2016. Para facilitar la instalación y prueba de esta vista previa, puede utilizar una imagen de la galería de máquinas virtuales de Azure con la vista previa de TI de SharePoint Server 2016 y sus requisitos previos preinstalados. Para obtener más información, consulte [Prueba de la vista previa de TI de SharePoint 2016 en Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Creación de una granja de SharePoint de intranet de desarrollo y pruebas hospedada en Azure

Tiene dos opciones para crear un entorno de desarrollo y pruebas para una granja de SharePoint hospedada en Azure:

- Red virtual solo en la nube
- Red virtual entre locales

Puede crear estos entornos de desarrollo y pruebas de forma gratuita con su [suscripción a MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/) o una [suscripción de evaluación de Azure](http://azure.microsoft.com/pricing/free-trial/).

### Red virtual solo en la nube

La red virtual solo en la nube no está conectada a una red local. Si desea crear rápidamente una granja de SharePoint básica o de alta disponibilidad, consulte [Creación de granjas de servidores de SharePoint](virtual-machines-sharepoint-farm-azure-preview.md). En el ejemplo siguiente se muestra la configuración básica de la granja de servidores de SharePoint.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/Non-HAFarm.png)

### Red virtual entre locales

Una red virtual entre locales está conectada a una red local con una conexión de ExpressRoute o VPN de sitio a sitio. Si desea crear un entorno de desarrollo y pruebas que imita la configuración final y experimentar con el acceso al servidor de SharePoint y realizar la administración remota a través de una conexión VPN, consulte [Configurar una granja de servidores de intranet de SharePoint en una nube híbrida para pruebas](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

![](./media/virtual-machines-workload-intranet-sharepoint-farm/CreateSPFarmHybridCloud.png)

El siguiente paso es crear una granja de SharePoint de intranet de alta disponibilidad en Azure.

## Implementación de una granja de servidores de SharePoint de intranet hospedada en Azure

La configuración representativa y básica de una granja de SharePoint de intranet, funcional y de alta disponibilidad, aparece en el ejemplo siguiente.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

Consta de:

- Una granja de SharePoint de intranet con dos servidores en los niveles web, de aplicación y de base de datos.
- Configuración de grupos de disponibilidad AlwaysOn de SQL Server con dos servidores SQL Server y un equipo de nodos de mayoría en un clúster.
- Azure Active Directory en la red virtual con dos controladores de dominio de réplica.

Para ver esta configuración como infografía, consulte [SharePoint con SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788).

### Lista de materiales

Esta configuración básica requiere el conjunto de componentes y servicios siguiente de Azure:

- Nueve máquinas virtuales.
- Cuatro discos de datos adicionales para los controladores de dominio y servidores SQL Server.
- Tres servicios en la nube.
- Cuatro conjuntos de disponibilidad.
- Una red virtual entre locales.
- Una cuenta de almacenamiento.
- Una suscripción de Azure.

Estas son las máquinas virtuales y sus tamaños predeterminados para esta configuración.

Elemento | Descripción de la máquina virtual | Imagen de la Galería | Tamaño predeterminado
--- | --- | --- | ---
1\. | Primer controlador de dominio | Windows Server 2012 R2 Datacenter | A2 (mediano)
2\. | Segundo controlador de dominio | Windows Server 2012 R2 Datacenter | A2 (mediano)
3\. | Primer servidor de base de datos | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
4\. | Segundo servidor de base de datos | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
5\. | Nodo de mayoría para el clúster | Windows Server 2012 R2 Datacenter | A1 (pequeño)
6\. | Primer servidor de aplicaciones de SharePoint | Versión de evaluación de Microsoft SharePoint Server 2013: Windows Server 2012 R2 | A4 (extragrande)
7\. | Segundo servidor de aplicaciones de SharePoint | Versión de evaluación de Microsoft SharePoint Server 2013: Windows Server 2012 R2 | A4 (extragrande)
8\. | Primer servidor web de SharePoint | Versión de evaluación de Microsoft SharePoint Server 2013: Windows Server 2012 R2 | A4 (extragrande)
9\. | Segundo servidor web de SharePoint | Versión de evaluación de Microsoft SharePoint Server 2013: Windows Server 2012 R2 | A4 (extragrande)

Para calcular los costos estimados para esta configuración, consulte [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/).

1. En **Módulos**, haga clic en **Calcular**, y, a continuación, haga clic en **Máquinas virtuales** las veces suficientes como para crear una lista de nueve máquinas virtuales.
2. Para cada máquina virtual, seleccione:
	- La región deseada
	- **Windows** para el tipo
	- **Estándar** para el plan de tarifa
	- El tamaño predeterminado de la tabla anterior o el tamaño deseado para **Tamaño de instancia**

> [AZURE.NOTE]La calculadora de precios de Azure no incluye los costos adicionales de la licencia de SQL Server para las dos máquinas virtuales que ejecutan SQL Server 2014 Enterprise. Para obtener más información, consulte [Precios de máquinas virtuales: SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

### Fases de implementación

Para implementar esta configuración, siga este proceso :

- Fase 1: Configuración de Azure.

	Use el Portal de Azure clásico y Azure PowerShell para crear una cuenta de almacenamiento, servicios en la nube y una red virtual entre locales. Para los pasos de configuración detallados, consulte [Fase 1](virtual-machines-workload-intranet-sharepoint-phase1.md).

- Fase 2: Configuración de controladores de dominio.

	Configure dos controladores de dominio de réplica de Azure Active Directory y los valores de DNS para la red virtual. Para los pasos de configuración detallados, consulte [Fase 2](virtual-machines-workload-intranet-sharepoint-phase2.md).

- Fase 3: Configuración de la infraestructura de SQL Server.

	Prepare las máquinas virtuales de SQL Server para su uso con SharePoint y cree el clúster de SQL Server. Para los pasos de configuración detallados, consulte [Fase 3](virtual-machines-workload-intranet-sharepoint-phase3.md).

- Fase 4: Configuración de los servidores de SharePoint.

	Configure las cuatro máquinas virtuales de SharePoint para una nueva granja de Sharepoint. Para la información detallada, consulte [Fase 4](virtual-machines-workload-intranet-sharepoint-phase4.md).

- Fase 5: Creación de un grupo de disponibilidad AlwaysOn.

	Prepare las bases de datos de SharePoint, cree un grupo de disponibilidad AlwaysOn y, a continuación, agregue las bases de datos de SharePoint a las mismas. Para los pasos de configuración detallados, consulte [Fase 5](virtual-machines-workload-intranet-sharepoint-phase5.md).

Una vez configurada, puede expandir esta granja de SharePoint con ayuda de [Arquitecturas de Microsoft Azure para SharePoint 2013](http://technet.microsoft.com/library/dn635309.aspx).

## Recursos adicionales

[Implementación de SharePoint con grupos de disponibilidad AlwaysOn de SQL Server en Azure](virtual-machines-workload-deploy-spsqlao-overview.md)

[Configurar una granja de servidores de intranet de SharePoint en una nube híbrida para pruebas](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Arquitecturas de Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Infografía de SharePoint con AlwaysOn de SQL Server](http://go.microsoft.com/fwlink/?LinkId=394788)

[Granjas de servidores de SharePoint hospedadas en servicios de infraestructura de Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Instrucciones de implementación de los servicios de infraestructura de Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carga de trabajo de servicios de infraestructura de Azure: aplicación de línea de negocio de alta disponibilidad](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=AcomDC_1203_2015-->