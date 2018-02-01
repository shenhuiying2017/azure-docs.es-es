---
title: "Replicación de datos en Azure Storage | Microsoft Docs"
description: "Los datos de su cuenta de Microsoft Azure Storage se replican para garantizar la durabilidad y la alta disponibilidad. Entre las opciones de replicación se incluyen el almacenamiento con redundancia local (LRS), el almacenamiento con redundancia de zona (ZRS), el almacenamiento con redundancia geográfica (GRS) y el almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS)."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: a8a8d8e95af3e6d98aa4dd98b11c066dca81421b
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="azure-storage-replication"></a>Replicación de Azure Storage

Los datos de su cuenta de almacenamiento de Microsoft Azure se replican siempre para garantizar la durabilidad y la alta disponibilidad. La replicación copia los datos para que estén protegidos contra los errores de hardware transitorios, conservando así el tiempo de actividad de la aplicación. 

Puede optar por replicar los datos en el mismo centro de datos, en centros de datos que estén en la misma región o entre regiones. Si los datos se replican a través de varios centros de datos o entre regiones, también están protegidos contra errores graves en una sola ubicación.

La replicación garantiza que la cuenta de almacenamiento cumpla el [contrato de nivel de servicio (SLA) para Storage](https://azure.microsoft.com/support/legal/sla/storage/), incluso en caso de errores. Consulte en el SLA información acerca de las garantías de durabilidad y disponibilidad de Azure Storage.

Cuando cree una cuenta de almacenamiento, puede seleccionar una de las siguientes opciones de replicación:

* [Almacenamiento con redundancia local (LRS)](#locally-redundant-storage)
* [Almacenamiento con redundancia de zona (ZRS)](#zone-redundant-storage)
* [Almacenamiento con redundancia geográfica (GRS)](#geo-redundant-storage)
* [Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS).](#read-access-geo-redundant-storage)

El almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) es la opción predeterminada cuando crea una cuenta de almacenamiento.

En la tabla siguiente se proporciona una breve descripción de las diferencias entre los tipos LRS, ZRS, GRS y RA-GRS. Las secciones siguientes de este artículo tratan cada tipo de replicación con más detalle.

| Estrategia de replicación | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Los datos se replican entre varios centros de datos. |Sin  |Sí |Sí |Sí |
| Los datos se pueden leer desde la ubicación secundaria al igual que desde la ubicación principal. |Sin  |Sin  |Sin  |Sí |
| Diseñado para proporcionar una durabilidad de objetos ___ a lo largo de un año determinado. |Como mínimo 99.999999999 % (once nueves)|Como mínimo 99.9999999999 % (doce nueves)|Como mínimo 99.99999999999999 % (dieciséis nueves)|Como mínimo 99.99999999999999 % (dieciséis nueves)|

Consulte [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/) para más información sobre las diferentes opciones de redundancia.

> [!NOTE]
> Premium Storage solo admite almacenamiento con redundancia local (LRS). Para más información sobre Premium Storage, consulte [Premium Storage: almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../../virtual-machines/windows/premium-storage.md).
>

## <a name="locally-redundant-storage"></a>Almacenamiento con redundancia local
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## <a name="zone-redundant-storage"></a>Almacenamiento con redundancia de zona

La versión preliminar del almacenamiento con redundancia de zona (ZRS) está diseñada para simplificar el desarrollo de aplicaciones de alta disponibilidad. ZRS proporciona a los objetos de almacenamiento una durabilidad de al menos el 99,9999999999 % (doce nueves) durante un año determinado. ZRS replica los datos de forma sincrónica a través de varias zonas de disponibilidad. Puede usar ZRS en escenarios como las aplicaciones transaccionales, en los cuales el tiempo de inactividad no es aceptable.

ZRS permite a los clientes leer y escribir datos, incluso si una sola zona no está disponible o es irrecuperable. Inserta y actualiza los datos de forma sincrónica y ampliamente coherente.   

ZRS está disponible actualmente en las versiones preliminares de las siguientes regiones (muy pronto habrá más regiones disponibles):

- Este de EE. UU. - 2 
- Centro de EE. UU. 
- Centro de Francia (esta región está actualmente en versión preliminar. Consulte [Microsoft Azure preview with Azure Availability Zones now open in France](https://azure.microsoft.com/blog/microsoft-azure-preview-with-azure-availability-zones-now-open-in-france) [Versión preliminar de Microsoft Azure con las zonas de disponibilidad de Azure que están abiertas en Francia] para solicitar acceso.)

### <a name="zrs-classic-accounts"></a>Cuentas de ZRS Classic

La capacidad de ZRS existente ahora se conoce como ZRS Classic. Las cuentas de ZRS Classic solo están disponibles en los blobs en bloques de cuentas de almacenamiento V1 de uso general. 

ZRS Classic replica datos de forma asincrónica en centros de datos de una o dos regiones. Es posible que una réplica no esté disponible hasta que Microsoft inicie la conmutación por error en el elemento secundario. 

Las cuentas de ZRS Classic no pueden convertirse a (o desde) LRS, GRS o RA-GRS. Las cuentas de ZRS Classic no admiten ni las métricas ni el registro.   

Una vez que ZRS esté disponible en una región de forma general, ya no podrá crear una cuenta de ZRS Classic desde el portal de dicha región, pero puede crear una mediante otros medios.  
En el futuro se proporcionará un proceso de migración automático de ZRS Classic a ZRS.

Las cuentas de ZRS admiten la migración manual de una cuenta de ZRS en dicha región a (o desde) una cuenta de LRS, GRS o RA-GRS. Puede realizar esta migración manual mediante AzCopy, el Explorador de Azure Storage, Azure PowerShell, la CLI de Azure o una de las bibliotecas cliente de Azure Storage.

> [!NOTE]
> Las cuentas de ZRS Classic caerán en desuso y será necesario hacer la migración antes del 31 de marzo de 2021. Microsoft enviará más detalles a los clientes de ZRS Classic antes de que estas cuentas dejen de usarse.

Si tiene preguntas adicionales, puede consultar la sección [Preguntas más frecuentes](#frequently-asked-questions). 

## <a name="geo-redundant-storage"></a>Almacenamiento con redundancia geográfica
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="read-access-geo-redundant-storage"></a>Almacenamiento con redundancia geográfica con acceso de lectura
El almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) maximiza la disponibilidad de la cuenta de almacenamiento. RA-GRS proporciona acceso de solo lectura a los datos de la ubicación secundaria, además de ofrecer replicación geográfica entre dos regiones.

Cuando habilita el acceso de solo lectura para los datos de la región secundaria, los datos quedan disponibles en un punto de conexión secundario, además del punto de conexión principal de la cuenta de almacenamiento. El punto de conexión secundario es similar al punto de conexión principal, pero anexa el sufijo `–secondary` al nombre de la cuenta. Por ejemplo, si el punto de conexión principal de Blob service es `myaccount.blob.core.windows.net`, el punto de conexión secundario es `myaccount-secondary.blob.core.windows.net`. Las claves de acceso de la cuenta de almacenamiento son iguales para los extremos principal y secundario.

Tenga en cuenta lo siguiente cuando use RA-GRS:

* La aplicación tiene que administrar el punto de conexión con el que interactúa al usar RA-GRS.
* Como la replicación asincrónica implica un retraso, ante la eventualidad de un desastre regional, por ejemplo, es posible que los cambios que todavía no se hayan replicado a la región secundaria se pierdan si no se pueden recuperar los datos desde la región principal.
* Si Microsoft inicia la conmutación por error en la región secundaria, tendrá acceso de lectura y escritura a esos datos una vez completada la conmutación por error. Para obtener más información, consulte la [Guía de recuperación ante desastres](../storage-disaster-recovery-guidance.md).
* RA-GRS está pensado para fines de alta disponibilidad. Para obtener instrucciones sobre escalabilidad, revise la [lista de comprobación de rendimiento](../storage-performance-checklist.md).

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1. ¿Cómo se puede cambiar el tipo de replicación geográfica de la cuenta de almacenamiento?

   Puede cambiar el tipo de replicación geográfica de la cuenta de almacenamiento mediante [Azure Portal](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md) o una de las bibliotecas cliente de Azure Storage.

   > [!NOTE]
   > Las cuentas ZRS no se pueden convertir a LRS o GRS. Igualmente, una cuenta LRS o GRS existente no se puede convertir a una cuenta ZRS.
    
<a id="changedowntime"></a>
#### <a name="2-does-changing-the-replication-type-of-my-storage-account-result-in-down-time"></a>2. ¿Si cambio el tipo de replicación de la cuenta de almacenamiento se produce un tiempo de inactividad?

   No, si cambia el tipo de replicación de la cuenta de almacenamiento no se produce ningún tiempo de inactividad.

<a id="changecost"></a>
#### <a name="3-are-there-additional-costs-to-changing-the-replication-type-of-my-storage-account"></a>3. ¿Hay algún costo adicional si se cambia el tipo de replicación de la cuenta de almacenamiento?

   Sí. Si cambia la cuenta de almacenamiento de LRS a GRS (o RA-GRS), se incurre en un costo adicional debido a las salidas relativas a la copia de los datos existentes desde la ubicación primaria a la ubicación secundaria. Una vez copiados los datos iniciales, no hay cargos adicionales de salida por la replicación geográfica desde la ubicación primaria a la secundaria. Para obtener más información sobre los cargos de ancho de banda, consulte la [página de precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

   Si cambia de GRS a LRS, no hay ningún costo adicional, pero los datos se eliminarán de la ubicación secundaria.

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4. ¿Cómo puede ayudarme RA-GRS?

   El almacenamiento GRS proporciona replicación de los datos desde una región primaria a una secundaria que se encuentra a cientos de kilómetros de distancia de la región primaria. Con GRS, los datos se mantienen incluso en caso de un apagón regional completo o un desastre del cual la región primaria no se pueda recuperar. El almacenamiento RA-GRS ofrece replicación de GRS y agrega la capacidad de leer los datos desde la región secundaria. Para obtener sugerencias acerca de cómo diseñar la alta disponibilidad, consulte [Diseño de aplicaciones de alta disponibilidad mediante RA-GRS](../storage-designing-ha-apps-with-ragrs.md).

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5. ¿Hay alguna forma de averiguar cuánto tiempo se tarda en replicar los datos desde la región primaria a la secundaria?

   Si utiliza un almacenamiento RA-GRS puede comprobar la hora de última sincronización de la cuenta de almacenamiento. La hora de la última sincronización es un valor de fecha y hora GMT. Todas las escrituras primarias anteriores a la hora de la última sincronización se han escrito correctamente en la ubicación secundaria, lo que significa que están disponibles para leerse desde la ubicación secundaria. Las escrituras primarias posteriores a la hora de última sincronización pueden o no estar disponibles para lecturas todavía. Puede consultar este valor utilizando [Azure Portal](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md) o una de las bibliotecas cliente de Azure Storage.

<a id="outage"></a>
#### <a name="6-if-there-is-an-outage-in-the-primary-region-how-do-i-switch-to-the-secondary-region"></a>6. Si se produce una interrupción en la región primaria, ¿cómo puedo cambiar a la región secundaria?

   Para más información, vea [Qué hacer si se produce una interrupción del servicio Azure Storage](../storage-disaster-recovery-guidance.md).

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7. ¿Qué es el RPO y el RTO en GRS?

   **Objetivo de punto de recuperación (RPO)**: en GRS y RA-GRS, el servicio de almacenamiento realiza un replicación geográfica asincrónica de los datos, desde la ubicación primaria a la secundaria. Si se produce un desastre regional de gran magnitud en la región principal, Microsoft realiza una conmutación por error en la región secundaria. Si se produce una conmutación por error, se pueden perder los cambios recientes que aún no se hayan replicado geográficamente. El número de minutos de pérdida potencial de datos se conoce como RPO e indica el momento específico en el que se pueden recuperar los datos. Normalmente, Azure Storage tiene un RPO inferior a 15 minutos, aunque actualmente no hay ningún contrato de nivel de servicio sobre cuánto tiempo tarda la replicación geográfica.

   **Objetivo de tiempo de recuperación (RTO)**: se trata de una medida que indica cuánto tiempo tarda en realizarse la conmutación por error y tener la cuenta de almacenamiento de nuevo en línea. El tiempo para realizar la conmutación por error incluye lo siguiente:

   * El tiempo que Microsoft necesita para determinar si se pueden recuperar los datos en la ubicación principal, o si es necesario realizar una conmutación por error.
   * El tiempo necesario para realizar la conmutación por error de la cuenta de almacenamiento mediante el cambio de las entradas DNS principales hacia la ubicación secundaria.

   Microsoft tiene la responsabilidad de conservar los datos. Si hay alguna posibilidad de recuperar los datos en la región primaria, Microsoft retrasará la conmutación por error y se centrará en la recuperación de los datos. Una versión futura del servicio le permitirá desencadenar una conmutación por error en el nivel de cuenta, para que pueda controlar el RTO.

#### <a name="8-what-azure-storage-objects-does-zrs-support"></a>8. ¿Qué objetos de Azure Storage admite ZRS? 
Blobs en bloques, blobs en páginas (excepto los que se encargan de realizar copias de seguridad de los discos de las máquinas virtuales), tablas, archivos y colas. 

#### <a name="9-does-zrs-also-include-geo-replication"></a>9. ¿ZRS también incluye la replicación geográfica? 
Actualmente, ZRS no admite la replicación geográfica. Si su escenario requiere una replicación entre regiones a fin de poder recuperarse ante desastres, utilice una cuenta de almacenamiento de GRS o RA-GRS.   

#### <a name="10-what-happens-when-one-or-more-zrs-zones-go-down"></a>10. ¿Qué ocurre cuando una o más zonas de ZRS dejan de funcionar? 
Cuando la primera zona deja de funcionar, ZRS continúa escribiendo las réplicas de los datos entre las dos zonas restantes de la región. Si una segunda zona deja de funcionar, el acceso de lectura y escritura no estará disponible hasta que al menos dos zonas vuelvan a estar en funcionamiento. 

## <a name="next-steps"></a>pasos siguientes
* [Diseño de aplicaciones de alta disponibilidad mediante RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
* [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Acerca de las cuentas de almacenamiento de Azure](../storage-create-storage-account.md)
* [Objetivos de escalabilidad y rendimiento de Azure Storage](storage-scalability-targets.md)
* [Opciones de redundancia de Microsoft Azure Storage y almacenamiento con redundancia geográfica con acceso de lectura ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [Documento de SOSP: Azure Storage, un servicio de almacenamiento en nube altamente disponible con gran coherencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
