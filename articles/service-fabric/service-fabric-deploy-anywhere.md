<properties
   pageTitle="Deploy Anywhere con Service Fabric (Windows Server y Linux) | Microsoft Azure"
   description="Los clústeres de Service Fabric se ejecutarán en Windows Server y Linux, lo que permite que las aplicaciones de Service Fabric se puedan implementar y alojar en cualquier lugar donde sea posible ejecutar Windows Server y Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunalds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/19/2015"
   ms.author="kunalds"/>

# Deploy Anywhere en Windows Server o Linux con Service Fabric
Service Fabric permite la creación de clústeres de Service Fabric en cualquier máquina virtual o en equipos que ejecuten Windows Server o Linux. Esto permite que las aplicaciones de Service Fabric se implementen y ejecuten en cualquier entorno, ya sea de forma local o mediante cualquier proveedor de nube donde tenga un conjunto de equipos de Windows Server o Linux conectados entre sí. Service Fabric ofrecerá un paquete de instalación para crear clústeres de Service Fabric. Un beneficio clave de la característica Deploy Anywhere es que no se producen bloqueos de proveedor cuando se compila una aplicación con Service Fabric, ya que se puede elegir dónde se ejecutan esas aplicaciones. Esto también aumenta las posibilidades de llegar a un conjunto más amplio de la base de clientes, ya que estos pueden tener diferentes requisitos para los entornos en los que van a ejecutar sus aplicaciones; por ejemplo, los clientes del sector sanitario y financiero pueden tener necesidades distintas de las de un fabricante de automóviles o una empresa de viajes. Se espera que la vista previa técnica de esta característica se lance en el primer trimestre de 2016.

## Sistemas operativos compatibles
Podrá crear clústeres en máquinas virtuales o máquinas que ejecuten estos sistemas operativos: * Windows Server 2012 R2 * Windows Server 2016 * Linux

## Lenguajes de programación admitidos
Podrá escribir aplicaciones de Service Fabric en cualquiera de estos lenguajes de programación: * C# * Java

## Configuración y creación de clústeres
Service Fabric proporcionará un paquete de instalación que podrá descargar desde Microsoft Download Center. Una vez que haya descargado este paquete, deberá realizar cambios en un archivo de configuración para especificar la configuración para el clúster. Una vez que se haya modificado la configuración del clúster, se ejecutará un script de instalación que creará el clúster que abarcará las máquinas que especificó en la configuración del clúster. Cuando lancemos la versión preliminar de esta funcionalidad en el primer trimestre de 2016, proporcionaremos los detalles exactos del proceso de instalación.

## Implementaciones en la nube frente a implementaciones locales
El proceso de creación de un clúster local de Service Fabric será similar al proceso de creación de un clúster en cualquier nube que elija donde cuente con un conjunto de máquinas virtuales. Los pasos iniciales para aprovisionar las máquinas virtuales estarán determinados por la nube o el entorno local que use, pero, una vez que tenga un conjunto de máquinas virtuales con conectividad de red habilitada entre ellas, los pasos siguientes para configurar el paquete de Service Fabric, editar la configuración del clúster y ejecutar la creación del clúster y los scripts de administración son muy similares. Esto garantiza que sus conocimientos y su experiencia con respecto al uso y la administración de los clústeres de Service Fabric se puedan aplicar si decide cambiar a un nuevo entorno de hospedaje.

## Ventajas de la característica Deploy Anywhere
* No se generan bloqueos de proveedor.
* Las aplicaciones de Service Fabric, una vez escritas, se pueden ejecutar en varios entornos de hospedaje con pocos cambios (o ninguno).
* El proceso de creación de aplicaciones de Service Fabric se aplica de una plataforma de hospedaje a otras.
* La experiencia operativa que se adquiere al ejecutar clústeres de Service Fabric se puede aplicar de un entorno de implementación a otros.
* Amplio alcance de los clientes sin limitaciones derivadas de las restricciones del entorno de hospedaje.
* Consiga un nivel adicional de confiabilidad y protección contra interrupciones generalizadas al poder mover los servicios a otro entorno de implementación en caso de que un centro de datos o un proveedor de nube sufran una interrupción.

## Diferencias entre Deploy Anywhere y los clústeres de Service Fabric hospedados de Azure
La ejecución de clústeres de Service Fabric mediante el servicio hospedado de Azure ofrece algunas ventajas con respecto al uso de la opción Deploy Anywhere, por lo que, si no tiene necesidades específicas en cuanto al lugar donde se ejecutan los clústeres, se recomienda ejecutarlos como clústeres hospedados de Azure. En Azure se proporciona integración con otras características y servicios de Azure, lo que facilita las operaciones y la administración del clúster:

* Portal de Azure: el Portal de Azure facilita la creación y la administración de clústeres.
* Administrador de recursos de Azure: el uso de ARM permite una administración sencilla de todos los recursos que usa el clúster como una unidad y simplifica el costo del seguimiento y la facturación. 
* Diagnóstico: en Azure se proporciona la integración con WAD (diagnósticos de Microsoft Azure) y Visión operativa para el diagnóstico.
* Escalado automático: para los clústeres hospedados de Azure se proporcionará una funcionalidad integrada de escalado automático. En otros entornos en los que se usa la característica Deploy Anywhere, hay que crear una característica propia de escalado automático o realizar el escalado manualmente mediante las API que ofrece Service Fabric para escalar los clústeres.

<!---HONumber=AcomDC_1203_2015-->