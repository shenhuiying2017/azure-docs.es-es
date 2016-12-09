---
title: 'Ancho de banda de red de Azure RemoteApp: directrices generales | Microsoft Docs'
description: "Entender algunas directrices básicas del ancho de banda de red para sus colecciones y aplicaciones de Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 529bf318-ae37-4c14-a11c-43fa703d68a3
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b44fcb6cca30acbd5771a0301f72faa7d9105849


---
# <a name="azure-remoteapp-network-bandwidth---general-guidelines-if-you-cant-test-your-own"></a>Ancho de banda de red de Azure RemoteApp: directrices generales (si no puede probarlo usted mismo)
> [!IMPORTANT]
> Azure RemoteApp va a dejar de estar disponible. Para obtener más información, lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Si no tiene el tiempo ni la posibilidad de ejecutar las [pruebas del ancho de banda de red](remoteapp-bandwidthtests.md) para Azure RemoteApp, le presentamos algunas directrices bastante genéricas que pueden ayudarle a calcular el ancho de banda de red por usuario.

Si tiene una combinación de estos escenarios, no le recomendamos nada que sea inferior a (o igual a) 10 MB/s como ancho de banda de red mínimo para las aplicaciones modernas conectadas a Internet en un entorno remoto. (Aunque, como se explicó anteriormente, esto no garantizará una experiencia de usuario superior a la media).

## <a name="complex-powerpoint-simple-powerpoint"></a>PowerPoint de tipo complejo y de tipo simple
Azure RemoteApp funciona mejor con una red LAN de 100 MB. En el perfil de red de 10 MB/s, cuando la vibración superior a 120 ms representa más de un 5 %, el usuario tendrá una experiencia media. A 1 MB/s, la diferencia es evidente. Por ejemplo, en una presentación con diapositivas, el usuario podría no ver las transiciones animadas completamente ya que se omiten los fotogramas.

## <a name="internet-explorer-mixed-pdf-pdf-text"></a>Internet Explorer, PDF mixto, PDF, texto
Un perfil de red de 10 MB/s se acerca a la red LAN en la mayoría de los aspectos. 1 MB/s proporcionará una experiencia adecuada, aunque puede haber una cierta vibración cuando un usuario se desplaza mientras hay imágenes en la pantalla.

## <a name="flash-video-youtube"></a>Vídeo Flash (YouTube)
Una red LAN de 100 MB/s proporciona la mejor experiencia, aunque 10 MB/s es aceptable (es decir, mantenemos la velocidad de fotogramas, pero aumenta la vibración). A 1 MB/s, la vibración es muy alta y apreciable.

## <a name="word-typing-word-remote-input"></a>Escritura en Word (entrada remota de Word)
Se trata de un escenario de uso de ancho de banda bajo. A 256 KB/s, proporcionamos una experiencia tan buena como la de la red LAN.

## <a name="learn-more"></a>Más información
* [Cálculo aproximado del uso del ancho de banda de red de Azure RemoteApp](remoteapp-bandwidth.md)
* [Azure RemoteApp: ¿cómo funcionan el ancho de banda de red y la calidad de la experiencia conjuntamente?](remoteapp-bandwidthexperience.md)
* [Azure RemoteApp - tseting your network bandwidth usage with some common scenarios (Azure RemoteApp: probar su uso de ancho de banda de red con algunos escenarios comunes)](remoteapp-bandwidthtests.md)




<!--HONumber=Nov16_HO3-->


