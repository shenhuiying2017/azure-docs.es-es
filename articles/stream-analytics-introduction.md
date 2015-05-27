<properties 
	pageTitle="Introducción a Stream Analytics | Azure" 
	description="Comprender el análisis de secuencias de Azure" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="05/06/2015" 
	ms.author="jeffstok"/>


# Introducción a Análisis de transmisiones de Azure

Análisis de transmisiones de Azure es un servicio totalmente administrado que proporciona un procesamiento completo de eventos de baja latencia, alta disponibilidad y escalable a través de la transmisión de datos en la nube.


## Introducción al Análisis de transmisiones de Azure (vídeo)

En este [vídeo](http://azure.microsoft.com/documentation/videos/introduction-to-azure-stream-analytics-with-santosh-balasubramanian/), Santosh Balasubramanian y Scott Hanselman presentan Análisis de transmisiones de Azure. (Duración: 11:22)

> [AZURE.VIDEO introduction-to-azure-stream-analytics-with-santosh-balasubramanian]

## Descripción general y motivación

En la actualidad, grandes cantidades de datos se transmiten a alta velocidad por la red. Las organizaciones que pueden procesar estos datos y actuar sobre ellos en tiempo real pueden mejorar drásticamente la eficacia y diferenciarse en el mercado. Escenarios de Análisis de transmisiones en tiempo real pueden encontrarse en todos los sectores: análisis y alertas personalizados y en tiempo real de los valores bursátiles que ofrecen las empresas de servicios financieros; detección de fraudes en tiempo real; servicios de protección de datos e identidades; ingesta y análisis confiables de los datos generados por sensores y accionadores incrustados en objetos físicos (Internet de las cosas o IoT); análisis clickstream en Internet; y aplicaciones de relaciones con el cliente (CRM) que emiten alertas cuando la experiencia del cliente en un período de tiempo se ve mermada. Las empresas buscan la manera más flexible, confiable y rentable de realizar tales análisis de datos de streaming de eventos en tiempo real para triunfar en el moderno mundo empresarial tan competitivo.

La creación de sistemas de procesamiento de secuencias es una tarea compleja. Las operaciones de streaming, como las correlaciones y las agregaciones, no solo se han de implementar de manera eficiente sino que también deben ser escalables y tolerantes a los errores. Otro nivel de desafíos de carácter operativo es la implementación, la depuración y la supervisión. Los costes asociados con la creación y el mantenimiento de una solución de este tipo aumentan rápidamente. Las grandes empresas se han resignado a pagar costes tan altos con soluciones creadas de forma personalizada, mientras que las pequeñas empresas a menudo pierden la oportunidad debido a los grandes impedimentos para entrar y a los costes prohibitivos asociados con ellos. Azure Stream Analytics aborda estos desafíos.

Análisis de transmisiones de Azure es un servicio de cálculo de streaming completamente administrado en tiempo real que se hospeda Microsoft Azure. Ofrece procesamiento de eventos complejos altamente resistente, de baja latencia y escalable de streaming de datos. Análisis de transmisiones de Azure permite a los desarrolladores combinar fácilmente streaming de datos con registros históricos o datos de referencia para capturar información de la empresa de manera rápida y fácil.

Con unos cuantos clics en el Portal de Azure, los clientes pueden crear un trabajo de streaming usando un lenguaje similar a SQL para especificar transformaciones y supervisar la escala y la velocidad de su canalización de streaming general. El servicio se puede escalar fácilmente desde unos pocos kilobytes hasta un gigabyte o más de eventos procesados por segundo. La mayoría otras soluciones de streaming disponibles en la actualidad requieren que los clientes escriban un complejo código personalizado, pero con Azure Stream Analytics los clientes pueden escribir SQL sencillo, familiar y declarativo.

Análisis de transmisiones de Azure proporciona diversos operadores, desde filtros sencillos a correlaciones complejas. En cuestión de minutos, se pueden definir operaciones con ventanas de duración definida (por ejemplo agregados con ventanas), correlacionar varios streamings para detectar patrones (por ejemplo, secuencias) o incluso comparar las actuales condiciones con valores y modelos históricos con el sencillo conjunto de operadores del lenguaje de consulta de tipo SQL de Análisis de transmisiones. Especificar una canalización de streaming es tan sencillo como configurar sus entradas y salidas, y realizar una consulta de tipo SQL que describa las transformaciones deseadas. Aunque esto es suficiente en la mayoría de los casos, los escenarios de mayor escala y más complejos se beneficiarán de la capacidad de configuración de Análisis de transmisiones. Los usuarios pueden determinar cuánta potencia de proceso desean dedicar en cada paso de la canalización para conseguir el rendimiento máximo deseado.

Azure Stream Analytics se conecta actualmente con Centros de eventos de Azure de forma directa para la ingesta de streaming y con el servicio de blobs de Azure para los datos históricos. Con la gama de interfaces de entrada y salida de los Centros de eventos de Azure es fácil integrar Análisis de transmisiones de Azure con otros orígenes de datos y motores de procesamiento sin perder el carácter de streaming de los cálculos. Los datos se pueden escribir desde Análisis de transmisiones hasta el almacenamiento de Azure donde luego se pueden volver a procesar como una serie de eventos o usarse en otras formas de análisis por lotes mediante HDInsight. Análisis de transmisiones de Azure aprovecha años de trabajo de Microsoft Research en el desarrollo de motores de streaming altamente optimizadas para el procesamiento dependiente del tiempo, así como en integraciones de lenguajes para las especificaciones intuitivas de estos. Análisis de transmisiones de Azure se crea aprovechando la comunidad de código abierto de Hadoop, YARN y REEF para el procesamiento de escala horizontal.


## Principales capacidades

### Facilidad de uso
Análisis de transmisiones admite un modelo de consulta declarativa simple para describir las transformaciones. Con el fin de mejorar la facilidad de uso, seleccionamos una variante SQL como lenguaje específico de dominio (DSL) y aislamos a los clientes de las considerables complejidades técnicas que subyacen en nuestro sistema de streaming. Con el lenguaje de consulta de Análisis de transmisiones, puede implementar rápida y fácilmente funciones temporales, que incluyen uniones de base temporal, agregados basados en ventanas, filtros temporales, así como otras operaciones comunes, como uniones, agregados, proyecciones y filtros.

Aunque ampliamos la semántica de SQL de varias formas intuitivas para un usuario de SQL, permanecemos dentro de los límites sintácticos del estándar SQL para facilitar la implementación y el uso de la herramienta. Para obtener información adicional sobre nuestro lenguaje de consulta, consulte la sección "Pasos siguientes".

### Escalabilidad
Stream Analytics es capaz de controlar la capacidad de proceso de alto nivel de eventos de hasta 1 GB por segundo. La integración con los Centros de eventos de Azure permite que la solución ingiera millones de eventos por segundo procedentes de dispositivos conectados, datos de clickstream y archivos de registro, por nombrar unos cuantos. Para ello, hemos aprovechado la capacidad de particionamiento de los Centros de eventos, los cuales pueden producir 1 MB/s por partición. Análisis de transmisiones ofrece compatibilidad con el particionamiento y procesamiento de estos eventos ingeridos a lo largo de los ejes horizontal y vertical. Un usuario puede particionar el cálculo en varios pasos lógicos dentro de la definición de consulta, con la posibilidad de particionar cada uno de manera adicional en elementos de ejecución paralela de los datos. Con el tiempo, Análisis de transmisiones también se escalará automáticamente según la velocidad de ingesta de eventos, la complejidad del procesamiento y las latencias esperadas para permitir que los usuarios personalicen su carga de trabajo en consecuencia. Para obtener más información sobre la implementación de escalabilidad, consulte los vínculos en la sección “Pasos siguientes”.

### Confiabilidad, capacidad de repetición y recuperación rápida
Análisis de transmisiones ayuda a evitar la pérdida de datos y ofrece continuidad empresarial ante errores de nodos gracias a sus capacidades integradas de recuperación y creación de puntos de comprobación. Estas capacidades se entregan mediante la arquitectura del modelo de "cliente ancla" de Análisis de transmisiones. El servicio está diseñado para conservar el estado y optimizar la reanudación a partir del error de un nodo, volver a realizar los cálculos y almacenar en caché las salidas para dar cuenta de manera eficiente de los errores de bajada.

Gracias a la posibilidad de mantener el estado internamente, el servicio puede proporcionar resultados repetibles donde sea posible archivar eventos y volver a aplicar el procesamiento en el futuro, al tiempo que se obtienen los mismos resultados. Esta característica permite a los clientes retroceder en el tiempo e investigar los cálculos en escenarios como análisis de causas raíz y análisis de hipótesis. Estas características, cuando se combinan, permiten recuperarse rápidamente de los errores de nodos y volver a procesar rápidamente el estado perdido.

### Baja latencia
Stream Analytics está actualmente optimizado para latencias observables de extremo a extremo en el rango del subsegundo. Esto permite una gran capacidad de procesamiento continua del sistema. El modelo de comunicación es un modelo de procesamiento por lotes adaptativo y basado en la extracción que opera en función de tiempos de espera y límites de tamaño configurados. Por lo tanto, los eventos y otros registros se procesan por lotes hasta que se alcanzan los límites. Incluso con una gran capacidad de procesamiento, el sistema pueda proporcionar bajas latencias.

### Datos de referencia
Stream Analytics ofrece a los usuarios la posibilidad de especificar y usar datos de referencia. Podrían tratarse de datos históricos o de datos que cambian con menos frecuencia con el tiempo. El sistema simplifica el uso de datos de referencia para que se consideren como cualquier otro streaming de eventos entrante que se une a otras operaciones de streaming de eventos ingeridos en tiempo real para realizar transformaciones. Para obtener información adicional sobre la construcción y el uso de datos de referencia, consulte la sección "Pasos siguientes".



## Lo que motiva a una empresa a elegir Análisis de transmisiones de Azure

### Bajo coste:
El servicio Análisis de transmisiones está optimizado para proporcionar a los usuarios costes muy bajos por poner en marcha y mantener soluciones de análisis en tiempo real. El servicio se creó para que pague según el uso. El uso se obtiene en función del volumen de eventos procesados y la cantidad de potencia de proceso aprovisionada dentro del clúster para gestionar los trabajos de streaming respectivos.

### Implementación rápida
Con Análisis de transmisiones, puede tener una solución de procesamiento de eventos en tiempo real de gran escalabilidad sin incurrir en costes de hardware y otros costes iniciales, sin instalaciones o configuraciones que consumen tiempo. Azure se encarga de todo eso por usted. Puede ponerse en marcha en cuestión de minutos con solo suscribirse al servicio a través del Portal de Azure. La interfaz de usuario de uso fácil del Portal le lleva por una guía detallada de inicio rápido para que configure y pruebe su origen de entrada y su almacén de salida. Un editor de consultas sencillo proporciona características de recomendación y Autocompletar.

### Rápido desarrollo.
Análisis de transmisiones le ayuda a reducir la fricción y la complejidad al desarrollar funciones de análisis para el escalado horizontal de sistemas distribuidos. Los desarrolladores solo tienen que describir la transformación que deseen en un lenguaje de consulta basado en SQL y el sistema se encargará automáticamente de la distribución de la carga de trabajo teniendo en cuenta la escala, el rendimiento y la resistencia, eliminando la necesidad de programación de procedimientos que es necesaria en la mayoría de las soluciones de procesamiento de streaming. Gracias a sus capacidades integradas y la facilidad de uso del portal, Análisis de transmisiones satisface las necesidades de los desarrolladores de alto nivel que desean realizar rápida y fácilmente un procesamiento en tiempo real sin tener que preocuparse de cosas tales como la adquisición de infraestructura, el mantenimiento continuado y la escala.

## Escenarios y casos de uso

### Análisis en tiempo real para Internet de las cosas (IoT)
A medida que los dispositivos se hacen más inteligentes y se crean más dispositivos con capacidades de comunicación, la expectativa de lo que se puede hacer con los datos generados y recopilados desde estos dispositivos sigue evolucionando tanto en los espacios comerciales como del consumidor. Se espera que con tal cantidad de datos disponibles, podremos combinar y procesar rápidamente los datos, además de obtener un mejor conocimiento del entorno que nos rodea y los dispositivos que usamos regularmente. Un escenario IoT convencional se puede describir mediante un ejemplo de máquinas expendedoras.

Las máquinas expendedoras envían periódicamente datos, como stock de productos, estado, temperatura, etc., a una puerta de enlace de campo (si la máquina expendedora no es compatible con IP) o a una puerta de enlace en la nube (si admite IP) para la ingesta en el sistema. La secuencia de datos entrantes se procesa y transforma de forma que la salida calculada se pueda reenviar inmediatamente a través de las puertas de enlace al dispositivo para realizar la acción correspondiente. Por ejemplo, si la máquina se ha sobrecalentado, el dispositivo podría tenerse que reiniciar por sí solo o realizar automáticamente una actualización del firmware sin ninguna intervención humana. La salida procesada también puede desencadenar la programación de otras alertas y notificaciones para el técnico en función de los eventos.

A medida que se recopilan y procesan más datos, se puede usar también el aprendizaje automático para desarrollar y aprender a partir de patrones observados en el sistema. Por ejemplo, la posibilidad de predecir cuándo pueden necesitar mantenimiento las máquinas en función del streaming de eventos en tiempo real que se suministra al sistema o de programar mantenimiento preventivo en la ruta de un técnico cuando las cosas están a punto de estropearse.

Este patrón de dispositivo que envía información a un sistema de procesamiento y que potencialmente toma medidas basadas en los resultados procesados en tiempo real se puede observar habitualmente en los casos de uso de IoT. Entre otros escenarios de este tipo, se incluyen automóviles conectados, análisis clickstream y administración de instalaciones. Para optimizar este bucle de realimentación de cara a una latencia baja y una alta capacidad de procesamiento, Análisis de transmisiones se puede usar para ingerir datos de Centros de eventos de Azure; estos datos se procesan y envían inmediatamente de vuelta al centro de eventos para que el dispositivo suscrito respectivo realice la acción adecuada.


![Análisis de transmisiones de Azure: análisis en tiempo real para Internet de las cosas (IoT)][img.stream.analytics.scenario1]


### Análisis de telemetría y registro a través de paneles
A medida que crece el volumen de dispositivos, máquinas y aplicaciones, un caso de uso común para las empresas es la necesidad de supervisar y responder a las necesidades cambiantes del negocio creando análisis detallados prácticamente en tiempo real. Un escenario convencional de análisis de telemetría/registro se puede describir mediante un ejemplo de aplicación o servicio en línea, pero el patrón se suele observar en empresas que recopilan y presentan informes sobre la telemetría de aplicaciones o dispositivos.

La aplicación o el servicio recopilan datos de estado de forma periódica. Se recopilan datos que representan el estado actual de la aplicación o la infraestructura en un momento dado, registros de solicitudes de usuario y otros datos que representan acciones o actividades que se realizan dentro de la aplicación. Los datos se guardan en un blob o en otros tipos de almacenes de datos con fines históricos para su posterior procesamiento.

Con la tendencia reciente hacia la creación de paneles en tiempo real, además de guardar los datos en un blob o en otro tipo de almacén para realizar análisis históricos, los clientes buscan procesar y transformar directamente el streaming de datos entrantes para poder ofrecerlo inmediatamente a los usuarios finales en forma de paneles y notificaciones cuando sea necesario realizar una acción. Por ejemplo, si un sitio de servicio deja de funcionar, este hecho se puede notificar al personal de operaciones para que comiencen a investigar y resuelvan el problema rápidamente. En varios de estos casos de uso, una persona supervisa normalmente un panel en tiempo real creado sobre el conjunto de datos actualizado después de procesar los datos ingeridos a través de Stream Analytics.
 
![Análisis de transmisiones de Azure: análisis de telemetría y registro mediante paneles][img.stream.analytics.scenario2]

### Archivo de eventos para su futuro procesamiento
Las expectativas sobre la ejecución rápida y ágil en las empresas continúan creciendo. Las empresas y los desarrolladores optan ahora por plataformas basadas en la nube, de uso sencillo, para hacer frente a la demanda de mayor agilidad y buscan plataformas que les permitan ingerir y procesar un streaming continuo de datos generados por sus sistemas casi en tiempo real. En la actualidad, estos clientes son incapaces de aprovechar un servicio que esté optimizado para escrituras de baja latencia baja en un almacén de datos y, por tanto, acaban por perder varios conjuntos de datos fundamentales que podrían revelar valiosa información para su negocio. Un escenario convencional de archivador de eventos se puede describir de la manera siguiente:

Datos de distintos dispositivos y plataformas que están distribuidos por todo el mundo se insertan en un recopilador de datos centralizado. Cuando los datos se encuentran en la ubicación central, se realiza en ellos alguna transformación carente de estado, como limpiar información de identificación personal, agregar etiquetado geográfico y realizar búsquedas IP. Los datos transformados se archivan después en el almacenamiento de blobs de manera que puedan usarse fácilmente en HDInsight o en otras herramientas para el procesamiento sin conexión.

![Análisis de transmisiones de Azure: archivo de eventos para su futuro procesamiento][img.stream.analytics.scenario3]
 
## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->
[img.stream.analytics.scenario1]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_01.png
[img.stream.analytics.scenario2]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_02.png
[img.stream.analytics.scenario3]: ./media/stream-analytics-introduction/Introduction-to-Azure-Stream-Analytics_03.png


<!--Link references-->
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.limitations]: stream-analytics-limitations.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=54-->