<properties title="Uso de la extensi&oacute;n de VM Docker con el portal de Azure" pageTitle="Uso de la extensi&oacute;n de VM Docker para Linux en Azure" description="Describe Docker y las extensiones de m&aacute;quinas virtuales de Azure y muestra c&oacute;mo crear m&aacute;quinas virtuales en Azure mediante programaci&oacute;n que son host de Docker desde la l&iacute;nea de comandos usando la interfaz de comandos azure-cli." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/21/2014" ms.author="rasquill" />
<!--The next line, with one pound sign at the beginning, is the page title-->

# Uso de la extensión de VM Docker con el portal de Azure

[Docker][Docker] es uno de los enfoques de virtualización más populares que utilizan los [contenedores de Linux][contenedores de Linux] en lugar de máquinas virtuales como una forma de aislar datos y calcular recursos compartidos. Puede usar la extensión de VM Docker para que el [agente Linux de Azure][agente Linux de Azure] cree una VM Docker que hospede cualquier número de contenedores para sus aplicaciones de Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

En esta sección

-   [Creación de una nueva máquina virtual desde la galería de imágenes][Creación de una nueva máquina virtual desde la galería de imágenes]
-   [Creación de certificados de Docker][Creación de certificados de Docker]
-   [Adición de la extensión de VM Docker][Adición de la extensión de VM Docker]
-   [Comprobación del cliente Docker y del host Docker de Azure][Comprobación del cliente Docker y del host Docker de Azure]
-   [Pasos siguientes][Pasos siguientes]

> [WACOM.NOTE] En este tema se describe cómo crear una máquina virtual Docker en el portal de Azure. Para ver cómo crear una VM Docker en la línea de comandos, vea [Uso de la extensión de VM Docker desde la interfaz entre plataformas de Azure (xplat-cli)][Uso de la extensión de VM Docker desde la interfaz entre plataformas de Azure (xplat-cli)]. Para ver un análisis de alto nivel de contenedores y sus ventajas, consulte [Documento de alto nivel de Docker][Documento de alto nivel de Docker].

## <span id="createvm"></span>Creación de una nueva máquina virtual desde la galería de imágenes</a>

El primer paso requiere una máquina virtual de Azure de una imagen de Linux que admita la extensión de VM Docker, usando una imagen Ubuntu 14.04 LTS de la galería de imágenes como imagen de servidor de ejemplo y el escritorio Ubuntu 14.04 como cliente. En el portal, haga clic en **+ Nuevo** en la esquina inferior izquierda para crear una nueva instancia de máquina virtual y seleccione una imagen Ubuntu 14.04 LTS entre las selecciones disponibles o en la galería de imágenes completa, tal y como se muestra a continuación.

> [WACOM.NOTE] Actualmente, solo las imágenes Ubuntu 14.04 LTS posteriores a julio de 2014 admiten la extensión de VM Docker.

![Creación de una imagen Ubuntu][Creación de una imagen Ubuntu]

## <a id'dockercerts'>Creación de certificados de Docker</a>

Una vez creada la máquina virtual, asegúrese de que Docker está instalado en su equipo cliente. (Para obtener detalles, consulte [Instrucciones de instalación de Docker][Instrucciones de instalación de Docker].)

Cree los archivos de certificado y de clave para la comunicación Docker conforme a [Ejecución de Docker con https][Ejecución de Docker con https] y colóquelos en el directorio **`~/.docker`** de su equipo cliente.

> [WACOM.NOTE] La extensión de VM Docker del portal actualmente requiere credenciales con la codificación base64.

En la línea de comandos, use **`base64`** u otra herramienta de codificación que prefiera para crear temas con la codificación base64. Hacer esto con un conjunto sencillo de archivos de certificado y clave podría tener un aspecto similar al siguiente:

     ~/.docker$ l
     ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
     ~/.docker$ base64 ca.pem > ca64.pem
     ~/.docker$ base64 server-cert.pem > server-cert64.pem
     ~/.docker$ base64 server-key.pem > server-key64.pem
     ~/.docker$ l
     ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
     ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem

## <a id'adddockerextension'>Adición de la extensión de VM Docker</a>

Para agregar la extensión de VM Docker, busque la instancia de máquina virtual que creó, desplácese hacia abajo hasta **Extensiones** y haga clic en ella para mostrar extensiones de VM, tal y como se muestra a continuación.

![][0]

### Adición de una extensión

Haga clic en **+ Agregar** para mostrar las extensiones de VM posibles que puede agregar a esta máquina virtual.

![][1]

### Selección de la extensión de VM Docker

Elija la extensión de VM Docker, que muestra la descripción y vínculos importantes de Docker, y haga clic en **Crear** en la parte inferior para comenzar el procedimiento de instalación.

![][2]

![][3]

### Adición de los archivos de certificado y clave:

En los campos del formulario, escriba las versiones con codificación base64 del certificado CA, su certificado de servidor y su clave de servidor, tal y como se muestra en el siguiente gráfico.

![][4]

> [WACOM.NOTE] Tenga en cuenta que (como en la imagen anterior), se usa 4243 de forma predeterminada. Puede escribir cualquier extremo aquí, pero el paso siguiente será abrir el extremo coincidente. Si cambia el valor predeterminado, asegúrese de abrir el extremo coincidente en el paso siguiente.

## Adición del extremo de comunicación de Docker

Cuando vea su máquina virtual en el grupo de recursos que ha creado, desplácese hacia abajo para hacer clic en **Extremos** para ver los extremos en la máquina virtual tal y como se muestra aquí.

![][5]

Haga clic en **+ Agregar** para agregar otro extremo y, en el caso predeterminado, escriba un nombre para el extremo (en este ejemplo **docker**) y 4243 tanto para puertos privados como para puertos públicos. Deje el valor del protocolo que se muestra en **TCP** y haga clic en **Aceptar** para crear el extremo.

![][6]

## <span id="testclientandserver"></span>Comprobación del cliente Docker y del host Docker de Azure</a>

Busque y copie el nombre del dominio de la máquina virtual y, en la línea de comandos del equipo cliente, escriba `docker --tls -H tcp://`*dockerextension*`.cloudapp.net:4243 info` (donde *dockerextension* se cambia por el subdominio de la máquina virtual).

El resultado debe ser similar a esto:

    $ docker --tls -H tcp://dockerextension.cloudapp.net:4243 info
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
     Pool Name: docker-8:1-131214-pool
     Pool Blocksize: 65.54 kB
     Data file: /var/lib/docker/devicemapper/devicemapper/data
     Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
     Data Space Used: 305.7 MB
     Data Space Total: 107.4 GB
     Metadata Space Used: 729.1 kB
     Metadata Space Total: 2.147 GB
     Library Version: 1.02.82-git (2013-10-04)
    Execution Driver: native-0.2
    Kernel Version: 3.13.0-36-generic
    WARNING: No swap limit support

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Pasos siguientes

Está preparado para ir a la [Guía del usuario de Docker][Guía del usuario de Docker] y usar su VM Docker. Si desea comenzar a crear máquinas virtuales Docker rápida y repetidamente, vea [Uso de la extensión de VM Docker desde la interfaz entre plataformas de Azure (xplat-cli)][Uso de la extensión de VM Docker desde la interfaz entre plataformas de Azure (xplat-cli)].

<!--Anchors--> <!--Image references--> <!--Link references-->

  [Docker]: https://www.docker.com/
  [contenedores de Linux]: http://en.wikipedia.org/wiki/LXC
  [agente Linux de Azure]: ../virtual-machines-linux-agent-user-guide/
  [Creación de una nueva máquina virtual desde la galería de imágenes]: #createvm
  [Creación de certificados de Docker]: #dockercerts
  [Adición de la extensión de VM Docker]: #adddockerextension
  [Comprobación del cliente Docker y del host Docker de Azure]: #testclientandserver
  [Pasos siguientes]: #next-steps
  [Uso de la extensión de VM Docker desde la interfaz entre plataformas de Azure (xplat-cli)]: http://azure.microsoft.com/es-es/documentation/articles/virtual-machines-docker-with-xplat-cli/
  [Documento de alto nivel de Docker]: http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard
  [Creación de una imagen Ubuntu]: ./media/virtual-machines-docker-with-portal/ChooseUbuntu.png
  [Instrucciones de instalación de Docker]: https://docs.docker.com/installation/#installation
  [Ejecución de Docker con https]: http://docs.docker.com/articles/https/
  [0]: ./media/virtual-machines-docker-with-portal/ClickExtensions.png
  [1]: ./media/virtual-machines-docker-with-portal/ClickAdd.png
  [2]: ./media/virtual-machines-docker-with-portal/ChooseDockerExtension.png
  [3]: ./media/virtual-machines-docker-with-portal/CreateButtonFocus.png
  [4]: ./media/virtual-machines-docker-with-portal/AddExtensionFormFilled.png
  [5]: ./media/virtual-machines-docker-with-portal/AddingEndpoint.png
  [6]: ./media/virtual-machines-docker-with-portal/AddEndpointFormFilledOut.png
  [Guía del usuario de Docker]: https://docs.docker.com/userguide/
