---
title: "Implementar el inyector de Azure Log Analytics para la supervisión de Cloud Foundry | Microsoft Docs"
description: "Instrucciones paso a paso para implementar el inyector de Loggregator de Cloud Foundry para Azure Log Analytics, configurar Azure Log Analytics y la consola de OMS y usar estas herramientas para supervisar las métricas de estado y rendimiento del sistema Cloud Foundry."
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
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: f704a2638a4b6b57c014d502dd87303a55334672
ms.contentlocale: es-es
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Implementar el inyector de Azure Log Analytics para la supervisión del sistema Cloud Foundry

## <a name="background"></a>Fondo

[Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) es un servicio de Microsoft [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/) (OMS). Sirve para recopilar y analizar los datos que se generan en los entornos tanto locales como en la nube.

El inyector de Microsoft Azure Log Analytics es un componente de Cloud Foundry que reenvía las métricas del conjunto predeterminado de resultados de [Cloud Foundry Loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) a Azure Log Analytics. Con el inyector podrá recopilar, ver y analizar las métricas de estado y rendimiento del sistema Cloud Foundry en varias implementaciones.

En este documento aprenderá a implementar el inyector en su entorno de Cloud Foundry y, después, tendrá acceso a los datos desde la consola de OMS de Azure Log Analytics.

## <a name="prerequisites"></a>Requisitos previos

### <a name="1-deploy-a-cf-or-pcf-environment-in-azure"></a>1. Implementar un entorno de Cloud Foundry o de Pivotal Cloud Foundry en Azure

Puede usar el inyector con una implementación de Cloud Foundry (CF) de código abierto o con una implementación de Pivotal Cloud Foundry (PCF).

* [Implementar Cloud Foundry en Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Implementar Pivotal Cloud Foundry en Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Instalar las herramientas de línea de comandos de Cloud Foundry para implementar el inyector

El inyector se ejecuta como una aplicación en el entorno de Cloud Foundry, con lo cual necesita la CLI de CF para implementar la aplicación. También necesita permiso de acceso al conjunto predeterminado de resultados de Loggregator y a Cloud Controller, de modo que necesitará un cliente de línea de comandos de UAA para crear y configurar el usuario.

* [Instalar la CLI de Cloud Foundry](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Instalar el cliente de línea de comandos de UAA de Cloud Foundry](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Asegúrese de que Rubygems está instalado antes de configurar el cliente de línea de comandos de UAA.

### <a name="3-create-an-oms-workspace-in-azure"></a>3. Crear un área de trabajo de OMS en Azure

#### <a name="create-the-oms-workspace-manually"></a>Crear un área de trabajo de OMS manualmente

Cuando el inyector termine de implementarse, puede crear el área de trabajo de OMS manualmente y cargar las alertas y las vistas de OMS preconfiguradas.

1. En Azure Portal, busque Log Analytics en la lista de servicios de Marketplace y, después, seleccione Log Analytics.
2. Haga clic en Crear y seleccione opciones para los siguientes elementos:

* Área de trabajo de OMS: escriba un nombre para el área de trabajo.
* Suscripción: si tiene varias suscripciones, elija la misma que la de la implementación de Cloud Foundry.
* Grupo de recursos: puede crear un grupo de recursos o seleccionar el mismo que el de la implementación de Cloud Foundry.
* Ubicación
* Plan de tarifa: haga clic en Aceptar para finalizar.
> Para más información, vea [Introducción a Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

#### <a name="create-the-oms-workspace-through-the-oms-template"></a>Crear el área de trabajo de OMS con la plantilla de OMS

Puede crear el área de trabajo de OMS y cargar las alertas y las vistas de OMS preconfiguradas a través de la [solución de Azure OMS Log Analytics para Cloud Foundry](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-cloudfoundry-solution).

## <a name="deploy-the-nozzle"></a>Implementar el inyector

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Implementar el inyector como un icono de Operations Manager de PCF

Si ha implementado PCF a través de Operations Manager, siga estos pasos para [instalar y configurar el inyector para PCF](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). El inyector se instala como un icono con Operations Manager.

### <a name="deploy-the-nozzle-as-an-application-to-cloud-foundry"></a>Implementar el inyector como una aplicación en Cloud Foundry

Si no usa Operations Manager de PCF, debe implementar el inyector como una aplicación.

#### <a name="log-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Iniciar sesión en la implementación de CF como un administrador a través de la CLI de CF

En el cuadro de desarrollo, ejecute el siguiente comando:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

> "SYSTEM_DOMAIN" es el nombre de dominio de CF. Lo puede recuperar buscando "SYSTEM_DOMAIN" en el archivo de manifiesto de implementación de CF. 
> "CF_User" es el nombre del administrador de CF. Puede recuperar el nombre y la contraseña buscando el nombre y "cf_admin_password" en la sección "scim" del archivo de manifiesto de implementación de CF.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Crear un usuario de CF y concederle los privilegios necesarios

En el cuadro de desarrollo, ejecute los siguientes comandos:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

> "SYSTEM_DOMAIN" es el nombre de dominio de CF. Lo puede recuperar buscando "SYSTEM_DOMAIN" en el archivo de manifiesto de implementación de CF.

#### <a name="download-the-latest-azure-log-analytics-nozzle-release"></a>Descargar la versión más reciente del inyector Azure Log Analytics

En el cuadro de desarrollo, ejecute el siguiente comando:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables-in-manifestyml-in-your-current-directory"></a>Establecer variables de entorno en "manifest.yml" en el directorio actual

Este es el manifiesto de aplicación del inyector; debe reemplazar el valor por información específica de su área de trabajo de OMS.

```
OMS_WORKSPACE             : OMS workspace ID: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_KEY                   : OMS key: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to OMS Log Analytics, default is 10s.
OMS_BATCH_TIME            : Interval for posting a batch to OMS Log Analytics, default is 10s.
OMS_MAX_MSG_NUM_PER_BATCH : The max number of messages in a batch to OMS Log Analytics, default is 1000.
API_ADDR                  : The api URL of the CF environment, refer to "Push the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
DOPPLER_ADDR              : Loggregator's traffic controller URL, refer to "Deploy the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
FIREHOSE_USER             : CF user you created in "Push the Nozzle as an App to Cloud Foundry" section, who has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma separated list, valid event types are METRIC,LOG,HTTP
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the Trafficcontroller
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments
IDLE_TIMEOUT              : Keep Alive duration for the firehose consumer, default is 60s.
LOG_LEVEL                 : Logging level of the nozzle, valid levels: DEBUG, INFO, ERROR
LOG_EVENT_COUNT           : If true, the total count of events that the nozzle has received and sent will be logged to OMS Log Analytics as CounterEvents
LOG_EVENT_COUNT_INTERVAL  : The time interval of logging event count to OMS Log Analytics, default is 60s.
```

### <a name="push-the-application-from-your-dev-box"></a>Implementar la aplicación desde el cuadro de desarrollo

Asegúrese de que está en la carpeta "oms-log-analytics-firehose-nozzle" y ejecute lo siguiente:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Validar la instalación del inyector

### <a name="from-apps-manager-for-pcf"></a>Desde el Administrador de aplicaciones (de PCF)

1. Inicie sesión en Operations Manager; asegúrese de que el icono se muestra en el panel de instalación.
2. Inicie sesión en el Administrador de aplicaciones; asegúrese de que el espacio que se ha creado para el inyector se muestra en el informe de uso y de que el estado es normal.

### <a name="from-dev-box"></a>Desde el cuadro de desarrollo

En el cuadro de desarrollo de la ventana de la CLI de CF, escriba lo siguiente:
```
cf apps
```
Asegúrese de que la aplicación de inyector de OMS se está ejecutando.

## <a name="view-the-data-in-oms-portal"></a>Ver los datos en el portal de OMS

### <a name="1-import-oms-view"></a>1. Importación de la vista de OMS

En el portal de OMS, vaya a **Diseñador de vistas** -> **Importar** -> **Examinar**, seleccione uno de lo archivos omsview (por ejemplo, *Cloud Foundry.omsview*) y guarde la vista. Ahora, se muestra un **icono** en la página principal de información general de OMS. Haga clic en el **icono**; mostrará las métricas visualizadas.

Los operadores pueden personalizar estas vistas o crearlas a través del **Diseñador de vistas**.

*"Cloud Foundry.omsview"* es una versión de vista previa de la plantilla de vista de OMS de Cloud Foundry, que es una plantilla predeterminada totalmente configurada en proceso. Envíenos sus sugerencias y comentarios a la [sección de problemas](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Creación de reglas de alerta

Los operadores pueden [crear alertas](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts) y personalizar las consultas y los valores de umbral según sea necesario. Estas son las alertas recomendadas.

| Consulta de búsqueda                                                                  | Generación de alerta según | Descripción                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Número de resultados < 1   | **bbs.Domain.cf-apps** indica si el dominio cf-apps está actualizado, lo que significa que las solicitudes de aplicación de CF procedentes de Cloud Controller están sincronizadas con bbs.LRPsDesired (instancias de aplicación aptas para Diego) para su ejecución. Si no se obtienen datos, significa que el dominio cf-apps no está actualizado en el período de tiempo en cuestión. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Número de resultados > 0   | En las celdas Diego, 0 indica un estado correcto y 1, incorrecto. Establezca la alerta si se detectan varias **celdas Diego en estado incorrecto** en el período de tiempo determinado. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Número de resultados > 0 | 1 significa que el sistema está en buen estado y 0, que no lo está. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Número de resultados > 0   | Consul emite su estado periódicamente. 0 significa que el sistema está en buen estado y 1, que el emisor de ruta detecta que **Consul está inactivo**. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" o Name_s="doppler.shedEnvelopes") Delta_d>0 | Número de resultados > 0 | Número diferencial de mensajes **quitados** intencionadamente por Doppler debido a la contrapresión. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Número de resultados > 0   | Loggregator emite **LGR** para señalar que hay problemas con el proceso de registro, por ejemplo, cuando la salida del mensaje de registro es demasiado alta. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Número de resultados > 0   | Cuando el inyector recibe una alerta de consumidor lento de Loggregator, envía a OMS un ValueMetric de tipo **slowConsumerAlert**. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Número de resultados > 0   | Si el número diferencial de **eventos perdidos** alcanza un umbral, significa que el inyector podría tener algún problema. |

## <a name="scale"></a>Escala

### <a name="scale-the-nozzle"></a>Escalar el inyector

Es recomendable que los operadores comiencen con al menos dos instancias del inyector. El conjunto predeterminado de resultados distribuye la carga de trabajo entre todas las instancias del inyector.
Para asegurarse de que el inyector puede asumir el tráfico de datos del conjunto predeterminado de resultados, el operador debe configurar la alerta **slowConsumerAlert** recogida en la sección "Creación de reglas de alerta"; cuando esta alerta se active, siga las [instrucciones de inyector lento](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) para determinar si se necesita un ajuste de escala.
Para escalar el inyector, use el Administrador de aplicaciones o la CLI de CF para aumentar los números de instancia o los recursos de memoria/disco del inyector.

### <a name="scale-the-loggregator"></a>Escalar Loggregator

Loggregator emite el mensaje de registro **LGR** para señalar que hay problemas con el proceso de registro. El operador puede supervisar la alerta para averiguar si es necesario ajustar la escala de Loggregator.
Para escalar Loggregator verticalmente, el operador puede aumentar el tamaño del búfer de Doppler o agregar más instancias de servidor de Doppler en el manifiesto de CF. Para más información, vea las [instrucciones para escalar Loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Actualizar

Para actualizar el inyector con una versión más reciente, descargue la nueva versión del inyector, siga los pasos descritos en la sección "Implementar" e implemente la aplicación de nuevo.

Para quitar el inyector, siga estos pasos:

### <a name="from-the-ops-manager"></a>Desde Operations Manager

1. Inicie sesión en Operations Manager.
2. Busque el icono "Inyector de Microsoft Azure Log Analytics para PCF".
3. Haga clic en el icono de elementos no utilizados y confirme la acción de eliminación.

### <a name="from-the-dev-box"></a>Desde el cuadro de desarrollo

En la ventana de la CLI de CF, escriba lo siguiente:
```
cf delete <App Name> -r
```

Si quita el inyector, los datos en el portal de OMS no se quitan automáticamente; expirarán en función de la configuración de retención de Log Analytics de OMS.

## <a name="support-and-feedback"></a>Soporte y comentarios

El código del inyector de Azure Log Analytics es abierto. Envíe sus preguntas y comentarios a la [sección github](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Para abrir una solicitud de soporte de Azure, use la categoría de servicio "Máquina virtual que ejecuta Cloud Foundry". 

## <a name="next-step"></a>Paso siguiente

Aparte de las métricas de Cloud Foundry que el inyector abarca, puede usar el agente de OMS para obtener información sobre los datos operativos de nivel de máquina virtual (Syslog, rendimiento, alertas, inventario). Este agente se instala como un complemento de Bosh en las máquinas virtuales de CF.
> Vea [Deploy OMS agent to your Cloud Foundry deployment](https://github.com/Azure/oms-agent-for-linux-boshrelease) (Implementar el agente de OMS en la implementación de Cloud Foundry) para obtener más información.
