---
title: "Nunca almacene datos confidenciales en imágenes personalizadas para Azure RemoteApp | Microsoft Docs"
description: "Obtenga información acerca de las directrices para almacenar datos en imágenes personalizadas en Azure RemoteApp"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 5a19903b-15f9-49d9-9bc1-ae80f2671aa1
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 75d5415d33324d957617426e75909a6c6c58b1f9
ms.lasthandoff: 03/31/2017


---
# <a name="never-store-sensitive-data-on-custom-images"></a>Nunca almacene información confidencial en imágenes personalizadas
> [!IMPORTANT]
> Azure RemoteApp dejará de estar disponible el 31 de agosto de 2017. Para obtener más información, lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Cuando hospede su propia aplicación en Azure RemoteApp, el primer paso es crear una imagen personalizada. Usamos esa imagen personalizada para crear instancias de máquina virtual que sirvan las aplicaciones a los usuarios. La imagen personalizada debe contener SOLO aplicaciones y nunca datos confidenciales que se puedan perder, como bases de datos SQL, archivos de personal o archivos de datos especiales como archivos de empresa de QuickBooks. Todos los datos confidenciales deben ser externos a Azure RemoteApp en un servidor de archivos, otra máquina virtual de Azure o SQL Azure. La imagen solo debe hospedar la aplicación que se conecta al origen de datos y presenta los datos. Consulte [Requisitos para las imágenes Azure RemoteApp](remoteapp-imagereqs.md) para obtener más información. 

Para entender por qué no debe almacenar datos confidenciales, debe entender cómo funciona Azure RemoteApp. Cuando se crea o actualiza una colección, se crean varios clones o copias de la imagen entre bambalinas. Todas estas instancias de máquina virtual son réplicas exactas de la imagen personalizada. Cuando los usuarios inician aplicaciones, se conectan a una de estas instancias de máquina virtual. Sin embargo, no se garantiza la misma instancia y no debe importar porque son no persistentes. Las instancias de máquina virtual que hospedan las aplicaciones no son persistentes y pueden destruirse o eliminarse, por ejemplo, durante la actualización de la colección. 

Una vez que se aprovisiona la colección y los usuarios empiecen a conectarse a las máquinas virtuales, los datos de usuario son persistentes y se protegen porque se guardan en un almacenamiento independiente dentro de un VHD que llamamos un [disco de perfil de usuario (UPD)](remoteapp-upd.md), que es el perfil de usuario en c:\users\<perfilusuario>. Cuando se inicia una aplicación, el UPD se monta y se tratan como un perfil de usuario local por el sistema operativo. Obtenga más información sobre cómo [Azure RemoteApp guarda la configuración y los datos de usuario](remoteapp-upd.md).

Datos de ejemplo que no deben residir en la imagen:

* Datos compartidos de los usuarios para obtener acceso a
* Base de datos SQL o Base de datos QuickBooks
* Todos los datos en D:\

Los datos de ejemplo que pueden residir en el perfil predeterminado que se copiará en cada UPD de todos los usuarios:

* Archivos de configuración por usuario
* Scripts que los usuarios tendrían que conservar en el UPD

Puntos clave:

* Nunca almacene datos confidenciales que se pueden perder en la imagen cuando cree una imagen personalizada.
* Los datos confidenciales siempre deben residir en un servidor de archivos independiente, una máquina virtual de Azure independiente, en la nube y siempre deben ser externos a las instancias de máquina virtual que hospedan las aplicaciones en Azure RemoteApp. 
* Los datos de usuario se guardan y se conserva en el disco de perfil de usuario (UDP)


