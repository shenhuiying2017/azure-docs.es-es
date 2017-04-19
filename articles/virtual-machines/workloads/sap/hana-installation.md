---
title: "Instalación de SAP HANA en SAP HANA en Azure (instancias grandes) | Microsoft Docs"
description: "Instalación de SAP HANA en SAP HANA en Azure (instancia grande)."
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
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
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: 61d191b5b2cc18731caacb6d192b851b6b49b22c
ms.lasthandoff: 04/13/2017


---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Procedimiento para instalar y configurar SAP HANA en Azure (instancias grandes)

La instalación de SAP HANA es su responsabilidad y puede hacerlo inmediatamente después de la entrega de un nuevo SAP HANA en el servidor de Azure (instancias de grandes). Tenga en cuenta que, según la directiva de SAP, la instalación de SAP HANA la debe realizar un instalador de SAP HANA certificado (alguien que haya superado el examen de certificación de Certified SAP Technology Associate - Instalación de SAP HANA), o un integrador de sistemas (SI) certificado por SAP.

Existen consideraciones de conectividad específicas relacionadas con SAP HANA (lado de servidor) y SAP HANA (lado de cliente) que deben tenerse en cuenta. En muchos casos, el servidor de SAP HANA envía su dirección IP al cliente donde se almacena en caché y se usa para intentos de conexión posteriores. Puesto que SAP HANA en Azure (instancias grandes) realiza el proceso NAT en la dirección IP de servidor interna utilizada en la red de inquilinos para un intervalo de direcciones IP proporcionado para redes virtuales de Azure especificadas, el servidor de base de datos de SAP HANA, de forma predeterminada, enviaría el intervalo de direcciones IP &quot;interno&quot;. Por ejemplo, para la resolución de nombres de host, en lugar de que SAP HANA proporcione la dirección IP con NAT, se utiliza la dirección IP interna almacenada en caché. Por tanto, una aplicación que use un cliente de SAP HANA (ODBC, JDBC, etc.) no sería capaz de conectarse con esta dirección IP. Para indicar al servidor de SAP HANA que debe propagar la dirección IP con NAT al cliente, se debe editar el archivo de configuración del sistema global de SAP HANA (global.ini).

Agregue lo siguiente al archivo global.ini (ya sea directamente o a través de SAP HANA Studio):
```
[public\_hostname\_resolution]
use\_default\_route = no
map\_<hostname of SAP HANA on Azure (Large Instances) tenant> = <NAT IP Address (IP address that can be accessed in Azure)>
```
Vea la sección _Mapping Host Names for Database Client Access_ (Asignación de nombres de host para el acceso de cliente de base de datos) que encontrará en [SAP HANA Administration Guide](http://help.sap.com/hana/sap_hana_administration_guide_en.pdf) (Guía de administración de SAP HANA) para obtener más detalles y ejemplos.

En el lado cliente (para servidores de aplicaciones de SAP en Azure), edite el archivo /etc/hosts (en Linux) o /system32/drivers/etc/hosts (en Windows Server) e incluya una entrada para el nombre de host de SAP HANA en el inquilino de Azure (instancias grandes) y su dirección IP con NAT correspondiente.

>[!NOTE] 
>Cuando instale el cliente de base de datos de SAP HANA, si hay errores al conectarse a SAP HANA en Azure (instancias grandes) durante la instalación del cliente de base de datos de SAP HANA, utilice la dirección IP NAT del inquilino de instancias grandes de HANA implícitamente en lugar del nombre de host.

## <a name="storage"></a>Almacenamiento

El diseño de almacenamiento para SAP HANA en Azure (instancias grandes) lo configura SAP HANA en Azure Service Management a través de procedimientos recomendados de SAP. Consulte las notas del producto [SAP HANA Storage Requirements](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Requisitos de almacenamiento de SAP HANA).

Las configuraciones exactas del almacenamiento para las distintas instancias grandes de HANA tienen el siguiente aspecto:

| Tamaño de memoria | HANA/datos | HANA/registro | HANA/compartido | HANA/registro/copias de seguridad |
| --- | --- | --- | --- | --- |
| 768 GB | 1280 GB | 512 GB | 768 GB | 512 GB |
| 1536 GB | 3456 GB | 768 GB | 1.024 GB | 768 GB |
| 3072 GB | 7680 GB | 1536 GB | 1.024 GB | 1536 GB |

Además, los clientes pueden adquirir capacidad de almacenamiento adicional en incrementos de 1 TB. Este almacenamiento adicional se puede agregar como nuevos volúmenes a instancias grandes de HANA.

El controlador de almacenamiento y los nodos de las marcas de instancia grandes se sincronizan con los servidores NTP. Mediante la sincronización de SAP HANA en unidades de Azure (instancias grandes) y Azure Virtual Machines con un servidor NTP, no debería haber un desfase de tiempo significativo entre la infraestructura y las unidades de proceso de Azure o las marcas de instancias grandes.

## <a name="operating-system"></a>Sistema operativos

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana) es la distribución de Linux instalada para SAP HANA en Azure (instancias grandes). Esta distribución particular proporciona funcionalidades específicas de SAP &quot;listas para usar&quot; (incluidos los parámetros predefinidos para ejecutar SAP en SLES de forma eficaz).

Vea [Biblioteca de recursos/Informes oficiales](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) en el sitio web de SUSE y [SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) (SAP en SUSE) en la red de la comunidad de SAP (SCN) para varios recursos útiles relacionados con la implementación de SAP HANA en SLES (incluida la configuración de alta disponibilidad, el endurecimiento de la seguridad específico de las operaciones de SAP etc.).

Vínculos adicionales y útiles relacionados con SLES:

- [SAP HANA on SUSE Linux Site](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) (Sitio de SAP HANA en SUSE Linux)
- [Best Practice for SAP: Enqueue Replication – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113) (Procedimiento recomendado para SAP: poner en cola la replicación - SAP NetWeaver en SUSE Linux Enterprise 12)
- [ClamSAP – SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (ClamSAP: protección contra virus de SLES para SAP), incluido SLES 12 para SAP Applications

Notas de compatibilidad con SAP aplicables a la implementación de SAP HANA en SLES 12 SP1:

- [SAP Support Note #1944799 – SAP HANA Guidelines for SLES Operating System Installation](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html) (Nota de compatibilidad con SAP nº 1944799: instrucciones de SAP HANA para la instalación de sistema operativo)
- [SAP Support Note #2205917 – SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications](https://launchpad.support.sap.com/#/notes/2205917/E) (Nota de compatibilidad con SAP nº 2205917: configuración de SO recomendada de BD de SAP HANA para SLES 12 para aplicaciones SAP)
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12:  Installation Notes](https://launchpad.support.sap.com/#/notes/1984787) (Nota de compatibilidad con SAP nº 1984787 - SUSE Linux Enterprise Server 12:  notas de instalación)
- [SAP Support Note #171356 – SAP Software on Linux:  General Information](https://launchpad.support.sap.com/#/notes/1984787) (Nota de compatibilidad con SAP nº 171356 - Software SAP en Linux: información general)
- [SAP Support Note #1391070 – Linux UUID Solutions](https://launchpad.support.sap.com/#/notes/1391070) (Nota de compatibilidad con SAP nº 1391070: soluciones UUID de Linux)

## <a name="time-synchronization"></a>Sincronización de la hora

SAP es sumamente sensible a las diferencias horarias para los distintos componentes que forman el sistema SAP. Los volcados cortos de ABAP de SAP con el título de error de ZDATE\_LARGE\_TIME\_DIFF probablemente le resulten familiares si ha estado trabajando con SAP (base) durante mucho tiempo, ya que estos volcados cortos aparecen cuando la hora del sistema de diferentes servidores o VM se distancia demasiado.

Para SAP HANA en Azure (instancias grandes), la sincronización de la hora que se realiza en Azure no se aplica a las unidades de proceso en las marcas de instancia grande. Esto no se aplica para ejecutar aplicaciones de SAP de forma nativa en Azure (en VM), ya que Azure garantiza que la hora del sistema está correctamente sincronizada. Como consecuencia de esto, se debe configurar un servidor horario independiente, que lo puedan usar servidores de aplicaciones SAP que se ejecutan en VM de Azure y las instancias de base de datos de SAP HANA que se ejecutan en instancias grandes de HANA. La infraestructura de almacenamiento en marcas de instancias grandes se sincronizado con los servidores NTP.



