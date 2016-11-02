<properties
    pageTitle="Supervisión de Surface Hub con Log Analytics | Microsoft Azure"
    description="Use la solución Surface Hub para realizar un seguimiento del estado de sus dispositivos con esta solución y comprender cómo se están utilizando."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="banders"/>


# <a name="monitor-surface-hubs-with-log-analytics"></a>Supervisión de Surface Hub con Log Analytics

Este artículo describe cómo puede usar la solución Surface Hub en Log Analytics para supervisar los dispositivos Microsoft Surface Hub con Microsoft Operations Management Suite (OMS). Log Analytics ayuda a realizar un seguimiento del estado de sus dispositivos con esta solución y comprender cómo se están utilizando.

Cada dispositivo Surface Hub tiene el agente de supervisión de Microsoft instalado. A través del agente se pueden enviar datos desde Surface Hub a OMS. Los archivos de registro se leen desde los dispositivos Surface Hub y se envían al servicio de OMS. Los problemas (por ejemplo, que los servidores estén sin conexión, el calendario no se sincronice o la cuenta del dispositivo no pueda iniciar sesión en Skype) se muestran en OMS en el panel de Surface Hub. Mediante el uso de los datos en el panel, puede identificar los dispositivos que no se están ejecutando o que tienen otros problemas, así como, posiblemente aplicar correcciones para los problemas detectados.


## <a name="installing-and-configuring-the-solution"></a>Instalación y configuración de la solución

Utilice la siguiente información para instalar y configurar la solución. Para administrar sus dispositivos Surface Hub desde Microsoft Operations Management Suite (OMS), necesitará lo siguiente:

- Una suscripción válida a [OMS](http://www.microsoft.com/oms).
- Un nivel de [suscripción a OMS](https://azure.microsoft.com/pricing/details/log-analytics/) que será admita el número de dispositivos que quiera supervisar. Los precios de OMS varían en función de cuántos dispositivos inscritos haya y de la cantidad de datos que se procesen. Deberá tener esto en cuenta al planear la implementación de Surface Hub.

Después, agregará una suscripción de OMS a su suscripción de Microsoft Azure existente o creará una nueva área de trabajo directamente a través del portal de OMS. Las instrucciones detalladas para usar cualquiera de estos métodos se encuentran en el artículo de [introducción a Log Analytics](log-analytics-get-started.md). Una vez configurada la suscripción de OMS, hay dos formas de inscribir dispositivos Surface Hub:

- Automáticamente mediante InTune
- Manualmente a través de la aplicación **Configuración** del dispositivo Surface Hub.

## <a name="set-up-monitoring"></a>Configuración de la supervisión

Puede supervisar el estado y la actividad de su dispositivo Surface Hub usando Log Analytics de OMS. Puede inscribir el dispositivo Surface Hub en OMS mediante InTune o localmente a través de la aplicación **Configuración** de Surface Hub.

## <a name="connect-surface-hubs-to-oms-through-intune"></a>Conexión de dispositivos Surface Hub a OMS mediante InTune

Necesitará el identificador de área de trabajo y la clave de área de trabajo del área de trabajo de OMS que va a administrar sus dispositivos Surface Hub. Puede obtenerlos desde el portal de OMS.

InTune es un producto de Microsoft que permite administrar de forma centralizada la configuración de OMS que se aplica a uno o varios de los dispositivos. Siga estos pasos para configurar los dispositivos a través de InTune:

1. Inicie sesión en InTune.
2. Vaya a **Configuración** > **Orígenes conectados**.
3. Cree o edite una directiva basada en la plantilla de Surface Hub.
4. Vaya a la sección de OMS (Azure Operational Insights) de la directiva y agregue el *identificador de área de trabajo* y la *clave de área de trabajo* a la directiva.
5. Guarde la directiva.
6. Asocia la directiva con el grupo de dispositivos adecuado.

  ![Directiva de InTune](./media/log-analytics-surface-hubs/intune.png)

Después, InTune sincroniza la configuración de OMS con los dispositivos en el grupo de destino, con lo que se inscriben en el área de trabajo de OMS.

## <a name="connect-surface-hubs-to-oms-using-the-settings-app"></a>Conexión de los dispositivos Surface Hub a OMS a través de la aplicación Configuración

Necesitará el identificador de área de trabajo y la clave de área de trabajo del área de trabajo de OMS que va a administrar sus dispositivos Surface Hub. Puede obtenerlos desde el portal de OMS.

Si no usa InTune para administrar su entorno, puede inscribir dispositivos manualmente a través de la aplicación **Configuración** en cada dispositivo Surface Hub:

1. En Surface Hub, abra **Configuración**.
2. Escriba las credenciales de administrador de dispositivos cuando se le solicite.
3. Haga clic en **Este dispositivo** y, en **Supervisión**, haga clic en **Configure OMS Settings** (Configuración de OMS).
4. Seleccione **Habilitar supervisión**.
6. En el cuadro de diálogo de configuración de OMS, escriba el **identificador de área de trabajo** y escriba la **clave de área de trabajo**.  
  ![settings](./media/log-analytics-surface-hubs/settings.png)
7. Haga clic en **Aceptar** para completar la configuración.

Aparecerá una confirmación que indica si la configuración de OMS se aplicó correctamente en el dispositivo. Si es así, aparecerá un mensaje que indica que el agente se ha conectado correctamente al servicio de OMS. El dispositivo comienza a enviar datos a OMS, donde puede verlos y utilizarlos.

## <a name="monitor-surface-hubs"></a>Supervisión de dispositivos Surface Hub

La supervisión de los dispositivos Surface Hubs con OMS es muy similar a la de todos los demás dispositivos inscritos.

1. Inicie sesión en el portal de OMS.
2. Desplácese hasta el panel de paquete de solución Surface Hub.
3. Se muestra el estado del dispositivo.

  ![Panel de Surface Hub](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Puede crear [alertas](log-analytics-alerts.md) en función de las búsquedas de registros existentes o personalizadas. Con los datos que recopile OMS de los dispositivos Surface Hub, puede buscar problemas y generar alertas sobre las condiciones que defina para sus dispositivos.


## <a name="next-steps"></a>Pasos siguientes

- Use [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para ver datos detallados de Surface Hub.
- Cree [alertas](log-analytics-alerts.md) para recibir una notificación cuando se produzcan problemas con Surface Hub.



<!--HONumber=Oct16_HO2-->


