# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Uso de herramientas de automatización de la infraestructura con máquinas virtuales de Azure
Para crear y administrar máquinas virtuales (VM) de Azure de manera coherente a escala, suele ser deseable alguna forma de automatización. Existen muchas herramientas y soluciones que le permiten automatizar la implementación de toda la infraestructura de Azure y el ciclo de vida de administración. En este artículo se detallan algunas de las herramientas de automatización de la infraestructura que puede usar en Azure. Estas herramientas se adaptan normalmente a alguno de los siguientes enfoques:

- Automatización de la configuración de las máquinas virtuales
    - Estas herramientas incluyen [Ansible](#ansible), [Chef](#chef) y [Puppet](#puppet).
    - Entre las herramientas específicas para la personalización de máquinas virtuales se incluye [cloud-init](#cloud-init) para máquinas virtuales Linux, [PowerShell Desired State Configuration (DSC)](#powershell-dsc) y la [extensión de script personalizado de Azure](#azure-custom-script-extension) para todas las máquinas virtuales de Azure.
 
- Automatización de la administración de la infraestructura
    - Entre las herramientas se incluye [Packer](#packer) para automatizar las compilaciones personalizadas de imágenes de máquina virtual, y [Terraform](#terraform) para automatizar el proceso de compilación de la infraestructura.
    - [Azure Automation](#azure-automation) puede realizar acciones en toda la infraestructura de Azure y en la local.

- Automatización de la implementación y entrega de aplicaciones
    - Algunos ejemplos son [Visual Studio Team Services](#visual-studio-team-services) y [Jenkins](#jenkins).


## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) es un motor de automatización para la administración de configuraciones, la creación de máquinas virtuales o la implementación de aplicaciones. Ansible utiliza un modelo sin agente, normalmente con claves SSH, para autenticar y administrar las máquinas de destino. Las tareas de configuración se definen en runbooks e incluyen un número de módulos de Ansible disponibles para llevar a cabo tareas específicas. Para más información, consulte el [funcionamiento de Ansible](https://www.ansible.com/how-ansible-works).

Obtenga información sobre cómo:

- [Instalar y configurar Ansible en Linux para su uso con Azure](../articles/virtual-machines/linux/ansible-install-configure.md).
- [Crear una máquina virtual básica](../articles/virtual-machines/linux/ansible-create-vm.md).
- [Crear un entorno de máquina virtual completo que incluya los recursos de soporte](../articles/virtual-machines/linux/ansible-create-complete-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) es una plataforma de automatización que ayuda a definir cómo se configura, implementa y administra la infraestructura. Entre los componentes adicionales se incluye Chef Habitat para la automatización del ciclo de vida de la aplicación más que de la infraestructura, y Chef InSpec que ayuda a automatizar el cumplimiento normativo con los requisitos de seguridad o de directiva. Los clientes de Chef se instalan en las máquinas de destino, con uno o varios servidores de Chef centrales que almacenan y administran las configuraciones. Para más información, consulte la [introducción a Chef](https://docs.chef.io/chef_overview.html).

Obtenga información sobre cómo:

- [Implementar Chef Automate desde Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Instalar Chef en Windows y crear máquinas virtuales de Azure](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) es una plataforma de automatización preparada para la empresa que controla el proceso de entrega e implementación de la aplicación. Los agentes se instalan en las máquinas de destino para permitir a Puppet Master ejecutar los manifiestos que definen la configuración deseada de la infraestructura y las máquinas virtuales de Azure. Puppet puede integrarse con otras soluciones como Jenkins y GitHub para obtener un flujo de trabajo de DevOps mejorado. Para más información, consulte el [funcionamiento de Puppet](https://puppet.com/product/how-puppet-works).

Obtenga información sobre cómo:

- [Implementar Puppet desde Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2016-1?tab=Overview).


## <a name="cloud-init"></a>Cloud-Init
[cloud-init](https://cloudinit.readthedocs.io) es un enfoque ampliamente usado para personalizar una máquina virtual Linux la primera vez que se arranca. Puede usar cloud-init para instalar paquetes y escribir archivos o para configurar los usuarios y la seguridad. Dado que se llama a cloud-init durante el proceso de arranque inicial, no se necesitan pasos adicionales ni agentes para aplicar la configuración.  Para más información sobre cómo dar formato correctamente a sus archivos `#cloud-config`, consulte el [sitio de documentación de cloud-init](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  Los archivos `#cloud-config` son archivos de texto codificados en base64.

cloud-init también funciona entre distribuciones. Por ejemplo, no use **apt-get install** o **yum install** para instalar un paquete. En su lugar, puede definir una lista de paquetes que se van a instalar. Cloud-init usará automáticamente la herramienta de administración de paquetes nativos para la distribución de Linux (distro) que seleccione.

 Estamos trabajando activamente con nuestros asociados de distribuciones de Linux certificadas para disponer de imágenes con cloud-init habilitado en Azure Marketplace. Estas imágenes harán que las implementaciones y configuraciones de cloud-init funcionen perfectamente con las máquinas virtuales y los conjuntos de escalado de máquinas virtuales. En la tabla siguiente se describe la disponibilidad actual de imágenes con cloud-init habilitado en la plataforma de Azure:

| Publicador | Oferta | SKU | Versión | preparado para cloud-init
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |16.04-LTS |más reciente |yes | 
|Canonical |UbuntuServer |14.04.5-LTS |más reciente |yes |
|CoreOS |CoreOS |Stable |más reciente |yes |
|OpenLogic |CentOS |7-CI |más reciente |Vista previa |
|Redhat |RHEL |7-RAW-CI |más reciente |Vista previa |

Obtener información más detallada sobre cloud-init en Azure:

- [Compatibilidad con cloud-init para máquinas virtuales Linux en Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Consulte el tutorial sobre configuración automatizada de máquinas virtuales con cloud-init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell Desired State Configuration (DSC)](https://msdn.microsoft.com/en-us/powershell/dsc/overview) es una plataforma de administración que se usa para definir la configuración de las máquinas de destino. DSC también se puede utilizar en Linux mediante el [servidor de infraestructura de administración abierta (OMI)](https://collaboration.opengroup.org/omi/).

Las configuraciones de DSC definen lo que se debe instalar en una máquina y cómo configurar el host. Un motor de administración de configuración local (LCM) se ejecuta en cada nodo de destino que procesa las acciones requeridas en función de las configuraciones insertadas. Un servidor de extracción es un servicio web que se ejecuta en un host central para almacenar las configuraciones de DSC y los recursos asociados. El servidor de extracción se comunica con el motor de LCM en cada host de destino para proporcionar las configuraciones necesarias e informar sobre el cumplimiento.

Obtenga información sobre cómo:

- [Crear una configuración básica de DSC](https://msdn.microsoft.com/powershell/dsc/quickstart).
- [Configurar un servidor de extracción de DSC](https://msdn.microsoft.com/powershell/dsc/pullserver).
- [Usar DSC para Linux](https://msdn.microsoft.com/powershell/dsc/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Extensión de script personalizado de Azure
La extensión de script personalizado para [Linux](../articles/virtual-machines/linux/extensions-customscript.md) o [Windows](../articles/virtual-machines/windows/extensions-customscript.md) descarga y ejecuta scripts en máquinas virtuales de Azure. Puede utilizar la extensión al crear una máquina virtual o en cualquier momento después de empezar a usarla. 

Los scripts se pueden descargar desde Azure Storage o desde cualquier ubicación pública como, por ejemplo, un repositorio de GitHub. Con la extensión de script personalizado, puede escribir scripts en cualquier lenguaje que se pueda ejecutar en la máquina virtual de origen. Estos scripts se pueden usar para instalar aplicaciones o configurar la máquina virtual según sea necesario. Para proteger las credenciales, la información confidencial como las contraseñas se puede almacenar en una configuración protegida. Estas credenciales solo se descifran dentro de la máquina virtual.

Obtenga información sobre cómo:

- [Crear una máquina virtual Linux con la CLI de Azure y usar la extensión de script personalizado](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Crear una máquina virtual Windows con Azure PowerShell y usar la extensión de script personalizado](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packer
[Packer](https://www.packer.io) automatiza el proceso de compilación cuando se crea una imagen personalizada de la máquina virtual en Azure. Puede usar Packer para definir el sistema operativo y ejecutar scripts posteriores a la configuración que permiten personalizar la máquina virtual según sus necesidades específicas. Una vez terminada la configuración, la máquina virtual se captura como una imagen de Manage Disks. Packer automatiza el proceso para crear la máquina virtual de origen, la red y los recursos de almacenamiento, ejecutar los scripts de configuración y, finalmente, crear la imagen de la máquina virtual.

Obtenga información sobre cómo:

- [Usar Packer para crear una imagen de máquina virtual Linux en Azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Usar Packer para crear una imagen de máquina virtual Windows en Azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) es una herramienta de automatización que le permite definir y crear una infraestructura de Azure completa con un lenguaje de formato de plantilla única: el lenguaje de configuración de HashiCorp (HCL). Con Terraform, puede definir las plantillas que automatizan el proceso de creación de los recursos de la red, del almacenamiento y de la máquina virtual para una solución de aplicación determinada. Puede utilizar las plantillas de Terraform existentes para otras plataformas con Azure para garantizar la coherencia y simplificar la implementación de la infraestructura sin necesidad de convertirlas a una plantilla de Azure Resource Manager.

Obtenga información sobre cómo:

- [Instalar y configurar Terraform con Azure](../articles/virtual-machines/linux/terraform-install-configure.md).
- [Crear una infraestructura de Azure con Terraform](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/) usa runbooks para procesar un conjunto de tareas en las máquinas virtuales de su elección. Azure Automation se usa para administrar las máquinas virtuales existentes en lugar de para crear una infraestructura. Azure Automation puede ejecutarse en máquinas virtuales Linux y Windows, así como en máquinas locales virtuales o físicas con Hybrid Runbook Worker. Los runbooks se pueden almacenar en un repositorio de control de código fuente como GitHub. Estos runbooks se pueden ejecutar manualmente o según una programación definida.

Azure Automation también proporciona un servicio Desired State Configuration (DSC) que le permite crear definiciones para configurar un conjunto determinado de máquinas virtuales. DSC garantiza que se aplica la configuración necesaria y que la máquina virtual sigue siendo coherente. DSC de Automatización de Azure se puede ejecutar tanto en máquinas virtuales Windows como Linux.

Obtenga información sobre cómo:

- [Crear un runbook de PowerShell](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Usar Hybrid Runbook Worker para administrar recursos locales](../articles/automation/automation-hybrid-runbook-worker.md).
- [Usar DSC de Automatización de Azure](../articles/automation/automation-dsc-getting-started.md).


## <a name="visual-studio-team-services"></a>Visual Studio Team Services
[Team Services](https://www.visualstudio.com/team-services/) es un conjunto de herramientas que le ayudarán a compartir y realizar un seguimiento del código, usar compilaciones automatizadas y crear una canalización de desarrollo e integración continua (CI/CD) completa. Team Services se integra con Visual Studio y otros editores para facilitar el uso. Team Services también puede crear y configurar máquinas virtuales de Azure e implementar código en ellas.

Obtenga información sobre cómo:

- [Crear una canalización de integración continua con Team Services](../articles/virtual-machines/windows/tutorial-vsts-iis-cicd.md).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) es un servidor de integración continua que ayuda a implementar y probar aplicaciones, y a crear canalizaciones automatizadas para la entrega de código. Existen cientos de complementos para ampliar la plataforma principal de Jenkins y también lo puede integrar con muchos otros productos y soluciones mediante los webhooks. Puede instalar Jenkins de forma manual en una máquina virtual de Azure, ejecutar Jenkins desde dentro de un contenedor de Docker o utilizar una imagen de Azure Marketplace que se haya creado previamente.

Obtenga información sobre cómo:

- [Crear una infraestructura de desarrollo en una máquina virtual Linux en Azure con Jenkins, GitHub y Docker](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Pasos siguientes
Hay muchas opciones diferentes para utilizar herramientas de automatización de infraestructura en Azure. Es libre de elegir la solución que mejor se adapte a sus necesidades y entorno. Para empezar a trabajar y probar algunas de las herramientas integradas en Azure, vea cómo automatizar la personalización de una máquina virtual [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) o [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md).
