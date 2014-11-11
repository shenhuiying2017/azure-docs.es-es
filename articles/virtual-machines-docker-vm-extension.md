<properties title="Using the Docker Virtual Machines Extension for Linux on Azure" pageTitle="Using the Docker VM Extension for Linux on Azure" description="Describes Docker and the Azure Virtual Machines extensions, and shows how to programmatically create Virtual Machines on Azure that are docker hosts from the command line using the azure-cli command interface." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="08/21/2014" ms.author="ralph.squillace@microsoft.com" />

# Uso de la extensión de máquina virtual Docker para Linux en Azure

[Docker][Docker] es uno de los enfoques de virtualización más populares que utilizan los [contenedores de Linux][contenedores de Linux] en lugar de máquinas virtuales como una forma de aislar datos y calcular recursos compartidos. Puede usar la extensión de VM Docker para que el [agente Linux de Azure][agente Linux de Azure] cree una VM Docker que hospede cualquier número de contenedores para sus aplicaciones de Azure.

Este tema sigue este [anuncio de blog de MS Open Tech][anuncio de blog de MS Open Tech] y describe:

-   [Contenedores Docker y Linux][Contenedores Docker y Linux]
-   [Uso de la extensión de VM Docker con Azure][Uso de la extensión de VM Docker con Azure]
-   [Extensiones de máquina virtual para Linux y Windows][Extensiones de máquina virtual para Linux y Windows]
-   [Recursos de contenedor y de gestión de contenedores para Azure][Recursos de contenedor y de gestión de contenedores para Azure]

## Contenedores Docker y Linux

[Docker][Docker] es uno de los enfoques de virtualización más populares que utilizan los [contenedores de Linux][contenedores de Linux] en lugar de máquinas virtuales como una forma de aislar datos y calcular recursos compartidos y proporciona otros servicios que le permiten generar o ensamblar aplicaciones rápidamente y distribuirlas entre otros contenedores Docker.

Los contenedores Docker y Linux no son [Hipervisores][Hipervisores] como Windows Hyper-V y [KVM][KVM] en Linux (existen muchos otros ejemplos). Los hipervisores virtualizan el sistema operativo subyacente para permitir a los sistemas operativos completos ejecutarse dentro del hipervisor como si fuesen una aplicación.

El enfoque del hipervisor proporciona ventajas de seguridad importantes porque la máquina virtual "invitada" no tiene acceso al sistema operativo "host"; en su lugar, es posible que solo utilice los recursos del hipervisor. Sin embargo, entre las desventajas se incluye un mayor procesamiento y sobrecarga de almacenamiento, así como un inicio relativamente más lento para las nuevas máquinas virtuales ya que, entre otras cosas, replican por completo los sistemas operativos invitados.

#### Contenedores Docker y Linux

Los enfoques de Docker y otros *contenedores* han reducido radicalmente tanto el tiempo y el procesamiento de inicio consumido como la sobrecarga necesaria al utilizar características de aislamiento de procesos y archivos del kernel de Linux para exponer únicamente características de kernel en un contenedor aislado de otro modo. Desde dentro del contenedor, las características del sistema de archivos y de kernel aparecen en la aplicación como si fuese la única aplicación que se está ejecutando.

Además, Docker proporciona varias características de gestión de contenedores que le permiten cargar varias imágenes del contenedor desde la comunidad así como generar y cargar por su cuenta muy, muy rápido. Para obtener información completa sobre qué es Docker y cómo funciona en realidad, consulte [¿Qué es Docker?][¿Qué es Docker?].

Como con la mayoría de las tecnologías, existen importantes ventajas y desventajas. Debido a que los contenedores comparten acceso al kernel del equipo host, si hay código malintencionado capaz de obtener la raíz es posible que también pueda obtener acceso no solo al equipo host si no también a los otros contenedores. Para asegurar aún más su sistema de contenedores, [Docker recomienda][Docker recomienda] el uso de directivas de grupo o de [seguridad basada en roles][seguridad basada en roles] adicionales, como [SELinux][SELinux] o [AppArmor][AppArmor], por ejemplo; así como reducir todo lo posible las capacidades de kernel que se otorgan al contenedor. Además, hay muchos otros documentos en Internet que describen enfoques de seguridad mediante el uso de contenedores como Docker.

## Uso de la extensión de VM Docker con Azure

Para utilizar la extensión de VM Docker con Azure, debe instalar una versión de [azure-cli][azure-cli] superior a la 0.8.6 (en el momento de redactar este documento la versión actual es la 0.8.7). Puede instalar azure-cli tanto en Mac como en Linux.

> [WACOM.NOTE] También puede instalar azure-cli en Microsoft Windows. Sin embargo, dado que Docker se generó con las dependencias del kernel de Linux, para utilizar Windows como un cliente de Docker es necesario que hospede una distribución completa de Linux como una máquina virtual dentro de Hyper-V o de otro hipervisor. Una vez que haya hecho esto, puede usar los comandos de azure-cli y de Docker de este documento y los de Docker. Docker tiene un programa de instalación propio, [Boot2Docker][Boot2Docker] que puede utilizar para automatizar esta misma instalación.

El proceso completo para usar Docker en Azure es simple:

-   Instale las herramientas de la línea de comando azure-cli y sus dependencias en el equipo desde el que desee controlar Azure (en Windows, será una distribución de Linux que se ejecuta como una máquina virtual)
-   Utilice los comandos azure-cli de Docker para crear un host de Docker de VM en Azure
-   Utilice los comandos Docker locales para gestionar sus contenedores de Docker en su VM Docker en Azure.

> [WACOM.NOTE] La azure-cli (interfaz de la línea de comandos) actualmente es la única forma de crear una VM controlada por Docker en Azure para hospedar contenedores de Docker. El documento de instalación general está [aquí][aquí]; las siguientes secciones ofrecen algunas sugerencias más para instalar sin problemas en diferentes sistemas operativos.

### Instalación de azure-cli en Linux

En Linux, la instalación de azure-cli necesita el [administrador de paquetes de node (npm)][administrador de paquetes de node (npm)], que requiere nodejs, por lo que va a utilizar su administrador de paquetes favorito para instalar nodejs, dependiendo de la plataforma que elija. Si tiene npm instalado, para obtener el paquete de azure-cli escriba:

    npm install -g azure-cli

que instalará el paquete azure-cli de forma global. Para confirmar la instalación, escriba `azure` en el símbolo del sistema y, tras un breve instante, debería ver el arte ASCII de azure-cli, que enumera los comandos básicos que tiene a su disposición. Si la instalación ha funcionado correctamente, debería poder escribir `azure help vm` y ver que uno de los comandos que aparecen en la lista es "docker".

> [WACOM.NOTE] Si está utilizando una instalación LTS de Ubuntu 14.04, esa imagen tiene una instalación de nodo ligeramente diferente que puede requerir trabajo adicional. Una sugerencia que parece que funciona bien se encuentra [aquí][1] en la sección **Instalación mediante un PPA**, que describe cómo instalar la versión más reciente de nodejs directamente y parece que funciona bien en una distribución LTS de Ubuntu 14.04.

También, como la mayoría de los componentes Linux, puede clonar el origen, compilarlo e instalarlo del mismo modo de forma local. Las instrucciones para ello se encuentran en [][azure-cli]<https://github.com/Azure/azure-sdk-tools-xplat></a>.

### Instalación de azure-cli en Mac

En un Mac, la forma más sencilla de instalar azure-cli también es mediante npm con el mismo comando: `npm install -g azure-cli`. Sin embargo, también puede utilizar el [instalador de Mac][instalador de Mac]. Como con Linux y Windows, entonces puede escribir `azure` en el símbolo del sistema asociado y confirmar que azure-cli está instalado.

### Conexión de azure-cli con su cuenta de Azure

Antes de poder utilizar azure-cli debe asociar las credenciales de su cuenta de Azure con azure-cli en su plataforma. La sección [Conexión a su suscripción Azure][Conexión a su suscripción Azure] explica cómo descargar e importar su archivo **.publishsettings** o asociar la línea de comandos de azure-cli con un identificador de la organización. Los pasos para ambos métodos de autenticación y autorización se describen en el documento anterior.

> [WACOM.NOTE] Existen algunas diferencias de comportamiento al utilizar uno u otro método de autorización, por eso debe asegurarse de leer el documento anterior para comprender las diferentes funcionalidades.

### Instalación de Docker y uso de la extensión de VM Docker para Azure

Ahora tiene un equipo (o una máquina virtual en ese equipo) con azure-cli instalado y conectado a su cuenta Azure. Siga las [instrucciones de instalación de Docker][instrucciones de instalación de Docker] para instalar Docker de forma local en su equipo. Para la mayoría de los sistemas operativos y distribuciones, esto significa escribir `apt-get install docker.io`. Confirme que la versión de Docker es la 1.0 o superior.

Ha instalado el símbolo de azure-cli en su equipo, lo ha conectado a su cuenta de Azure y ha instalado Docker. Para crear una nueva VM host de Docker en Azure se necesita una imagen de VM de Linux que tenga instalado el [Agente de VM Linux de Azure][agente Linux de Azure]. Actualmente, las únicas imágenes que ya tienen esto instalado son

-   una imagen Ubuntu de la galería de imágenes o

-   una imagen de Linux personalizada que haya creado con el Agente de VM Linux de Azure instalado y configurado, consulte [Agente de VM Linux de Azure][agente Linux de Azure] para obtener más información sobre cómo generar su propia VM Linux personalizada con el Agente VM de Azure.

Con el símbolo del sistema azure-cli, busque la imagen Ubuntu más reciente en la galería de la VM que utilizar escribiendo

`azure vm image list | grep Ubuntu-14_04`

y prepárese para copiar el nombre de una de las imágenes más recientes que aparecen en la lista. En el símbolo del sistema, escriba

    azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20140729-alpha2-es-es-30GB" <username> <password>

donde:

-   *<vm-cloudservice name>* es el nombre de la VM que se convertirá en el equipo host del contenedor de Docker en Azure.

-   *<username>* es el nombre de usuario del usuario raíz predeterminado de la VM.

-   *<password>* es la contraseña de la cuenta de *nombre de usuario* que cumple con los estándares de complejidad de Azure.

> [WACOM.NOTE] Actualmente, una contraseña debe tener al menos 8 caracteres, contener un carácter en minúscula y otro en mayúscula, un número y un carácter especial como [!@\#$%^&+=][!@\#$%^&+=]. No, el punto al final de la frase anterior NO es un carácter especial.

Si el comando es correcto, debería ver algo como lo siguiente, dependiendo de los argumentos y opciones precisas que haya utilizado:

![][0]

> [WACOM.NOTE] Como se ha mencionado anteriormente, crear una máquina virtual puede llevar unos minutos, pero después de que se haya aprovisionado, el demonio de Docker se inicia y puede conectarse al host del contenedor de Docker.

Para probar la VM Docker que ha creado en Azure, escriba

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:4243 info`

donde *<vm-name-you-used>* es el nombre de la máquina virtual que ha utilizado en su llamada a `azure vm docker create`. De nuevo, en función de los argumentos y opciones específicos que haya especificado, debería ver algo parecido a la respuesta siguiente, que indica que su VM host de Docker está activa y ejecutándose en Azure y esperando sus comandos.

![][2]

## Una nota sobre la autenticación de VM host de Docker

Además de crear la VM Docker, el comando `azure vm docker create` también crea automáticamente los certificados necesarios para permitir a su equipo cliente de Docker conectarse al host del contenedor de Azure mediante HTTPS y los certificados se almacenan tanto en el equipo cliente como en el host, según corresponda. En las siguientes ejecuciones, se vuelven a utilizar los certificados existentes y se comparten con el nuevo host.

De forma predeterminada, los certificados se colocan en `~/.docker` y Docker se configurará para ejecutarse en el puerto **4243**. Si desea utilizar un puerto o directorio diferente, entonces puede utilizar una de las siguientes opciones de línea de comando `azure vm docker create` para configurar su VM host de contenedor Docker para que utilice un puerto diferente o diferentes certificados para conectarse con los clientes:

    -dp, --docker-port [port]              Port to use for docker [4243]
    -dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]

El demonio de Docker del host está configurado para que realice escuchas y autenticaciones de las conexiones de cliente en el puerto especificado mediante los certificados generados por el comando `azure vm docker create`. El equipo cliente debe tener estos certificados para obtener acceso al host de Docker.

> [WACOM.NOTE] Al contrario, un host de red que se ejecuta sin estos certificados será vulnerable a cualquiera que se pueda conectar al equipo. Antes de modificar la configuración predeterminada, asegúrese de que comprende los riesgos de sus equipos y aplicaciones.

## Pasos siguientes

Ahora está listo para emitir los comandos de Docker en la [guía de usuario de Docker][guía de usuario de Docker] en su VM host de Docker en Azure.

## Extensiones de máquina virtual para Linux y Windows

La extensión de VM Docker para Azure es solo una de las varias extensiones de VM que ofrecen un comportamiento especial, hay aún más en desarrollo. Por ejemplo, varias de las características de la [extensión del agente VM de Linux][agente Linux de Azure] le permiten modificar y gestionar la imagen, incluyendo características de seguridad, características de kernel y red, etc. La extensión VMAccess para las imágenes de Windows le permite restablecer o modificar la configuración de Acceso a Escritorio remoto y restablecer la contraseña del administrador.

Para obtener una lista completa, consulte [Extensiones de VM de Azure][Extensiones de VM de Azure].

<!--Anchors-->

  [Docker]: https://www.docker.com/
  [contenedores de Linux]: http://en.wikipedia.org/wiki/LXC
  [agente Linux de Azure]: http://azure.microsoft.com/es-es/documentation/articles/virtual-machines-linux-agent-user-guide/
  [anuncio de blog de MS Open Tech]: http://msopentech.com/blog/2014/08/15/getting_started_docker_on_microsoft_azure/
  [Contenedores Docker y Linux]: #Docker-and-Linux-Containers
  [Uso de la extensión de VM Docker con Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
  [Extensiones de máquina virtual para Linux y Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
  [Recursos de contenedor y de gestión de contenedores para Azure]: #Container-and-Container-Management-Resources-for-Azure
  [Hipervisores]: http://en.wikipedia.org/wiki/Hypervisor
  [KVM]: http://www.linux-kvm.org/page/Main_Page
  [Docker recomienda]: https://docs.docker.com/articles/security/
  [seguridad basada en roles]: http://en.wikipedia.org/wiki/Role-based_access_control
  [SELinux]: http://selinuxproject.org/page/Main_Page
  [AppArmor]: http://wiki.apparmor.net/index.php/Main_Page
  [azure-cli]: https://github.com/Azure/azure-sdk-tools-xplat
  [Boot2Docker]: https://docs.docker.com/installation/windows/
  [aquí]: http://azure.microsoft.com/es-es/documentation/articles/xplat-cli/#install
  [1]: https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-an-ubuntu-14-04-server
  [instalador de Mac]: http://go.microsoft.com/fwlink/?linkid=252249&clcid=0x409
  [Conexión a su suscripción Azure]: http://azure.microsoft.com/es-es/documentation/articles/xplat-cli/#configure
  [instrucciones de instalación de Docker]: https://docs.docker.com/installation/#installation
  [0]: ./media/virtual-machines-docker/dockercreateresults.png
  [2]: ./media/virtual-machines-docker/connectingtodockerhost.png
  [guía de usuario de Docker]: https://docs.docker.com/userguide/
  [Extensiones de VM de Azure]: http://msdn.microsoft.com/es-es/library/azure/dn606311.aspx
