---
title: "Recopilación de registros personalizados en Azure Log Analytics (OMS) | Microsoft Docs"
description: "Log Analytics puede recopilar eventos de archivos de texto en equipos Windows y Linux.  En este artículo se describe cómo definir un nuevo registro personalizado y detalles de los registros que crean en el área de trabajo de Log Analytics."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: aca7f6bb-6f53-4fd4-a45c-93f12ead4ae1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: bwren
ms.openlocfilehash: 401fbb39194a24721274f55f0fc2a4cdc235a32b
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="custom-logs-in-log-analytics"></a>Registros personalizados de Log Analytics
El origen de datos de registros personalizados en Log Analytics permite recopilar eventos de archivos de texto en equipos Windows y Linux. Muchas aplicaciones registran información en archivos de texto, en lugar de los servicios de registro estándar, como el registro de eventos de Windows o Syslog.  Una vez recopilada la información, puede analizar cada entrada del registro en campos individuales mediante la característica [Campos personalizados](log-analytics-custom-fields.md) de Log Analytics.

![Recopilación de registros personalizados](media/log-analytics-data-sources-custom-logs/overview.png)

Los archivos de registro que se van a recopilar deben cumplir los criterios siguientes.

- El registro debe tener una sola entrada por línea o usar una marca de tiempo que coincida con uno de los formatos siguientes al principio de cada entrada.

    AAAA-MM-DD HH:MM:SS <br>M/D/AAAA HH:MM:SS AM/PM <br>Lun DD,AAAA HH:MM:SS

- El archivo de registro no debe permitir actualizaciones circulares, en las que el archivo se sobrescribe con nuevas entradas.
- El archivo de registro debe utilizar la codificación ASCII o UTF-8.  No se admiten otros formatos, como UTF-16.

>[!NOTE]
>Si hay entradas duplicadas en el archivo de registro, Log Analytics los recoge.  Sin embargo, los resultados de búsqueda serán incoherentes donde los resultados del filtro muestren más eventos que el recuento de resultados.  Es importante que valide el registro para determinar si la aplicación que crea está causando este comportamiento y solucionarlo si es posible antes de crear la definición de la colección de registros personalizada.  
>
  
## <a name="defining-a-custom-log"></a>Definición de un registro personalizado
Utilice el procedimiento siguiente para definir un archivo de registro personalizado.  Desplácese hasta el final de este artículo para ver un tutorial con un ejemplo de cómo agregar un registro personalizado.

### <a name="step-1-open-the-custom-log-wizard"></a>Paso 1. Apertura del Asistente para registros personalizados
El Asistente para registros personalizados se ejecuta en Azure Portal y le permite definir un nuevo registro personalizado para la recopilación.

1. En Azure Portal, seleccione **Log Analytics** > el área de trabajo > **Configuración avanzada**.
2. Haga clic en **Datos** > **Registros personalizados**.
3. De forma predeterminada, todos los cambios realizados en la configuración se insertan automáticamente en todos los agentes.  En el caso de los agentes de Linux, se envía un archivo de configuración al recopilador de datos Fluentd.  Si desea modificar este archivo manualmente en cada uno de los agentes de Linux, desactive la casilla *Apply below configuration to my Linux machines*(Aplicar la configuración siguiente a mis equipos Linux).
4. Haga clic en **Agregar+** para abrir el Asistente para registros personalizados.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Paso 2: Carga y análisis de un registro de ejemplo
Para empezar, cargue una muestra del registro personalizado.  El Asistente analizará y mostrará las entradas de este archivo para que las valide.  Log Analytics utilizará el delimitador especificado para identificar cada registro.

**Nueva línea** es el delimitador predeterminado y se utiliza para los archivos de registro que tienen una sola entrada por línea.  Si la línea empieza con una fecha y hora en uno de los formatos disponibles, puede especificar un delimitador de **marca de tiempo** que admita entradas que abarcan más de una línea.

Si se usa un delimitador de marca de tiempo, la propiedad TimeGenerated de cada registro almacenada en Log Analytics se rellenará con la fecha y la hora especificadas para esa entrada en el archivo de registro.  Si se usa un delimitador de nueva línea, TimeGenerated se rellena con la fecha y hora en que Log Analytics recopila la entrada.


1. Haga clic en **Browse** (Examinar) y vaya a un archivo de ejemplo.  Tenga en cuenta que este botón puede llamarse **Choose File** (Elegir archivo) en algunos exploradores.
2. Haga clic en **Next**.
3. El Asistente para registros personalizados cargará el archivo y mostrará los registros que identifique.
4. Cambie el delimitador que se utiliza para identificar un registro nuevo y seleccione el delimitador que mejor identifica las entradas en el archivo de registro.
5. Haga clic en **Next**.

### <a name="step-3-add-log-collection-paths"></a>Paso 3: Incorporación de rutas de recopilación de registros
Debe definir una o más rutas de acceso en el agente para colocar el registro personalizado.  Puede proporcionar un nombre y una ruta de acceso específicos para el archivo de registro, o bien puede especificar una ruta de acceso con un carácter comodín para el nombre.  Esto admite aplicaciones que crean un archivo nuevo cada día o cuando un archivo alcanza un tamaño determinado.  También puede proporcionar varias rutas de acceso para un solo archivo de registro.

Por ejemplo, una aplicación puede crear un archivo de registro cada día con la fecha incluida en el nombre, como registro20100316.txt. Un patrón para dicho registro podría ser *registro\*.txt*, que se aplicará a cualquier archivo de registro que siga el esquema de asignación de nombres de la aplicación.

La tabla siguiente proporciona ejemplos de patrones válidos para especificar diferentes archivos de registro.

| DESCRIPCIÓN | Ruta de acceso |
|:--- |:--- |
| Todos los archivos en *C:\Logs* con la extensión .txt en el agente de Windows |C:\Logs\\\*.txt |
| Todos los archivos en *C:\Logs* con un nombre que empieza con "registro" y una extensión .txt en el agente de Windows |C:\Logs\log\*.txt |
| Todos los archivos en */var/log/audit* con la extensión .txt en el agente de Linux |/var/log/audit/*.txt |
| Todos los archivos en */var/log/audit* con un nombre que empieza con "registro" y una extensión .txt en el agente de Linux |/var/log/audit/log\*.txt |

1. Seleccione Windows o Linux para especificar qué formato de ruta de acceso va a agregar.
2. Escriba la ruta de acceso y haga clic en el botón **+** .
3. Repita el proceso para rutas de acceso adicionales.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Paso 4 Suministro de un nombre y una descripción del registro
El nombre que especifique se utilizará para el tipo de registro como se describió anteriormente.  Siempre finalizará con _CL para distinguirlo como un registro personalizado.

1. Escriba un nombre para el registro.  El sufijo **\_CF** se anexa automáticamente.
2. Agregue una **Descripción**opcional.
3. Haga clic en **Next** (Siguiente) para guardar la definición del registro personalizado.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Paso 5. Comprobación de que se recopilan los registros personalizados
Los datos iniciales de un nuevo registro personalizado pueden tardar hasta una hora en aparecer en Log Analytics.  Empezará a recopilar las entradas de los registros que se encuentran en la ruta de acceso que ha especificado a partir del punto que definió en el registro personalizado.  No mantendrá las entradas que ha cargado durante la creación de registro personalizado, pero recopilará entradas ya existentes en los archivos de registro que encuentra.

Una vez que Log Analytics empieza a recopilar del registro personalizado, sus registros estarán disponibles con una búsqueda de registros.  Utilice el nombre que asignó al registro personalizado como **Type** en la consulta.

> [!NOTE]
> Si falta la propiedad RawData en la búsqueda, puede ser necesario cerrar y volver a abrir el explorador.
>
>

### <a name="step-6-parse-the-custom-log-entries"></a>Paso 6. Análisis de las entradas del registro personalizado
La entrada de registro completa se almacenará en una sola propiedad denominada **RawData**.  Probablemente, deseará separar las diferentes partes de información de cada entrada en propiedades individuales almacenadas en el registro.  Esto se hace mediante la característica [Campos personalizados](log-analytics-custom-fields.md) de Log Analytics.

No se proporcionan aquí pasos detallados para analizar las entradas del registro personalizado.  Consulte la documentación de [campos personalizados](log-analytics-custom-fields.md) para ver esta información.

## <a name="removing-a-custom-log"></a>Eliminación de un registro personalizado
Use el proceso siguiente en Azure Portal para eliminar un registro personalizado que haya definido anteriormente.

1. En el menú **Datos** en **Configuración avanzada** del área de trabajo, seleccione **Registros personalizados** para enumerar todos los registros personalizados.
2. Haga clic en la opción **Quitar** situada junto al registro personalizado que desea quitar.


## <a name="data-collection"></a>Colección de datos
Log Analytics recopilará nuevas entradas de cada registro personalizado aproximadamente cada 5 minutos.  El agente registrará su lugar en cada archivo de registro del que se recopila.  Si el agente queda sin conexión durante un período, Log Analytics recopilará entradas desde donde quedó, incluso si se crearon mientras el agente estaba sin conexión.

Todo el contenido de la entrada del registro se escribe en una sola propiedad denominada **RawData**.  Se pueden analizar en varias propiedades que se pueden analizar y buscar por separado mediante la definición de [campos personalizados](log-analytics-custom-fields.md) después de haber creado el registro personalizado.

## <a name="custom-log-record-properties"></a>Propiedades de las entradas del registro personalizado
Las entradas del registro personalizado tienen un tipo con el nombre del registro que asigne y las propiedades en la tabla siguiente.

| Propiedad | DESCRIPCIÓN |
|:--- |:--- |
| TimeGenerated |Fecha y hora en las que Log Analytics recopiló el registro.  Si el registro usa un delimitador basado en el tiempo, es el tiempo recopilado en la entrada. |
| SourceSystem |Tipo de agente del que se recopiló el registro. <br> OpsManager: agente de Windows, ya sea una conexión directa o System Center Operations Manager <br> Linux: todos los agentes de Linux. |
| RawData |Texto completo de la entrada recopilada. |
| ManagementGroupName |Nombre del grupo de administración de agentes de System Center Operations Manager.  En el caso de los otros agentes, es AOI-\<id. de área de trabajo\>. |

## <a name="log-searches-with-custom-log-records"></a>Búsquedas de registros con entradas de registros personalizados
Las entradas de los registros personalizados se almacenan en el área de trabajo de Log Analytics de la misma forma que las entradas de cualquier otro origen de datos.  Tienen un tipo que coincide con el nombre proporcionado al definir el registro, por lo que puede utilizar la propiedad Type en la búsqueda para recuperar registros recopilados de un registro específico.

La tabla siguiente proporciona distintos ejemplos de búsquedas de registros que recuperan entradas de los registros personalizados.

| Consultar | DESCRIPCIÓN |
|:--- |:--- |
| MyApp_CL |Todos los eventos de un registro personalizado llamado MyApp_CL. |
| MyApp_CL &#124; donde Severity_CF=="error" |Todos los eventos de un registro personalizado llamado MyApp_CL con el valor *error* en un campo personalizado llamado *Severity_CF*. |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Ejemplo de tutorial de agregar un registro personalizado
La siguiente sección le guiará por un ejemplo de creación de un registro personalizado.  El registro de ejemplo que se recopila tiene una sola entrada en cada línea que empieza con una fecha y hora, después, campos delimitados por comas para el código, el estado y el mensaje.  A continuación se muestran varias entradas de ejemplo.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Carga y análisis de un registro de ejemplo
Se proporciona uno de los archivos de registro, y puede ver los eventos que se recopilarán.  En este caso, una nueva línea es un delimitador suficiente.  Sin embargo, si una sola entrada en el registro puede abarcar varias líneas, es necesario usar un delimitador de marca de tiempo.

![Carga y análisis de un registro de ejemplo](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Incorporación de rutas de recopilación de registros
Los archivos de registro se colocarán en *C:\MyApp\Logs*.  Se creará un archivo nuevo cada día con un nombre que incluye la fecha con el patrón *appAAAAMMDD.log*.  Un patrón suficiente para este registro sería *C:\MyApp\Logs\\\*.log*.

![Ruta de recopilación de registros](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Suministro de un nombre y una descripción del registro
Use el nombre *MyApp_CL* y escriba una descripción en el campo **Descripción**.

![Nombre de registro](media/log-analytics-data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Comprobación de que se recopilan los registros personalizados
Utilice una consulta *Type=MyApp_CL* para devolver todas las entradas de registro del registro recopilado.

![Consulta del registro sin campos personalizados](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Análisis de las entradas del registro personalizado
Los campos personalizados se usan para definir los campos *EventTime* (Hora del evento), *Code* (Código), *Status* (Estado) y *Message* (Mensaje) y poder ver la diferencia en los registros devueltos por la consulta.

![Consulta del registro con campos personalizados](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>pasos siguientes
* Utilice [Campos personalizados](log-analytics-custom-fields.md) para analizar las entradas del registro personalizado en campos individuales.
* Obtenga información acerca de las [búsquedas de registros](log-analytics-log-searches.md) para analizar los datos recopilados de soluciones y orígenes de datos.
