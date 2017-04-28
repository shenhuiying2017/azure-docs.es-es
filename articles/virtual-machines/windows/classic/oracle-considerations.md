---
title: "Consideraciones sobre el uso de imágenes de máquina virtual de Oracle | Microsoft Docs"
description: "Obtenga información acerca de las configuraciones y las limitaciones admitidas de una máquina virtual de Oracle en Windows Server en Azure antes de efectuar la implementación."
services: virtual-machines-windows
documentationcenter: 
manager: timlt
author: rickstercdn
tags: azure-service-management
ms.assetid: 5d71886b-463a-43ae-b61f-35c6fc9bae25
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 1a808a83a8ed1162d57f7c5a49e34b2e3be50833
ms.lasthandoff: 04/12/2017


---
# <a name="miscellaneous-considerations-for-oracle-virtual-machine-images"></a>Consideraciones variadas sobre las imágenes de máquina virtual de Oracle
En este artículo se tratan las consideraciones que se deben tener con las máquinas virtuales de Oracle en Azure, que se basan en imágenes de software de Oracle proporcionadas por Oracle.  

* Imágenes de máquina virtual de Oracle Database
* Imágenes de máquina virtual de Oracle WebLogic Server
* Imágenes de máquina virtual de Oracle JDK

## <a name="oracle-database-virtual-machine-images"></a>Imágenes de máquina virtual de Oracle Database

### <a name="no-static-internal-ip"></a>Ninguna dirección IP interna estática
Azure asigna una dirección IP interna a cada máquina virtual. A menos que la máquina virtual forma parte de una red virtual, la dirección IP de la máquina virtual es dinámica y puede cambiar después de reiniciar la máquina virtual. Esto puede causar problemas porque la base de datos de Oracle espera que la dirección IP sea estática. Para evitar el problema, piense en agregar la máquina virtual a una red virtual de Azure. Consulte [Red virtual](https://azure.microsoft.com/documentation/services/virtual-network/) y [Creación de una red virtual en Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md) para más información.

### <a name="attached-disk-configuration-options"></a>Opciones de configuración de discos conectados

Los discos conectados se basan en el servicio de almacenamiento de blobs de Azure. Teóricamente, cada disco estándar puede realizar aproximadamente 500 operaciones de entrada/salida por segundo (IOPS). Nuestra oferta de disco premium es preferible para las cargas de trabajo de base de datos de alto rendimiento y puede alcanzar las 5.000 IOPS disco. Aunque se puede usar un disco individual, siempre que cubra las necesidades de rendimiento, se puede mejorar el rendimiento de IOPS efectivo si se usan varios discos conectados, repartir los datos de la base de datos a través de ellos y, a continuación, usar Automatic Storage Management (ASM) de Oracle. Consulte [Información general de almacenamiento automático de Oracle](http://www.oracle.com/technetwork/database/index-100339.html) para obtener más información. Aunque se puede usar el seccionamiento de varios discos en el nivel de sistema operativo, hay compensaciones que realizan mediante cualquiera de estas rutas. 

Considere dos enfoques diferentes para la conexión de varios discos, en función de si desea establecer prioridades en el rendimiento de las operaciones de lectura o escritura para la base de datos:

* **ASM de Oracle en sí** es probable que permita obtener un mejor rendimiento de la operación de escritura, pero un peor IOPS para las operaciones de lectura en comparación con el enfoque consistente en usar matrices de discos. En la ilustración siguiente se describe de manera lógica esta disposición.  
    ![](media/mysql-2008r2/image2.png)

> [!IMPORTANT]
> Evalúe el equilibrio entre el rendimiento de escritura y de lectura en función del caso. Los resultados reales pueden variar, así que realice una comprobación apropiada. ASM favorece las operaciones de escritura, el seccionamiento del disco del sistema operativo favorece las operaciones de lectura.
> 

### <a name="clustering-rac-is-not-supported"></a>No se admite la agrupación en clústeres (RAC)
Oracle Real Application Clusters (RAC) se ha diseñado para mitigar el error de un nodo individual en una configuración local de clúster de varios nodos.  Se basa en dos tecnologías locales que no funcionan en entornos de nube pública de hiperescala como Microsoft Azure: multidifusión y disco compartido de red. Si desea diseñar una configuración de varios nodos con redundancia geográfica de Oracle DB, será preciso que implemente la replicación de datos con Oracle DataGuard.

### <a name="high-availability-and-disaster-recovery-considerations"></a>Consideraciones sobre la alta disponibilidad y la recuperación ante desastres
Cuando se usa la Base de datos de Oracle en máquinas virtuales de Azure, usted es responsable de implementar una solución de recuperación ante desastres y disponibilidad alta para evitar los tiempos de inactividad. También es responsable de la copia de seguridad de sus propios datos y la aplicación.

La alta disponibilidad y recuperación ante desastres para Oracle Database Enterprise Edition (sin RAC) en Azure puede lograrse mediante [Protección de datos, Protección de datos activa](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html) u [Oracle Golden Gate](http://www.oracle.com/technetwork/middleware/goldengate), con dos bases de datos en dos máquinas virtuales independientes. Ambas máquinas virtuales deben estar en la misma [red virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para asegurarse de que pueden acceder entre sí a través de la dirección IP privada persistente.  Además, recomendamos colocar las máquinas virtuales en el mismo [conjunto de disponibilidad](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para permitir a Azure colocarlas en dominios de error y de actualización independientes. Cada máquina virtual debe tener al menos 2 GB de memoria y 5 GB de espacio en disco.

Con la Protección de datos de Oracle, se puede lograr alta disponibilidad con una base de datos principal en una máquina virtual, una base de datos secundaria (de reserva) en otra máquina virtual y la replicación unidireccional establecida entre ellos. El resultado es un acceso de lectura a la copia de la base de datos. Con Oracle GoldenGate, puede configurar la replicación bidireccional entre las dos bases de datos. Para más información sobre cómo configurar una solución de alta disponibilidad para bases de datos mediante estas herramientas, consulte la documentación de [Protección de datos activa](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) y [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) en el sitio web de Oracle. Si se necesita acceso de lectura-escritura a la copia de la base de datos, puede usar [Protección de datos activa de Oracle](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Imágenes de máquina virtual de Oracle WebLogic Server
* **La agrupación en clústeres solo se admite en Enterprise Edition.** Tiene licencia para usar la agrupación en clústeres de WebLogic solo cuando utiliza la versión Enterprise Edition de WebLogic Server. No use la agrupación en clústeres con WebLogic Server Standard Edition.
* **Tiempos de espera de conexión** : si la aplicación se basa en las conexiones a puntos de conexión públicos de otro servicio en la nube de Azure (por ejemplo, un servicio de nivel de base de datos), Azure puede cerrar estas conexiones abiertas después de 4 minutos de inactividad. Esto puede afectar a las características y las aplicaciones basadas en grupos de conexiones, ya que es posible que las conexiones que están inactivas durante un período superior a ese límite no sigan siendo válidas. Si esto afecta a la aplicación, considere la posibilidad de habilitar la lógica "keep-alive" en sus grupos de conexiones.
  
   Si un punto de conexión es *interno* a su implementación del servicio en la nube de Azure (como una máquina virtual de base de datos independiente situada dentro del *mismo* servicio en la nube que las máquinas virtuales WebLogic), la conexión es directa, no se basa en Azure Load Balancer y, por tanto, no está sujeta a un tiempo de espera de conexión.
* **Tampoco se admite la multidifusión UDP.** Azure admite la unidifusión UDP, pero no admite la multidifusión ni la difusión. WebLogic Server es capaz de depender de las capacidades de unidifusión UDP de Azure. Para obtener mejores resultados dependiendo de la unidifusión UDP, recomendamos que el tamaño del clúster WebLogic se mantenga estático o con no más de 10 servidores administrados incluidos en el clúster.
* **WebLogic Server espera que los puertos públicos y privados sean los mismos para el acceso de T3 (por ejemplo, al usar Enterprise JavaBeans).** Considere un escenario de varios niveles, en el que una aplicación de capa de servicio (EJB) se esté ejecutando en un clúster de servidor WebLogic compuesto por dos o más servidores administrados, en un servicio en la nube denominado **SLWLS**. El nivel de cliente está en un servicio de nube diferente, ejecutando un programa Java simple que intenta llamar a EJB en el nivel de servicio. Dado que es necesario equilibrar la carga de la capa de servicio, debe crearse un extremo público con equilibrio de carga para las máquinas virtuales en el clúster de WebLogic Server. Si el puerto privado que especifique para dicho extremo es diferente del puerto público (por ejemplo, 7006:7008), se producirá un error como el siguiente:
  
       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:
  
       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]
  
   Esto se debe a que para cualquier acceso remoto T3, WebLogic Server espera que el puerto con equilibrador de carga y el puerto del servidor WebLogic administrado sean el mismo. En el caso anterior, el cliente tiene acceso al puerto 7006 (el puerto del equilibrador de carga) y el servidor administrado está escuchando en 7008 (el puerto privado). Esta restricción se aplica solo al acceso T3, no a HTTP.
  
   Para evitar este problema, use una de las siguientes soluciones:
  
  * Use los mismos números de puerto públicos y privados para los extremos de equilibrio de carga dedicados al acceso a T3.
  * Incluya el siguiente parámetro JVM al iniciar WebLogic Server:
    
         -Dweblogic.rjvm.enableprotocolswitch=true

Para obtener información relacionada, consulte el artículo de KB **860340.1** en <http://support.oracle.com>.

* **Agrupación en clústeres dinámica y limitaciones de equilibrio de carga.** Supongamos que desea usar un clúster dinámico en WebLogic Server y exponerlo a través de un único extremo con equilibrio de carga público en Azure. Esto puede hacerse siempre que use un número de puerto fijo para cada uno de los servidores administrados (no asignados dinámicamente a partir un rango) y no inicie más servidores administrados que máquinas esté siguiendo el administrador (es decir, que no haya más de un servidor administrado por máquina virtual). Si la configuración provoca que se inicien más servidores WebLogic que máquinas virtuales (es decir, donde varias instancias de WebLogic Server compartirán la misma máquina virtual), entonces no será posible para más de uno de esos servidores de instancias de WebLogic Server establecer un enlace a un número de puerto determinado: los demás de esa máquina virtual generarán un error.
  
   Por otro lado, si configura el servidor de administración para asignar automáticamente números de puerto exclusivos para los servidores administrados, el equilibrio de carga no será posible porque Azure no admite la asignación de un único puerto público a varios puertos privados, como sería necesario para esta configuración.
* **Varias instancias de Weblogic Server en una máquina virtual.** Dependiendo de los requisitos de su implementación, puede considerar la opción de ejecutar varias instancias de WebLogic Server en la misma máquina virtual, si la máquina virtual es suficientemente grande. Por ejemplo, en un máquina virtual de tamaño medio que contiene dos núcleos, puede elegir ejecutar dos instancias de WebLogic Server. Sin embargo, tenga en cuenta que todavía se recomienda evitar introducir puntos únicos de error en la arquitectura, que sería el caso si usase una sola máquina virtual que está ejecutando varias instancias de WebLogic Server. Usar al menos dos máquinas virtuales podría ser un mejor enfoque y, a continuación, cada una de esas máquinas virtuales puede ejecutar varias instancias de WebLogic Server. Cada una de estas instancias de WebLogic Server podría continuar formando parte del mismo clúster. No obstante, tenga en cuenta que actualmente no resulta posible usar Azure para efectuar el equilibrio de carga de los extremos que están expuestos por tales implementaciones de WebLogic Server dentro de la misma máquina virtual, porque el equilibrador de carga de Azure requiere que los servidores con equilibrio de carga se distribuyan entre máquinas virtuales únicas.

## <a name="oracle-jdk-virtual-machine-images"></a>Imágenes de máquina virtual de Oracle JDK
* **Actualizaciones más recientes de JDK 6 y 7.** Aunque se recomienda usar la última versión compatible pública de Java (actualmente Java 8), Azure también ofrece imágenes de JDK 6 y 7. Esto está pensado para las aplicaciones heredadas que aun no están preparadas para actualizarse a JDK 8. Mientras que es posible que las actualizaciones a imágenes de JDK anteriores ya no estén disponibles para el público general, dada la asociación de Microsoft con Oracle, las imágenes de JDK 6 y 7 ofrecidas por Azure están diseñadas para contener una actualización más reciente no pública que la ofrecida normalmente por Oracle para tan solo un grupo selecto de clientes compatibles con Oracle. Con el tiempo habrá disponibles nuevas versiones de las imágenes de JDK con versiones actualizadas de JDK 6 y 7.
  
   Tenga en cuenta que el JDK disponible en estas imágenes de JDK 6 y 7 y las máquinas virtuales e imágenes derivadas de estas solo se podrán usar dentro de Azure.
* **JDK de 64 bits.** Las imágenes de máquina virtual de Oracle WebLogic Server y las imágenes de máquina virtual de JDK de Oracle proporcionadas por Azure contienen las versiones de 64 bits de Windows Server y el JDK.

## <a name="additional-resources"></a>Recursos adicionales
[Imágenes de máquina virtual de Oracle para Azure](../../linux/classic/oracle-images.md)


