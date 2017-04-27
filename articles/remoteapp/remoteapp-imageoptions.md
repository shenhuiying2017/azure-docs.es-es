---
title: "Creación de una imagen de Azure RemoteApp | Microsoft Docs"
description: "Obtenga información acerca de las opciones disponibles para la creación de imágenes para Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: cb0f9424-6185-45a1-abe9-c23f1edf34f2
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: f2decec5385ffab59a441cdc28da80371b579df7
ms.lasthandoff: 03/31/2017


---
# <a name="create-an-azure-remoteapp-image"></a>Creación de una imagen de Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp dejará de estar disponible el 31 de agosto de 2017. Para obtener más información, lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Azure RemoteApp usa imágenes para mantener las aplicaciones que se comparten con los usuarios. (Tomamos su imagen y la usamos para crear máquinas virtuales, que es a lo que acceden los usuarios cuando inician sesión en Azure RemoteApp). Para crear una colección de Azure RemoteApp con las aplicaciones de su elección, ya estén en la nube o sean híbridas, empiece por crear una imagen con esas aplicaciones instaladas. A continuación, cree una colección que utiliza esa imagen, asigne usuarios a la colección y publique aplicaciones para esos usuarios.

Tiene varias opciones para crear o utilizar imágenes. El [requisito](remoteapp-imagereqs.md) básico para una imagen es que ejecute Windows Server 2012 R2 y tenga instalado el rol de host de sesión de Escritorio remoto (RDSH). La forma de conseguir esto es lo que resulta interesante.

Tiene las siguientes opciones cuando se trata de imágenes:

* Puede importar y utilizar una [imagen basada en una máquina virtual de Azure](remoteapp-image-on-azurevm.md). Esto es una buena opción para aplicaciones de línea de negocio que requieren una configuración personalizada. Puede personalizar la imagen para que funcione para la aplicación.
* Puede [crear y cargar una imagen personalizada](remoteapp-create-custom-image.md). Esto es apropiado si ya tiene una imagen que se utiliza para la implementación local de Servicios de Escritorio remoto.
* Puede utilizar una de las [imágenes de plantilla](remoteapp-images.md) incluidas en su suscripción de RemoteApp. Estas imágenes se crean y mantienen por parte del equipo de RemoteApp y contienen algunas aplicaciones estándar (por ejemplo, el conjunto de programas de Office) que puede poner a disposición de los usuarios. Tenga en cuenta que la imagen Office 365 Pro Plus se puede utilizar en una configuración de producción.

Independientemente de dónde obtenga la imagen o de cómo la cree, es importante que entienda los [requisitos de la aplicación](remoteapp-appreqs.md) para garantizar que la aplicación funciona bien en RemoteApp. A continuación, el siguiente paso es crear una colección en la [nube](remoteapp-create-cloud-deployment.md) o [híbrida](remoteapp-create-hybrid-deployment.md).


