---
title: "Clúster de Azure Kubernetes para Windows | Microsoft Docs"
description: "Implementación y primeros pasos para un clúster de Kubernetes para contenedores de Windows en Azure Container Service"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2017
ms.author: dlepow
translationtype: Human Translation
ms.sourcegitcommit: 31aaa122bfca5140dcd22d2a2233c46cd28f27b9
ms.openlocfilehash: c139fc34d15545ce6a7a91842a3ebdff7c029a01
ms.lasthandoff: 02/23/2017


---

# <a name="get-started-with-windows-containers-in-a-kubernetes-cluster"></a>Introducción a los contenedores de Windows en un clúster de Kubernetes


En este artículo se muestra cómo crear un clúster de Kubernetes en Azure Container Service que contenga nodos de Windows para ejecutar contenedores de Windows. 

> [!NOTE]
> La compatibilidad de contenedores de Windows con Kubernetes en Azure Container Service está en versión preliminar. Use Azure Portal o una plantilla de Resource Manager para crear un clúster de Kubernetes con nodos de Windows. Esta característica no se admite actualmente con la CLI de Azure 2.0.



En la imagen siguiente se muestra la arquitectura de un clúster de Kubernetes en Azure Container Service con un nodo maestro de Linux y dos nodos agentes de Windows. 

* El maestro de Linux atiende a la API de REST de Kubernetes y es accesible mediante SSH en el puerto 22 o `kubectl` en el puerto 443. 
* Los nodos agentes de Windows están agrupados en un conjunto de disponibilidad de Azure y ejecutan los contenedores. Se puede acceder a los nodos de Windows por un túnel SSH de RDP a través del nodo maestro. Las reglas de equilibrio de carga de Azure se agregan dinámicamente al clúster en función de los servicios expuestos.


![Imagen del clúster de Kubernetes en Azure](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

Todas las máquinas virtuales están en la misma red privada virtual y son totalmente accesibles entre sí. Todas las máquinas virtuales ejecutan kubelet, Docker y un proxy.

## <a name="prerequisites"></a>Requisitos previos


* **Clave pública RSA de SSH**: si la implementación se realiza a través del portal o de una de las plantillas de inicio rápido de Azure, es preciso especificar la clave pública RSA de SSH para la autenticación con máquinas virtuales de Azure Container Service. Para crear claves RSA de SSH (Secure Shell), consulte las instrucciones de [OS X y Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) o de [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). 

* **Identificador y secreto de cliente de la entidad de servicio**: para más información e instrucciones, consulte [Acerca de la entidad de servicio para un clúster de Kubernetes](container-service-kubernetes-service-principal.md).




## <a name="create-the-cluster"></a>Creación de clústeres

Puede usar Azure Portal para [crear un clúster de Kubernetes](container-service-deployment.md#create-a-cluster-by-using-the-azure-portal) con nodos agentes de Windows. Al crear el clúster, tenga en cuenta las siguientes opciones:

* En la hoja **Aspectos básicos**, en **Orquestador**, seleccione **Kubernetes**. 
* En la hoja **Master configuration** (Configuración maestra), especifique las credenciales de usuario y de la entidad de servicio de los nodos maestros de Linux.
* En la hoja **Configuración del Agente**, en **Sistema operativo**, seleccione **Windows (versión preliminar)**. Escriba las credenciales de administrador para los nodos agentes de Windows.

Para más información, consulte [Implementación de un clúster de Azure Container](container-service-deployment.md).

## <a name="connect-to-the-cluster"></a>Conexión al clúster

Use la herramienta de línea de comandos `kubectl` para conectarse desde su equipo local al nodo maestro del clúster de Kubernetes. Para conocer los pasos de instalación y configuración de `kubectl`, consulte [Conexión a un clúster de Azure Container Service](container-service-connect.md#connect-to-a-kubernetes-cluster). Puede usar comandos de `kubectl` para acceder a la interfaz de usuario web de Kubernetes con el fin de crear y administrar cargas de trabajo de contenedor de Windows.

## <a name="create-your-first-kubernetes-service"></a>Creación de su primer servicio Kubernetes

Después de crear el clúster y conectarse con `kubectl`, puede intentar iniciar una aplicación web básica de Windows y exponerla a Internet. En este ejemplo, especifica los recursos de contenedor mediante un archivo YAML y, a continuación, lo crea mediante `kubctl apply`.

1. Para ver una lista de los nodos, escriba `kubectl get nodes`. Si quiere obtener detalles completos de los nodos, escriba:  

  ```
  kubectl get nodes -o yaml
  ```

2. Cree un archivo denominado `simpleweb.yaml` y copie lo siguiente. Este archivo configura una aplicación web con la imagen del sistema operativo base Windows Server 2016 Server Core desde [Docker Hub](https://hub.docker.com/r/microsoft/windowsservercore/).  

  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: win-webserver
    labels:
      app: win-webserver
  spec:
    ports:
      # the port that this service should serve on
    - port: 80
      targetPort: 80
    selector:
      app: win-webserver
    type: LoadBalancer
  ---
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    labels:
      app: win-webserver
    name: win-webserver
  spec:
    replicas: 1
    template:
      metadata:
        labels:
          app: win-webserver
        name: win-webserver
      spec:
        containers:
        - name: windowswebserver
          image: microsoft/windowsservercore
          command:
          - powershell.exe
          - -command
          - "<#code used from https://gist.github.com/wagnerandrade/5424431#> ; $$ip = (Get-NetIPAddress | where {$$_.IPAddress -Like '*.*.*.*'})[0].IPAddress ; $$url = 'http://'+$$ip+':80/' ; $$listener = New-Object System.Net.HttpListener ; $$listener.Prefixes.Add($$url) ; $$listener.Start() ; $$callerCounts = @{} ; Write-Host('Listening at {0}...' -f $$url) ; while ($$listener.IsListening) { ;$$context = $$listener.GetContext() ;$$requestUrl = $$context.Request.Url ;$$clientIP = $$context.Request.RemoteEndPoint.Address ;$$response = $$context.Response ;Write-Host '' ;Write-Host('> {0}' -f $$requestUrl) ;  ;$$count = 1 ;$$k=$$callerCounts.Get_Item($$clientIP) ;if ($$k -ne $$null) { $$count += $$k } ;$$callerCounts.Set_Item($$clientIP, $$count) ;$$header='<html><body><H1>Windows Container Web Server</H1>' ;$$callerCountsString='' ;$$callerCounts.Keys | % { $$callerCountsString+='<p>IP {0} callerCount {1} ' -f $$_,$$callerCounts.Item($$_) } ;$$footer='</body></html>' ;$$content='{0}{1}{2}' -f $$header,$$callerCountsString,$$footer ;Write-Output $$content ;$$buffer = [System.Text.Encoding]::UTF8.GetBytes($$content) ;$$response.ContentLength64 = $$buffer.Length ;$$response.OutputStream.Write($$buffer, 0, $$buffer.Length) ;$$response.Close() ;$$responseStatus = $$response.StatusCode ;Write-Host('< {0}' -f $$responseStatus)  } ; "
        nodeSelector:
          beta.kubernetes.io/os: windows
  ```

3. Para iniciar la aplicación, escriba:

  ```
  kubectl apply -f simpleweb.yaml
  ```
  
  > [!NOTE] 
  > La configuración incluye `type: LoadBalancer`. Esta configuración hace que el servicio se exponga a Internet mediante un equilibrador de carga de Azure. Para más información, consulte [Equilibrio de carga de contenedores en un clúster de Kubernetes en Azure Container Service](container-service-kubernetes-load-balancing.md).
  
4. Para comprobar la implementación del servicio (que tarda unos 30 segundos), escriba:

  ```
  kubectl get pods
  ```

5. Una vez que se está ejecutando el servicio, para ver las direcciones IP internas y externas del servicio, escriba:

  ```
  kubectl get svc
  ``` 

  ![Direcciones IP del servicio de Windows](media/container-service-kubernetes-windows-walkthrough/externalipa.png)

  La adición de la dirección IP externa puede tardar varios minutos. Antes de que el equilibrador de carga configure la dirección externa, aparece como `<pending>`.


6. Después de que la dirección IP externa está disponible, puede establecer comunicación con el servicio en el explorador web.

  ![Aplicación de servidor de Windows en el explorador](media/container-service-kubernetes-windows-walkthrough/wincontainerwebserver.png)


## <a name="access-the-windows-nodes"></a>Acceso a los nodos de Windows
Se puede acceder a los nodos de Windows desde un equipo local de Windows mediante Conexión a Escritorio remoto. Se recomienda usar un túnel SSH de RDP a través del nodo maestro. 

Existen varias opciones para crear túneles de SSH en Windows. En esta sección se describe cómo usar PuTTY para crear el túnel.

1. [Descargue PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) en el sistema Windows.

2. Ejecute la aplicación.

3. Escriba un nombre de host que conste del nombre de usuario del administrador del clúster y el nombre DNS público del primer maestro del clúster. El **nombre de host** es similar a `adminuser@PublicDNSName`. Escriba 22 en el campo **Puerto**.

    ![Configuración 1 de PuTTY](media/container-service-kubernetes-windows-walkthrough/putty1.png)

4. Seleccione **SSH > Auth** (SSL > Aut.). Agregue una ruta de acceso al archivo de claves privadas (en formato. ppk) para la autenticación. Puede usar una herramienta como [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) para generar este archivo a partir de la clave SSH utilizada para crear el clúster.

    ![Configuración 2 de PuTTY](media/container-service-kubernetes-windows-walkthrough/putty2.png)

5. Seleccione **SSH > Tunnels** (SSH > Túneles) y configure los siguientes puertos reenviados: Puesto que la máquina local de Windows ya usa el puerto 3389, se recomienda utilizar las siguientes opciones para llegar a los nodos 0 y 1 de Windows. (Siga este patrón con otros nodos de Windows).

  **Nodo de Windows 0**

  * **Puerto de origen:** 3390
  * **Destino:** 10.240.245.5:3389

  **Nodo de Windows 1**

  * **Puerto de origen:** 3391
  * **Destino:** 10.240.245.6:3389

  ![Imagen de túneles RDP de Windows](media/container-service-kubernetes-windows-walkthrough/rdptunnels.png)

6. Cuando haya terminado, haga clic en **Session > Save** (Sesión > Guardar) para guardar la configuración de la conexión.

7. Para conectarse a la sesión de PuTTY, haga clic en **Open** (Abrir). Finalice la conexión al nodo maestro.

8. Inicie Conexión a Escritorio remoto. Para conectar con el primer nodo de Windows, en **Equipo**, especifique `localhost:3390`y haga clic en **Conectar**. (Para conectarse al segundo, especifique `localhost:3390`, y así sucesivamente.) Para finalizar la conexión, proporcione la contraseña de administrador local de Windows configurada durante la implementación.








## <a name="next-steps"></a>Pasos siguientes

A continuación, se recomiendan vínculos para aprender más acerca de Kubernetes:

* [Campo de entrenamiento de Kubernetes](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html): muestra cómo implementar, escalar, actualizar y depurar aplicaciones en contenedores.
* [Guía del usuario de Kubernetes](http://kubernetes.io/docs/user-guide/): proporciona información sobre cómo ejecutar programas en un clúster de Kubernetes existente.
* [Ejemplos de Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples): proporciona ejemplos de cómo ejecutar aplicaciones reales con Kubernetes.
