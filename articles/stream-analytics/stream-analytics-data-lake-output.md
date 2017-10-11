---
title: Salida de Stream Analytics Data Lake Store | Microsoft Docs
description: "Configuración de la autenticación y la autorización de un Almacén de Azure Data Lake en un trabajo de Análisis de transmisiones"
keywords: 
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: ea5baafa-0054-4c70-973a-6a3a8c6eaffc
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 3d867df3ef875d5cc41de418c3d1d269ff751fda
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="stream-analytics-data-lake-store-output"></a>Salida de los Análisis de transmisiones del Almacén de Data Lake
Los trabajos de Análisis de transmisiones admiten varios métodos de salida, y uno de ellos es el [Almacén de Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/). El Almacén de Azure Data Lake es un repositorio de gran escala en toda la empresa para cargas de trabajo de análisis de macrodatos. El Almacén de Data Lake permite almacenar datos de cualquier tamaño, tipo y velocidad de ingesta para realizar análisis exploratorios y operativos.

## <a name="authorize-a-data-lake-store-account"></a>Autorización de una cuenta del Almacén de Data Lake
1. Cuando se selecciona Data Lake Store como una salida en Azure Portal, se le pedirá que autorice el uso de la instancia de Data Lake Store existente o pida acceso a Data Lake Store mediante el Portal de Azure clásico.
   
   ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)  
   
2. Si ya dispone de acceso a Data Lake Store, haga clic en "Autorizar ahora" y, durante unos segundos, se mostrará una página con el mensaje "Redirigiendo a la autorización...". Esta página se cerrará automáticamente y se le mostrará la página donde podrá configurar la salida del Almacén de Data Lake.

Si no se registró en Data Lake Store, puede hacer clic en el vínculo "Suscribirme ahora" para iniciar la solicitud, o bien siga las [instrucciones de introducción](../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="configure-the-data-lake-store-output-properties"></a>Configuración de las propiedades de salida del Almacén de Data Lake
Una vez que se haya autenticado la cuenta del Almacén de Data Lake, podrá configurar las propiedades para su salida del Almacén de Data Lake. En la siguiente tabla podrá encontrar una lista de nombres de propiedades y su descripción para configurar la salida del Almacén de Data Lake.

<table>
<tbody>
<tr>
<td><B>NOMBRE DE LA PROPIEDAD</B></td>
<td><B>DESCRIPCIÓN</B></td>
</tr>
<tr>
<td>Alias de salida</td>
<td>Se trata de un nombre descriptivo utilizado en las consultas para dirigir la salida de la consulta a este Almacén de Data Lake.</td>
</tr>
<tr>
<td>Cuenta del Almacén de Data Lake</td>
<td>El nombre de la cuenta de almacenamiento a donde está enviando la salida Aparecerá una lista de cuentas de almacén de Data Lake Store a que el usuario que ha iniciado sesión tiene acceso.</td>
</tr>
<tr>
<td>Patrón de prefijo de la ruta [<I>opcional</I>]</td>
<td>La ruta de acceso utilizada para escribir sus archivos en la cuenta del Almacén de Data Lake especificada. <BR>{date}, {time}<BR>Ejemplo 1: carpeta1/registros/{date}/{time}<BR>Ejemplo 2: carpeta1/registros/{date}</td>
</tr>
<tr>
<td>Formato de fecha [<I>opcional</I>]</td>
<td>Si el token de fecha se usa en la ruta de acceso de prefijo, puede seleccionar el formato de fecha en el que se organizan los archivos. Ejemplo: AAAA/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [<I>opcional</I>]</td>
<td>Si el token de hora se usa en la ruta de acceso de prefijo, puede seleccionar el formato de hora en el que se organizan los archivos. Actualmente, el único valor admitido es HH.</td>
</tr>
<tr>
<td>Formato de serialización de eventos</td>
<td>Formato de serialización para los datos de salida. Se admiten JSON, CSV y Avro.</td>
</tr>
<tr>
<td>Codificación</td>
<td>Si el formato CSV o JSON, debe especificarse una codificación. Por el momento, UTF-8 es el único formato de codificación compatible.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Solo se aplica para la serialización de CSV. Análisis de transmisiones admite un número de delimitadores comunes para la serialización de datos CSV. Los valores admitidos son la coma, punto y coma, espacio, tabulador y barra vertical.</td>
</tr>
<tr>
<td>Formato</td>
<td>Solo se aplica para la serialización de JSON. Separado por líneas especifica que la salida se formateará de tal forma que cada objeto JSON esté separado por una línea nueva. Matriz especifica que la salida se formateará como una matriz de objetos JSON.</td>
</tr>
</tbody>
</table>

## <a name="renew-data-lake-store-authorization"></a>Renovación de la autorización del Almacén de Data Lake
Actualmente, existe una limitación según la que el token de autenticación debe actualizarse manualmente cada 90 días para todos los trabajos con salida del Almacén de Data Lake. También tendrá que volver a autenticar la cuenta del Almacén de Data Lake si ha cambiado la contraseña desde que se creó o autenticó por última vez su trabajo. Un síntoma de este problema es la ausencia de salidas de trabajos y un error en los registros de operaciones en el que se indica que se debe volver a conceder la autorización.

Para resolver este problema, detenga su trabajo en ejecución y vaya a la salida del Almacén de Data Lake. Haga clic en el vínculo “Renovar autorización” y, durante unos segundos, se mostrará una página con el mensaje “Redirigiendo a la autorización...”. La página se cerrará automáticamente y, si la operación se realiza correctamente, indicará “La autorización se ha renovado correctamente”. Después, debe hacer clic en “Guardar” en la parte inferior de la página y podrá continuar reiniciando el trabajo desde la hora de la última detención para evitar la pérdida de datos.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)

