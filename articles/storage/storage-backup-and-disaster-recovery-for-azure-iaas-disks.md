---
title: "Copia de seguridad y recuperación ante desastres para discos IaaS de Azure | Microsoft Docs"
description: "En este artículo se explica cómo planear la copia de seguridad y recuperación ante desastres (DR) de máquinas virtuales (VM) y discos IaaS de Azure. En este documento se tratan los discos administrados y no administrados."
services: storage
cloud: Azure
documentationcenter: na
author: luywang
manager: kavithag
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: luywang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 0de675e46dbd527148ac5fedcd35d81962adfb7e
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Copia de seguridad y recuperación ante desastres para discos IaaS de Azure

En este artículo se explica cómo planear la copia de seguridad y recuperación ante desastres (DR) de máquinas virtuales (VM) y discos IaaS de Azure. En este documento se tratan los discos administrados y los no administrados.

En primer lugar se aborda la funcionalidad de tolerancia a errores integrada en la plataforma Azure que ayudan a protegerse contra errores locales. Luego se describen los escenarios de desastre que la funcionalidad integrada no cubre del todo, que es el principal tema que se trata en este documento. También se muestran varios ejemplos de escenarios de carga de trabajo a los que se pueden aplicar distintas consideraciones sobre copia de seguridad y recuperación ante desastres. Luego se revisan las posibles soluciones para la recuperación ante desastres de discos IaaS. 

## <a name="introduction"></a>Introducción

La plataforma Azure utiliza diversos métodos de redundancia y tolerancia a errores que contribuyen a proteger a los clientes de errores de hardware localizados que se pueden producir. Puede que los errores locales incluyan problemas con una máquina del servidor de Azure Storage en la que se almacena parte de los datos de un disco virtual o errores de una SSD o HDD presente en el servidor. Estos errores de componentes de hardware aislados pueden producirse durante las operaciones normales y la plataforma está diseñada para ser resistente a estos errores. Los desastres de gran envergadura pueden producir errores o inaccesibilidad de un gran número de servidores de almacenamiento o de un centro de datos completo. Aunque las máquinas virtuales y los discos normalmente están protegidos de errores localizados, es preciso seguir otros pasos para proteger la carga de trabajo de errores catastróficos en toda la región (como un desastre de gran envergadura) que pueden afectar a la máquina virtual y los discos.

Además de la posibilidad de errores de la plataforma, pueden producirse problemas con los datos o la aplicación del cliente. Por ejemplo, puede que una nueva versión de su aplicación realice inadvertidamente cambios importantes en los datos. En ese caso, tal vez convenga revertir la aplicación y los datos a una versión anterior con el último buen estado conocido. Para ello, es necesario haber realizado copias de seguridad periódicas.

Para la recuperación ante desastres regionales, debe realizar una copia de los discos de máquina virtual IaaS en otra región. 

Antes de examinar las opciones de copia de seguridad y recuperación ante desastres, repasemos algunos métodos disponibles para controlar errores localizados.

### <a name="azure-iaas-resiliency"></a>Resistencia de IaaS de Azure

*Resistencia* se refiere a la tolerancia a errores normales que se producen en los componentes de hardware. Resistencia es la capacidad de recuperarse de los errores y seguir funcionando. No se trata de evitar los errores, sino de responder a ellos de manera que se evite el tiempo de inactividad o la pérdida de datos. El objetivo de la resistencia es devolver la aplicación a un estado plenamente operativo después de un error. Las máquinas virtuales y los discos de Azure están diseñados para ser resistentes a errores de hardware comunes. Echemos un vistazo a cómo la plataforma Azure IaaS proporciona esta resistencia.

Una máquina virtual se compone principalmente de dos partes: (1) un servidor de proceso y (2) los discos persistentes. Ambos componentes afectan a la tolerancia a errores de una máquina virtual.

Si el servidor host de proceso de Azure que hospeda la máquina virtual experimenta un error de hardware (lo que es poco frecuente), Azure está diseñado para restaurar automáticamente la máquina virtual en otro servidor. Si esto ocurre, se experimentará un reinicio y luego se realizará una copia de seguridad de la máquina virtual. Azure detecta automáticamente estos errores de hardware y ejecuta las recuperaciones para ayudar a garantizar que la máquina virtual del cliente estará disponible cuanto antes.

Con respecto a los discos IaaS, la durabilidad de los datos es fundamental en la plataforma de almacenamiento persistente. Los clientes de Azure tienen aplicaciones empresariales importantes que se ejecutan en IaaS y dependen de la persistencia de los datos. Azure diseña la protección de estos discos IaaS con tres copias redundantes de datos almacenados localmente, lo que ofrece gran durabilidad contra errores locales. Si se produce un error en uno de los componentes de hardware que contiene el disco, la máquina virtual no se ve afectada porque hay dos copias adicionales que admiten las solicitudes de disco. Funciona bien, aunque dos componentes de hardware diferente que admiten un disco tengan errores al mismo tiempo (lo que sería muy poco frecuente). Para asegurar que siempre se mantienen tres réplicas, el servicio Azure Storage genera automáticamente una nueva copia de datos en segundo plano si una de las tres copias deja de estar disponible. Por consiguiente, no debe ser necesario utilizar usar RAID con discos de Azure para tolerancia a errores. Una configuración simple de RAID 0 debe ser suficiente para seccionar los discos si es necesario para crear volúmenes más grandes.

Gracias a esta arquitectura, **Azure ha ofrecido de manera constante durabilidad de nivel empresarial para discos IaaS, con una [tasa de error anualizada del 0 %](https://en.wikipedia.org/wiki/Annualized_failure_rate)**.

Los errores de hardware localizados en el host de proceso o en la plataforma de almacenamiento pueden en ocasiones traducirse en falta de disponibilidad temporal de la máquina virtual que está cubierta por el [acuerdo de nivel de servicio de Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) para disponibilidad de la máquina virtual. Azure ofrece también un Acuerdo de Nivel de Servicio líder del sector para instancias únicas de máquina virtual que usan discos de Premium Storage.

Para proteger las cargas de trabajo de aplicación de tiempo de inactividad debido a la falta temporal de disponibilidad de un disco o una máquina virtual, los clientes pueden aprovechar los [conjuntos de disponibilidad](../virtual-machines/windows/manage-availability.md). Dos máquinas virtuales o más de un conjunto de disponibilidad proporcionan redundancia para la aplicación. Azure luego crea estas máquinas virtuales y discos en dominios de error independientes con distintos componentes de alimentación, red y servidor. Por consiguiente, los errores de hardware localizados normalmente no afectan a varias máquinas virtuales del conjunto al mismo tiempo, lo que proporciona alta disponibilidad de la aplicación. Se recomienda usar conjuntos de disponibilidad cuando se requiere alta disponibilidad. Para más información, vea los aspectos de recuperación ante desastres que se detallan a continuación.

### <a name="backup-and-disaster-recovery"></a>Copia de seguridad y recuperación ante desastres

La recuperación ante desastres (DR) es la capacidad de recuperación de incidentes importantes pero poco frecuentes: errores a gran escala no transitorios, tales como una interrupción del servicio que afecta a toda la región. La recuperación ante desastres incluye la copia de seguridad y el archivado de datos, y puede incluir la intervención manual, como la restauración manual de una base de datos a partir de la copia de seguridad.

Puede que la protección integrada frente a errores localizados de la plataforma de Azure no proteja completamente la máquina virtual y los discos en caso de desastres de gran envergadura que pueden provocar interrupciones a gran escala. Esto incluye eventos catastróficos tales como que un centro de datos se vea afectado por un huracán, terremoto, incendio o errores de unidades de hardware a gran escala. Además, se pueden producir errores debido a problemas de datos o aplicaciones.

Para contribuir a proteger de interrupciones las cargas de trabajo de IaaS, debe planear redundancia y copias de seguridad para habilitar la recuperación. Para la recuperación ante desastres, debe planear la redundancia y la copia de seguridad en una ubicación geográfica distinta fuera del sitio principal. Así se ayuda a garantizar que la copia de seguridad no se ve afectada por el mismo evento que originalmente incidió en la máquina virtual o los discos. Para más información, vea [Disaster Recovery for Applications built on Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) (Recuperación ante desastres para aplicaciones generadas en Azure).

Las consideraciones sobre recuperación ante desastres pueden incluir los siguientes aspectos:

1. Alta disponibilidad (HA) es la capacidad de la aplicación de seguir ejecutándose en un estado correcto, sin tiempo de inactividad relevante. Por "estado correcto," queremos decir que la aplicación responde y los usuarios pueden conectarse e interactuar con ella. Ciertas bases de datos y aplicaciones críticas pueden tener que estar disponibles siempre, aunque existan errores en la plataforma. Para estas cargas de trabajo, puede que necesite planear la redundancia para la aplicación, así como para los datos.

2. Durabilidad de los datos: en ciertos casos, la consideración más importante que hay que hacerse es garantizar que los datos se conserven en caso de desastre. Por consiguiente, puede que tenga que realizar una copia de seguridad de los datos en otro sitio. Para estas cargas de trabajo, no se requiere una redundancia completa para la aplicación, sino una copia de seguridad periódica de los discos.

## <a name="backup-and-dr-scenarios"></a>Escenarios de copia de seguridad y recuperación ante desastres

Veamos algunos ejemplos típicos de escenarios de carga de trabajo de aplicación y las consideraciones sobre la planeación de recuperación ante desastres.

### <a name="scenario-1-major-database-solutions"></a>Escenario 1: Soluciones de bases de datos principales

Considere la posibilidad de usar un servidor de base de datos de producción, como SQL Server u Oracle, que pueda admitir alta disponibilidad. Los usuarios y las aplicaciones de producción críticas dependen de esta base de datos. Es posible que el plan de recuperación ante desastres para este sistema tenga que admitir los requisitos siguientes:

1. Los datos deben estar protegidos y ser recuperables.
2.  El servidor debe estar disponible para su uso.

Esto puede requerir que se mantenga una réplica de la base de datos en una región distinta como copia de seguridad. Según los requisitos para la recuperación de datos y la disponibilidad del servidor, la solución puede variar de un sitio de réplica activa-pasiva o activa-activa para copias de seguridad periódicas sin conexión de los datos. Las bases de datos relacionales, como SQL Server y Oracle, ofrecen varias opciones de replicación. Con SQL Server, se pueden usar [grupos de disponibilidad Always On de SQL Server](https://msdn.microsoft.com/library/hh510230.aspx) para alta disponibilidad.

Las bases de datos NoSQL, como MongoDB, también admiten [réplicas](https://docs.mongodb.com/manual/replication/) para conseguir redundancia. Se pueden usar las réplicas de caché para conseguir alta disponibilidad.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Escenario 2: Un clúster de máquinas virtuales redundantes

Considere la posibilidad de una carga de trabajo controlada por un clúster de máquinas virtuales que proporcionan redundancia y equilibrio de carga. Un ejemplo sería un clúster Cassandra implementado en una región. Este tipo de arquitectura ya proporciona un alto nivel de redundancia en esa región. Pero, para proteger la carga de trabajo de un error de nivel regional, debe plantearse la posibilidad de propagar el clúster en dos regiones o realizar copias de seguridad periódicas en otra región.

### <a name="scenario-3-iaas-application-workload"></a>Escenario 3: Carga de trabajo de la aplicación IaaS

Podría tratarse de una carga de trabajo de producción típica que se ejecuta en una máquina virtual de Azure. Por ejemplo, podría ser un servidor web o servidor de archivos que contiene el contenido y otros recursos de un sitio. También podría ser una aplicación empresarial personalizada que se ejecuta en una máquina virtual que almacena los datos, los recursos y el estado de la aplicación en los discos de máquina virtual. En este caso, es importante asegurarse de realizar copias de seguridad periódicamente. La frecuencia de copia de seguridad debe basarse en la naturaleza de la carga de trabajo de máquina virtual. Por ejemplo, si la aplicación se ejecuta todos los días y modifica los datos, la copia de seguridad debe realizarse cada hora.

Otro ejemplo es un servidor de informes que extrae datos de otros orígenes y genera informes agregados. La pérdida de esta máquina virtual o estos discos se traducirá en la pérdida de los informes. Pero, tal vez sea posible volver a ejecutar el proceso de creación de informes y volver a generar la salida. En ese caso, no se tiene realmente una pérdida de datos aunque el servidor de informes se vea afectado por un desastre, por lo que podría tener un mayor nivel de tolerancia para la pérdida parcial de los datos en el servidor de informes. En ese caso, realizar copias de seguridad con menor frecuencia es una posibilidad para reducir el costo.

### <a name="scenario-4-iaas-application-data-issues"></a>Escenario 4: Problemas de datos de la aplicación IaaS

Tiene una aplicación que calcula, mantiene y sirve de datos comerciales críticos, como información sobre los precios. Una nueva versión de la aplicación tenía un error de software que calculó incorrectamente el precio y dañó los datos comerciales existentes servidos por la plataforma. En este caso, la mejor medida sería revertir a la versión anterior de la aplicación y los datos primero. Para habilitarla, realice copias de seguridad periódicas del sistema.

## <a name="disaster-recovery-solution-azure-backup-service"></a>Solución de recuperación ante desastres: servicio Azure Backup

El [servicio Azure Backup](https://azure.microsoft.com/services/backup/) puede usarse para copia de seguridad y recuperación ante desastres y funciona tanto con [Managed Disks](storage-managed-disks-overview.md) como con [discos no administrados](storage-about-disks-and-vhds-windows.md#unmanaged-disks). Puede crear un trabajo de copia de seguridad con copias de seguridad basadas en tiempo, fácil restauración de la máquina virtual y directivas de retención de copia de seguridad. 

Si usa [discos de Premium Storage](storage-premium-storage.md), [Managed Disks](storage-managed-disks-overview.md) o discos de otros tipos con la opción de [almacenamiento con redundancia local (LRS)](storage-redundancy.md#locally-redundant-storage), es especialmente importante aprovechar las copias de seguridad periódicas de recuperación ante desastres. Azure Backup almacena los datos en el almacén de Recovery Services para su retención a largo plazo. Elija la opción de [Almacenamiento con redundancia geográfica (GRS)](storage-redundancy.md#geo-redundant-storage) opción para el almacén de Backup Recovery Services. Así se garantizará que las copias de seguridad se replican en una región distinta de Azure para su protección contra desastres regionales.

Con [discos no administrados](storage-about-disks-and-vhds-windows.md#unmanaged-disks), puede usar el almacenamiento de tipo LRS para discos IaaS, pero asegúrese de que Azure Backup está habilitado con la opción de GRS en el almacén de Recovery Services.

**Si usa la opción [GRS](storage-redundancy.md#geo-redundant-storage)/[RA-GRS](storage-redundancy.md#read-access-geo-redundant-storage) con los discos no administrados, todavía tiene que realizar instantáneas coherentes de copia de seguridad y recuperación ante desastres.** Se debe usar el [servicio Azure Backup ](https://azure.microsoft.com/services/backup/) o [instantáneas coherentes](#alternative-solution-consistent-snapshots).

Seguidamente se ofrece un resumen de los pasos necesarios.

| Escenario | Replicación automática | Solución de recuperación ante desastres |
| --- | --- | --- |
| *Discos de Premium Storage* | Local ([LRS](storage-redundancy.md#locally-redundant-storage)) | [Copia de seguridad de Azure](https://azure.microsoft.com/services/backup/) |
| *Managed Disks* | Local ([LRS](storage-redundancy.md#locally-redundant-storage)) | [Copia de seguridad de Azure](https://azure.microsoft.com/services/backup/) |
| *Discos LRS no administrados* | Local ([LRS](storage-redundancy.md#locally-redundant-storage)) | [Copia de seguridad de Azure](https://azure.microsoft.com/services/backup/) |
| *Discos GRS no administrados* | Entre regiones ([GRS](storage-redundancy.md#geo-redundant-storage)) | [Copia de seguridad de Azure](https://azure.microsoft.com/services/backup/)<br/>[Instantáneas coherentes](#alternative-solution-consistent-snapshots) |
| *Discos RA-GRS no administrados* | Entre regiones ([RA-GRS](storage-redundancy.md#read-access-geo-redundant-storage)) | [Copia de seguridad de Azure](https://azure.microsoft.com/services/backup/)<br/>[Instantáneas coherentes](#alternative-solution-consistent-snapshots) |

La alta disponibilidad puede conseguirse mejor mediante el uso de Managed Disks en un conjunto de disponibilidad con Azure Backup. Si usa discos no administrados, todavía puede usar Azure Backup con recuperación ante desastres. Si no puede usar Azure Backup, tome [instantáneas coherentes](#alternative-solution-consistent-snapshots) tal y como se describe en una sección posterior es una solución alternativa de copia de seguridad y recuperación ante desastres.

Las opciones de alta disponibilidad, copia de seguridad y recuperación ante desastres en los niveles de aplicación o de infraestructura se pueden representar como sigue:

| *Level* | Alta disponibilidad   | Copia de seguridad y recuperación ante desastres |
| --- | --- | --- |
| *Aplicación* | Always On de SQL | Copia de seguridad de Azure |
| *Infraestructura*  | Conjunto de disponibilidad  | GRS con instantáneas coherentes |

### <a name="using-the-azure-backup-service"></a>Uso del servicio Azure Backup

[Azure Backup](../backup/backup-azure-vms-introduction.md) puede realizar copias de seguridad de las máquinas virtuales que ejecutan Windows o Linux en el almacén de Azure Recovery Services. La copia de seguridad y restauración de datos críticos para el negocio resulta complicada por el hecho de que hay que realizar la copia de seguridad mientras se ejecutan las aplicaciones que producen los datos. Para solucionar este problema, el servicio Azure Backup ofrece copias de seguridad coherentes con la aplicación para las cargas de trabajo de Microsoft mediante el servicio de instantáneas de volumen (VSS) para garantizar que los datos se escriben correctamente en el almacenamiento. En las máquinas virtuales Linux, solo se pueden realizar copias de seguridad coherentes con el archivo, dado que Linux no dispone de una plataforma equivalente a VSS.

![][1]

El servicio Azure Backup inicia el trabajo de copia de seguridad a la hora programada y desencadena la extensión de reserva instalada en la máquina virtual para tomar una instantánea de un momento dado. Esta instantánea se toma en coordinación con VSS para obtener una instantánea coherente de los discos de la máquina virtual sin tener que apagarla. La extensión de reserva de la máquina virtual realiza un vaciado de todas las escrituras antes de tomar la instantánea coherente de todos los discos. Después de tomar la instantánea, el servicio Azure Backup transfiere los datos al almacén de Backup. Para que el proceso de copia de seguridad resulte más eficaz, el servicio identifica y transfiere únicamente los bloques de datos que han cambiado desde la última copia de seguridad.


Para restaurarlos, puede ver las copias de seguridad disponibles a través de Azure Backup y luego iniciar una restauración. Puede crear y restaurar copias de seguridad de Azure a través del[Azure Portal](https://portal.azure.com/), [mediante PowerShell](../backup/backup-azure-vms-automation.md ) o mediante la [CLI de Azure](https://docs.microsoft.com/azure/xplat-cli-install). Para más información, vea Azure Backup.

### <a name="steps-to-enable-backup"></a>Pasos para habilitar copia de seguridad

Los pasos siguientes suelen seguirse para habilitar las copias de seguridad de las máquinas virtuales mediante [Azure Portal](https://portal.azure.com/). Habrá algunas variaciones según el escenario exacto. Consulte la documentación de [Azure Backup](../backup/backup-azure-vms-introduction.md) para obtener todos los detalles. Azure Backup también [admite máquinas virtuales con Managed Disks](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Siga estos pasos para crear un almacén de Recovery Services para una máquina virtual:

    a.  Mediante [Azure Portal](https://portal.azure.com/), examine todos los recursos y busque "almacenes de Recovery Services".

    b.  En el menú Almacenes de Recovery Services, haga clic en Agregar y siga estos pasos para crear otro almacén en la misma región que la máquina virtual. Por ejemplo, si la máquina virtual se encuentra en la región del oeste de EE. UU, elija Oeste de EE. UU. para el almacén.

2.  Compruebe la replicación de almacenamiento para el almacén recién creado. Para ello, acceda al almacén de Recovery Services y vaya a la configuración/Configuración de copia de seguridad. Asegúrese de que la opción GRS está seleccionada de manera predeterminada. Así se asegurará de que el almacén se replica automáticamente en un centro de datos secundario. Por ejemplo, el almacén de la región oeste de EE. UU. se replicará automáticamente en la región este de EE. UU.

3.  Configure la directiva de copia de seguridad y seleccione la máquina virtual en la misma interfaz de usuario.

4.  Asegúrese de que Backup Agent está instalado en la máquina virtual. Si la máquina virtual se crea con una imagen de la galería de Azure, ya tiene instalado Backup Agent. En caso contrario (es decir, si utiliza una imagen personalizada), siga las instrucciones para [instalar el agente de VM en la máquina virtual](../backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent-on-the-virtual-machine).

5.  Asegúrese de que la máquina virtual permite la conectividad de red para que funcione el servicio Backup. Siga estas instrucciones para la [conectividad de red](../backup/backup-azure-arm-vms-prepare.md#network-connectivity).

6.  Una vez completados los pasos anteriores, la copia de seguridad se ejecutará a intervalos regulares, tal y como se especifica en la directiva de copia de seguridad. Si es necesario, puede desencadenar la primera copia de seguridad manualmente desde el panel de almacén en Azure Portal.

Para la automatización de Azure Backup mediante scripts, consulte [Cmdlets de PowerShell para copia de seguridad de máquina virtual](../backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Pasos para la recuperación

Si tiene que reparar o volver a generar una máquina virtual, puede restaurarla desde cualquiera de los puntos de recuperación de copia de seguridad del almacén. Hay un par de opciones diferentes para realizar la recuperación:

1.  Puede crear otra máquina virtual como una representación de un momento dado de la máquina virtual de copia de seguridad.

2.  Puede restaurar los discos y luego usar la plantilla de la máquina virtual para personalizar y volver a generar la máquina virtual restaurada. 

Vea las instrucciones de [Uso de Azure Portal para restaurar máquinas virtuales](../backup/backup-azure-arm-restore-vms.md#restoring-a-vm-during-azure-datacenter-disaster). En el documento se explican también los pasos concretos para restaurar máquinas virtuales con copia de seguridad en el centro de datos emparejado mediante el almacén de Backup con redundancia geográfica en caso de desastre en el centro de datos principal. En ese caso, Azure Backup usa el servicio de proceso de la segunda región para crear la máquina virtual restaurada.

También puede usar PowerShell para [restaurar una máquina virtual](../backup/backup-azure-vms-automation.md#restore-an-azure-vm) o para [crear una máquina virtual a partir de discos restaurados](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Solución alternativa: Instantáneas coherentes con la

Si no puede usar el servicio Azure Backup, puede implementar su propio mecanismo de copia de seguridad con instantáneas. Resulta complicado crear las instantáneas coherentes para todos los discos usados por una máquina virtual y luego replicar esas instantáneas en otra región. Por esta razón, Azure considera mejor opción aprovechar el servicio Backup que compilar una solución personalizada. Si utiliza almacenamiento RA-GRS/GRS para los discos, las instantáneas se replican automáticamente en un centro de datos secundaria. Si usa almacenamiento LRS para los discos, debe replicar los datos usted mismo. Para más información, vea [Copias de seguridad de discos de máquinas virtuales de Azure no administrados con instantáneas incrementales](storage-incremental-snapshots.md).

Una instantánea es una representación de un objeto en un momento dado. Una instantánea se facturará según el tamaño incremental de los datos contiene. Para más información, vea [Creación de una instantánea de un blob](storage-blob-snapshots.md).

### <a name="creating-snapshots-while-the-vm-is-running"></a>Creación de instantáneas mientras la máquina virtual está en ejecución

Aunque puede tomar una instantánea en cualquier momento, si la máquina virtual se está ejecutando, todavía hay datos que se transmiten a los discos y las instantáneas pueden contener operaciones parciales que estaban en marcha. Además, si intervienen varios discos, es posible que se hayan producido instantáneas de distintos discos en distintos momentos, lo que significa que puede que estas instantáneas no estén coordinadas. Esto es especialmente problemático para los volúmenes seccionados cuyos archivos pueden resultar dañados si se realizan cambios durante la copia de seguridad.

Para evitar esta situación, el proceso de copia de seguridad debe implementar los siguientes pasos:

1.  Inmovilización de todos los discos

2.  Vaciado de todas las escrituras pendientes

3.  Luego, [creación de una instantánea de un blob](storage-blob-snapshots.md) para todos los discos

Algunas aplicaciones de Windows como SQL Server ofrecen un mecanismo de copia de seguridad coordinado a través de VSS para crear copias de seguridad coherentes con la aplicación. En Linux, puede usar una herramienta como fsfreeze para coordinar los discos, que proporciona copias de seguridad coherentes con el archivo, pero no instantáneas coherentes con la aplicación. Este proceso es complicado, por lo que debe plantarse el uso de [Azure Backup](../backup/backup-azure-vms-introduction.md) o una solución de copia de seguridad de terceros que ya lo implementan.

El proceso anterior dará como resultado una colección de instantáneas coordinadas de todos los discos de máquina virtual, que representa una vista de un momento dado de la máquina virtual. Se trata de un punto de restauración de copia de seguridad para la máquina virtual. Puede repetir el proceso a intervalos programados para crear copias de seguridad periódicas. Vea a continuación los pasos para [copiar las copias de seguridad en otra región](#copy-the-snapshots-to-another-region) de recuperación ante desastres.

### <a name="creating-snapshots-while-the-vm-is-offline"></a>Creación de instantáneas mientras la máquina virtual está sin conexión

Otra posibilidad para crear copias de seguridad coherentes es apagar la máquina virtual y tomar instantáneas de blob de cada disco. Esto resulta más sencillo que coordinar instantáneas de una máquina virtual en ejecución, pero requiere algunos minutos de inactividad. Para este proceso, siga estos pasos:

1. Apague la máquina virtual.

2. Cree una instantánea de cada blob de VHD, que solo tarda unos segundos.

    Para crear una instantánea, puede usar [PowerShell](storage-powershell-guide-full.md#how-to-manage-azure-blob-snapshots), la [API de Rest de Azure Storage](https://msdn.microsoft.com/library/azure/ee691971.aspx), la [CLI de Azure](https://docs.microsoft.com/azure/xplat-cli-install) o cualquiera de las bibliotecas de cliente de Azure Storage, como [la biblioteca del cliente de Storage para .NET](https://msdn.microsoft.com/library/azure/hh488361.aspx).

3. Inicie la máquina virtual, con lo que finaliza el tiempo de inactividad. Todo el proceso se completa habitualmente en unos minutos.

Este proceso produce una colección de instantáneas coherentes de todos los discos, lo que proporciona un punto de restauración de copia de seguridad para la máquina virtual. Vea a continuación los pasos para copiar las copias de seguridad en otra región de recuperación ante desastres.

### <a name="copy-the-snapshots-to-another-region"></a>Copia de las instantáneas en otra región

La creación de las instantáneas por sí sola puede no ser suficiente para la recuperación ante desastres. También debe replicar las copias de seguridad de instantánea en otra región.

Si usa GRS o RA-GRS para los discos, las instantáneas se replican automáticamente en la región secundaria. Puede haber unos minutos de intervalo antes de la replicación y, si el centro de datos principal deja de funcionar antes de que las instantáneas terminen de replicarse, no podrá acceder a las instantáneas desde el centro de datos secundario. La probabilidad de que esto ocurra es pequeña.

> [!Note] 
> No basta con tener los discos en una cuenta GRS o RA-GRS para proteger la máquina virtual ante desastres. También debe crear instantáneas coordinadas o usar Azure Backup. Esto es necesario para recuperar una máquina virtual a un estado coherente.

Si usa LRS, debe copiar las instantáneas en otra cuenta de almacenamiento inmediatamente después de crear la instantánea. El destino de copia puede ser una cuenta de almacenamiento LRS en una región distinta, con lo que la copia se encuentra en una región remota. También puede copiar la instantánea en una cuenta de almacenamiento RA-GRS en la misma región. En este caso, la instantánea se replicará forma diferida en la región secundaria remota. La copia de seguridad se protege ante desastres en el sitio principal una vez finalizadas la copia de seguridad y la replicación.

Para copiar las instantáneas incrementales para recuperación ante desastres con eficacia, consulte las instrucciones dadas en [Copias de seguridad de discos de máquinas virtuales de Azure no administrados con instantáneas incrementales](storage-incremental-snapshots.md).

![][2]

### <a name="recovery-from-snapshots"></a>Recuperación de instantáneas

Para recuperar una instantánea, cópiela para crear un blob. Si va a copiar la instantánea de la cuenta principal, puede copiarla en el blob base de la instantánea, con lo que se revierte el disco a la instantánea; esto se conoce como promocionar la instantánea. Si va a copiar la copia de seguridad de instantánea de una cuenta secundaria (en el caso de RA-GRS), debe copiarse en una cuenta principal. Puede copiar una instantánea [mediante PowerShell](storage-powershell-guide-full.md#how-to-copy-a-snapshot-of-a-blob) o con la utilidad AzCopy. Para más información, vea [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md).

En el caso de las máquinas virtuales con varios discos, debe copiar todas las instantáneas que forman parte del mismo punto de restauración coordinada. Después de copiar las instantáneas en blobs de VHD grabable, puede usarlos para volver a crear la máquina virtual con la plantilla de la máquina virtual.

## <a name="other-options"></a>Otras opciones

### <a name="sql-server"></a>SQL Server

SQL Server en una máquina virtual tiene su propia funcionalidad integrada para realizar copias de seguridad de la base de datos de SQL Server en Azure Blob Storage o en un recurso compartido de archivos. Si la cuenta de almacenamiento es GRS o RA-GRS, puede acceder a las copias de seguridad en el centro de datos secundario de la cuenta de almacenamiento en caso de desastre, con las mismas restricciones descritas anteriormente. Para más información, consulte [Copias de seguridad y restauración para SQL Server en máquinas virtuales de Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). Además de la copia de seguridad y restauración, los [grupos de disponibilidad AlwaysOn de SQL Server](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) pueden conservar réplicas secundarias de las bases de datos para reducir considerablemente el tiempo de recuperación ante desastres.

## <a name="other-considerations"></a>Otras consideraciones

En este artículo se describe cómo realizar una copia de seguridad o tomar instantáneas de las máquinas virtuales y sus discos para admitir la recuperación ante desastres y cómo utilizarlas para recuperar. Con el modelo de Azure Resource Manager, muchas personas usan plantillas para crear sus máquinas virtuales y otras infraestructuras en Azure. Puede usar una plantilla para crear una máquina virtual que tenga siempre la misma configuración. Si usa imágenes personalizadas para crear las máquinas virtuales, debe asegurarse también de que las imágenes están protegidas con una cuenta RA-GRS para almacenarlas.

Por lo tanto, el proceso de copia de seguridad puede ser una combinación de estas dos acciones:

1. Copia de seguridad de los datos (discos)
2. Copia de seguridad de la configuración (plantillas, imágenes personalizadas)

Según la opción de copia de seguridad que elija, puede que tenga que controlar la copia de seguridad de los datos y la configuración o hacer que servicio Backup controle todo esto automáticamente.

## <a name="appendix---understanding-the-impact-of-lrs-grs-and-ra-grs"></a>Apéndice: comprensión del impacto de LRS, GRS y RA-GRS

En el caso de las cuentas de almacenamiento de Azure, hay tres tipos de redundancia de datos que se debe considerar con respecto a la recuperación ante desastres: localmente redundante (LRS), con redundancia geográfica (GRS) o con redundancia geográfica y acceso de lectura (RA-GRS). 

El almacenamiento con redundancia local (LRS) mantiene tres copias de los datos en el mismo centro de datos. Al escribir los datos, las tres copias se actualizan antes devolver al autor de la llamada el mensaje de operación correcta, para que se sepa que son idénticas. El disco está protegido contra errores locales, ya que es muy poco probable que las tres copias se vean afectadas al mismo tiempo. En el caso de LRS, no hay ninguna redundancia geográfica, por lo que el disco no está protegido contra errores catastróficos que pueden afectar a una unidad de almacenamiento o centro de datos completo.

Con GRS y RA-GRS, se conservan tres copias de los datos en la región principal (seleccionada por el usuario) y otras tres copias de los datos se conservan en la región secundaria correspondiente (establecida por Azure). Por ejemplo, si almacena datos en la región oeste de EE. UU., los datos se replicarán en la región este de EE. UU. Esto se hace de forma asincrónica y hay un pequeño retraso entre las actualizaciones en la réplica principal y la secundaria. Las réplicas de los discos en el sitio secundario son coherentes por disco (con el retraso), pero puede que las réplicas de varios discos activos no estén sincronizadas **entre sí**. Para tener réplicas coherentes en varios discos, se requieren instantáneas coherentes.

La diferencia principal entre GRS y RA-GRS es que con RA-GRS se puede leer la copia secundaria en cualquier momento. Si hay un problema que presenta los datos en la región principal inaccesible, el equipo de Azure hará que todo lo posible para restaurar el acceso. Aunque el centro de datos principal esté inactivo, si tiene RA-GRS habilitada, puede acceder a los datos del centro de datos secundario. Por lo tanto, si tiene previsto leer desde la réplica cuando el centro de datos principal esté inaccesible, debe plantearse el uso de RA-GRS.

Si resulta ser una interrupción importante, el equipo de Azure puede desencadenar una conmutación por error geográfica y cambiar las entradas DNS principales para que señalen a un almacenamiento secundario. En este momento, si tiene GRS o RA-GRS habilitadas, puede acceder a los datos de la región que se utiliza para sea la secundaria. En otras palabras, si la cuenta de almacenamiento es GRS y hay un problema, solo puede acceder el almacenamiento secundario si existe una conmutación por error geográfica.

Para más información, vea [Qué hacer si se produce una interrupción del servicio Azure Storage](storage-disaster-recovery-guidance.md). 

Tenga en cuenta que Microsoft controla si se produce una conmutación por error. La conmutación por error no se controla por cuenta de almacenamiento, por lo tanto no son los clientes individuales quienes deciden su uso. Para implementar la recuperación ante desastres con cuentas de almacenamiento o discos de máquina virtual en concreto, debe usar las técnicas descritas anteriormente en este artículo.



[1]: ./media/storage-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/storage-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
