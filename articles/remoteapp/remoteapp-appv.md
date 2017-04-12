---
title: Uso de aplicaciones de App-V con Azure RemoteApp| Microsoft Docs
description: "Obtenga información sobre cómo usar aplicaciones de App-V en Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: e2292cb2-5c89-4b2b-ab11-74dbacd07c31
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 0e82c639ea7bfceb89f0cf9c59ab657a951f2d5f
ms.lasthandoff: 03/31/2017


---
# <a name="using-app-v-apps-in-azure-remoteapp"></a>Uso de aplicaciones App-V en RemoteApp de Azure
> [!IMPORTANT]
> Azure RemoteApp dejará de estar disponible el 31 de agosto de 2017. Para obtener más información, lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Puede usar aplicaciones App-V en una colección híbrida de Azure RemoteApp, que requiera unión a dominio.

Antes de empezar, asegúrese de instalar el cliente de App-V 5.1 con las actualizaciones más recientes. Deberá crear una [imagen personalizada](remoteapp-create-custom-image.md) que incluya el cliente de App-V.  

Es fácil de usar su infraestructura de App-V existente con Azure RemoteApp. Puesto que se implementa una colección híbrida en una red virtual de Azure que tiene acceso a su controlador de dominio y las máquinas virtuales están unidas a un dominio, puede aprovechar sus métodos de infraestructura e implementación de App-v existentes para hospedar fácilmente la aplicación de App-V en Azure RemoteApp. Estas son algunas consideraciones que deben tenerse en cuenta según el tipo de implementación de App-V que tiene actualmente:

| Opciones de configuración |  | Positive | Negative |
| --- | --- | --- | --- |
| Método de entrega |Streaming (a petición) |La aplicación siempre es la más reciente y actualizada. |Primera latencia |
| Montado |Más rápido; la aplicación ya está presente en la máquina virtual. |Sobredimensionamiento: se usa el espacio de la imagen (límite de 127 GB) | |
| Almacenamiento de ubicación de aplicaciones |Contenido compartido |La aplicación se ejecuta en la memoria de la instancia de RemoteApp de Azure. |Consume memoria y una buena conexión al servidor (archivo) de streaming en el que se encuentra la aplicación. |
| Disco (en caché) |Ejecución rápida La aplicación no depende de la disponibilidad del origen del contenido. |Sobredimensionamiento: se usa el espacio de la imagen (límite de 127 GB) | |
| Destinatarios |Usuario |Requiere la infraestructura de App-V independiente completa. | |
| Global (equipo) |Publique previamente o seleccione el destino mediante el servidor de publicación |Debe actualizar la imagen de Azure si desea actualizar la aplicación (enorme). Ocupa espacio en la imagen. | |

 Después de crear su imagen personalizada y colección híbrida, publique su aplicación, asigne usuarios y disfrute de sus aplicaciones de App-V existentes hospedadas en Azure RemoteApp entregadas a cualquier dispositivo en cualquier lugar.


