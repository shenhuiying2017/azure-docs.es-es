---
title: "Uso de la extensión de máquina virtual Docker para Linux | Microsoft Docs"
description: "Describe Docker y las extensiones de máquinas virtuales de Azure y cómo crear máquinas virtuales en Azure que son host de Docker mediante la CLI de Azure en el modelo de implementación clásico."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 19cf64e8-f92c-43ad-a120-8976cd9102ac
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/27/2016
ms.author: rasquill
ms.openlocfilehash: 932744208d9d53c87e31dcdf9e34539750be4bdb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-docker-vm-extension-with-the-azure-classic-portal"></a>Uso de la extensión de máquina virtual de Docker con el Portal de Azure clásico
> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.

[Docker](https://www.docker.com/) es uno de los enfoques de virtualización más populares que utilizan los [contenedores de Linux](http://en.wikipedia.org/wiki/LXC) en lugar de máquinas virtuales como una forma de aislar datos y calcular recursos compartidos. Puede usar la extensión de máquina virtual Docker que administra el [agente Linux de Azure] para crear una máquina virtual Docker que hospede un número cualquiera de contenedores para sus aplicaciones en Azure.

> [!NOTE]
> En este tema se describe cómo crear una máquina virtual de Docker desde el Portal de Azure clásico. Para ver cómo crear una máquina virtual de Docker en la línea de comandos, consulte [Uso de la extensión de VM Docker desde la interfaz de la línea de comandos de Azure (CLI de Azure)]. Para ver un análisis de alto nivel de los contenedores y sus ventajas, consulte [Documento de alto nivel de Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).
> 
> 

## <a name="create-a-new-vm-from-the-image-gallery"></a>Creación de una nueva máquina virtual desde la galería de imágenes
El primer paso requiere una máquina virtual de Azure de una imagen de Linux que admita la extensión de VM Docker, usando una imagen Ubuntu 14.04 LTS de la galería de imágenes como imagen de servidor de ejemplo y el escritorio Ubuntu 14.04 como cliente. En el portal, haga clic en **+ Nuevo** en la esquina inferior izquierda para crear una nueva instancia de máquina virtual y seleccione una imagen Ubuntu 14.04 LTS entre las selecciones disponibles o en la galería de imágenes completa, tal y como se muestra a continuación.

> [!NOTE]
> Actualmente, solo las imágenes Ubuntu 14.04 LTS posteriores a julio de 2014 admiten la extensión de VM Docker.
> 
> 

![Creación de una imagen Ubuntu](./media/portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Creación de certificados de Docker
Una vez creada la máquina virtual, asegúrese de que Docker está instalado en su equipo cliente. (Para obtener detalles, consulte [Instrucciones de instalación de Docker](https://docs.docker.com/installation/#installation)).

Cree los archivos de certificado y de clave para la comunicación de Docker conforme a [Ejecución de Docker con https] y colóquelos en el directorio **`~/.docker`** de su equipo cliente.

> [!NOTE]
> La extensión de VM Docker del portal actualmente requiere credenciales con la codificación base64.
> 
> 

En la línea de comandos, use **`base64`** u otra herramienta de codificación que prefiera para crear temas con la codificación base64. Hacer esto con un conjunto sencillo de archivos de certificado y clave podría tener un aspecto similar al siguiente:

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>Adición de la extensión de VM Docker
Para agregar la extensión de VM Docker, busque la instancia de máquina virtual que creó, desplácese hacia abajo hasta **Extensiones** y haga clic en ella para mostrar extensiones de VM, como se muestra a continuación.

> [!NOTE]
> Esta funcionalidad solo se admite en el Portal de vista previa: https://portal.azure.com/
> 
> 

![](media/portal-use-docker/ClickExtensions.png)

### <a name="add-an-extension"></a>Adición de una extensión
Haga clic en **+ Agregar** para mostrar las extensiones de VM posibles que puede agregar a esta máquina virtual.

![](media/portal-use-docker/ClickAdd.png)

### <a name="select-the-docker-vm-extension"></a>Selección de la extensión de VM Docker
Elija la extensión de VM Docker, que muestra la descripción y vínculos importantes de Docker, y haga clic en **Crear** en la parte inferior para comenzar el procedimiento de instalación.

![](media/portal-use-docker/ChooseDockerExtension.png)

![](media/portal-use-docker/CreateButtonFocus.png)

### <a name="add-your-certificate-and-key-files"></a>Adición de los archivos de certificado y clave:
En los campos del formulario, escriba las versiones con codificación base64 del certificado CA, su certificado de servidor y su clave de servidor, tal y como se muestra en el siguiente gráfico.

![](media/portal-use-docker/AddExtensionFormFilled.png)

> [!NOTE]
> Tenga en cuenta que (como en la imagen anterior), se usa 2376 de forma predeterminada. Puede escribir cualquier extremo aquí, pero el paso siguiente será abrir el extremo coincidente. Si cambia el valor predeterminado, asegúrese de abrir el extremo coincidente en el paso siguiente.
> 
> 

## <a name="add-the-docker-communication-endpoint"></a>Adición del extremo de comunicación de Docker
Al ver el grupo de recursos que ha creado, seleccione el grupo de seguridad de red asociado a la máquina virtual y haga clic en **Reglas de seguridad de entrada** para ver las reglas, como se muestra aquí.

![](media/portal-use-docker/AddingEndpoint.png)

Haga clic en **+ Agregar** para agregar otra regla y, en el caso predeterminado, escriba un nombre para el punto de conexión (en este ejemplo **Docker**) y 2376 para "Intervalo de puertos de destino". Deje el valor del protocolo que se muestra en **TCP** y haga clic en **Aceptar** para crear el punto de conexión.

![](media/portal-use-docker/AddEndpointFormFilledOut.png)

## <a name="test-your-docker-client-and-azure-docker-host"></a>Comprobación del cliente de Docker y del host de Docker de Azure
Busque y copie el nombre del dominio de la máquina virtual y, en la línea de comandos del equipo cliente, escriba `docker --tls -H tcp://`*dockerextension*`.cloudapp.net:2376 info` (donde *dockerextension* se cambia por el subdominio de la máquina virtual).

El resultado debe ser similar a esto:

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
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
```

Cuando haya completado los pasos anteriores, dispondrá de un host Docker totalmente funcional que se ejecuta en una máquina virtual de Azure, configurado para conectarse remotamente desde otros clientes.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
Está preparado para ir a la [Guía del usuario de Docker] y usar su VM Docker. Si desea automatizar la creación de hosts de Docker en máquinas virtuales de Azure a través de la interfaz de línea de comandos, consulte [Uso de la extensión de VM Docker desde la interfaz de la línea de comandos de Azure (CLI de Azure)]

<!--Anchors-->
[Create a new VM from the Image Gallery]:#createvm
[Create Docker Certificates]:#dockercerts
[Add the Docker VM Extension]:#adddockerextension
[Test Docker Client and Azure Docker Host]:#testclientandserver
[Next steps]:#next-steps

<!--Image references-->
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[6]:./media/markdown-template-for-new-articles/pretty49.png
[7]:./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Uso de la extensión de VM Docker desde la interfaz de la línea de comandos de Azure (CLI de Azure)]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[agente Linux de Azure]:../agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md

[Ejecución de Docker con https]:http://docs.docker.com/articles/https/
[Guía del usuario de Docker]:https://docs.docker.com/userguide/
