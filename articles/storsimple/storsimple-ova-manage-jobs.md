<properties 
   pageTitle="Visualización y administración de trabajos de la matriz virtual de StorSimple | Microsoft Azure"
   description="Describe la página Trabajos del servicio StorSimple Manager y cómo usarla para hacer un seguimiento de los trabajos actuales y recientes de la máquina virtual de StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="v-sharos" />

# Uso del servicio StorSimple para ver los trabajos de la matriz virtual de StorSimple (vista previa)

## Información general

La página **Trabajos** ofrece un portal central único para ver y administrar los trabajos que se inician en las matrices virtuales (que también se conocen como dispositivos virtuales locales) y que están conectados al servicio StorSimple Manager. Puede ver los trabajos en ejecución, finalizados y con error de varios dispositivos virtuales. Los resultados se presentan en un formato tabular.

![Página de trabajos](./media/storsimple-ova-manage-jobs/ovajobs1.png)

Puede buscar rápidamente los trabajos que le interesan filtrando por campos como:

- **Estado**: puede buscar todos los trabajos o los trabajos en ejecución, completados o con error.
- **Desde y Hasta**: los trabajos se pueden filtrar según el intervalo de fecha y hora.
- **Tipo**: el tipo de trabajo puede ser todos, copia de seguridad, restauración, conmutación por error, descarga de actualizaciones o instalación de actualizaciones.
- **Dispositivos**: los trabajos se inician en un dispositivo específico conectado al servicio. A continuación, los trabajos filtrados se tabulan en función de los siguientes atributos:

    - **Tipo**: el tipo de trabajo puede ser todos, copia de seguridad, restauración, conmutación por error, descarga de actualizaciones o instalación de actualizaciones.

    - **Estado**: los trabajos pueden ser todos, en ejecución, completados o con error.

    - **Entidad**: los trabajos pueden estar asociados a un volumen, un recurso compartido o un dispositivo.

    - **Dispositivo**: el nombre del dispositivo en el que se inició el trabajo.

    - **Hora de inicio**: la hora a la que se inició el trabajo.

    - **Progreso**: el porcentaje de finalización de un trabajo en ejecución. Para un trabajo completado, este debe equivaler siempre al 100%.

La lista de trabajos se actualiza cada 30 segundos.

## Ver detalles del trabajo

Realice los pasos siguientes para ver los detalles de cualquier trabajo.

#### Para ver los detalles del trabajo

1. En la página **Trabajos**, para mostrar los trabajos en los que esté interesado, ejecute una consulta con los filtros adecuados. Puede buscar los trabajos completados o en ejecución.

2. Seleccione un trabajo en la lista tabular de trabajos.

3. En la parte inferior de la página, haga clic en **Detalles**.

4. En el cuadro de diálogo **Detalles**, puede ver el estado, los detalles y las estadísticas de tiempo. La siguiente ilustración muestra un ejemplo del cuadro de diálogo **Detalles del trabajo de copia de seguridad**.
 
    ![Página Detalles del trabajo](./media/storsimple-ova-manage-jobs/ovajobs2.png)

## Pasos siguientes

[Obtener información sobre cómo usar la interfaz de usuario web local para administrar la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0218_2016-->