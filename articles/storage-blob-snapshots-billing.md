<properties 
	pageTitle="Introducción a cómo las instantáneas pueden incrementar los costes" 
	description="Guía para comprender la facturación de las instantáneas de blob de almacenamiento de Azure" 
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
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Introducción a cómo las instantáneas pueden incrementar los costes

Crear una instantánea, que es una copia de solo lectura de un blob, puede conllevar cargos adicionales de almacenamiento de datos en la cuenta. Cuando se diseñe la aplicación, es importante tener en cuenta cómo se pueden contraer gastos al objeto de minimizar los costes innecesarios.

## Consideraciones sobre facturación importantes

La lista siguiente incluye los puntos clave que hay que tener en cuenta a la hora de crear una instantánea.

- Los cargos se generan con bloques o páginas únicos, bien estén estos en el blob o en la instantánea. La cuenta no generará gastos adicionales para instantáneas asociadas a un blob hasta que actualice el blob en el que se basan. Una vez actualice el blob de base, divergirá respecto a sus instantáneas y se aplicarán cargos para los bloques o páginas únicos en cada uno de los blobs o instantáneas.

- Al reemplazar un bloque en un blob en bloques, ese bloque será considerado como un bloque único a la hora de aplicar cargos. Esto es así incluso aunque el bloque tenga el mismo identificador de bloque y los mismos datos que tiene en la instantánea. Cuando se vuelve a confirmar el bloque, divergirá de su equivalente en la instantánea y se aplicarán cargos por sus datos. Lo mismo sucede con una página en un blob en páginas que se hubiera actualizado con datos idénticos.

- Si se reemplaza un blob en bloques llamando a los métodos UploadFile, UploadText, UploadStream o UploadByteArray, se reemplazan todos los bloques del blob. Si una instantánea está asociada a ese blob, todos los bloques en el blob e instantánea de base divergirán y se aplicarán cargos por todos los bloques en ambos blobs. Esto es así incluso si los datos del blob e instantánea de base siguen siendo idénticos.

- El servicio Blob de Azure no dispone de medios para determinar si dos bloques contienen datos idénticos. Cada bloque que se carga y confirma se trata como único, incluso si tiene los mismos datos y el mismo identificador de bloque. Dado que las cargas se incrementan con los bloques únicos, es importante considerar que cuando se actualiza un blob con una instantánea se generarán bloques únicos adicionales y se aplicarán cargos.

> [AZURE.NOTE] Los procesos recomendados sugieren administrar las instantáneas con cuidado para evitar recargos. Se recomienda que administre las instantáneas de la siguiente manera:

> - Elimine y vuelva a crear las instantáneas asociadas a un blob siempre que lo actualice, incluso si lo hace con datos idénticos, a menos que el diseño de la aplicación requiera que se conserven las instantáneas. Si elimina y vuelve a crear las instantáneas del blob, podrá estar seguro de que el blob y las instantáneas no van a divergir.

> - Si está realizando el mantenimiento de las instantáneas de blob, evite llamar a UploadFile, UploadText, UploadStream o UploadByteArray para actualizar el blob, ya que esos métodos reemplazan todos los bloques del blob. En su lugar, actualice el menor número posible de bloques mediante los métodos PutBlock y PutBlockList.


## Situaciones donde se facturan instantáneas


En las siguientes situaciones, se muestra cómo se ven incrementados los cargos para un blob en bloques y sus instantáneas. 

En el escenario 1, el blob de base no se ha actualizado desde que se realizó la instantánea, así que se aplicarán cargos únicamente para los bloques 1, 2 y 3.

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-1.png)

En el escenario 2, el blob de base está actualizado, pero no así la instantánea. Se ha actualizado el bloque 3 y, aunque contiene los mismos datos y el mismo identificador, no es igual que el bloque 3 de la instantánea. Como resultado, se aplicarán cargos a la cuenta por cuatro bloques.

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-2.png)

En el escenario 3, el blob de base está actualizado, pero no así la instantánea. El bloque 3 se ha reemplazado por el bloque 4 en el blob de base, pero la instantánea sigue reflejando el bloque 3. Como resultado, se aplicarán cargos a la cuenta por cuatro bloques.
 
![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-3.png)

En la situación 4, el blob de base se ha actualizado totalmente y no contiene ninguno de los bloques originales. Como resultado, se aplicarán cargos a la cuenta por la totalidad de los ocho bloques únicos. Esta situación se puede dar si usa un método de actualización como UploadFile, UploadText, UploadFromStream o UploadByteArray, ya que estos métodos reemplazan todos los contenidos de un blob.

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-4.png)
<!--HONumber=47-->
