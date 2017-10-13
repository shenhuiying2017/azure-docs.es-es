---
title: "Supervisión de una aplicación de almacenamiento en la nube en Azure y solucionar sus problemas | Microsoft Docs"
description: "Use herramientas de diagnóstico, métricas y alertas para solucionar problemas de una aplicación en la nube y supervisar esta."
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: db88c331f79d83e0124519f8b6dbb34514b456dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Supervisión de una aplicación de almacenamiento en la nube y solución de sus problemas

Este tutorial es la parte número cuatro y última de una serie. Aprenderá a supervisar una aplicación de almacenamiento en la nube y solucionar sus problemas.

En la parte número cuatro de la serie, se aprende a:

> [!div class="checklist"]
> * Activar el registro y las métricas
> * Habilitar las alertas para errores de autorización
> * Ejecutar el tráfico de prueba con tokens SAS incorrectos
> * Descargar y analizar registros

[Azure Storage Analytics](../common/storage-analytics.md) proporciona el registro y los datos de métricas para una cuenta de almacenamiento. Estos datos proporcionan información sobre el estado de la cuenta de almacenamiento. Antes de poder ver la cuenta de almacenamiento, debe configurar la recopilación de datos. Este proceso implica activar el registro, configurar las métricas y habilitar las alertas.

El registro y las métricas de cuentas de almacenamiento están habilitados en la pestaña **Diagnósticos** en Azure Portal. Existen dos tipos de métricas. Las métricas **agregadas** recopilan la entrada/salida, la disponibilidad, la latencia y los porcentajes de éxito. Estas métricas se agregan a los servicios Blob, Cola, Tabla y Archivo. La opción **Por API** recopila el mismo conjunto de métricas para cada operación de almacenamiento en la API del servicio Azure Storage. Los registros de almacenamiento permiten grabar en la cuenta de almacenamiento detalles de solicitudes correctas e incorrectas. Estos registros no solo le permiten ver detalles de lectura y escritura, sino que también le permitirán eliminar operaciones de tablas, colas y blobs de Azure. También le permiten ver los motivos de solicitudes con error, como los tiempos de espera, la limitación y los errores de autorización.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal

Inicie sesión en el [Azure Portal](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>Activar el registro y las métricas

En el menú izquierdo, seleccione **Grupos de recursos**, seleccione **myResourceGroup** y luego seleccione la cuenta de almacenamiento en la lista de recursos.

En **Diagnósticos** establezca **Estado** en **Activado**. Asegúrese de que las casillas **Métricas agregadas de blob**, **Métricas por API de blob** y **Registros de blob** están habilitadas.

Cuando haya terminado, haga clic en **Guardar**.

![Panel Diagnósticos](media/storage-monitor-troubleshoot-storage-application/figure1.png)

## <a name="enable-alerts"></a>Habilitación de alertas

Las alertas proporcionan una manera de enviar correos electrónicos a los administradores o de desencadenar un webhook basándose en una métrica de superación de umbral. En este ejemplo, se habilita una alerta para la métrica `SASClientOtherError`.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Vaya a la cuenta de almacenamiento en Azure Portal.

En el menú izquierdo, seleccione **Grupos de recursos**, seleccione **myResourceGroup** y luego seleccione la cuenta de almacenamiento en la lista de recursos.

En la sección **Supervisión**, seleccione **Reglas de alerta**.

Seleccione **+ Agregar alerta**, en **Agregar una regla de alerta**, y rellene la información necesaria. Elija `SASClientOtherError` en la lista desplegable **Métrica** .

![Panel Diagnósticos](media/storage-monitor-troubleshoot-storage-application/figure2.png)

## <a name="simulate-an-error"></a>Simulación de un error

Para simular una alerta válida, puede intentar solicitar un blob inexistente en su cuenta de almacenamiento. Para ello, reemplace el valor `<incorrect-blob-name>` por un valor que no existe. Ejecute el siguiente ejemplo de código varias veces para simular solicitudes de blob erróneas.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <storage-account-name> \
    --account-key <storage-account-key> \
    --container-name <container> \
    --name <incorrect-blob-name> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d` \
    --output tsv)

curl https://<storage-account-name>.blob.core.windows.net/<container>/<incorrect-blob-name>?$sasToken
```

La siguiente imagen es una alerta de ejemplo que se basa en el error simulado ejecutado con el ejemplo anterior.

 ![Alerta de ejemplo](media/storage-monitor-troubleshoot-storage-application/alert.png)

## <a name="download-and-view-logs"></a>Descarga y vista de registros

Los registros de almacenamiento guardan datos en un conjunto de blobs en un contenedor de blobs denominado **$logs** en su cuenta de almacenamiento. Este contenedor no se muestra si enumera todos los contenedores de blobs de su cuenta, pero puede ver su contenido si se accede a él directamente.

En este escenario, se utiliza [Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx) para interactuar con su cuenta de almacenamiento de Azure.

### <a name="download-microsoft-message-analyzer"></a>Descarga de Microsoft Message Analyzer

Descargue [Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) e instale la aplicación.

Inicie la aplicación y elija **File (Archivo)** > **Open (Abrir)** > **From Other File Sources (Desde otros orígenes de archivo)**.

En el cuadro de diálogo **File Selector** (Selector de archivos), seleccione **+ Add Azure Connection** (Agregar conexión de Azure). Especifique el **nombre de cuenta de almacenamiento** y la **clave de cuenta** y haga clic en **Aceptar**.

![Microsoft Message Analyzer - Cuadro de diálogo Agregar conexión de almacenamiento de Azure](media/storage-monitor-troubleshoot-storage-application/figure3.png)

Cuando esté conectado, expanda los contenedores de la vista de árbol de almacenamiento para ver los blobs de registro. Seleccione el registro más reciente y haga clic en **Aceptar**.

![Microsoft Message Analyzer - Cuadro de diálogo Agregar conexión de almacenamiento de Azure](media/storage-monitor-troubleshoot-storage-application/figure4.png)

En el cuadro de diálogo **New Session** (Nueva sesión), haga clic en **Start** (Iniciar) para ver el registro.

Cuando el registro se abra, podrá ver los eventos de almacenamiento. Como puede ver en la siguiente imagen, se desencadenó `SASClientOtherError` en la cuenta de almacenamiento. Para información adicional sobre el registro de almacenamiento, visite [Azure Storage Analytics](../common/storage-analytics.md).

![Microsoft Message Analyzer - Visualización de eventos](media/storage-monitor-troubleshoot-storage-application/figure5.png)

[Explorador de Storage](https://azure.microsoft.com/features/storage-explorer/) es otra herramienta que se puede usar para interactuar con sus cuentas de almacenamiento, incluido el contenedor **$logs** y los registros contenidos en él.

## <a name="next-steps"></a>Pasos siguientes

En la parte número cuatro y última de la serie, aprendió a supervisar la cuenta de almacenamiento y solucionar sus problemas. Mas concretamente, aprendió a:

> [!div class="checklist"]
> * Activar el registro y las métricas
> * Habilitar las alertas para errores de autorización
> * Ejecutar el tráfico de prueba con tokens SAS incorrectos
> * Descargar y analizar registros

Siga este vínculo para ver ejemplos de almacenamiento previamente creados.

> [!div class="nextstepaction"]
> [Ejemplos de script de almacenamiento de Azure](storage-samples-blobs-cli.md)