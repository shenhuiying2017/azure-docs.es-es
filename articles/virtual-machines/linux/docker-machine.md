---
title: Uso de Docker Machine para crear hosts Linux en Azure | Microsoft Docs
description: "Describe el uso de la máquina de Docker para crear hosts de docker en Azure."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: b303510970aca957a4da5f2ed51a9125302d419a
ms.lasthandoff: 04/03/2017


---
# <a name="use-docker-machine-with-the-azure-driver"></a>Uso de una máquina de Docker con el controlador de Azure
[Docker](https://www.docker.com/) ofrece virtualización con contenedores de Linux en lugar de las máquinas virtuales para aislar los datos de la aplicación y la computación en un recurso compartido. En este tema se describe cómo y cuándo usar [Docker Machine](https://docs.docker.com/machine/). El comando `docker-machine` crea una nueva VM de Linux en Azure habilitada como un host de Docker para los contenedores de Linux.

## <a name="create-vms-with-docker-machine"></a>Creación de VM con la máquina de Docker
Cree máquinas virtuales de host de docker en Azure con el comando `docker-machine create` mediante el argumento de controlador `azure` para la opción de controlador (`-d`) y cualquier otro argumento. 

El siguiente ejemplo se basa en los valores predeterminados, pero abre el puerto 80 en la máquina virtual a Internet para probar con un contenedor nginx, hace que `ops` sea el usuario de inicio de sesión para SSH y llama a la nueva máquina virtual `machine`. 

Escriba `docker-machine create --driver azure` para ver las opciones y sus valores predeterminados; también puede leer la [documentación del controlador de Azure de Docker](https://docs.docker.com/machine/drivers/azure/). (Tenga en cuenta que si tiene habilitada la autenticación de dos fases, se le pedirá autenticarse con el segundo factor).

```bash
docker-machine create -d azure \
  --azure-ssh-user ops \
  --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> \
  --azure-open-port 80 \
  machine
```

La salida debería ser similar a lo siguiente, dependiendo de si tiene configurada la autenticación de dos fases en la cuenta.

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(machine) Microsoft Azure: To sign in, use a web browser to open the page https://aka.ms/devicelogin. Enter the code <code> to authenticate.
(machine) Completed machine pre-create checks.
Creating machine...
(machine) Querying existing resource group.  name="machine"
(machine) Creating resource group.  name="machine" location="eastus"
(machine) Configuring availability set.  name="docker-machine"
(machine) Configuring network security group.  name="machine-firewall" location="eastus"
(machine) Querying if virtual network already exists.  name="docker-machine-vnet" location="eastus"
(machine) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(machine) Creating public IP address.  name="machine-ip" static=false
(machine) Creating network interface.  name="machine-nic"
(machine) Creating storage account.  name="vhdsolksdjalkjlmgyg6" location="eastus"
(machine) Creating virtual machine.  name="machine" location="eastus" size="Standard_A2" username="ops" osImage="canonical:UbuntuServer:15.10:latest"
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env machine
```

## <a name="configure-your-docker-shell"></a>Configuración del shell de docker
Ahora, escriba `docker-machine env <VM name>` para ver lo que necesita hacer para configurar el shell. 

```bash
docker-machine env machine
```

Eso imprime la información del entorno, que es algo parecido a esto. Tenga en cuenta que la dirección IP se ha asignado, que necesitará probar la VM.

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://191.237.46.90:2376"
export DOCKER_CERT_PATH="/Users/rasquill/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env machine)
```

Puede ejecutar el comando de configuración sugerido o establecer las variables de entorno usted mismo. 

## <a name="run-a-container"></a>Ejecución de un contenedor
Ahora puede ejecutar un servidor web simple para probar si todo funciona correctamente. Aquí se usa una imagen nginx estándar, se especifica que debe escuchar en el puerto 80 y que si la VM se reinicia, el contenedor también se debe reiniciar (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

La salida debe tener un aspecto similar al siguiente:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>Prueba del contenedor
Examine los contenedores en ejecución mediante `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

Y para comprobar el contenedor en funcionamiento, escriba `docker-machine ip <VM name>` para buscar la dirección IP (si se olvidó del comando `env`):

![Ejecución del contenedor ngnix](./media/docker-machine/nginxsuccess.png)

## <a name="next-steps"></a>Pasos siguientes
Si le interesa, puede probar la [extensión de máquina virtual de Docker](dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) de Azure para realizar la misma operación mediante la CLI de Azure o las plantillas de Azure Resource Manager. 

Para ver más ejemplos de cómo trabajar con Docker, consulte [cómo trabajar con Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) en la [demostración](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) realizada en Connect 2015. Para ver más guías rápidas de la demostración de HealthClinic.biz, consulte las [guías rápidas de las herramientas de desarrollador de Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).


