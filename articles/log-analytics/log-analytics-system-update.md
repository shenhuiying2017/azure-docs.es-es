<properties
	pageTitle="Solución Evaluación de actualizaciones del sistema de Log Analytics | Microsoft Azure"
	description="Puede usar la solución de actualizaciones del sistema de Log Analytics para ayudarle a aplicar las actualizaciones que faltan a los servidores de su infraestructura."
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
	ms.date="05/06/2016"
	ms.author="banders"/>

# Solución Evaluación de actualizaciones del sistema de Log Analytics


Puede usar la solución de actualizaciones del sistema de Log Analytics para ayudarle a aplicar las actualizaciones que faltan a los servidores de su infraestructura. Una vez instalada la solución, puede ver las actualizaciones que faltan en sus servidores supervisados mediante el icono **System Update Assessment** (Evaluación de actualizaciones del sistema) en la página **Información general** de OMS.

Si se detectan actualizaciones que faltan, se muestran los detalles en el panel **Actualizaciones**. Puede usar el panel **Actualizaciones** para trabajar con actualizaciones que faltan y desarrollar un plan para aplicarlas a los servidores que las necesiten.

## Instalación y configuración de la solución
Utilice la siguiente información para instalar y configurar la solución.

- Agregue la solución Evaluación de actualizaciones del sistema al área de trabajo de OMS mediante el proceso descrito en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md). No es necesario realizar ninguna configuración más.

## Detalles de la recopilación de datos de actualizaciones del sistema

En la tabla siguiente se muestran los métodos de recopilación de datos y otros detalles sobre cómo se recopilan los datos para Evaluación de actualizaciones del sistema.

| plataforma | Agente directo | Agente de SCOM | Almacenamiento de Azure | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
|---|---|---|---|---|---|---|
|Windows|![Sí](./media/log-analytics-system-update/oms-bullet-green.png)|![Sí](./media/log-analytics-system-update/oms-bullet-green.png)|![No](./media/log-analytics-system-update/oms-bullet-red.png)| ![No](./media/log-analytics-system-update/oms-bullet-red.png)|![Sí](./media/log-analytics-system-update/oms-bullet-green.png)| Al menos 2 veces al día y 15 minutos después de instalar una actualización|


### Para trabajar con actualizaciones, siga estos pasos:

1. En la página **Información general**, haga clic en el mosaico **Evaluación de actualizaciones del sistema**. ![imagen de la página Información general](./media/log-analytics-system-update/oms-updates01.png)
2. En el panel **Actualizaciones**, vea las categorías de actualización. ![imagen de la página Actualizaciones](./media/log-analytics-system-update/oms-updates02.png)
3. Desplácese a la derecha de la página para ver la hoja **Tipo de actualizaciones que faltan** y haga clic en **Actualizaciones de seguridad**. ![imagen de la página Actualizaciones](./media/log-analytics-system-update/oms-updates03.png)
4. En la página Búsqueda, se muestra una lista de actualizaciones de seguridad que se detectaron que faltaban en la infraestructura de servidores. Haga clic en el identificador de un artículo de Knowledge Base (KBID) para obtener más información sobre la actualización que falta. En este ejemplo, *KBID 3032323*. ![imagen de la página Actualizaciones](./media/log-analytics-system-update/oms-updates04.png)
5. El explorador web abrirá el artículo de Knowledge Base que describe la actualización. ![imagen del artículo de la knowledge base](./media/log-analytics-system-update/oms-updates05.png)
6. Con la información encontrada, puede crear un plan para aplicar las actualizaciones que faltan.

## Pasos siguientes

- [Busque registros](log-analytics-log-searches.md) para ver datos detallados de la actualización del sistema.

<!---HONumber=AcomDC_0518_2016-->