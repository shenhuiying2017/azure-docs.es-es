---
title: "Importación y exportación de datos en Azure Redis Cache | Microsoft Docs"
description: "Obtenga información sobre cómo importar datos a Blob Storage y exportar datos desde este servicio con las instancias de Azure Redis Cache de nivel Premium."
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 4a68ac38-87af-4075-adab-569d37d7cc9e
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: wesmc
ms.openlocfilehash: db488f759752880a47a78dfeec13b14f65bd503c
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="import-and-export-data-in-azure-redis-cache"></a>Importación y exportación de datos en Azure Redis Cache
Import/Export es una operación de administración de datos de Azure Redis Cache que permite realizar operaciones de importación o exportación con Azure Redis Cache mediante la importación y exportación de una instantánea de base de datos de Redis Cache (RDB) desde una caché premium a un blob en una cuenta de Azure Storage. 

- **Exportar**: Puede exportar las instantáneas de RDB de Azure Redis Cache a un blob en páginas.
- **Importar**: Puede importar las instantáneas de RDB de Redis Cache desde de un blob en páginas o un blob en bloques.

Este método le permite migrar entre diferentes instancias de Azure Redis Cache o rellenar la caché de datos antes de su uso.

En este artículo se proporciona una guía para importar y exportar datos con Azure Redis Cache y se responden las preguntas más frecuentes.

> [!IMPORTANT]
> Importación/Exportación es una característica en versión preliminar y solo está disponible para las cachés de [nivel premium](cache-premium-tier-intro.md) .
>
>

## <a name="import"></a>Importar
La importación se puede usar para traer los archivos RDB compatibles de Redis desde cualquier servidor de Redis que se ejecute en cualquier nube o entorno, incluidas las instancias de Redis que se ejecutan en Linux, Windows o cualquier proveedor de nube como, por ejemplo, Amazon Web Services. La importación de datos supone una manera fácil de crear una caché con datos rellenados previamente. Durante el proceso de importación, Azure Redis Cache carga los archivos RDB desde Azure Storage en la memoria y, a continuación, inserta las claves en la memoria caché.

> [!NOTE]
> Antes de comenzar la operación de importación, asegúrese de que los archivos de base de datos de Redis (RDB) se cargan en los blobs en páginas o en bloques de Azure Storage, en la misma región y suscripción que la instancia de Azure Redis Cache. Para obtener información, vea [Introducción al Almacenamiento de blobs de Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Si exportó el archivo RDB mediante la característica [Azure Redis Cache Export](#export) , el archivo RDB ya estará almacenado en un blob en páginas y listo para la importación.
>
>

1. Para importar uno o varios blobs de memoria caché exportados, [vaya a la memoria caché](cache-configure.md#configure-redis-cache-settings) en Azure Portal y haga clic en **Importar datos** desde el menú **Recurso**.

    ![Importar datos][cache-import-data]
2. Haga clic en **Elegir blobs** y seleccione la cuenta de almacenamiento que contiene los datos que desea importar.

    ![Selección de la cuenta de almacenamiento][cache-import-choose-storage-account]
3. Haga clic en el contenedor que contiene los datos que desea importar.

    ![Elegir contenedor][cache-import-choose-container]
4. Seleccione uno o varios blobs para importar; para ello, haga clic en el área a la izquierda del nombre del blob y, a continuación, haga clic en **Seleccionar**.

    ![Elegir blobs][cache-import-choose-blobs]
5. Haga clic en **Importar** para comenzar el proceso de importación.

   > [!IMPORTANT]
   > Los clientes de la memoria caché no pueden tener acceso a esta durante el proceso de importación, y los datos existentes en la memoria caché se eliminan.
   >
   >

    ![Importar][cache-import-blobs]

    Puede supervisar el progreso de la operación de importación siguiendo las notificaciones de Azure Portal o viendo los eventos en el [registro de auditoría](../azure-resource-manager/resource-group-audit.md).

    ![Progreso de la importación][cache-import-data-import-complete]

## <a name="export"></a>Exportación
La exportación permite exportar los datos almacenados en Azure Redis Cache a archivos RDB compatibles. Puede utilizar esta característica para mover datos desde una instancia de Azure Redis Cache a otra o a otro servidor de Redis. Durante el proceso de exportación, se crea un archivo temporal en la máquina virtual que hospeda la instancia del servidor de Azure Redis Cache y el archivo se carga en la cuenta de almacenamiento designada. Una vez completada la operación de exportación (de manera correcta o incorrecta), se elimina el archivo temporal.

1. Para exportar el contenido actual de la memoria caché al almacenamiento, [vaya a la memoria caché](cache-configure.md#configure-redis-cache-settings) de Azure Portal y haga clic en **Exportar datos** desde el menú **Recurso**.

    ![Elegir el contenedor de almacenamiento][cache-export-data-choose-storage-container]
2. Haga clic en **Elegir el contenedor de almacenamiento** y seleccione la cuenta de almacenamiento. La cuenta de almacenamiento debe encontrarse en la misma región y suscripción que la memoria caché.

   > [!IMPORTANT]
   > La exportación funciona con blobs en páginas, que son compatibles con las cuentas de almacenamiento del modelo clásico y de Resource Manager, aunque en este momento no lo son con las [cuentas de Blob Storage](../storage/common/storage-account-options.md#blob-storage-accounts).
   >
   >

    ![Cuenta de almacenamiento][cache-export-data-choose-account]
3. Elija el contenedor de blobs deseado y haga clic en **Seleccionar**. Para utilizar un contenedor nuevo, haga clic en **Agregar contenedor** para agregarlo primero y, a continuación, selecciónelo en la lista.

    ![Elegir el contenedor de almacenamiento][cache-export-data-container]
4. Escriba el **Prefijo del nombre del blob** y haga clic en **Exportar** para iniciar el proceso de exportación. El prefijo del nombre del blob se utiliza como prefijo para los nombres de los archivos generados por esta operación de exportación.

    ![Exportación][cache-export-data]

    Puede supervisar el progreso de la operación de exportación siguiendo las notificaciones de Azure Portal o viendo los eventos en el [registro de auditoría](../azure-resource-manager/resource-group-audit.md).

    ![Exportación de datos completa][cache-export-data-export-complete]

    Las memorias caché permanecen disponibles para su uso durante el proceso de exportación.

## <a name="importexport-faq"></a>P+F de Import/Export
Esta sección contiene las preguntas más frecuentes acerca de la característica Importación/Exportación.

* [¿Con qué planes de tarifa se puede usar Importación/Exportación?](#what-pricing-tiers-can-use-importexport)
* [¿Puedo importar datos desde cualquier servidor de Redis?](#can-i-import-data-from-any-redis-server)
* [¿Qué versiones de RDB puedo importar?](#what-rdb-versions-can-i-import)
* [¿La memoria caché estará disponible durante una operación de Import/Export?](#is-my-cache-available-during-an-importexport-operation)
* [¿Puedo utilizar Import/Export con un clúster de Redis?](#can-i-use-importexport-with-redis-cluster)
* [¿Cómo funciona la importación y exportación con una configuración de bases de datos personalizada?](#how-does-importexport-work-with-a-custom-databases-setting)
* [¿En qué se diferencia Importación/Exportación de la persistencia de Redis?](#how-is-importexport-different-from-redis-persistence)
* [¿Puedo automatizar Importación/Exportación mediante PowerShell, la CLI u otros clientes de administración?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [He recibido un error de tiempo de espera durante la operación de importación/exportación. ¿Qué significa esto?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Aparece un error al exportar los datos a Azure Blob Storage. ¿Qué ha ocurrido?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>¿Con qué planes de tarifa se puede usar Importación/Exportación?
Importación/Exportación solo está disponible en el plan de tarifa Premium.

### <a name="can-i-import-data-from-any-redis-server"></a>¿Puedo importar datos desde cualquier servidor de Redis?
Sí, además de importar los datos exportados desde instancias de Azure Redis Cache, puede importar archivos RDB desde cualquier servidor de Redis que se ejecute en cualquier nube o entorno, como Linux, Windows, o proveedores en la nube (como Amazon Web Services). Para ello, cargue el archivo RDB desde el servidor de Redis deseado en un blob en páginas o en bloques en una cuenta de Azure Storage y, luego, impórtelo en la instancia de Azure Redis Cache de nivel Premium. Por ejemplo, quizá desee exportar los datos desde la caché de producción e importarlos en una caché que se utiliza como parte de un entorno de ensayo con fines de prueba o migración.

> [!IMPORTANT]
> Para poder importar correctamente los datos exportados desde servidores de Redis distintos a Azure Redis Cache cuando se usa un blob en páginas, el tamaño de dicho blob debe ajustarse al límite de 512 bytes. Para ver un ejemplo para realizar cualquier relleno de bytes necesario, consulte [Sample page blog upload](https://github.com/JimRoberts-MS/SamplePageBlobUpload) (Carga de blog en páginas de ejemplo).
> 
> 

### <a name="what-rdb-versions-can-i-import"></a>¿Qué versiones de RDB puedo importar?

Azure Redis Cache admite la importación RDB hasta la versión 7 de RDB.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>¿La memoria caché estará disponible durante una operación de Import/Export?
* **Exportación** : las memorias caché permanecen disponibles y puede seguir usándolas durante una operación de exportación.
* **Importación** : las memorias caché dejan de estar disponibles cuando se inicia una operación de importación y vuelven a estar disponibles para su uso cuando la operación de importación finaliza.

### <a name="can-i-use-importexport-with-redis-cluster"></a>¿Puedo utilizar Import/Export con un clúster de Redis?
Sí, y puede importar/exportar entre una caché en clústeres y una caché que no esté en clústeres. Puesto que el clúster de Redis [solo admite la base de datos 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), solo se importarán los datos de la base de datos 0. Cuando se importan datos de caché en clúster, las claves se redistribuyen entre las particiones del clúster.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>¿Cómo funciona la importación y exportación con una configuración de bases de datos personalizada?
Algunos planes de tarifa tienen diferentes [límites de bases de datos](cache-configure.md#databases), por lo que hay algunas consideraciones al importar si ha configurado un valor personalizado para el parámetro `databases` al crear la memoria caché.

* Al importar a un plan de tarifa con un límite de `databases` menor que el nivel desde el que exportó:
  * Si utiliza el número predeterminado de `databases` , que es 16 para todos los planes de tarifa, no se pierden datos.
  * Si utiliza un número personalizado de `databases` que está dentro de los límites para el plan al que va a importar, no se pierden datos.
  * Si los datos exportados contenían datos en una base de datos que supera los límites del nuevo plan, no se importan los datos de esas bases de datos superiores.

### <a name="how-is-importexport-different-from-redis-persistence"></a>¿En qué se diferencia Import/Export de la persistencia de Redis?
Azure Redis Cache permite mantener datos almacenados en Redis en Azure Storage. Cuando se configura la persistencia, Azure Redis Cache conserva una instantánea de la memoria caché de Redis en un formato binario de Redis en el disco según una frecuencia de copia de seguridad configurable. Si se produce un evento catastrófico que deshabilita tanto la caché de réplica como la principal, los datos de la caché se restauran automáticamente con la instantánea más reciente. Para más información, consulte [Cómo configurar la persistencia de datos para una Azure Redis Cache Premium](cache-how-to-premium-persistence.md).

Import/Export permite traer datos a Azure Redis Cache o exportar desde este servicio. No configura la copia de seguridad y restauración mediante la persistencia de Redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>¿Puedo automatizar Importación/Exportación mediante PowerShell, la CLI u otros clientes de administración?
Sí. Para obtener más instrucciones de PowerShell vea las secciones [Importación de una caché en Redis](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) y [Exportación de una caché en Redis](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>He recibido un error de tiempo de espera durante la operación de Import/Export. ¿Qué significa?
Si permanece en la hoja **Importar datos** o **Exportar datos** durante más de 15 minutos antes de iniciar la operación, recibirá un error similar al siguiente ejemplo:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Para solucionarlo, inicie la operación de importación o exportación antes de que transcurran 15 minutos.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Aparece un error al exportar los datos a Azure Blob Storage. ¿Qué ha ocurrido?
La exportación solo funciona con archivos RDB almacenados como blobs en páginas. No se admiten otros tipos de blob en este momento, incluidas las cuentas de Blob Storage con niveles de acceso frecuente y esporádico. Para más información, consulte [Cuentas de Blob Storage](../storage/common/storage-account-options.md#blob-storage-accounts).

## <a name="next-steps"></a>pasos siguientes
Obtenga información acerca de cómo usar más características de la memoria caché del nivel Premium.

* [Introducción al nivel Premium de Azure Redis Cache](cache-premium-tier-intro.md)    

<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png
