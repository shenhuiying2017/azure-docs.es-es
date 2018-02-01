---
title: "Supervisión de Surface Hub con Azure Log Analytics | Microsoft Docs"
description: "Use la solución Surface Hub para realizar un seguimiento del estado de sus dispositivos con esta solución y comprender cómo se están utilizando."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4f56369e412bdd285d3c370f5153fee4f539dfcf
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>Supervisión de Surface Hubs con Log Analytics para realizar un seguimiento de su estado

![Símbolo de Surface Hub](./media/log-analytics-surface-hubs/surface-hub-symbol.png)

En este artículo se describe cómo se puede usar la solución Surface Hub en Log Analytics para supervisar los dispositivos Microsoft Surface Hub. Log Analytics ayuda a realizar un seguimiento del estado de sus dispositivos con esta solución y comprender cómo se están utilizando.

Cada dispositivo Surface Hub tiene el agente de supervisión de Microsoft instalado. A través del agente se pueden enviar datos desde Surface Hub a Log Analytics. Los archivos de registro se leen desde los dispositivos Surface Hub y se envían a Log Analytics. Los problemas (por ejemplo, que los servidores estén sin conexión, el calendario no se sincronice o la cuenta del dispositivo no pueda iniciar sesión en Skype) se muestran en Log Analytics en el panel de Surface Hub. Mediante el uso de los datos en el panel, puede identificar los dispositivos que no se están ejecutando o que tienen otros problemas, así como, posiblemente aplicar correcciones para los problemas detectados.

## <a name="install-and-configure-the-solution"></a>Instalación y configuración de la solución
Utilice la siguiente información para instalar y configurar la solución. Con el fin de administrar los dispositivos Surface Hub en Log Analytics, necesitará lo siguiente:

* Un nivel de [suscripción a Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) que admitirá el número de dispositivos que desea supervisar. Los precios de Log Analytics varían en función de cuántos dispositivos inscritos haya y de la cantidad de datos que se procesen. Deberá tener esto en cuenta al planear la implementación de Surface Hub.

A continuación, agregará un área de trabajo de Log Analytics existente o creará una nueva. Las instrucciones detalladas para usar cualquiera de estos métodos se encuentran en el artículo de [introducción a Log Analytics](log-analytics-get-started.md). Una vez que se configure el área de trabajo de Log Analytics, hay dos maneras de inscribir los dispositivos Surface Hub:

* Automáticamente mediante Intune
* Manualmente a través de la aplicación **Configuración** del dispositivo Surface Hub.

## <a name="set-up-monitoring"></a>Configuración de la supervisión
Puede supervisar el estado y la actividad de su dispositivo Surface Hub usando Log Analytics. Puede inscribir el dispositivo Surface Hub en Surface Hub mediante Intune o de manera local con **Configuración** de Surface Hub.

## <a name="connect-surface-hubs-to-log-analytics-through-intune"></a>Conexión de Surface Hubs a Log Analytics mediante Intune
Necesitará el identificador de área de trabajo y la clave de área de trabajo del área de trabajo de Log Analytics que va a administrar sus dispositivos Surface Hub. Puede obtenerlos en la configuración del área de trabajo en Azure Portal.

Intune es un producto de Microsoft que permite administrar de forma centralizada la configuración de Log Analytics que se aplica a uno o varios de los dispositivos. Siga estos pasos para configurar los dispositivos mediante Intune:

1. Inicie sesión en Intune.
2. Vaya a **Configuración** > **Orígenes conectados**.
3. Cree o edite una directiva basada en la plantilla de Surface Hub.
4. Vaya a la sección de OMS (Azure Operational Insights) de la directiva y agregue el *identificador de área de trabajo* y la *clave de área de trabajo* de Log Analytics a la directiva.
5. Guarde la directiva.
6. Asocia la directiva con el grupo de dispositivos adecuado.

   ![Directiva de Intune](./media/log-analytics-surface-hubs/intune.png)

Después, Intune sincroniza la configuración de Log Analytics con los dispositivos en el grupo de destino, con lo que se inscriben en el área de trabajo de Log Analytics.

## <a name="connect-surface-hubs-to-log-analytics-using-the-settings-app"></a>Conexión de los dispositivos Surface Hub a Log Analytics a través de la aplicación Configuración
Necesitará el identificador de área de trabajo y la clave de área de trabajo del área de trabajo de Log Analytics que va a administrar sus dispositivos Surface Hub. Puede obtenerlos en la configuración del área de trabajo de Log Analytics en Azure Portal.

Si no usa Intune para administrar su entorno, puede inscribir dispositivos manualmente con **Configuración** en cada dispositivo Surface Hub:

1. En Surface Hub, abra **Configuración**.
2. Escriba las credenciales de administrador de dispositivos cuando se le solicite.
3. Haga clic en **Este dispositivo** y, en **Supervisión**, haga clic en **Configure OMS Settings** (Configuración de OMS).
4. Seleccione **Habilitar supervisión**.
5. En el cuadro de diálogo de configuración de OMS, escriba el **identificador de área de trabajo** y la **clave de área de trabajo** de Log Analytics.  
   ![settings](./media/log-analytics-surface-hubs/settings.png)
6. Haga clic en **Aceptar** para completar la configuración.

Aparecerá una confirmación que indica si la configuración se aplicó correctamente en el dispositivo. Si es así, aparecerá un mensaje que indica que el agente se ha conectado correctamente a Log Analytics. El dispositivo comienza a enviar datos a Log Analytics, donde puede verlos y usarlos.

## <a name="monitor-surface-hubs"></a>Supervisión de dispositivos Surface Hub
La supervisión de los dispositivos Surface Hub con Log Analytics es muy similar a la de todos los demás dispositivos inscritos.

1. Inicie sesión en el Portal de Azure.
2. Vaya al área de trabajo de Log Analytics y seleccione **Información general**.
2. Haga clic en el icono de Surface Hub.
3. Se muestra el estado del dispositivo.

   ![Panel de Surface Hub](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Puede crear [alertas](log-analytics-alerts.md) en función de las búsquedas de registros existentes o personalizadas. Con los datos que recopile Log Analytics de los dispositivos Surface Hub, puede buscar problemas y generar alertas sobre las condiciones que defina para sus dispositivos.

## <a name="next-steps"></a>pasos siguientes
* Use [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para ver datos detallados de Surface Hub.
* Cree [alertas](log-analytics-alerts.md) para recibir una notificación cuando se produzcan problemas con Surface Hub.
