---
title: "Actualización de la colección Azure RemoteApp | Microsoft Docs"
description: "Más información sobre la colección Azure RemoteApp"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: e553d432-e581-48fe-b996-c432357eb64a
ms.service: remoteapp
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: compute
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 41c8a13ebd008ed4f9d8a5399bf8e272bf0fd7b2
ms.lasthandoff: 03/31/2017


---
# <a name="update-a-collection-in-azure-remoteapp"></a>Actualización de una colección en Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp dejará de estar disponible el 31 de agosto de 2017. Para obtener más información, lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Llegará un momento, inevitablemente, en que tendrá que actualizar las aplicaciones o la imagen de la colección Azure RemoteApp. Si usa una de las imágenes incluidas con la suscripción de Azure RemoteApp, ya sea en una colección híbrida o en la nube, la administración de todas y cada una de las actualizaciones la realiza la propia Azure RemoteApp, por lo que puede estar tranquilo.

Sin embargo, si usa una imagen personalizada (creada desde cero o modificando una de nuestras imágenes), es responsable del mantenimiento de la imagen y las aplicaciones. Si necesita actualizar la imagen o cualquiera de las aplicaciones de dicha colección, tendrá que crear una nueva versión actualizada de la imagen y, luego, reemplazar la de la colección por esta nueva imagen actualizada.

Por lo tanto, ¿cómo tiene que proceder para actualizar la colección? Es bastante sencillo:

1. Actualice la imagen que usó en la colección. Aplique las revisiones o actualizaciones necesarias y guárdela con un otro nombre.
2. Realice la [carga](remoteapp-uploadimage.md) o la [importación](remoteapp-image-on-azurevm.md) de la imagen en RemoteApp.
3. Ahora, en la página de la colección, haga clic en **Actualizar**.
4. Elija la nueva imagen de la lista de **imágenes de plantilla** .
5. Aquí llega la parte complicada, ha de decidir cómo tratar a los usuarios que usan actualmente una aplicación de la colección. Tiene las siguientes opciones:
   
   * **Darle a los usuarios un margen de 60 minutos tras la actualización**. Tan pronto como finalice la actualización, Azure RemoteApp mostrará un mensaje a los usuarios activos en el que se les indica que guarden el trabajo, cierren la sesión y vuelvan a iniciarla. Transcurridos los 60 minutos, se cerrará automáticamente la sesión de todos los usuarios activos que no la cerraron. Los usuarios pueden volver a iniciar sesión inmediatamente.
   * **Cerrar inmediatamente la sesión de los usuarios**. En cuanto finalice la actualización, cierre automáticamente la sesión de todos los usuarios sin avisarles. Si elige esta opción, los usuarios pueden perder datos. Sin embargo, pueden volver a conectarse a la aplicación inmediatamente.
6. Haga clic en la marca de verificación para iniciar la actualización.


