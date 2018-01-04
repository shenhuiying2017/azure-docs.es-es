---
title: "Información sobre la terminología de Azure Service Fabric | Microsoft Docs"
description: "Descripción sobre la terminología de Service Fabric. Describe la terminología y los conceptos clave que se emplean en el resto de la documentación."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: chackdan;subramar
ms.assetid: 3a970679-e19e-43b3-9be8-71773f307c57
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/18/2017
ms.author: ryanwi
ms.openlocfilehash: dc7e536ce40bf95e1950e1e44844cd8fe26ea1a1
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="service-fabric-terminology-overview"></a>Información general sobre la terminología de Service Fabric
Azure Service Fabric es una plataforma de sistemas distribuidos que facilita el empaquetamiento, la implementación y la administración de microservicios escalables y confiables. En este artículo se describe la terminología que se usa en Service Fabric para comprender los términos que se utilizan en la documentación.

Los conceptos descritos en esta sección también se explican en los siguientes vídeos de Microsoft Virtual Academy: <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">Core concepts</a> (Conceptos principales), <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965">Design-time concepts</a> (Conceptos de diseño) y <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">Runtime concepts</a> (Conceptos del sistema en tiempo de ejecución).

## <a name="infrastructure-concepts"></a>Conceptos de infraestructura
**Clúster**: conjunto de máquinas físicas o virtuales conectadas a la red en las que se implementan y administran los microservicios.  Los clústeres pueden escalar a miles de equipos.

**Nodo**: cada una de las máquinas físicas o virtuales que forman parte de un clúster se denominan *nodo*. A cada nodo se le asigna un nombre de nodo (una cadena). Los nodos tienen características, como propiedades de colocación. Cada máquina física o virtual tiene un servicio de Windows de inicio automático, `FabricHost.exe`, que empieza a ejecutarse en el arranque y luego inicia dos ejecutables: `Fabric.exe` y `FabricGateway.exe`. Estos dos ejecutables conforman el nodo. En los escenarios de prueba, se pueden hospedar varios nodos en un solo equipo y máquina virtual mediante la ejecución de varias instancias de `Fabric.exe` y `FabricGateway.exe`.

## <a name="application-concepts"></a>Conceptos de las aplicaciones
**Tipo de aplicación**: nombre o versión asignados a una colección de tipos de servicios. Se define en un archivo `ApplicationManifest.xml` y se inserta en un directorio del paquete de aplicación. Después, el directorio se copia en el almacén de imágenes del clúster de Service Fabric. Después puede crear una aplicación con nombre de este tipo de aplicación en el clúster.

Para más información, consulte el artículo sobre el [modelado de una aplicación](service-fabric-application-model.md).

**Paquete de aplicación**: directorio del disco que contiene el archivo `ApplicationManifest.xml` del tipo de aplicación. Hace referencia a los paquetes de servicios de cada uno de los tipos de servicios que conforman el tipo de aplicación. Los archivos en el directorio del paquete de aplicación se copian en el almacén de imágenes del clúster de Service Fabric. Por ejemplo, un paquete de aplicación de un tipo de aplicación de correo electrónico puede contener referencias a un paquete de servicios de cola, un paquete de servicios de front-end y un paquete de servicios de base de datos.

**Aplicación con nombre**: después de copiar un paquete de aplicación en el almacén de imágenes, se crea una instancia de la aplicación en el clúster. Cree una instancia al especificar el tipo de aplicación del paquete, con el nombre o la versión. A cada instancia de tipo de aplicación se le asigna un nombre de identificador uniforme de recursos (URI) similar al siguiente: `"fabric:/MyNamedApp"`. En un clúster se pueden crear varias aplicaciones con nombre de un único tipo de aplicación. También se pueden crear aplicaciones con nombre de diferentes tipos de aplicación. Cada aplicación con nombre se administra de manera independiente y tiene versiones independientes.      

**Tipo de servicio**: nombre o versión asignados a los paquetes de código, de datos y de configuración de un servicio. El tipo de servicio se define en el archivo `ServiceManifest.xml` y se inserta en un directorio del paquete de servicio. El archivo `ApplicationManifest.xml` de un paquete de aplicación hace referencia al directorio del paquete de servicio. En el clúster, después de crear una aplicación con nombre se pude crear un servicio con nombre desde uno de los tipos de servicio del tipo de aplicación. El archivo `ServiceManifest.xml` del tipo de servicio describe el servicio.

Para más información, consulte el artículo sobre el [modelado de una aplicación](service-fabric-application-model.md).

Existen dos tipos de servicios:

* **Sin estado:** los servicios sin estado se usan cuando el estado persistente del servicio se almacena en un servicio de almacenamiento externo como Azure Storage, Azure SQL Database o Azure Cosmos DB. Utilice un servicio sin estado en el caso de que el servicio no tenga almacenamiento persistente. Por ejemplo, para un servicio de calculadora en el que se pasan valores al servicio, se realizan cálculos con dichos valores y se devuelve un resultado.
* **Con estado:** los servicios con estado se usan si desea que Service Fabric administre el estado de estos a través de sus modelos de programación Reliable Collections o Reliable Actors. Al crear un servicio con nombre, especifique el número de particiones por las que desea distribuir el estado (a efectos de escalabilidad). Especifique también cuántas veces debe replicarse el estado en los nodos (para mayor confiabilidad). Todos los servicios con nombre tienen una única réplica principal y varias réplicas secundarias. Puede modificar el estado del servicio con nombre al escribir en la réplica principal. Tras ello, Service Fabric replica este estado en todas las réplicas secundarias para mantener el estado sincronizado. Service Fabric detecta automáticamente cuándo se produce un error de una réplica principal y, como respuesta, promueve una de las réplicas secundarias existentes para que ocupe el puesto de réplica principal. Seguidamente, Service Fabric crea una nueva réplica secundaria.  

**Réplicas o instancias**: hacen referencia al código (y estado de los servicios con estado) de un servicio que se implementa y está en ejecución. Consulte [Réplicas e instancias](service-fabric-concepts-replica-lifecycle.md).

**Reconfiguración** hace referencia al proceso de cualquier cambio en el conjunto de réplicas de un servicio. Consulte [Reconfiguración](service-fabric-concepts-reconfiguration.md).

**Paquete de servicio**: directorio del disco que contiene el archivo `ServiceManifest.xml` del tipo de servicio. Este archivo hace referencia al código, a los datos estáticos y a los paquetes de configuración del tipo de servicio. El archivo `ApplicationManifest.xml` del tipo de aplicación hace referencia a los archivos del directorio del paquete de servicio. Por ejemplo, un paquete de servicio puede hacer referencia al código, a los datos estáticos y a los paquetes de configuración que conforman un servicio de base de datos.

**Servicio con nombre**: después de crear una aplicación con nombre, puede crear una instancia de uno de sus tipos de servicio en el clúster. El tipo de servicio se especifica con el nombre y la versión. A cada instancia del tipo de servicio se le asigna un nombre de URI cuyo ámbito es el URI de su aplicación con nombre. Por ejemplo, si crea un servicio con nombre "MyDatabase" en la aplicación con nombre "MyNamedApp", el URI será similar al siguiente: `"fabric:/MyNamedApp/MyDatabase"`. En una aplicación con nombre, se pueden crear varios servicios con nombre. Todos los servicios con nombre pueden tener su propio esquema de partición y cuentas de instancia o réplica.

**Paquete de código**: directorio del disco que contiene archivos ejecutables (normalmente EXE o DLL) del tipo de servicio. El archivo `ServiceManifest.xml` del tipo de servicio hace referencia a los archivos del directorio del paquete de código. Al crear un servicio con nombre, el paquete de código se copia en los nodos seleccionados para ejecutar dicho servicio. A continuación, el código comienza a ejecutarse. Hay dos tipos de ejecutables de paquetes de código:

* **Ejecutables de invitado**: ejecutables que se ejecutan tal cual en sistema operativo host (Windows o Linux). Estos ejecutables no se vinculan ni hacen referencia a los archivos del sistema en tiempo de ejecución de Service Fabric y, por consiguiente, no usan ninguno de los modelos de programación de Service Fabric. Estos ejecutables no pueden utilizar algunas características de Service Fabric, como el servicio de nomenclatura para la detección de puntos de conexión. Los ejecutables de invitado no pueden informar de métricas de carga específicas de cada instancia del servicio.
* **Ejecutables de host de servicios**: los utilizan los modelos de programación de Service Fabric mediante la vinculación a los archivos del sistema en tiempo de ejecución de Service Fabric, lo que habilita las características de Service Fabric. Por ejemplo, una instancia del servicio con nombre puede registrar puntos de conexión con en el servicio de nombres de Service Fabric y también puede notificar métricas de carga.      

**Paquete de datos**: directorio del disco que contiene los archivos de datos de solo lectura estáticos del tipo de servicio (normalmente archivos fotográficos, de sonido y de vídeo). El archivo `ServiceManifest.xml` del tipo de servicio hace referencia a los archivos del directorio del paquete de datos. Al crear un servicio con nombre, el paquete de datos se copia en los nodos seleccionados para ejecutar dicho servicio. El código empieza a ejecutarse, tras lo cual se habilita el acceso a los archivos de datos.

**Paquete de configuración**: directorio del disco que contiene los archivos de configuración de solo lectura estáticos del tipo de servicio (normalmente archivos de texto). El archivo `ServiceManifest.xml` del tipo de servicio hace referencia a los archivos del directorio del paquete de configuración. Al crear un servicio con nombre, los archivos del paquete de configuración se copian en los nodos seleccionados para la ejecución de dicho servicio. Seguidamente, el código empieza a ejecutarse, tras lo cual se habilita el acceso a los archivos de configuración.

**Contenedores**: de forma predeterminada Service Fabric permite implementar y activar estos servicios como procesos. Service Fabric puede implementar también servicios en imágenes de contenedor. Los contenedores constituyen una tecnología de virtualización que permite virtualizar el sistema operativo subyacente de las aplicaciones. Una aplicación y sus sistema en tiempo de ejecución, dependencias y bibliotecas del sistema se ejecutan dentro de un contenedor. El contenedor tiene acceso privado total a la propia vista aislada del contenedor de las construcciones de sistema operativo. Service Fabric admite contenedores de Docker de los contenedores de Linux y Windows Server. Para obtener más información, lea el artículos sobre [Service Fabric y los contenedores](service-fabric-containers-overview.md).

**Esquema de partición**: al crear un servicio con nombre, se especifica un esquema de partición. Los servicios con importantes cantidades de estado dividen los datos entre las particiones, que distribuyen el estado entre los nodos del clúster. Al dividir los datos en varias particiones, el estado del servicio con nombre se puede escalar. Dentro de una partición, los servicios con nombre sin estado tienen instancias, mientras que los servicios con nombre con estado tienen réplicas. Normalmente, los servicios con nombre sin estado solo tienen una partición, ya que no tienen estado interno. Las instancias de la partición proporcionan disponibilidad. Si se produce un error en una instancia, las demás seguirán operando con normalidad y Service Fabric creará otra instancia. Los servicios con nombre con estado mantienen su estado en las réplicas y cada partición tiene su propio conjunto de réplicas donde todos los estados se mantienen sincronizados. Si se produce algún error en una réplica, Service Fabric creará una nueva réplica a partir de las réplicas existentes.

Para obtener más información, consulte el artículo [Partición de Reliable Services de Service Fabric](service-fabric-concepts-partitioning.md) .

## <a name="system-services"></a>Servicios del sistema
Hay servicios del sistema que se crean en cada clúster que proporcionan las funciones de la plataforma de Service Fabric.

**Naming Service**: servicio presente en cada clúster de Service Fabric, que resuelve los nombres de servicio como una ubicación en el clúster. Usted administra las propiedades y los nombres de servicio para el clúster de manera similar a un sistema de nombres de dominio (DNS). Los clientes pueden comunicarse de forma segura con cualquier nodo del clúster mediante Naming Service para resolver el nombre de un servicio y su ubicación. Las aplicaciones se mueven en el clúster. Por ejemplo, por errores, para el equilibrio de los recursos o para cambiar de tamaño el clúster. Puede desarrollar servicios y clientes que resuelvan la ubicación de red actual. Los clientes obtienen la dirección IP real de la máquina y el puerto en el que se está ejecutando.

Consulte el artículo sobre la [comunicación con los servicios](service-fabric-connect-and-communicate-with-services.md) para más información sobre las API de comunicación de clientes y servicios compatibles con Naming Service.

**Servicio Image Store**: cada clúster de Service Fabric tiene un servicio Image Store donde se guardan los paquetes de aplicación implementados y con versión. Copie un paquete de aplicación en el almacén de imágenes y luego registre el tipo de aplicación en dicho paquete. Después de aprovisionar el tipo de aplicación, cree una aplicación con nombre a partir de él. El registro de un tipo de aplicación del servicio de almacenamiento de imágenes se puede anular una vez eliminadas todas sus aplicaciones con nombre.

Lea [Descripción del valor ImageStoreConnectionString](service-fabric-image-store-connection-string.md) para obtener más información sobre el servicio del almacén de imágenes.

Para más información sobre la implementación en el servicio Image Store, consulte el artículo sobre la [implementación de una aplicación](service-fabric-deploy-remove-applications.md).

**Servicio Administrador de conmutación por error**: cada clúster de Service Fabric tiene uno, responsable de las siguientes acciones:
   - Realiza funciones relacionadas con la alta disponibilidad y la coherencia de los servicios.
   - Orquesta las actualizaciones de la aplicación y el clúster.
   - Interactúa con otros componentes del sistema.

## <a name="built-in-programming-models"></a>Modelos de programación integrados
Hay modelos de programación de .NET Framework disponibles para generar servicios de Service Fabric:

**Reliable Services**: una API para generar servicios con y sin estado. Los servicios con estado almacenan su estado en colecciones confiables, como un diccionario o una cola. También permiten conectar una amplia variedad de pilas de comunicación, como la API web y Windows Communication Foundation (WCF).

**Reliable Actors**: una API para crear objetos con y sin estado a través del modelo de programación de actor virtual. Este modelo resulta útil cuando hay muchas unidades independientes de cálculo o estado. Este modelo utiliza un modelo de subprocesos basado en turnos, por lo que es conveniente evitar usar código que llame a otros actores o servicios, ya que un actor individual no puede procesar otras solicitudes entrantes hasta que terminen todas sus solicitudes salientes.

Para más información, consulte el artículo de [elección de un modelo de programación para el servicio](service-fabric-choose-framework.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>pasos siguientes
Para más información acerca de Service Fabric:

* [Información general de Service Fabric](service-fabric-overview.md)
* [¿Por qué usar un enfoque de microservicios para crear aplicaciones?](service-fabric-overview-microservices.md)
* [Escenarios de aplicación](service-fabric-application-scenarios.md)


