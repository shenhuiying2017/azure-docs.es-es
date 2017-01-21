---
title: "Conexión a un clúster de Azure Container Service | Microsoft Docs"
description: "Conexión a un clúster del servicio Contenedor de Azure mediante un túnel de SSH."
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenedores, microservicios, DC/OS, Azure
ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: bcc2d3468c8a560105aa2c2feb0d969ec3cccdcb
ms.openlocfilehash: 5296586b9266f432042f847f4dff9e6ff62ebc8b


---
# <a name="connect-to-an-azure-container-service-cluster"></a>Conexión a un clúster del servicio Contenedor de Azure
Los clústeres de DC/OS, Kubernetes y Docker Swarm que implementa Azure Container Service exponen los puntos de conexión de REST.  Para Kubernetes, este punto de conexión se expone de forma segura en Internet y puede acceder a él directamente desde cualquier máquina conectada a Internet. Para DC/OS y Docker Swarm, debe crear un túnel SSH con el fin de conectarse de forma segura al punto de conexión REST. A continuación, se describe cada una de estas conexiones.

> [!NOTE]
> La compatibilidad con Kubernetes en Azure Container Service está actualmente en versión preliminar.
>

## <a name="connecting-to-a-kubernetes-cluster"></a>Conéctese a un clúster de Kubernetes.
Para conectarse a un clúster de Kubernetes, debe tener la herramienta de línea de comandos `kubectl` instalada.  La manera más fácil de instalar esta herramienta es usar la herramienta de línea de comandos `az` de Azure 2.0.

```console
az acs kubernetes install cli [--install-location=/some/directory]
```

Como alternativa, puede descargar el cliente directamente desde la [página de versiones](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md#downloads-for-v146).

Una vez que tenga `kubectl` instalado, debe copiar las credenciales del clúster en la máquina.  La manera más fácil de hacerlo vuelve a ser la herramienta de línea de comandos `az`:

```console
az acs kubernetes get-credentials --dns-prefix=<some-prefix> --location=<some-location>
```

Esto descargará las credenciales del clúster en `$HOME/.kube/config` donde `kubectl` espera que se encuentre.

Como alternativa, puede usar `scp` para copiar de forma segura el archivo de `$HOME/.kube/config` en la máquina virtual principal en el equipo local.

```console
mkdir $HOME/.kube/config
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

Si se encuentra en Windows debe usar Bash en Ubuntu en Windows o la herramienta 'pscp' de Putty.

Una vez que haya configurado `kubectl`, puede probarlo con una lista de los nodos del clúster:

```console
kubectl get nodes
```

Por último, puede visualizar el panel de Kubernetes. En primer lugar, ejecute:

```console
kubectl proxy
```

La interfaz de usuario de Kubernetes está disponible ahora en: http://localhost: 8001/interfaz de usuario

Para más instrucciones, puede ver la guía de [inicio rápido de Kubernetes](http://kubernetes.io/docs/user-guide/quick-start/).

## <a name="connecting-to-a-dcos-or-swarm-cluster"></a>Conexión a un clúster de DC/OS o Swarm

Los clústeres de DC/OS y Docker Swarm que implementa el servicio Contenedor de Azure exponen los puntos de conexión REST. Sin embargo, estos puntos de conexión no están abiertos al mundo exterior. Para administrar dichos puntos de conexión, es preciso crear un túnel de Secure Shell (SSH). Después de que se haya establecido un túnel SSH, puede ejecutar comandos contra los puntos de conexión y ver la interfaz de usuario del clúster a través de un explorador en su propio sistema. Este documento le guía en la creación de un túnel de SSH en Linux, OSX y Windows.

> [!NOTE]
> Puede crear una sesión de SSH con un sistema de administración de clústeres. Sin embargo, no es aconsejable. Si se trabaja directamente en un sistema de administración, es preciso asumir el riesgo de que se produzcan cambios involuntarios en la configuración.   
> 
> 

## <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>Creación de un túnel de SSH en Linux u OS X
Lo primero que se hace al crear un túnel de SSH en Linux u OS X es buscar el nombre DNS público de los patrones de carga equilibrada. Para ello, expanda el grupo de recursos de forma que se muestren todos los recursos. Busque y seleccione la dirección IP pública del patrón. Se abrirá una hoja que contiene información acerca de la dirección IP pública, que incluye el nombre DNS. Guarde este nombre para usarlo más adelante. <br />

![Nombre DNS público](media/pubdns.png)

Ahora, abra un shell y ejecute el siguiente comando, donde:

**PORT** es el puerto del punto de conexión que desea exponer. En el caso de Swarm, es el 2375. En el de DC/OS, utilice el puerto 80.  
**USERNAME** es el nombre de usuario que se especificó cuando se implementó el clúster.  
**DNSPREFIX** es el prefijo DNS que proporcionó al implementar el clúster.  
**REGION** es la región en la que está ubicado el grupo de recursos.  
**PATH_TO_PRIVATE_KEY** [OPCIONAL] es la ruta de acceso a la clave privada correspondiente a la clave pública que proporcionó al crear el clúster de Container Service. Utilice esta opción con la marca -i.

```bash
ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```
> El puerto de conexión SSH es el 2200 y no el puerto 22 estándar.
> 
> 

## <a name="dcos-tunnel"></a>Túnel de DC/OS
Para abrir un túnel a los puntos de conexión relacionados con DC/OS, ejecute un comando similar al siguiente:

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Ya puede acceder a los puntos de conexión relacionados con DC/OS en:

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

De igual forma, se puede acceder a las API de REST de cada aplicación a través de este túnel.

## <a name="swarm-tunnel"></a>Túnel de Swarm
Para abrir un túnel al punto de conexión de Swarm, ejecute un comando parecido al siguiente:

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Ahora puede establecer la variable de entorno DOCKER_HOST de la forma siguiente. Puede usar la interfaz de la línea de comandos (CLI) de Docker de la forma habitual.

```bash
export DOCKER_HOST=:2375
```

## <a name="create-an-ssh-tunnel-on-windows"></a>Creación de un túnel de SSH en Windows
Existen varias opciones para crear túneles de SSH en Windows. En este documento se describirá cómo usar PuTTY para hacerlo.

Descargue PuTTY en el sistema Windows y ejecute la aplicación.

Escriba un nombre de host que conste del nombre de usuario de administrador de clústeres y el nombre DNS público del primer patrón del clúster. El valor de **Nombre de host`adminuser@PublicDNS` se parecerá a este: **. Escriba 2200 en el campo **Puerto**.

![Configuración 1 de PuTTY](media/putty1.png)

Seleccione **SSH** y **Autenticación**. Agregue el archivo de clave privada para la autenticación.

![Configuración 2 de PuTTY](media/putty2.png)

Seleccione **Túneles** y configure los siguientes puertos reenviados:

* **Puerto de origen:** su preferencia (use 80 para DC/OS o 2375 para Swarm).
* **Destino:** use localhost:80 para DC/OS o localhost:2375 para Swarm.

En el siguiente ejemplo se configura para DC/OS, pero su aspecto sería similar para Docker Swarm.

> [!NOTE]
> El puerto 80 no debe estar en uso cuando se cree este túnel.
> 
> 

![Configuración 3 de PuTTY](media/putty3.png)

Cuando haya terminado, guarde la configuración de conexión y conecte la sesión de PuTTY. Cuando se conecte, podrá ver la configuración del puerto en el registro de eventos de PuTTY.

![Registro de eventos de PuTTY](media/putty4.png)

Cuando haya configurado el túnel para DC/OS, podrá acceder al punto de conexión relacionado en:

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

Cuando haya configurado el túnel para Docker Swarm, podrá acceder al clúster de Swarm a través de la CLI de Docker. Primero será preciso que configure una variable de entorno de Windows denominada `DOCKER_HOST` cuyo valor será ` :2375`.

## <a name="next-steps"></a>Pasos siguientes
Implemente y administre contenedores con DC/OS o Swarm:

* [Administración de contenedores con la API de REST](container-service-mesos-marathon-rest.md)
* [Administración de contenedores con Docker Swarm](container-service-docker-swarm.md)




<!--HONumber=Dec16_HO3-->


