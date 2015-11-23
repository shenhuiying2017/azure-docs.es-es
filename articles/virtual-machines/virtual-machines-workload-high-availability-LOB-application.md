<properties 
	pageTitle="Aplicación de línea de negocio de Azure | Microsoft Azure" 
	description="Obtenga información sobre el valor de una aplicación de línea de negocio de Azure, configure un entorno de prueba e implemente una configuración de alta disponibilidad." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/09/2015" 
	ms.author="josephd"/>

# Carga de trabajo de servicios de infraestructura de Azure: aplicación de línea de negocio de alta disponibilidad

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.


Configure la primera o siguiente aplicación de línea de negocio basada en web solo de intranet en Microsoft Azure y aprovéchese de la facilidad de configuración y de la posibilidad de ampliar rápidamente la aplicación para que incluya una nueva capacidad.
 
Con las máquinas virtuales y las características de la Red virtual de los Servicios de infraestructura de Azure, podrá implementar y ejecutar rápidamente una aplicación de línea de negocio que sea accesible para los usuarios de su organización. Por ejemplo, puede configurar lo siguiente.

![](./media/virtual-machines-workload-high-availability-LOB-application/workload-lobapp-phase4.png)
 
Dado que Red Virtual de Azure es una extensión de la red local con toda la nomenclatura correcta y el enrutamiento del tráfico establecidos, los usuarios tendrán acceso a los servidores de la aplicación de línea de negocio de la misma manera que si se encontrara en un centro de datos local.

Esta configuración permite ampliar fácilmente la capacidad de la aplicación agregando nuevas máquinas virtuales de Azure en las que los costos en curso tanto de mantenimiento como de hardware son menores que los de ejecutar un conjunto equivalente en el centro de datos.

El siguiente paso es configurar una aplicación de línea de negocio de desarrollo y pruebas hospedada en Azure.

## Creación de una aplicación de línea de negocio de desarrollo y pruebas hospedada en Azure

Una red virtual entre locales está conectada a una red local con una conexión de ExpressRoute o VPN de sitio a sitio. Si quiere crear un entorno de desarrollo y pruebas que imite la configuración final y experimentar con el acceso a la aplicación y realizar la administración remota a través de una conexión VPN, vea [Configuración de una aplicación de línea de negocio (LOB) basada en web en una nube híbrida para pruebas](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md).

![](./media/virtual-machines-workload-high-availability-LOB-application/CreateLOBAppHybridCloud_3.png)
 
Puede crear este entorno de desarrollo y pruebas de forma gratuita con su [suscripción a MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/) o una [suscripción de evaluación de Azure](http://azure.microsoft.com/pricing/free-trial/).

El siguiente paso es crear una aplicación de línea de negocio de alta disponibilidad en Azure.

## Implementación de una aplicación de línea de negocio hospedada en Azure

La configuración representativa y básica de una aplicación de línea de negocio de alta disponibilidad en Azure tiene el siguiente aspecto.

![](./media/virtual-machines-workload-high-availability-LOB-application/workload-lobapp-phase4.png)
 
Consta de:

- Una aplicación de línea de negocio solo de intranet con dos servidores en los niveles web y de base de datos.
- Una configuración AlwaysOn de SQL Server con dos máquinas virtuales que ejecuten SQL Server y un equipo de nodos de mayoría en un clúster.
- Servicios de dominio de Active Directory en la red virtual con dos controladores de dominio de réplica.

Para obtener información general sobre aplicaciones de línea de negocio, vea [Plano de arquitectura de aplicaciones de línea de negocio](http://msdn.microsoft.com/dn630664).

### Lista de materiales

Esta configuración básica requiere el conjunto de componentes y servicios siguiente de Azure:

- Siete máquinas virtuales
- Cuatro discos de datos adicionales para los controladores de dominio y las máquinas virtuales que ejecutan SQL Server
- Tres conjuntos de disponibilidad
- Una red virtual entre locales
- Dos cuentas de almacenamiento

Estas son las máquinas virtuales y sus tamaños predeterminados para esta configuración.

Elemento | Descripción de la máquina virtual | Imagen de la Galería | Tamaño predeterminado 
--- | --- | --- | --- 
1\. | Primer controlador de dominio | Windows Server 2012 R2 Datacenter | D1
2\. | Segundo controlador de dominio | Windows Server 2012 R2 Datacenter | D1
3\. | Base de datos principal | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | D4
4\. | Base de datos secundaria | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | D4
5\. | Nodo de mayoría para el clúster | Windows Server 2012 R2 Datacenter | D1
6\. | Primer servidor web | Windows Server 2012 R2 Datacenter | D3
7\. | Segundo servidor web | Windows Server 2012 R2 Datacenter | D3

Para calcular los costos estimados para esta configuración, vea [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/).

1. En **Módulos**, haga clic en **Calcular** y luego en **Máquinas virtuales** las veces suficientes como para crear una lista de siete máquinas virtuales.
2. Para cada máquina virtual, seleccione:
	- La región que quiera
	- **Windows** como tipo
	- **Estándar** como plan de tarifa
	- El tamaño predeterminado de la tabla anterior o el tamaño que prefiera como **Tamaño de instancia**

> [AZURE.NOTE]La calculadora de precios de Azure no incluye los costos adicionales de la licencia de SQL Server para las dos máquinas virtuales que ejecutan SQL Server 2014 Enterprise. Para más información, vea [Precios de máquinas virtuales: SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

### Fases de implementación

Para implementar esta configuración, siga este proceso :

- Fase 1: Configuración de Azure 

	Use Azure PowerShell para crear una red virtual entre locales, conjuntos de disponibilidad y las cuentas de almacenamiento. Para los pasos de configuración detallados, consulte [Fase 1](virtual-machines-workload-high-availability-LOB-application-phase1.md).

- Fase 2: Configuración de los controladores de dominio.

	Configure dos controladores de dominio de réplica de Active Directory y los valores de DNS para la red virtual. Para los pasos de configuración detallados, consulte [Fase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md).

- Fase 3: Configuración de la infraestructura de SQL Server.

	Cree las máquinas virtuales que ejecutan SQL Server y el clúster. Para los pasos de configuración detallados, consulte [Fase 3](virtual-machines-workload-high-availability-LOB-application-phase3.md).

- Fase 4: Configuración de servidores web.

	Cree las máquinas virtuales de servidor web y agregue la aplicación de línea de negocio. Para la información detallada, consulte [Fase 4](virtual-machines-workload-high-availability-LOB-application-phase4.md).

- Fase 5: Configuración de un grupo de disponibilidad AlwaysOn de SQL Server.

	Prepare las bases de datos de la aplicación de línea de negocio, cree un grupo de disponibilidad AlwaysOn de SQL Server y agregue las bases de datos de la aplicación al grupo. Para los pasos de configuración detallados, consulte [Fase 5](virtual-machines-workload-high-availability-LOB-application-phase5.md).

Una vez configurada, puede ampliar fácilmente esta aplicación de línea de negocio agregando más servidores web o máquinas virtuales que ejecuten SQL Server al clúster.

## Recursos adicionales

[Implementación de una aplicación de línea de negocio de alta disponibilidad en Azure](virtual-machines-workload-high-availability-LOB-application-overview.md)

[Plano de arquitectura de aplicaciones de línea de negocio](http://msdn.microsoft.com/dn630664)

[Configuración de una aplicación de LOB basada en web en una nube híbrida para pruebas](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Instrucciones de implementación de los servicios de infraestructura de Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carga de trabajo de servicios de infraestructura de Azure: granja de SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=Nov15_HO3-->