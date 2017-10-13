---
title: "Administración de Protección contra DDoS de Azure estándar mediante Azure Portal | Microsoft Docs"
description: "Aprenda a usar la telemetría de Protección contra DDoS de Azure estándar en Azure Monitor para mitigar un ataque."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.openlocfilehash: 5c599b4cc867dbc9a081af3a081195b998f63954
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Administración de Protección contra DDoS de Azure estándar mediante Azure Portal

>[!IMPORTANT]
>El servicio Protección contra DDoS de Azure estándar (Protección contra DDoS) se encuentra actualmente en la versión preliminar. El servicio Protección contra DDoS es compatible con un número limitado de recursos de Azure y en un determinado número de regiones. Necesita [registrarse en el servicio](http://aka.ms/ddosprotection) durante la versión preliminar limitada para habilitar Protección contra DDoS para su suscripción. Después de registrarse, el equipo de DDoS de Azure se pondrá en contacto con usted para guiarle en el proceso de habilitación. El servicio Protección contra DDoS está disponible en las regiones Este de EE. UU., Oeste de EE. UU. y Centro occidental de EE. UU. Durante la versión preliminar, no se le cobrará por usar el servicio.

En este artículo se muestra cómo usar Azure Portal para habilitar y deshabilitar Protección contra DDoS, y cómo usar la telemetría para mitigar un ataque. 

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en Azure Portal: http://portal.azure.com/.

## <a name="enable-ddos-protection"></a>Habilitar Protección contra DDoS

Habilite Protección contra DDoS en una red virtual nueva o existente.

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>Crear una red virtual y habilitar Protección contra DDoS

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.
2. Haga clic en **Redes** y seleccione **Red virtual**.
3. Escriba la información de la red virtual. En *Protección contra DDoS*, haga clic en **Habilitado** y luego haga clic en **Crear**.

    ![Creación de una red virtual](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

Una advertencia indica que se cobrarán cargos por habilitar Protección contra DDoS. En la versión preliminar no se cobra ningún cargo por habilitar Protección contra DDoS. Los cargos se generan en disponibilidad general y los clientes reciben un aviso de 30 días antes de empezar a cobrar los cargos y ofrecer la disponibilidad general.

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>Habilitar Protección contra DDoS en una red virtual existente 

1. Haga clic en **Redes virtuales** en el menú de Azure Portal y después seleccione la red virtual.
2. Haga clic en **Protección contra DDoS**, elija **Habilitado** en la pantalla *Protección contra DDoS* y luego haga clic en **Guardar**. 

Una advertencia indica que se cobrarán cargos por habilitar Protección contra DDoS. En la versión preliminar no se cobra ningún cargo por habilitar Protección contra DDoS. Los cargos se generan en disponibilidad general y los clientes reciben un aviso de 30 días antes de empezar a cobrar los cargos y ofrecer la disponibilidad general.

## <a name="disable-ddos-protection"></a>Deshabilitar Protección contra DDoS

1. Haga clic en **Redes virtuales** en el menú de Azure Portal y después seleccione la red virtual.
2. Haga clic en **Protección contra DDoS**, elija **Deshabilitado** en la pantalla *Protección contra DDoS* y luego haga clic en **Guardar**.

## <a name="configure-alerts-on-ddos-protection-metrics"></a>Configurar alertas para las métricas de Protección contra DDoS

Al aprovechar la configuración de alertas de Azure Monitor, puede seleccionar cualquiera de las métricas de Protección contra DDoS disponibles para avisar cuando hay una mitigación activa durante un ataque. Cuando se cumplan las condiciones, recibirá un correo electrónico de alerta en la dirección especificada.

1. Haga clic en **Supervisión** y luego en **Métricas**.
2. En la pantalla *Métricas*, seleccione el grupo de recursos, el tipo de recurso de **Dirección IP pública** y la dirección IP pública de Azure.
3. Para configurar una alerta de correo electrónico para una métrica, haga clic en **Click to add an alert** (Haga clic para agregar una alerta). Se pueden crear alertas de correo electrónico para cualquier métrica, pero la métrica más obvia es **Under DDoS attack or not** (Frente a ataque DDoS o no). Se trata de un valor booleano 1 o 0. Un **1** significa que está siendo atacado. Un **0** significa que no está siendo atacado.
4. Para recibir un correo electrónico cuando esté siendo atacado, establezca la métrica para **Under DDoS attack or no** (Frente a ataque DDoS o no) y **Condition to Greater than zero (0) over the last 5 minutes** (Condicionar a Mayor que cero [0] durante los últimos 5 minutos). Se pueden configurar alertas similares para otras métricas.

    ![Configuración de métricas](./media/ddos-protection-manage-portal/ddos-metrics.png)

    En un intervalo de pocos minutos tras la detección del ataque, recibe una notificación mediante las métricas de Azure Monitor.

    ![Alerta de ataque](./media/ddos-protection-manage-portal/ddos-alert.png) 

Puede aprender más sobre [configurar webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md) y [aplicaciones lógicas](../logic-apps/logic-apps-what-are-logic-apps.md) para la creación de alertas.

## <a name="configure-logging-on-ddos-protection-metrics"></a>Configurar el registro de métricas de Protección contra DDoS

1. Haga clic en **Supervisar** y luego en **Configuración de diagnóstico**.
2. En la pantalla *Métricas*, seleccione el grupo de recursos, el tipo de recurso de **Dirección IP pública** y la dirección IP pública de Azure.
3. Haga clic en **Turn on diagnostics to collect the following data** (Activar diagnósticos para recopilar los siguientes datos).

Hay tres opciones disponibles para el registro:

- **Archivar en una cuenta de almacenamiento**: escribe registros en una cuenta de almacenamiento.
- **Transmitir a un centro de eventos**: permite a un receptor de registros seleccionar los registros mediante un centro de eventos. Esto habilita la integración con Splunk u otros sistemas SIEM.
- **Enviar a Log Analytics**: escribe registros en el servicio Log Analytics de OMS de Azure.

## <a name="use-ddos-protection-telemetry"></a>Usar telemetría de Protección contra DDoS

La telemetría para un ataque se proporciona a través de Azure Monitor en tiempo real. La telemetría está disponible solo durante el tiempo en que una dirección IP pública está en proceso de mitigación. No verá ninguna telemetría antes o después de mitigar un ataque.

1. Haga clic en **Supervisión** y luego en **Métricas**. 
2. En la pantalla *Métricas*, seleccione el grupo de recursos, el tipo de recurso de **Dirección IP pública** y la dirección IP pública de Azure. En el lado izquierdo de la pantalla se muestra una serie de métricas disponibles. Cuando se seleccionan estas métricas, se representan en el gráfico de métricas de Azure Monitor, en la pantalla de información general. 

Los nombres de las métricas presentan distintos tipos de paquetes y bytes frente a paquetes, con una construcción básica de nombres de etiqueta en cada métrica, como se muestra aquí:

- **Nombre de etiqueta de quitados (por ejemplo, paquetes de entrada quitados por DDoS):** el número de paquetes quitados o limpiados por el sistema de Protección contra DDoS.
- **Nombre de etiqueta de reenviados (por ejemplo: paquetes de entrada reenviados por DDoS):** el número de paquetes reenviados por el sistema DDoS a la dirección IP virtual de destino (tráfico que no se filtró).
- **Sin nombre de etiqueta (por ejemplo, paquetes de entrada por DDoS):** el número total de paquetes que han llegado al sistema de limpieza (representa la suma de los paquetes quitados y los reenviados).

## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre los registros de Diagnósticos de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [Análisis de registros desde Azure Storage con Log Analytics](../log-analytics/log-analytics-azure-storage.md)
- [Introducción a Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)