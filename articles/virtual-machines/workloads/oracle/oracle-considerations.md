---
title: Soluciones de Oracle en Microsoft Azure | Microsoft Docs
description: Aprenda sobre las configuraciones admitidas y las limitaciones de las soluciones de Oracle en Microsoft Azure.
services: virtual-machines-linux
documentationcenter: 
manager: timlt
author: rickstercdn
tags: azure-resource-management
ms.assetid: 5d71886b-463a-43ae-b61f-35c6fc9bae25
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: rclaus
ms.openlocfilehash: 1bc03d15096e7f1d4538d6642a61aaee9bb572f7
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="oracle-solutions-and-their-deployment-on-microsoft-azure"></a>Soluciones de Oracle y su implementación en Microsoft Azure
En este artículo se trata la información necesaria para implementar correctamente diversas soluciones de Oracle en Microsoft Azure. Estas soluciones se basan en las imágenes de máquina virtual publicadas por Oracle en Azure Marketplace. Para obtener una lista de imágenes disponibles actualmente, ejecute el siguiente comando:
```azurecli-interactive
az vm image list --publisher oracle -o table --all
```
A partir del 6 de junio de 2017, la lista de imágenes es la siguiente:
```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Linux            Oracle       6.4                     Oracle:Oracle-Linux:6.4:6.4.20141206                         6.4.20141206
Oracle-Linux            Oracle       6.7                     Oracle:Oracle-Linux:6.7:6.7.20161007                         6.7.20161007
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20161020                         6.8.20161020
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20170406                         6.9.20170406
Oracle-Linux            Oracle       7.0                     Oracle:Oracle-Linux:7.0:7.0.20141217                         7.0.20141217
Oracle-Linux            Oracle       7.2                     Oracle:Oracle-Linux:7.2:7.2.20161020                         7.2.20161020
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20170320                         7.3.20170320
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Estas imágenes se consideran "Traiga su propia licencia" y, por tanto, solo se le cobrarán los costos de proceso, almacenamiento y red derivados de la ejecución de una máquina virtual.  Se supone que tiene la licencia correcta para usar software Oracle y que cuenta con un acuerdo de soporte técnico actual con Oracle. Oracle ha garantizado la movilidad de licencias del entorno local a Azure. Para más información sobre la movilidad de licencias, consulte la nota publicada de [Oracle y Microsoft](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

Los individuos pueden elegir también basar sus soluciones en una imagen personalizada que crean desde cero en Azure o carga su imagen personalizada de su entorno local.

## <a name="support-for-jd-edwards"></a>Compatibilidad con JD Edwards
De acuerdo con la nota de compatibilidad de Oracle con el [id. de documento 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), las versiones de JD Edwards EnterpriseOne 9.2 y superiores se admiten en **cualquier oferta de nube pública** que satisfaga sus `Minimum Technical Requirements` (MTR) específicos.  Debe crear imágenes personalizadas que cumplan sus especificaciones de MTR para la compatibilidad con el sistema operativo y la aplicación de software. 

## <a name="oracle-database-virtual-machine-images"></a>Imágenes de máquina virtual de Oracle Database
Oracle admite la ejecución de las ediciones Oracle DB 12.1 Standard y Enterprise en Azure en imágenes de máquina virtual basadas en Oracle Linux.  Para conseguir el mejor rendimiento para las cargas de trabajo de producción de Oracle DB en Azure, asegúrese de ajustar correctamente el tamaño de la imagen de máquina virtual y de usar discos administrados que estén respaldados por Premium Storage. Para obtener instrucciones sobre cómo conseguir poner en marcha rápidamente una instancia de Oracle DB en Azure mediante la imagen de máquina virtual publicada, [pruebe el tutorial de inicio rápido de Oracle DB](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Opciones de configuración de discos conectados

Los discos conectados se basan en el servicio de almacenamiento de blobs de Azure. Teóricamente, cada disco estándar puede realizar aproximadamente 500 operaciones de entrada/salida por segundo (IOPS). Nuestra oferta de disco premium es preferible para las cargas de trabajo de base de datos de alto rendimiento y puede alcanzar las 5000 IOPS disco. Aunque se puede usar un disco individual, siempre que cubra las necesidades de rendimiento, se puede mejorar el rendimiento de IOPS efectivo si se usan varios discos conectados, repartir los datos de la base de datos a través de ellos y, a continuación, usar Automatic Storage Management (ASM) de Oracle. Consulte la [información general sobre Oracle Automatic Storage](http://www.oracle.com/technetwork/database/index-100339.html) para más información específica de Oracle ASM. Para ver un ejemplo de cómo instalar y configurar Oracle ASM en una máquina virtual Linux de Azure, puede probar el tutorial [Instalación y configuración de Oracle Automated Storage Management](configure-oracle-asm.md).

## <a name="oracle-real-application-cluster-oracle-rac"></a>Clúster de aplicaciones reales de Oracle (Oracle RAC)
Oracle RAC está diseñado para mitigar el error de un único nodo en una configuración local de clúster de varios nodos. Se basa en dos tecnologías locales que no son nativas para los entornos de nube pública de hiperescala: multidifusión y disco compartido de red. Si la solución de base de datos requiere Oracle RAC en Azure, se necesita software de terceros para habilitar estas tecnologías.  Hay disponible una oferta de **Microsoft Azure Certified** llamada [FlashGrid Node for Oracle RAC](https://azuremarketplace.microsoft.com/marketplace/apps/flashgrid-inc.flashgrid-racnode?tab=Overview) en Azure Marketplace, publicada por FlashGrid Inc. Para más información sobre esta solución y su funcionamiento en Azure, consulte la [página de soluciones de FlashGrid](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Consideraciones sobre la alta disponibilidad y la recuperación ante desastres
Cuando se usan bases de datos Oracle en Azure, usted es responsable de implementar una solución de recuperación ante desastres y alta disponibilidad para evitar tiempo de inactividad. 

La alta disponibilidad y recuperación ante desastres para Oracle Database Enterprise Edition (sin basarse en Oracle RAC) se puede lograr en Azure mediante [Protección de datos, Protección de datos activa](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html) u [Oracle Golden Gate](http://www.oracle.com/technetwork/middleware/goldengate), con dos bases de datos en dos máquinas virtuales independientes. Ambas máquinas virtuales deben estar en la misma [red virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para asegurarse de que pueden acceder entre sí a través de la dirección IP privada persistente.  Además, recomendamos colocar las máquinas virtuales en el mismo conjunto de disponibilidad para permitir a Azure colocarlas en dominios de error y de actualización independientes.  Si desea tener redundancia geográfica, puede hacer que estas dos bases de datos se repliquen entre dos regiones diferentes y conectar las dos instancias con una instancia de VPN Gateway.

En el tutorial "[Implement Oracle DataGuard on Azure](configure-oracle-dataguard.md)" (Implementación de Oracle DataGuard en Azure) se describe el procedimiento de configuración básico para probar esto en Azure.  

Con la Protección de datos de Oracle, se puede lograr alta disponibilidad con una base de datos principal en una máquina virtual, una base de datos secundaria (de reserva) en otra máquina virtual y la replicación unidireccional establecida entre ellos. El resultado es un acceso de lectura a la copia de la base de datos. Con Oracle GoldenGate, puede configurar la replicación bidireccional entre las dos bases de datos. Para más información sobre cómo configurar una solución de alta disponibilidad para bases de datos mediante estas herramientas, consulte la documentación de [Protección de datos activa](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) y [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) en el sitio web de Oracle. Si se necesita acceso de lectura-escritura a la copia de la base de datos, puede usar [Protección de datos activa de Oracle](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

En el tutorial "[Implement Oracle GoldenGate on Azure](configure-oracle-golden-gate.md)" (Implementación de Oracle GoldenGate en Azure) se describe el procedimiento de configuración básico para probar esto en Azure.

A pesar de tener una solución de alta disponibilidad y recuperación ante desastres diseñada en Azure, debe asegurarse de contar con una estrategia de copia de seguridad para restaurar la base de datos.  En el tutorial [Backup and recover an Oracle Database](oracle-backup-recovery.md) (Copia de seguridad y recuperación de una instancia de Oracle Database) se describe el procedimiento básico para establecer una copia de seguridad coherente.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Imágenes de máquina virtual de Oracle WebLogic Server
* **La agrupación en clústeres solo se admite en Enterprise Edition.** Tiene licencia para usar la agrupación en clústeres de WebLogic solo cuando utiliza la versión Enterprise Edition de WebLogic Server. No use la agrupación en clústeres con WebLogic Server Standard Edition.
* **Tampoco se admite la multidifusión UDP.** Azure admite la unidifusión UDP, pero no admite la multidifusión ni la difusión. WebLogic Server es capaz de depender de las capacidades de unidifusión UDP de Azure. Para obtener mejores resultados dependiendo de la unidifusión UDP, recomendamos que el tamaño del clúster WebLogic se mantenga estático o con no más de 10 servidores administrados incluidos en el clúster.
* **WebLogic Server espera que los puertos públicos y privados sean los mismos para el acceso de T3 (por ejemplo, al usar Enterprise JavaBeans).** Considere un escenario de varios niveles, en el que una aplicación de capa de servicio (EJB) se ejecuta en un clúster de servidor WebLogic compuesto por dos o más máquinas virtuales, en una red virtual llamada **SLWLS**. El nivel de cliente está en una subred diferente, ejecutando un programa Java simple que intenta llamar a EJB en el nivel de servicio. Dado que es necesario equilibrar la carga de la capa de servicio, debe crearse un extremo público con equilibrio de carga para las máquinas virtuales en el clúster de WebLogic Server. Si el puerto privado que especifique es diferente del puerto público (por ejemplo, 7006:7008), se producirá un error como el siguiente:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Esto se debe a que para cualquier acceso remoto T3, WebLogic Server espera que el puerto con equilibrador de carga y el puerto del servidor WebLogic administrado sean el mismo. En el caso anterior, el cliente tiene acceso al puerto 7006 (el puerto del equilibrador de carga) y el servidor administrado está escuchando en 7008 (el puerto privado). Esta restricción se aplica solo al acceso T3, no a HTTP.

   Para evitar este problema, use una de las siguientes soluciones:

  * Use los mismos números de puerto públicos y privados para los extremos de equilibrio de carga dedicados al acceso a T3.
  * Incluya el siguiente parámetro JVM al iniciar WebLogic Server:

         -Dweblogic.rjvm.enableprotocolswitch=true

Para obtener información relacionada, consulte el artículo de KB **860340.1** en <http://support.oracle.com>.

* **Agrupación en clústeres dinámica y limitaciones de equilibrio de carga.** Supongamos que desea usar un clúster dinámico en WebLogic Server y exponerlo a través de un único extremo con equilibrio de carga público en Azure. Esto puede hacerse siempre que use un número de puerto fijo para cada uno de los servidores administrados (no asignados dinámicamente a partir un rango) y no inicie más servidores administrados que máquinas esté siguiendo el administrador (es decir, que no haya más de un servidor administrado por máquina virtual). Si la configuración provoca que se inicien más servidores WebLogic que máquinas virtuales (es decir, donde varias instancias de WebLogic Server compartirán la misma máquina virtual), entonces no será posible para más de una de esas instancias de servidores de WebLogic Server establecer un enlace a un número de puerto determinado: los demás de esa máquina virtual generan un error.

   Si configura el servidor de administración para asignar automáticamente números de puerto exclusivos para los servidores administrados, el equilibrio de carga no será posible porque Azure no admite la asignación de un único puerto público a varios puertos privados, como sería necesario para esta configuración.
* **Varias instancias de Weblogic Server en una máquina virtual.** Dependiendo de los requisitos de su implementación, puede considerar la opción de ejecutar varias instancias de WebLogic Server en la misma máquina virtual, si la máquina virtual es suficientemente grande. Por ejemplo, en un máquina virtual de tamaño medio que contiene dos núcleos, puede elegir ejecutar dos instancias de WebLogic Server. Sin embargo, tenga en cuenta que todavía se recomienda evitar introducir puntos únicos de error en la arquitectura, que sería el caso si usase una sola máquina virtual que está ejecutando varias instancias de WebLogic Server. Usar al menos dos máquinas virtuales podría ser un mejor enfoque y, a continuación, cada una de esas máquinas virtuales puede ejecutar varias instancias de WebLogic Server. Cada una de estas instancias de WebLogic Server podría continuar formando parte del mismo clúster. No obstante, tenga en cuenta que actualmente no resulta posible usar Azure para efectuar el equilibrio de carga de los extremos que están expuestos por tales implementaciones de WebLogic Server dentro de la misma máquina virtual, porque el equilibrador de carga de Azure requiere que los servidores con equilibrio de carga se distribuyan entre máquinas virtuales únicas.

## <a name="oracle-jdk-virtual-machine-images"></a>Imágenes de máquina virtual de Oracle JDK
* **Actualizaciones más recientes de JDK 6 y 7.** Aunque se recomienda usar la última versión compatible pública de Java (actualmente Java 8), Azure también ofrece imágenes de JDK 6 y 7. Esto está pensado para las aplicaciones heredadas que aun no están preparadas para actualizarse a JDK 8. Mientras que es posible que las actualizaciones a imágenes de JDK anteriores ya no estén disponibles para el público general, dada la asociación de Microsoft con Oracle, las imágenes de JDK 6 y 7 ofrecidas por Azure están diseñadas para contener una actualización más reciente no pública que la ofrecida normalmente por Oracle para tan solo un grupo selecto de clientes compatibles con Oracle. Con el tiempo habrá disponibles nuevas versiones de las imágenes de JDK con versiones actualizadas de JDK 6 y 7.

   Tenga en cuenta que el JDK disponible en estas imágenes de JDK 6 y 7 y las máquinas virtuales e imágenes derivadas de estas solo se podrán usar dentro de Azure.
* **JDK de 64 bits.** Las imágenes de máquina virtual de Oracle WebLogic Server y las imágenes de máquina virtual de JDK de Oracle proporcionadas por Azure contienen las versiones de 64 bits de Windows Server y el JDK.

## <a name="next-steps"></a>Pasos siguientes
Ahora tiene una visión general de las soluciones actuales de Oracle en Microsoft Azure. El siguiente paso es implementar su primera instancia de Oracle Database en Azure.
- Para comenzar, pruebe el tutorial [Create an Oracle Database on Azure](oracle-database-quick-create.md) (Creación de una instancia de Oracle Database en Azure).