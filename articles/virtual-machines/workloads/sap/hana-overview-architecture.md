---
title: Introducción y arquitectura de SAP HANA en Azure (instancias grandes) | Microsoft Docs
description: Introducción a la arquitectura para implementar SAP HANA en Azure (instancias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/02/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ceeec6991aaac64211301313c1bb8dc5f5faa1c0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Introducción y arquitectura de SAP HANA en Azure (instancias grandes)

## <a name="what-is-sap-hana-on-azure-large-instances"></a>¿Qué es SAP HANA en Azure (instancias grandes)?

SAP HANA en Azure (instancias grandes) es una solución exclusiva de Azure. Además de proporcionar máquinas virtuales con el fin de implementar y ejecutar SAP HANA, Azure ofrece la posibilidad de ejecutar e implementar SAP HANA en servidores sin sistema operativo dedicados a su organización. La solución SAP HANA en Azure (instancias grandes) se basa en hardware sin sistema operativo con hosts o servidores no compartidos asignados a su organización. El hardware del servidor se inserta en las marcas más grandes que contienen infraestructuras de proceso/servidor, redes y almacenamiento. De forma combinada, tiene la certificación de integración de centro de datos adaptado para HANA (TDI). SAP HANA en Azure (instancias grandes) ofrece diferentes SKU o tamaños de servidor. Las unidades pueden tener 72 CPU y 768 GB de memoria y se puede llegar hasta unidades con 960 CPU y 20 TB de memoria.

El aislamiento del cliente dentro de la demarcación de infraestructura se realiza en inquilinos y presenta el siguiente aspecto:

- **Redes**: aislamiento de los clientes dentro de la pila de infraestructura mediante redes virtuales por inquilino asignado al cliente. Un inquilino se asigna a un único cliente. Un cliente puede tener varios inquilinos. El aislamiento de red de los inquilinos prohíbe la comunicación de red entre ellos en el nivel de la demarcación de infraestructura, incluso si los inquilinos pertenecen al mismo cliente.
- **Componentes de almacenamiento**: aislamiento mediante máquinas virtuales de almacenamiento que tienen volúmenes de almacenamiento asignados. Los volúmenes de almacenamiento pueden asignarse solo a una máquina virtual de almacenamiento. Una máquina virtual de almacenamiento se asigna exclusivamente a un solo inquilino en la pila de la infraestructura certificada con SAP HANA TDI. Como resultado, se puede acceder a los volúmenes de almacenamiento asignados a una máquina virtual de almacenamiento solo en un inquilino específico y relacionado. Dichos volúmenes no son visibles entre los distintos inquilinos implementados.
- **Servidor o host**: una unidad de servidor o host no se comparte entre clientes o inquilinos. Un servidor o host implementado en un cliente es una unidad de proceso de reconstrucción completa atómica que se asigna a un único inquilino. *No* se usan particiones por hardware ni por software que puedan dar lugar a que un cliente comparta un host o servidor con otro cliente. Los volúmenes de almacenamiento asignados a la máquina virtual de almacenamiento del inquilino específico se montan en dicho servidor. Un inquilino puede tener una o muchas unidades de servidor de diferentes SKU asignadas de forma exclusiva.
- En una demarcación de infraestructura de SAP HANA en Azure (instancias grandes) se implementan muchos inquilinos diferentes que se aíslan entre sí a través de los conceptos de inquilino en los niveles de redes, almacenamiento y proceso. 


La ejecución de estas unidades de servidor de reconstrucción completa es compatible solo en SAP HANA. El nivel de aplicación de SAP o el nivel de middleware de la carga de trabajo se ejecutan en máquinas virtuales. Las demarcaciones de infraestructura que ejecutan las unidades de SAP HANA en Azure (instancias grandes) están conectadas a las redes troncales de los servicios de red de Azure. De este modo, se proporciona conectividad de baja latencia entre las unidades de SAP HANA en Azure (instancias grandes) y las máquinas virtuales.

Este documento es uno de varios documentos que tratan sobre SAP HANA en Azure (instancias grandes). Este documento presenta la arquitectura básica, las responsabilidades y los servicios proporcionados por la solución. También se describen las funcionalidades de alto nivel de la solución. Para la mayoría de otras áreas, como redes y conectividad, otros cuatro documentos tratan detalles e información en profundidad. La documentación de SAP HANA en Azure (instancias grandes) no aborda aspectos de la instalación de SAP NetWeaver ni de las implementaciones de SAP NetWeaver en máquinas virtuales. SAP NetWeaver en Azure se trata en documentos independientes disponibles en el mismo contenedor de documentación de Azure. 


Los distintos documentos de orientación de la instancia grande de HANA abarcan las siguientes áreas:

- [Introducción y arquitectura de SAP HANA en Azure (instancias grandes)](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infraestructura y conectividad con SAP HANA en Azure (instancias grandes)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Instalación y configuración de SAP HANA en Azure (instancias grandes)](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes)](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Solución de problemas y supervisión de SAP HANA en Azure (instancias grandes)](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Configuración de alta disponibilidad en SUSE mediante STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Copia de seguridad y restauración del sistema operativo para SKU de tipo II](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

## <a name="definitions"></a>Definiciones

En la Guía de implementación técnica y arquitectura, se utilizan con frecuencia varias definiciones habituales. Tenga en cuenta los siguientes términos y sus significados:

- **IaaS**: infraestructura como servicio.
- **PaaS**: plataforma como servicio.
- **SaaS**: software como servicio.
- **Componente de SAP**: una aplicación de SAP individual, por ejemplo, ERP Central Component (ECC), Business Warehouse (BW), Solution Manager o Enterprise Portal (EP). Los componentes de SAP pueden basarse en tecnologías tradicionales, como ABAP o Java, o en una aplicación no basada en NetWeaver, como Business Objects.
- **Entorno de SAP:** uno o varios componentes de SAP agrupados lógicamente para desempeñar una función empresarial, como desarrollo, control de calidad, aprendizaje, recuperación ante desastres o producción.
- **Infraestructura de SAP**: hace referencia a todos los recursos de SAP de su infraestructura de TI. La infraestructura de SAP incluye todos los entornos, tanto los que son de producción como los que no.
- **Sistema SAP**: la combinación de la capa de DBMS y el nivel de aplicación de, por ejemplo, un sistema de desarrollo de SAP ERP, un sistema de prueba de SAP BW y un sistema de producción de SAP CRM. Las implementaciones de Azure no admiten la división de estas dos capas entre la infraestructura local y la de Azure. Un sistema SAP debe implementarse de forma local o en Azure, pero no en ambos. Los diferentes sistemas de una infraestructura de SAP pueden implementarse en Azure o de forma local. Por ejemplo, puede implementar los sistemas de prueba y desarrollo de SAP CRM en Azure, mientras que el sistema de producción de SAP CRM se implementa de manera local. En el caso de SAP HANA en Azure (instancias grandes), se supone que hospedará el nivel de aplicación de SAP de los sistemas SAP en máquinas virtuales y la instancia de SAP HANA relacionada en una unidad de la demarcación de SAP HANA en Azure (instancias grandes).
- **Demarcación de instancias grandes:** una pila de infraestructura de hardware que está certificada para SAP HANA TDI y se dedica a ejecutar instancias de SAP HANA dentro de Azure.
- **SAP HANA en Azure (instancias grandes):** nombre oficial de la oferta de Azure para ejecutar instancias de HANA en hardware con certificación SAP HANA TDI que se implementa en demarcaciones de instancias grandes en diferentes regiones de Azure. El término relacionado *HANA (instancias grandes)* es la versión abreviada de *SAP HANA en Azure (instancias grandes)* y se usa con frecuencia en esta guía de implementación técnica.
- **Entre locales**: describe un escenario donde se implementan máquinas virtuales en una suscripción de Azure con conexión de sitio a sitio, entre varios sitios o de Azure ExpressRoute entre los centros de datos locales y Azure. En la documentación habitual de Azure, este tipo de implementaciones se denominan "escenarios entre locales". La razón de la conexión es extender los dominios locales, Azure Active Directory, OpenLDAP local y DNS local en Azure. La infraestructura local se extiende a los recursos de Azure de las suscripciones de Azure. Con esta extensión, las máquinas virtuales pueden formar parte del dominio local. 

   Los usuarios del dominio local pueden tener acceso a los servidores y ejecutar servicios en esas máquinas virtuales (por ejemplo, servicios de DBMS). Es posible la comunicación y resolución de nombres entre máquinas virtuales implementadas de forma local y en Azure. Se trata del escenario típico en que se implementan la mayoría de los recursos de SAP. Consulte [Planeamiento y diseño de Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) y [Creación de una red virtual con una conexión de sitio a sitio mediante Azure Portal](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para más información.
- **Inquilino**: un cliente implementado en la demarcación de HANA (instancias grandes) se aísla en un *inquilino*. Un inquilino se aísla en el nivel de redes, almacenamiento y proceso de otros inquilinos. Las unidades de almacenamiento y proceso asignadas a los distintos inquilinos no pueden verse ni comunicarse entre sí en el nivel de la demarcación de HANA (instancias grandes). Un cliente puede elegir que las implementaciones se realicen en diferentes inquilinos. Aún así, no hay ninguna comunicación entre los inquilinos a nivel de la marca de la instancia grande HANA.
- **Categoría de SKU:** para HANA (instancias grandes), se ofrecen las siguientes dos categorías de SKU:
    - **Clase de tipo I:** S72, S72m, S144, S144m, S192 y S192m
    - **Clase de tipo II:** S384, S384m, S384xm, S576, S768 y S960


Hay una serie de recursos adicionales sobre cómo implementar una carga de trabajo SAP en la nube. Si planea ejecutar una implementación de SAP HANA en Azure, debe poseer conocimientos y experiencia con los principios de IaaS de Azure y la implementación de cargas de trabajo de SAP en IaaS de Azure. Antes de continuar, consulte [Uso de soluciones de SAP enAzure Virtual Machines](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para más información. 

## <a name="certification"></a>Certificación

Además de la certificación NetWeaver, SAP requiere una certificación especial de SAP HANA para admitir SAP HANA en determinadas infraestructuras, como IaaS de Azure.

La nota fundamental de SAP sobre NetWeaver y hasta cierto punto sobre la certificación de SAP HANA, es [SAP Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (Nota de SAP 1928533: Aplicaciones de SAP en Azure: productos y tipos de máquina virtual de Azure admitidos).

La nota [SAP Note #2316233 - SAP HANA on Microsoft Azure (Large Instances)](https://launchpad.support.sap.com/#/notes/2316233/E) (Nota de SAP 2316233: SAP HANA en Microsoft Azure [instancias grandes]) también es relevante. Trata sobre la solución descrita en esta guía. Además, se admite la ejecución de SAP HANA en el tipo de máquina virtual de Azure GS5. La información de este caso está publicada en el [sitio web de SAP](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

La solución SAP HANA en Azure (instancias grandes) mencionada en la nota de SAP 2316233 proporciona a los clientes de Microsoft y SAP la capacidad de implementar cargas de trabajo grandes de SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA u otras cargas de trabajo de SAP HANA en Azure. Esta solución se basa en la demarcación de hardware dedicado con certificación de SAP HANA ([Integración de centro de datos adaptado para SAP HANA o TDI](https://scn.sap.com/docs/DOC-63140)). Si ejecuta una solución configurada según SAP HANA TDI, todas las aplicaciones basadas en SAP HANA (por ejemplo, SAP Business Suite en SAP HANA, SAP BW en SAP HANA, S4/HANA y BW4/HANA) trabajan en la infraestructura de hardware.

Comparada con la ejecución de SAP HANA en máquinas virtuales, esta solución tiene una ventaja. Proporciona volúmenes de memoria mucho mayores. Para habilitar esta solución, debe entender los siguientes aspectos fundamentales:

- El nivel de aplicación de SAP y las aplicaciones que no son de SAP se ejecutan en máquinas virtuales que se hospedan en las demarcaciones de hardware de Azure habituales.
- La infraestructura local, los centros de datos y las implementaciones de aplicaciones del cliente se conectan a la plataforma en la nube mediante ExpressRoute (recomendado) o una red privada virtual (VPN). Active Directory y DNS también se extienden a Azure.
- La instancia de base de datos de SAP HANA para cargas de trabajo de HANA se ejecuta en SAP HANA en Azure (Instancias grandes). La demarcación de instancias grandes está conectada a las redes de Azure, por lo que el software que se ejecuta en las máquinas virtuales puede interactuar con la instancia de HANA que se ejecuta en HANA (instancias grandes).
- El hardware de SAP HANA en Azure (instancias grandes) es hardware dedicado que se proporciona como IaaS con SUSE Linux Enterprise Server o Red Hat Enterprise Linux preinstalado. Al igual que con las máquinas virtuales, es responsabilidad suya llevar a cabo las actualizaciones y el mantenimiento del sistema operativo.
- La instalación de HANA o los componentes adicionales necesarios para ejecutar SAP HANA en las unidades de HANA (instancias grandes) es su responsabilidad. Todas las operaciones relacionadas correspondientes y la administración de SAP HANA en Azure también son responsabilidad suya.
- Además de las soluciones descritas aquí, puede instalar otros componentes en la suscripción de Azure conectada a SAP HANA en Azure (Instancias grandes). Por ejemplo, los componentes que permiten la comunicación, directa o no, con la base de datos de SAP HANA, como los servidores de salto, servidores RDP, SAP HANA Studio, SAP Data Services para escenarios de BI de SAP o soluciones de supervisión de red.
- Como en Azure, HANA (instancias grandes) ofrece funcionalidades auxiliares de alta disponibilidad y recuperación ante desastres.

## <a name="architecture"></a>Architecture

En un nivel genérico, en la solución SAP HANA en Azure (instancias grandes), el nivel de aplicación de SAP reside en máquinas virtuales. La capa de base de datos reside en hardware configurado según SAP TDI ubicado en una demarcación de instancias grandes en la misma región de Azure que está conectada a la IaaS de Azure.

> [!NOTE]
> Debe implementar el nivel de aplicación de SAP en la misma región de Azure que la capa de DBMS de SAP. Esta regla está bien documentada en la información publicada sobre cargas de trabajo de SAP en Azure. 

La arquitectura global de SAP HANA en Azure (instancias grandes) proporciona una configuración de hardware con certificación SAP TDI, que es un servidor de alto rendimiento sin sistema operativo y sin virtualización para la base de datos de SAP HANA. También proporciona la capacidad y flexibilidad de Azure para escalar los recursos del nivel de aplicación de SAP según sus necesidades.

![Introducción a la arquitectura de SAP HANA en Azure (Instancias grandes)](./media/hana-overview-architecture/image1-architecture.png)

La arquitectura que se muestra se divide en tres secciones:

- **Derecha**: muestra una infraestructura local que ejecuta aplicaciones diferentes en los centros de datos de modo que los usuarios finales puedan acceder a aplicaciones de línea de negocio, como SAP. Idealmente, esta infraestructura local se conecta a su vez a Azure mediante [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Centro**: muestra la IaaS de Azure y, en este caso, el uso de máquinas virtuales para hospedar SAP u otras aplicaciones que usan SAP HANA como sistema DBMS. Las instancias de HANA menores que funcionan con la memoria proporcionada por las máquinas virtuales se implementan en máquinas virtuales junto con el nivel de aplicación. Para más información sobre máquinas virtuales, consulte [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/).

   Se usan los servicios de red de Azure para agrupar los sistemas SAP junto con otras aplicaciones en redes virtuales. Estas redes virtuales se conectan a los sistemas locales, así como a SAP HANA en Azure (instancias grandes).

   Para más información sobre las aplicaciones y bases de datos de SAP NetWeaver que admiten la ejecución en Azure, consulte [SAP Support Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (Nota de SAP 1928533: Aplicaciones de SAP en Azure: productos y tipos de máquina virtual de Azure admitidos). Para obtener documentación sobre cómo implementar soluciones de SAP en Azure, consulte:

  -  [Uso de SAP en máquinas virtuales Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Uso de soluciones de SAP en Azure Virtual Machines](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Izquierda**: muestra el hardware con certificación SAP HANA TDI en la demarcación de instancias grandes de Azure. Las unidades de HANA (instancias grandes) se conectan a las redes virtuales de su suscripción con la misma tecnología que la empleada para la conectividad entre la infraestructura local y la de Azure.

El sello de instancias grandes de Azure en sí combina los componentes siguientes:

- **Procesamiento**: servidores basados en procesadores Intel Xeon E7-8890v3 o Intel Xeon E7-8890v4 que proporcionan la funcionalidad de computación necesaria y poseen certificación para SAP HANA.
- **Red**: tejido de red de alta velocidad unificada que interconecta los componentes de procesamiento, almacenamiento y LAN.
- **Almacenamiento**: una infraestructura de almacenamiento a la que se accede por medio de un tejido de red unificada. La capacidad de almacenamiento específico que se proporciona depende de la configuración específica de SAP HANA en Azure (instancias grandes) que se implementa. Está disponible más capacidad de almacenamiento con un costo mensual adicional.

Dentro de la infraestructura multiinquilino del sello de instancias grandes, los clientes se implementan como inquilinos aislados. En la implementación del inquilino, asigne un nombre a una suscripción de Azure dentro de la inscripción de Azure. HANA (instancias grandes) se factura a esta suscripción de Azure. Estos inquilinos mantienen una relación de 1:1 con la suscripción de Azure. Para una red, es posible acceder a una unidad de HANA (instancias grandes) implementada en un inquilino de una región de Azure desde redes virtuales diferentes que pertenecen a diferentes suscripciones de Azure. Esas suscripciones de Azure deben pertenecer a la misma inscripción de Azure. 

Al igual que las máquinas virtuales, SAP HANA en Azure (instancias grandes) se ofrece en varias regiones de Azure. Para ofrecer funcionalidades de recuperación ante desastres, puede elegir participar. Los diferentes sellos de instancia grande en una región geopolítica están conectados entre sí. Por ejemplo, las demarcaciones de HANA (instancias grandes) en Oeste de EE. UU. y Este de EE. UU. se conectan a través de una conexión de red dedicada para la replicación de recuperación ante desastres. 

Al igual que puede elegir entre diferentes tipos de máquinas virtuales con Azure Virtual Machines, también puede elegir entre diferentes SKU de HANA (instancias grandes) que se adapten a distintos tipos de carga de trabajo de SAP HANA. SAP aplica una relación de memoria a sockets de procesador para diferentes cargas de trabajo en función de la generación de los procesadores Intel. En la tabla siguiente muestra los tipos de SKU que se ofrecen.

A partir de julio de 2017, SAP HANA en Azure (instancias grandes) está disponible en varias configuraciones en las regiones de Azure Oeste de EE. UU. y Este de EE. UU., Este de Australia, Sudeste de Australia, Europa Occidental y Europa del Norte.

| Solución de SAP | CPU | Memoria | Storage | Disponibilidad |
| --- | --- | --- | --- | --- |
| Optimizada para OLAP: SAP BW, BW/4HANA<br /> o SAP HANA para cargas de trabajo de OLAP genérico | SAP HANA en Azure S72<br /> – 2 procesadores Intel® Xeon® E7-8890 v3<br /> 36 núcleos de CPU y 72 subprocesos de CPU |  768 GB |  3 TB | Disponible |
| --- | SAP HANA en Azure S144<br /> – 4 procesadores Intel® Xeon® E7-8890 v3<br /> 72 núcleos de CPU y 144 subprocesos de CPU |  1,5 TB |  6 TB | No disponible |
| --- | SAP HANA en Azure S192<br /> – 4 procesadores Intel® Xeon® E7-8890 v4<br /> 96 núcleos de CPU y 192 subprocesos de CPU |  2,0 TB |  8 TB | Disponible |
| --- | SAP HANA en Azure S384<br /> – 8 procesadores Intel® Xeon® E7-8890 v4<br /> 192 núcleos de CPU y 384 subprocesos de CPU |  4,0 TB |  16 TB | Disponible |
| Optimizada para OLTP: SAP Business Suite<br /> en SAP HANA o S/4HANA (OLTP),<br /> OLTP genérico | SAP HANA en Azure S72m<br /> – 2 procesadores Intel® Xeon® E7-8890 v3<br /> 36 núcleos de CPU y 72 subprocesos de CPU |  1,5 TB |  6 TB | Disponible |
|---| SAP HANA en Azure S144m<br /> – 4 procesadores Intel® Xeon® E7-8890 v3<br /> 72 núcleos de CPU y 144 subprocesos de CPU |  3,0 TB |  12 TB | No disponible |
|---| SAP HANA en Azure S192m<br /> – 4 procesadores Intel® Xeon® E7-8890 v4<br /> 96 núcleos de CPU y 192 subprocesos de CPU  |  4,0 TB |  16 TB | Disponible |
|---| SAP HANA en Azure S384m<br /> – 8 procesadores Intel® Xeon® E7-8890 v4<br /> 192 núcleos de CPU y 384 subprocesos de CPU |  6,0 TB |  18 TB | Disponible |
|---| SAP HANA en Azure S384xm<br /> – 8 procesadores Intel® Xeon® E7-8890 v4<br /> 192 núcleos de CPU y 384 subprocesos de CPU |  8,0 TB |  22 TB |  Disponible |
|---| SAP HANA en Azure S576<br /> – 12 procesadores Intel® Xeon® E7-8890 v4<br /> 288 núcleos de CPU y 576 subprocesos de CPU |  12,0 TB |  28 TB | Disponible |
|---| SAP HANA en Azure S768<br /> – 16 procesadores Intel® Xeon® E7-8890 v4<br /> 384 núcleos de CPU y 768 subprocesos de CPU |  16,0 TB |  36 TB | Disponible |
|---| SAP HANA en Azure S960<br /> – 20 procesadores Intel® Xeon® E7-8890 v4<br /> 480 núcleos de CPU y 960 subprocesos de CPU |  20,0 TB |  46 TB | Disponible |

- Núcleos de CPU = suma de núcleos de CPU sin Hyper-Threading de la suma de procesadores de la unidad del servidor.
- Subprocesos de CPU = suma de suprocesos de proceso proporcionado por núcleos de CPU con Hyper-Threading de la suma de procesadores de la unidad del servidor. Todas las unidades se configuran de forma predeterminada para utilizar la tecnología Hyper-Threading.


Las configuraciones específicas que se elijan dependen de la carga de trabajo, los recursos de CPU y la memoria que desee. La carga de trabajo de OLTP puede usar las SKU que estén optimizadas para la carga de trabajo OLAP. 

La base de hardware para todas las ofertas tiene la certificación SAP HANA TDI. Dos clases diferentes de hardware dividen las SKU en:

- S72, S72m, S144, S144m, S192 y S192m, a las que se hace referencia como "clase de tipo I" de SKU.
- S384, S384m, S384xm, S576, S768 y S960, a las que se hace referencia como "clase de tipo II" de SKU.

Una demarcación completa de HANA (instancias grandes) no está asignada exclusivamente para que la use un único cliente. Esto se aplica también a los bastidores de recursos de procesos y almacenamiento conectados mediante un tejido de red implementado en Azure. La infraestructura de HANA (instancias grandes), como Azure, implementa &quot;inquilinos&quot; de clientes diferentes que están aislados entre sí en los tres niveles siguientes:

- **Red**: aislamiento a través de redes virtuales dentro de la demarcación de HANA (instancias grandes).
- **Storage**: aislamiento a través de máquinas virtuales de almacenamiento que tienen volúmenes de almacenamiento asignados y aíslan los volúmenes de almacenamiento entre inquilinos.
- **Proceso**: asignación dedicada de las unidades de servidor a un único inquilino. Sin particiones de hardware ni de software de las unidades de servidor. Sin uso compartido de una sola unidad de host o servidor entre los inquilinos. 

Las implementaciones de unidades de HANA (instancias grandes) entre varios inquilinos no son visibles entre sí. Las unidades de HANA (instancias grandes) implementadas en diferentes inquilinos tampoco se pueden comunicar directamente entre sí en el nivel de la demarcación de HANA (instancias grandes). Solo las unidades de HANA (instancias grandes) dentro de un mismo inquilino se pueden comunicar entre sí en el nivel de la demarcación de HANA (instancias grandes).

Se asigna un inquilino implementado en la demarcación de instancias grandes a una suscripción de Azure con fines de facturación. Para una red, se puede acceder desde redes virtuales de otras suscripciones de Azure dentro de la misma inscripción de Azure. Si implementa con otra suscripción de Azure en la misma región de Azure, también puede elegir pedir un inquilino de instancia grande de HANA independiente.

Existen diferencias notables entre ejecutar SAP HANA en HANA (instancias grandes) y ejecutar SAP HANA en máquinas virtuales implementadas en Azure:

- No hay ningún nivel de virtualización para SAP HANA en Azure (Instancias grandes). El rendimiento proviene del hardware de reconstrucción completa subyacente.
- A diferencia de Azure, el servidor de SAP HANA en Azure (Instancias grandes) está dedicado a un cliente específico. No hay ninguna posibilidad de que un host o unidad de servidor tenga particiones de hardware o software. Como resultado, se utiliza una unidad de HANA (instancias grandes) tal cual se asignó como un conjunto a un inquilino y después al usuario. Tras un reinicio o un apagado del servidor, no se implementa el sistema operativo ni SAP HANA en otro servidor de manera automática. (Para las SKU de clase de tipo I, la única excepción es si un servidor se encuentra en problemas y se tiene que volver a implementar en otro servidor).
- A diferencia de Azure, donde los tipos de procesador de host se seleccionan para lograr una relación precio/rendimiento óptima, los tipos de procesador elegidos para SAP HANA en Azure (instancias grandes) son los de mayor rendimiento en la línea de procesadores Intel E7v3 y E7v4.


### <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Ejecución de varias instancias de SAP HANA en una unidad de HANA (instancias grandes)
Se puede hospedar más de una instancia de SAP HANA activa en las unidades de HANA (instancias grandes). Para proporcionar las funcionalidades de instantáneas de almacenamiento y recuperación ante desastres, esta configuración requiere un volumen por cada instancia. Actualmente, las unidades de HANA (instancias grandes) se pueden subdividir de esta forma:

- **S72, S72m, S144, S192**: en incrementos de 256 GB con la unidad más pequeña inicial de 256 GB. Se pueden combinar incrementos diferentes, como 256 GB y 512 GB, hasta el máximo de memoria de la unidad.
- **S144m y S192m**: en incrementos de 256 GB con la unidad más pequeña de 512 GB. Se pueden combinar incrementos diferentes, como 512 GB y 768 GB, hasta el máximo de memoria de la unidad.
- **Clase de tipo II**: en incrementos de 512 GB con la unidad más pequeña inicial de 2 TB. Se pueden combinar incrementos diferentes, como 512 GB, 1 GB y 1,5 GB, hasta el máximo de memoria de la unidad.

Algunos ejemplos de la ejecución de varias instancias de SAP HANA tendrían este aspecto.

| SKU | Tamaño de memoria | Tamaño de almacenamiento | Tamaños con varias bases de datos |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 instancia de HANA de 768 GB<br /> o 1 instancia de 512 GB + 1 instancia de 256 GB<br /> o 3 instancias de 256 GB | 
| S72m | 1,5 TB | 6 TB | 3 instancias de HANA de 512 GB<br />o 1 instancia de 512 GB + 1 instancia de 1 TB<br />o 6 instancias de 256 GB<br />o 1 instancia de 1,5 TB | 
| S192m | 4 TB | 16 TB | 8 instancias de 512 GB<br />o 4 instancias de 1 TB<br />o 4 instancias de 512 GB + 2 instancias de 1 TB<br />o 4 instancias de 768 GB + 2 instancias de 512 GB<br />o 1 instancia de 4 TB |
| S384xm | 8 TB | 22 TB | 4 instancias de 2 TB<br />o 2 instancias de 4 TB<br />o 2 instancias de 3 TB + 1 instancia de 2 TB<br />o 2 instancias de 2,5 TB + 1 instancia de 3 TB<br />o 1 instancia de 8 TB |


Hay también otras variaciones. 

### <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Uso de los nodos de extensión y las capas de datos de SAP HANA
SAP admite un modelo de capas de datos para SAP BW de las diferentes versiones de SAP NetWeaver y SAP BW/4HANA. Para más información sobre el modelo de capas de datos, consulte el documento de SAP [SAP BW/4HANA and SAP BW on HANA with SAP HANA extension nodes](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#) (SAP BW/4HANA y SAP BW en HANA con nodos de extensión de SAP HANA).
Con HANA (instancias grandes), puede utilizar la configuración de la opción 1 de los nodos de extensión de SAP HANA, tal como se detalla en estas preguntas más frecuentes y en los documentos del blog de SAP. Las configuraciones de la opción-2 se pueden configurar con las siguientes SKU las instancias grandes de HANA (Instancias grandes): S72m, S192, S192m, S384 y S384m. 

Al examinar la documentación, es posible que la ventaja no se vea de inmediato. Sin embargo, si se examinan las directrices de ajuste de tamaño de SAP, se puede ver una ventaja mediante el uso de los nodos de extensión de SAP HANA de las opciones 1 y 2. Estos son algunos ejemplos:

- Las directrices de ajuste de tamaño de SAP HANA suelen requerir que la cantidad de volumen de datos sea el doble que la memoria. Si ejecuta una instancia de SAP HANA con los datos activos, solo se llenará de datos el 50 % de la memoria o menos. Lo ideal es que el resto de la memoria se use para el funcionamiento del propio SAP HANA.
- Eso significa que en una unidad S192 de la instancia grande de HANA con 2 TB de memoria que ejecuta una base de datos de SAP BW, solo tiene 1 TB de volumen de datos.
- Si usa un nodo de extensión de SAP HANA adicional de la opción 1 y también una SKU de la unidad S192 de HANA (instancias grandes), tendría 2 TB adicionales para el volumen de datos. En la configuración de la opción 2, tendría 4 TB adicionales para el volumen de datos semiactivos. En comparación con el nodo activo, la capacidad de memoria total del nodo de extensión "semiactivo" puede utilizarse para almacenar los datos para la opción 1. Se puede usar el doble de memoria para el volumen de datos en la opción 2 de la configuración del nodo de extensión de SAP HANA.
- Tendrá una capacidad de 3 TB para los datos y una relación entre activos y semiactivos de 1:2 para la opción 1. Tiene 5 TB de datos y una relación de 1:4 con la opción 2 de la configuración del nodo de extensión.

Cuanto mayor sea el volumen de datos en comparación con la memoria, más posibilidad habrá de que los datos semiactivos que pide se encuentren en el almacenamiento en disco.


## <a name="operations-model-and-responsibilities"></a>Modelo de operaciones y responsabilidades

El servicio proporcionado con SAP HANA en Azure (Instancias grandes) se alinea con los servicios de IaaS de Azure. Obtiene una instancia de HANA (instancias grandes) con un sistema operativo instalado que está optimizado para SAP HANA. Al igual que con las máquinas virtuales de IaaS de Azure, la mayoría de las tareas necesarias para proteger el sistema operativo, instalar el software adicional que necesite, instalar HANA, operar el sistema operativo y HANA y actualizar el sistema operativo y HANA son de su responsabilidad. Microsoft no exige que actualice el sistema operativo ni HANA.

![Responsabilidades de SAP HANA en Azure (Instancias grandes)](./media/hana-overview-architecture/image2-responsibilities.png)

Como se muestra en el diagrama, SAP HANA en Azure (instancias grandes) es una oferta de IaaS de varios inquilinos. La división de responsabilidad reside en el límite entre el sistema operativo y la infraestructura, en su mayor parte. Microsoft es responsable de todos los aspectos del servicio por debajo de la línea del sistema operativo. Usted es responsable de todos los aspectos del servicio encima de la línea. El sistema operativo es su responsabilidad. Puede seguir usando la mayor parte de los métodos locales que emplee actualmente para el cumplimiento normativo, la seguridad, la administración de aplicaciones, la base y la administración del sistema operativo. Los sistemas aparecen como si estuvieran en la red en todos los aspectos.

Este servicio está optimizado para SAP HANA, por lo que hay áreas en las que debe colaborar con Microsoft para usar las funcionalidades de la infraestructura subyacente a fin de obtener resultados óptimos.

En la lista siguiente, se proporcionan más detalles sobre cada una de las capas y sus responsabilidades:

**Redes**: todas las redes internas para la demarcación de instancias grandes que ejecuta SAP HANA. Su responsabilidad incluye el acceso al almacenamiento, la conectividad entre las instancias (para el escalado horizontal y otras funciones), la conectividad con la infraestructura y la conectividad con Azure cuando el nivel de aplicación de SAP se hospeda en máquinas virtuales. También incluye la conectividad de WAN entre los centros de datos de Azure para la replicación con fines de recuperación ante desastres. El inquilino se encarga de dividir todas las redes en particiones, que tienen aplicada calidad de servicio.

**Almacenamiento:** almacenamiento virtualizado con particiones para todos los volúmenes que necesitan los servidores de SAP HANA, así como para las instantáneas. 

**Servidores**: los servidores físicos dedicados a ejecutar las bases de datos de SAP HANA asignadas a los inquilinos. Los servidores de clase de tipo I de SKU se abstraen del hardware. Con estos tipos de servidores, la configuración del servidor se recopila y se mantiene en perfiles, que se pueden mover de un hardware físico a otro hardware físico. Este movimiento (manual) de un perfil mediante operaciones se puede comparar ligeramente a la recuperación de servicios de Azure. Los servidores de las SKU de clase de tipo II no ofrecen esta funcionalidad.

**SDDC**: software de administración que sirve para administrar los centros de datos como entidades definidas por software. Permite a Microsoft agrupar recursos por motivos de escalabilidad, disponibilidad y rendimiento.

**SO:** el sistema operativo que elija (SUSE Linux o Red Hat Linux) que se ejecuta en los servidores. Las imágenes de sistema operativo que se le proporcionan son las imágenes ofrecidas por el proveedor de Linux individual a Microsoft para ejecutar SAP HANA. Debe tener una suscripción con el proveedor de Linux para la imagen optimizada de SAP HANA específica. Es responsable del registro de las imágenes con el proveedor del sistema operativo. 

Desde el momento en que Microsoft le hace entrega del producto, usted es responsable de cualquier revisión adicional del sistema operativo Linux. Esta aplicación de revisiones incluye paquetes adicionales que podrían ser necesarios para una correcta instalación de SAP HANA y que no se hayan incluido por el proveedor específico de Linux en sus imágenes de sistema operativo optimizadas para SAP HANA. (Para más información, consulte la documentación de instalación de HANA de SAP y las notas de SAP.) 

Usted es responsable de aplicar revisiones al SO debido a un funcionamiento inadecuado o la optimización del sistema operativo y sus controladores en relación con el hardware de servidor específico. También es responsable de la aplicación de revisiones de seguridad o funcionales del sistema operativo. 

Es también su responsabilidad la supervisión y planificación de la capacidad de:

- Consumo de recursos de CPU.
- Consumo de memoria.
- Volúmenes de disco relacionados con espacio disponible, IOPS y latencia.
- Tráfico de volumen de red entre HANA (instancias grandes) y el nivel de aplicación de SAP.

La infraestructura subyacente de HANA (instancias grandes) proporciona funcionalidad para las copias de seguridad y la restauración del volumen del sistema operativo. Usar esta funcionalidad es también su responsabilidad.

**Middleware**: la instancia de SAP HANA, principalmente. La administración, las operaciones y la supervisión son responsabilidad suya. Puede usar la funcionalidad que se proporciona para usar instantáneas de almacenamiento para la copia de seguridad y la restauración, además de para la recuperación ante desastres. Estas capacidades se proporcionan con la infraestructura. Sus responsabilidades incluyen también el diseño de la alta disponibilidad o la recuperación ante desastres con estas funcionalidades, aprovecharlas y supervisar que las instantáneas de almacenamiento se hayan ejecutado correctamente.

**Datos**: los datos administrados por SAP HANA y otros datos, como archivos de copia de seguridad que se encuentran en volúmenes o recursos compartidos de archivos. Sus responsabilidades incluyen la supervisión del espacio libre en disco y la administración del contenido de los volúmenes. También es responsable de supervisar la ejecución correcta de las copias de seguridad de los volúmenes de disco y de las instantáneas de almacenamiento. La ejecución correcta de la replicación de datos en los sitios de recuperación ante desastres es responsabilidad de Microsoft.

**Aplicaciones**: las instancias de la aplicación de SAP o, en el caso de aplicaciones que no son de SAP, el nivel de aplicación de dichas aplicaciones. Sus responsabilidades incluyen la implementación, administración, operaciones y supervisión de esas aplicaciones. Usted es responsable de planear la capacidad de consumo de recursos de CPU, consumo de memoria, consumo de Azure Storage y consumo de ancho de banda de red dentro de las redes virtuales. También es responsable de planear la capacidad de consumo de recursos de redes virtuales para SAP HANA en Azure (instancias grandes).

**WAN**: las conexiones que establece entre la implementación local y las implementaciones de Azure para las cargas de trabajo. Todos los clientes con HANA (instancias grandes) usan Azure ExpressRoute para la conectividad. Esta conexión no forma parte de la solución SAP HANA en Azure (instancias grandes). Usted es responsable de la configuración de esta conexión.

**Archivo**: es posible que prefiera archivar copias de los datos con sus propios métodos en cuentas de almacenamiento. El archivado conlleva tareas de administración, cumplimiento normativo, costos y operaciones. Es su responsabilidad generar copias de archivo y copias de seguridad en Azure y almacenarlas con un método compatible.

Consulte el [Acuerdo de Nivel de Servicio para SAP HANA en Azure (Instancias grandes)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Ajuste de tamaño

El ajuste de tamaño para HANA (instancias grandes) es igual que para HANA en general. Para los sistemas existentes e implementados que desea trasladar de otro RDBMS a HANA, SAP proporciona una serie de informes que se ejecutan en los sistemas SAP existentes. Si la base de datos se traslada a HANA, estos informes comprueban los datos y calculan los requisitos de memoria para la instancia de HANA. Consulte las siguientes notas de SAP para más información sobre cómo ejecutar estos informes y cómo obtener las revisiones o versiones más recientes:

- [SAP Note #1793345 - Sizing for SAP Suite on HANA](https://launchpad.support.sap.com/#/notes/1793345) (Nota de SAP 1793345: Ajuste de tamaño para SAP Suite en HANA)
- [SAP Note #1872170 - Suite on HANA and S/4 HANA sizing report](https://launchpad.support.sap.com/#/notes/1872170) (Nota de SAP 1872170: Informe de ajuste de tamaño de Suite en HANA y S/4 HANA)
- [SAP Note #2121330 - FAQ: SAP BW on HANA Sizing Report](https://launchpad.support.sap.com/#/notes/2121330) (Nota de SAP 2121330: P+F: Informe de ajuste de tamaño de SAP BW en HANA)
- [SAP Note #1736976 - Sizing Report for BW on HANA](https://launchpad.support.sap.com/#/notes/1736976) (Nota de SAP 1736976: Informe de ajuste de tamaño para BW en HANA)
- [SAP Note #2296290 - New Sizing Report for BW on HANA](https://launchpad.support.sap.com/#/notes/2296290) (Nota de SAP 2296290: Nuevo informe de ajuste de tamaño para BW en HANA)

Para las implementaciones en green field, está disponible SAP Quick Sizer para calcular los requisitos de memoria de la implementación de software de SAP sobre HANA.

Los requisitos de memoria de HANA aumentan a medida que aumenta el volumen de datos. Tenga en cuenta el consumo de memoria actual, que le ayudará a predecir lo que va a ocurrir en el futuro. En función de los requisitos de memoria, puede asignar la demanda a una de las SKU de HANA (instancias grandes).

## <a name="requirements"></a>Requisitos

En esta lista se recopilan los requisitos para ejecutar SAP HANA en Azure (instancias grandes).

**Microsoft Azure**

- Una suscripción de Azure que se pueda vincular a SAP HANA en Azure (Instancias grandes).
- Contrato de soporte técnico Premier de Microsoft. Consulte [SAP Support Note #2015553 – SAP on Microsoft Azure: Support Prerequisites](https://launchpad.support.sap.com/#/notes/2015553) (Nota de SAP sobre soporte 2015553: Requisitos previos de soporte técnico de SAP en Microsoft Azure) para información específica relacionada con la ejecución de SAP en Azure. Si usa unidades de HANA (instancias grandes) con 384 o más CPU, también necesita extender el contrato de soporte técnico Premier para incluir Respuesta rápida de Azure.
- Conocimiento de las SKU de HANA (instancias grandes) que necesita después de realizar un ajuste de tamaño con SAP.

**Conectividad de red**

- ExpressRoute entre la infraestructura local y la de Azure: asegúrese de pedir a su ISP una conexión de 1 Gbps como mínimo para conectar el centro de datos local a Azure. 

**Sistema operativos**

- Licencias para SUSE Linux Enterprise Server 12 for SAP Applications.

   > [!NOTE] 
   > El sistema operativo entregado por Microsoft no está registrado con SUSE. No está conectado a una instancia de la herramienta de administración de suscripciones.

- SUSE Linux Subscription Management Tool implementada en Azure en una máquina virtual. Esta herramienta proporciona la funcionalidad para que SAP HANA en Azure (instancias grandes) sea registrado y actualizado respectivamente por SUSE. (No hay ningún acceso a internet en el centro de datos de HANA [instancias grandes]). 
- Licencias para Red Hat Enterprise Linux 6.7 o 7.2 para SAP HANA.

   > [!NOTE]
   > El sistema operativo entregado por Microsoft no está registrado con Red Hat. No está conectado a una instancia del administrador de suscripciones de Red Hat.

- Red Hat Subscription Manager implementado en Azure en una máquina virtual. Red Hat Subscription Manager proporciona la funcionalidad para que SAP HANA en Azure (instancias grandes) sea registrado y actualizado respectivamente por Red Hat. (No hay ningún acceso directo a Internet desde el inquilino implementado en la demarcación de instancias grandes de Azure).
- SAP requiere que también disponga de un contrato de soporte con el proveedor de Linux. Este requisito no se elimina con la solución de HANA (instancias grandes) ni por el hecho de ejecutar Linux en Azure. A diferencia de lo que ocurre con algunas de las imágenes de la galería de Azure para Linux, la tarifa del servicio *no* se incluye en la oferta de la solución HANA (instancias grandes). Es su responsabilidad cumplir los requisitos de SAP en relación con los contratos de soporte técnico con el distribuidor de Linux. 
   - Si se trata de SUSE Linux, busque los requisitos del contrato de soporte técnico en [SAP Note #1984787 - SUSE Linux Enterprise Server 12: Installation notes](https://launchpad.support.sap.com/#/notes/1984787) (Nota de SAP 1984787: SUSE Linux Enterprise Server 12: notas de instalación) y [SAP Note #1056161 - SUSE Priority Support for SAP applications](https://launchpad.support.sap.com/#/notes/1056161) (Nota de SAP 1056161: Soporte con prioridad de SUSE para aplicaciones SAP).
   - Para Red Hat Linux, necesita tener los niveles de suscripción adecuados que incluyan soporte técnico y actualizaciones del servicio de los sistemas operativos de HANA (instancias grandes). Red Hat recomienda Red Hat Enterprise Linux para [soluciones de SAP] (suscripción https://access.redhat.com/solutions/3082481. 

Para la matriz de compatibilidad de las distintas versiones de SAP HANA con las diferentes versiones de Linux, consulte la [Nota de SAP 2235581](https://launchpad.support.sap.com/#/notes/2235581).


**Base de datos**

- Las licencias y los componentes de instalación de software para SAP HANA (Platform Edition o Enterprise Edition).

**Applications**

- Licencias y componentes de instalación de software para todas las aplicaciones de SAP que se conecten a SAP HANA y contratos de soporte de SAP relacionados.
- Las licencias y componentes de instalación de software para aplicaciones que no sean de SAP utilizadas en relación con el entorno de SAP HANA en Azure (instancias grandes) y los contratos de soporte relacionados.

**Habilidades**

- Experiencia y conocimientos sobre IaaS de Azure y sus componentes.
- Experiencia y conocimientos sobre cómo implementar una carga de trabajo SAP en Azure.
- Personal certificado para la instalación de SAP HANA.
- Conocimientos de la arquitectura de SAP para diseñar la alta disponibilidad y la recuperación ante desastres de SAP HANA.

**SAP**

- Se espera que sea un cliente de SAP y que tenga un contrato de soporte técnico con SAP.
- Especialmente para las implementaciones de las SKU de clase de tipo II de HANA (instancias grandes), consulte con SAP las versiones de SAP HANA y las configuraciones posibles en hardware de escalado vertical de tamaño grande.


## <a name="storage"></a>Storage

El diseño de almacenamiento para SAP HANA en Azure (instancias grandes) es configurado por SAP HANA en el modelo de implementación clásica a través de las instrucciones recomendadas de SAP. Las instrucciones se documentan en las notas del producto [Requisitos de almacenamiento de SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

La clase de tipo I de HANA (instancias grandes) suele tener cuatro veces el volumen de memoria como volumen de almacenamiento. Para las unidades de HANA (instancias grandes) de clase de tipo II, el almacenamiento no es cuatro veces superior. Las unidades tienen un volumen previsto para almacenar copias de seguridad del registro de transacciones de HANA. Para más información, consulte [Instalación y configuración de SAP HANA en Azure (instancias grandes)](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Vea la tabla siguiente en relación a la asignación de almacenamiento. En la tabla se enumera la capacidad aproximada de los diferentes volúmenes que se proporcionan con las diferentes unidades de HANA (instancias grandes).

| SKU de Instancias grandes de HANA | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| S72 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3 328 GB | 768 GB |1 280 GB | 768 GB |
| S192 | 4 608 GB | 1 024 GB | 1 536 GB | 1 024 GB |
| S192m | 11.520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S384 | 11.520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S384m | 12.000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xm | 16.000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S576 | 20.000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S768 | 28.000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S960 | 36.000 GB | 4 100 GB | 2 050 GB | 4 100 GB |


Los volúmenes implementados reales pueden variar en función de la implementación y la herramienta que se usa para mostrar el tamaño de los volúmenes.

Si subdivide una SKU de HANA (instancias grandes), algunos ejemplos de los posibles fragmentos de división serían como sigue:

| Partición de memoria en GB | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| 1024 | 1 792 GB | 640 GB | 1 024 GB | 640 GB |
| 1536 | 3 328 GB | 768 GB | 1 280 GB | 768 GB |


Estos tamaños son números de volumen aproximados que pueden variar ligeramente en función de la implementación y las herramientas que se usan para analizar los volúmenes. También hay otros tamaños de partición, como 2,5 TB. Estos tamaños de almacenamiento se calculan con una fórmula similar a la utilizada para las particiones anteriores. El término "particiones" no indica en absoluto que el sistema operativo, la memoria o los recursos de CPU se dividan en particiones. Indica las particiones de almacenamiento para las distintas instancias de HANA que es posible que quiera implementar en una única unidad de HANA (instancias grandes). 

Podría necesitar más almacenamiento. Puede agregar almacenamiento comprando almacenamiento adicional en unidades de 1 TB. Este almacenamiento adicional se puede agregar como volumen adicional. También se puede utilizar para extender uno o varios de los volúmenes existentes. No es posible reducir el tamaño de los volúmenes que implementó originalmente y que se documentan principalmente en las tablas anteriores. Tampoco es posible cambiar los nombres de los volúmenes ni los de montaje. Los volúmenes de almacenamiento descritos anteriormente se conectan a las unidades de HANA (instancias grandes) como volúmenes NFS4.

Puede usar instantáneas de almacenamiento para la copia de seguridad y la restauración, además de para la recuperación ante desastres. Para más información, consulte [Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes)](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="encryption-of-data-at-rest"></a>Cifrado de datos en reposo
El almacenamiento usado para HANA (instancias grandes) permite un cifrado transparente de los datos cuando se almacenan en los discos. Cuando se implementa una unidad de HANA (instancias grandes), puede habilitar a este tipo de cifrado. También puede cambiar a volúmenes cifrados después de que se complete la implementación. El movimiento de volúmenes no cifrados a cifrados es transparente y no requiere ningún tiempo de inactividad. 

Con las SKU de clase de tipo I, se cifra el volumen en el que está almacenado el LUN de arranque. En el caso de las SKU de clase de tipo II de HANA (instancias grandes), tendrá que cifrar el LUN de arranque con los métodos del sistema operativo. Para más información, póngase en contacto con el equipo de Microsoft Service Management.


## <a name="networking"></a>Redes

La arquitectura del servicio de red de Azure es un componente clave para implementar las aplicaciones de SAP de forma correcta en HANA (instancias grandes). Por lo general, las implementaciones de SAP HANA en Azure (Instancias grandes) tienen un entorno de SAP más amplio con varias soluciones SAP distintas y diversos tamaños de base de datos, consumo de recursos de CPU y utilización de memoria. Es probable que no todos los sistemas SAP estén basados en SAP HANA. La infraestructura de SAP probablemente es un híbrido que usa:

- Sistemas SAP implementados localmente. Debido a sus tamaños, estos sistemas actualmente no se pueden hospedar en Azure. Un ejemplo es un sistema SAP ERP de producción que se ejecuta en SQL Server (como base de datos) y requiere más recursos de CPU o memoria de los que pueden proporcionar las máquinas virtuales.
- Sistemas SAP basados en SAP HANA implementados localmente.
- Sistemas SAP implementados en máquinas virtuales. Estos sistemas pueden ser instancias de desarrollo, pruebas, espacio aislado o producción para cualquiera de las aplicaciones basadas en SAP NetWeaver que se pueden implementar correctamente en Azure (en máquinas virtuales), en función de la demanda de memoria y el consumo de recursos. Estos sistemas también pueden basarse en bases de datos como SQL Server. Para más información, consulte [SAP Support Note #1928533 – SAP applications on Azure: Supported products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533/E) (Nota de SAP 1928533: Aplicaciones de SAP en Azure: tipos de máquina virtual de Azure y productos compatibles). Y estos sistemas también pueden basarse en bases de datos como SAP HANA. Para más información, consulte [Plataformas de IaaS certificadas para SAP HANA](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
- Servidores de aplicaciones de SAP implementados en Azure (en máquinas virtuales) que aprovechan SAP HANA en Azure (instancias grandes) en demarcaciones de instancias grandes de Azure.

Es normal una infraestructura de SAP con cuatro o más escenarios de implementación diferentes. Hay también muchos casos de cliente de infraestructuras de SAP completas que se ejecutan en Azure. A medida que las máquinas virtuales se vuelven más eficaces, aumenta el número de clientes que trasladan todas sus soluciones SAP a Azure.

Las redes de Azure en el contexto de los sistemas SAP implementados en Azure no son complicadas. Se basan en los siguientes principios:

- Las redes virtuales de Azure deben estar conectadas al circuito ExpressRoute que se conecta a la red local.
- Normalmente, un circuito ExpressRoute que conecta local y Azure necesitaría un ancho de banda de 1 Gbps o más. Este ancho de banda mínimo permite suficiente ancho de banda para la transferencia de datos entre los sistemas locales y los sistemas que se ejecutan en máquinas virtuales. También permite un ancho de banda adecuado para la conexión de los usuarios locales a los sistemas de Azure.
- Todos los sistemas SAP en Azure deben configurarse en redes virtuales para poder comunicarse entre sí.
- Active Directory y DNS hospedados localmente se extienden a Azure a través de ExpressRoute desde un entorno local.


> [!NOTE] 
> Desde un punto de vista facturación, solo se puede vincular una suscripción de Azure a un único inquilino en una demarcación de instancias grandes de una región de Azure específica. A la inversa, se puede vincular solo un inquilino de la demarcación de instancias grandes a una única suscripción de Azure. Este requisito es coherente con otros objetos facturables en Azure.

Si se implementa SAP HANA en Azure (instancias grandes) en varias regiones de Azure diferentes, se implementará un inquilino diferente en la demarcación de instancias grandes. Puede ejecutar las dos en la misma suscripción de Azure, siempre que estas instancias formen parte de la misma infraestructura de SAP. 

> [!IMPORTANT] 
> La única implementación compatible con SAP HANA en Azure (instancias grandes) es la de Azure Resource Manager.

 

### <a name="additional-virtual-network-information"></a>Información adicional sobre la red virtual

Para conectar una red virtual a ExpressRoute, se debe crear una puerta de enlace de Azure. Para más información, consulte [Puertas de enlace de red virtual para ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 

Se puede usar una puerta de enlace de Azure con ExpressRoute para una infraestructura fuera de Azure o para una demarcación de instancias grandes de Azure. Una puerta de enlace de Azure también se puede usar para la conexión entre redes virtuales. Para más información, consulte [Configuración de una conexión de red a red para el Administrador de recursos mediante PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Puede conectar la puerta de enlace de Azure a un máximo de cuatro conexiones de ExpressRoute diferentes, siempre que procedan de diferentes enrutadores perimetrales empresariales de Microsoft. Para más información, consulte [Infraestructura y conectividad con SAP HANA en Azure (instancias grandes)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> El rendimiento proporcionado por una puerta de enlace de Azure es distinto para ambos casos de uso. Para más información, consulte [Acerca de VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). El rendimiento máximo que se puede lograr con una puerta de enlace de red virtual es de 10 Gbps, mediante una conexión de ExpressRoute. La copia de archivos entre una máquina virtual que resida en una red virtual y un sistema local (como una sola secuencia de copia) no consigue el rendimiento total de las diferentes SKU de puerta de enlace. Para aprovechar el ancho de banda completo de la puerta de enlace de red virtual, use varias secuencias. O bien, debe copiar archivos diferentes en secuencias paralelas de un único archivo.


### <a name="networking-architecture-for-hana-large-instance"></a>Arquitectura de redes de HANA (instancias grandes)
La arquitectura de redes de HANA (instancias grandes) se puede dividir en cuatro partes distintas:

- Redes locales y conexión de ExpressRoute a Azure. Esta parte es el dominio de los clientes y se conecta a Azure a través de ExpressRoute. Consulte la parte inferior derecha en la ilustración siguiente.
- Servicios de red de Azure, como se explicó anteriormente con las redes virtuales, que tienen de nuevo puertas de enlace. Esta parte es un área en la que debe buscar los diseños adecuados para los requisitos de las aplicaciones, de seguridad y de cumplimiento. Si usa HANA (instancias grandes) es otro punto a tener en cuenta en cuanto al número de redes virtuales y de SKU de puerta de enlace de Azure entre los que elegir. Observe la parte superior derecha de la ilustración.
- Conectividad de HANA (instancias grandes) a través de la tecnología de ExpressRoute en Azure. Microsoft implementa y controla esta parte. Todo lo que necesita hacer es proporcionar algunos intervalos de direcciones IP después de la implementación de los recursos de HANA (instancias grandes) que conectan el circuito ExpressRoute a las redes virtuales. Para más información, consulte [Infraestructura y conectividad con SAP HANA en Azure (instancias grandes)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Redes en HANA (instancias grandes), las cuales son principalmente transparentes para usted.

![Red virtual conectada a SAP HANA en Azure (instancias grandes) y localmente](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

El hecho de que use HANA (instancias grandes) no cambia el requisito de tener que conectar los recursos locales a Azure a través de ExpressRoute. No cambia tampoco el requisito de tener una o varias redes virtuales que ejecutan las máquinas virtuales que hospedan el nivel de aplicación que se conecta a las instancias de HANA que se hospedan en unidades de HANA (instancias grandes). 

Las diferencias en las implementaciones de SAP en Azure son:

- Las unidades de HANA (instancias grandes) del inquilino del cliente se conectan a las redes virtuales través de otro circuito ExpressRoute. Con el fin de separar las condiciones de carga, los vínculos de ExpressRoute del entorno local a las redes virtuales y los vínculos entre las redes virtuales y HANA (instancias grandes) no comparten los mismos enrutadores.
- La naturaleza del perfil de carga de trabajo entre el nivel de aplicación de SAP y HANA (instancias grandes) es diferente y tiene muchas solicitudes pequeñas y transferencias de datos como ráfagas (conjuntos de resultados) desde SAP HANA al nivel de aplicación.
- La arquitectura de aplicación de SAP es más sensible a la latencia de la red que los escenarios típicos donde los datos se intercambian entre el entorno local y Azure.
- La puerta de enlace de red virtual tiene al menos dos conexiones de ExpressRoute. Las dos conexiones comparten el ancho de banda máximo para los datos entrantes de la puerta de enlace de red virtual.

La latencia de red que se experimenta entre las máquinas virtuales y las unidades de HANA (instancias grandes) puede ser mayor que una latencia de red de recorrido de ida y vuelta entre máquinas virtuales. En función de la región de Azure, los valores medidos pueden superar la latencia de recorrido de ida y vuelta de 0,7 ms clasificada como por debajo del promedio en [SAP Note #1100926 - FAQ: Network performance](https://launchpad.support.sap.com/#/notes/1100926/E) (Nota de SAP 1100926: Preguntas más frecuentes sobre el rendimiento de red). Sin embargo, los clientes han implementado correctamente aplicaciones de SAP de producción basadas en SAP HANA en SAP HANA (instancias grandes). Los clientes que realizaron la implementación notificaron grandes mejoras al ejecutar sus aplicaciones SAP en SAP HANA con unidades de HANA (instancias grandes). Asegúrese de probar exhaustivamente los procesos empresariales en HANA en Azure (instancias grandes).
 
Para proporcionar latencia de red determinista entre las máquinas virtuales y HANA (instancias grandes), la elección de la SKU de la puerta de enlace de red virtual es esencial. A diferencia de los patrones de tráfico entre el entorno local y las máquinas virtuales, el patrón de tráfico entre las máquinas virtuales y HANA (instancias grandes) puede generar pequeñas pero elevadas ráfagas de solicitudes y volúmenes de datos para transmitir. Para poder controlar correctamente esas ráfagas, se recomienda el uso de la SKU de puerta de enlace UltraPerformance. Para las SKU de clase de tipo II de HANA (instancias grandes), es obligatorio el uso de la SKU de puerta de enlace UltraPerformance como puerta de enlace de red virtual.

> [!IMPORTANT] 
> Dado el tráfico de red general entre la capa de base de datos y la de la aplicación de SAP, solo se admiten las SKU de puerta de enlace HighPerformance o UltraPerformance para redes virtuales para la conexión a SAP HANA en Azure (instancias grandes). Para las SKU de la clase de tipo II de HANA (instancias grandes), solo se admite la SKU de puerta de enlace UltraPerformance como puerta de enlace de red virtual.



### <a name="single-sap-system"></a>Único sistema SAP

La infraestructura local mostrada anteriormente se conecta a través de ExpressRoute con Azure. El circuito ExpressRoute se conecta a un enrutador perimetral de empresa. Para más información, consulte [Información técnica sobre ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Una vez establecida, esa ruta se conecta a la red troncal de Azure y son accesibles todas las regiones de Azure.

> [!NOTE] 
> Para ejecutar infraestructuras de SAP en Azure, conéctese al enrutador perimetral de empresa más cercano a la región de Azure en la infraestructura de SAP. Las demarcaciones de instancias grandes de Azure están conectadas a través de dispositivos enrutadores perimetrales de empresa dedicados para minimizar la latencia de red entre las máquinas virtuales de IaaS de Azure y las demarcaciones de grandes instancias.

La puerta de enlace de red virtual de las máquinas virtuales que hospedan instancias de la aplicación de SAP está conectada al circuito ExpressRoute. La misma red virtual está conectada a un enrutador perimetral de empresa independiente dedicado para la conexión con las demarcaciones de instancias grandes.

Este sistema es un ejemplo sencillo de un único sistema SAP. El nivel de aplicación de SAP se hospeda en Azure. La base de datos de SAP HANA se ejecuta en SAP HANA en Azure (instancias grandes). Se supone que el ancho de banda de la puerta de enlace de red virtual de 2 Gbps o 10 Gbps de rendimiento no representa un cuello de botella.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Varios sistemas SAP o sistemas SAP grandes

Si se implementan varios sistemas SAP o sistemas SAP grandes para su conexión con SAP HANA en Azure (instancias grandes), el rendimiento de la puerta de enlace de red virtual podría convertirse en un cuello de botella. En ese caso, debe dividir los niveles de aplicación en varias redes virtuales. También puede crear una red virtual especial que se conecta a HANA (instancias grandes) para casos tales como:

- Realizar copias de seguridad directamente desde las instancias de HANA en HANA (instancias grandes) a una máquina virtual de Azure que hospeda los recursos compartidos de NFS.
- Copiar las copias de seguridad de gran tamaño u otros archivos de unidades de instancias grandes de HANA en espacio en disco administrado en Azure.

Usar una red virtual independiente para hospedar las máquinas virtuales que administran el almacenamiento. Esta disposición evita los efectos de las grandes transferencias de archivos o datos desde HANA (instancias grandes) a Azure sobre la puerta de enlace de red virtual que sirve las máquinas virtuales que ejecutan el nivel de aplicación de SAP. 

Para una arquitectura de red más escalable:

- Aproveche varias redes virtuales para un solo nivel de aplicación de SAP más grande.
- Implemente una red virtual distinta para cada sistema SAP implementado, en comparación con la combinación de estos sistemas SAP en subredes independientes en la misma red virtual.

 Una arquitectura de red más escalable para SAP HANA en Azure (Instancias grandes):

![Implementación del nivel de aplicación de SAP en varias redes virtuales](./media/hana-overview-architecture/image4-networking-architecture.png)

La ilustración muestra el nivel de aplicación de SAP y los componentes, que se implementan a través de varias redes virtuales. Esta configuración presenta una sobrecarga de latencia inevitable que se produce durante la comunicación entre las aplicaciones hospedadas en esas redes virtuales. De manera predeterminada, el tráfico de red entre máquinas virtuales ubicadas en diferentes redes virtuales discurrirá a través de enrutadores perimetrales de empresa en esta configuración. Desde septiembre de 2016 es posible optimizar este enrutamiento. 

La manera de optimizar y reducir la latencia en la comunicación entre dos redes virtuales pasa por el emparejamiento de las redes virtuales que estén en la misma región. Este método funciona incluso si esas redes virtuales están en distintas suscripciones. Gracias al emparejamiento de redes virtuales, la comunicación entre máquinas virtuales en dos redes virtuales diferentes puede emplear la red troncal de Azure para permitir una comunicación directa entre ambas. La latencia se muestra como si las máquinas virtuales estuvieran en la misma red virtual. El tráfico que se dirige a los intervalos de direcciones IP que están conectados a través de la puerta de enlace de red virtual se enruta a través de la puerta de enlace de red virtual individual de dicha red. 

Para más información sobre el emparejamiento de redes virtuales, consulte [Emparejamiento de redes virtuales](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Enrutamiento en Azure

Hay tres consideraciones importantes sobre el enrutamiento de red para SAP HANA en Azure (instancias grandes):

* Las máquinas virtuales solo pueden acceder a SAP HANA en Azure (instancias grandes) en la conexión ExpressRoute dedicada, no directamente desde el entorno local. El acceso directo desde local a las unidades de HANA (instancias grandes), tal como lo entrega Microsoft, no es posible inmediatamente. Las restricciones de enrutamientos transitivas son debidas a la arquitectura de red de Azure actual utilizada para SAP HANA (instancias grandes). Los clientes de administración y las aplicaciones que necesiten acceso directo, como SAP Solution Manager ejecutado de forma local, no se pueden conectar a la base de datos de SAP HANA.

* Si tiene unidades de HANA (instancias grandes) implementadas en dos regiones de Azure distintas para la recuperación ante desastres, se aplican las mismas restricciones en el enrutamiento transitorio. En otras palabras, las direcciones IP de una unidad de HANA (instancias grandes) en una región (por ejemplo, Oeste de EE. UU.) no se enrutarán a una unidad de HANA (instancias grandes) implementada en otra región (por ejemplo, Este de EE. UU.). Esta restricción es independiente del uso del emparejamiento de redes de Azure entre regiones o de los circuitos ExpressRoute que conectan las unidades de HANA (instancias grandes) con las redes virtuales. Para ver una representación gráfica, consulte la ilustración de la sección "Uso de unidades de HANA (instancias grandes) en varias regiones". Esta restricción, que se incluye con la arquitectura implementada, prohíbe el uso inmediato de la replicación del sistema de HANA como funcionalidad de recuperación ante desastres.

* Las unidades de SAP HANA en Azure (instancias grandes) tienen una dirección IP asignada desde el intervalo de direcciones del grupo IP de servidor que ha enviado. Para más información, consulte [Infraestructura y conectividad con SAP HANA en Azure (instancias grandes)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Se puede acceder a esta dirección IP a través de las suscripciones de Azure y de la conexión ExpressRoute que conecta las redes virtuales a HANA en Azure (instancias grandes). La dirección IP asignada fuera del intervalo de direcciones del grupo IP de servidor se asigna directamente a la unidad de hardware. Ya *no* se asigna mediante NAT, como sucedía en las primeras implementaciones de esta solución. 

> [!NOTE] 
> Para superar la limitación del enrutamiento transitorio como se explicó en los dos primeros elementos de la lista anterior, debe utilizar componentes adicionales para el enrutamiento. Los componentes que se pueden usar para superar la restricción pueden ser:

> * Un servidor proxy inverso para enrutar los datos, desde y hacia. Por ejemplo, F5 BIG-IP, NGINX con Traffic Manager implementado en Azure como solución de enrutamiento de tráfico y firewall virtual.
> * Usar [reglas de IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) en una máquina virtual Linux para habilitar el enrutamiento entre las ubicaciones locales y las unidades de HANA (instancias grandes) o entre unidades de HANA (instancias grandes) en regiones diferentes.

> Tenga en cuenta que Microsoft no proporciona la implementación y soporte técnico para las soluciones personalizadas que tienen dispositivos de red de terceros o IPTables. El proveedor del componente utilizado o el integrador es el que debe proporcionar el soporte técnico. 

### <a name="internet-connectivity-of-hana-large-instance"></a>Conectividad a Internet de HANA (instancias grandes)
HANA (instancias grandes) *NO* tiene conectividad directa a Internet. Por ejemplo, esta limitación puede restringir la capacidad de registrar la imagen del sistema operativo directamente con el proveedor del sistema operativo. Debe trabajar con la herramienta de administración de suscripciones de SUSE Linux Enterprise Server local o con el administrador de suscripciones de Red Hat Enterprise Linux.

### <a name="data-encryption-between-vms-and-hana-large-instance"></a>Cifrado de datos entre las máquinas virtuales y HANA (instancias grandes)
Los datos transferidos entre HANA (instancias grandes) y las máquinas virtuales no se cifran. Sin embargo, solo para el intercambio entre el DBMS de HANA y las aplicaciones basadas en JDBC y ODBC, puede habilitar el cifrado del tráfico. Para más información, consulte [esta documentación de SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

### <a name="use-hana-large-instance-units-in-multiple-regions"></a>Uso de unidades de HANA (instancias grandes) en varias regiones

Pueden existir razones para implementar SAP HANA en Azure (instancias grandes) en varias regiones de Azure además de la recuperación ante desastres. Es posible que desee acceder a HANA (instancias grandes) desde cada una de las máquinas virtuales implementadas en las diferentes redes virtuales en las regiones. Las direcciones IP asignadas a las diferentes unidades de HANA (instancias grandes) no se propagan más allá de las redes virtuales que están conectadas directamente a través de su puerta de enlace a las instancias. Como resultado, se presenta un pequeño cambio en el diseño de la red virtual. Una puerta de enlace de red virtual puede controlar cuatro circuitos ExpressRoute diferentes fuera de los diferentes enrutadores perimetrales empresariales. Cada red virtual que está conectada a una de las demarcaciones de instancias grandes puede estar conectada a la demarcación de instancias grandes en otra región de Azure.

![Redes virtuales conectadas a demarcaciones de instancias grandes de Azure en diferentes regiones de Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

En la ilustración se muestra cómo las distintas redes virtuales en ambas regiones están conectadas a dos circuitos ExpressRoute diferentes que se utilizan para conectarse a SAP HANA en Azure (instancias grandes) en ambas regiones de Azure. Las conexiones recién introducidas son las líneas rojas rectangulares. Con estas conexiones, fuera de las redes virtuales, las máquinas virtuales que se ejecutan en una de esas redes virtuales pueden tener acceso a cada una de las diferentes unidades de HANA (instancias grandes) implementadas en las dos regiones. Como se muestra en la ilustración, se supone que tiene dos conexiones ExpressRoute desde local a las dos regiones de Azure. Esta disposición se recomienda por motivos de recuperación ante desastres.

> [!IMPORTANT] 
> Si se usan varios circuitos ExpressRoute, deben utilizarse las configuraciones de anteposición de AS Path y de preferencia local de BGP para garantizar el enrutamiento adecuado del tráfico.


