<properties
   pageTitle="Creación de la primera aplicación de Service Fabric en Linux con Java | Microsoft Azure"
   description="Creación e implementación de aplicación de Service Fabric con Java"
   services="service-fabric"
   documentationCenter="java"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="seanmck"/>


# Creación de la primera aplicación de Azure Service Fabric

> [AZURE.SELECTOR]
- [C Sharp](service-fabric-create-your-first-application-in-visual-studio.md)
- [Java](service-fabric-create-your-first-linux-application-with-java.md)

Service Fabric ofrece SDK para compilar servicios en Linux tanto en .NET Core como Java. En este tutorial, veremos cómo crear una aplicación para Linux y cómo compilar un servicio con Java.

## Requisitos previos

Antes de empezar, asegúrese de [configurar el entorno de desarrollo Linux](service-fabric-get-started-linux.md). Si usa Mac OS X, puede [configurar un entorno one-box de Linux en una máquina virtual mediante Vagrant](service-fabric-get-started-mac.md).

## Creación de la aplicación

Una aplicación de Service Fabric puede contener uno o varios servicios, cada uno de ellos con un rol específico en la prestación de la funcionalidad de la aplicación. El SDK de Service Fabric para Linux incluye un generador [Yeoman](http://yeoman.io/) que permite crear fácilmente el primer servicio y agregar más posteriormente. Vamos a usar Yeoman para crear una nueva aplicación con un único servicio.

1. En un terminal, escriba **yo azuresfjava**.

2. Asigne un nombre a la aplicación.

3. Elija el tipo del primer servicio y asígnele un nombre. En este tutorial, elegiremos un servicio de actor confiable.

  ![Generador Yeoman de Service Fabric para Java][sf-yeoman]

>[AZURE.NOTE] Para más información acerca de las opciones, consulte [Información general del modelo de programación de Service Fabric](service-fabric-choose-framework.md).

## Compilar la aplicación

Las plantillas de Yeoman de Service Fabric incluyen un script de compilación para [Gradle](https://gradle.org/), que se puede usar para compilar la aplicación desde el terminal.

  ```bash
  gradle
  ```

## Implementación de la aplicación

Una vez compilada la aplicación, puede implementarla en el clúster local mediante la CLI de Azure.

1. Conéctese al clúster de Service Fabric local.

    ```bash
    azuresfcli servicefabric cluster connect
    ```

2. Use el script de instalación proporcionado en la plantilla para copiar el paquete de aplicación en el almacén de imágenes del clúster, registrar el tipo de aplicación y crear una instancia de la aplicación.

    ```bash
    cd myapp
    ./install.sh
    ```

3. Abra un explorador y vaya a Service Fabric Explorer en http://localhost:19080/Explorer (reemplace localhost por la dirección IP privada de la máquina virtual si usa Vagrant en Mac OS X).

4. Expanda el nodo Applications y observe que ahora hay una entrada para su tipo de aplicación y otra para la primera instancia de ese tipo.

## Inicio del cliente de prueba y ejecución de una conmutación por error

Los proyectos de actor no hacen nada por sí solos. Necesitan que otro servicio o cliente les envíe mensajes. La plantilla de actor incluye un sencillo script de prueba que puede usar para interactuar con el servicio de actor.

1. Ejecute el script con la utilidad de inspección para ver la salida del servicio de actor.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. En Service Fabric Explorer, busque el nodo que hospeda la réplica principal del servicio de actor. En la captura de pantalla siguiente, es el nodo 3.

    ![Búsqueda de la réplica principal en Service Fabric Explorer][sfx-primary]

3. Haga clic en el nodo que encontró en el paso anterior y seleccione **Desactivar (reiniciar)** en el menú Acciones. Esto reiniciará uno de los cinco nodos del clúster local y forzará una conmutación por error a una de las réplicas secundarias que se ejecutan en otro nodo. Al hacerlo, preste atención a la salida del cliente de prueba y tenga en cuenta que el contador sigue incrementándose a pesar de la conmutación por error.

## Compilación e implementación de una aplicación con el complemento para Eclipse Neon

Si instaló el complemento de Service Fabric para Eclipse Neon, puede usarlo para crear, compilar e implementar aplicaciones de Service Fabric compiladas con Java.

### Creación de la aplicación

El complemento de Service Fabric está disponible mediante la extensibilidad de Eclipse.

1. En Eclipse, elija **File > Other > Service Fabric** (Archivo > Otros > Service Fabric). Verá un conjunto de opciones, incluidos Actores y Contenedores.

    ![Plantillas de Service Fabric en Eclipse][sf-eclipse-templates]

2. En este caso, elija Stateless Service (Servicio sin estado).

3. Se le pedirá que confirme el uso de la perspectiva de Service Fabric, que optimiza Eclipse para usarlos con proyectos de Service Fabric. Elija “Yes” (Sí).

### Implementación de la aplicación

Las plantillas de Service Fabric incluyen un conjunto de tareas de Gradle para compilar e implementar aplicaciones, que se pueden desencadenar mediante Eclipse.

1. Elija **Run > Run Configurations** (Ejecutar > Ejecutar configuraciones).

2. Expanda **Gradle Project** (Proyecto Gradle) y elija **ServiceFabricDeployer**.

3. Haga clic en **Ejecutar**.

La aplicación se compila e implementa en un momento. Puede supervisar su estado en Service Fabric Explorer.

## Pasos siguientes

- [Más información acerca de Reliable Actors](service-fabric-reliable-actors-introduction.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

<!---HONumber=AcomDC_0928_2016-->