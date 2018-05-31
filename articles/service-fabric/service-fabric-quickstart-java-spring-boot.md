---
title: Implementación de una aplicación de Spring Boot en Azure Service Fabric | Microsoft Docs
description: En esta guía de inicio rápido, implementará una aplicación de Spring Boot para Azure Service Fabric con la aplicación de ejemplo Spring Boot.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: 6c84b60018ec03b7f9bc572db9181b8a47a0c595
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365413"
---
# <a name="quickstart-deploy-a-java-spring-boot-application-to-azure"></a>Inicio rápido: Implementación de una aplicación de Spring Boot con Java en Azure
Azure Service Fabric es una plataforma de sistemas distribuidos para implementar y administrar microservicios y contenedores. 

En esta guía de inicio rápido se muestra cómo implementar una aplicación de Spring Boot en Service Fabric con una máquina de desarrollador Mac o Linux. Este tutorial rápido se usa el ejemplo [Getting Started](https://spring.io/guides/gs/spring-boot/) del sitio web de Spring. Al utilizar las conocidas herramientas de línea de comandos, esta guía de inicio rápido le guía a través de la implementación del ejemplo de Spring Boot como una aplicación de Service Fabric. Cuando haya terminado, tendrá el ejemplo Getting Started de Spring Boot funcionando en Service Fabric. 

![Captura de pantalla de la aplicación](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

En esta guía de inicio rápido, ha aprendido a hacer lo siguiente:

* Implementación de una aplicación de Spring Boot en Service Fabric
* Implementar la aplicación en el clúster local 
* Implementar la aplicación en un clúster en Azure
* Escalar horizontalmente la aplicación en varios nodos
* Realización de la conmutación por error del servicio sin impacto en la disponibilidad

## <a name="prerequisites"></a>requisitos previos
Para completar esta guía de inicio rápido:
1. Instalación del SDK de Service Fabric y la interfaz de la línea de comandos (CLI) de Service Fabric

    a. [Mac](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cli#cli-mac)
    
    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)

2. [Instalación de Git](https://git-scm.com/)
3. Instalación de Yeoman

    a. [Mac](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)

    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
4. Configuración del entorno de Java

    a. [Mac](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
    
    b.  [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Descarga del ejemplo
En una ventana de terminal, ejecute el comando siguiente para clonar la aplicación de ejemplo Getting Started de Spring Boot en la máquina local.
```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Creación de una aplicación Spring Boot 
1. En el directorio `gs-spring-boot/complete`, ejecute el comando siguiente para compilar la aplicación 

    ```bash
    ./gradlew build
    ``` 

## <a name="package-the-spring-boot-application"></a>Empaquetado de una aplicación de Spring Boot 
1. En el directorio `gs-spring-boot` del clon, ejecute el comando `yo azuresfguest`. 

2. Escriba la siguiente información para cada mensaje. 

    ![Entradas de Yeoman](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. En la carpeta `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code`, cree un archivo llamado `entryPoint.sh`. Agregue al archivo `entryPoint.sh` lo siguiente. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

4. Adición del recurso **Puntos de conexión** en el archivo `gs-spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml`

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    El archivo **ServiceManifest.xml** ahora tiene este aspecto: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

En este punto, ha creado una aplicación de Service Fabric del ejemplo Getting Started de Spring Boot que se puede implementar en Service Fabric.

## <a name="run-the-application-locally"></a>Ejecución de la aplicación de forma local
1. Inicie el clúster local en máquinas Ubuntu mediante la ejecución del siguiente comando:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Si utiliza un equipo Mac, inicie el clúster local desde la imagen del Docker (suponiendo que ha seguido los [requisitos previos ](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-mac#create-a-local-container-and-set-up-service-fabric) para configurar el clúster local para Mac). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    El inicio del clúster local tarda algún tiempo. Para confirmar que el clúster está totalmente operativo, acceda a Service Fabric Explorer en **http://localhost:19080**. Los cinco nodos con estado correcto indican que el clúster local está en funcionamiento. 
    
    ![Estado correcto del clúster local](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

2. Vaya a la carpeta `gs-spring-boot/SpringServiceFabric`.
3. Ejecute el comando siguiente para conectarse a su clúster local. 

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. Ejecute el script `install.sh`. 

    ```bash
    ./install.sh
    ```

5. Abra el explorador web que prefiera y acceda a la aplicación a través de **http://localhost:8080**. 

    ![Front-end local de la aplicación](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)
    
Ahora puede acceder a la aplicación de Spring Boot que se implementó en un clúster de Service Fabric.  

## <a name="deploy-the-application-to-azure"></a>Implementación de la aplicación en Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Configuración del clúster de Service Fabric
Para implementar la aplicación en un clúster de Azure, cree su propio clúster.

Los Party Cluster son clústeres de Service Fabric gratuitos, de duración limitada, hospedados en Azure y operados por el equipo de Service Fabric. Puede usar un Party Cluster para implementar aplicaciones y aprender todo lo relacionado con la plataforma. El clúster usa un único certificado autofirmado para la seguridad de nodo a nodo y de cliente a nodo.

Inicie sesión y [únase a un clúster de Linux](http://aka.ms/tryservicefabric). Descargue los certificados PFX en el equipo. Para ello, haga clic en el vínculo **PFX**. Haga clic en el vínculo **Léame** para buscar la contraseña del certificado e instrucciones sobre cómo configurar varios entornos para el certificado. Conserve las páginas **Bienvenida** y **Léame** abiertas; se usarán algunas de las instrucciones en los pasos siguientes. 

> [!Note]
> Hay un número limitado de clústeres Party Cluster disponibles por hora. Si se produce un error al intentar registrarse en un Party Cluster, puede esperar un tiempo y volver a intentarlo o puede seguir estos pasos de [Creación de un clúster de Service Fabric en Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) para crear un clúster en su suscripción. 
>
> El servicio Spring Boot está configurado para escuchar en el puerto 8080 el tráfico entrante. Asegúrese de que dicho puerto está abierto en el clúster. Si está usando el Party Cluster, el puerto estará abierto.
>

Service Fabric proporciona varias herramientas que puede usar para administrar un clúster y sus aplicaciones:

- Service Fabric Explorer, una herramienta basada en explorador.
- Interfaz de la línea de comandos (CLI) de Service Fabric, que se ejecuta sobre la CLI de Azure 2.0.
- Comandos de PowerShell. 

En esta guía de inicio rápido, se usan la CLI de Service Fabric y Service Fabric Explorer. 

Para utilizar la CLI, es preciso crear un archivo PEM basado en el archivo PFX que descargó. Para modificar el archivo, use el comando siguiente. (En el caso de los Party Cluster, puede copiar un comando específico para su archivo PFX siguiendo las instrucciones de la página **Léame**).

```bash
openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
``` 

Para usar Service Fabric Explorer, tendrá que importar el archivo PFX del certificado que descargó del sitio web de Party Cluster en el almacén de certificados (Windows o Mac) o en el explorador mismo (Ubuntu). Es necesaria la contraseña de la clave privada del archivo PFX; puede obtenerla en la página **Léame**.

Utilice el método con el que se sienta más cómodo para importar el certificado en el sistema. Por ejemplo: 

- En Windows: haga doble clic en el archivo PFX y siga las indicaciones para instalar el certificado en el almacén personal, `Certificates - Current User\Personal\Certificates`. Como alternativa, puede usar el comando de PowerShell de las instrucciones de la página **Léame**.
- En Mac: haga doble clic en el archivo PFX y siga las indicaciones para instalar el certificado en la cadena de claves.
- En Ubuntu: Mozilla Firefox es el explorador predeterminado en Ubuntu 16.04. Para importar el certificado en Firefox, haga clic en el botón de menú en la esquina superior derecha del explorador y, a continuación, haga clic en **Opciones**. En la página **Preferencias**, utilice el cuadro de búsqueda para buscar "certificados". Haga clic en **Ver certificados**, seleccione la pestaña **Sus certificados**, haga clic en **Importar** y siga las indicaciones para importar el certificado.
 
   ![Instalación del certificado en Firefox](./media/service-fabric-quickstart-java-spring-boot/install-cert-firefox.png) 


### <a name="deploy-the-application-using-cli"></a>Implementación de la aplicación con la CLI
Ahora que la aplicación y el clúster están listos, puede implementarlos en el clúster directamente desde la línea de comandos.

1. Vaya a la carpeta `gs-spring-boot/SpringServiceFabric`.
2. Ejecute el siguiente comando para conectarse al clúster de Azure. 

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. Ejecute el script `install.sh`. 

    ```bash
    ./install.sh
    ```

4. Abra un explorador web y acceda a la aplicación a través de **http://\<ConnectionIPOrUrl>:8080**. 

    ![Front-end local de la aplicación](./media/service-fabric-quickstart-java-spring-boot/springbootsfazure.png)
    
Ahora puede acceder a la aplicación de Spring Boot que se ejecuta en un clúster de Service Fabric de Azure.  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>Escalar aplicaciones y servicios en un clúster
Los servicios se pueden escalar en un clúster para adaptarse a un cambio en la carga en los servicios. Para escalar un servicio, cambie el número de instancias que se ejecutan en el clúster. Hay muchas formas de escalar los servicios; por ejemplo, se pueden usar scripts o comandos de la CLI de Service Fabric (sfctl). En los pasos siguientes, se utiliza Service Fabric Explorer.

Service Fabric Explorer se ejecuta en todos los clústeres de Service Fabric y se puede acceder a él desde un explorador. Para ello, vaya al puerto de administración HTTP de clústeres (19080); por ejemplo, `http://localhost:19080`.

Para escalar el servicio front-end web, realice estas operaciones:

1. Abra Service Fabric Explorer en el clúster, por ejemplo, `http://localhost:19080`.
2. Haga clic en el botón de puntos suspensivos (tres puntos) situado junto al nodo **fabric:/SpringServiceFabric/SpringGettingStarted** en la vista de árbol y seleccione **Scale Service** (Escalar servicio).

    ![Escalar servicio de Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    Ahora puede escalar el número de instancias del servicio.

3. Cambie el número a **3** y haga clic en **Scale Service** (Escalar servicio).

    También puede escalar el servicio mediante la línea de comandos.

    ```bash 
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 3 --stateless 
    ``` 

4. Haga clic en el nodo **fabric:/SpringServiceFabric/SpringGettingStarted** en la vista de árbol y expanda el nodo de partición (representado por un GUID).

    ![Escalar servicio de Service Fabric Explorer completado](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    El servicio tiene tres instancias y la vista de árbol muestra en qué nodos se ejecutan las instancias.

Mediante esta sencilla tarea de administración, hemos duplicado los recursos disponibles para el servicio de front-end para procesar la carga de usuarios. Es importante entender que no hacen falta varias instancias de un servicio para que se ejecute de forma confiable. Si se produce un error en un servicio, Service Fabric se asegurará de que se ejecute una nueva instancia de servicio en el clúster.

## <a name="fail-over-services-in-a-cluster"></a>Servicios de conmutación por error en un clúster 
Para demostrar la conmutación por error del servicio, se simula un reinicio del nodo mediante Service Fabric Explorer. Asegúrese de que solo se esté ejecutando una instancia del servicio. 

1. Abra Service Fabric Explorer en el clúster, por ejemplo, `http://localhost:19080`.
2. Haga clic en el botón de puntos suspensivos (tres puntos) junto al nodo que ejecuta la instancia del servicio y reinicie el nodo. 

    ![Reinicio del nodo de Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
3. La instancia del servicio se moverá a otro nodo y la aplicación no tendrá ningún tiempo de inactividad. 

    ![Reinicio del nodo de Service Fabric Explorer completado](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido a hacer lo siguiente:

* Implementación de una aplicación de Spring Boot en Service Fabric
* Implementar la aplicación en el clúster local 
* Implementar la aplicación en un clúster en Azure
* Escalar horizontalmente la aplicación en varios nodos
* Realización de la conmutación por error del servicio sin impacto en la disponibilidad

Para más información sobre cómo trabajar con aplicaciones Java en Service Fabric, diríjase al tutorial de las aplicaciones Java.

> [!div class="nextstepaction"]
> [Implementación de una aplicación Java](./service-fabric-tutorial-create-java-app.md)
