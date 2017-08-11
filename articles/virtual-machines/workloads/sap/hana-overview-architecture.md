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
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 073763143e1131aded74f610037cd4aa1fd92f5e
ms.contentlocale: es-es
ms.lasthandoff: 07/26/2017

---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Introducción y arquitectura de SAP HANA en Azure (instancias grandes) 
La documentación es una guía de implementación técnica y arquitectura en cinco partes en la que se proporciona información para ayudar a implementar SAP en la nueva oferta SAP HANA en Azure (instancias grandes) en Azure. No es exhaustiva ni trata detalles específicos sobre la configuración de soluciones SAP. En su lugar, proporciona información útil para ayudar con la implementación inicial y las operaciones continuadas. No debe usarse en lugar de la documentación de SAP sobre la instalación de SAP HANA (ni las muchas notas de soporte técnico de SAP que tratan sobre el tema). Además, proporciona detalles sobre la instalación de SAP HANA en Azure (Instancias grandes).


En las cinco partes de esta guía se tratan los temas siguientes:

- [Introducción y arquitectura de SAP HANA en Azure (instancias grandes)](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infraestructura y conectividad con SAP HANA en Azure (instancias grandes)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Procedimiento para instalar y configurar SAP HANA en Azure (instancias grandes)](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes)](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Solución de problemas y supervisión de SAP HANA en Azure (instancias grandes)](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="definitions"></a>Definiciones

En la Guía de implementación técnica y arquitectura, se utilizan con frecuencia varias definiciones habituales. Tenga en cuenta los siguientes términos y sus significados:

- **IaaS**: infraestructura como servicio
- **PaaS**: plataforma como servicio
- **SaaS**: software como servicio
- **Componente de SAP:** una aplicación de SAP individual, como ECC, BW, Solution Manager o EP. Los componentes de SAP pueden basarse en tecnologías tradicionales, como ABAP o Java, o en una aplicación no basada en NetWeaver, como Business Objects.
- **Entorno de SAP:** uno o varios componentes de SAP agrupados lógicamente para desempeñar una función empresarial, como desarrollo, control de calidad, aprendizaje, recuperación ante desastres o producción.
- **Infraestructura de SAP**: hace referencia a todos los recursos de SAP de su infraestructura de TI. La infraestructura de SAP incluye todos los entornos, tanto los que son de producción como los que no.
- **Sistema SAP**: la combinación de la capa de DBMS y la de la aplicación de un sistema de desarrollo SAP ERP, un sistema de prueba SAP BW, un sistema de producción SAP CRM, etc. Las implementaciones de Azure no admiten la división de estas dos capas entre la infraestructura local y la de Azure. Esto significa que un sistema SAP debe implementarse de forma local o en Azure, pero no en ambos. Sin embargo, los diferentes sistemas de una infraestructura de SAP pueden implementarse en Azure o de forma local. Por ejemplo, podría implementar los sistemas de prueba y desarrollo SAP CRM en Azure, mientras que el sistema de producción SAP CRM se implementa de manera local. En el caso de SAP HANA en Azure (instancias grandes), se supone que admitirá la capa de la aplicación de SAP de sistemas SAP en máquinas virtuales de Azure y la instancia de HANA en una unidad en el sello de instancias grandes de HANA.
- **Sello de instancias grandes:** una pila de infraestructura de hardware que está certificada para SAP HANA TDI y se dedica a ejecutar instancias de SAP HANA dentro de Azure.
- **SAP HANA en Azure (Instancias grandes):** nombre oficial de la oferta de Azure para ejecutar instancias de HANA en hardware con certificación SAP HANA TDI que se implementa en sellos de instancias grandes en diferentes regiones de Azure. El término relacionado **Instancia grande de HANA** es la versión abreviada de SAP HANA en Azure (instancias grandes) y se usa con frecuencia en esta guía de implementación técnica.
- **Entre locales:** describe un escenario donde se implementan máquinas virtuales en una suscripción de Azure con conexión de sitio a sitio, entre varios sitios o de ExpressRoute entre los centros de datos locales y Azure. En la documentación habitual de Azure, este tipo de implementaciones se denominan "escenarios entre locales". La razón tras la conexión es extender los dominios locales, Active Directory/OpenLDAP locales y DNS local a Azure. La infraestructura local se extiende a los recursos de Azure de las suscripciones de Azure. Con esta extensión, las máquinas virtuales pueden formar parte del dominio local. Los usuarios del dominio local pueden tener acceso a los servidores y ejecutar servicios en esas máquinas virtuales (por ejemplo, servicios de DBMS). Es posible la comunicación y resolución de nombres entre máquinas virtuales implementadas de forma local y en Azure. Se trata del escenario típico en que se implementan la mayoría de los recursos de SAP. Vea las guías de [Planeamiento y diseño de VPN Gateway](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) y [Creación de una red virtual con una conexión de sitio a sitio mediante Azure Portal](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obtener más detalles.

Además, se han publicado varios recursos adicionales sobre el tema de la implementación de la carga de trabajo de SAP en la nube pública de Microsoft Azure. Es muy recomendable que quien planee y ejecute una implementación de SAP HANA en Azure tenga conocimientos y experiencia con los principios de IaaS de Azure y la implementación de cargas de trabajo de SAP en IaaS de Azure. En los siguientes recursos se proporciona más información, que se debe consultar antes de continuar:


- [Uso de soluciones SAP en máquinas virtuales de Microsoft Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="certification"></a>Certificación

Además de la certificación NetWeaver, SAP requiere una certificación especial de SAP HANA para admitir SAP HANA en determinadas infraestructuras, como IaaS de Azure.

La nota fundamental de SAP sobre NetWeaver, y hasta cierto punto sobre la certificación de SAP HANA, es [SAP Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (Nota de SAP 1928533: Aplicaciones de SAP en Azure: productos y tipos de máquina virtual de Azure admitidos).

Esta nota [SAP Note #2316233 - SAP HANA on Microsoft Azure (Large Instances)](https://launchpad.support.sap.com/#/notes/2316233/E) (Nota de SAP 2316233: SAP HANA en Microsoft Azure [Instancias grandes]) también es relevante. Trata sobre la solución descrita en esta guía. Además, se admite la ejecución de SAP HANA en el tipo de máquina virtual de Azure GS5. [La información para este caso está publicada en el sitio web de SAP](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

La solución SAP HANA en Azure (Instancias grandes) mencionada en la nota de SAP 2316233 proporciona a los clientes de Microsoft y SAP la capacidad de implementar cargas de trabajo grandes de SAP Business Suite, SAP Business Warehouse (BW), S/4 HANA, BW/4HANA u otras cargas de trabajo de SAP HANA en Azure. Esta solución se basa en el sello de hardware dedicado con certificación de SAP HANA ([Integración adaptada de centro de datos de SAP HANA o TDI](https://scn.sap.com/docs/DOC-63140)). La ejecución como solución configurada SAP HANA TDI le proporciona la confianza de saber que todas las aplicaciones basadas en SAP HANA (incluidas SAP Business Suite en SAP HANA, SAP Business Warehouse (BW) en SAP HANA, S4/HANA y BW4/HANA) van a funcionar en la infraestructura de hardware.

En comparación con la ejecución de SAP HANA en Azure Virtual Machines, esta solución cuenta con la ventaja de proporcionar volúmenes de memoria mucho más grandes. Si desea habilitar esta solución, debe comprender algunos aspectos clave:

- La capa de la aplicación de SAP y las aplicaciones que no son de SAP se ejecutan en Azure Virtual Machines (VM) que se hospedan en los sellos de hardware de Azure habituales.
- La infraestructura local, los centros de datos y las implementaciones de aplicaciones del cliente se conectan a la plataforma en la nube de Microsoft Azure mediante Azure ExpressRoute (recomendado) o una red privada virtual (VPN). Active Directory (AD) y DNS también se extienden a Azure.
- La instancia de base de datos de SAP HANA para cargas de trabajo de HANA se ejecuta en SAP HANA en Azure (Instancias grandes). El sello de instancias grandes está conectado a redes de Azure, por lo que el software que se ejecuta en las máquinas virtuales de Azure puede interactuar con la instancia de HANA que se ejecuta en Instancias grandes de HANA.
- El hardware de SAP HANA en Azure (Instancias grandes) es hardware dedicado que se proporciona en una infraestructura como servicio (IaaS) con SUSE Linux Enterprise Server o Red Hat Enterprise Linux preinstalado. Al igual que con Azure Virtual Machines, es responsabilidad suya llevar a cabo las actualizaciones y el mantenimiento del sistema operativo.
- La instalación de HANA o de otros componentes necesarios para ejecutar SAP HANA en las unidades de Instancias grandes de HANA es responsabilidad suya, al igual que lo son las operaciones continuadas y las administraciones de SAP HANA en Azure.
- Además de las soluciones descritas aquí, puede instalar otros componentes en la suscripción de Azure conectada a SAP HANA en Azure (Instancias grandes).  Por ejemplo, los componentes que permiten la comunicación, directa o no, con la base de datos de SAP HANA (servidores de salto, servidores RDP, SAP HANA Studio, SAP Data Services para escenarios de BI de SAP o soluciones de supervisión de red).
- Como en Azure, Instancias grandes de HANA ofrece funcionalidades auxiliares de alta disponibilidad y recuperación ante desastres.

## <a name="architecture"></a>Arquitectura

En un nivel alto, en la solución SAP HANA en Azure (Instancias grandes), la capa de la aplicación de SAP reside en máquinas virtuales de Azure y la capa de base de datos, en hardware configurado con SAP TDI y ubicado en un sello de instancias grandes en la misma región de Azure que la conectada a IaaS de Azure.

> [!NOTE]
> Debe implementar la capa de la aplicación de SAP en la misma región de Azure que la capa de DBMS de SAP. Esta regla está bien documentada en la información publicada sobre la carga de trabajo de SAP en Azure. 

La arquitectura global de SAP HANA en Azure (Instancias grandes) proporciona que una configuración de hardware con certificación SAP TDI (servidor de alto rendimiento sin sistema operativo ni virtualización para la base de datos de SAP HANA), así como la capacidad y flexibilidad de Azure para escalar los recursos de la capa de la aplicación de SAP según sus necesidades.

![Introducción a la arquitectura de SAP HANA en Azure (Instancias grandes)](./media/hana-overview-architecture/image1-architecture.png)

La arquitectura que se muestra se divide en tres secciones:

- **Derecha:** una infraestructura local donde se ejecutan diferentes aplicaciones en centros de datos con usuarios finales que acceden a aplicaciones de LOB (por ejemplo, SAP). Idealmente, esta infraestructura local se conecta a su vez a Azure mediante Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Central:** muestra IaaS de Azure y, en este caso, el uso de máquinas virtuales de Azure para hospedar SAP u otras aplicaciones que usan SAP HANA como sistema DBMS. Las instancias de HANA menores que funcionan con la memoria proporcionada por máquinas virtuales de Azure se implementan en máquinas virtuales de Azure junto con su capa de la aplicación. Consulte más información sobre [máquinas virtuales](https://azure.microsoft.com/services/virtual-machines/).
<br />Se usan redes de Azure para agrupar sistemas SAP junto con otras aplicaciones en redes virtuales de Azure. Estas redes virtuales se conectan a sistemas locales, así como a SAP HANA en Azure (Instancias grandes).
<br />Para más información sobre las aplicaciones y bases de datos de SAP NetWeaver que admiten la ejecución en Microsoft Azure, consulte [SAP Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (Nota de SAP 1928533: Aplicaciones de SAP en Azure: productos y tipos de máquina virtual de Azure admitidos). Para obtener documentación sobre la implementación de soluciones de SAP en Azure, consulte:

  -  [Uso de SAP en máquinas virtuales (VM) Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Uso de soluciones SAP en máquinas virtuales de Microsoft Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Izquierda:** muestra el hardware con certificación SAP HANA TDI en el sello de instancias grandes de Azure. Las unidades de Instancias grandes de HANA se conectan a las redes virtuales de Azure de su suscripción con la misma tecnología que la empleada para la conectividad entre la infraestructura local y la de Azure.

El sello de instancias grandes de Azure en sí combina los componentes siguientes:

- **Computación:** servidores basados en procesadores Intel Xeon E7-8890v3 o Intel Xeon E7-8890v4 que proporcionan la funcionalidad de computación necesaria y poseen certificación para SAP HANA.
- **Red:** tejido de red de alta velocidad unificada que interconecta los componentes de computación, almacenamiento y LAN.
- **Almacenamiento:** una infraestructura de almacenamiento a la que se accede por medio de un tejido de red unificada. Se proporciona capacidad de almacenamiento específica en función de la configuración de SAP HANA en Azure (Instancias grandes) concreta que se vaya a implementar (hay más capacidad de almacenamiento disponible por un costo mensual adicional).

Dentro de la infraestructura multiinquilino del sello de instancias grandes, los clientes se implementan como inquilinos aislados. En la implementación del inquilino, necesitará el nombre de una suscripción de Azure dentro de la inscripción de Azure. Va a ser la suscripción de Azure en la que se facturarán las instancias grandes de HANA. Estos inquilinos mantienen una relación de 1:1 con la suscripción de Azure. En lo que respecta a las redes, es posible tener acceso a una unidad de instancia grande de HANA implementada en un inquilino de una región de Azure desde dos redes virtuales diferentes de Azure que pertenecen a diferentes suscripciones de Azure según la red. Aunque esas suscripciones de Azure deben pertenecer a la misma inscripción de Azure. 

Al igual que con las máquinas virtuales de Azure, SAP HANA en Azure (Instancias grandes) se ofrece en varias regiones de Azure. A fin de ofrecer funcionalidades de recuperación ante desastres, puede elegir participar. Los diferentes sellos de instancia grande en una región geopolítica están conectados entre sí. Por ejemplo, los sellos de instancia grande de HANA en el oeste y este de EE. UU. se conectan a través de una conexión de red dedicada para la replicación de recuperación ante desastres. 

Al igual que puede elegir entre diferentes tipos de máquinas virtuales con Azure Virtual Machines, también puede elegir entre diferentes SKU de Instancias grandes de HANA que se adapten a distintos tipos de carga de trabajo de SAP HANA. SAP aplica una relación de memoria a sockets de procesador para diferentes cargas de trabajo en función de la generación de procesadores Intel. Se ofrecen cuatro tipos diferentes de SKU:

A partir de julio de 2017, SAP HANA en Azure (instancias grandes) está disponible en varias configuraciones en las regiones de Azure Oeste y Este de EE. UU., Este de Australia, Sudeste de Australia, Europa Occidental y Europa del Norte:

| Solución de SAP | CPU | Memoria | Almacenamiento | Disponibilidad |
| --- | --- | --- | --- | --- |
| Optimizada para OLAP: SAP BW, BW/4HANA<br /> o SAP HANA para cargas de trabajo de OLAP genérico | SAP HANA en Azure S72<br /> – 2 procesadores Intel® Xeon® E7-8890 v3 |  768 GB |  3 TB | Disponible |
| --- | SAP HANA en Azure S144<br /> – 4 procesadores Intel® Xeon® E7-8890 v3 |  1,5 TB |  6 TB | No disponible |
| --- | SAP HANA en Azure S192<br /> – 4 procesadores Intel® Xeon® E7-8890 v4 |  2,0 TB |  8 TB | Disponible |
| --- | SAP HANA en Azure S384<br /> – 8 procesadores Intel® Xeon® E7-8890 v4 |  4,0 TB |  16 TB | Listo para comprar |
| Optimizada para OLTP: SAP Business Suite<br /> en SAP HANA o S/4HANA (OLTP),<br /> OLTP genérico | SAP HANA en Azure S72m<br /> – 2 procesadores Intel® Xeon® E7-8890 v3 |  1,5 TB |  6 TB | Disponible |
|---| SAP HANA en Azure S144m<br /> – 4 procesadores Intel® Xeon® E7-8890 v3 |  3,0 TB |  12 TB | No disponible |
|---| SAP HANA en Azure S192m<br /> – 4 procesadores Intel® Xeon® E7-8890 v4 |  4,0 TB |  16 TB | Disponible |
|---| SAP HANA en Azure S384m<br /> – 8 procesadores Intel® Xeon® E7-8890 v4 |  6,0 TB |  18 TB | Listo para comprar |
|---| SAP HANA en Azure S384xm<br /> – 8 procesadores Intel® Xeon® E7-8890 v4 |  8,0 TB |  22 TB |  Listo para comprar |
|---| SAP HANA en Azure S576<br /> – 12 procesadores Intel® Xeon® E7-8890 v4 |  12,0 TB |  28 TB | Listo para comprar |
|---| SAP HANA en Azure S768<br /> – 16 procesadores Intel® Xeon® E7-8890 v4 |  16,0 TB |  36 TB | Listo para comprar |
|---| SAP HANA en Azure S960<br /> – 20 procesadores Intel® Xeon® E7-8890 v4 |  20,0 TB |  46 TB | Listo para comprar |
| --- | --- | --- | --- | --- |

Las diferentes configuraciones anteriores que están disponibles o no disponibles se documentan en [SAP Note #2316233 - SAP HANA on Microsoft Azure (Large Instances)](https://launchpad.support.sap.com/#/notes/2316233/E) (Nota de SAP 2316233: SAP HANA en Microsoft Azure [instancias grandes]). Las configuraciones, que están marcadas como "Listo para comprar" tendrán en breve su entrada en la nota de SAP. Pero las SKU de esas instancias ya se pueden pedir para las seis regiones de Azure en las que está disponible el servicio de instancias grandes de HANA.

Las configuraciones específicas que se elijan dependen de la carga de trabajo, los recursos de CPU y la memoria que desee. La carga de trabajo de OLTP puede aprovechar las SKU que estén optimizadas para cargas de trabajo de OLAP. 

La base de hardware para todas las ofertas con certificación SAP HANA TDI. Pero se distingue entre dos clases diferentes de hardware, que divide las SKU en:

- S72, S72m, S144, S144m, S192 y S192m, a las que se hace referencia como "clase de tipo I" de SKU.
- S384, S384m, S384xm, S576, S768 y S960, a las que se hace referencia como "clase de tipo II" de SKU.

Es importante tener en cuenta que la infraestructura completa del sello de instancia grande de HANA no está asignada exclusivamente para que la use un único cliente. Esto se aplica a los bastidores de recursos de procesos y almacenamiento conectados mediante un tejido de red implementado en Azure. La infraestructura de Instancias grandes de HANA, como Azure, implementa &quot;inquilinos&quot; de clientes diferentes que están aislados entre sí mediante aislamiento de red. Por lo tanto, estas implementaciones de Instancias grandes de HANA no interfieren entre sí ni son visibles unas a otras. Con respecto a la facturación, se asigna un inquilino implementado en el sello de instancia grande a una suscripción de Azure. Pero con respecto a la red, se puede tener acceso desde redes virtuales de Azure de otras suscripciones de Azure dentro de la misma inscripción de Azure. También puede elegir pedir un inquilino de instancia grande de HANA independiente si implementa con otra suscripción de Azure en la misma región de Azure.

Sin embargo, existen diferencias notables entre ejecutar SAP HANA en Instancias grandes de HANA y ejecutar SAP HANA en máquinas virtuales de Azure implementadas en Azure:

- No hay ningún nivel de virtualización para SAP HANA en Azure (Instancias grandes). El rendimiento proviene del hardware sin sistema operativo subyacente.
- A diferencia de Azure, el servidor de SAP HANA en Azure (Instancias grandes) está dedicado a un cliente específico. Tras un reinicio o un apagado del servidor, no se implementa el sistema operativo ni SAP HANA en otro servidor. (Para las SKU de clase de tipo I, la única excepción es si un servidor puede encontrar problemas y se tiene que volver a implementar en otro servidor).
- A diferencia de Azure, donde los tipos de procesador de host se seleccionan para lograr una relación precio/rendimiento óptima, los tipos de procesador elegidos para SAP HANA en Azure (instancias grandes) son los de mayor rendimiento en la línea de procesadores Intel E7v3 y E7v4.

Se implementan varios clientes en el sello de instancia grande de SAP HANA y cada uno está separado de los demás porque se implementa en su propia red VLAN. Para conectar instancias grandes de HANA a una red virtual de Azure (VNet), los componentes de red conectan las unidades de instancias grandes de HANA de inquilinos de manera aislada a las redes virtuales de Azure de la suscripción de Azure de los inquilinos. 

### <a name="running-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Ejecución de varias instancias de SAP HANA en una unidad de instancia grande de HANA
Se puede hospedar más de una instancia de SAP HANA activa en las unidades de instancia grande de HANA. Con el fin de proporcionar las capacidades de instantáneas de almacenamiento y recuperación ante desastres, esta configuración requiere un volumen por cada instancia. A partir de ahora, las unidades de instancia grande de HANA se pueden subdividir de esta forma:

- S72, S72m, S144, S192: en incrementos de 256 GB con la unidad más pequeña inicial de 256 GB. Se pueden combinar incrementos diferentes como 256 GB, 512 GB y así sucesivamente con el máximo de memoria de la unidad.
- S144m y S192m: en incrementos de 256 GB con la unidad más pequeña de 512 GB. Se pueden combinar incrementos diferentes como 512 GB, 768 GB y así sucesivamente con el máximo de memoria de la unidad.
- Clase de tipo II: en incrementos de 512 GB con la unidad más pequeña inicial de 2 TB. Se pueden combinar incrementos diferentes como 512 GB, 1 TB, 1,5 TB y así sucesivamente con el máximo de memoria de la unidad.

Algunos ejemplos de la ejecución de varias instancias de SAP HANA tendrían este aspecto:

| SKU | Tamaño de memoria | Tamaño de almacenamiento | Tamaños con varias bases de datos |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 instancia de HANA de 768 GB<br /> o 1 instancia de 512 GB + 1 instancia de 256 GB<br /> o 3 instancias de 256 GB | 
| S72m | 768 GB | 3 TB | 3 instancias de HANA de 512 GB<br />o 1 instancia de 512 GB + 1 instancia de 1 TB<br />o 6 instancias de 256 GB<br />o 1 instancia de 1,5 TB | 
| S192m | 4 TB | 16 TB | 8 instancias de 512 GB<br />o 4 instancias de 1 TB<br />o 4 instancias de 512 GB + 2 instancias de 1 TB<br />o 4 instancias de 768 GB + 2 instancias de 512 GB<br />o 1 instancia de 4 TB |
| S384xm | 8 TB | 22 TB | 4 instancias de 2 TB<br />o 2 instancias de 4 TB<br />o 2 instancias de 3 TB + 1 instancia de 2 TB<br />o 2 instancias de 2,5 TB + 1 instancia de 3 TB<br />o 1 instancia de 8 TB |


Se hace a la idea. Por supuesto, también hay otras variaciones. 


## <a name="operations-model-and-responsibilities"></a>Modelo de operaciones y responsabilidades

El servicio proporcionado con SAP HANA en Azure (Instancias grandes) se alinea con los servicios de IaaS de Azure. Obtiene una instancia de Instancias grandes de HANA con un sistema operativo instalado que está optimizado para SAP HANA. Al igual que con las máquinas virtuales de IaaS de Azure, la mayoría de las tareas necesarias para proteger el sistema operativo, instalar el software adicional que necesite, instalar HANA, operar el sistema operativo y HANA, y actualizar el sistema operativo y HANA son de su responsabilidad. Microsoft no exige que actualice el sistema operativo ni HANA.

![Responsabilidades de SAP HANA en Azure (Instancias grandes)](./media/hana-overview-architecture/image2-responsibilities.png)

Como ve en el diagrama anterior, SAP HANA en Azure (Instancias grandes) es una oferta de infraestructura como servicio multiinquilino. Como resultado, la división de responsabilidad reside en el límite entre el sistema operativo y la infraestructura, en su mayor parte. Microsoft se responsabiliza de todos los aspectos del servicio por debajo de la línea del sistema operativo y el usuario es responsable de aquellos por encima de la línea, incluido el sistema operativo. Por tanto, puede seguir usando la mayor parte de los métodos locales que emplee actualmente para el cumplimiento normativo, la seguridad, la administración de aplicaciones, la base y la administración del sistema operativo. Los sistemas aparecen como si estuvieran en la red en todos los aspectos.

Sin embargo, este servicio está optimizado para SAP HANA, por lo que hay áreas en que debe colaborar con Microsoft para usar las funcionalidades de la infraestructura subyacente a fin de obtener resultados óptimos.

En la lista siguiente, se proporcionan más detalles sobre cada una de las capas y sus responsabilidades:

**Redes:** todas las redes internas para el sello de instancias grandes que ejecuta SAP HANA, su acceso al almacenamiento, la conectividad entre las instancias (para el escalado horizontal y otras funciones), la conectividad con el entorno y la conectividad a Azure cuando la capa de la aplicación de SAP se hospeda en Azure Virtual Machines. También incluye la conectividad de WAN entre centros de datos de Azure para la replicación con fines de recuperación ante desastres. El inquilino se encarga de dividir todas las redes en particiones, que tienen QOS aplicada.

**Almacenamiento:** almacenamiento virtualizado con particiones para todos los volúmenes que los servidores de SAP HANA necesitan, así como para las instantáneas. 

**Servidores:** los servidores físicos dedicados a ejecutar las bases de datos de SAP HANA asignadas a los inquilinos. Los servidores de clase de tipo I de SKU se abstraen del hardware. Con estos tipos de servidores, la configuración del servidor se recopila y se mantiene en perfiles, que se pueden mover de un hardware físico a otro hardware físico. Este movimiento (manual) de un perfil mediante operaciones se puede comparar ligeramente a la recuperación de servicios de Azure. Los servidores de las SKU de clase de tipo II no ofrecen este tipo de capacidad.

**SDDC:** Software de administración que sirve para administrar un centro de datos como entidad definida por software. Permite a Microsoft agrupar recursos por motivos de escalabilidad, disponibilidad y rendimiento.

**SO:** el sistema operativo que elija (SUSE Linux o Red Hat Linux) que se ejecuta en los servidores. Las imágenes de sistema operativo que se le proporcionan son las imágenes ofrecidas por el proveedor de Linux individual a Microsoft a fin de ejecutar SAP HANA. Debe tener una suscripción con el proveedor de Linux para la imagen optimizada de SAP HANA específica. Sus responsabilidades incluyen el registro de las imágenes con el proveedor del sistema operativo. Desde el momento en que Microsoft le hace entrega del producto, también recae sobre usted la responsabilidad de cualquier revisión adicional del sistema operativo Linux. Esta aplicación de revisiones incluye paquetes adicionales que podrían ser necesarios para una correcta instalación de SAP HANA (consulte la documentación de la instalación de SAP HANA y las notas de SAP) y que no se hayan incluido por el proveedor específico de Linux en sus imágenes de sistema operativo optimizadas para SAP HANA. La responsabilidad del cliente también incluye la aplicación de revisiones del sistema operativo vinculadas a un funcionamiento inadecuado u optimización del sistema operativo y sus controladores en relación con el hardware de servidor específico. O cualquier revisión funcional o de seguridad del sistema operativo. Es también responsabilidad del cliente la supervisión y planificación de capacidad de:

- Consumo de recursos de CPU
- Consumo de memoria
- Volúmenes de disco relacionados con espacio disponible, IOPS y latencia
- Tráfico de volumen de red entre Instancias grandes de HANA y la capa de la aplicación de SAP

La infraestructura subyacente de Instancias grandes de HANA proporciona funcionalidad para las copias de seguridad y la restauración del volumen del sistema operativo. Usar esta funcionalidad es también su responsabilidad.

**Middleware:** la instancia de SAP HANA, principalmente. La administración, las operaciones y la supervisión son responsabilidad suya. Se proporciona funcionalidad que permite usar instantáneas de almacenamiento para la copia de seguridad y la restauración, además de para la recuperación ante desastres. Estas capacidades se proporcionan con la infraestructura. Pero sus responsabilidades incluyen también el diseño de la alta disponibilidad o la recuperación ante desastres con estas capacidades, aprovecharlas y supervisar que las instantáneas de almacenamiento se hayan ejecutado correctamente.

**Datos:** los datos administrados por SAP HANA y otros datos, como archivos de copia de seguridad que se encuentran en volúmenes o recursos compartidos de archivos. Sus responsabilidades incluyen la supervisión del espacio disponible en disco y la administración del contenido en los volúmenes, así como la supervisión de la ejecución correcta de las copias de seguridad de volúmenes de disco e instantáneas de almacenamiento. Sin embargo, la ejecución correcta de la replicación de datos en sitios de recuperación ante desastres es responsabilidad de Microsoft.

**Aplicaciones:** las instancias de aplicación de SAP o, en el caso de aplicaciones no de SAP, la capa de la aplicación de dichas aplicaciones. Entre sus responsabilidades se encuentran la implementación, la administración, las operaciones y la supervisión de esas aplicaciones en relación con el planeamiento de capacidad para el consumo de recursos de CPU, el consumo de memoria, el consumo de Azure Storage y el consumo de ancho de banda de red dentro de redes virtuales de Azure y desde redes virtuales de Azure hacia SAP HANA en Azure (instancias grandes).

**WAN:** Las conexiones que establece entre la implementación local y las implementaciones de Azure para cargas de trabajo. Todos nuestros clientes con instancias grandes de HANA usan Azure ExpressRoute para la conectividad. Esta conexión no forma parte de la solución SAP HANA en Azure (Instancias grandes), por lo que será responsabilidad suya configurarla.

**Archivado:** es posible que prefiera archivar copias de los datos con sus propios métodos en cuentas de almacenamiento. El archivado conlleva tareas de administración, cumplimiento normativo, costos y operaciones. Es su responsabilidad generar copias de archivado y copias de seguridad en Azure, y almacenarlas con un método compatible.

Consulte el [Acuerdo de Nivel de Servicio para SAP HANA en Azure (Instancias grandes)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Ajuste de tamaño

El ajuste de tamaño para Instancias grandes de HANA es igual que para HANA en general. Para los sistemas existentes e implementados, debería cambiar de otro RDBMS a HANA. SAP ofrece una serie de informes que se ejecutan en los sistemas SAP existentes. Si la base de datos se mueve a HANA, estos informes comprueban los datos y calculan los requisitos de memoria para la instancia de HANA. Lea las siguientes notas de SAP para más información sobre cómo ejecutar estos informes y cómo obtener las revisiones o versiones más recientes:

- [SAP Note #1793345 - Sizing for SAP Suite on HANA](https://launchpad.support.sap.com/#/notes/1793345) (Nota de SAP 1793345: Ajuste de tamaño para SAP Suite en HANA)
- [SAP Note #1872170 - Suite on HANA and S/4 HANA sizing report](https://launchpad.support.sap.com/#/notes/1872170) (Nota de SAP 1872170: Informe de ajuste de tamaño de Suite en HANA y S/4 HANA)
- [SAP Note #2121330 - FAQ: SAP BW on HANA Sizing Report](https://launchpad.support.sap.com/#/notes/2121330) (Nota de SAP 2121330: P+F: Informe de ajuste de tamaño de SAP BW en HANA)
- [SAP Note #1736976 - Sizing Report for BW on HANA](https://launchpad.support.sap.com/#/notes/1736976) (Nota de SAP 1736976: Informe de ajuste de tamaño para BW en HANA)
- [SAP Note #2296290 - New Sizing Report for BW on HANA](https://launchpad.support.sap.com/#/notes/2296290) (Nota de SAP 2296290: Nuevo informe de ajuste de tamaño para BW en HANA)

Para las implementaciones en green field, está disponible SAP Quick Sizer para calcular los requisitos de memoria de la implementación de software de SAP sobre HANA.

Los requisitos de memoria de HANA crecen a medida que aumenta el volumen de datos, por lo que debe tener en cuenta el consumo actual de memoria y una previsión de cuál va a ser en el futuro. En función de los requisitos de memoria, puede asignar la demanda a una de las SKU de Instancias grandes de HANA.

## <a name="requirements"></a>Requisitos

En esta lista se recopilan los requisitos para ejecutar SAP HANA en Azure (instancias grandes).

**Microsoft Azure:**

- Una suscripción de Azure que se pueda vincular a SAP HANA en Azure (Instancias grandes).
- Contrato de soporte técnico Premier de Microsoft. Consulte [SAP Support Note #2015553 – SAP on Microsoft Azure: Support Prerequisites](https://launchpad.support.sap.com/#/notes/2015553) (Nota de SAP sobre soporte 2015553: Requisitos previos de SAP en Microsoft Azure) para información específica relacionada con la ejecución de SAP en Azure. Con unidades de instancia grande de HANA con 384 o más CPU, también necesita extender el contrato de soporte técnico Premier para incluir Respuesta rápida de Azure (ARR).
- Conocimiento de las SKU de las instancias grandes de HANA que necesita después de realizar un ajuste de tamaño con SAP.

**Conectividad de red:**

- Azure ExpressRoute entre la infraestructura local y la de Azure: asegúrese de pedir a su ISP una conexión de 1 Gbps como mínimo para conectar el centro de datos local a Azure. 

**Sistema operativo:**

- Licencias para SUSE Linux Enterprise Server 12 for SAP Applications.

> [!NOTE] 
> El sistema operativo entregado por Microsoft no está registrado con SUSE ni está conectado a una instancia de SMT.

- SUSE Linux Subscription Management Tool (SMT) implementada en Azure en una máquina virtual de Azure. Esto hace posible que SUSE registre SAP HANA en Azure (Instancias grandes) y la actualice (ya que se carece de acceso a Internet en el centro de datos de Instancias grandes de HANA). 
- Licencias para Red Hat Enterprise Linux 6.7 o 7.2 para SAP HANA.

> [!NOTE]
> El sistema operativo entregado por Microsoft no está registrado con Red Hat ni está conectado a una instancia Red Hat Subscription Manager.

- Red Hat Subscription Manager implementado en Azure en una máquina virtual de Azure. El administrador de suscripciones de Red Hat hace posible que se registre SAP HANA en Azure (Instancias grandes) y se actualice (ya que se carece de acceso directo a Internet desde el inquilino implementado en el sello de instancias grandes de Azure).
- SAP requiere que también disponga de un contrato de soporte con el proveedor de Linux. Este requisito no se elimina con la solución de instancias grandes de HANA ni por el hecho de ejecutar Linux en Azure. A diferencia de lo que ocurre con algunas de las imágenes de la galería de Azure para Linux, la tarifa del servicio NO se incluye en la oferta de la solución de instancias grandes de HANA. Actúa como un cliente para cumplir los requisitos de SAP en relación con los contratos de soporte técnico con el distribuidor de Linux.   
   - Si se trata de SUSE Linux, busque los requisitos del contrato de soporte técnico en [SAP Note #1984787 - SUSE LINUX Enterprise Server 12: Installation notes](https://launchpad.support.sap.com/#/notes/1984787) (Nota de SAP 1984787: SUSE LINUX Enterprise Server 12: notas de instalación) y [SAP Note #1056161 - SUSE Priority Support for SAP applications](https://launchpad.support.sap.com/#/notes/1056161) (Nota de SAP 1056161: Soporte con prioridad de SUSE LINUX para aplicaciones SAP).
   - Para Red Hat Linux, necesita tener los niveles de suscripción adecuados que incluyan soporte técnico y servicios (actualizaciones del sistema operativo de instancias grandes de HANA). Red Hat recomienda obtener una suscripción a "RHEL for SAP Business Applications". En lo que respecta al soporte técnico y los servicios, consulte [SAP Note #2002167 - Red Hat Enterprise Linux 7.x: Installation and Upgrade](https://launchpad.support.sap.com/#/notes/2002167) (Nota de SAP 2002167: Instalación y actualización de Red Hat Enterprise Linux 7.x) y [SAP Note #1496410 - Red Hat Enterprise Linux 6.x: Installation and Upgrade](https://launchpad.support.sap.com/#/notes/1496410) (Nota de SAP 1496410: Instalación y actualización de Red Hat Enterprise Linux 6.x) para obtener información detallada.

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

**SAP:**

- Se espera que sea un cliente de SAP y que tenga un contrato de soporte técnico con SAP.
- Especialmente para las implementaciones de las SKU de clase de tipo II de instancia grande de HANA, se recomienda consultar con SAP las versiones de SAP HANA y las configuraciones posibles en hardware de escalado vertical de tamaño grande.


## <a name="storage"></a>Almacenamiento

El diseño de almacenamiento para SAP HANA en Azure (instancias grandes) lo configura SAP HANA en Azure Service Management a través de directrices recomendadas de SAP, documentadas en las notas del producto [SAP HANA Storage Requirements](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Requisitos de almacenamiento de SAP HANA).

Las instancias grandes de HANA de clase de tipo I suelen tener cuatro veces el volumen de memoria como volumen de almacenamiento. Para las unidades de instancia grande de HANA de clase de tipo II, el almacenamiento no va a ser más de cuatro veces superior. Las unidades tienen un volumen previsto para almacenar copias de seguridad del registro de transacciones de HANA. Obtenga más información en [Procedimiento para instalar y configurar SAP HANA en Azure (instancias grandes)](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Vea la tabla siguiente en relación a la asignación de almacenamiento. En la tabla se enumera la capacidad aproximada de los diferentes volúmenes que se proporcionan con las diferentes unidades de instancia grande de HANA.

| SKU de Instancias grandes de HANA | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| S72 | 1280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3328 GB | 768 GB |1280 GB | 768 GB |
| S192 | 4608 GB | 1.024 GB | 1536 GB | 1.024 GB |
| S192m | 11.520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384 | 11.520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384m | 12.000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xm | 16.000 GB | 2050 GB | 2050 GB | 2040 GB |
| S576 | 20.000 GB | 3100 GB | 2050 GB | 3100 GB |
| S768 | 28.000 GB | 3100 GB | 2050 GB | 3100 GB |
| S960 | 36.000 GB | 4100 GB | 2050 GB | 4100 GB |


Los volúmenes implementados reales pueden variar ligeramente en función de la implementación y la herramienta que se usa para mostrar el tamaño de los volúmenes.

Si subdivide una SKU de instancia grande de HANA, algunos ejemplos de los posibles fragmentos de división serían como sigue:

| Partición de memoria en GB | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1280 GB | 512 GB | 768 GB | 512 GB |
| 1024 | 1792 GB | 640 GB | 1.024 GB | 640 GB |
| 1536 | 3328 GB | 768 GB | 1280 GB | 768 GB |


Estos tamaños son números de volumen aproximados que pueden variar ligeramente en función de la implementación y las herramientas que se usan para analizar los volúmenes. También hay otros tamaños de partición posibles, como 2,5 TB. Estos tamaños de almacenamiento se calcularían con una fórmula similar a la que se usó para las particiones anteriores. El término "particiones" no indica en absoluto que el sistema operativo, la memoria o los recursos de CPU se dividan en particiones. Solo indica las particiones de almacenamiento para las distintas instancias de HANA que es posible que quiera implementar en una única unidad de instancia grande de HANA. 

Como cliente es posible que necesite más almacenamiento y tendrá la posibilidad de agregarlo si compra almacenamiento adicional en unidades de 1 TB. Este almacenamiento adicional se puede agregar como un volumen adicional o puede usarse para extender uno o varios de los volúmenes existentes. No es posible reducir el tamaño de los volúmenes que implementó originalmente y que se documentan principalmente en las tablas anteriores. Tampoco es posible cambiar los nombres de los volúmenes o los de montaje. Los volúmenes de almacenamiento descritos anteriormente se adjuntan a las unidades de instancia grande de HANA como volúmenes NFS4.

Como cliente puede elegir usar las instantáneas de almacenamiento para fines de recuperación ante desastres y de copia de seguridad y restauración. Puede encontrar más información sobre este tema en [Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes)](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="encryption-of-data-at-rest"></a>Cifrado de datos en reposo
El almacenamiento usado para instancias grandes de HANA permite un cifrado transparente de los datos tal y como se almacenan en los discos. Durante el período de implementación de una unidad de instancia grande de HANA, tiene la opción de tener este tipo de cifrado habilitado. También puede optar por cambiar los volúmenes cifrados después de que se complete la implementación. El movimiento de volúmenes no cifrados a cifrados es transparente y no requiere ningún tiempo de inactividad. 

Con las SKU de clase de tipo I, se cifra el volumen en el que está almacenado el LUN de arranque. En el caso de las SKU de clase de tipo II de instancias grandes de HANA, tendrá que cifrar el LUN de arranque con los métodos del sistema operativo. 


## <a name="networking"></a>Redes

La arquitectura de las redes de Azure es un componente clave para implementar las aplicaciones de SAP de forma correcta en instancias grandes de HANA. Por lo general, las implementaciones de SAP HANA en Azure (Instancias grandes) tienen un entorno de SAP más amplio con varias soluciones SAP distintas y diversos tamaños de base de datos, consumo de recursos de CPU y utilización de memoria. Es probable que no todos esos sistemas SAP estén basados en SAP HANA, por lo que el entorno de SAP probablemente sea un entorno híbrido que use:

- Sistemas SAP implementados localmente. Debido a su tamaño, actualmente estos sistemas no se pueden hospedar en Azure; un ejemplo típico sería un sistema SAP ERP de producción con Microsoft SQL Server (como base de datos), que requiere más recursos de CPU y memoria de los que las máquinas virtuales de Azure pueden proporcionar.
- Sistemas SAP basados en SAP HANA implementados localmente.
- Sistemas SAP implementados en máquinas virtuales de Azure. Estos sistemas pueden ser instancias de desarrollo, pruebas, espacio aislado o producción para cualquiera de las aplicaciones basadas en SAP NetWeaver que se pueden implementar correctamente en Azure (en máquinas virtuales), en función de la demanda de memoria y el consumo de recursos. Estos sistemas también podrían basarse en bases de datos como SQL Server (vea [SAP Support Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533/E) [Nota de SAP 1928533: Aplicaciones de SAP en Azure: productos y tipos de máquina virtual de Azure admitidos]) o SAP HANA (vea [SAP HANA Certified IaaS Platforms](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) [Plataformas IaaS certificadas para SAP HANA]).
- Servidores de aplicaciones de SAP implementados en Azure (en máquinas virtuales) que aprovechan SAP HANA en Azure (Instancias grandes) en sellos de instancias grandes de Azure.

Aunque un entorno híbrido de SAP (con cuatro o más escenarios de implementación diferentes) es habitual, hay muchos casos de cliente de entornos de SAP completos que se ejecutan en Azure. Con el aumento de la eficacia de las máquinas virtuales de Microsoft Azure, aumenta el número de clientes que mueven todas sus soluciones SAP a Azure.

Las redes de Azure en el contexto de los sistemas SAP implementados en Azure no son complicadas. Se basan en los siguientes principios:

- Las redes virtuales de Azure deben estar conectadas al circuito de Azure ExpressRoute que se conecta a la red local.
- Normalmente, un circuito ExpressRoute que se conecte en local a Azure necesitaría un ancho de banda de 1 Gbps o más. Este ancho de banda mínimo ofrece el ancho de banda suficiente para transferir datos entre sistemas locales y sistemas que se ejecutan en máquinas virtuales de Azure (así como la conexión a sistemas de Azure desde el entorno local de los usuarios finales).
- Todos los sistemas SAP en Azure deben configurarse en redes virtuales de Azure para poder comunicarse entre sí.
- Active Directory y DNS hospedados localmente se extienden a Azure a través de ExpressRoute desde un entorno local.


> [!NOTE] 
> Desde el punto de vista de la facturación, una sola suscripción de Azure solo se puede vincular a un único inquilino en un sello de instancias grandes en una región de Azure específica y, por el contrario, un único inquilino del sello de instancias grandes solo se puede vincular a una única suscripción de Azure. Este hecho no es diferente a cualquier otro objeto facturable en Azure.

Si se implementa SAP HANA en Azure (instancias grandes) en varias regiones de Azure diferentes, se implementará un inquilino diferente en el sello de instancias grandes. Sin embargo, puede ejecutar las dos en la misma suscripción de Azure, siempre que estas instancias formen parte del mismo entorno de SAP. 

> [!IMPORTANT] 
> La única implementación compatible con SAP HANA en Azure (Instancias grandes) es la de Azure Resource Manager.

 

### <a name="additional-azure-vnet-information"></a>Información adicional sobre las redes virtuales de Azure

Para conectar una red virtual de Azure con ExpressRoute, se debe crear una puerta de enlace de Azure (consulte [Acerca de las puertas de enlace de red virtual para ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Se puede usar una puerta de enlace de Azure con ExpressRoute hacia una infraestructura externa a Azure (o hacia un sello de grandes instancias de Azure) o para conectarse entre redes virtuales de Azure (consulte [Configuración de una conexión de red virtual a red virtual para Resource Manager mediante PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Puede conectar la puerta de enlace de Azure a un máximo de cuatro conexiones ExpressRoute diferentes, siempre que procedan de diferentes enrutadores perimetrales de Microsoft Enterprise (MSEE).  Para más información, vea [Infraestructura y conectividad con SAP HANA en Azure (instancias grandes)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> El rendimiento proporcionado por una puerta de enlace de Azure es distinto para ambos casos de uso (consulte [Acerca de VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). El rendimiento máximo que se puede lograr con una puerta de enlace de red virtual es de 10 Gbps, mediante una conexión ExpressRoute. Tenga en cuenta que la copia de archivos entre una máquina virtual de Azure que resida en una red virtual de Azure y un sistema local (como una sola secuencia de copia) no consigue el rendimiento total de las diferentes SKU de puerta de enlace. Para aprovechar el ancho de banda completo de la puerta de enlace de red virtual, debe usar varias secuencias o copiar diferentes archivos en secuencias paralelas de un único archivo.


### <a name="networking-architecture-for-hana-large-instances"></a>Arquitectura de red para instancias grandes de HANA
La arquitectura de red para instancias grandes de HANA se puede dividir en cuatro partes distintas, como se muestra a continuación:

- Redes locales y conexión ExpressRoute a Azure. Esta parte es el dominio de los clientes y se conecta a Azure a través de ExpressRoute. Esta es la parte en la esquina inferior derecha del gráfico siguiente.
- Redes de Azure con redes virtuales de Azure, como se explicó brevemente antes, que de nuevo tienen puertas de enlace. Se trata de un área donde debe buscar los diseños adecuados para los requisitos de las aplicaciones, de seguridad y cumplimiento. El uso de instancias grandes de HANA es otro punto para tener en cuenta con respecto al número de redes virtuales y SKU de puerta de enlace de Azure para elegir. Esta es la parte en la esquina superior derecha del gráfico.
- Conectividad de instancias grandes de HANA a través de la tecnología de ExpressRoute en Azure. Microsoft implementa y controla esta parte. Lo único que tiene que hacer como cliente consiste en proporcionar algunos intervalos de direcciones IP después de la implementación de los recursos en instancias grandes de HANA que conectan el circuito ExpressRoute a las redes virtuales de Azure (vea [Infraestructura y conectividad con SAP HANA en Azure (instancias grandes)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 
- Redes en instancias grandes de HANA, lo que es principalmente transparente para usted como cliente.

![Red virtual de Azure conectada a SAP HANA en Azure (Instancias grandes) y localmente](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

El hecho de que use instancias grandes de HANA no cambia el requisito de tener que conectar los recursos locales a Azure a través de ExpressRoute. No cambia el requisito de tener una o varias redes virtuales que ejecutan las máquinas virtuales de Azure que hospedan el nivel de aplicación que se conecta a las instancias de HANA que se hospedan en unidades de instancia grande de HANA. 

La diferencia con las implementaciones de SAP puramente en Azure se debe a los siguientes hechos:

- Las unidades de instancia grande de HANA del inquilino del cliente se conectan a las redes virtuales de Azure través de otro circuito ExpressRoute. Con el fin de separar las condiciones de carga, los vínculos de ExpressRoute del entorno local a las redes virtuales de Azure y los vínculos entre las redes virtuales de Azure y las instancias grandes de HANA no comparten los mismos enrutadores.
- La naturaleza del perfil de carga de trabajo entre la capa de aplicación de SAP y la instancia de HANA es diferente y tiene muchas solicitudes pequeñas y transferencias de datos como ráfagas (conjuntos de resultados) desde SAP HANA al nivel de la aplicación.
- La arquitectura de aplicación de SAP es más sensible a la latencia de la red que los escenarios típicos donde los datos se intercambian entre el entorno local y Azure.
- La puerta de enlace de red virtual tiene al menos dos conexiones ExpressRoute y ambas comparten el ancho de banda máximo de la puerta de enlace de red virtual para los datos de entrada.

La latencia de red que se experimenta entre las máquinas virtuales de Azure y las unidades de instancia grande de HANA puede ser mayor que una latencia de red de recorrido de ida y vuelta entre máquinas virtuales. En función de la región de Azure, los valores medidos pueden superar la latencia de recorrido de ida y vuelta de 0,7 ms clasificada como por debajo del promedio en [SAP Note #1100926 - FAQ: Network performance](https://launchpad.support.sap.com/#/notes/1100926/E) (Nota de SAP 1100926: Preguntas más frecuentes sobre el rendimiento de red). Pero los clientes han implementado correctamente aplicaciones de SAP de producción basadas en SAP HANA en instancias grandes de SAP HANA. Los clientes que realizaron la implementación notificaron grandes mejoras al ejecutar sus aplicaciones SAP en SAP HANA con unidades de instancia grande de HANA. Pero debe probar exhaustivamente los procesos empresariales en instancias grandes de HANA en Azure.
 
Para proporcionar la latencia de red determinista entre las máquinas virtuales de Azure y la instancia grande de HANA, la elección de la SKU de la puerta de enlace de red virtual de Azure es esencial. A diferencia de los patrones de tráfico entre el entorno local y las máquinas virtuales de Azure, el patrón de tráfico entre máquinas virtuales de Azure e instancias grandes de HANA puede generar pequeñas pero elevadas ráfagas de solicitudes y volúmenes de datos para transmitir. Para poder controlar correctamente esas ráfagas, se recomienda encarecidamente el uso de la SKU de puerta de enlace UltraPerformance. Para las SKU de clase de tipo II de instancia grande de HANA, es obligatorio el uso de la SKU de puerta de enlace UltraPerformance como puerta de enlace de red virtual de Azure.  

> [!IMPORTANT] 
> Dado el tráfico de red general entre la capa de base de datos y la de la aplicación de SAP, solo se admiten las SKU de puerta de enlace HighPerformance o UltraPerformance para redes virtuales para la conexión a SAP HANA en Azure (Instancias grandes). Para las SKU de la clase de tipo II de instancia grande de HANA, solo se admite la SKU de puerta de enlace UltraPerformance como puerta de enlace de red virtual de Azure.



### <a name="single-sap-system"></a>Único sistema SAP

La infraestructura local mostrada arriba se conecta a través de ExpressRoute con Azure, mientras que el circuito de ExpressRoute se conecta a un enrutador de perímetro de Microsoft Enterprise (MSEE) (consulte [Información técnica de ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Una vez establecida, esa ruta se conecta a la red troncal de Microsoft Azure, y todas las regiones de Azure son accesibles.

> [!NOTE] 
> Para ejecutar entornos de SAP en Azure, conéctese al MSEE más cercano a la región de Azure en el entorno de SAP. Los sellos de instancias grandes de Azure están conectados a través de dispositivos MSEE dedicados para minimizar la latencia de red entre máquinas virtuales de Azure en sellos de grandes instancias e IaaS de Azure.

La puerta de enlace de red virtual para las máquinas virtuales de Azure, que hospedan instancias de aplicación de SAP, está conectada a ese circuito de ExpressRoute y la misma red virtual está conectada a un enrutador MSEE independiente dedicado a la conexión con sellos de instancias grandes.

Se trata de un ejemplo sencillo de un único sistema SAP, en el que la capa de la aplicación de SAP se hospeda en Azure y la base de datos de SAP HANA se ejecuta en SAP HANA en Azure (Instancias grandes). Se supone que el ancho de banda de la puerta de enlace de red virtual de 2 Gbps o 10 Gbps de rendimiento no representa un cuello de botella.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Varios sistemas SAP o sistemas SAP grandes

Si se implementan varios sistemas SAP o sistemas SAP grandes que se conectan a SAP HANA en Azure (instancias grandes), es razonable suponer que el rendimiento de la puerta de enlace de red virtual pueda convertirse en un cuello de botella. En ese caso, debe dividir los niveles de aplicación en varias redes virtuales de Azure. También puede ser recomendable crear redes virtuales especiales que se conectan a instancias grandes de HANA para casos como:

- Realizar copias de seguridad directamente desde las instancias de HANA en instancias grandes de HANA a una máquina virtual de Azure que hospeda los recursos compartidos de NFS.
- Copiar las copias de seguridad de gran tamaño u otros archivos de unidades de instancias grandes de HANA en espacio en disco administrado en Azure.

Usar redes virtuales independientes que hospedan máquinas virtuales que administran el almacenamiento impide que los archivos grandes o la transferencia de datos de instancias grandes de HANA a Azure repercutan en la puerta de enlace de la red virtual que abastece a las máquinas virtuales que se ejecutan en la capa de aplicación de SAP. 

Para una arquitectura de red más escalable:

- Aproveche varias redes virtuales de Azure para una sola capa de la aplicación de SAP más grande.
- Implemente una red virtual de Azure distinta para cada sistema SAP implementado, en comparación con la combinación de estos sistemas SAP en subredes independientes en la misma red virtual.

 Una arquitectura de red más escalable para SAP HANA en Azure (Instancias grandes):

![Implementación de la capa de la aplicación de SAP en varias redes virtuales de Azure](./media/hana-overview-architecture/image4-networking-architecture.png)

La implementación de la capa de la aplicación de SAP, o componentes, en varias redes virtuales de Azure como se muestra arriba, introdujo una sobrecarga ineludible de la latencia durante la comunicación entre las aplicaciones hospedadas en esas redes virtuales de Azure. De manera predeterminada, el tráfico de red entre máquinas virtuales de Azure ubicadas en diferentes redes virtuales discurrirá a través de enrutadores MSEE en esta configuración. Pero desde septiembre de 2016 es posible optimizar este enrutamiento. La manera de optimizar y reducir la latencia en la comunicación entre dos redes virtuales pasa por el emparejamiento de las redes virtuales de Azure que estén en la misma región. Incluso si esas redes virtuales se encuentran en suscripciones diferentes. Gracias al emparejamiento de redes virtuales de Azure, la comunicación entre máquinas virtuales en dos redes virtuales diferentes de Azure puede emplear la red troncal de Azure para permitir una comunicación directa entre ambas. De este modo se consigue una latencia similar a la que se daría si las máquinas virtuales estuvieran en la misma red virtual. Al mismo tiempo, el tráfico que dirige los intervalos de direcciones IP que están conectadas a través de la puerta de enlace de red virtual de Azure se enruta a través de la puerta de enlace de red virtual individual de dicha red. Puede obtener detalles acerca del emparejamiento de redes virtuales de Azure en el artículo [Emparejamiento de VNET](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Enrutamiento en Azure

Existen dos consideraciones importantes sobre el enrutamiento de red para SAP HANA en Azure (Instancias grandes):

1. Las máquinas virtuales de Azure solo pueden acceder a SAP HANA en Azure (Instancias grandes) en la conexión ExpressRoute dedicada, no directamente desde el entorno local. Los clientes de administración y las aplicaciones que necesiten acceso directo, como SAP Solution Manager ejecutado de forma local, no se pueden conectar a la base de datos de SAP HANA.

2. Las unidades SAP HANA en Azure (instancias grandes) tienen una dirección IP asignada del intervalo de direcciones del grupo de direcciones IP del servidor que ha enviado como cliente; vea [Infraestructura y conectividad con SAP HANA en Azure (instancias grandes)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obtener más información.  Se puede acceder a esta dirección IP a través de las suscripciones de Azure y de la instancia de ExpressRoute que conecta las redes virtuales de Azure a HANA en Azure (instancias grandes). La dirección IP asignada de ese intervalo de direcciones del grupo de direcciones IP del servidor se asigna directamente a la unidad de hardware y NO se aplica más la traducción de direcciones de red (NAT), como fue el caso en las primeras implementaciones de esta solución. 

> [!NOTE] 
> Si necesita conectarse a SAP HANA en Azure (Instancias grandes) en un escenario de _almacenamiento de datos_, en el que las aplicaciones o los usuarios finales deben conectarse a la base de datos de SAP HANA (que se ejecuta directamente), debe usarse otro componente de red: un proxy inverso para enrutar los datos en ambos sentidos. Por ejemplo, F5 BIG-IP, NGINX con Traffic Manager implementado en Azure como solución de enrutamiento de tráfico y firewall virtual.

### <a name="internet-connectivity-of-hana-large-instances"></a>Conectividad a Internet de instancias grandes de HANA
Las instancias grandes de HANA NO tienen conectividad directa a Internet. Esto limita su capacidad para, por ejemplo, registrar la imagen del sistema operativo directamente con el proveedor del sistema operativo. Por tanto, tiene que trabajar con el servidor de SMT de SLES local o con el administrador de suscripciones de RHEL.

### <a name="data-encryption-between-azure-vms-and-hana-large-instances"></a>Cifrado de datos entre máquinas virtuales de Azure e instancias grandes de HANA
Los datos transferidos entre las instancias grandes de HANA y las máquinas virtuales de Azure no se cifran. Sin embargo, solo para el intercambio entre el DBMS de HANA y las aplicaciones basadas en JDBC y ODBC, puede habilitar el cifrado del tráfico. Consulte [esta documentación de SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

### <a name="using-hana-large-instance-units-in-multiple-regions"></a>Uso de unidades de instancia grande de HANA en varias regiones

Pueden existir otras razones para implementar SAP HANA en Azure (Instancias grandes) en varias regiones de Azure, además de la recuperación ante desastres. Es posible que desee acceder a Instancias grandes de HANA desde cada una de las máquinas virtuales implementadas en las diferentes redes virtuales en las regiones. Dado que las direcciones IP asignadas a las diferentes unidades de Instancias grandes de HANA no se propagan más allá de las redes virtuales de Azure (que están conectadas directamente a las instancias por medio de su puerta de enlace), hay un pequeño cambio en el diseño de red virtual descrito antes: una puerta de enlace de red virtual de Azure puede controlar cuatro circuitos de ExpressRoute diferentes desde MSEE diferentes, y cada red virtual que esté conectada a uno de los sellos de instancias grandes se puede conectar al sello de instancias grandes en otra región de Azure.

![Redes virtuales de Azure conectadas a sellos de instancias grandes de Azure en diferentes regiones de Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

En la ilustración anterior, se muestra cómo las distintas redes virtuales de Azure en ambas regiones están conectadas a dos circuitos de ExpressRoute diferentes que se utilizan para conectarse a SAP HANA en Azure (Instancias grandes) en ambas regiones de Azure. Las conexiones recién introducidas son las líneas rojas rectangulares. Con estas conexiones fuera de las redes virtuales de Azure, las máquinas virtuales que se ejecutan en una de esas redes virtuales pueden tener acceso a cada una de las diferentes unidades de instancia grande de HANA implementadas en las dos regiones. Como se ve en la ilustración anterior, se supone que tiene dos conexiones ExpressRoute entre la infraestructura local y las dos regiones de Azure; esto está recomendado para la recuperación ante desastres.

> [!IMPORTANT] 
> Si se usan varios circuitos de ExpressRoute, deben utilizarse las configuraciones de anteposición de AS Path y de preferencia local de BGP para garantizar el enrutamiento adecuado del tráfico.



