---
title: "Azure Compute: extensión Diagnostics de Linux | Microsoft Docs"
description: "Instrucciones de configuración de la extensión Diagnostics de Linux (LAD) de Azure para recopilar métricas y registrar eventos de máquinas virtuales Linux que se ejecuten en Azure."
services: virtual-machines-linux
author: jasonzio
manager: anandram
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/09/2017
ms.author: jasonzio
ms.openlocfilehash: 525d706bd709ae72f2dca1c21e06db533ccf32b4
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Uso de la extensión Diagnostics de Linux para supervisar métricas y registros

En este documento se describen las versiones 3.0 y posteriores de la extensión Diagnostics de Linux.

> [!IMPORTANT]
> Para obtener información sobre las versiones 2.3 y anteriores, consulte [este documento](./classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Introducción

La extensión Diagnostics de Linux ayuda a los usuarios a supervisar el mantenimiento de máquinas virtuales Linux que se ejecuten en Microsoft Azure. Ofrece la siguiente funcionalidad:

* Recopila métricas de rendimiento del sistema de la máquina virtual y las almacena en una tabla específica de una cuenta de almacenamiento designada.
* Recupera eventos de registro de syslog y los almacena en una tabla específica de la cuenta de almacenamiento designada.
* Permite a los usuarios personalizar las métricas de datos que se recopilan y se cargan.
* Permite a los usuarios personalizar las utilidades de syslog y los niveles de gravedad de los eventos que se recopilan y se cargan.
* Permite a los usuarios cargar los archivos de registro especificados en la tabla de almacenamiento designada.
* Admite el envío de métricas y eventos de registro a puntos de conexión arbitrarios de EventHub y blobs con formato JSON en la cuenta de almacenamiento designada.

Esta extensión funciona con los dos modelos de implementación de Azure.

## <a name="installing-the-extension-in-your-vm"></a>Instalación de la extensión en la máquina virtual

Puede habilitar esta extensión mediante cmdlets de Azure PowerShell, scripts de CLI de Azure o plantillas de implementación de Azure. Para obtener más información, consulte [Funciones de la extensión](./extensions-features.md).

No se puede usar Azure portal para habilitar LAD 3.0 ni configurarlo. En su lugar, instala versión 2.3 y la configura. Los grafos y las alertas de Azure Portal funcionan con datos procedentes de ambas versiones de la extensión.

Mediante estas instrucciones de instalación y una [configuración de ejemplo descargable](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) se configura LAD 3.0 para:

* capturar las mismas métricas que las proporcionadas por LAD 2.3 y almacenarlas,
* capturar un conjunto útil de métricas del sistema de archivos (función nueva de LAD 3.0),
* capturar la recopilación predeterminada de syslog habilitada por LAD 2.3, y
* habilitar la experiencia de Azure Portal para la creación de gráficos y desencadenamiento de alertas en métricas de máquinas virtuales.

La configuración que se puede descargar es solo un ejemplo; modifíquela como corresponda para adaptarla a sus necesidades.

### <a name="prerequisites"></a>Requisitos previos

* **Versión 2.2.0 o posterior del agente Linux de Azure**. La mayoría de las imágenes de la galería de máquina virtual Linux de Azure incluyen la versión 2.2.7 o posterior. Ejecute `/usr/sbin/waagent -version` para confirmar la versión instalada en la máquina virtual. Si la máquina virtual está ejecutando una versión anterior del agente invitado, siga [estas instrucciones](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/update-agent) para actualizarla.
* **Azure CLI**. [Instale el entorno de CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) en la máquina.
* El comando wget, si aún no lo tiene: ejecute `sudo apt-get install wget`.
* Una suscripción a Azure existente con una cuenta de almacenamiento para almacenar los datos.

### <a name="sample-installation"></a>Instalación de ejemplo

Rellene las tres primeras líneas con los parámetros correctos y, a continuación, ejecute este script a modo de raíz:

```bash
# Set your Azure VM diagnostic parameters correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 9999-12-31T23:59Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

La URL de la configuración de muestreo y su contenido están sujetos a cambios. Descargue una copia del archivo JSON de configuración del portal y personalícelo en función de sus necesidades. Cualquier plantilla o automatización que genere deberían usar su propia copia en lugar de descargar dicha URL cada vez.

### <a name="updating-the-extension-settings"></a>Actualización de la configuración de la extensión

Después de cambiar la Configuración pública o protegida, ejecute el mismo comando para implementarla en la VM. Si se ha realizado cualquier modificación en la configuración, la configuración actualizada se envía a la extensión. LAD recarga la configuración y se reinicia automáticamente.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migración desde versiones anteriores de la extensión

La versión más reciente de la extensión es la **3.0**. **Todas las versiones anteriores (2.x) están en desuso y pueden retirarse a partir del 31 de julio de 2018**.

> [!IMPORTANT]
> En esta extensión se introducen importantes cambios de configuración. La finalidad de uno de estos cambios era aumentar la seguridad de la extensión; por tanto, no se pudo conservar la compatibilidad con las versiones 2.x anteriores. Además, el publicador de esta extensión es diferente del de las versiones 2.x.
>
> Para migrar a esta nueva versión de la extensión desde versiones 2.x, debe desinstalar la antigua (con el nombre de publicador anterior) y, a continuación, instalar la versión 3.

Recomendaciones:

* Instale la extensión con la actualización de versión secundaria automática habilitada.
  * En máquinas virtuales con el modelo de implementación clásica, especifique "3.*" como versión si va a instalar la extensión mediante la CLI xPLAT de Azure o PowerShell.
  * En las máquinas virtuales del modelo de implementación de Azure Resource Manager, incluya ""autoUpgradeMinorVersion": true" en la plantilla de implementación de máquina virtual.
* Use una cuenta de almacenamiento nueva o diferente para LAD 3.0. Hay varias pequeñas incompatibilidades entre LAD 2.3 y LAD 3.0 que hacen que compartir una cuenta sea problemático:
  * LAD 3.0 almacena los eventos de syslog en una tabla con un nombre diferente.
  * Las cadenas de counterSpecifier para las métricas de `builtin` son diferentes en LAD 3.0.

## <a name="protected-settings"></a>Configuración protegida

Este conjunto de información de configuración contiene información confidencial que debería protegerse de la vista pública, por ejemplo, las credenciales de almacenamiento. Esta configuración se transmite la extensión y es almacenada por esta de forma cifrada.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Nombre | Valor
---- | -----
storageAccountName | Es el nombre de la cuenta de almacenamiento en la que la extensión escribe los datos.
storageAccountEndPoint | (Opcional) es el punto de conexión que identifica la nube en la que existe la cuenta de almacenamiento. Si no hubiera configuración, LAD selecciona la nube pública de Azure (`https://core.windows.net`) de forma predeterminada. Para usar una cuenta de almacenamiento en Azure Alemania, Azure Government o Azure China, establezca este valor como corresponda.
storageAccountSasToken | Es un [token de SAS de cuenta](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) para blob services y table services (`ss='bt'`) aplicable a contenedores y objetos (`srt='co'`), que otorga permisos, los crea, los enumera, los actualiza y los escribe (`sp='acluw'`). *No* incluya el signo de interrogación principal (?).
mdsdHttpProxy | (Opcional) es información del proxy HTTP necesaria para habilitar la extensión para conectarse a la cuenta de almacenamiento y el punto de conexión especificados.
sinksConfig | (Opcional) es información sobre destinos alternativos a los que pueden enviarse métricas y eventos. La información específica de cada receptor de datos admitido por la extensión se trata en las siguientes secciones.

Es posible construir el token de SAS necesario de forma muy sencilla mediante Azure Portal.

1. Seleccione la cuenta de almacenamiento de uso general en la que quiere que escriba la extensión.
1. Seleccione "Firma de acceso compartido" de la sección Configuración del menú izquierdo.
1. Cree las secciones pertinentes, conforme a lo descrito anteriormente.
1. Haga clic en el botón "Generar SAS".

![imagen](./media/diagnostic-extension/make_sas.png)

Copie la SAS generada en el campo storageAccountSasToken. Elimine el signo de interrogación principal ("?").

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

En esta sección opcional se definen destinos adicionales a los que la extensión envía la información que recopila. La matriz "sink" contiene un objeto para cada receptor de datos adicional. El atributo "type" determina los demás atributos del objeto.

Elemento | Valor
------- | -----
name | Una cadena usada para hacer referencia a este receptor en cualquier otra parte de la configuración de la extensión.
type | Es el tipo de receptor que se va a definir. Determina los demás valores (si los hubiera) en instancias de este tipo.

La versión 3.0 de la extensión Diagnostics de Linux admite dos tipos de receptores: de EventHub y Json BLOB.

#### <a name="the-eventhub-sink"></a>El receptor de EventHub

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

La entrada "sasURL" contiene la URL completa, incluido el token SAS, para la instancia de EventHub en la que deberían publicarse los datos. LAD necesita una directiva de denominación de SAS que habilite la notificación Send. Por ejemplo:

* Cree un espacio de nombres de Event Hubs llamado `contosohub`.
* Cree un Event Hub en el espacio de nombres llamado `syslogmsgs`.
* Cree una directiva de acceso compartido en el Event Hub llamada `writer` que habilite la notificación Send.

Si creó una SAS válida hasta la medianoche en horario UTC del 1 de enero de 2018, el valor de sasURL podría ser:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Para obtener más información sobre la generación de tokens de SAS para Event Hubs, consulte [esta página web](../../event-hubs/event-hubs-authentication-and-security-model-overview.md).

#### <a name="the-jsonblob-sink"></a>El receptor de Json BLOB

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Los datos dirigidos a un receptor de Json BLOB se almacenan en blobs en Azure Storage. Cada instancia de LAD crea un blob cada hora para cada nombre de receptor. Todos los blobs siempre contienen una matriz de objeto JSON válida sintácticamente. Se agregan nuevas entradas a la matriz automáticamente. Los blobs se almacenan en un contenedor con el mismo nombre que el receptor. Las reglas de Azure Storage para los nombres de contenedores de blobs se aplican a los nombres de los receptores de Json BLOB: entre 3 y 63 caracteres alfanuméricos ASCII o guiones.

## <a name="public-settings"></a>Configuración pública

Esta estructura contiene varios bloques de valores de configuración que controlan la información recopilada por la extensión. Cada valor de configuración es opcional. Si especifica `ladCfg`, también debe especificar `StorageAccount`.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Elemento | Valor
------- | -----
StorageAccount | Es el nombre de la cuenta de almacenamiento en la que la extensión escribe los datos. Debe ser el mismo nombre que se especifique en la [Configuración protegida](#protected-settings).
mdsdHttpProxy | (Opcional) es el mismo que en la [Configuración protegida](#protected-settings). El valor público reemplaza al privado, en caso de estar establecido. Coloque elementos de configuración de proxy que contengan un secreto, como una contraseña, en la [Configuración protegida](#protected-settings).

El resto de los elementos se describen con más detalle en las secciones siguientes.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

Esta estructura opcional controla la recopilación de métricas y registros para enviarlos al servicio de Métricas de Azure y otros receptores de datos. Debe especificar `performanceCounters`, `syslogEvents` o ambos. Debe especificar la estructura de `metrics`.

Elemento | Valor
------- | -----
eventVolume | (Opcional) controla el número de particiones creadas en la tabla de almacenamiento. Debe ser uno de los siguientes valores: `"Large"`, `"Medium"` o `"Small"`. Si no se especifica, el valor predeterminado es `"Medium"`.
sampleRateInSeconds | (Opcional) es el intervalo predeterminado entre la recopilación de métricas sin procesar (sin agregar). La frecuencia de muestreo más pequeña admitida es de 15 segundos. Si no se especifica, el valor predeterminado es `15`.

#### <a name="metrics"></a>Métricas

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Elemento | Valor
------- | -----
resourceId | El identificador de recurso de Azure Resource Manager de la VM o del conjunto de escalado de máquinas virtuales al que pertenezca la VM. Esta configuración también debe especificarse en caso de usar cualquier receptor de Json BLOB en la configuración.
scheduledTransferPeriod | La frecuencia con la que se computarán métricas agregadas y se transferirán a Métricas de Azure, expresadas en forma de intervalo de tiempo ISO 8601. El período de transferencia mínimo es de 60 segundos, es decir, PT1M. Debe especificar al menos un elemento scheduledTransferPeriod.

Se recopilan muestras de las métricas especificadas en la sección performanceCounters cada 15 segundos o a la frecuencia de muestreo definida para el contador de forma explícita. Si aparecen varias frecuencias de scheduledTransferPeriod (como en el ejemplo), cada agregación se computa de forma independiente.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

Esta sección opcional controla la recopilación de métricas. Se agregan muestras sin procesar para cada elemento [scheduledTransferPeriod](#metrics) a fin de generar estos valores:

* mean
* minimum
* maximum
* last-collected value
* Número de muestras sin procesar usadas para computar el agregado

Elemento | Valor
------- | -----
sinks | (Opcional) es una lista separada por comas de nombres de receptores a los que LAD envía los resultados de las métricas agregadas. Todas las métricas agregadas se publican en cada receptor indicado. Consulte [sinksConfig](#sinksconfig). Ejemplo: `"EHsink1, myjsonsink"`.
type | Identifica el proveedor real de la métrica.
class | Junto con "counter", identifica la métrica específica en el espacio de nombres del proveedor.
counter | Junto con "class", identifica la métrica específica en el espacio de nombres del proveedor.
counterSpecifier | Identifica la métrica específica en el espacio de nombres de Métricas de Azure.
condition | (Opcional) selecciona una instancia específica del objeto al que se aplica la métrica o selecciona la agregación en todas las instancias de este objeto. Para obtener más información, consulte las [definiciones de las métricas](#metrics-supported-by-builtin) de `builtin`.
sampleRate | Intervalo de ISO 8601 que establece la frecuencia de recopilación de muestras sin procesar de esta métrica. Si no se establece, el intervalo de recopilación se establece a partir del valor de [sampleRateInSeconds](#ladcfg). La frecuencia de muestreo más corta admitida es de 15 segundos (PT15S).
unit | Debería ser una de estas cadenas: "Count", "Bytes", "Seconds", "Percent", "CountPerSecond", "BytesPerSecond" o "Millisecond". Define la unidad para la métrica. Los consumidores de los datos recopilados esperan que los valores de los datos recopilados coincidan con esta unidad. LAD omite este campo.
DisplayName | La etiqueta (en el idioma especificado por la configuración regional correspondiente) que va a adjuntarse a estos datos en el servicio de Métricas de Azure. LAD omite este campo.

El elemento counterSpecifier es un identificador arbitrario. Los consumidores de métricas, como la característica de creación de grafos y desencadenamiento alertas de Azure Portal, usan counterSpecifier como "clave" para identificar una métrica o instancia de una métrica. Para las métricas de `builtin`, se recomienda usar valores counterSpecifier que empiecen por `/builtin/`. Si va a recopilar una instancia específica de una métrica, se recomienda adjuntar el identificador de la instancia al valor de counterSpecifier. Estos son algunos ejemplos:

* `/builtin/Processor/PercentIdleTime`: tiempo de inactividad promediado en todos los núcleos
* `/builtin/Disk/FreeSpace(/mnt)`: espacio libre para el elemento /mnt filesystem
* `/builtin/Disk/FreeSpace`: espacio libre promediado entre todos los elementos filesystem montados

Ni LAD ni Azure Portal esperan que el valor de counterSpecifier concuerde con ningún patrón. Sea coherente en el modo de construcción de valores counterSpecifier.

Si especifica `performanceCounters`, LAD siempre escribe datos en una tabla en Azure Storage. Puede hacer que se escriban los mismos datos a blobs JSON o Event Hubs, pero no puede deshabilitar el almacenamiento de datos en una tabla. Todas las instancias de la extensión de diagnóstico configuradas para usar el mismo nombre de cuenta de almacenamiento y el mismo punto de conexión agregan sus métricas y registros a la misma tabla. Si hay demasiadas máquinas virtuales escribiendo en la misma partición de tabla, Azure puede limitar las escrituras a dicha partición. El valor eventVolume hace que las entradas se repartan entre 1 (Small), 10 (Medium) o 100 (Large) particiones diferentes. Por lo general, un valor "Medium" es suficiente para asegurarse de que el tráfico no se vea limitado. La característica de las métricas de Azure de Azure Portal usa los datos de esta tabla para crear gráficos o desencadenar alertas. El nombre de tabla es la concatenación de las siguientes cadenas:

* `WADMetrics`
* El elemento "scheduledTransferPeriod" para los valores agregados almacenados en la tabla
* `P10DV2S`
* Una fecha en formato "AAAAMMDD", que cambia cada 10 días

Algunos ejemplos son `WADMetricsPT1HP10DV2S20170410` y `WADMetricsPT1MP10DV2S20170609`.

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

Esta sección opcional controla la recopilación de eventos de registro de syslog. Si se omite la sección, no se captura ningún evento de syslog.

La recopilación de syslogEventConfiguration tiene una entrada para cada recurso de syslog de interés. Si el valor de minSeverity es "NONE" para un recurso en particular, o si el recurso no aparece en el elemento, no se captura ningún evento de dicho recurso.

Elemento | Valor
------- | -----
sinks | Es una lista separada por comas de nombres de receptores en los que se publican los eventos de registros individuales. Todos los eventos de registro que coincidan con las restricciones de syslogEventConfiguration se publican en cada receptor indicado. Por ejemplo, "EHforsyslog".
facilityName | Es un nombre de recurso de syslog (como "LOG\_USER" o "LOG\_LOCAL0"). Consulte la sección "facility" de la [página man de syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) para obtener la lista completa.
minSeverity | Es un nivel de gravedad de syslog (como "LOG\_ERR" o "LOG\_INFO"). Consulte la sección "level" de la [página man de syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) para obtener la lista completa. La extensión captura eventos enviados al recurso al nivel especificado o uno superior.

Si especifica `syslogEvents`, LAD siempre escribe datos en una tabla en Azure Storage. Puede hacer que se escriban los mismos datos a blobs JSON o Event Hubs, pero no puede deshabilitar el almacenamiento de datos en una tabla. El compartimiento de la creación de particiones para esta tabla es el mismo descrito para `performanceCounters`. El nombre de tabla es la concatenación de las siguientes cadenas:

* `LinuxSyslog`
* Una fecha en formato "AAAAMMDD", que cambia cada 10 días

Algunos ejemplos son `LinuxSyslog20170410` y `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Esta sección opcional controla la exclusión de consultas [OMI](https://github.com/Microsoft/omi) arbitrarias.

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Elemento | Valor
------- | -----
espacio de nombres | (Opcional) es el espacio de nombres OMI en el que la consulta debería ejecutarse. Si no se especifica, el valor predeterminado es "root/scx", implementado por los [proveedores multiplataforma de System Center](http://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation).
query | Es la consulta de OMI que va a ejecutarse.
table | (Opcional) es la tabla de almacenamiento de Azure, en la cuenta de almacenamiento designada (consulte [Configuración protegida](#protected-settings)).
frequency | (Opcional) es el número de segundos entre la ejecución de la consulta. El valor predeterminado es de 300 (5 minutos) y el mínimo es de 15 segundos.
sinks | (Opcional) es una lista separada por comas de nombres de receptores adicionales en los que deberían publicarse los resultados de las métricas de muestra sin procesar. Ni la extensión ni Métricas de Azure computan ninguna agregación de estos ejemplos sin procesar.

Deben especificarse "table", "sinks" o ambos.

### <a name="filelogs"></a>fileLogs

Controla la captura de los archivos de registro. LAD captura nuevas líneas de texto a medida que se escriben en el archivo y las escribe en filas de tabla o en cualquier receptor especificado (de JsonBlob o EventHub).

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Elemento | Valor
------- | -----
file | Es la ruta de acceso completa del archivo de registro que va a inspeccionarse y capturarse. La ruta de acceso debe denominar un único archivo. No puede denominar un directorio ni contener caracteres comodín.
table | (Opcional) es la tabla de Azure Storage en la cuenta de almacenamiento designada (especificada en la configuración protegida) en la que se escriben nuevas líneas de la "cola" del archivo.
sinks | (Opcional) es una lista separada por combas de nombres de receptores adicionales a la que se envían líneas de registro.

Deben especificarse "table", "sinks" o ambos.

## <a name="metrics-supported-by-the-builtin-provider"></a>Métricas admitidas por el proveedor de builtin

El proveedor de métricas builtin es una fuente de métricas del máximo interés para una gran cantidad de usuarios. Estas métricas se dividen en cinco clases amplias:

* Procesador
* Memoria
* Red
* Filesystem
* Disco

### <a name="builtin-metrics-for-the-processor-class"></a>Métricas builtin para la clase Processor

La clase de métricas Processor proporciona información sobre el uso del procesador en la máquina virtual. Al agregar porcentajes, el resultado es el promedio de todas las CPU. En una máquina virtual de dos núcleos, si uno de ellos estaba activo al 100 % y el otro, inactivo al 100 %, el valor de PercentIdleTime sería de 50. Si la actividad de cada núcleo era del 50 % durante el mismo período, el resultado indicado sería de 50. En una máquina virtual de cuatro núcleos, si uno de ellos estaba activo al 100 % y todos los demás, inactivos, el valor de PercentIdleTime sería de 75.

counter | Significado
------- | -------
PercentIdleTime | Porcentaje de tiempo del período de agregación durante el que los procesadores ejecutaban el bucle de inactividad del kernel
PercentProcessorTime | Porcentaje de tiempo de ejecución de un subproceso no inactivo
PercentIOWaitTime | Porcentaje de tiempo de espera para la finalización de operaciones de E/S
PercentInterruptTime | Porcentaje de tiempo de ejecución de interrupciones de hardware o software y DPC (llamadas a procedimiento aplazadas)
PercentUserTime | Del tiempo no inactivo durante el período de agregación, el porcentaje empleado en modo de usuario en prioridad normal
PercentNiceTime | Del tiempo no inactivo, el porcentaje empleado en prioridad disminuida (nice)
PercentPrivilegedTime | Del tiempo no inactivo, el porcentaje empleado en modo privilegiado (kernel)

Los cuatro primeros contadores deberían sumar un 100 %. Los tres últimos contadores también suman 100 %. Subdividen la suma de PercentProcessorTime, PercentIOWaitTime y PercentInterruptTime.

Para obtener un único agregado de métricas de todos los procesadores, establezca `"condition": "IsAggregate=TRUE"`. Para obtener una métrica para un procesador específico, como el segundo procesador lógico de una máquina virtual de cuatro núcleos, establezca `"condition": "Name=\\"1\\""`. Los números de procesador lógico se encuentran en el rango `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>Métricas builtin para la clase Memory

La clase de métricas Memory proporciona información sobre utilización, paginación e intercambio de memoria.

counter | Significado
------- | -------
AvailableMemory | Memoria física disponible en MiB
PercentAvailableMemory | Memoria física disponible en forma de porcentaje del total de memoria
UsedMemory | Memoria física en uso (MiB)
PercentUsedMemory | Memoria física en uso en forma de porcentaje del total de memoria
PagesPerSec | Paginación total (lectura y escritura)
PagesReadPerSec | Páginas leídas desde la memoria auxiliar (archivo de intercambio, archivo de programa, archivo asignado, etc.)
PagesWrittenPerSec | Páginas escritas en la memoria auxiliar (archivo de intercambio, archivo asignado, etc.)
AvailableSwap | Espacio de intercambio sin usar (MiB)
PercentAvailableSwap | Espacio de intercambio sin usar en forma de porcentaje del total de intercambio
UsedSwap | Espacio de intercambio (MiB) en uso
PercentUsedSwap | Espacio de intercambio en uso en forma de porcentaje del total de intercambio

Esta clase de métricas tiene una sola instancia. El atributo "condition" no tiene valores de configuración útiles y debería omitirse.

### <a name="builtin-metrics-for-the-network-class"></a>Métricas builtin para la clase Network

La clase de métricas Network proporciona información sobre la actividad de red de una interfaz de red concreta desde el arranque. LAD no expone las métricas de ancho de banda, las cuales pueden recuperarse a partir de métricas de host.

counter | Significado
------- | -------
BytesTransmitted | Total de bytes enviados desde el arranque
BytesReceived | Total de bytes recibidos desde el arranque
BytesTotal | Total de bytes enviados o recibidos desde el arranque
PacketsTransmitted | Total de paquetes enviados desde el arranque
PacketsReceived | Total de paquetes recibidos desde el arranque
TotalRxErrors | Número de errores de recepción desde el arranque
TotalTxErrors | Número de errores de transmisión desde el arranque
TotalCollisions | Número de colisiones notificadas por los puertos de red desde el arranque

 Aunque esta clase está instanciada, LAD no admite la captura de agregados de métricas Network de todos los dispositivos de red. Para obtener las métricas de una interfaz instanciada, como eth0, establezca `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-filesystem-class"></a>Métricas builtin para la clase Filesystem

La clase de métricas Filesystem proporciona información sobre el uso del sistema de archivos. Los valores absolutos y porcentuales se notifican como se hubieran mostrado a un usuario normal (no raíz).

counter | Significado
------- | -------
FreeSpace | Espacio disponible en disco en bytes
UsedSpace | Espacio usado en disco en bytes
PercentFreeSpace | Porcentaje de espacio libre
PercentUsedSpace | Porcentaje de espacio usado
PercentFreeInodes | Porcentaje de inodes sin usar
PercentUsedInodes | Porcentaje de inodes asignados (en uso) sumado de todos los sistemas de archivos
BytesReadPerSecond | Bytes leídos por segundo
BytesWrittenPerSecond | Bytes escritos por segundo
BytesPerSecond | Bytes leídos o escritos por segundo
ReadsPerSecond | Operaciones de lectura por segundo
WritesPerSecond | Operaciones de escritura por segundo
TransfersPerSecond | Operaciones de lectura o escritura por segundo

Los valores agregados de todos los archivos del sistema pueden obtenerse estableciendo `"condition": "IsAggregate=True"`. Los valores para un sistema de archivos montado específico, como "/mnt", pueden obtenerse estableciendo `"condition": 'Name="/mnt"'`.

### <a name="builtin-metrics-for-the-disk-class"></a>Métricas builtin para la clase Disk

La clase de métricas Disk proporciona información sobre el uso de dispositivos de disco. Estas estadísticas se aplican a toda la unidad. Si hay varios sistemas de archivos en un dispositivo, los contadores para dicho dispositivo están, de hecho, agregados en todos ellos.

counter | Significado
------- | -------
ReadsPerSecond | Operaciones de lectura por segundo
WritesPerSecond | Operaciones de escritura por segundo
TransfersPerSecond | Total de operaciones por segundo
AverageReadTime | Promedio de segundos por operación de lectura
AverageWriteTime | Promedio de segundos por operación de escritura
AverageTransferTime | Promedio de segundos por operación
AverageDiskQueueLength | Promedio de operaciones de disco en cola
ReadBytesPerSecond | Número de bytes leídos por segundo
WriteBytesPerSecond | Número de bytes escritos por segundo
BytesPerSecond | Número de bytes leídos o escritos por segundo

Los valores agregados de todos los discos pueden obtenerse estableciendo `"condition": "IsAggregate=True"`. Para obtener información para un dispositivo en concreto (por ejemplo, /dev/sdf1), establezca `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Instalación y configuración de LAD 3.0 a través de CLI

Si suponemos que la configuración protegida se encuentra en el archivo PrivateConfig.json y que la información de configuración pública está en PublicConfig.json, ejecute este comando:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

El comando supone que se está usando el modo de Administración de recursos de Azure (ARM) de la CLI de Azure. Para configura LAD para máquinas virtuales con el modelo de implementación clásica (ASM), cambie al modo "asm" (`azure config mode asm`) y omita el nombre del grupo de recursos en el comando. Para obtener más información, consulte la [documentación de la CLI multiplataforma](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Una configuración de LAD 3.0 de ejemplo

Conforme a las anteriores definiciones, a continuación hay un ejemplo de configuración de extensión de LAD 3.0 con una explicación. Para aplicar este ejemplo a su caso, debería usar su propio nombre de cuenta de almacenamiento, token de SAS de cuenta y tokens de SAS de Event Hubs.

### <a name="privateconfigjson"></a>PrivateConfig.json

Estos valores privados configuran lo siguiente:

* Una cuenta de almacenamiento
* Un token de SAS de cuenta que coincida
* Varios receptores (Json BLOB o Event Hubs con tokens SAS)

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="publicconfigjson"></a>PublicConfig.json

Estos valores públicos hacen que LAD:

* Cargue métricas de porcentaje de tiempo de procesador y espacio usado en disco en la tabla `WADMetrics*`.
* Cargue mensajes del recurso de syslog "user" y la gravedad "info" en la tabla `LinuxSyslog*`.
* Cargue resultados sin formato de consultas OMI (PercentProcessorTime y PercentIdleTime) en la tabla llamada `LinuxCPU`.
* Cargue líneas anexadas en el archivo `/var/log/myladtestlog` en la tabla `MyLadTestLog`.

En cualquier caso, también se cargan datos en:

* Azure Blob Storage (el nombre del contenedor es el definido en el receptor de JSON Blob)
* El punto de conexión de Event Hubs (especificado en el receptor de Event Hubs)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "sampleRateInSeconds": 15,
  "ladCfg": {
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

El elemento `resourceId` de la configuración debe coincidir con el de la VM o el del conjunto de escalado de máquinas virtuales.

* La característica de creación de gráficos y desencadenamiento de alertas de métricas de la plataforma de Azure conoce el elemento resourceId de la máquina virtual en la que se esté trabajando. Espera encontrar los datos de la máquina virtual mediante el elemento resourceId de la clave de búsqueda.
* Si usa el escalado automático de Azure, el elemento resourceId de la configuración del escalado automático debe coincidir con el usado por LAD.
* El elemento resourceId se compila en los nombres de instancias de JSON Blob escritas por LAD.

## <a name="view-your-data"></a>Consulta de los datos

Use Azure Portal para consultar datos de rendimiento o establecer alertas:

![imagen](./media/diagnostic-extension/graph_metrics.png)

Los datos de `performanceCounters` se almacenan en una tabla de Azure Storage. Las API de Azure Storage están disponibles para múltiples lenguajes y plataformas.

Los datos enviados a receptores de Json BLOB se almacenan en blobs de la cuenta de almacenamiento a los que se dio nombre en la [Configuración protegida](#protected-settings). Puede consumir los datos de blob con cualquier API de Azure Blob Storage.

Además, puede usar las siguientes herramientas de la interfaz de usuario para acceder a los datos de Azure Storage:

* Explorador de servidores de Visual Studio.
* [Explorador de Microsoft Azure Storage](https://azurestorageexplorer.codeplex.com/ "Explorador de Azure Storage").

Esta instantánea de una sesión del Explorador de Microsoft Azure Storage muestra las tablas y los contenedores de Azure Storage generados a partir de una extensión de LAD 3.0 configurada correctamente en una máquina virtual de prueba. La imagen no coincide exactamente con la [configuración de ejemplo de LAD 3.0](#an-example-lad-30-configuration).

![imagen](./media/diagnostic-extension/stg_explorer.png)

Consulte los [documentos de EventHubs](../../event-hubs/event-hubs-what-is-event-hubs.md) pertinentes para obtener información sobre cómo consumir mensajes publicados en un punto de conexión de EventHubs.

## <a name="next-steps"></a>Pasos siguientes

* Cree alertas de métricas de [Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) para las métricas que recopile.
* Cree [gráficos de supervisión](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para las métricas.
* Obtenga información sobre cómo [crear un conjunto de escalado de máquinas virtuales](/azure/virtual-machines/linux/tutorial-create-vmss) con sus propias métricas para controlar el escalado automático.
