---
title: "Notas de la versión del SDK de iOS de Azure Mobile Engagement | Microsoft Docs"
description: "Actualizaciones y procedimientos más recientes para el SDK de iOS para Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a43ff0f6-90d5-4b3c-8d7a-a1db21bc776b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 9bdaa57f9902373ccf796ff109332b64c66bf9e7
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Notas de la versión del SDK de iOS de Azure Mobile Engagement

## <a name="410-07172017"></a>4.1.0 (07/17/2017)
* Se han corregido las notificaciones borradas en el fondo.
* Se han corregido las advertencias de XCode 9 sobre las API a las que no se llamaba en cola principal.
* Se ha corregido una fuga de memoria en los sondeos de cobertura.
* Se ha eliminado el soporte técnico para iOS 6.X. A partir de esta versión, el destino de implementación de la aplicación tiene que ser como mínimo iOS 7.

## <a name="401-12132016"></a>4.0.1 (12/13/2016)
* Entrega de registros en segundo plano mejorada

## <a name="400-09122016"></a>4.0.0 (09/12/2016)
* Notificación fija no ejecutada en dispositivos iOS 10.
* XCode 7 en desuso.

## <a name="324-06302016"></a>3.2.4 (30/06/2016)
* Agregación fija entre registros técnicos y otros registros.

## <a name="323-06072016"></a>3.2.3 (06/07/2016)
* Se corrigió el error donde los comentarios de entrega no se notifican cuando la aplicación funciona en segundo plano.
* Se ha optimizado el envío de registros técnicos.

## <a name="322-04072016"></a>3.2.2 (04/07/2016)
* Se ha corregido el error en la cancelación de solicitud HTTP que provocaba a veces bloqueos.

## <a name="321-12112015"></a>3.2.1 (12/11/2015)
* Se ha corregido el retraso que se produce cuando se desencadena una nueva instancia de aplicación por una notificación con vínculos profundos.

## <a name="320-10082015"></a>3.2.0 (10/08/2015)
* Se habilitó Bitcode en el SDK para que funcione con **Xcode 7**.
* Se corrigieron los errores relacionados con las notificaciones desde la aplicación.
* Las notificaciones desde la aplicación ahora son más fiables en caso de que, por ejemplo, la batería esté baja.
* Se quitaron registros de la consola adicionales generados por la biblioteca de terceros.

## <a name="310-08262015"></a>3.1.0 (08/26/2015)
* Corrija los errores de compatibilidad de iOS 9 con una biblioteca de terceros. Generaban bloqueos al enviar resultados de sondeo, información de la aplicación o datos adicionales.

## <a name="300-06192015"></a>3.0.0 (19/06/2015)
* Mobile Engagement usa notificaciones push silenciosas.
* Soporte de iOS 4.X eliminado. A partir de esta versión, el destino de implementación de la aplicación debe ser como mínimo iOS 6.

## <a name="220-05212015"></a>2.2.0 (05/21/2015)
* El identificador de dispositivo de Mobile Engagement para dispositivos anteriores a iOS 6 ahora se basa en un GUID que se genera durante la instalación.

## <a name="210-04242015"></a>2.1.0 (24/04/2015)
* Agregada compatibilidad con Swift.
* Al hacer clic en una notificación, la dirección URL de la acción se ejecuta ahora justo después de abrir la aplicación.
* Se ha agregado el archivo de encabezado que faltaba en el paquete del SDK.
* Se ha corregido un problema cuando se deshabilitaba el generador de informes de error de Mobile Engagement.

## <a name="200-02172015"></a>2.0.0 (02/17/2015)
* Versión inicial de Azure Mobile Engagement
* La configuración de appId o sdkKey se sustituye por una configuración de la cadena de conexión.
* Se ha eliminado la API para enviar y recibir mensajes XMPP arbitrarios de entidades XMPP arbitrarias.
* Se ha eliminado la API para enviar y recibir mensajes entre dispositivos.
* Mejoras de seguridad.
* Se ha eliminado el seguimiento de SmartAd.
