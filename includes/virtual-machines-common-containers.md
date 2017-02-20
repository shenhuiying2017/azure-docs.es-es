Las soluciones de nube de Azure están integradas en máquinas virtuales (emulación de hardware de equipo físico), para habilitar el empaquetado ágil de implementaciones de software y mejorar la consolidación de los recursos respecto al hardware físico. Los contenedores de [Docker](https://www.docker.com) y el ecosistema de Docker han aumentado considerablemente las formas de desarrollar, distribuir y administrar software distribuido. El código de la aplicación de un contenedor está aislado de la máquina virtual del host y de otros contenedores en la misma máquina virtual. Este aislamiento ofrece más agilidad en el desarrollo e implementación.

Azure ofrece los siguientes valores de Docker:

* [Muchas](../articles/virtual-machines/virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) formas [diferentes](../articles/virtual-machines/virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) de crear hosts Docker para que los contenedores se adapten a su situación
* [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/) crea clústeres de hosts de contenedor con orquestadores como **marathon** y **swarm**.
* [Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md) y las [plantillas de los grupo de recursos](../articles/resource-group-authoring-templates.md) para simplificar la implementación y actualización de aplicaciones distribuidas complejas
* integración con una matriz grande de ambas herramientas de administración de configuración patentadas y de código abierto

Y dado que se pueden crear mediante programación máquinas virtuales y contenedores de Linux en Azure, también puede usar herramientas de *orquestación* de contenedores y máquinas virtuales para crear grupos de máquinas virtuales e implementar aplicaciones dentro de contenedores de Linux y ahora [contenedores de Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview).

Este artículo no solo explica estos conceptos en un nivel alto, también contiene cientos de vínculos para obtener más información, tutoriales y productos relacionados con el uso del contenedor y el clúster en Azure. Si solo desea los vínculos, están aquí, en las [herramientas para trabajar con contenedores](#tools-for-working-with-azure-vms-and-containers).

## <a name="the-difference-between-virtual-machines-and-containers"></a>Diferencia entre las máquinas virtuales y los contenedores
Las máquinas virtuales se ejecutan en un entorno de virtualización de hardware aislado proporcionado por un [hipervisor](http://en.wikipedia.org/wiki/Hypervisor). En Azure, el servicio [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) atiende todo esto automáticamente; solo tiene que elegir el sistema operativo y configurarlo, &mdash;o cargar una imagen de máquina virtual personalizada para crear las máquinas virtuales. Las máquinas virtuales son una tecnología "curtida en mil batallas" comprobada, y hay muchas herramientas disponibles para administrar el sistema operativo y las aplicaciones que contienen.  Las aplicaciones de una máquina virtual se ocultan al sistema operativo host. Desde el punto de vista de una aplicación o un usuario en una máquina virtual, esta aparece como un equipo físico autónomo.

Los [contenedores de Linux](http://en.wikipedia.org/wiki/LXC) y los creados y hospedados con herramientas de Docker, no usan un hipervisor para proporcionar aislamiento. En su lugar, el host contenedor usa las características de aislamiento del sistema de archivos y de procesos del kernel de Linux para exponer al contenedor sus aplicaciones, determinadas características del kernel y su propio sistema de archivos aislado. Desde el punto de vista de una aplicación que se ejecuta dentro de un contenedor, el contenedor aparece como una instancia única del sistema operativo. Una aplicación contenida no puede ver los procesos ni otros recursos situado fuera de su contenedor.

Se utilizan mucho menos recursos en un contenedor de Docker que los que se usan en una máquina virtual. Los contenedores de Docker emplean un modelo de aislamiento y ejecución de aplicaciones que no comparte el kernel del host de Docker. El contenedor tiene mucho menor consumo de disco porque no incluye el sistema operativo completo. El tiempo de inicio y el espacio en disco necesario son mucho menores que en una máquina virtual.
Los contenedores de Windows proporcionan las mismas ventajas que los de Linux para las aplicaciones que se ejecutan en Windows. Los contenedores de Windows admiten el formato de imagen de Docker y la API de Docker, pero también se pueden administrar con PowerShell. Se encuentran disponibles dos tiempos de ejecución de contenedores con los contenedores de Windows, de Windows Server y de Hyper-V. Los contenedores de Hyper-V proporcionan un nivel adicional de aislamiento porque hospedan cada contenedor en una máquina virtual superoptimizada. Para obtener más información sobre los contenedores de Windows, consulte [Acerca de los contenedores de Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview). Para empezar a trabajar con contenedores de Windows en Azure, aprenda a [implementar un clúster de Azure Container Service](/articles/container-service/container-service-deployment.md).

## <a name="what-are-containers-good-for"></a>¿Para qué son buenos los contenedores?

Los contenedores pueden mejorar:

* La rapidez con la que se puede desarrollar y compartir el código de aplicación.
* La velocidad y la confianza con la que se puede probar una aplicación.
* La velocidad y la confianza con la que se puede implementar una aplicación.

Los contenedores se ejecutan en un host contenedor, es decir, un sistema operativo y, en Azure, eso significa una máquina virtual de Azure. Incluso si ya le gusta la idea de los contenedores, va a necesitar una infraestructura de máquina virtual que hospede los contenedores, pero los beneficios consisten en que a los contenedores no les importa en qué máquina virtual se ejecutan (aunque si el contenedor desea un entorno de ejecución Windows o Linux será importante, por ejemplo).


## <a name="what-are-containers-good-for"></a>¿Para qué son buenos los contenedores?
Son excelentes para muchas cosas, pero favorecen &mdash;al igual que [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) y [Azure Service Fabric](../articles/service-fabric/service-fabric-overview.md)&mdash; la creación de aplicaciones distribuidas de un solo servicio y orientadas a microservicio. En ellas, el diseño de la aplicación está basado en partes más pequeñas que admiten composición en lugar de en componentes más grandes y con acoplamiento más fuerte.

Esto es especialmente cierto en entornos de nube pública como Azure, en los que se alquilan máquinas virtuales cuando y donde lo desea. No sólo obtiene aislamiento y una implementación rápida y herramientas de orquestación, pero puede tomar decisiones de infraestructura de aplicaciones más eficaces.

Por ejemplo, podría disponer actualmente de una implementación compuesta por 9 máquinas virtuales de Azure de un tamaño grande para una aplicación distribuida de alta disponibilidad. Si los componentes de esta aplicación se pueden implementar en contenedores, es posible que pueda usar solo cuatro máquinas virtuales e implementar los componentes de su aplicación dentro de 20 contenedores para obtener redundancia y equilibrio de carga.

Esto es solo un ejemplo, por supuesto, pero si puede hacerlo en su escenario, puede ajustarse a picos de uso con más contenedores en lugar de más máquinas virtuales de Azure y usar la carga de CPU total restante de forma mucho más eficaz que antes.

Además, hay muchos escenarios que no se prestan a un enfoque de microservicios; usted sabrá distinguir mejor si le pueden ayudar mejor los microservicios o los contenedores.

### <a name="container-benefits-for-developers"></a>Ventajas del contenedor para los desarrolladores
En general, es fácil darse cuenta de que la tecnología de contenedores es un paso adelante, pero también ofrece ventajas más específicas. Veamos el ejemplo de los contenedores de Docker. En este tema no se profundiza en Docker [para más información al respecto, lea [What is Docker?](https://www.docker.com/whatisdocker/) (¿Qué es Docker?) o la [wikipedia](http://wikipedia.org/wiki/Docker_%28software%29)], pero Docker y su ecosistema ofrecen enormes ventajas tanto a los desarrolladores como a los profesionales de TI.

Los desarrolladores se acostumbran rápidamente a los contenedores de Docker porque, ante todo, facilitan el uso de contenedores de Linux y Windows:

* Pueden usar comandos incrementales simplificados para crear una imagen fija que sea fácil de implementar y que pueda automatizar la creación de esas imágenes mediante un dockerfile
* Dichas imágenes se pueden compartir fácilmente mediante comandos simples de inserción y de extracción de estilo [git](https://git-scm.com/) en [registros de Docker privados](https://registry.hub.docker.com/) o [públicos](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Pueden pensar en componentes de aplicación asilados en lugar de equipos
* Pueden usar un gran número de herramientas que comprenden los contenedores docker y diferentes imágenes base

### <a name="container-benefits-for-operations-and-it-professionals"></a>Ventajas de los contenedores para las operaciones y los profesionales de TI
Los profesionales de TI y de operaciones también se benefician de la combinación de los contenedores y las máquinas virtuales.

* los servicios independientes están aislados del entorno de ejecución del host de máquina virtual
* se puede verificar que el código independiente es idéntico
* los servicios independientes pueden iniciarse, detenerse y moverse rápidamente entre entornos de producción, pruebas y desarrollo

Características como estas &mdash;and existen más&mdash; emocionan a las empresas establecidas, en las que las organizaciones de tecnología de la información profesionales tienen la tarea de encajar recursos &mdash;incluidos la potencia de procesamiento pura&mdash; en las tareas requeridas no solo permanecen en la empresa, sino que aumentan la satisfacción del cliente y el alcance. Las pequeñas empresas, los ISV y las nuevas empresas tienen exactamente el mismo requisito, pero es posible que lo describan de manera diferente.

## <a name="what-are-virtual-machines-good-for"></a>¿Para qué resultan buenas las máquinas virtuales?
Las máquinas virtuales son la espina dorsal de la informática en nube, y eso no cambia. Si las máquinas virtuales se inician más lentamente, tienen una mayor superficie de disco y no se asignan directamente a una arquitectura de microservicios, tienen ventajas muy importantes:

1. De forma predeterminada, tienen protecciones de seguridad predeterminadas mucho más sólidas para el equipo host
2. Admiten cualquier configuración de aplicación y sistema operativo principal
3. Tienen ecosistemas de herramienta antiguos de comando y control
4. Proporcionan el entorno de ejecución a los contenedores de host

El último elemento es importante, porque una aplicación independiente todavía requiere un sistema operativo específico y un tipo de CPU, dependiendo de las llamadas que efectuará la aplicación. Es importante recordar que los contenedores se instalan en máquinas virtuales debido a que contienen las aplicaciones que desea implementar; los contenedores no son reemplazos para máquinas virtuales ni sistemas operativos.

## <a name="high-level-feature-comparison-of-vms-and-containers"></a>Comparación de características de alto nivel de máquinas virtuales y contenedores
En la siguiente tabla se describe a muy alto nivel el tipo de diferencias de características que &mdash;sin mucho trabajo adicional&mdash; existen entre máquinas virtuales y contenedores de Linux. Tenga en cuenta que es posible que algunas características resulten más o menos deseables dependiendo de las necesidades de su propia aplicación, y que al igual que con todo el software, el trabajo adicional proporciona una mayor compatibilidad de características, especialmente en el área de la seguridad.

| Característica | Máquinas virtuales | Contenedores |
|:--- | --- | --- |
| Compatibilidad con la seguridad "Predeterminada" |en un mayor grado |en un grado ligeramente menor |
| Memoria en disco necesaria |Sistema operativo más aplicaciones completos |Solo requisitos de aplicación |
| Tiempo que se tarda en iniciar |Significativamente más largo: el arranque del SO más la carga de la aplicación |Significativamente más corto: solamente es necesario que se inicien las aplicaciones porque el kernel ya está en ejecución |
| Portabilidad |Portable con la preparación adecuada |Portable en formato de imagen; suelen ser más pequeños |
| Automatización de imágenes |Varía considerablemente dependiendo del sistema operativo y las aplicaciones |[Registro docker](https://registry.hub.docker.com/); otros |

## <a name="creating-and-managing-groups-of-vms-and-containers"></a>Creación y administración de grupos de máquinas virtuales y contenedores
En este momento, cualquier arquitecto, desarrollador o especialista en operaciones de TI podrá pensar: "Puedo automatizar TODO esto; esta es realmente un centro de datos como servicio".

Tiene razón, puede serlo, y puede haber un número indeterminado de sistemas, muchos de las cuales es posible que ya esté usando, que puedan administrar grupos de máquinas virtuales de Azure e inyectar código personalizado mediante scripts, a menudo con [CustomScriptingExtension para Windows](https://msdn.microsoft.com/library/azure/dn781373.aspx) o [CustomScriptingExtension para Linux](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/). Puede automatizar &mdash;y es posible que ya lo haya hecho&mdash; sus implementaciones de Azure mediante PowerShell o scripts de la CLI de Azure.

Estas capacidades a menudo se migran posteriormente a herramientas como [Puppet](https://puppetlabs.com/) y [Chef](https://www.chef.io/) para automatizar la creación y configuración de máquinas virtuales a escala. (Consulte algunos vínculos sobre [cómo usar estas herramientas con Azure](#tools-for-working-with-containers)).

### <a name="azure-resource-group-templates"></a>Plantillas de grupo de recursos de Azure
Más recientemente, Azure publicó la API de REST de [Administración de recursos de Azure](../articles/resource-manager-deployment-model.md) y herramientas actualizadas de PowerShell y de la CLI de Azure para que sea más fácil usarlo. Puede implementar, modificar o volver a implementar topologías de toda la aplicación mediante [plantillas de Azure Resource Manager](../articles/resource-group-authoring-templates.md) con la API de administración de recursos de Azure usando:

* [Azure Portal mediante plantillas](https://github.com/Azure/azure-quickstart-templates) &mdash;sugerencia, use el botón "Implementar en Azure"
* la [CLI de Azure](../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* los [módulos de Azure PowerShell](../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="deployment-and-management-of-entire-groups-of-azure-vms-and-containers"></a>Implementación y administración de todos los grupos de máquinas virtuales y contenedores de Azure
Hay varios sistemas conocidos que pueden implementar todos los grupos de máquinas virtuales e instalar Docker (u otros sistemas de host del contenedor de Linux) en ellos como un grupo automatizable. Para obtener vínculos directos, consulte la sección [contenedores y herramientas](#containers-and-vm-technologies) disponible a continuación. Existen varios sistemas para hacer esto en mayor o menor medida, y esta lista no es exhaustiva. Dependiendo de su conjunto de habilidades y escenarios, puede que resulten útiles o no.

Docker tiene su propio conjunto de herramientas de creación de máquinas virtuales ([docker-machine](../articles/virtual-machines/virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) y una herramienta de administración de clústeres con contenedor de Docker de equilibrio de carga ([swarm](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Además, la [extensión de máquina virtual de Azure para Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) incluye compatibilidad predeterminada con [`docker-compose`](https://docs.docker.com/compose/), que puede implementar contenedores de aplicaciones configurados en varios contenedores.

Además, puede probar el [sistema operativo de centro de datos de Mesosphere (DCOS)](http://docs.mesosphere.com/install/azurecluster/). DCOS se basa en los "kernel de sistemas distribuidos" [mesos](http://mesos.apache.org/) de código abierto que le permiten tratar su centro de datos como un servicio direccionable. DCOS tiene paquetes integrados para varios sistemas importantes como [Spark](http://spark.apache.org/) y [Kafka](http://kafka.apache.org/) (y otros), así como servicios integrados como [Marathon](https://mesosphere.github.io/marathon/) (un sistema de control de contenedores) y [Chronos](https://mesos.github.io/chronos/) (un programador distribuido). Mesos se deriva de las lecciones aprendidas en Twitter, AirBnb y otras empresas de escala web. También puede usar **swarm** como motor de orquestaciones.

Además, [kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/) es un sistema de código abierto para la administración de grupos de máquinas virtuales y contenedores derivado de las lecciones aprendidas en Google. Incluso puede usar [kubernetes con trama para proporcionar compatibilidad con red](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave).

[Deis](http://deis.io/overview/) es una "Plataforma como-servicio" (PaaS) de código abierto que facilita la implementación y administración de aplicaciones en sus propios servidores. Deis se basa en Docker y CoreOS para proporcionar un PaaS ligero con un flujo de trabajo inspirado en Heroku. Puede [crear un grupo de máquina virtual de Azure de tres nodos e instalar Deis](../articles/virtual-machines/virtual-machines-linux-deis-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) en Azure fácilmente y luego [instalar una aplicación Hello World Go](../articles/virtual-machines/virtual-machines-linux-deis-cluster.md#deploy-and-scale-a-hello-world-application).

[CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html) es una distribución de Linux con una superficie optimizada, compatibilidad con Docker y su propio sistema de contenedor denominado [rkt](https://github.com/coreos/rkt), también tiene una herramienta de administración de grupos de contenedores denominada [fleet](https://coreos.com/using-coreos/clustering/).

Ubuntu, otra distribución de Linux muy popular, admite Docker muy bien, pero también admite [clústeres de Linux (estilo LXC)](https://help.ubuntu.com/lts/serverguide/lxc.html).

## <a name="tools-for-working-with-azure-vms-and-containers"></a>Herramientas para trabajar con máquinas virtuales de Azure y contenedores
Para trabajar con contenedores y máquinas virtuales de Azure se usan herramientas. En esta sección se proporciona una lista de algunos de los conceptos y herramientas más útiles o importantes acerca de contenedores, grupos y las herramientas de configuración y de orquestación más importantes que se usan con ellos.

> [!NOTE]
> Esta área está cambiando muy rápidamente, y mientras hacemos lo posible para mantener actualizado este tema y sus vínculos, podría ser una tarea imposible. Asegúrese de buscar temas interesantes para mantenerse al día.
>
>

### <a name="containers-and-vm-technologies"></a>Contenedores y tecnologías de máquina virtual
Algunas tecnologías de contenedor de Linux:

* [Docker](https://www.docker.com)
* [LXC](https://linuxcontainers.org/)
* [CoreOS y rkt](https://github.com/coreos/rkt)
* [Proyecto de contenedor abierto](http://opencontainers.org/)
* [RancherOS](http://rancher.com/rancher-os/)

Vínculos de contenedor de Windows:

* [Contenedores de Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)

Vínculos de Visual Studio Docker:

* [Visual Studio 2015 RC Tools para Docker - vista previa](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0)

Herramientas de Docker:

* [Docker daemon](https://docs.docker.com/installation/#installation)
* Clientes Docker
  * [Cliente de Windows Docker en Chocolatey](https://chocolatey.org/packages/docker)
  * [Instrucciones de instalación de Docker](https://docs.docker.com/installation/#installation)

Docker en Microsoft Azure:

* [Extensión de VM de Docker para Linux en Azure](../articles/virtual-machines/virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Guía de usuario de la extensión de máquina virtual de Azure Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md)
* [Uso de la extensión de la máquina virtual de Docker desde la interfaz de la línea de comandos de Azure (CLI de Azure)](../articles/virtual-machines/virtual-machines-linux-classic-cli-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Uso de la extensión de la máquina virtual de Docker con Azure Portal](../articles/virtual-machines/virtual-machines-linux-classic-portal-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Uso de una máquina Docker en Azure](../articles/virtual-machines/virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Uso de Docker con Swarm en Azure](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Introducción a Docker y Compose en Azure](../articles/virtual-machines/virtual-machines-linux-docker-compose-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Uso de una plantilla de grupo de recursos de Azure para crear rápidamente un host Docker en Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)
* [La compatibilidad integrada con `compose`](https://github.com/Azure/azure-docker-extension#11-public-configuration-keys) para las aplicaciones que contiene
* [Implementación de su propio Docker Registry privado en Azure](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Distribuciones de Linux y ejemplos de Azure:

* [CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html)

Configuración, administración del clúster y orquestación de contenedor:

* [Flota en CoreOS](https://coreos.com/using-coreos/clustering/)
* Deis

  * [Cree un grupo de máquina virtual de Azure de tres nodos, instale Deis e inicie una aplicación Hello World Go](../articles/virtual-machines/virtual-machines-linux-deis-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* kubernetes

  * [Guía completa para la implementación automatizada del clúster de Kubernetes con CoreOS y Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
  * [Visualizador de Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
* [Mesos](http://mesos.apache.org/)

  * [Sistema operativo de centro de datos de Mesosphere (DCOS)](http://beta-docs.mesosphere.com/install/azurecluster/)
* [Jenkins](https://jenkins-ci.org/) y [Hudson](http://hudson-ci.org/)

  * [Blog: Complemento esclavo de Jenkins para Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
  * [Repositorio de GitHub: Complemento de almacenamiento de Jenkins para Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
  * [Terceros: Complemento esclavo de Hudson para Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
  * [Terceros: Complemento de almacenamiento de Hudson para Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
* [Automatización de Azure](https://azure.microsoft.com/services/automation/)

  * [Vídeo: Cómo utilizar la automatización de Azure con máquinas virtuales Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
* PowerShell DSC para Linux

  * [Blog: Cómo hacer DSC de Powershell para Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
  * [GitHub: DSC de cliente Docker](https://github.com/anweiss/DockerClientDSC)

## <a name="next-steps"></a>Pasos siguientes
Desproteja [Docker](https://www.docker.com) y [Contenedores de Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview).

<!--Anchors-->
[microservices]: http://martinfowler.com/articles/microservices.html
[microservice]: http://martinfowler.com/articles/microservices.html
<!--Image references-->


<!--HONumber=Feb17_HO3-->


