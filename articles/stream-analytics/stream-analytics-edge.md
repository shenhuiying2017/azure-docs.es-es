---
title: "Azure Stream Analytics en IoT Edge (versión preliminar)"
description: "Cree trabajos perimetrales en Azure Stream Analytics e impleméntelos en dispositivos en los que se ejecuta Azure IoT Edge."
keywords: "flujo de datos, iot, perímetro"
services: stream-analytics
documentationcenter: 
author: jseb225
manager: jhubbard
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 11/15/2017
ms.author: jeanb
ms.openlocfilehash: 6e94758581bd510e58a709a53e30c11a5c1f1b62
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="azure-stream-analytics-on-iot-edge-preview"></a>Azure Stream Analytics en IoT Edge (versión preliminar)

> [!IMPORTANT]
> Esta funcionalidad está en versión preliminar. No es aconsejable usarlo en producción.
 
Azure Stream Analytics (ASA) en IoT Edge permite a los desarrolladores desarrollar inteligencia analítica casi en tiempo real más próxima a los dispositivos IoT para que puedan desbloquear el valor total de los datos generados por los dispositivos. Diseñado para los clientes que requieren una latencia baja, resistencia, un uso eficaz del ancho de banda y el cumplimiento, las empresas ahora pueden implementar la lógica de control cerca de las operaciones industriales y complementar los análisis de macrodatos que se realizan en la nube.  
Azure Stream Analytics en IoT Edge se ejecuta dentro del marco de [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) y la implementación y administración de los trabajos de ASA se puede realizar mediante IoT Hub una vez que el trabajo se crea en ASA.
Esta característica está en versión preliminar y si tiene cualquier pregunta o comentario puede usar [esta encuesta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) para ponerse en contacto con el equipo del producto. 

## <a name="scenarios"></a>Escenarios
![Diagrama de alto nivel](media/stream-analytics-edge/ASAedge_highlevel.png): estos son algunos escenarios típicos en los que es especialmente interesante ejecutar ASA en el perímetro:
* **Comando de baja latencia y control**: por ejemplo, los sistemas de seguridad en la fabricación son necesarios para responder a los datos operativos con una latencia muy baja. Con ASA en IoT Edge, puede analizar los datos de sensores casi en tiempo real y emitir comandos cuando detecte anomalías para detener una máquina o desencadenar alertas.
*   **Conectividad limitada a la nube**: los sistemas críticos, como un equipo de minería remoto, buques conectados o las perforaciones petrolífera en mar abierto necesitan analizar y reaccionar ante los datos, incluso cuando la nube conectividad sea intermitente. Con el ASA, la lógica de streaming funciona independientemente de la conectividad de red y puede elegir lo que envía a la nube para su posterior procesamiento o almacenamiento.
* **Ancho de banda limitado**: el volumen de datos que generan los motores de jet o los automóviles conectados puede ser tan grande que los datos se deben filtrar o procesar con anterioridad antes de enviarlos a la nube. ASA puede filtrar o agregar los datos que se envían a la nube.
* **Cumplimiento**: el cumplimiento de las normas puede requerir que se agreguen algunos datos o que se oculte su identidad localmente antes de enviarlos a la nube. Con ASA, 

## <a name="edge-jobs-in-azure-stream-analytics"></a>Trabajos de Edge en Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>¿Qué es un trabajo "perimetral"?

Los trabajos de Edge de ASA se ejecutan como módulos en el [runtime de Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Constan de dos partes:
1.  Una parte en la nube que es responsable de la definición del trabajo: los usuarios definen las entradas, la salida, las consultas y otros valores (eventos que no funcionan, etc.) en la nube.
2.  El módulo de ASA en IoT Edge que se ejecuta localmente. Contiene el motor de procesamiento de eventos complejos de ASA y recibe la definición del trabajo de la nube. 

ASA usa IoT Hub para implementar los trabajos perimetrales en los dispositivos. Para más información acerca de [la implementación de IoT Edge, consulte aquí](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Trabajo perimetral](media/stream-analytics-edge/ASAedge_job.png)


### <a name="installation-instructions"></a>Instrucciones de instalación
Los valores posibles se describen en la tabla siguiente. En las secciones siguientes encontrará más información.
|      |Paso   | Lugar     | Notas   |
| ---   | ---   | ---       |  ---      |
| 1   | **Creación de un trabajo perimetral de ASA**   | Azure Portal      |  Cree un nuevo trabajo y seleccione **Edge** como **entorno de hospedaje**. <br> Estos trabajos se crean o administran desde la nube y se ejecutan en sus propios dispositivos de IoT Edge.     |
| 2   | **Creación de un contenedor de almacenamiento**   | Azure Portal       | Los contenedores de almacenamiento se utilizan para guardar la definición de trabajo donde pueden obtenerla sus dispositivos de IoT. <br>  Todos los contenedor de almacenamiento existente se pueden reutilizar.     |
| 3   | **Configuración de un entorno de IoT Edge en dispositivos**   | Dispositivos      | Instrucciones para [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) o [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Implementación de ASA en dispositivos de IoT Edge**   | Azure Portal      |  La definición del trabajo ASA se exporta al contenedor de almacenamiento que creó anteriormente.       |
Puede seguir [este tutorial paso a paso](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) para implementar su primer trabajo de ASA en IoT Edge.

#### <a name="create-an-asa-edge-job"></a>Creación de un trabajo de Edge de ASA
1. En Azure Portal, cree un nuevo "trabajo de Stream Analytics". [Vínculo directo para crear un nuevo trabajo de ASA aquí](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).
2. En la pantalla de creación, seleccione **Edge** como **entorno de hospedaje** (vea la imagen siguiente) ![Creación de trabajo](media/stream-analytics-edge/ASAEdge_create.png)
3. Definición de trabajo
    1. **Definir flujos de entrada**. Defina uno o varios flujos de entrada para el trabajo.
    2. Definición de datos de referencia (opcional).
    3. **Definir flujos de salida**. Defina uno o varios flujos de salida para el trabajo. 
    4. **Definir consulta**. Defina la consulta ASA en la nube mediante el editor insertado. El compilador comprueba automáticamente la sintaxis habilitada para el perímetro de ASA. La consulta también se puede probar mediante la carga de datos de ejemplo. 
4. Establecimiento de valores opcionales
    1. **Ordenación de eventos**. Puede configurar la directiva de fuera de servicio en el portal. La documentación está disponible [aquí](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396).
    2. **Configuración regional**. Defina el formato de internalización.


#### <a name="create-a-storage-container"></a>Creación de un contenedor de almacenamiento
Se requiere un contenedor de almacenamiento es necesario para exportar la consulta ASA compilada y la configuración del trabajo. Se utiliza para configurar la imagen de Docker ASA con una consulta específica. 
1. Siga [estas instrucciones](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) para crear una cuenta de almacenamiento desde Azure Portal. Puede mantener todas las opciones predeterminadas para usar esta cuenta con ASA.
2. En la cuenta recién creada, cree un contenedor de almacenamiento de blobs:
    1. Haga clic en "Blobs" y, después, en "+ Contenedor". 
    2. Escriba un nombre y mantenga el contenedor como "Privado"


> [!Note]
> Cuando se crea una implementación, ASA exporta la definición del trabajo a un contenedor de almacenamiento. Esta definición de trabajo siguen siendo la misma mientras dure una implementación. En consecuencia, si desea actualizar un trabajo que se ejecuta en el perímetro, es preciso que lo edite en ASA y que, a continuación, cree una nueva implementación en IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Configuración de un entorno de IoT Edge en los dispositivos
Los trabajos de Edge se pueden implementar en dispositivos que ejecuten Azure IoT Edge.
Para hacerlo, es preciso seguir estos pasos:
- Crear una instancia de IoT Hub.
- Instalar Docker y el runtime de IoT Edge en los dispositivos perimetrales.
- Configure los dispositivos como "Dispositivos de IoT Edge" en IoT Hub.

Estos pasos se describen en la documentación de IoT Edge para [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) o [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Implementación de ASA en dispositivos de IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Adición de ASA a una implementación
- En Azure Portal, abra IoT Hub, navegue hasta el Explorador de IoT Edge y abra la hoja de su dispositivo.
- Seleccione **Set modules** (Establecer módulos) y, después, seleccione **Import Azure Service IoT Edge Module** (Importar módulo de Azure Service IoT Edge).
- Seleccione la suscripción y el trabajo Edge de ASA que ha creado. Después, seleccione su cuenta de almacenamiento. Haga clic en Guardar.
![Adición de un módulo de ASA a una implementación](media/stream-analytics-edge/set_module.png)


> [!Note]
> En este paso, ASA solicita acceso al contenedor de almacenamiento seleccionado y, después, crea una carpeta denominada "EdgeJobs". Para cada implementación, se crea una nueva subcarpeta en la carpeta "EdgeJobs".
> Con el fin de implementar el trabajo en dispositivos perimetrales, ASA crea una firma de acceso compartido (SAS) para el archivo de definición de trabajo. La clave SAS se transmite de forma segura a los dispositivos de IoT Edge que usen un dispositivo gemelo. Esta clave expira a los tres años de haberse creado.


Para más información acerca de las implementaciones de IoT Edge, consulte [esta página](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Configuración de rutas
IoT Edge proporciona una manera de enrutar de forma declarativa mensajes tanto entre los módulos como entre los módulos e IoT Hub. La sintaxis completa se describe [aquí](https://docs.microsoft.com/azure/iot-edge/module-composition).
Los nombres de las entradas y salidas creadas en el trabajo de ASA se pueden utilizar como puntos de conexión para el enrutamiento.  

###### <a name="example"></a>Ejemplo
```
{
"routes": {                                              
    "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
    "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream", 
    "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")" 
}
}   

```
Este ejemplo muestra las rutas del escenario descrito en la siguiente imagen. Contiene un trabajo perimetral denominado "**ASA**", con una entrada denominada "**temperature**" y una salida denominada "**alert**".
![Ejemplo de enrutamiento](media/stream-analytics-edge/RoutingExample.png)

En este ejemplo se definen las siguientes rutas:
- Todos los mensaje de **tempSensor** se envían al módulo denominado **ASA**, a la entrada denominada **temperature**.
- Todas las salidas del módulo de **ASA** se envían a la instancia de IoT Hub vinculada a este dispositivo ($upstream).
- Todas las salidas del módulo de **ASA** se envían al punto de conexión **control** de **tempSensor**.


## <a name="technical-information"></a>Información técnica
### <a name="current-limitations-for-edge-jobs-compared-to-cloud-jobs"></a>Limitaciones actuales de los trabajos perimetrales, en comparación con los trabajos en la nube
El objetivo es tener paridad entre los trabajos perimetrales y los trabajos en la nube. Ya se admiten la mayoría de las características de nuestro lenguaje de consulta SQL.
Sin embargo, las siguientes características aún no se admiten en los trabajos perimetrales:
* Funciones definidas por el usuario (UDF) y agregados definidos por el usuario (UDA).
* Funciones de Azure Machine Learning.
* Uso de más de 14 agregados en un solo paso.
* Formato AVRO para la entrada y salida. En este momento se admiten solo CSV y JSON.
* Compresión de entrada JSON.
* Los siguientes operadores SQL:
    * AnomalyDetection
    * Operadores geoespaciales:
        * CreatePoint
        * CreatePolygon
        * CreateLineString
        * ST_DISTANCE
        * ST_WITHIN
        * ST_OVERLAPS
        * ST_INTERSECTS
    * PARTITION BY
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Requisitos de runtime y hardware
Para ejecutar el ASA en IoT Edge, se necesitan dispositivos que se puedan ejecutar [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA y Azure IoT Edge usan contenedores de **Docker** para proporcionar una solución portátil que se ejecuta en un sistema operativo que admite múltiples hosts (Windows, Linux).

ASA en IoT Edge está disponible en forma de imágenes de Windows y Linux que se ejecutan en arquitecturas x86-64 o ARM. 


### <a name="input-and-output"></a>Entrada y salida
#### <a name="input-and-output-streams"></a>Flujos de entrada y salida
Los trabajos de Edge de ASA pueden obtener entradas y salidas de otros módulos que se ejecutan en dispositivos de IoT Edge. Para conectarse a determinados módulos, y desde ellos, puede establecer la configuración de enrutamiento en el momento de la implementación. Se puede encontrar más información en [la documentación de composición del módulo de IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

En las entradas y salidas, se admiten los formatos CSV y JSON.

Por cada flujo de entrada y salida que se crea en un trabajo de ASA, se crea un punto de conexión correspondiente en el módulo implementado. Estos puntos de conexión se pueden utilizar en las rutas de la implementación.



##### <a name="reference-data"></a>Datos de referencia
Los datos de referencia (que también se conocen como tabla de búsqueda) son un conjunto finito de datos estáticos o que, por naturaleza, cambian lentamente. Se utilizan para realizar búsquedas o para ponerlos en correlación con su flujo de datos. Para usar los datos de referencia en un trabajo de Azure Stream Analytics, por lo general usará una [instrucción JOIN de los datos de referencia](https://msdn.microsoft.com/library/azure/dn949258.aspx) en la consulta. Para más información, consulte la [documentación de ASA acerca de los datos de referencia](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data).

Para usar datos de referencia para ASA en IoT Edge, debe seguir estos pasos: 
1. Cree una nueva entrada para el trabajo
2. Elija **Datos de referencia** como **tipo de origen**.
3. Establezca la ruta de acceso del archivo. Esta debe ser una ruta de acceso **absoluta** en el dispositivo ![Creación de datos de referencia](media/stream-analytics-edge/ReferenceData.png)
4. Habilite **Unidades compartidas** en la configuración de Docker y asegúrese de que la unidad está habilitada antes de iniciar la implementación.

Para más información, consulte la [documentación de Docker para Windows](https://docs.docker.com/docker-for-windows/#shared-drives).

> [!Note]
> Por el momento solo se admiten datos de referencia locales.




## <a name="license-and-third-party-notices"></a>Licencia y avisos de terceros
* [Licencia de versión preliminar de Azure Stream Analytics en IoT Edge](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Aviso de terceros para la versión preliminar de Azure Stream Analytics en IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="get-help"></a>Obtener ayuda
Para más ayuda, pruebe el [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Pasos siguientes

* [Más información acerca de Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [Tutorial de ASA en IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Enviar comentarios al equipo mediante esta encuesta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
