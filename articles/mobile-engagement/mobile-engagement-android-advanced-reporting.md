---
title: Opciones avanzadas de informes para el SDK de Android para Azure Mobile Engagement
description: "Describe cómo realizar los informes avanzados para capturar el análisis para el SDK de Android para Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 7da7abd5-19d6-4892-94d8-818e5424b2cd
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 2a1445afa2c2fca1a31ad9c012b9c8a917ebf65c
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="advanced-reporting-with-engagement-on-android"></a>Informes avanzados con Engagement en Android
> [!div class="op_single_selector"]
> * [Windows universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

Este tema describe los escenarios de informes adicionales en la aplicación Android. Puede aplicar estas opciones a la aplicación creada en el tutorial [Introducción a Azure Mobile Engagement para aplicaciones Android](mobile-engagement-android-get-started.md) .

## <a name="prerequisites"></a>Requisitos previos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

El tutorial realizado era deliberadamente directo y sencillo, pero hay una serie de opciones avanzadas entre las que elegir.

## <a name="modifying-your-activity-classes"></a>Modificación de sus clases `Activity`
En el [tutorial de introducción](mobile-engagement-android-get-started.md), todo lo que ha tenido que hacer que sus subclases `*Activity` heredaran de las clases `Engagement*Activity` correspondientes. Por ejemplo, si su actividad heredada amplía `ListActivity`, se ampliaría `EngagementListActivity` también.

> [!IMPORTANT]
> Al usar `EngagementListActivity` o `EngagementExpandableListActivity`, asegúrese de que cualquier llamada a `requestWindowFeature(...);` se realice antes que la llamada a `super.onCreate(...);`. De lo contrario, se producirá un bloqueo.
> 
> 

Puede encontrar estas clases en la carpeta `src` y puede copiarlas en su proyecto. Las clases también están en **JavaDoc**.

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Método alternativo: llamar a `startActivity()` y `endActivity()` manualmente
Si no puede o no quiere sobrecargar sus clases `Activity`, puede iniciar y finalizar sus actividades llamando a los métodos `EngagementAgent` directamente.

> [!IMPORTANT]
> El SDK de Android nunca llama al método `endActivity()`, incluso cuando la aplicación está cerrada (en Android, las aplicaciones nunca se cierran realmente). Por tanto, es *MUY* recomendable llamar al método `startActivity()` en la devolución de llamada `onResume` de *TODAS* las actividades y al método `endActivity()` en la devolución de llamada `onPause()` de *TODAS* las actividades. Esta es la única manera de asegurarse de que las sesiones no se pierdan. Si una sesión se pierde, el servicio Engagement nunca se desconectará del back-end de Engagement (dado que el servicio permanece conectado mientras una sesión esté pendiente).
> 
> 

Este es un ejemplo:

    public class MyActivity extends Some3rdPartyActivity
    {
      @Override
      protected void onResume()
      {
        super.onResume();
        String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
        EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
      }

      @Override
      protected void onPause()
      {
        super.onPause();
        EngagementAgent.getInstance(this).endActivity();
      }
    }

Este ejemplo es parecido a la clase `EngagementActivity` y sus variantes, cuyo código fuente se proporciona en la carpeta `src`.

## <a name="using-applicationoncreate"></a>Uso de Application.onCreate()
Cualquier código que coloque en `Application.onCreate()` y en otras devoluciones de llamada de aplicaciones se ejecutará para todos los procesos de la aplicación, incluido el servicio Engagement. Esto puede tener efectos secundarios no deseados, como asignaciones innecesarias de memoria y subprocesos en el proceso de Engagement o receptores o servicios de difusión duplicados.

Si anula `Application.onCreate()`, se recomienda que agregue el siguiente fragmento de código al comienzo de su función `Application.onCreate()`:

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

Puede hacer lo mismo para `Application.onTerminate()`, `Application.onLowMemory()` y `Application.onConfigurationChanged(...)`.

También puede ampliar `EngagementApplication` en lugar de `Application`: la devolución de llamada `Application.onCreate()` solo realiza el proceso y llama a `Application.onApplicationProcessCreate()` si el proceso actual no es el que hospeda el servicio Engagement; las mismas reglas se aplican para las demás devoluciones de llamada.

## <a name="tags-in-the-androidmanifestxml-file"></a>Etiquetas del archivo AndroidManifest.xml
En la etiqueta de servicio del archivo AndroidManifest.xml, el atributo `android:label` le permite elegir el nombre del servicio Engagement que aparecerá para los usuarios finales en la pantalla Servicios en ejecución de su teléfono. Se recomienda establecer este atributo en `"<Your application name>Service"` (por ejemplo, `"AcmeFunGameService"`).

La especificación del atributo `android:process` garantiza que el servicio Engagement se ejecuta en su propio proceso (al ejecutarse Engagement en el mismo proceso que su aplicación, el subproceso principal y de interfaz de usuario podría tener menos capacidad de respuesta).

## <a name="building-with-proguard"></a>Compilación con ProGuard
Si compila su paquete de aplicación con ProGuard, deberá mantener algunas clases. Puede usar el siguiente snippet de configuración:

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
     }
