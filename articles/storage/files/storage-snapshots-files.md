---
title: "Descripción general de una instantánea de recurso compartido de Azure Files (versión preliminar) | Microsoft Docs"
description: "Las instantáneas de recurso compartido de Azure Files son una versión de solo lectura de un recurso compartido de Azure Files que se usa en un momento dado. Una vez se crea la instantánea de recurso compartido, puede leerla, copiarla o eliminarla, pero no modificarla. Las instantáneas de recurso compartido le ofrecen una oportunidad de realizar una copia de seguridad del recurso compartido en el momento en que este aparezca."
services: storage
documentationcenter: .net
author: renash
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: renash
ms.openlocfilehash: 70b7fec318b527c6c67bd281892c10814712bbc6
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-files-share-snapshot-preview-overview"></a>Descripción general de una instantánea de recurso compartido de Azure Files (versión preliminar)
Azure Files proporciona la funcionalidad de tomar instantáneas de recurso compartido de recursos compartidos de archivos. Las instantáneas de recursos compartidos (versión preliminar) capturan el estado del recurso compartido en ese momento dado. En este artículo, se describen qué funcionalidades de instantáneas de recurso compartido se proporcionan y cómo se puede aprovechar de ellas en el caso de uso personalizado.


## <a name="when-to-use-share-snapshots"></a>Cuándo usar instantáneas de recursos compartidos

### <a name="protection-against-application-error-and-data-corruption"></a>Error de protección frente a la aplicación y datos dañados
-------------------------------------------------------------------------

Las aplicaciones que usan recursos compartidos de Azure File realizan varias operaciones, como la escritura, lectura, almacenamiento, transmisión o procesamiento. Una aplicación puede desconfigurarse o puede producirse un error que provoque una sobrescritura accidental o daños en algunos bloques. Para protegerse frente a estos escenarios, puede tomar una instantánea de recurso compartido antes de implementar el nuevo código de aplicación y si se produce un problema o un error de aplicación con la nueva implementación, puede volver a la versión anterior de los datos que se encuentran en ese recurso compartido de archivos. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Protección frente a eliminaciones accidentales y cambios no intencionados
----------------------------------------------------------------------------------------------

Imagine que un usuario trabaja en un archivo de texto que se encuentra en un recurso compartido de archivos. Una vez que se cierra el archivo de texto, perdemos la capacidad de deshacer los cambios. En estos casos, necesitaremos recuperar una versión anterior del archivo. Las instantáneas de recursos compartidos le permiten recuperar versiones anteriores del archivo si se cambia el nombre o se elimina accidentalmente.

### <a name="general-backup-purposes"></a>Propósitos generales de copia de seguridad
---------------------------

Después de crear un recurso compartido de Azure File, puede crear periódicamente una instantánea de recurso compartido del recurso compartido de archivos de Azure para usarla para la copia de seguridad de datos. Compartir periódicamente la instantánea cuando se realiza ayuda a conservar las versiones anteriores de datos que pueden usarse para el requisito de auditoría futuro y la recuperación ante desastres.

## <a name="share-snapshot-capabilities"></a>Funcionalidades de instantánea de recurso compartido

La instantánea de recurso compartido es una copia de solo lectura a un momento dado de sus datos. Puede crear, eliminar y administrar instantáneas mediante la API de REST. Las mismas capacidades están también disponibles en la Biblioteca cliente, la CLI y Azure Portal. La integración de PowerShell para la instantánea de recurso compartido estará disponible en breve. Puede ver las instantáneas de un recurso compartido con la API de REST y SMB. Los clientes pueden recuperar la lista de versiones del directorio o archivo y también pueden montar una versión específica directamente como unidad. Una vez se crea la instantánea de recurso compartido, puede leerla, copiarla o eliminarla, pero no modificarla. No puede copiar una instantánea de recurso compartido completa en otra cuenta de almacenamiento. Tendrá que hacerlo archivo por archivo con azcopy u otros mecanismos de copia.

La funcionalidad de instantánea de recurso compartido se proporciona a nivel de recurso compartido de archivos, mientras que la recuperación se ofrece en el nivel de archivos individual para permitir la restauración de archivos individuales. Puede restaurar un recurso compartido de archivo completo mediante SMB, API de REST, el portal, Biblioteca cliente, o usar herramientas de PowerShell/CLI.

Una instantánea de recurso compartido de un recurso compartido de archivo es idéntica a su recurso compartido de archivo de base, excepto en que el URI del recurso compartido tiene un valor **DateTime** anexado al URI de recurso compartido para indicar la hora en la que se realizó la instantánea de recurso compartido. Por ejemplo, si un URI de recurso compartido de archivos es http://storagesample.core.file.windows.net/myshare, el URI de instantánea de recurso compartido es similar a
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z.
```

Las instantáneas de recurso compartido se conservan hasta que se eliminan explícitamente. Una instantánea de recurso compartido no puede durar más que su recurso compartido de archivos base. Puede enumerar las instantáneas asociadas al recurso compartido de archivos base para llevar a cabo un seguimiento de las instantáneas actuales. Cuando cree una instantánea de recurso compartido de un recurso compartido de archivos, los archivos que se encuentran en las propiedades del sistema del recurso compartido se copian a la instantánea de recurso compartido con los mismos valores. Los metadatos del recurso compartido de archivos y los archivos base también se copian en la instantánea de recursos compartidos, a menos que especifique metadatos independientes para la instantánea de recurso compartido al crearla.

No puede eliminar un recurso compartido que disponga de instantáneas de recurso compartido sin eliminar todas sus instantáneas de recurso compartido primero.


## <a name="share-snapshot-space-usage"></a>Uso de espacio de instantánea de recurso compartido 

Las instantáneas de recurso compartido son incrementales en naturaleza, lo que significa que solo se guardan los datos que han cambiado después de la instantánea de recurso compartido más reciente. Esto minimiza el tiempo necesario para crear la instantánea de recurso compartido y ahorra en costos de almacenamiento. Cualquier operación de escritura en el objeto o propiedad u operación de actualización de metadatos se cuenta para el "contenido cambiado" y se guardará en la instantánea de recurso compartido. 

Para ahorrar espacio, puede eliminar la instantánea de recurso compartido durante el período en el que la renovación se encuentra en su punto más alto.

Incluso si las instantáneas de recurso compartido se guardan de forma incremental, el proceso de eliminación de la instantánea de recurso compartido se ha diseñado de manera que tiene que conservar solo la instantánea de recurso compartido más reciente para restaurar el recurso compartido. Cuando elimine una instantánea de recurso compartido, solo se quitan los datos exclusivos para esa instantánea de recurso compartido. Las instantáneas activas contienen toda la información necesaria para examinar y restaurar sus datos (desde el momento en el que se realizó la instantánea de recurso compartido) a la ubicación original o alternativa. La restauración puede realizarse a nivel de elemento.

Las instantáneas no se cuentan para el límite de recurso compartido de 5 TB. No hay ningún límite de espacio total ocupado por una instantánea de recurso compartido. Los límites de cuenta de almacenamiento se siguen aplicando.

## <a name="limits"></a>límites

El número máximo de instantáneas de recurso compartido que permite Azure Files actualmente es de 200. Después de 200 instantáneas de recurso compartido, el usuario tendrá que eliminar las instantáneas de recurso compartido más antiguas para crear nuevas instantáneas de recurso compartido. No hay ningún límite en las llamadas simultáneas para crear la instantánea de recurso compartido.
No hay ningún límite en la cantidad de espacio que las instantáneas de recurso compartido de un recurso compartido de archivos determinado puede consumir. 

## <a name="copy-data-back-to-a-share-from-share-snapshot"></a>Copia de nuevo de un recurso compartido desde una instantánea de recurso compartido

Las operaciones de copia que implican archivos e instantáneas de recurso compartido siguen estas reglas:

Puede copiar archivos individuales en una instantánea de recurso compartido de archivos por encima de su recurso compartido base o en cualquier otra ubicación. Puede restaurar una versión anterior de un archivo o restaurar un recurso compartido de archivos completo copiando archivo por archivo desde la instantánea de recurso compartido. La instantánea de recurso compartido no se promociona al recurso compartido base. La instantánea de recurso compartido mantiene el copiado posterior intacto, pero el recurso compartido de archivos base se sobrescribe con una copia de los datos que estaban disponibles en la instantánea de recurso compartido. Todos los archivos restaurados cuentan para el "contenido cambiado".

Puede copiar un archivo en una instantánea de recurso compartido en un destino con un nombre distinto. El archivo de destino resultante es un archivo en el que se puede escribir y no una instantánea de recurso compartido.

Cuando un archivo de destino se sobrescribe con una copia, las instantáneas de recurso compartido asociadas al archivo de destino original no se modifican.

## <a name="general-best-practices"></a>Procedimientos recomendados generales 

Cuando se ejecuta una infraestructura en Azure, automatice las copias de seguridad para la recuperación de datos cuando sea posible. Las acciones automatizadas son más fiables que los procesos manuales, lo que le ayuda a mejorar la recuperabilidad y la protección de datos. Puede usar la API de REST, el SDK de cliente o scripting de automatización.

Tenga en cuenta la configuración de retención y frecuencia de instantáneas de recurso compartido antes de implementar el programador de instantáneas de recurso compartido para evitar gastos de instantáneas de recurso compartido innecesarios.

Las instantáneas de recurso compartido proporcionan solo una protección de nivel de archivo y las eliminaciones no intencionadas en el recurso compartido de archivos o la cuenta de almacenamiento no se protegen por instantáneas de recurso de compartido. Debe bloquear la cuenta de almacenamiento o grupo de recursos para proteger la cuenta de almacenamiento de eliminaciones accidentales.

## <a name="next-steps"></a>Pasos siguientes
* [Work with Azure Files share snapshot](storage-how-to-use-files-snapshots.md) (Funcionamiento de instantánea de recursos compartidos de Azure Files)
* [Preguntas más frecuentes sobre instantáneas de recurso compartido](storage-files-faq.md)

