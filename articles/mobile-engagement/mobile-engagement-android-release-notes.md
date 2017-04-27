---
title: "Integración del SDK de Android para Azure Mobile Engagement"
description: "Procedimientos y actualizaciones más recientes para el SDK de Android para Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 585341f9-3f39-459a-af42-864e400a0128
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 03/30/2017
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: e50c1c70a4378560f8ce3182069cba4832fa4d22
ms.lasthandoff: 03/31/2017


---
# <a name="release-notes"></a>Notas de la versión

## <a name="424-03302017"></a>4.2.4 (30/03/2017)
* Se han corregido los colores del texto de notificación en aplicación de Android 7 para que coincidan con los de las versiones anteriores de Android.

## <a name="423-08102016"></a>4.2.3 (08/10/2016)
* No se producirán más bloqueos de WiFi.
* Se ha corregido un interbloqueo al llamar a getDeviceId antes de init (error de la versión 4.2.0).

## <a name="422-05172016"></a>4.2.2 (17/05/2016)
* Mejoras de estabilidad.

## <a name="421-05102016"></a>4.2.1 (10/05/2016)
* Seguridad: deshabilite el acceso a archivos locales de la vista web.
* Seguridad: quite la clase `EngagementPreferenceActivity` que extiende la clase `PreferenceActivity` no segura y obsoleta.
* Seguridad: las actividades de cobertura están ahora documentadas para usar `exported="false"`, esta marca puede usarse también en versiones del SDK anteriores.

## <a name="420-03112016"></a>4.2.0 (03/11/2016)
* El SDK tiene una licencia de MIT.
* Permite especificar un identificador de dispositivo personalizado en el tiempo de inicialización del SDK.

## <a name="415-02012016"></a>4.1.5 (02/01/2016)
* Mejoras de estabilidad.

## <a name="414-01262016"></a>4.1.4 (01/26/2016)
* Mejoras de estabilidad.

## <a name="413-1292015"></a>4.1.3 (9/12/2015)
* Mejoras de estabilidad.

## <a name="412-11252015"></a>4.1.2 (25/11/2015)
* Mejoras de estabilidad.

## <a name="411-11042015"></a>4.1.1 (11/04/2015)
* Mejoras de estabilidad.

## <a name="410-08252015"></a>4.1.0 (08/25/2015)
* Controle el nuevo modelo de permiso para Android M.
* Ahora puede configurar las características de ubicación en tiempo de ejecución en lugar de usar `AndroidManifest.xml`.
* Corrija un error de permiso: si usa `ACCESS_FINE_LOCATION`, ya no necesita `ACCESS_COARSE_LOCATION`.
* Mejoras de estabilidad.

## <a name="400-07062015"></a>4.0.0 (07/06/2015)
* Cambios en el protocolo interno para que la inserción y los análisis sean más confiables.
* La inserción nativa (GCM/ADM) ahora también se usa para notificaciones dentro de la aplicación, por lo que debe configurar las credenciales de inserción nativas para cualquier tipo de campaña de inserción.
* Corrección de la notificación con imagen grande: se mostraban solo 10 s después de insertarse.
* Corrección de un error en la vista web: al hacer clic en un vínculo, también se ejecutaba la URL de la acción predeterminada.
* Corrección de un bloqueo excepcional relacionado con la administración del almacenamiento local.
* Corrección de la administración dinámica de cadenas de configuración.
* Actualización del CLUF.

## <a name="300-02172015"></a>3.0.0 (02/17/2015)
* Versión inicial de Azure Mobile Engagement
* La configuración de appID se reemplaza por una configuración de cadena de conexión.
* Se ha eliminado la API para enviar y recibir mensajes XMPP arbitrarios de entidades XMPP arbitrarias.
* Se ha eliminado la API para enviar y recibir mensajes entre dispositivos.
* Mejoras de seguridad.
* Seguimiento de SmartAd y Google Play eliminados.


