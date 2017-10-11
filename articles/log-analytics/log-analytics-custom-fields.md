---
title: Campos personalizados de Log Analytics | Microsoft Docs
description: "La característica Custom Fields (Campos personalizados) de Log Analytics le permite crear sus propios campos de búsqueda a partir de datos de OMS y agregarlos a las propiedades de un registro recopilado.  En este artículo, se describe el proceso que debe seguir para crear un campo personalizado y se incluye un tutorial detallado con un evento de ejemplo."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: bwren
ms.openlocfilehash: 9e02094f155eaade9bc5fb49c4fbb798e546e989
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="custom-fields-in-log-analytics"></a>Campos personalizados de Log Analytics
La característica **Custom Fields** (Campos personalizados) de Log Analytics le permite ampliar los registros existentes en el repositorio de OMS agregando sus propios campos de búsqueda.  Los campos personalizados se rellenan automáticamente a partir de los datos extraídos de otras propiedades del mismo registro.

![Introducción a los campos personalizados](media/log-analytics-custom-fields/overview.png)

Por ejemplo, en el registro que se muestra a continuación, hay información útil que está escondida en la descripción del evento.  Si estos datos se extraen en diferentes propiedades, estarán disponibles para ciertas acciones, como la ordenación y el filtrado.

![Botón Log Search (Búsqueda de registros)](media/log-analytics-custom-fields/sample-extract.png)

> [!NOTE]
> En la versión preliminar, el área de trabajo tiene un límite de 100 campos personalizados.  Este límite se ampliará cuando esta característica esté disponible con carácter general.
> 
> 

## <a name="creating-a-custom-field"></a>Creación de un campo personalizado
Cuando se crea un campo personalizado, Log Analytics necesita saber qué datos debe usar para rellenar el valor.  Para identificar rápidamente estos datos, utiliza una tecnología de Microsoft Research denominada FlashExtract.  Log Analytics es capaz de reconocer los datos que debe extraer a partir de los ejemplos proporcionados, por lo que no es necesario especificar instrucciones explícitas.

En las secciones siguientes, se describe el procedimiento necesario para crear un campo personalizado.  Al final de este artículo, encontrará un tutorial con una extracción de ejemplo.

> [!NOTE]
> El campo personalizado se rellena a medida que los registros que cumplen los criterios especificados se agregan al almacén de datos de OMS; por tanto, el campo personalizado solo aparecerá en los registros recopilados una vez que se haya creado.  El campo personalizado no se agregará a los registros que ya se encuentren en el almacén de datos en el momento de su creación.
> 
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Paso 1: Identificación de los registros que incluirán el campo personalizado
El primer paso consiste en identificar los registros que incluirán el campo personalizado.  Comience con una [búsqueda de registros estándar](log-analytics-log-searches.md) y, después, seleccione un registro que sirva de modelo para que Log Analytics aprenda de él.  Cuando especifique que va a extraer los datos en un campo personalizado, se abrirá **Field Extraction Wizard** y (Asistente para extraer campos) podrá validar y delimitar los criterios.

1. Vaya a **Log Search** (Búsqueda de registros) y utilice una [consulta para recuperar los registros](log-analytics-log-searches.md) que incluirán el campo personalizado.
2. Seleccione el registro que Log Analytics utilizará como modelo para extraer los datos y rellenar el campo personalizado.  Deberá identificar los datos que quiere extraer de este registro. Log Analytics utilizará esa información para determinar la lógica con la que rellenará el campo personalizado en todos los registros que sean similares.
3. Haga clic en el botón situado a la izquierda de una propiedad de texto del registro y seleccione **Extract fields from**(Extraer campos de).
4. A continuación, se abrirá **Field Extraction Wizard** (Asistente para extraer archivos) y el registro seleccionado aparecerá en la columna **Ejemplo principal**.  El campo personalizado se va a definir para aquellos recursos que tengan los mismos valores en las propiedades seleccionadas.  
5. Si la selección no coincide exactamente con sus expectativas, seleccione otros campos para restringir los criterios.  Para cambiar los valores de campo de los criterios, debe cancelar y seleccionar otro registro que coincida con los criterios que quiere usar.

### <a name="step-2---perform-initial-extract"></a>Paso 2: Ejecución de la extracción inicial.
Una vez identificados los registros que incluirán el campo personalizado, deberá identificar los datos que desea extraer.  Log Analytics utilizará esta información para identificar patrones parecidos en registros similares.  En el paso siguiente, podrá validar los resultados y proporcionar más detalles para que Log Analytics los utilice en su análisis.

1. Resalte el texto del registro de ejemplo con el que desea rellenar el campo personalizado.  A continuación, aparecerá un cuadro de diálogo en el que tendrá que especificar un nombre para el campo y realizar la extracción inicial.  Los caracteres **\_CF** se anexarán automáticamente.
2. Haga clic en **Extract** (Extraer) para realizar un análisis de los registros recopilados.  
3. En las secciones **Resumen**y **Resultados de búsqueda**, se muestran los resultados de la extracción para que pueda revisar si son correctos.  **Summary** (Resumen), aparecen los criterios utilizados para identificar registros y el recuento de cada uno de los valores de datos identificados.  **Search Results** (Resultados de búsqueda), aparece una lista detallada de los registros que cumplen los criterios.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Paso 3: Comprobación de la precisión de la extracción y creación de un campo personalizado
Una vez realizada la extracción inicial, Log Analytics mostrará los resultados en función de los datos que ya se han recopilado.  Si los resultados parecen correctos, puede crear el campo personalizado sin necesidad de realizar ninguna otra operación.  De lo contrario, puede delimitar los resultados para que Log Analytics mejore su lógica.

1. Si alguno de los valores de la extracción inicial no es correcto, haga clic en el icono **Editar** que encontrará junto al registro incorrecto y seleccione **Modificar este resaltado** para poder realizar los cambios.
2. La entrada se copia en la sección **Ejemplos adicionales**, bajo **Ejemplo principal**.  En esta sección, puede ajustar el texto resaltado para ayudar a Log Analytics a entender qué selección debería haber hecho.
3. Haga clic en **Extract** (Extraer) si desea utilizar esta nueva información para evaluar todos los registros existentes.  Los resultados pueden modificarse en función de esta nueva información para otros registros distintos del que acaba de modificar.
4. Continúe con las correcciones hasta que todos los registros de la extracción identifiquen correctamente los datos que van a incluirse en el nuevo campo personalizado.
5. Haga clic en **Save Extract** (Guardar extracción) cuando esté satisfecho con los resultados.  Ahora, el campo personalizado está definido, pero aún no se ha agregado a ningún registro.
6. Espere a que se recopilen los registros nuevos que cumplan los criterios y vuelva a ejecutar la búsqueda de registros. Los nuevos registros deberían incluir el campo personalizado.
7. Utilice el campo personalizado como cualquier otra propiedad del registro.  Puede usarlo para agregar y agrupar datos, e incluso para generar nueva información.

## <a name="viewing-custom-fields"></a>Presentación de los de campos personalizados
Puede ver una lista de todos los campos personalizados en el grupo de administración del icono **Settings** (Configuración) del panel de OMS.  Seleccione **Datos** y después **Campos personalizados** para obtener una lista de todos los campos personalizados del área de trabajo.  

![Custom Fields](media/log-analytics-custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Eliminación de un campo personalizado
Existen dos formas de quitar un campo personalizado.  La primera es a través de la opción **Remove** (Quitar) que aparece en cada campo cuando se consulta la lista completa, tal y como que se describió anteriormente.  El otro método consiste en recuperar un registro y hacer clic en el botón situado a la izquierda del campo.  El menú incluirá una opción para quitar el campo personalizado.

## <a name="sample-walkthrough"></a>Tutorial de ejemplo
La siguiente sección contiene un ejemplo completo que le guiará por el proceso de creación de un campo personalizado.  En este ejemplo, se extrae el nombre de servicio de los eventos de Windows que indican un cambio de estado en el servicio.  En este ejemplo, se utilizan eventos creados por el Administrador de control de servicios en el registro del sistema de equipos Windows.  Si desea seguir este ejemplo, debe [recopilar eventos de información del registro del sistema](log-analytics-data-sources-windows-events.md).

En primer lugar, escribimos la siguiente consulta para que devuelva todos los eventos del Administrador de control de servicios que tengan el identificador 7036, que es el evento que indica el inicio o la detención de un servicio.

![Consultar](media/log-analytics-custom-fields/query.png)

A continuación, seleccionamos cualquier registro con el identificador de evento 7036.

![Registro de origen](media/log-analytics-custom-fields/source-record.png)

El nombre de servicio que buscamos es el que aparece en la propiedad **RenderedDescription** , por lo que seleccionamos el botón que aparece junto a ella.

![Extraer campos](media/log-analytics-custom-fields/extract-fields.png)

Se abre **Field Extraction Wizard** (Asistente para extraer campos), donde los campos **EventLog** y **EventID** aparecen seleccionados en la columna **Ejemplo principal**.  Esto indica que el campo personalizado se va a definir para los eventos del registro del sistema que tengan el identificador 7036.  Como es suficiente, no es necesario seleccionar ningún otro campo.

![Main Example](media/log-analytics-custom-fields/main-example.png)

Ahora, resaltamos el nombre del servicio en la propiedad **RenderedDescription** y utilizamos **Servicio** para identificar el nombre del servicio.  El campo personalizado se llamará **Service_CF**.

![Título del campo](media/log-analytics-custom-fields/field-title.png)

Como podemos ver, el nombre del servicio se identifica correctamente en algunos registros, pero no en otros.   En **Resultados de búsqueda**, vemos que parte del nombre del **Adaptador de rendimiento de WMI** no aparece seleccionado.  En **Resumen**, vemos que hay cuatro registros con **DPRMA** que contienen una palabra adicional incorrecta, y dos registros identificados como **Instalador de módulos** en lugar de como **Instalador de Módulos de Windows**.  

![Search Results](media/log-analytics-custom-fields/search-results-01.png)

Vamos a comenzar con el registro **WMI Performance Adapter** (Adaptador de rendimiento de WMI).  Hacemos clic en el icono de edición y luego en **Modify this highlight**(Modificar texto resaltado).  

![Modificar texto resaltado](media/log-analytics-custom-fields/modify-highlight.png)

Ampliamos la selección para que incluya la palabra **WMI** y ejecutamos de nuevo la extracción.  

![Ejemplo adicional](media/log-analytics-custom-fields/additional-example-01.png)

Ahora, podemos ver que las entradas de **Adaptador de rendimiento de WMI** se han corregido. Log Analytics utilizará esta información para corregir los registros de **Instalador de módulos de Windows**.  Sin embargo, en la sección **Resumen**, vemos que **DPMRA** sigue sin identificarse correctamente.

![Search Results](media/log-analytics-custom-fields/search-results-02.png)

Nos desplazamos a un registro que tenga el servicio DPMRA y repetimos el mismo proceso para corregirlo.

![Ejemplo adicional](media/log-analytics-custom-fields/additional-example-02.png)

 Cuando ejecutamos de nuevo la extracción, podemos ver que ahora todos los resultados son correctos.

![Search Results](media/log-analytics-custom-fields/search-results-03.png)

También vemos que se ha creado **Service_CF**, aunque aún no se ha agregado a ningún registro.

![Recuento inicial](media/log-analytics-custom-fields/initial-count.png)

A medida que pasa el tiempo, se recopilan nuevos eventos y podemos ver que el campo **Service_CF** se va agregando a los registros que cumplen nuestros criterios.

![Resultados finales](media/log-analytics-custom-fields/final-results.png)

Ahora, podemos usar el campo personalizado como cualquier otra propiedad del registro.  Para comprobarlo, creamos una consulta que agrupe los datos en función del nuevo campo **Service_CF** y que analice qué servicios son los más activos.

![Agrupar por consulta](media/log-analytics-custom-fields/query-group.png)

## <a name="next-steps"></a>Pasos siguientes
* Si desea crear consultas con campos personalizados en función de unos criterios, obtenga más información acerca de las [búsquedas de registros](log-analytics-log-searches.md) .
* Supervise los [archivos de registro personalizados](log-analytics-data-sources-custom-logs.md) que se analizan con campos personalizados.

