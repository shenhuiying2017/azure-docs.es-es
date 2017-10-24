---
title: "Implementación del inyector de Azure Log Analytics para la supervisión de Cloud Foundry | Microsoft Docs"
description: "Guía paso a paso sobre cómo implementar el inyector de Cloud Foundry Loggregator para Azure Log Analytics. Use el inyector para supervisar las métricas de rendimiento y mantenimiento del sistema Cloud Foundry."
services: virtual-machines-linux
documentationcenter: 
author: ningk
manager: timlt
editor: 
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: 0d13d39d2921c51c537534a5b000564a9df91880
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Implementar el inyector de Azure Log Analytics para la supervisión del sistema Cloud Foundry

[Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) es un servicio de Microsoft [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/) (OMS). Sirve para recopilar y analizar los datos que se generan en los entornos tanto locales como en la nube.

El inyector de Log Analytics es un componente de Cloud Foundry (CF) que reenvía las métricas del conjunto predeterminado de resultados de [Cloud Foundry Loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) a Log Analytics. Con el inyector, puede recopilar, ver y analizar las métricas de mantenimiento y rendimiento del sistema CF en varias implementaciones.

En este documento, aprenderá a implementar el inyector en su entorno de CF y, después, tendrá acceso a los datos desde la consola de OMS de Log Analytics.

## <a name="prerequisites"></a>Requisitos previos

Los pasos siguientes son requisitos previos para implementar el inyector.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Implementar un entorno de CF o de Pivotal Cloud Foundry en Azure

Puede usar el inyector con una implementación de CF de código abierto o con una implementación de Pivotal Cloud Foundry (PCF).

* [Implementar Cloud Foundry en Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Implementar Pivotal Cloud Foundry en Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Instalar las herramientas de línea de comandos de Cloud Foundry para implementar el inyector

El inyector se ejecuta como una aplicación en el entorno de CF. Necesita la CLI de CF para implementar la aplicación.

El inyector también necesita permiso de acceso al conjunto predeterminado de resultados de Loggregator y a Cloud Controller. Para crear y configurar el usuario, necesita el servicio User Account and Authentication (UAA).

* [Instalar la CLI de Cloud Foundry](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Instalar el cliente de línea de comandos de UAA de Cloud Foundry](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Antes de configurar el cliente de línea de comandos de UAA, asegúrese de que Rubygems está instalado.

### <a name="3-create-an-oms-workspace-in-azure"></a>3. Crear un área de trabajo de OMS en Azure

Puede crear el área de trabajo de OMS manualmente o con una plantilla. Cargue las vistas y alertas preconfiguradas de OMS después de terminar de implementar el inyector.

Crear un área de trabajo manualmente:

1. En Azure Portal, busque la lista de servicios en Azure Marketplace y, después, seleccione Log Analytics.
2. Seleccione **Crear** y, después, seleccione opciones para los elementos siguientes:

   * **Área de trabajo de OMS**: escriba un nombre para el área de trabajo.
   * **Suscripción**: si tiene varias suscripciones, elija la misma que la de la implementación de CF.
   * **Grupo de recursos**: puede crear un grupo de recursos o seleccionar el mismo que el de la implementación de CF.
   * **Ubicación**: escriba la ubicación.
   * **Plan de tarifa**: haga clic en **Aceptar** para finalizar.

Para más información, consulte [Introducción a Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

De forma alternativa, puede crear el área de trabajo de OMS con la plantilla de OMS. Con este método, la plantilla carga automáticamente las vistas y alertas de OMS preconfiguradas. Para más información, vea [Azure OMS Log Analytics solution for Cloud Foundry](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-cloudfoundry-solution) (Solución Azure Log Analytics de OMS para Cloud Foundry).

## <a name="deploy-the-nozzle"></a>Implementar el inyector

Hay un par de maneras diferentes de implementar el inyector: como un icono de PCF o como una aplicación de CF.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Implementar el inyector como un icono de Operations Manager de PCF

Si ha implementado PCF mediante el uso de Operations Manager, siga los pasos para [instalar y configurar el inyector para PCF](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). El inyector se instala como un icono con Operations Manager.

### <a name="deploy-the-nozzle-as-a-cf-application"></a>Implementación del inyector como una aplicación de CF

Si no usa Operations Manager de PCF, implemente el inyector como una aplicación. Este proceso se describe en las secciones siguientes.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Inicio de sesión en la implementación de CF como un administrador a través de la CLI de CF

Ejecute el siguiente comando:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" es el nombre de dominio de CF. Lo puede recuperar buscando "SYSTEM_DOMAIN" en el archivo de manifiesto de implementación de CF. 

"CF_User" es el nombre del administrador de CF. Puede recuperar el nombre y la contraseña buscando el nombre y "cf_admin_password" en la sección "scim" del archivo de manifiesto de implementación de CF.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Crear un usuario de CF y concederle los privilegios necesarios

Ejecute los comandos siguientes:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" es el nombre de dominio de CF. Lo puede recuperar buscando "SYSTEM_DOMAIN" en el archivo de manifiesto de implementación de CF.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Descarga de la última versión del inyector de Log Analytics

Ejecute el siguiente comando:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Establecimiento de variables de entorno

Ahora puede establecer las variables de entorno en el archivo manifest.yml en el directorio actual. A continuación, se muestra el manifiesto de la aplicación para el inyector. Reemplace los valores con información específica de su área de trabajo de OMS.

```
OMS_WORKSPACE             : OMS workspace ID: open OMS portal from your OMS workspace, select Settings, and select connected sources.
OMS_KEY                   : OMS key: open OMS portal from your OMS workspace, select Settings, and select connected sources.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to OMS Log Analytics. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to OMS Log Analytics. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to OMS Log Analytics. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to OMS Log Analytics as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to OMS Log Analytics. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Inserción de la aplicación desde el equipo de desarrollo

Asegúrese de que está en la carpeta oms-log-analytics-firehose-nozzle. Ejecute el siguiente comando:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Validar la instalación del inyector

### <a name="from-apps-manager-for-pcf"></a>Desde el Administrador de aplicaciones (de PCF)

1. Inicie sesión en Operations Manager y asegúrese de que el icono se muestra en el panel de instalación.
2. Inicie sesión en el Administrador de aplicaciones, asegúrese de que el espacio que se ha creado para el inyector se muestra en el informe de uso y confirme que el estado es normal.

### <a name="from-your-development-computer"></a>Desde el equipo de desarrollo

En la ventana de la CLI de CF, escriba:
```
cf apps
```
Asegúrese de que la aplicación de inyector de OMS se está ejecutando.

## <a name="view-the-data-in-the-oms-portal"></a>Visualización de los datos en el portal de OMS

### <a name="1-import-the-oms-view"></a>1. Importe la vista de OMS

Desde el portal de OMS, examine **Diseñador de vistas** > **Importar** > **Examinar** y seleccione uno de los archivos omsview. Por ejemplo, seleccione *Cloud Foundry.omsview* y guarde la vista. Ahora, se muestra un icono en la página de **información general** de OMS. Selecciónelo para ver las métricas visualizadas.

Puede personalizar estas vistas o crearlas a través del **Diseñador de vistas**.

*"Cloud Foundry.omsview"* es una plantilla de vista de OMS de Cloud Foundry. Se trata de una plantilla predeterminada totalmente configurada. Si tiene sugerencias o comentarios sobre la plantilla, envíelos a la [sección de problemas](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Creación de reglas de alerta

Puede [crear las alertas](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts) y personalizar las consultas y los valores de umbral según sea necesario. Estas son las alertas recomendadas:

| Consulta de búsqueda                                                                  | Generación de alerta según | Descripción                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Número de resultados < 1   | **bbs.Domain.cf-apps** indica si el dominio cf-apps está actualizado. Significa que las solicitudes de aplicación de CF procedentes de Cloud Controller están sincronizadas con bbs.LRPsDesired (instancias de aplicación aptas para Diego) para su ejecución. Si no se obtienen datos, significa que el dominio cf-apps no está actualizado en el período de tiempo especificado. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Número de resultados > 0   | En las celdas Diego, 0 indica un estado correcto y 1, incorrecto. Establezca la alerta si se detectan varias celdas Diego en estado incorrecto en el período de tiempo especificado. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Número de resultados > 0 | 1 significa que el sistema está en buen estado y 0, que no lo está. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Número de resultados > 0   | Consul emite su estado periódicamente. 0 significa que el sistema está en buen estado y 1, que el emisor de ruta detecta que Consul está inactivo. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" o Name_s="doppler.shedEnvelopes") Delta_d>0 | Número de resultados > 0 | Número diferencial de mensajes quitados intencionadamente por Doppler debido a la contrapresión. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Número de resultados > 0   | Loggregator emite el mensaje **LGR** para señalar que hay problemas con el proceso de registro. Un ejemplo de este problema es cuando la salida del mensaje de registro es demasiado alta. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Número de resultados > 0   | Cuando el inyector recibe una alerta de consumidor lento de Loggregator, envía a OMS un ValueMetric de tipo **slowConsumerAlert**. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Número de resultados > 0   | Si el número diferencial de eventos perdidos alcanza un umbral, significa que el inyector podría tener algún problema. |

## <a name="scale"></a>Escala

Puede escalar el inyector y Loggregator.

### <a name="scale-the-nozzle"></a>Escalar el inyector

Debe comenzar con al menos dos instancias del inyector. El conjunto predeterminado de resultados distribuye la carga de trabajo entre todas las instancias del inyector.
Para asegurarse de que el inyector puede soportar el tráfico de datos del conjunto predeterminado de resultados, configure la alerta **slowConsumerAlert** indicada en la sección anterior "Creación de reglas de alerta". Después de recibir el aviso, siga las [instrucciones para el inyector lento](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz), a fin de determinar si el ajuste de escala es necesario.
Para escalar el inyector, use el Administrador de aplicaciones o la CLI de CF para aumentar los números de instancia o los recursos de memoria o de disco del inyector.

### <a name="scale-the-loggregator"></a>Escalado de Loggregator

Loggregator emite un mensaje de registro **LGR** para señalar que hay problemas con el proceso de registro. Puede supervisar la alerta para averiguar si es necesario escalar verticalmente Loggregator.
Para escalar verticalmente Loggregator, aumente el tamaño de búfer de Doppler o agregue instancias de servidor de Doppler adicionales en el manifiesto de CF. Para más información, vea [las instrucciones para ajustar la escala de Loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Actualizar

Para actualizar el inyector con una versión más reciente, descargue la nueva versión del inyector, siga los pasos descritos en la sección anterior "Implementación del inyector" y vuelva a implementar la aplicación.

### <a name="remove-the-nozzle-from-ops-manager"></a>Eliminación del inyector de Operations Manager

1. Inicie sesión en Operations Manager.
2. Busque el icono **Inyector de Microsoft Azure Log Analytics para PCF**.
3. Seleccione el icono de elementos no utilizados y confirme la eliminación.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Eliminación del inyector del equipo de desarrollo

En la ventana de la CLI de CF, escriba lo siguiente:
```
cf delete <App Name> -r
```

Si elimina el inyector, los datos del portal de OMS no se quitan automáticamente. Expira en función de la configuración de retención de Log Analytics de OMS.

## <a name="support-and-feedback"></a>Soporte y comentarios

El inyector de Azure Log Analytics es de código abierto. Envíe sus preguntas y comentarios a la [sección de GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Para abrir una solicitud de soporte técnico de Azure, elija "Máquina virtual que ejecuta Cloud Foundry" como la categoría de servicio. 

## <a name="next-step"></a>Paso siguiente

Aparte de las métricas de Cloud Foundry que el inyector abarca, puede usar el agente de OMS para obtener información sobre los datos operativos de nivel de máquina virtual (como Syslog, rendimiento, alertas e inventario). El agente de OMS se instala como un complemento de Bosh en las máquinas virtuales de CF.

Para más información, vea [Deploy OMS agent to your Cloud Foundry deployment](https://github.com/Azure/oms-agent-for-linux-boshrelease) (Implementar el agente de OMS en la implementación de Cloud Foundry).
