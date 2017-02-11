---
title: "Almacenamiento de máquinas virtuales con Linux y Azure | Microsoft Docs"
description: "Describe los servicios Standard y Premium Storage de Azure con máquinas virtuales con Linux."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: d364c69e-0bd1-4f80-9838-bbc0a95af48c
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/04/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: bc18d25044fb790ef85ce950a785259cc1204fe4


---
# <a name="azure-and-linux-vm-storage"></a>Almacenamiento de máquinas virtuales con Linux y Azure
Almacenamiento de Azure es la solución de almacenamiento en la nube para las aplicaciones modernas que dependen de la durabilidad, la disponibilidad y la escalabilidad para satisfacer las necesidades de sus clientes.  Además de permitir a los desarrolladores compilar aplicaciones a gran escala para sustentar nuevos escenarios, Azure Storage proporciona también la base de almacenamiento para Azure Virtual Machines.

## <a name="azure-storage-standard-and-premium"></a>Azure Storage: Standard y Premium
Las máquinas virtuales de Azure se pueden crear en discos de almacenamiento estándar o premium.  Al usar el Portal para elegir la máquina virtual debe alternar un menú desplegable en la pantalla de conceptos básicos para ver los discos estándar y premium.  La captura de pantalla siguiente resalta ese menú de alternancia.  Cuando esté activo SSD, solo aparecerán las máquinas virtuales con Premium Storage habilitado, todas respaldadas por unidades SSD.  Cuando esté activo HDD, aparecerán las unidades de disco de giro respaldadas por máquinas virtuales habilitadas para el almacenamiento estándar, junto con las máquinas virtuales de Premium Storage respaldadas por SSD.

  ![screen1](../virtual-machines/media/virtual-machines-linux-azure-vm-storage-overview/screen1.png)

Al crear una máquina virtual desde `azure-cli` puede elegir entre estándar y premium al elegir el tamaño de máquina virtual a través de la marca de CLI `-z` o `--vm-size`.

### <a name="create-a-vm-with-standard-storage-vm-on-the-cli"></a>Creación de una máquina virtual con la máquina virtual de almacenamiento estándar en la CLI
La marca de CLI `-z` elige Standard_A1, siendo A1 la máquina virtual con Linux basada en el almacenamiento estándar.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_A1
```

### <a name="create-a-vm-with-premium-storage-on-the-cli"></a>Creación de una máquina virtual con Premium Storage en la CLI
La marca de CLI `-z` elige Standard_DS1, siendo DS1 la máquina virtual con Linux basada en Premium Storage.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_DS1
```

## <a name="standard-storage"></a>Standard storage
Azure Standard Storage es el tipo de almacenamiento estándar.  Standard Storage es un sistema rentable que sigue teniendo un buen rendimiento.  

## <a name="premium-storage"></a>Premium Storage
El Almacenamiento premium de Azure le ofrece soporte de disco de alto rendimiento y latencia baja para máquinas virtuales con cargas de trabajo intensivas de E/S. Discos de máquinas virtuales que usan datos de almacén de Almacenamiento premium en unidades de estado sólido (SSD). Puede migrar los discos de máquina virtual de su aplicación al Almacenamiento premium de Azure para aprovechar la velocidad y rendimiento de estos discos.

Características de Premium Storage:

* Discos de Premium Storage: Azure Premium Storage admite discos de máquina virtual que se pueden conectar a las máquinas virtuales de Azure de la serie DS, DSv2 o GS.
* Blob en páginas premium: Premium Storage admite blobs en páginas de Azure, que se usan para contener discos persistentes para máquinas virtuales de Azure (VM).
* Almacenamiento con redundancia local premium: una cuenta de Premium Storage solo admite almacenamiento con redundancia local (LRS) como opción de replicación y mantiene tres copias de los datos en una única región.
* [Premium Storage](../storage/storage-premium-storage.md)

## <a name="premium-storage-supported-vms"></a>Máquinas virtuales compatibles con Premium Storage
Premium Storage es compatible con las máquinas virtuales de Azure de las series DS, DSv2, GS y Fs. Puede usar discos de Standard Storage y Premium Storage con las máquinas virtuales compatibles con Premium Storage. Sin embargo, no puede utilizar discos de Premium Storage con series de máquinas virtuales que no son compatibles con este tipo de almacenamiento.

Las siguientes son las distribuciones de Linux que se validan con Almacenamiento premium.

| Distribución | Versión | Kernel compatible |
| --- | --- | --- |
| Ubuntu |12.04 |3.2.0-75.110+ |
| Ubuntu |14.04 |3.13.0-44.73+ |
| Debian |7.x, 8.x |3.16.7-ckt4-1+ |
| SLES |SLES 12 |3.12.36-38.1+ |
| SLES |SLES 11 SP4 |3.0.101-0.63.1+ |
| CoreOS |584.0.0+ |3.18.4+ |
| CentOS |6.5, 6.6, 6.7, 7.0, 7.1 |3.10.0-229.1.2.el7+ |
| RHEL |6.8+, 7.2+ | |

## <a name="file-storage"></a>Almacenamiento de archivos
El almacenamiento de archivos de Azure ofrece recursos compartidos de archivos en la nube mediante el protocolo SMB estándar. Con archivos de Azure, puede migrar las aplicaciones empresariales que se basan en los servidores de archivos a Azure. Las aplicaciones que se ejecutan en Azure pueden montar fácilmente recursos compartidos de archivos de máquinas virtuales de Azure que ejecutan Linux. Y con la última versión de Almacenamiento de archivos, es posible también montar un recurso compartido de archivos desde una aplicación local que sea compatible con SMB 3.0.  Dado que los recursos compartidos de archivos son recursos compartidos de SMB, puede tener acceso a ellos a través de las API del sistema de archivos estándar.

El almacenamiento de archivos se basa en la misma tecnología que el almacenamiento de blobs, tablas y en cola, por lo que el almacenamiento de archivos ofrece la disponibilidad, durabilidad, escalabilidad y redundancia geográfica que están integradas en la plataforma de Almacenamiento de Azure. Para más información sobre los límites y objetivos de rendimiento de Azure Storage, vea Objetivos de escalabilidad y rendimiento del almacenamiento de Azure.

* [Uso del almacenamiento de archivos de Azure con Linux](../storage/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>Almacenamiento de acceso frecuente
La capa de almacenamiento de acceso frecuente de Azure está optimizada para almacenar datos que se consultan con frecuencia.  El almacenamiento de acceso frecuente es el tipo de almacenamiento para almacenes de blobs.

## <a name="cool-storage"></a>Almacenamiento de acceso esporádico
La capa de almacenamiento de acceso esporádico de Azure está optimizada para almacenar datos a los que se accede con poca frecuencia y tienen larga duración. Entre los casos de uso de este tipo de almacenamiento se encuentran las copias de seguridad, el contenido multimedia, los datos científicos, la información de cumplimiento y los datos de archivo. En general, cualquier dato al que rara vez se obtiene acceso es un candidato perfecto para el almacenamiento de acceso esporádico.

|  | capa de almacenamiento de acceso frecuente | capa de almacenamiento de acceso esporádico |
|:--- |:---:|:---:|
| Disponibilidad |99,9 % |99% |
| Disponibilidad (lecturas de RA-GRS) |99,99% |99,9 % |
| Cargos de uso |Mayores costos de almacenamiento |Menores costos de almacenamiento |
| Menor acceso |Mayor acceso | |
| y costos de transacción |y costos de transacción | |

## <a name="redundancy"></a>Redundancia
Los datos de su cuenta de almacenamiento de Microsoft Azure siempre se replican para garantizar la durabilidad y la alta disponibilidad, cumpliendo el SLA de Almacenamiento de Azure incluso ante errores transitorios de hardware.

Cuando cree una cuenta de almacenamiento, debe seleccionar una de las siguientes opciones de replicación:

* Almacenamiento con redundancia local (LRS)
* Almacenamiento con redundancia de zona (ZRS)
* Almacenamiento con redundancia geográfica (GRS)
* Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS).

### <a name="locally-redundant-storage"></a>Almacenamiento con redundancia local
El almacenamiento con redundancia local (LRS) replica los datos dentro de la región en la que creó su cuenta de almacenamiento. Para maximizar la durabilidad, cada solicitud hecha respecto de los datos en la cuenta de almacenamiento se replica tres veces. Cada una de estas tres réplicas reside en dominios de error y dominios de actualización independientes.  Una solicitud se devuelve correctamente solo una vez que se ha escrito a todas las tres réplicas.

### <a name="zone-redundant-storage"></a>Almacenamiento con redundancia de zona
El almacenamiento con redundancia de zona (ZRS) replica sus datos entre dos o tres instalaciones, ya sea dentro de una sola región o entre dos regiones, lo que proporciona una mayor durabilidad que LRS. Si su cuenta de almacenamiento tiene habilitado ZRS, sus datos se conservan incluso ante un error en una de las instalaciones.

### <a name="geo-redundant-storage"></a>Almacenamiento con redundancia geográfica
El almacenamiento con redundancia geográfica (GRS) replica sus datos a una región secundaria que se encuentra a cientos de kilómetros de distancia de la región primaria. Si la cuenta de almacenamiento tiene habilitado GRS, sus datos se mantienen incluso ante un apagón regional completo o un desastre del cual la región principal no se puede recuperar.

### <a name="read-access-geo-redundant-storage"></a>Almacenamiento con redundancia geográfica con acceso de lectura
El almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) maximiza la disponibilidad para la cuenta de almacenamiento al proporcionar acceso de solo lectura a los datos de la ubicación secundaria, además de la replicación entre dos regiones que proporciona GRS. Ante la eventualidad de que los datos dejen de estar disponibles en la región principal, la aplicación puede leer datos desde la región secundaria.

Para profundizar en la redundancia de Azure Storage, vea:

* [Replicación de almacenamiento de Azure](../storage/storage-redundancy.md)

## <a name="scalability"></a>Escalabilidad
Almacenamiento de Azure se puede escalar de forma masiva para almacenar y procesar cientos de terabytes de datos y admitir así los escenarios de datos de gran tamaño requeridos por las aplicaciones científicas, de análisis financiero y multimedia. Igualmente, permite almacenar las pequeñas cantidades de datos que se necesitan en un sitio web de una pequeña empresa. Sean cuales sean sus necesidades, solo tendrá que pagar por los datos que almacene. Actualmente, Almacenamiento de Azure alberga decenas de billones de objetos de cliente únicos y administra, por término medio, millones de solicitudes por segundo.

En el caso de cuentas de almacenamiento estándar: una cuenta de almacenamiento estándar tiene una tasa total máxima de solicitudes de 20 000 E/S por segundo. El número total de E/S por segundo en todos los discos de máquina virtual de una cuenta de almacenamiento estándar no debe superar este límite.

En el caso de cuentas de Premium Storage: una cuenta de Premium Storage tiene una capacidad total máxima de proceso de 50 Gbps. La capacidad total de proceso en todos los discos de la máquina virtual no debe superar este límite.

## <a name="availability"></a>Disponibilidad
Garantizamos que al menos el 99,99 % (99,9 % para el nivel de acceso esporádico) del tiempo, procesaremos las solicitudes de lectura de datos de cuentas de almacenamiento geográficamente redundante con acceso de lectura (RA-GRS), siempre y cuando los intentos fallidos de lectura de datos de la región primaria se vuelvan a intentar en la región secundaria.

Garantizamos que, al menos durante el 99,9 % (99 % para el nivel de acceso esporádico) del tiempo, procesaremos correctamente las solicitudes de lectura de datos de cuentas de almacenamiento con redundancia local (LRS), almacenamiento con redundancia de zona (ZRS) y almacenamiento con redundancia geográfica (GRS).

Garantizamos que, al menos durante el 99,9 % (99 % para el nivel de acceso esporádico) del tiempo, procesaremos correctamente las solicitudes de escritura de datos en cuentas de almacenamiento con redundancia local (LRS), almacenamiento con redundancia de zona (ZRS) y almacenamiento con redundancia geográfica (GRS), además de las cuentas de almacenamiento geográficamente redundante con acceso de lectura (RA-GRS).

* [Acuerdo de Nivel de Servicio para Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)

## <a name="regions"></a>Regiones
Azure tiene disponibilidad general en 30 regiones de todo el mundo y ha anunciado planes para 4 regiones adicionales. La expansión geográfica es un aspecto prioritario para Azure, ya que permite que los clientes consigan un mayor rendimiento y apoya los requisitos y preferencias de los clientes con respecto a la ubicación de los datos.  La última región de Azure que se ha puesto en marcha es la de Alemania.

Microsoft Cloud Germany ofrece una opción diferente de los servicios de Microsoft Cloud ya disponibles en Europa, creando más oportunidades de innovación y crecimiento económico para asociados y clientes exhaustivamente regulados en Alemania, la Unión Europea (EU) y la Asociación Europea de Libre Comercio (AELC).

Los datos de los clientes en los nuevos centros de datos, ubicados en Magdeburg y Fráncfort, se encuentran bajo el control del administrador de datos T-Systems International, una empresa alemana filial de Deutsche Telekom. Los servicios comerciales en la nube de Microsoft de estos centros de datos se adhieren a las normas alemanas relativas al control de datos y ofrecen a los clientes opciones adicionales acerca de cómo y dónde se procesan los datos.

* [Mapa de las regiones de Azure](https://azure.microsoft.com/regions/)

## <a name="security"></a>Seguridad
Almacenamiento de Azure pone a su disposición diferentes funciones de seguridad que, al usarlas en conjunto, permiten a los desarrolladores crear aplicaciones seguras. La propia cuenta de almacenamiento se puede proteger mediante el control de acceso basado en rol y Azure Active Directory. Los datos se pueden proteger en tránsito entre una aplicación y Azure usando cifrado de cliente, HTTPS o SMB 3.0. Los datos se pueden configurar para que se cifren automáticamente cuando se escriben en Azure Storage mediante el cifrado del servicio de almacenamiento (SSE). Se puede establecer el cifrado de los discos de datos y del sistema operativo utilizados por máquinas virtuales mediante el Cifrado de discos de Azure. Se puede conceder acceso delegado a los objetos de datos de Almacenamiento de Azure mediante las Firmas de acceso compartido.

### <a name="management-plane-security"></a>Seguridad en el plano de la administración
El plano de la administración está compuesto por los recursos que se usan para administrar la cuenta de almacenamiento. En esta sección, hablaremos sobre el modelo de implementación de Azure Resource Manager y cómo usar el control de acceso basado en rol (RBAC) para controlar el acceso a sus cuentas de almacenamiento. También hablaremos acerca de cómo administrar las claves de la cuenta de almacenamiento y cómo volver a generarlas.

### <a name="data-plane-security"></a>Seguridad en el plano de los datos
En esta sección, analizaremos el modo de permitir el acceso a los objetos de datos reales de la cuenta de almacenamiento, como blobs, archivos, colas y tablas, y el uso de Firmas de acceso compartido y Directivas de acceso almacenadas. Nos fijaremos en las Firmas de acceso compartido tanto a nivel de servicio como a nivel de cuenta. También veremos cómo limitar el acceso a una dirección IP específica (o un intervalo de direcciones IP), cómo limitar el protocolo utilizado para HTTPS y cómo revocar una Firma de acceso compartido sin esperar a que expire.

## <a name="encryption-in-transit"></a>Cifrado en tránsito
En esta sección se describe cómo proteger los datos cuando se transfieren a o desde Almacenamiento de Azure. Hablaremos sobre el uso recomendado de HTTPS y el cifrado usado por SMB 3.0 para recursos compartidos de archivos de Azure. También echaremos un vistazo al Cifrado de cliente, que permite cifrar los datos antes de transferirlos al Almacenamiento en una aplicación cliente y descifrarlos una vez transferidos desde este servicio.

## <a name="encryption-at-rest"></a>Cifrado en reposo
Hablaremos acerca del cifrado del servicio de almacenamiento (SSE) y el modo de habilitarlo para una cuenta de almacenamiento, lo que conlleva que los blobs en bloques, los blobs en páginas y los blobs en anexos se cifren automáticamente al escribir en Almacenamiento de Azure. También veremos cómo puede usar Cifrado de discos de Azure y exploraremos los casos y las diferencias básicas entre Cifrado de discos, Cifrado del servicio de almacenamiento y Cifrado en el cliente. Asimismo, estudiaremos brevemente el cumplimiento de la norma FIPS para equipos del Gobierno de EE. UU.

* [Guía de seguridad de Azure Storage](../storage/storage-security-guide.md)

## <a name="cost-savings"></a>Ahorros en costos
* [Costo del almacenamiento](https://azure.microsoft.com/pricing/details/storage/)
* [Calculadora de costo de almacenamiento](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>Límites de almacenamiento
* [Límites del servicio Storage](../azure-subscription-service-limits.md#storage-limits)




<!--HONumber=Nov16_HO3-->


