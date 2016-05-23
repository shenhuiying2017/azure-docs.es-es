<properties
   pageTitle="Creación de clústeres de Service Fabric de Azure en Windows Server y Linux | Microsoft Azure"
   description="Los clústeres de Service Fabric se ejecutan en Windows Server y Linux, lo que significa que podrá implementar y hospedar aplicaciones de Service Fabric en cualquier lugar donde sea posible ejecutar Windows Server y Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="Chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/02/2016"
   ms.author="chackdan"/>

# Creación de clústeres independientes de Service Fabric en Windows Server o Linux
Service Fabric de Azure permite crear clústeres de Service Fabric en las máquinas virtuales o en los equipos que ejecutan Windows Server o Linux. Esto significa que podrá implementar y ejecutar aplicaciones de Service Fabric en cualquier entorno donde haya un conjunto de equipos de Windows Server o Linux que estén conectados entre sí, ya sea de manera local o con algún proveedor de nube.

**Nota**: La creación de un clúster en Azure debe realizarse mediante una plantilla del modelo de recursos de Azure o el Portal de Azure. Lea [Configuración de un clúster de Service Fabric con una plantilla del Administrador de recursos de Azure](service-fabric-cluster-creation-via-arm.md) o [Creación de un clúster de Service Fabric en el Portal de Azure](service-fabric-cluster-creation-via-portal.md) para obtener más información.

Service Fabric proporciona un paquete de instalación para crear estos clústeres independientes de Service Fabric de forma local. Una ventaja clave de esto es que no se producen bloqueos de proveedor cuando se compila una aplicación con Service Fabric, ya que se puede elegir dónde se ejecutan esas aplicaciones. Esto también aumenta su capacidad para llegar a una base de clientes más amplia, dado que los clientes pueden tener diferentes requisitos para los entornos en los que quieren ejecutar las aplicaciones. Por ejemplo, los clientes del sector sanitario o financiero pueden tener diferentes necesidades que los clientes del sector automovilístico o de viajes.

## Sistemas operativos compatibles
Podrá crear clústeres en máquinas virtuales o equipos que ejecuten estos sistemas operativos:

* Windows Server 2012 R2
* Windows Server 2016
* Linux

Para obtener más información sobre Windows Server, lea [Service Fabric cluster creation for Windows Server](service-fabric-cluster-creation-for-windows-server.md) (Creación de clústeres de Service Fabric para Windows Server).

## Configuración y creación de clústeres
Service Fabric proporciona un paquete de instalación que se puede descargar. Una vez que haya descargado este paquete, deberá realizar cambios en un archivo de configuración JSON para especificar la configuración del clúster. Una vez que se haya modificado la configuración del clúster, se ejecuta un script de instalación que crea el clúster abarcando las máquinas que especificó en la configuración del clúster. También puede ejecutar un script para quitar un clúster de un conjunto de máquinas.

## Implementaciones en la nube frente a implementaciones locales
El proceso de creación de un clúster local de Service Fabric es similar al proceso de creación de un clúster en cualquier nube que elija donde cuente con un conjunto de máquinas virtuales. Los pasos iniciales para aprovisionar las máquinas virtuales se regirán por el proveedor de la nube o el entorno local que use. Cuando tenga un conjunto de máquinas virtuales con la conectividad de red habilitada entre ellas, los pasos para configurar el paquete de Service Fabric, editar la configuración del clúster y ejecutar la creación del clúster y los scripts de administración son idénticos. Esto garantiza que sus conocimientos y su experiencia con respecto al uso y la administración de los clústeres de Service Fabric se puedan aplicar si decide cambiar a un nuevo entorno de hospedaje.

## Ventajas de crear clústeres independientes de Service Fabric
* Dado que ningún proveedor está bloqueado, puede elegir dónde crear el clúster.
* Las aplicaciones de Service Fabric, una vez escritas, se pueden ejecutar en varios entornos de hospedaje con pocos cambios (o ninguno).
* El proceso de creación de aplicaciones de Service Fabric se aplica de un entorno de hospedaje a otro.
* La experiencia operativa que se adquiere al ejecutar y administrar clústeres de Service Fabric se puede aplicar de un entorno a otro.
* Existe una amplia cobertura de clientes sin limitaciones derivadas de las restricciones del entorno de hospedaje.
* Hay un nivel adicional de confiabilidad y protección contra interrupciones generalizadas al poder mover los servicios a otro entorno de implementación en caso de que un centro de datos o un proveedor de nube sufran una interrupción.

## Ventajas de los clústeres de Service Fabric en Azure frente a los clústeres independientes de Service Fabric creados de forma local
La ejecución de clústeres de Service Fabric en Azure proporciona ventajas frente a la opción local. Por tanto, si no tiene necesidades específicas en cuanto al lugar donde se ejecutan los clústeres, le sugerimos que los ejecute en Azure. En Azure se proporciona integración con otras características y servicios de Azure, lo que facilita las operaciones y la administración del clúster y hace que sea más confiable.

* **Portal de Azure:** el Portal de Azure facilita la creación y la administración de clústeres.

* **Administrador de recursos de Azure:** el uso del Administrador de recursos de Azure permite una administración sencilla de todos los recursos que usa el clúster como una unidad y simplifica el costo del seguimiento y la facturación.
* **Clúster de Service Fabric como recurso de Azure** Un clúster de Service Fabric es un recurso de ARM, por tanto, puede ajustarlo como hace con otros recursos de ARM en Azure.
* **Integración con la infraestructura de Azure**: Service Fabric se coordina con la infraestructura de Azure subyacente para el SO, la red y otras actualizaciones con el fin de mejorar la disponibilidad y la confiabilidad de las aplicaciones.  
* **Diagnósticos:** en Azure se proporciona la integración con Diagnósticos y Visión operativa de Azure.
* **Escalado automático:** para los clústeres hospedados en Azure, proporcionamos una funcionalidad integrada de escalado automático debido a los conjuntos de escalas de la máquina virtual. En los entornos locales y otros entornos en la nube, hay que crear una característica propia de escalado automático o realizar el escalado manualmente mediante las API que ofrece Service Fabric para escalar los clústeres.

## Pasos siguientes
Creación de un clúster en máquinas virtuales o equipos que ejecutan Windows Server: [Service Fabric cluster creation for Windows Server](service-fabric-cluster-creation-for-windows-server.md) (Creación de clústeres de Service Fabric para Windows Server)

Creación de un clúster en máquinas virtuales o equipos que ejecutan Linux: [Service Fabric en Linux](service-fabric-linux-overview.md)

<!---HONumber=AcomDC_0511_2016-->