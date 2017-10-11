---
title: "Guía de copia de seguridad de SAP HANA en Azure Virtual Machines | Microsoft Azure"
description: "Guía de copia de seguridad para SAP HANA proporciona dos posibilidades de copia de seguridad principales para SAP HANA en Azure Virtual Machines"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: 9e5b124643b753f404ba6012d3df998f567be59a
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Guía de copia de seguridad de SAP HANA en Azure Virtual Machines

## <a name="getting-started"></a>Introducción

La guía de copia de seguridad de SAP HANA en Azure Virtual Machines solo describirá temas específicos de Azure. Para elementos relacionados con la copia de seguridad de SAP HANA, consulte la documentación de SAP HANA (vea _Documentación de copia de seguridad de SAP HANA_ más adelante en este artículo).

Este artículo se centra en las dos principales posibilidades de copia de seguridad para SAP HANA en Azure Virtual Machines:

- Copia de seguridad de HANA en el sistema de archivos en Azure Linux Virtual Machine (vea [Azure Backup de SAP HANA en el nivel de archivo](sap-hana-backup-file-level.md))
- Copia de seguridad de HANA basada en instantáneas de almacenamiento con la característica de instantáneas de Azure Storage Blob manualmente o el servicio Azure Backup (vea [Copia de seguridad de SAP HANA basada en instantáneas de almacenamiento](sap-hana-backup-storage-snapshots.md))

SAP HANA ofrece una API de copia de seguridad, que permite que las herramientas de copia de seguridad de terceros se integren directamente con SAP HANA. (Esto no está dentro del ámbito de esta guía). No hay ninguna integración directa de SAP HANA con el servicio Azure Backup disponible actualmente basada en esta API.

SAP HANA es compatible oficialmente con máquinas virtuales de Azure tipo GS5 como instancia única con una restricción adicional para las cargas de trabajo OLAP (vea [Buscar plataformas de IaaS certificadas](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) en el sitio web de SAP). En este artículo se actualizará a medida que estén disponibles nuevas ofertas para SAP HANA en Azure.

También hay una solución híbrida de SAP HANA en Azure, donde SAP HANA ejecuta elementos no virtualizados en servidores físicos. Sin embargo, esta guía de Azure Backup para SAP HANA cubre un entorno puro de Azure donde se ejecuta SAP HANA en una máquina virtual de Azure, no SAP HANA en &quot;instancias de gran tamaño.&quot; Vea [Introducción y arquitectura de SAP HANA en Azure (instancias grandes)](hana-overview-architecture.md) para obtener más información sobre esta solución de copia de seguridad en &quot;instancias grandes&quot; basada en instantáneas de almacenamiento.

Puede obtener información general sobre productos SAP admitidos en Azure en la [nota 1928533 de SAP](https://launchpad.support.sap.com/#/notes/1928533).

Las siguientes tres cifras ofrecen una visión general de las opciones de copia de seguridad de SAP HANA mediante las funcionalidades nativas de Azure actualmente, y también muestran tres escenarios posibles de copia de seguridad futuras. Los artículos relacionados [Copia de seguridad de SAP HANA basada en instantáneas de almacenamiento](sap-hana-backup-file-level.md) y [Azure Backup de SAP HANA en el nivel de archivo](sap-hana-backup-storage-snapshots.md) describen estas opciones con más detalle, incluidas las consideraciones de tamaño y rendimiento para copias de seguridad de SAP HANA con un tamaño de varios terabytes.

![Esta ilustración muestra dos posibilidades para guardar el estado actual de la VM](media/sap-hana-backup-guide/image001.png)

Esta ilustración muestra la posibilidad de guardar el estado actual de la VM, ya sea a través del servicio Azure Backup o la instantánea manual de los discos de VM. Con este enfoque, no tiene que administrar las copias de seguridad de SAP HANA. El desafío del escenario de instantáneas de disco es la coherencia del sistema de archivos y un estado del disco coherente con la aplicación. El tema de la coherencia se describe en la sección _Coherencia de datos de SAP HANA al tomar instantáneas de almacenamiento_ más adelante en este artículo. Las funcionalidades y restricciones del servicio Azure Backup relacionadas con las copias de seguridad de SAP HANA también se tratarán más tarde en este artículo.

![Esta ilustración muestra opciones para realizar una copia de seguridad de archivos de SAP HANA dentro de la VM](media/sap-hana-backup-guide/image002.png)

Esta ilustración muestra opciones para realizar una copia de seguridad de archivos de SAP HANA dentro de la VM y luego almacenar los archivos de copia de seguridad de HANA en otro lugar mediante herramientas distintas. La realización de una copia de seguridad de HANA requiere más tiempo que una solución de copia de seguridad basada en instantáneas, pero tiene ventajas con respecto a la integridad y la coherencia. Se proporcionarán más detalles más tarde en este artículo.

![Esta ilustración muestra un posible escenario futuro de copia de seguridad de SAP HANA](media/sap-hana-backup-guide/image003.png)

Esta ilustración muestra un posible escenario futuro de copia de seguridad de SAP HANA. Si SAP HANA permitió realizar copias de seguridad desde una replicación secundaria, agregaría opciones adicionales para las estrategias de copia de seguridad. Actualmente no es posible según una entrada en el sitio wiki de SAP HANA:

_&quot;¿Es posible realizar copias de seguridad en el lado secundario?_

_No, actualmente solo puede tomar copias de seguridad de registros y datos en el lado principal. Si está habilitada la copia de seguridad de registro automática, después de la adquisición en el lado secundario, las copias de seguridad del registro se escribirán automáticamente ahí.&quot;_

## <a name="sap-resources-for-hana-backup"></a>Recursos SAP para copia de seguridad de HANA

### <a name="sap-hana-backup-documentation"></a>Documentación de copia de seguridad de SAP HANA

- [Introducción a la administración de SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planeación de su estrategia de copia de seguridad y recuperación](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Programación de la copia de seguridad de HANA mediante ABAP DBACOCKPIT](http://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Programación de copias de seguridad de datos (SAP HANA Cockpit)](http://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Preguntas más frecuentes sobre la copia de seguridad de SAP HANA en la [nota 1642148 de SAP](https://launchpad.support.sap.com/#/notes/1642148)
- Preguntas más frecuentes sobre las instantáneas de base de datos y almacenamiento en la [nota 2039883 de SAP](https://launchpad.support.sap.com/#/notes/2039883)
- Sistemas de archivos de red no apropiados para la copia de seguridad y la recuperación en la [nota 1820529 de SAP](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>¿Por qué la copia de seguridad de SAP HANA?

Azure Storage ofrece disponibilidad y fiabilidad desde el principio (vea [Introducción a Microsoft Azure Storage](../../../storage/common/storage-introduction.md) para obtener más información sobre Azure Storage).

Lo mínimo que puede hacer una &quot;copia de seguridad&quot; es confiar en los SLA de Azure y conservar los datos de SAP HANA y los archivos de registro en VHD de Azure asociados a la VM de servidor de SAP HANA. Este enfoque trata los errores de VM, pero no los posibles daños en los archivos de registro y datos de SAP HANA, o errores lógicos como la eliminación de datos o archivos por accidente. Las copias de seguridad también son necesarias para el cumplimiento o por motivos legales. En resumen, las copias de seguridad de SAP HANA son siempre necesarias.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Cómo comprobar la precisión de una copia de SAP HANA
Cuando se usan instantáneas de almacenamiento, se recomienda ejecutar una restauración de prueba en un sistema diferente. Este enfoque proporciona una manera de asegurarse de que una copia de seguridad es correcta y de que los procesos internos de copia de seguridad y restauración funcionan según lo esperado. Aunque esto es un importante obstáculo a nivel local, es mucho más fácil cumplirlo en la nube proporcionando recursos necesarios temporalmente para este fin.

Tenga en cuenta que realizar una restauración simple y comprobar si HANA está listo para empezar no es suficiente. Idealmente, debe ejecutar una comprobación de la coherencia de tabla para asegurarse de que la base de datos restaurada está bien. SAP HANA ofrece varios tipos de comprobaciones de coherencia que se describen en la [nota 1977584 de SAP](https://launchpad.support.sap.com/#/notes/1977584).

También podrá encontrar información sobre la comprobación de coherencia de la tabla en el sitio web de SAP en [Comprobaciones de coherencia de tabla y catálogo](http://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

Para las copias de seguridad de archivos estándar no es necesaria una restauración de prueba. Existen dos herramientas de SAP HANA que ayudan a comprobar qué copia de seguridad se puede utilizar para la restauración: hdbbackupdiag y hdbbackupcheck. Vea [Comprobación manual si una recuperación es posible](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) para obtener más información sobre estas herramientas.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Ventajas y desventajas de la copia de seguridad de HANA frente a instantáneas de almacenamiento

SAP no da preferencia a las copias de seguridad de HANA frente a instantáneas de almacenamiento. Enumera sus ventajas y desventajas, por lo que puede determinar cuál usar según la situación y la tecnología de almacenamiento disponible (vea [Planeación de su estrategia de copia de seguridad y recuperación](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

En Azure, tenga en cuenta el hecho de que la característica de instantáneas de blobs de Azure no garantiza la coherencia del sistema de archivos (vea [Uso de instantáneas con PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). La siguiente sección _Coherencia de datos de SAP HANA al tomar instantáneas de almacenamiento_ describe algunas consideraciones relacionadas con esta característica.

Además, tiene que entender las implicaciones de facturación cuando se trabaja con frecuencia con instantáneas de blob, como se describe en este artículo: [Comprender cómo las instantáneas pueden incrementar los gastos](/rest/api/storageservices/understanding-how-snapshots-accrue-charges). No son tan obvias como cuando se usan discos virtuales de Azure.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Coherencia de datos de SAP HANA al tomar instantáneas de almacenamiento

La coherencia del sistema de archivos y la aplicación es un problema complejo cuando se toman instantáneas de almacenamiento. La manera más fácil de evitar problemas sería apagar SAP HANA, o quizás incluso la máquina virtual completa. El cierre puede ser factible con una demostración o prototipo, o incluso en un sistema de desarrollo, pero no es una opción para un sistema de producción.

En Azure, debe tener en cuenta que la característica de instantáneas de blobs de Azure no garantiza la coherencia del sistema de archivos. Sin embargo, funciona correctamente mediante la característica de instantáneas de SAP HANA, siempre que haya solo un único disco virtual implicado. Sin embargo, incluso con un solo disco, deben comprobarse los elementos adicionales. La [nota 2039883 de SAP](https://launchpad.support.sap.com/#/notes/2039883) dispone de información importante sobre las copias de seguridad de SAP HANA a través de instantáneas de almacenamiento. Por ejemplo, menciona que, con el sistema de archivos XFS, es necesario ejecutar **xfs\_freeze** antes de iniciar una instantánea de almacenamiento para garantizar la coherencia (vea [xfs\_freeze(8) - Linux man page](https://linux.die.net/man/8/xfs_freeze) para obtener más información sobre **xfs\_freeze**).

El tema de coherencia pasa a ser incluso más complejo en un escenario donde un sistema de archivos único abarca varios discos o volúmenes. Por ejemplo, si utiliza mdadm o LVM, y fragmentación. La nota de SAP mencionada anteriormente indica:

_&quot;Pero tenga en cuenta que el sistema de almacenamiento debe garantizar la coherencia de E/S al crear una instantánea de almacenamiento por volumen de datos de SAP HANA, es decir, la toma de instantáneas de un volumen de datos específicos del servicio de SAP HANA debe ser una operación atómica.&quot;_

Suponiendo que exista un sistema de archivos XFS que abarque cuatro discos virtuales de Azure, los pasos siguientes proporcionan una instantánea coherente que representa el área de datos de HANA:

- Preparación de la instantánea de HANA
- Inmovilización del sistema de archivos (por ejemplo, utilice **xfs\_freeze**)
- Creación de todas las instantáneas de blob necesarias en Azure
- Cancelación de la inmovilización del sistema de archivos
- Confirmación de la instantánea de HANA

La recomendación es utilizar el procedimiento anterior en todos los casos para estar seguro, con independencia del sistema de archivos. O bien, si es un solo disco o una fragmentación, a través de mdadm o LVM en varios discos.

Es importante confirmar la instantánea de HANA. Debido a la &quot;copia en escritura&quot;, SAP HANA no debe requerir espacio en disco adicional mientras esté en este modo de preparación de la instantánea. Además, no es posible iniciar nuevas copias de seguridad hasta que se confirme la instantánea de SAP HANA.

El servicio Azure Backup usa las extensiones de máquina virtual de Azure para conservar la coherencia del sistema de archivos. Estas extensiones de VM no están disponibles para el uso independiente. Tiene aún que administrar la coherencia de SAP HANA. Vea el artículo relacionado [Azure Backup de SAP HANA en el nivel de archivo](sap-hana-backup-file-level.md) para obtener más información.

### <a name="sap-hana-backup-scheduling-strategy"></a>Estrategia de programación de copia de seguridad de SAP HANA

El artículo de SAP HANA [Planeación de su estrategia de copia de seguridad y recuperación](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) indica un plan básico para realizar copias de seguridad:

- Instantánea del almacenamiento (diaria)
- Copia de seguridad de datos completa con formato de archivo o backint (una vez a la semana)
- Copias de seguridad de registro automáticas

Opcionalmente, puede realizar el proceso completo sin instantáneas de almacenamiento; se reemplazarán por copias de seguridad diferenciales de HANA, como copias de seguridad incrementales o diferenciales (vea [Copias de seguridad diferenciales](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)).

La guía de administración de HANA proporciona una lista de ejemplo. Sugiere que recupere SAP HANA en un momento específico en el tiempo mediante la siguiente secuencia de copias de seguridad:

1. Copia de seguridad de datos completa
2. Copia de seguridad diferencial
3. Copia de seguridad incremental 1
4. Copia de seguridad incremental 2
5. Copias de seguridad de registro

No es posible proporcionar una directriz general respecto a una programación exacta de como cuándo y con qué frecuencia debe realizarse un tipo de copia de seguridad específica. Depende mucho del cliente y de la cantidad de cambios de datos que se realicen en el sistema. Una recomendación básica del lado de SAP, que se puede ver como guía general, es realizar una copia de seguridad de HANA completa una vez a la semana.
Con respecto a las copias de seguridad de registro, vea la documentación de SAP HANA [Copias de seguridad de registro](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm).

SAP también recomienda realizar algunas tareas de mantenimiento del catálogo de copia de seguridad para evitar que crezca de forma desmesurada (vea [Tareas de mantenimiento para el catálogo de copia de seguridad y el almacenamiento de copia de seguridad](http://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>Archivos de configuración de SAP HANA

Como se indica en las preguntas más frecuentes en la [nota 1642148 de SAP](https://launchpad.support.sap.com/#/notes/1642148), los archivos de configuración de SAP HANA no forman parte de una copia de seguridad de HANA estándar. No son fundamentales para restaurar un sistema. La configuración de HANA podría cambiarse manualmente después de la restauración. En caso de que desee obtener la misma configuración personalizada durante el proceso de restauración, es necesario realizar la copia de seguridad de los archivos de configuración de HANA por separado.

Si las copias de seguridad de HANA estándar van a un sistema de archivos de copia de seguridad de HANA dedicado, puede copiar también los archivos de configuración en el mismo sistema de archivos de copia de seguridad y, a continuación, copiarlo todo junto en el destino final de almacenamiento como un almacenamiento de blobs esporádico.

### <a name="sap-hana-cockpit"></a>SAP HANA Cockpit

SAP HANA Cockpit ofrece la posibilidad de supervisar y administrar SAP HANA a través de un explorador. También permite administrar copias de seguridad de SAP HANA y, por lo tanto, puede utilizarse como alternativa a SAP HANA Studio y ABAP DBACOCKPIT (vea [SAP HANA Cockpit](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) para obtener más información).

![Esta ilustración muestra la pantalla de administración de base de datos de SAP HANA Cockpit](media/sap-hana-backup-guide/image004.png)

Esta ilustración muestra la pantalla de administración de base de datos de SAP HANA Cockpit y el icono de copia de seguridad de la izquierda. La visualización del icono de copia de seguridad requiere permisos de usuario apropiados para la cuenta de inicio de sesión.

![Las copias de seguridad pueden supervisarse en SAP HANA Cockpit mientras están en curso](media/sap-hana-backup-guide/image005.png)

Las copias de seguridad pueden supervisarse en SAP HANA Cockpit mientras están en curso y, una vez que hayan finalizado, todos los detalles de la copia de seguridad están disponibles.

![Un ejemplo de uso de Firefox en una VM de Azure SLES 12 con el escritorio de Gnome](media/sap-hana-backup-guide/image006.png)

Las capturas de pantalla anteriores se realizaron desde una máquina virtual Windows de Azure. Este es un ejemplo de uso de Firefox en una VM de Azure SLES 12 con el escritorio de Gnome Muestra la opción para definir programaciones de copia de seguridad de SAP HANA en SAP HANA Cockpit. A medida que vea, sugiere una fecha y hora como prefijo para los archivos de copia de seguridad. En SAP HANA Studio, el prefijo predeterminado es &quot;COMPLETE\_DATA\_BACKUP&quot; al realizar una copia de seguridad completa. Se recomienda usar un prefijo único.

### <a name="sap-hana-backup-encryption"></a>Cifrado de copia de seguridad de SAP HANA

SAP HANA ofrece cifrado de datos y de registro. Si no se cifran los datos de SAP HANA y de registro, las copias de seguridad tampoco se cifran. Es responsabilidad del cliente usar algún tipo de solución de terceros para cifrar las copias de seguridad de SAP HANA. Vea [Cifrado de datos y volumen de registro](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) para obtener más información sobre el cifrado de SAP HANA.

En Microsoft Azure, un cliente puede utilizar la característica de cifrado de VM IaaS para cifrar. Por ejemplo, puede utilizar discos de datos dedicados asociados a la VM, que se usan para almacenar copias de seguridad de SAP HANA y luego realizar copias de estos discos.

El servicio Azure Backup puede controlar las VM o los discos cifrados (consulte [Procedimiento de realización de copias de seguridad y restauración de máquinas virtuales cifradas con Azure Backup](../../../backup/backup-azure-vms-encryption.md)).

Otra opción sería mantener la VM de SAP HANA y sus discos sin cifrado y almacenar los archivos de copia de seguridad de SAP HANA en una cuenta de almacenamiento para la que se ha habilitado el cifrado (vea [Cifrado del servicio Azure Storage para datos en reposo (versión preliminar)](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Prueba de configuración

### <a name="test-virtual-machine-on-azure"></a>Prueba de la máquina virtual en Azure

Se usó una instalación de SAP HANA en una máquina virtual Azure GS5 para las siguientes pruebas de copia de seguridad/restauración.

![En esta ilustración se muestra parte de la información general de Azure Portal para la VM de prueba HANA](media/sap-hana-backup-guide/image007.png)

En esta ilustración se muestra parte de la información general de Azure Portal para la VM de prueba HANA.

### <a name="test-backup-size"></a>Prueba de tamaño de copia de seguridad

![Esta ilustración se realizó a partir de la consola de copia de seguridad en HANA Studio y muestra el tamaño del archivo de copia de seguridad de 229 GB para el servidor de indexación de HANA](media/sap-hana-backup-guide/image008.png)

Se completó una tabla ficticia con datos para obtener un tamaño de copia de seguridad total de más de 200 GB para obtener datos con un rendimiento realista. La ilustración se realizó a partir de la consola de copia de seguridad en HANA Studio y muestra el tamaño del archivo de copia de seguridad de 229 GB para el servidor de indexación de HANA. Para las pruebas, se utilizó el prefijo de copia de seguridad predeterminado "COMPLETE_DATA_BACKUP" en SAP HANA Studio. En los sistemas de producción real, debe definirse un prefijo más útil. SAP HANA Cockpit sugiere la fecha/hora.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Prueba de la herramienta para copiar archivos directamente en Azure Storage

Para transferir archivos de copia de seguridad de SAP HANA directamente a Azure Blob Storage o recursos compartidos de archivos de Azure, se usó la herramienta blobxfer, ya que admite ambos destinos y se puede integrar fácilmente en los scripts de automatización debido a su interfaz de línea de comandos. La herramienta blobxfer está disponible en [GitHub](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Prueba de estimación del tamaño de la copia de seguridad

Es importante estimar el tamaño de la copia de seguridad de SAP HANA. Esta estimación ayuda a mejorar el rendimiento al definir el tamaño máximo de archivo de copia de seguridad para un número de archivos de copia de seguridad, debido al paralelismo durante la copia de archivo. (Estos detalles se explican posteriormente en este artículo). Debe decidir también si se debe realizar una copia de seguridad completa o delta (incremental o diferencial).

Afortunadamente, hay una instrucción SQL que calcula el tamaño de los archivos de copia de seguridad: **seleccione \* a partir de M\_BACKUP\_SIZE\_ESTIMATIONS** (vea [Estimación del espacio necesario en el sistema de archivos para una copia de seguridad de datos](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![El resultado de esta instrucción SQL coincide con casi exactamente el tamaño real de la copia de seguridad de datos completa en disco](media/sap-hana-backup-guide/image009.png)

Para el sistema de prueba, el resultado de esta instrucción SQL coincide con casi exactamente el tamaño real de la copia de seguridad de datos completa en disco.

### <a name="test-hana-backup-file-size"></a>Prueba del tamaño del archivo de copia de seguridad de HANA

![La consola de copia de seguridad de HANA Studio permite restringir el tamaño máximo de archivo de los archivos de copia de seguridad de HANA](media/sap-hana-backup-guide/image010.png)

La consola de copia de seguridad de HANA Studio permite restringir el tamaño máximo de archivo de los archivos de copia de seguridad de HANA. En el entorno de ejemplo, esta característica permite obtener varios archivos de copia de seguridad más pequeños en lugar de un archivo de copia de seguridad de 230 GB. El tamaño de archivo más pequeño un impacto significativo en el rendimiento (vea el artículo relacionado [Azure Backup de SAP HANA en el nivel de archivo](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Resumen

Basándose en los resultados de prueba, las siguientes tablas muestran las ventajas y desventajas de las soluciones para realizar copias de seguridad de una base de datos de SAP HANA que se ejecuta en Azure Virtual Machines.

**Copia de seguridad de SAP HANA al sistema de archivos y copia de archivos de copia de seguridad posteriormente en el destino de copia de seguridad final**

|Solución                                           |Ventajas                                 |Desventajas                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Conservación de copias de seguridad de HANA en discos de VM                      |No hay esfuerzos de administración adicionales     |Consume espacio en disco de VM local           |
|Herramienta Bloxfer para copiar archivos de copia de seguridad en almacenamiento de blobs |Paralelismo para copiar varios archivos, opción de usar el almacenamiento de blobs esporádico | Mantenimiento de la herramienta adicional y scripting personalizado | 
|Copia de blobs a través de Powershell o CLI                    |No es necesaria ninguna herramienta adicional, puede realizarse a través de Azure Powershell o CLI |proceso manual, el cliente tiene que realizar el scripting y la administración de blobs copiados para la restauración|
|Copia del recurso compartido NFS                                  |Procesamiento posterior de archivos de copia de seguridad en otra VM sin afectar al servidor de HANA|Proceso de copia lento|
|Copia de Blobxfer al servicio de archivos de Azure                |No consume espacio en discos de VM locales|Escritura directa no compatible con copia de seguridad de HANA, restricción del tamaño del recurso compartido de archivo actual de 5 TB|
|Azure Backup Agent                                 | Sería la mejor solución         | No disponible actualmente en Linux    |



**Copia de seguridad de SAP HANA basada en instantáneas de almacenamiento**

|Solución                                           |Ventajas                                 |Desventajas                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Servicio Azure Backup                               | Permite la copia de seguridad de VM basada en instantáneas de blobs | Si no se usa la restauración a nivel de archivo, requiere la creación de una nueva VM para el proceso de restauración, lo que entonces implica que sea necesaria una nueva clave de licencia de SAP HANA|
|Instantáneas de blobs manuales                              | Flexibilidad para crear y restaurar discos de VM específicos sin cambiar el identificador único de VM|Todo el trabajo es manual, tiene que hacerlo el cliente|

## <a name="next-steps"></a>Pasos siguientes
* [Azure Backup de SAP HANA en el nivel de archivo](sap-hana-backup-file-level.md) describe la opción de copia de seguridad basada en archivos.
* [Copia de seguridad de SAP HANA basada en instantáneas de almacenamiento](sap-hana-backup-storage-snapshots.md) describe la opción de copia de seguridad basada en instantáneas de almacenamiento.
* Para obtener información sobre cómo establecer la alta disponibilidad y planear la recuperación ante desastres de SAP HANA en Azure (instancias grandes), vea [Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes)](hana-overview-high-availability-disaster-recovery.md).
