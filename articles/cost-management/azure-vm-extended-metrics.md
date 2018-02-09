---
title: "Adición de métricas extendidas a máquinas virtuales de Azure | Microsoft Docs"
description: "Este artículo le ayuda a habilitar y configurar métricas de diagnóstico extendidas en las máquinas virtuales de Azure."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 91797aaab1dca96e78643f57776eb16d336e894b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Adición de métricas extendidas a máquinas virtuales de Azure

Cost Management utiliza los datos de métrica de Azure de las máquinas virtuales de Azure para mostrar información detallada acerca de sus recursos. Cost Management utiliza los datos de métrica, también denominados contadores de rendimiento, para generar informes. Sin embargo, Cost Management no recopila automáticamente todos los datos de métrica de Azure de las máquinas virtuales invitadas (es preciso habilitar la recopilación de métricas). Este artículo le ayuda a habilitar y configurar métricas de diagnóstico adicionales en las máquinas virtuales de Azure.

Tras habilitar la recopilación de métricas puede:

- Saber si las máquinas virtuales están llegando a sus límites de CPU, disco y memoria.
- Detectar tendencias de uso y anomalías.
- Controlar los costos mediante el ajuste de tamaño en función del uso.
- Obtener recomendaciones de para la optimización rentable del tamaño de Cost Management.

Por ejemplo, puede supervisar los porcentajes de CPU y de memoria de las máquinas virtuales de Azure. Las métricas de las máquinas virtual de Azure corresponden a _Porcentaje de CPU [host]_ y _Porcentaje de memoria [invitado]_.

## <a name="verify-that-metrics-are-enabled-on-vms"></a>Comprobación de que las métricas están habilitadas en las máquinas virtuales

1. Inicie sesión en Azure Portal: http://portal.azure.com/.
2. En **Máquinas virtuales**, seleccione una máquina virtual y en **Supervisión**, seleccione **Métricas**. Se muestra una lista de las métricas disponibles.
3. Seleccione algunas métricas y un gráfico mostrará los datos de las mismas.  
    ![Métrica de ejemplo: porcentaje de CPU de host](./media/azure-vm-extended-metrics/metric01.png)

En el ejemplo anterior, hay disponible un conjunto limitado de métricas estándar de los hosts, pero no métricas de memoria. Las métricas de memoria forman parte de las métricas extendidas. Para habilitar las métricas extendidas es preciso dar algunos pasos más. La siguiente información le guía por el proceso de su habilitación.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Habilitación de métricas extendidas en Azure Portal

Las métricas estándar son las métricas del equipo host. La métrica _Porcentaje de CPU [host]_ es un ejemplo. También hay métricas básicas para las máquinas virtuales invitadas y también se denominan métricas extendidas. Entre los ejemplos de métricas extendidas se encuentran _Porcentaje de memoria [invitado]_ y _Memoria disponible [invitado]_.

La habilitación de las métricas extendidas es un proceso sencillo. En cada máquina virtual, habilite la supervisión a nivel de invitado. Cuando se habilita la supervisión a nivel de invitado, el agente de Azure Diagnostics se instala en la máquina virtual. El siguiente proceso es el mismo para las máquinas virtuales clásicas y regulares, así como para las máquinas virtuales Windows y Linux.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Habilitar supervisión a nivel de invitado en las máquinas virtuales existentes

1. En **Máquinas virtuales**, vea la lista de las máquinas virtuales y seleccione una de ellas.
2. En **Supervisión**, seleccione **Métricas**.
3. Haga clic en **Configuración de diagnóstico**.
4. En la página Configuración de diagnóstico, haga clic en **Habilitar supervisión a nivel de invitado**. Las máquinas virtuales Linux requieren una cuenta de almacenamiento existente. Si no elige una cuenta de almacenamiento para una máquina virtual de Windows, se crea automáticamente.  
    ![Habilitar la supervisión a nivel de invitado](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
5. Pocos minutos después, el agente de Azure Diagnostics está instalado en la máquina virtual. Actualice la página y la lista de métricas disponibles se actualizará con las métricas de invitado.  
    ![Métricas extendidas](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Habilitar la supervisión a nivel de invitado en las máquinas virtuales nuevas

Al crear nuevas máquinas virtuales, asegúrese de seleccionar **Diagnósticos del SO invitado**. Las máquinas virtuales Linux requieren una cuenta de almacenamiento existente. Si no elige una cuenta de almacenamiento para una máquina virtual de Windows, se crea automáticamente.

![Habilitar los diagnósticos del SO invitado](./media/azure-vm-extended-metrics/new-enable-diag.png)

## <a name="resource-manager-credentials"></a>Credenciales de Resource Manager

Después de habilitar las métricas extendidas, asegúrese de que Cost Management tiene acceso a sus [credenciales de Resource Manager](activate-subs-accounts.md). Dichas credenciales son necesarias para que Cost Management recopile y muestre los datos de rendimiento de las máquinas virtuales. También se utilizan para crear recomendaciones de optimización de costo. Cost Management necesita al menos tres días de datos de rendimiento de una instancia para determinar si es candidata a una recomendación de reducción de tamaño.

## <a name="enable-vm-metrics-with-a-script"></a>Habilitar las métricas de una máquina virtual con un script

Las métricas de una máquina virtual se pueden habilitar con scripts de Azure PowerShell. Cuando haya muchas máquinas virtuales en las que desee habilitar las métricas, puede usar un script para automatizar el proceso. En GitHub, en [Azure Enable Diagnostics](https://github.com/Cloudyn/azure-enable-diagnostics), puede encontrar scripts de ejemplo.

## <a name="view-azure-performance-metrics"></a>Visualización de métricas de rendimiento de Azure

Para ver métricas del rendimiento de las instancias de Azure en el portal de Cloudyn, vaya a **Assets** (Recursos) > **Compute** > **Instance Explorer** (Explorador de instancias). En la lista de instancias de máquina virtual, expanda una de ellas y, después, expanda un recurso para ver sus detalles.

![Instance Explorer (Explorador de instancias)](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Pasos siguientes

- Si no ha habilitado el acceso de la API de Azure Resource Manager en sus cuentas, pase a [Activate Azure subscriptions and accounts with Azure Cost Management](activate-subs-accounts.md) (Activación de suscripciones y cuentas de Azure con Azure Cost Management)
