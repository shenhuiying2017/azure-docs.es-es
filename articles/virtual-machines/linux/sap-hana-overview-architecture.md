---
title: "Introducción y arquitectura de SAP HANA en Azure (Instancias grandes) | Microsoft Docs"
description: "Introducción a la arquitectura para implementar SAP HANA en Azure (Instancias grandes)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fa842efd99718be7fa9eaf8aac8030c32cbceeec
ms.openlocfilehash: a412304c33d915be6e30c6b748c8431a9c7e9f34
ms.lasthandoff: 03/01/2017


---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Introducción y arquitectura de SAP HANA en Azure (instancias grandes) 
En esta guía de implementación técnica y arquitectura en cinco partes, se proporciona información que lo ayudará a implementar SAP en la nueva oferta SAP HANA en Azure (Instancias grandes) en Azure. No es exhaustiva ni trata detalles específicos sobre la configuración de soluciones SAP. En su lugar, proporciona información útil para ayudar con la implementación inicial y las operaciones continuadas. No debe usarse en lugar de la documentación de SAP sobre la instalación de SAP HANA (ni las muchas notas de soporte técnico de SAP que tratan sobre el tema). Además, proporciona detalles sobre la instalación de SAP HANA en Azure (Instancias grandes).


En las cinco partes de esta guía se tratan los temas siguientes:

- [Introducción y arquitectura](sap-hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infraestructura y conectividad](sap-hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Instalación de SAP HANA](sap-hana-overview-sap-hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Alta disponibilidad y recuperación ante desastres](sap-hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Supervisión y solución de problemas](sap-hana-overview-troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="definitions"></a>Definiciones

En la Guía de implementación técnica y arquitectura, se utilizan con frecuencia varias definiciones habituales. Tenga en cuenta los siguientes términos y sus significados:

- **IaaS**: infraestructura como servicio
- **PaaS**: plataforma como servicio
- **SaaS**: software como servicio
- **Componente de SAP**: una aplicación de SAP individual, como ECC, BW, Solution Manager o EP. Los componentes de SAP pueden basarse en tecnologías tradicionales, como ABAP o Java, o en una aplicación no basada en NetWeaver, como Business Objects.
- **Entorno de SAP**: uno o varios componentes de SAP agrupados lógicamente para desempeñar una función empresarial, como desarrollo, control de calidad, aprendizaje, recuperación ante desastres o producción.
- **Infraestructura de SAP**: hace referencia a todos los recursos de SAP de su infraestructura de TI. La infraestructura de SAP incluye todos los entornos, tanto los que son de producción como los que no.
- **Sistema SAP**: la combinación de la capa de DBMS y la de la aplicación de un sistema de desarrollo SAP ERP, un sistema de prueba SAP BW, un sistema de producción SAP CRM, etc. Las implementaciones de Azure no admiten la división de estas dos capas entre la infraestructura local y la de Azure. Esto significa que un sistema SAP debe implementarse de forma local o en Azure, pero no en ambos. Sin embargo, los diferentes sistemas de una infraestructura de SAP pueden implementarse en Azure o de forma local. Por ejemplo, podría implementar los sistemas de prueba y desarrollo SAP CRM en Azure, mientras que el sistema de producción SAP CRM se implementa de manera local. En el caso de SAP HANA en Azure (Instancias grandes), se admite la ejecución de la capa de la aplicación de SAP de sistemas SAP en máquinas virtuales de Azure y de la instancia de HANA en una unidad en el sello de instancias grandes.
- **Sello de instancias grandes:** una pila de infraestructura de hardware que está certificada para SAP HANA TDI y se dedica a ejecutar instancias de SAP HANA dentro de Azure.
- **SAP HANA en Azure (Instancias grandes):** nombre oficial de la oferta de Azure para ejecutar instancias de HANA en hardware con certificación SAP HANA TDI que se implementa en sellos de instancias grandes en diferentes regiones de Azure. El término relacionado **Instancias grandes de HANA** es la versión abreviada de SAP HANA en Azure (Instancias grandes) y se usa con frecuencia en esta guía de implementación técnica.
- **Entre locales**: describe un escenario en el que se implementan máquinas virtuales en una suscripción de Azure con conectividad de sitio a sitio, entre varios sitios o de ExpressRoute entre el centro o los centros de datos locales y Azure. En la documentación habitual de Azure, este tipo de implementaciones se denominan "escenarios entre locales". La razón tras la conexión es extender los dominios locales, Active Directory/OpenLDAP locales y DNS local a Azure. La infraestructura local se extiende a los recursos de Azure de la suscripción. Con esta extensión, las máquinas virtuales pueden formar parte del dominio local. Los usuarios del dominio local pueden tener acceso a los servidores y ejecutar servicios en esas máquinas virtuales (por ejemplo, servicios de DBMS). Es posible la comunicación y resolución de nombres entre máquinas virtuales implementadas de forma local y en Azure. Se trata del escenario típico en que se implementarán la mayoría de los recursos de SAP. Consulte [Planeamiento y diseño de VPN Gateway](../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) y [Creación de una red virtual con una conexión de sitio a sitio mediante Azure Portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para más información.

Además, se han publicado varios recursos adicionales sobre el tema de la implementación de la carga de trabajo de SAP en la nube pública de Microsoft Azure. Es muy recomendable que quien planee una implementación de SAP HANA en Azure posea conocimientos y experiencia con los principios de IaaS de Azure y la implementación de cargas de trabajo de SAP en IaaS de Azure. En los siguientes recursos se proporciona más información, que se debe consultar antes de continuar:

- [Uso de SAP en máquinas virtuales (VM) Windows](../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Uso de soluciones SAP en máquinas virtuales de Microsoft Azure](../virtual-machines-linux-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="certification"></a>Certificación

Además de la certificación NetWeaver, SAP requiere una certificación especial de SAP HANA para admitir SAP HANA en determinadas infraestructuras, como IaaS de Azure.

La nota fundamental de SAP sobre NetWeaver, y hasta cierto punto sobre la certificación de SAP HANA, es [SAP Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (Nota de SAP 1928533: Aplicaciones de SAP en Azure: productos y tipos de máquina virtual de Azure admitidos).

Esta nota [SAP Note #2316233 - SAP HANA on Microsoft Azure (Large Instances)](https://launchpad.support.sap.com/#/notes/2316233/E) (Nota de SAP 2316233: SAP HANA en Microsoft Azure [Instancias grandes]) también es relevante. Trata sobre la solución descrita en esta guía. Además, se admite la ejecución de SAP HANA en el tipo de máquina virtual de Azure GS5. [La información para este caso está publicada en el sitio web de SAP](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

La solución SAP HANA en Azure (Instancias grandes) mencionada en la nota de SAP 2316233 proporciona a los clientes de Microsoft y SAP la capacidad de implementar cargas de trabajo grandes de SAP Business Suite, SAP Business Warehouse (BW), S/4 HANA, BW/4HANA u otras cargas de trabajo de SAP HANA en Azure. Esto se basa en el sello de hardware dedicado con certificación de SAP HANA ([Integración adaptada de centro de datos de SAP HANA o TDI](https://scn.sap.com/docs/DOC-63140)). La ejecución como solución configurada SAP TDI le proporciona la confianza de saber que funcionarán todas las aplicaciones basadas en SAP HANA (incluidas SAP Business Suite en SAP HANA, SAP Business Warehouse (BW) en SAP HANA, S4/HANA y BW4/HANA).

En comparación con la ejecución de SAP HANA en Azure Virtual Machines, esta solución cuenta con la ventaja de proporcionar volúmenes de memoria mucho más grandes. Si desea habilitar esta solución, debe comprender algunos aspectos clave:

- La capa de la aplicación de SAP y las aplicaciones que no son de SAP se ejecutan en Azure Virtual Machines (VM) que se hospedan en los sellos de hardware de Azure habituales.
- La infraestructura local, los centros de datos y las implementaciones de aplicaciones del cliente se conectan a la plataforma en la nube de Microsoft Azure mediante Azure ExpressRoute (recomendado) o una red privada virtual (VPN). Active Directory (AD) y DNS también se extienden a Azure.
- La instancia de base de datos de SAP HANA para cargas de trabajo de HANA se ejecuta en SAP HANA en Azure (Instancias grandes). El sello de instancias grandes está conectado a redes de Azure, por lo que el software que se ejecuta en las máquinas virtuales de Azure puede interactuar con la instancia de HANA que se ejecuta en Instancias grandes de HANA.
- El hardware de SAP HANA en Azure (Instancias grandes) es hardware dedicado que se proporciona en una infraestructura como servicio (IaaS) con SUSE Linux Enterprise Server o Red Hat Enterprise Linux preinstalado. Al igual que con Azure Virtual Machines, es responsabilidad suya llevar a cabo las actualizaciones y el mantenimiento del sistema operativo.
- La instalación de HANA o de otros componentes necesarios para ejecutar SAP HANA en las unidades de Instancias grandes de HANA es responsabilidad suya, al igual que lo son las operaciones continuadas y las administraciones de SAP HANA en Azure.
- Además de las soluciones descritas aquí, puede instalar otros componentes en la suscripción de Azure conectada a SAP HANA en Azure (Instancias grandes).  Por ejemplo, los componentes que permiten la comunicación, directa o no, con la base de datos de SAP HANA (servidores de salto, servidores RDP, SAP HANA Studio, SAP Data Services para escenarios de BI de SAP o soluciones de supervisión de red).
- Como en Azure, Instancias grandes de HANA ofrece funcionalidades auxiliares para habilitar la alta disponibilidad y la recuperación ante desastres.

## <a name="architecture"></a>Arquitectura

En un nivel alto, en la solución SAP HANA en Azure (Instancias grandes), la capa de la aplicación de SAP reside en máquinas virtuales de Azure y la capa de base de datos, en hardware configurado con SAP TDI y ubicado en un sello de instancias grandes en la misma región de Azure que la conectada a IaaS de Azure.

> [!NOTE]
> La capa de la aplicación de SAP debe implementarse en la misma región de Azure que la capa de DBMS de SAP. Esto está bien documentado en los datos publicados sobre la carga de trabajo de SAP en Azure.

La arquitectura global de SAP HANA en Azure (Instancias grandes) proporciona que una configuración de hardware con certificación SAP TDI (servidor de alto rendimiento sin sistema operativo ni virtualización para la base de datos de SAP HANA), así como la capacidad y flexibilidad de Azure para escalar los recursos de la capa de la aplicación de SAP según sus necesidades.

![Introducción a la arquitectura de SAP HANA en Azure (Instancias grandes)](./media/sap-hana-overview-architecture/image1-architecture.png)

La arquitectura anterior se divide en tres secciones:

- **Derecha:** una infraestructura local donde se ejecutan diferentes aplicaciones en centros de datos con usuarios finales que acceden a aplicaciones de LOB (por ejemplo, SAP). Idealmente, esta infraestructura local se conecta a su vez a Azure mediante Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Central:** muestra IaaS de Azure y, en este caso, el uso de máquinas virtuales de Azure para hospedar SAP u otras aplicaciones que utilizan SAP HANA como sistema DBMS. Las instancias de HANA menores que funcionan con la memoria proporcionada por máquinas virtuales de Azure se implementan en máquinas virtuales de Azure junto con su capa de la aplicación. Consulte más información sobre [máquinas virtuales](https://azure.microsoft.com/services/virtual-machines/).
<br />Se usan redes de Azure para agrupar sistemas SAP junto con otras aplicaciones en redes virtuales de Azure. Estas redes virtuales se conectan a sistemas locales, así como a SAP HANA en Azure (Instancias grandes).
<br />Para más información sobre las aplicaciones y bases de datos de SAP NetWeaver que admiten la ejecución en Microsoft Azure, consulte [SAP Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (Nota de SAP 1928533: Aplicaciones de SAP en Azure: productos y tipos de máquina virtual de Azure admitidos). Para ver documentación sobre la implementación de soluciones de SAP en Azure, consulte:

  -  [Uso de SAP en máquinas virtuales (VM) Windows](../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Uso de soluciones SAP en máquinas virtuales de Microsoft Azure](../virtual-machines-linux-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Izquierda:** muestra el hardware con certificación SAP HANA TDI en el sello de instancias grandes de Azure. Las unidades de Instancias grandes de HANA se conectan a las redes virtuales de Azure de su suscripción con la misma tecnología que la empleada para la conectividad entre la infraestructura local y la de Azure.

El sello de instancias grandes de Azure en sí combina los componentes siguientes:

- **Computación:** servidores basados en procesadores Intel Xeon E7-8890v3 que proporcionan la funcionalidad de computación necesaria y poseen certificación para SAP HANA.
- **Red:** tejido de red de alta velocidad unificada que interconecta los componentes de computación, almacenamiento y LAN.
- **Almacenamiento:** una infraestructura de almacenamiento a la que se accede por medio de un tejido de red unificada. Se proporciona capacidad de almacenamiento específica en función de la configuración de SAP HANA en Azure (Instancias grandes) concreta que se vaya a implementar (hay más capacidad de almacenamiento disponible por un costo mensual adicional).

Dentro de la infraestructura multiinquilino del sello de instancias grandes, los clientes se implementan como inquilinos aislados. Estos inquilinos mantienen una relación de 1:1 con la suscripción de Azure. Esto significa que no puede acceder a la misma instancia de SAP HANA en Azure (Instancias grandes) que se ejecuta en un sello de instancias grandes de Azure desde dos suscripciones de Azure diferentes.

Al igual que con las máquinas virtuales de Azure, SAP HANA en Azure (Instancias grandes) se ofrece en varias regiones de Azure. A fin de ofrecer funcionalidades de recuperación ante desastres, puede elegir participar. Los diferentes sellos de instancias grandes en las diversas regiones de Azure están conectados entre sí.

Al igual que puede elegir entre diferentes tipos de máquinas virtuales con Azure Virtual Machines, también puede elegir entre diferentes SKU de Instancias grandes de HANA que se adapten a distintos tipos de carga de trabajo de SAP HANA. SAP aplica una relación de memoria a sockets de procesador para diferentes cargas de trabajo en función de la generación de procesadores Intel. Se ofrecen cuatro tipos diferentes de SKU:

A partir de diciembre de 2016, SAP HANA en Azure (grandes instancias) está disponible en seis configuraciones de las regiones de Azure de Oeste de EE. UU. y Este de EE. UU.:

| Solución de SAP | CPU | RAM | Almacenamiento |
| --- | --- | --- | --- |
| Optimizada para OLAP: SAP BW, BW/4HANA<br /> o SAP HANA para cargas de trabajo de OLAP genérico | SAP HANA en Azure S72<br /> – 2 procesadores Intel® Xeon® E7-8890 v3 |  768 GB |  3 TB |
| --- | SAP HANA en Azure S144<br /> – 4 procesadores Intel® Xeon® E7-8890 v3 |  1,5 TB |  6 TB |
| --- | SAP HANA en Azure S192<br /> – 4 procesadores Intel® Xeon® E7-8890 v4 |  2,0 TB |  8 TB |
| Optimizada para OLTP: SAP Business Suite<br /> en SAP HANA o S/4HANA (OLTP),<br /> OLTP genérico | SAP HANA en Azure S72m<br /> – 2 procesadores Intel® Xeon® E7-8890 v3 |  1,5 TB |  6 TB |
|---| SAP HANA en Azure S144m<br /> – 4 procesadores Intel® Xeon® E7-8890 v3 |  3,0 TB |  12 TB |
|---| SAP HANA en Azure S192m<br /> – 4 procesadores Intel® Xeon® E7-8890 v4 |  4,0 TB |  16 TB |

Las diferentes configuraciones anteriores se documentan en [SAP Note #2316233 - SAP HANA on Microsoft Azure (Large Instances)](https://launchpad.support.sap.com/#/notes/2316233/E) (Nota de SAP 2316233: SAP HANA en Microsoft Azure [Instancias grandes]).

Las configuraciones específicas que se elijan dependen de la carga de trabajo, los recursos de CPU y la memoria que desee. La carga de trabajo de OLTP puede aprovechar las SKU que estén optimizadas para cargas de trabajo de OLAP. Del mismo modo, puede aprovechar las SKU de OLTP para cargas de trabajo de OLAP HANA. Sin embargo, es posible que deba restringir la memoria usada con HANA a la certificada con la generación de procesadores Intel E7, tal y como [se indica en el sitio web de SAP](http://global12.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/appliances.html) para el tipo de aplicación _BW en HANA escalado verticalmente_ (Scale-up BWoH).

Es importante tener en cuenta que la infraestructura completa del sello de instancias grandes no está asignada exclusivamente para que la use un único cliente. Esto se aplica a SAP HANA en Azure (Instancias grandes), al igual que a los bastidores de recursos de proceso y almacenamiento conectados mediante un tejido de red implementado en Azure. La infraestructura de Instancias grandes de HANA, como Azure, implementa &quot;inquilinos&quot; de clientes diferentes que están aislados entre sí mediante aislamiento de red. Por lo tanto, estas implementaciones de Instancias grandes de HANA no interfieren entre sí ni son visibles unas a otras. Se asigna un inquilino implementado en el sello de instancias grandes a una suscripción de Azure. Si tiene una segunda suscripción de Azure que también requiera Instancias grandes de HANA, se implementaría en un inquilino independiente en un sello de instancias grandes con todo el aislamiento de red que impide la comunicación directa entre estas instancias.

Sin embargo, existen diferencias notables entre ejecutar SAP HANA en Instancias grandes de HANA y ejecutar SAP HANA en máquinas virtuales de Azure implementadas en Azure:

- No hay ningún nivel de virtualización para SAP HANA en Azure (Instancias grandes). El rendimiento proviene del hardware sin sistema operativo subyacente.
- A diferencia de Azure, el servidor de SAP HANA en Azure (Instancias grandes) está dedicado a un cliente específico. Tras un reinicio o un apagado del servidor, no se implementa el sistema operativo ni SAP HANA en otro servidor. (La única excepción es cuando un servidor experimente problemas y se deba volver a implementar en otro servidor blade).
- A diferencia de Azure, donde los tipos de procesador de host se seleccionan para lograr una relación precio/rendimiento óptima, los tipos de procesador elegidos para SAP HANA en Azure (Instancias grandes) son los de mayor rendimiento en la línea de procesadores Intel E7v3.

Se implementarán varios clientes en hardware de SAP HANA en Azure (Instancias grandes) y cada uno está separado de los demás porque se implementa en su propia red VLAN. Para conectar Instancias grandes de HANA a una red virtual de Azure, los componentes de red existentes llevan a cabo la traducción de direcciones de red (NAT) entre el intervalo de direcciones IP de la red virtual de Azure y el espacio de direcciones IP de VLAN dentro de la infraestructura de hardware.

## <a name="operations-model-and-responsibilities"></a>Modelo de operaciones y responsabilidades

El servicio proporcionado con SAP HANA en Azure (Instancias grandes) se alinea con los servicios de IaaS de Azure. Obtiene una instancia de Instancias grandes de HANA con un sistema operativo instalado que está optimizado para SAP HANA. Al igual que con las máquinas virtuales de IaaS de Azure, la mayoría de las tareas necesarias para proteger el sistema operativo, instalar el software adicional que necesite, instalar HANA, operar el sistema operativo y HANA, y actualizar el sistema operativo y HANA son de su responsabilidad. Microsoft no exigirá que actualice el sistema operativo ni HANA.

![Responsabilidades de SAP HANA en Azure (Instancias grandes)](./media/sap-hana-overview-architecture/image2-responsibilities.png)

Como ve en el diagrama anterior, SAP HANA en Azure (Instancias grandes) es una oferta de infraestructura como servicio multiinquilino. Como resultado, la división de responsabilidad reside en el límite entre el sistema operativo y la infraestructura, en su mayor parte. Microsoft se responsabiliza de todos los aspectos del servicio por debajo de la línea del sistema operativo y el usuario es responsable de aquellos por encima de la línea, incluido el sistema operativo. Por lo tanto, puede seguir usando la mayor parte de los métodos locales que emplee actualmente para el cumplimiento normativo, la seguridad, la administración de aplicaciones, la base y la administración del sistema operativo. Los sistemas aparecen como si estuvieran en la red en todos los aspectos.

Sin embargo, este servicio está optimizado para SAP HANA, por lo que hay áreas en que debe colaborar con Microsoft para usar las funcionalidades de la infraestructura subyacente a fin de obtener resultados óptimos.

En la lista siguiente, se proporcionan más detalles sobre cada una de las capas y sus responsabilidades:

**Redes:** todas las redes internas para el sello de instancias grandes que ejecuta SAP HANA, su acceso al almacenamiento, la conectividad entre las instancias (para el escalado horizontal y otras funciones), la conectividad con el entorno y la conectividad a Azure cuando la capa de la aplicación de SAP se hospeda en Azure Virtual Machines. También incluye la conectividad de WAN entre centros de datos de Azure para la replicación con fines de recuperación ante desastres. El inquilino se encarga de dividir todas las redes en particiones, que tienen QOS aplicada.

**Almacenamiento:** almacenamiento virtualizado con particiones para todos los volúmenes que los servidores de SAP HANA necesitan, así como para las instantáneas.

**Servidores:** los servidores físicos dedicados a ejecutar las bases de datos de SAP HANA asignadas a los inquilinos. Incluyen virtualización de hardware.

**SDDC:** software de administración que sirve para administrar los centros de datos como entidades definidas por software. Permite a Microsoft agrupar recursos por motivos de escalabilidad, disponibilidad y rendimiento.

**SO:** el sistema operativo que elija (SUSE Linux o Red Hat Linux) que se ejecuta en los servidores. Las imágenes de SO que se le proporcionan serán las imágenes ofrecidas por el proveedor de Linux individual a Microsoft a fin de ejecutar SAP HANA. Debe tener una suscripción con el proveedor de Linux para la imagen optimizada de SAP HANA específica. Sus responsabilidades incluyen el registro de las imágenes con el proveedor del sistema operativo. Desde el momento en que Microsoft le hace entrega del producto, también recae sobre usted la responsabilidad de cualquier revisión adicional del sistema operativo Linux. Esto guarda relación con los paquetes adicionales que podrían ser necesarios para una correcta instalación de SAP HANA (consulte en la documentación de la instalación de SAP HANA y las notas de SAP) y que no se habían incluido por el proveedor específico de Linux en sus imágenes de SO optimizadas de SAP HANA. La responsabilidad del cliente también incluye la aplicación de revisiones del sistema operativo vinculadas a un funcionamiento inadecuado u optimización del sistema operativo y sus controladores en relación con el hardware de servidor específico. O cualquier revisión funcional o de seguridad del sistema operativo. Es también responsabilidad del cliente la supervisión y planificación de capacidad de:

- Consumo de recursos de CPU
- Consumo de memoria
- Volúmenes de disco relacionados con espacio disponible, E/S y latencia
- Tráfico de volumen de red entre Instancias grandes de HANA y la capa de la aplicación de SAP

La infraestructura subyacente de Instancias grandes de HANA proporciona funcionalidad para las copias de seguridad y la restauración del volumen del sistema operativo. Aprovechar esta funcionalidad es también su responsabilidad.

**Middleware:** la instancia de SAP HANA, principalmente. La administración, las operaciones y la supervisión son responsabilidad suya. Se proporciona funcionalidad que permite usar instantáneas de almacenamiento para la copia de seguridad y la restauración, además de para la recuperación ante desastres. Estas funcionalidades se proporcionan con la infraestructura. Sin embargo, sus responsabilidades incluyen también diseñar la alta disponibilidad o la recuperación ante desastres con estas funcionalidades, aprovecharlas y supervisar que las instantáneas de almacenamiento se hayan ejecutado correctamente.

**Datos:** los datos administrados por SAP HANA y otros datos, como archivos de copia de seguridad que se encuentran en volúmenes o recursos compartidos de archivos. Sus responsabilidades incluyen la supervisión del espacio disponible en disco y la administración del contenido en los volúmenes, así como la supervisión de la ejecución correcta de las copias de seguridad de volúmenes de disco e instantáneas de almacenamiento. Sin embargo, la ejecución correcta de la replicación de datos en sitios de recuperación ante desastres es responsabilidad de Microsoft.

**Aplicaciones:** las instancias de aplicación de SAP o, en el caso de aplicaciones no de SAP, la capa de la aplicación de dichas aplicaciones. Entre sus responsabilidades, se encuentran la implementación, la administración, las operaciones y la supervisión de esas aplicaciones en relación con el planeamiento de capacidad para el consumo de recursos de CPU, el consumo de memoria, el consumo de Azure Storage y el consumo de ancho de banda de red dentro de redes virtuales de Azure y desde redes virtuales de Azure hacia SAP HANA en Azure (Instancias grandes).

**WAN:** las conexiones que establece entre la implementación local y las implementaciones de Azure para cargas de trabajo. Si las cargas de trabajo son críticas, utilice Azure ExpressRoute. Esta conexión no forma parte de la solución SAP HANA en Azure (Instancias grandes), por lo que será responsabilidad suya configurarla.

**Archivado:** es posible que prefiera archivar copias de los datos con sus propios métodos en cuentas de almacenamiento. Esto conlleva tareas de administración, cumplimiento normativo, costos y operaciones. Es su responsabilidad generar copias de archivado y copias de seguridad en Azure, y almacenarlas con un método compatible.

Consulte el [Acuerdo de Nivel de Servicio para SAP HANA en Azure (Instancias grandes)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Ajuste de tamaño

El ajuste de tamaño para Instancias grandes de HANA es igual que para HANA en general. Para los sistemas existentes e implementados, debería cambiar de otro RDBMS a HANA. SAP ofrece una serie de informes que se ejecutan en los sistemas SAP existentes. Comprueban los datos y calculan los requisitos de memoria de tablas si la base de datos se mueve a HANA. Lea las siguientes notas de SAP para más información sobre cómo ejecutar estos informes y cómo obtener las revisiones o versiones más recientes:

- SAP Note #1793345 - Sizing for SAP Suite on HANA (Nota de SAP 1793345: Ajuste de tamaño para SAP Suite en HANA)
- SAP Note #1872170 - Suite on HANA and S/4 HANA sizing report (Nota de SAP 1872170: Informe de ajuste de tamaño de Suite en HANA y S/4 HANA)
- SAP Note #2121330 - FAQ: SAP BW on HANA Sizing Report (Nota de SAP 2121330: P+F: Informe de ajuste de tamaño de SAP BW en HANA)
- SAP Note #1736976 - Sizing Report for BW on HANA (Nota de SAP 1736976: Informe de ajuste de tamaño para BW en HANA)
- SAP Note #2296290 - New Sizing Report for BW on HANA (Nota de SAP 2296290: Nuevo informe de ajuste de tamaño para BW en HANA)

Para las implementaciones en green field, está disponible SAP Quick Sizer para calcular los requisitos de memoria de la implementación de software de SAP sobre HANA.

Los requisitos de memoria de HANA crecerán a medida que aumente el volumen de datos, por lo que deberá tener en cuenta el consumo actual de memoria y una previsión de cuál será en el futuro. En función de los requisitos de memoria, puede asignar la demanda a una de las SKU de Instancias grandes de HANA.

## <a name="requirements"></a>Requisitos

Estos son los requisitos para ejecutar SAP HANA en Azure (Instancias grandes).

**Microsoft Azure:**

- Una suscripción de Azure que se pueda vincular a SAP HANA en Azure (Instancias grandes).
- Soporte técnico Premier crítico de Microsoft. Consulte [SAP Support Note #2015553 – SAP on Microsoft Azure: Support Prerequisites](https://launchpad.support.sap.com/#/notes/2015553) (Nota de SAP sobre soporte 2015553: Requisitos previos de SAP en Microsoft Azure) para información específica relacionada con la ejecución de SAP en Azure.
- Conocimiento de las SKU de las instancias grandes de HANA que necesita después de realizar un ajuste de tamaño con SAP.

**Conectividad de red:**

- Azure ExpressRoute entre la infraestructura local y la de Azure: asegúrese de pedir a su ISP como mínimo una conexión de 1 Gbps para conectar el centro de datos local a Azure.

**Sistema operativo:**

- Licencias para SUSE Linux Enterprise Server 12 for SAP Applications.

> [!NOTE] 
> El sistema operativo entregado por Microsoft no está registrado con SUSE ni está conectado a una instancia de SMT.

- SUSE Linux Subscription Management Tool (SMT) implementada en Azure en una máquina virtual de Azure. Esto hace posible que SUSE registre SAP HANA en Azure (Instancias grandes) y la actualice (ya que se carece de acceso a Internet en el centro de datos de Instancias grandes de HANA). 
- Licencias para Red Hat Enterprise Linux 6.7 o 7.2 para SAP HANA.

> [!NOTE]
> El sistema operativo entregado por Microsoft no está registrado con Red Hat ni está conectado a una instancia Red Hat Subscription Manager.

- Red Hat Subscription Manager implementado en Azure en una máquina virtual de Azure. Esto hace posible que Red Hat registre SAP HANA en Azure (Instancias grandes) y la actualice (ya que se carece de acceso directo a Internet desde el inquilino implementado en el sello de instancias grandes de Azure).
- El contrato de servicio y soporte técnico con el proveedor de Linux que está implícitamente incluido en la suscripción de versión específica de Linux u otro contrato de servicio y soporte técnico que abarca la versión específica de Linux utilizada y que cumple los criterios de SAP.

**Base de datos**

- Las licencias y los componentes de instalación de software para SAP HANA (Platform Edition o Enterprise Edition).

**Aplicaciones:**

- Licencias y componentes de instalación de software para todas las aplicaciones de SAP que se conecten a SAP HANA y los contratos de soporte de SAP relacionados.
- Licencias y componentes de instalación de software para aplicaciones que no sean de SAP utilizadas en relación con el entorno de SAP HANA en Azure (Instancias grandes) y los contratos de soporte relacionados.

**Habilidades:**

- Experiencia y conocimientos sobre IaaS de Azure y sus componentes.
- Experiencia y conocimientos sobre la implementación de cargas de trabajo de SAP en Azure.
- Personal certificado para la instalación de SAP HANA.
- Conocimientos de arquitectura de SAP para diseñar la alta disponibilidad y la recuperación ante desastres en torno a SAP HANA.

## <a name="networking"></a>Redes

La arquitectura de las redes de Azure es un componente clave para implementar las aplicaciones de SAP de forma correcta. Por lo general, las implementaciones de SAP HANA en Azure (Instancias grandes) tienen un entorno de SAP más amplio con varias soluciones SAP distintas y diversos tamaños de base de datos, consumo de recursos de CPU y utilización de memoria. Es muy probable que solo uno o dos de esos sistemas SAP estén basados en SAP HANA, por lo que el entorno de SAP probablemente sea un entorno híbrido que use:

- Sistemas SAP implementados localmente. Debido a su tamaño, actualmente no se pueden hospedar en Azure; un ejemplo típico sería un sistema SAP ERP de producción con Microsoft SQL Server (como base de datos), además de &gt;100 CPU y 1 TB de memoria.
- Sistemas SAP basados en SAP HANA implementados localmente (por ejemplo, un sistema SAP ERP que requiere 6 TB o más de memoria para su base de datos de SAP HANA).
- Sistemas SAP implementados en máquinas virtuales de Azure. Estos sistemas pueden ser instancias de desarrollo, pruebas, espacio aislado o producción para cualquiera de las aplicaciones basadas en SAP NetWeaver que se pueden implementar correctamente en Azure (en máquinas virtuales), en función de la demanda de memoria y el consumo de recursos. También podrían basarse en bases de datos como SQL Server (consulte [SAP Support Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533/E) [Nota de SAP 1928533: Aplicaciones de SAP en Azure: productos y tipos de máquina virtual de Azure admitidos]) o SAP HANA (consulte [SAP HANA Certified IaaS Platforms](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) [Plataformas IaaS certificadas para SAP HANA]).
- Servidores de aplicaciones de SAP implementados en Azure (en máquinas virtuales) que aprovechan SAP HANA en Azure (Instancias grandes) en sellos de instancias grandes de Azure.

Aunque un entorno híbrido de SAP (con cuatro o más escenarios de implementación distintos) es típico, hay casos de entornos de SAP completos en Azure y, a medida que las máquinas virtuales de Microsoft Azure adquieran mayor potencia, aumentará el número de soluciones SAP que se implementen en máquinas virtuales de Azure.

Las redes de Azure en el contexto de los sistemas SAP implementados en Azure no son complicadas. Se basan en los siguientes principios:

- Las redes virtuales de Azure deben estar conectadas al circuito de Azure ExpressRoute que se conecta a la red local.
- Normalmente, un circuito de ExpressRoute necesita un ancho de banda de 1 Gbps o más. Esto ofrece el suficiente ancho de banda para transferir datos entre sistemas locales y sistemas que se ejecutan en máquinas virtuales de Azure (así como la conexión a sistemas de Azure desde el entorno local de los usuarios finales).
- Todos los sistemas SAP en Azure deben configurarse en redes virtuales de Azure para poder comunicarse entre sí.
- Active Directory y DNS hospedados localmente se extienden a Azure a través de ExpressRoute.

**Recomendación:** Implemente el entorno de SAP completo en Azure en una única suscripción de Azure. Muchos de los procedimientos dentro de un entorno de SAP requieren conectividad de red transparente, y posiblemente menor, entre las instancias de producción, prueba y desarrollo de SAP; además, la arquitectura de SAP NetWeaver posee muchos automatismos que se basan en dicha conexión de red transparente entre estas diferentes instancias. Por lo tanto, es muy recomendable mantener el entorno de SAP completo en una sola suscripción de Azure, incluso si está implementado en varias regiones de Azure.

La arquitectura y los procesos de SAP HANA en Azure (Instancias grandes) se basan en la recomendación anterior.

> [!NOTE] 
> Una sola suscripción de Azure solo se puede vincular a un único inquilino en un sello de instancias grandes en una región de Azure específica y, por el contrario, un único inquilino del sello de instancias grandes solo se puede vincular a una única suscripción de Azure.

Si se implementa SAP HANA en Azure (Instancias grandes) en dos regiones de Azure diferentes, se implementará un inquilino diferente en el sello de instancias grandes. Sin embargo, puede esperar que ambos terminen bajo la misma suscripción de Azure, siempre que estas instancias formen parte del mismo entorno de SAP.

> [!IMPORTANT] 
> La única implementación compatible con SAP HANA en Azure (Instancias grandes) es la de Azure Resource Manager.

### <a name="additional-azure-vnet-information"></a>Información adicional sobre las redes virtuales de Azure

Para conectar una red virtual de Azure con ExpressRoute, se debe crear una puerta de enlace de Azure (consulte [Acerca de las puertas de enlace de red virtual para ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Se puede usar una puerta de enlace de Azure con ExpressRoute hacia una infraestructura externa a Azure (o hacia un sello de grandes instancias de Azure) o para conectarse entre redes virtuales de Azure (consulte [Configuración de una conexión de red virtual a red virtual para Resource Manager mediante PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Puede conectar la puerta de enlace de Azure a un máximo de cuatro conexiones ExpressRoute diferentes, siempre que procedan de diferentes intercambios de MS Enterprise (MSEE).

> [!NOTE] 
> El rendimiento proporcionado por una puerta de enlace de Azure es distinto para ambos casos de uso (consulte [Acerca de VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). El rendimiento máximo que se puede lograr con una puerta de enlace de red virtual es de 10 Gbps, mediante una conexión ExpressRoute. Tenga en cuenta que la copia de archivos entre una máquina virtual de Azure que resida en una red virtual de Azure y un sistema local (como una sola secuencia de copia) no conseguirá el rendimiento total de las diferentes SKU de puerta de enlace. Para aprovechar el ancho de banda completo de la puerta de enlace de red virtual, debe usar varias secuencias o copiar diferentes archivos en secuencias paralelas de un único archivo.

Cuando lea los artículos anteriores, fíjese especialmente en la información sobre la disponibilidad de SKU de la puerta de enlace UltraPerformance en diferentes regiones de Azure.

### <a name="networking-for-sap-hana-on-azure"></a>Redes para SAP HANA en Azure

Para la conexión a SAP HANA en Azure (Instancias grandes), debe haber una red virtual de Azure conectada con la red local de un cliente a través de su puerta de enlace de red virtual mediante ExpressRoute. Además, debe estar conectada a través de un segundo circuito de ExpressRoute a las instancias grandes de HANA ubicadas en un sello de instancias grandes de Azure. La puerta de enlace de red virtual tendrá al menos dos conexiones ExpressRoute y ambas compartirán el ancho de banda máximo de la puerta de enlace de red virtual.

> [!IMPORTANT] 
> Dado el tráfico de red general entre la capa de base de datos y la de la aplicación de SAP, solo se admiten las SKU de puerta de enlace HighPerformance o UltraPerformance para redes virtuales para la conexión a SAP HANA en Azure (Instancias grandes).

![Red virtual de Azure conectada a SAP HANA en Azure (Instancias grandes) y localmente](./media/sap-hana-overview-architecture/image3-on-premises-infrastructure.png)

### <a name="single-sap-system"></a>Único sistema SAP

La infraestructura local mostrada arriba se conecta a través de ExpressRoute con Azure, mientras que el circuito de ExpressRoute se conecta a un enrutador de perímetro de Microsoft Enterprise (MSEE) (consulte [Información técnica de ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Una vez establecida, esa ruta se conecta a la red troncal de Microsoft Azure, y todas las regiones de Azure son accesibles.

> [!NOTE] 
> Para ejecutar entornos de SAP en Azure, conéctese al MSEE más cercano a la región de Azure en el entorno de SAP. Los sellos de instancias grandes de Azure están conectados a través de dispositivos MSEE dedicados para minimizar la latencia de red entre máquinas virtuales de Azure en sellos de grandes instancias e IaaS de Azure.

La puerta de enlace de red virtual para las máquinas virtuales de Azure, que hospedan instancias de aplicación de SAP, está conectada a ese circuito de ExpressRoute y la misma red virtual está conectada a un enrutador MSEE independiente dedicado a la conexión con sellos de instancias grandes.

Se trata de un ejemplo sencillo de un único sistema SAP, en el que la capa de la aplicación de SAP se hospeda en Azure y la base de datos de SAP HANA se ejecuta en SAP HANA en Azure (Instancias grandes). Se supone que el ancho de banda de la puerta de enlace de red virtual de 2 Gbps o 10 Gbps no representa un cuello de botella.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Varios sistemas SAP o sistemas SAP grandes

Si se implementan varios sistemas SAP o sistemas SAP grandes que se conectan a SAP HANA en Azure (Instancias grandes), es razonable suponer el rendimiento de la SKU de puerta de enlace de red virtual HighPerformance pueda convertirse en cuello de botella. En ese caso, elija la SKU UltraPerformance, si está disponible. Sin embargo, si solo está disponible la SKU HighPerformance (hasta 2 Gbps) o si es posible que la SKU UltraPerformance (hasta 10 Gbps) no sea suficiente, deberá dividir las capas de la aplicación en varias redes virtuales de Azure.

Para una arquitectura de red más escalable:

- Aproveche varias redes virtuales de Azure para una sola capa de la aplicación de SAP más grande.
- Implemente una red virtual de Azure distinta para cada sistema SAP implementado, en comparación con la combinación de estos sistemas SAP en subredes independientes en la misma red virtual.

 Una arquitectura de red más escalable para SAP HANA en Azure (Instancias grandes):

![Implementación de la capa de la aplicación de SAP en varias redes virtuales de Azure](./media/sap-hana-overview-architecture/image4-networking-architecture.png)

La implementación de la capa de la aplicación de SAP, o componentes, en varias redes virtuales de Azure como se muestra arriba, introdujo una sobrecarga ineludible de la latencia durante la comunicación entre las aplicaciones hospedadas en esas redes virtuales de Azure. De manera predeterminada, el tráfico de red entre máquinas virtuales de Azure ubicadas en diferentes redes virtuales discurrirá a través de enrutadores MSEE en esta configuración. Sin embargo, desde septiembre de 2016 es posible evitar y optimizar esta situación. La manera de optimizar y reducir la latencia en la comunicación entre dos redes virtuales pasa por el emparejamiento de las redes virtuales de Azure que estén en la misma región. Incluso si estas se encuentran en distintas suscripciones. Gracias al emparejamiento de redes virtuales de Azure, la comunicación entre máquinas virtuales en dos redes virtuales diferentes de Azure puede emplear la red troncal de estas para permitir una comunicación directa entre ambas. De este modo se consigue una latencia similar que si las máquinas virtuales estuvieran en la misma red virtual. Al mismo tiempo, el tráfico que dirige los intervalos de direcciones IP que están conectadas a través de la puerta de enlace de red virtual de Azure se enruta a través de la puerta de enlace de red virtual individual de dicha red. Puede obtener detalles acerca del emparejamiento de redes virtuales de Azure en el artículo [Emparejamiento de VNET](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview).
### <a name="minimal-deployment"></a>Implementación mínima

Para un sistema SAP pequeño (implementación mínima), las máquinas virtuales de Azure hospedan la capa de la aplicación de SAP en un entorno de Azure nativo (dentro de una sola red virtual) y se conectan al sello de instancias grandes por medio de ExpressRoute. Siga estos pasos para preparar SAP HANA en Azure (Instancias grandes) para su uso:

- Recopile información específica relacionada con cuatro intervalos de direcciones IP diferentes:

  1. Un intervalo de direcciones /&29; para conexiones P2P que se usará para los circuitos de ExpressRoute.
  
  2. Un bloque CIDR /24 único (recomendado) que se usará para asignar las direcciones IP específicas necesarias para SAP HANA en Azure (Instancias grandes).
  3. Uno o más bloques CIDR /24 (recomendado) para las subredes de inquilino de red virtual de Azure. Se trata de subredes en la suscripción de Azure del cliente donde residirán las máquinas virtuales de Azure relacionadas con SAP; se permitirá que las direcciones accedan a SAP HANA en Azure (Instancias grandes). Debería haber un bloque de direcciones de inquilino por cada subred, y los bloques se pueden agregar si son contiguos y están en la misma red virtual.
  4. Una subred /28 de las de la puerta de enlace de red virtual (se debe usar una /27 para utilizar redes P2S).

  - Se necesitan los dos primeros intervalos (uno por cada suscripción de Azure y región). Se necesitan como mínimo los intervalos de direcciones IP indicados en los puntos 3 y 4 para cada red virtual de Azure y, si desea tener varios inquilinos o subredes en una red virtual, se deberían especificar varios intervalos para el punto 3.
![Intervalos de direcciones IP necesarios en la implementación mínima de SAP HANA en Azure (Instancias grandes)](./media/sap-hana-overview-architecture/image5-ip-address-range-a.png)

  -  Si se configuran varias subredes de inquilinos en una red virtual de Azure: ![Intervalos de direcciones IP con espacio de direcciones contiguo para red virtual de Azure](./media/sap-hana-overview-architecture/image6-ip-address-range-b.png)

> [!IMPORTANT] 
> Los intervalos de direcciones IP especificados arriba no deben solaparse en ningún caso con otro intervalo; cada uno de ellos debe ser discreto y no ser subred de ningún otro intervalo. Solo se debe aplicar a las redes virtuales de Azure la dirección definida en los puntos 3 y 4; todas las demás se utilizan para la conectividad de instancias grandes y el enrutamiento. Además, como procedimiento recomendado, los intervalos de direcciones del espacio de direcciones deben coincidir con los intervalos de subredes y no incluir espacio sin asignar ni vacío. Si se producen solapamientos de los intervalos definidos en los puntos 1 y 2 con los definidos para los puntos 3 y 4, la red virtual de Azure no se conectará al circuito de ExpressRoute.

- Microsoft crea un circuito de ExpressRoute entre la suscripción de Azure y el sello de instancias grandes.
- Cree un inquilino de red en el sello de instancias grandes.
- Configure las redes en la infraestructura de SAP HANA en Azure (Instancias grandes) de forma que acepten direcciones IP del intervalo especificado dentro de la red virtual de Azure, que se comunicará con Instancias grandes de HANA.
- Configure la traducción de direcciones de red (NAT) en el inquilino de cliente en el sello de instancias grandes (para que la dirección IP interna del inquilino se asigne a una dirección IP definida por el inquilino para Azure).
- Según la SKU concreta de SAP HANA en Azure (Instancias grandes) que se haya adquirido, asigne una unidad de proceso en una red de inquilino, asigne y monte almacenamiento, e instale el sistema operativo (SUSE o RedHat Linux).

Implementación mínima de la arquitectura de red de SAP HANA en Azure (Instancias grandes):

![Implementación mínima con intervalos de direcciones IP](./media/sap-hana-overview-architecture/image7-minimal-deployment.png)

### <a name="routing-in-azure"></a>Enrutamiento en Azure

Existen dos consideraciones importantes sobre el enrutamiento de red para SAP HANA en Azure (Instancias grandes):

1. Las máquinas virtuales de Azure solo pueden acceder a SAP HANA en Azure (Instancias grandes) en la conexión ExpressRoute dedicada, no directamente desde el entorno local. Por lo tanto, los clientes de administración y las aplicaciones que necesiten acceso directo, como SAP Solution Manager ejecutado de forma local, no se pueden conectar a la base de datos de SAP HANA.

2. SAP HANA en Azure (Instancias grandes) tiene asignada una dirección IP de un grupo NAT definido. Esta dirección IP es accesible a través de la suscripción de Azure y ExpressRoute. Dado que la dirección IP forma parte de un grupo NAT, deberá realizar una configuración de red adicional en el entorno. Para más información, consulte el artículo relacionado sobre la instalación de SAP HANA.

> [!NOTE] 
> Si necesita conectarse a SAP HANA en Azure (Instancias grandes) en un escenario de _almacenamiento de datos_, en el que las aplicaciones o los usuarios finales deben conectarse a la base de datos de SAP HANA (que se ejecuta directamente), debe usarse otro componente de red: un proxy inverso para enrutar los datos en ambos sentidos. Por ejemplo, F5 BIG-IP con Traffic Manager implementado en Azure como solución de enrutamiento de tráfico y firewall virtual.

### <a name="leveraging-in-multiple-regions"></a>Aprovechamiento en varias regiones

Pueden existir otras razones para implementar SAP HANA en Azure (Instancias grandes) en varias regiones de Azure, además de la recuperación ante desastres. Es posible que desee acceder a Instancias grandes de HANA desde cada una de las máquinas virtuales implementadas en las diferentes redes virtuales en las regiones. Dado que las direcciones IP con NAT de los diferentes servidores de Instancias grandes de HANA no se propagan más allá de las redes virtuales de Azure (que están conectadas directamente a las instancias por medio de su puerta de enlace), hay un pequeño cambio en el diseño de red virtual descrito antes: una puerta de enlace de red virtual de Azure puede controlar cuatro circuitos de ExpressRoute diferentes desde MSEE diferentes, y cada red virtual que esté conectada a uno de los sellos de instancias grandes se puede conectar al sello de instancias grandes en otra región de Azure.

![Redes virtuales de Azure conectadas a sellos de instancias grandes de Azure en diferentes regiones de Azure](./media/sap-hana-overview-architecture/image8-multiple-regions.png)

En la ilustración anterior, se muestra cómo las distintas redes virtuales de Azure en ambas regiones están conectadas a dos circuitos de ExpressRoute diferentes que se utilizan para conectarse a SAP HANA en Azure (Instancias grandes) en ambas regiones de Azure. Las conexiones recién introducidas son las líneas rojas rectangulares. Con estas conexiones fuera de las redes virtuales de Azure, las máquinas virtuales que se ejecutan en una de esas redes virtuales pueden acceder a cada una de las diferentes unidades de Instancias grandes de HANA implementadas en las dos regiones (siempre que se utilice la misma suscripción). Como se ve en la ilustración anterior, se supone que tiene dos conexiones ExpressRoute entre la infraestructura local y las dos regiones de Azure; esto está recomendado para la recuperación ante desastres.

> [!IMPORTANT] 
> Si se usan varios circuitos de ExpressRoute, deben utilizarse las configuraciones de anteposición de AS Path y de preferencia local de BGP para garantizar el enrutamiento adecuado del tráfico.



