<properties
   pageTitle="Información general de Service Fabric y los contenedores | Microsoft Azure"
   description="Información general sobre Service Fabric y el uso de contenedores para implementar aplicaciones de microservicios. Este artículo le proporciona una visión general de cómo se pueden utilizar los contenedores y las funcionalidades disponibles en Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager=""
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="msfussell"/>

# Vista previa: Service Fabric y contenedores 

>[AZURE.NOTE] Esta característica está en fase de vista previa para Linux y no está disponible actualmente en Windows Server. Estará también disponible como vista previa para Windows Server en la próxima versión de Service Fabric después de la disponibilidad general de Windows Server 2016 y se admitirá en versiones posteriores después de ella.

## Introducción
Service Fabric es un [orquestador](service-fabric-cluster-resource-manager-introduction.md) de servicios a través de un clúster de máquinas. Se pueden desarrollar servicios de muchas maneras, desde el uso de los [modelos de programación de Service Fabric ](service-fabric-choose-framework.md) a la implementación de [archivos ejecutables invitados](service-fabric-deploy-existing-app.md). De forma predeterminada Service Fabric permite implementar y activar estos servicios como procesos. Los procesos proporcionan la activación más rápida y el uso de densidad más alto de los recursos del clúster. Service Fabric también puede implementar servicios en imágenes de contenedores y, lo que es más importante, puede combinar servicios en procesos y servicios en contenedores en la misma aplicación. Puede conseguir lo mejor de ambos mundos según el escenario.

## ¿Qué son los contenedores?
Los contenedores son componentes encapsulados, que se pueden implementar individualmente y que se ejecutan como instancias aisladas en el mismo kernel, aprovechando la virtualización de nivel del sistema operativo. Esto significa que cada aplicación, su tiempo de ejecución, las dependencias y las bibliotecas del sistema se ejecutan dentro de un contenedor con acceso completo y privado a su propia vista aislada de las construcciones del sistema operativo. Junto con la portabilidad, este grado de aislamiento de seguridad y recursos es la principal ventaja del uso de contenedores con Service Fabric, que de lo contrario ejecutará los servicios en procesos.

Los contenedores constituyen una tecnología de virtualización que permite virtualizar el sistema operativo subyacente de las aplicaciones. Proporcionan un entorno inmutable para que las aplicaciones se ejecuten con un grado de aislamiento variable. Los contenedores se ejecutan directamente en el kernel y tienen una vista aislada del sistema de archivos y de otros recursos. En comparación con las máquinas virtuales, los contenedores ofrecen las siguientes ventajas:

1.  **Tamaño pequeño**: los contenedores usan un espacio de almacenamiento único y deltas más pequeños para cada capa, por lo que resultan más eficaces.

2.  **Tiempo de arranque rápido**: los contenedores no tienen que arrancar un sistema operativo, por lo que pueden estar disponibles mucho más rápidamente que las máquinas virtuales, normalmente en cuestión de segundos.

3.  **Portabilidad**: se puede portar una imagen de aplicación de contenedor para que se ejecute en la nube o localmente, dentro de las máquinas virtuales o directamente en las máquinas físicas.

4.  **Proporcionan regulación de recursos** para limitar los recursos físicos que puede consumir un contenedor en el host.

## Tipos de contenedor
Service Fabric admite los siguientes tipos de contenedores:

### Contenedores de Docker en Linux
Docker proporciona las API de nivel superior para crear y administrar contenedores sobre contenedores de kernel de Linux y proporciona Docker Hub como repositorio central para almacenar y recuperar imágenes del contenedor.

### Contenedores de Windows Server
Windows Server 2016 proporciona dos tipos distintos de contenedores que difieren en el nivel de aislamiento que ofrecen. Los contenedores de Windows Server se parecen a los de Docker en que tienen un aislamiento del sistema de archivos y un espacio de nombres pero comparten el kernel con el host en que se ejecutan. En Linux, este aislamiento tradicionalmente lo han proporcionado los cgroups y los espacios de nombres, y los contenedores de Windows Server se comportan de forma parecida.

Los contenedores de Windows Hyper-V proporcionan un mayor grado de seguridad y aislamiento ya que los contenedores no comparten el kernel del sistema operativo entre ellos o con el host. Con este mayor nivel de aislamiento de seguridad, los contenedores de Hyper-V están especialmente diseñados para escenarios hostiles, multiinquilino.

La ilustración siguiente muestra los diferentes tipos de niveles de aislamiento y virtualización disponibles en el sistema operativo. ![Plataforma de Service Fabric][Image1]

## Escenarios de uso de contenedores
Entre los ejemplos habituales de cuándo se usan contenedores, se puede destacar

1. **Levantamiento y movimiento de IIS**. Si tiene algunas aplicaciones existentes de ASP.NET MVC que le gustaría seguir usando, en lugar de migrarlas al núcleo de ASP.NET, colóquelas en un contenedor. Estas aplicaciones de ASP.NET MVC dependen de IIS. Puede empaquetarlas en imágenes de contenedor a partir de la imagen IIS creada previamente e implementarlas con Service Fabric. Consulte [Imágenes de contenedores en Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/quick_start_images) que describe cómo crear imágenes IIS.


2. **Mezcla de los contenedores y los microservicios de Service Fabric**. Use una imagen de contenedor existente para parte de la aplicación. Por ejemplo, podría usar el [contenedor NGINX](https://hub.docker.com/_/nginx/) para el front-end web de su aplicación y los servicios con estado compilados con Reliable Services para el cálculo de back-end más intenso. Un ejemplo de este escenario incluye las aplicaciones de juegos


3. **Reducción del impacto de los servicios de "vecinos ruidosos"**. La capacidad de regulación de recursos de los contenedores le permite restringir los recursos que utiliza un servicio en un host. Si hay servicios que pueden consumir un gran número de recursos y, por tanto, afectan al rendimiento de otros, (por ejemplo, una consulta de larga ejecución como operación), considere la posibilidad de poner estos servicios en contenedores mediante el regulador de recursos.

## Compatibilidad de los contenedores con Service Fabric
Service Fabric admite actualmente la implementación de contenedores de Docker en contenedores de Linux y Windows Server en Windows Server 2016. La compatibilidad con contenedores de Hyper-V se agregará en una versión futura.

En el [modelo de aplicación](service-fabric-application-model.md) de Service Fabric, un contenedor representa un host de la aplicación en el que se colocan varias réplicas de servicio. Se admiten los siguientes escenarios para contenedores:

1.	**Contenedores invitados**: similar al [escenario de archivo ejecutable de invitado](service-fabric-deploy-existing-app.md) en el que puede implementar las aplicaciones existentes en un contenedor. Por ejemplo nodejs, javascript o cualquier código (EXE).


2.	**Servicios sin estado dentro de contenedores**: estos son los servicios sin estado creados mediante los modelos de programación de Reliable Services y Reliable Actors. En la actualidad, esto es solo compatible con Linux. La compatibilidad con los servicios sin estado en contenedores de Windows se agregará en una versión futura.

 
3.	**Servicios con estado dentro de contenedores**: estos son los servicios con estado creados mediante los modelos de programación de Reliable Actors en Linux. En la actualidad, Reliable Services no es compatible con Linux. La compatibilidad con los servicios con estado en contenedores de Windows se agregará en una versión futura.

Service Fabric tiene varias funcionalidades de contenedor que le ayudarán a crear aplicaciones que se componen de microservicios en contenedores. Son los llamados servicios en contenedores. Entre estas funcionalidades, cabe destacar:

- Activación e implementación de la imagen de contenedor
- Regulador de recursos
- Autenticación de repositorio
- Asignación de puerto a host de contenedor
- Detección y comunicación entre contenedores
- Capacidad de configurar y establecer variables de entorno

## Pasos siguientes
En este artículo, ha aprendido acerca de los contenedores, ha aprendido que Service Fabric es un orquestador de contenedores y las características que ofrece Service Fabric para dar soporte a los contenedores. En los siguientes pasos analizaremos algunos ejemplos de cada una de las características para mostrarle cómo utilizarlas.

[Implementación de un contenedor en Service Fabric](service-fabric-deploy-container.md)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png

<!---HONumber=AcomDC_0928_2016-->