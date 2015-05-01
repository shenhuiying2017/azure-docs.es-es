
<properties 
  pageTitle="Opciones de redundancia de Almacenamiento de Azure | Microsoft Azure" 
  description="Los datos de su cuenta de almacenamiento de Microsoft Azure siempre se replican para garantizar la durabilidad y la alta disponibilidad. Obtenga información acerca de las opciones de replicación para su cuenta de Almacenamiento de Azure." 
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="adinah" 
  editor=""/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="03/20/2015" 
  ms.author="tamram"/>

# Opciones de redundancia de Almacenamiento de Azure



Los datos de su cuenta de almacenamiento de Microsoft Azure siempre se replican para garantizar la durabilidad y la alta disponibilidad, cumpliendo el [SLA de Almacenamiento de Azure](http://azure.microsoft.com/support/legal/sla/) incluso ante errores transitorios de hardware. Para obtener detalles de arquitectura sobre la durabilidad con Almacenamiento de Azure, consulte el [Documento de SOSP de Almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

Cuando cree la cuenta de almacenamiento, debe seleccionar una de las siguientes opciones de replicación:  

- **Almacenamiento con redundancia local (LRS).** El almacenamiento con redundancia local conserva tres copias de sus datos. LRS se replica tres veces dentro de una sola instalación en una región. LRS protege los datos ante errores normales de hardware, pero no ante los errores de una instalación.
  
LRS se ofrece con un descuento. Para la máxima durabilidad, es recomendable utilizar el almacenamiento con redundancia geográfica, que se describe a continuación.


- **Almacenamiento con redundancia de zona (ZRS).** El almacenamiento con redundancia de zona conserva tres copias de sus datos. ZRS se replica tres veces entre dos o tres instalaciones, ya sea dentro de una sola región o entre dos regiones, proporcionando una durabilidad más alta que LRS. ZRS garantiza que los datos se conservan dentro de una región única.
ZRS ofrece un mayor nivel de durabilidad que LRS; sin embargo, para disfrutar de la máxima durabilidad, es recomendable utilizar el almacenamiento con redundancia geográfica, que se describe a continuación.  

	> [AZURE.NOTE] ZRS solo está disponible actualmente para blobs en bloques.  
	> Una vez que ha creado su cuenta de almacenamiento y ha seleccionado la replicación con redundancia de zona, no es posible convertirla para usar ningún otro tipo de replicación o viceversa.
 


- **Almacenamiento con redundancia geográfica (GRS)**. El almacenamiento con redundancia geográfica está habilitado para su cuenta de almacenamiento de manera predeterminada cuando la crea. GRS mantiene seis copias de sus datos. Con GRS, sus datos se replican tres veces dentro de la región principal, y se replican también tres veces en una región secundaria a cientos de kilómetros de distancia de la región principal, proporcionando el nivel más alto de durabilidad. En caso de que se produzca un error en la región principal, Almacenamiento de Azure conmutará por error a la región secundaria. GRS garantiza la durabilidad de sus datos en dos regiones independientes.


- **Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS)**. El almacenamiento con redundancia geográfica con acceso de lectura replica sus datos a una ubicación geográfica secundaria y, además, proporciona acceso de lectura a sus datos en la ubicación secundaria. El almacenamiento con redundancia geográfica con acceso de lectura le permite tener acceso a los datos desde la ubicación principal o la secundaria, ante la eventualidad de que alguna de las ubicaciones deje de estar disponible.

	> [AZURE.IMPORTANT] Puede cambiar la manera en que se replican los datos una vez creada su cuenta de almacenamiento, pero tenga en cuenta que puede incurrir en un coste por transferencia de datos adicional único si cambia de LRS a GRS o RA-GRS. Si elige GRS cuando crea la cuenta, no puede cambiar de manera subsiguiente a ningún otro tipo de replicación o viceversa.
 

La siguiente tabla proporciona una breve descripción de las diferencias entre LRS, ZRS, GRS y RA-GRS, mientras que secciones subsiguientes abordan con más detalle cada tipo de replicación.


|Estrategia de replicación|LRS|ZRS|GRS|RA-GRS 
|--------------------|---|---|---|------
|Los datos se replican entre varias instalaciones|No|Sí|Sí|Sí|
|Los datos se pueden leer desde la ubicación secundaria al igual que desde la ubicación principal|No|No|No|Sí
|Cantidad de copias de datos mantenidas en nodos independientes|3|3|6|6
 

Para obtener información de precios para la replicación de cuentas de almacenamiento, consulte [Detalles de precios de almacenamiento.](http://azure.microsoft.com/pricing/details/storage/)

## Almacenamiento con redundancia local (LRS)

El almacenamiento con redundancia local replica los datos dentro de la región en la que creó su cuenta de almacenamiento. Para maximizar la durabilidad, cada solicitud hecha respecto de los datos en la cuenta de almacenamiento se replica tres veces. Cada una de estas tres réplicas reside en dominios de error y dominios de actualización independientes. Un dominio de error (FD) es un grupo de nodos que representan una unidad física de error y se pueden considerar como nodos pertenecientes a la misma estantería. Un dominio de actualización (UD) es un grupo de nodos que se actualizan en conjunto durante el proceso de actualización del servicio (implementación). Las tres réplicas se distribuyen entre dominios UD y FD para garantizar que los datos se encuentran disponibles incluso si un error de hardware afecta a una sola estantería y cuando los nodos se actualizan durante una implementación. Una solicitud se devuelve correctamente solo una vez que se ha escrito a todas las tres réplicas.

A pesar que el almacenamiento con redundancia geográfica se recomienda para la mayoría de las aplicaciones, el almacenamiento con redundancia local puede ser deseable en ciertos escenarios:  

- LRS es menos costoso que GRS y también ofrece un mayor rendimiento. Si su aplicación almacena datos que se pueden reconstruir fácilmente, puede optar por LRS.

- Algunas aplicaciones están restringidas a la replicación de datos solo dentro de una región debido a requisitos de gobernanza de datos.

- Si la aplicación tiene su propia estrategia de replicación geográfica, es posible que no requiera GRS.


## Almacenamiento con redundancia de zona (ZRS)

El almacenamiento con redundancia de zona replica sus datos entre dos o tres instalaciones, ya sea dentro de una sola región o entre dos regiones, lo que proporciona una mayor durabilidad que LRS. Si su cuenta de almacenamiento tiene habilitado ZRS, sus datos se conservan incluso ante un error en una de las instalaciones.


>[AZURE.NOTE]  ZRS solo está disponible actualmente para blobs en bloques. Tenga en cuenta que una vez que haya creado la cuenta de almacenamiento y haya seleccionado la replicación con redundancia de zona, no podrá convertirla para utilizar cualquier otro tipo de aplicación (o viceversa).


## Almacenamiento con redundancia geográfica (GRS)


El almacenamiento con redundancia geográfica replica sus datos a una región secundaria que se encuentra a cientos de kilómetros de distancia de la región principal. Si la cuenta de almacenamiento tiene habilitado GRS, sus datos se mantienen incluso ante un apagón regional completo o un desastre del cual la región principal no se puede recuperar. 

En el caso de una cuenta de almacenamiento con GRS habilitado, una actualización primero se envía a la región principal, donde se replica tres veces. Luego la actualización se replica a la región secundaria, donde también se replica tres veces, entre dominios de error y dominios de actualización independientes.

 
> [AZURE.NOTE] Con GRS, las solicitudes para escribir datos se replican de manera asincrónica a la región secundaria. Es importante tener en cuenta que optar por GRS no afecta la latencia de las solicitudes realizadas respecto de la región principal. Sin embargo, como la replicación asincrónica implica un retraso, ante la eventualidad de un desastre regional es posible que los cambios que todavía no se hayan replicado a la región secundaria se pierdan si no es posible recuperar los datos desde la región principal.
 
Cuando crea una cuenta de almacenamiento, selecciona la región principal de la cuenta. La región secundaria se determina según la región principal y no es posible cambiarla. La siguiente tabla muestra los emparejamientos de la región principal y la secundaria:

|Principal            |Secundaria        
| ---------------   |----------------
|Centro y norte de EE. UU.   |Centro y sur de EE. UU.
|Centro y sur de EE. UU.   |Centro y norte de EE. UU.
|Este de EE. UU.            |Oeste de EE. UU.        
|Oeste de EE. UU.            |Este de EE. UU.         
|Este de EE. UU. 2          |Centro de EE. UU.      
|Centro de EE. UU.         |Este de EE. UU.       
|Norte de Europa       |Oeste de Europa     
|Oeste de Europa        |Norte de Europa    
|Sudeste de Asia    |Este de Asia       
|Este de Asia          |Sudeste de Asia 
|Este de China         |Norte de China     
|Norte de China        |Este de China      
|Este de Japón         |Oeste de Japón      
|Oeste de Japón         |Este de Japón      
|Sur de Brasil       |Centro y sur de EE. UU.
|Este de Australia     |Sudeste de Australia
|Sudeste de Australia|Este de Australia  

 
## Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS)

El almacenamiento con redundancia geográfica con acceso de lectura maximiza la disponibilidad para la cuenta de almacenamiento al proporcionar acceso de solo lectura a los datos de la ubicación secundaria, además de la replicación entre dos regiones que proporciona GRS. Ante la eventualidad de que los datos dejen de estar disponibles en la región principal, la aplicación puede leer datos desde la región secundaria.

Cuando habilita el acceso de solo lectura a los datos en la región secundaria, los datos quedan disponibles en un extremo secundario, además del extremo principal para la cuenta de almacenamiento. El extremo secundario es similar al extremo principal, pero anexa el sufijo "-secondary" al nombre de la cuenta. Por ejemplo, si el extremo principal del servicio BLOB es `myaccount.blob.core.windows.net`, el extremo secundario es `myaccount-secondary.blob.core.windows.net`. Las claves de acceso de la cuenta de almacenamiento son iguales para los extremos principal y secundario.

## Pasos siguientes

- [Destinos de rendimiento y escalabilidad de Almacenamiento de Azure](storage-scalability-targets.md) 
- [Opciones de redundancia de Almacenamiento de Microsoft Azure y Almacenamiento con redundancia geográfica con acceso de lectura ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)  
- [Emulador de almacenamiento de Microsoft Azure 3.1 con RA-GRS ](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/08/microsoft-azure-storage-emulator-3-1-with-ra-grs.aspx)
- [Documento de SOSP de Almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  


<!--HONumber=52-->