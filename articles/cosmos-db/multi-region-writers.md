---
title: Varios maestros a escala global con Azure Cosmos DB | Microsoft Docs
description: ''
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: 2446fac7526015d11737529c26d54e910643b750
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="multi-master-at-global-scale-with-azure-cosmos-db"></a>Varios maestros a escala global con Azure Cosmos DB 
 
Desarrollar aplicaciones distribuidas globalmente que responden con latencias locales y mantener al mismo tiempo vistas coherentes de los datos en todo el mundo, supone un problema complejo. Los clientes usan bases de datos distribuidas globalmente, dada la necesidad de mejorar la latencia del acceso a los datos, conseguir una alta disponibilidad de estos, garantizar la recuperación ante desastres, y también para satisfacer sus requisitos empresariales. La funcionalidad de varios maestros de Azure Cosmos DB proporciona altos niveles de disponibilidad (99,999 %), latencia de milisegundos de una sola cifra para escribir datos y escalabilidad con compatibilidad completa y flexible con la resolución de conflictos. Estas características simplifican enormemente el desarrollo de aplicaciones distribuidas globalmente. En las aplicaciones distribuidas globalmente, es esencial la compatibilidad con varios maestros. 

![Arquitectura de varios maestros](./media/multi-region-writers/multi-master-architecture.png)

Gracias a la compatibilidad con varios maestros de Azure Cosmos DB, es posible realizar escrituras en contenedores de datos (por ejemplo, colecciones, grafos, tablas) distribuidos en cualquier parte del mundo. Puede actualizar los datos en cualquier región que esté asociada a su cuenta de base de datos. Estas actualizaciones de datos pueden propagarse de forma asincrónica. Además de proporcionar acceso rápido y latencia de escritura a los datos, la funcionalidad de varios maestros también proporciona una solución práctica para problemas de equilibrio de carga y conmutación por error. En resumen, con Azure Cosmos DB obtiene una latencia de escritura de <10 ms en el percentil 99, un 99,999 % de disponibilidad de lectura y escritura y la posibilidad de escalar el rendimiento de lectura y escritura, y todo esto en cualquier parte del mundo.   

## <a name="a-simple-multi-master-example--content-publishing"></a>Ejemplo sencillo de varios maestros: publicación de contenido  

Vamos a examinar un escenario del mundo real que describe cómo usar la compatibilidad con varios maestros con Azure Cosmos DB. Considere la posibilidad de una plataforma de publicación de contenido basada en Azure Cosmos DB. Estos son algunos requisitos que debe cumplir esta plataforma para una experiencia del usuario excelente para publicadores y consumidores. 

* Los autores y suscriptores están repartidos por todo el mundo.  

* Los autores deben publicar (escribir) artículos en su región local (más cercana).  

* Los autores tienen lectores/suscriptores de los artículos que se distribuyen en todo el mundo.  

* Los suscriptores deben recibir una notificación cuando se publican artículos nuevos.  

* Los suscriptores deben poder leer artículos desde su región local. También deben poder agregar revisiones a estos artículos.  

* Cualquiera, incluido al autor de los artículos, debe poder ver todas las revisiones asociadas a los artículos desde una región local.  

Si damos por supuesto millones de consumidores y publicadores con miles de millones de artículos, pronto tendremos que hacer frente a problemas de escala y para garantizar un acceso local. Este caso de uso es un perfecto candidato para la funcionalidad de varios maestros de Azure Cosmos DB. 

## <a name="benefits-of-having-multi-master-support"></a>Ventajas de tener la compatibilidad con varios maestros 

La compatibilidad con varios maestros es esencial para las aplicaciones distribuidas globalmente. La funcionalidad de varios maestros consta de [varias regiones maestras](distribute-data-globally.md) que participan por igual en un modelo de escritura en cualquier parte (patrón activo-activo) y se usa para garantizar que los datos están disponibles en cualquier momento allí donde los necesite. Las actualizaciones realizadas en una región individual se propagan asincrónicamente a todas las demás regiones (las cuales, a su vez, son regiones maestras por sí mismas). Las regiones de Azure DB Cosmos que funcionan como regiones maestras en una configuración de varios maestros trabajan automáticamente para reunir los datos de todas las réplicas y garantizar la [coherencia global y la integridad de los datos](consistency-levels.md). En la siguiente imagen se muestra la replicación de lectura/escritura de uno y varios maestros.

![Maestro único y varios maestros](./media/multi-region-writers/single-vs-multi-master.png)

La implementación de varios maestros supone una carga adicional para los desarrolladores. Los clientes a gran escala que intentan implementar por su cuenta la funcionalidad de varios maestros pueden tardar cientos de horas en configurar y probar una configuración de varios maestros a nivel mundial, y muchos cuentan con equipos de ingenieros dedicados cuyo único trabajo es supervisar y mantener la replicación de varios maestros. Crear y administrar la configuración de varios maestros por su cuenta le quita tiempo y recursos a la innovación de la propia aplicación y tiene como resultado costos mucho más altos. Azure Cosmos DB proporciona compatibilidad inmediata con varios maestros y deja a los desarrolladores libres de esta sobrecarga.  

En resumen, con varios maestros se obtienen las siguientes ventajas:

* **Mejora de la recuperación ante desastres, la disponibilidad de escritura y la conmutación por error**: se pueden usar varios maestros para preservar en mayor medida la alta disponibilidad de una base de datos de misión crítica. Por ejemplo, una base de datos de varios maestros puede replicar los datos de una región a una región de conmutación por error cuando la región principal deja de estar disponible debido a una interrupción o a un desastre regional. Esta región de conmutación por error servirá como región maestra completamente funcional para respaldar la aplicación. Con varios maestros se ofrece una mayor protección de la "capacidad de supervivencia" con respecto a los desastres naturales, interrupciones del suministro eléctrico o sabotaje, o las dos cosas, ya que el resto de regiones puede estar en configuraciones de varios maestros completamente diferentes con una disponibilidad de escritura garantizada > 99,999 %. 

* **Mejora de la latencia de escritura para los usuarios finales**: cuanto más se acerquen los datos (que se suministran) a los usuarios finales, mejor será la experiencia. Por ejemplo, si tiene usuarios en Europa, pero la base de datos está en los Estados Unidos o Australia, la latencia añadida es aproximadamente de 140 ms y 300 ms en sus respectivas regiones. Comenzar con retaso es inaceptable en muchos juegos conocidos, necesidades bancarias o aplicaciones interactivas (web o móviles). La latencia desempeña un papel importante en la percepción por el cliente de una experiencia de alta calidad y está demostrado que afecta hasta cierto punto al comportamiento de los usuarios. A medida que la tecnología mejora y, especialmente, con la llegada de la realidad virtual (VR), la realidad aumentada (AR) y la realidad mixta (MR), que requieren experiencias incluso más inmersivas y realistas, los desarrolladores necesitan producir ahora sistemas de software con requisitos de latencia rigurosos. Por lo tanto, es más importante que nunca tener aplicaciones y datos (contenido de las aplicaciones) disponibles localmente. Con la configuración de varios maestros de Azure Cosmos DB, el rendimiento es tan rápido como las lecturas y escrituras locales normales y se mejora globalmente gracias a la distribución geográfica.  

* **Mejora de la escalabilidad y el rendimiento de escritura**: la configuración de varios maestros proporcionará un mayor rendimiento y una mejor utilización, al tiempo que ofrece varios modelos de coherencia con garantía de exactitud, y todo ello respaldado por Acuerdos de Nivel de Servicio (SLA). 

  ![Escalado del rendimiento de escritura con varios maestros](./media/multi-region-writers/scale-write-throughput.png)

* **Mejora de la compatibilidad con entornos desconectados (por ejemplo, dispositivos perimetrales)**: la configuración de varios maestros permite a los usuarios replicar todos los datos, o un subconjunto de ellos, desde un dispositivo perimetral hasta la región más cercana en un entorno desconectado. Este escenario es habitual de los sistemas de automatización del personal de ventas, donde el equipo portátil de un individuo (un dispositivo desconectado) almacena un subconjunto de datos relacionados con el vendedor individual. Las regiones maestras de la nube que están ubicadas en cualquier parte del mundo pueden funcionar como destino de la copia desde los dispositivos perimetrales remotos.  

* **Equilibrio de carga**: con varios maestros, la carga en la aplicación se puede volver a equilibrar moviendo los usuarios y las cargas de trabajo desde una región muy cargada hasta regiones donde la carga está distribuida uniformemente. La capacidad de escritura se puede extender fácilmente agregando una nueva región y luego cambiando algunas escrituras a la nueva región. 

* **Mejor uso de la capacidad aprovisionada**: con varios maestros, en el caso de cargas de trabajo pesadas y mixtas, puede saturar la capacidad aprovisionada entre varias regiones.  En algunos casos, puede redistribuir lecturas y escrituras de manera más uniforme, de forma que no es necesario aprovisionar tanto rendimiento y se produce un mayor ahorro en los costos para los clientes.  

* **Arquitecturas de aplicación más sencillas y resistente**: las aplicaciones que se mueven a una configuración de varios maestros obtienen resistencia de los datos garantizada.  Como Azure Cosmos DB oculta toda la complejidad, se puede simplificar considerablemente la arquitectura y el diseño de la aplicación. 

* **Prueba de conmutación por error sin riesgos**: la prueba de conmutación por error no tiene ninguna degradación sobre el rendimiento de escritura. Con varios maestros, todas las demás regiones son maestros completos, por lo que la conmutación por error no tendrá mucho impacto sobre el rendimiento de escritura.  

* **Menor costo total de propiedad (TCO) y menos operaciones de desarrollo**: cumplir los objetivos de escalabilidad, rendimiento, distribución global y tiempo de recuperación es con frecuencia caro debido al alto costo que suponen los complementos y el mantenimiento de una infraestructura de copia de seguridad que está en reposo hasta que se produce el desastre. Con la funcionalidad de varios maestros de Azure Cosmos DB respaldada por Acuerdos de Nivel de Servicio líderes del sector, los desarrolladores ya no necesitan crear y mantener ellos mismos la lógica de interconexión con el back-end, sino que pueden estar tranquilos y dedicarse a ejecutar sus cargas de trabajo críticas. 

## <a name="use-cases-where-multi-master-support-is-needed"></a>Casos de uso donde se necesita la compatibilidad con varios maestros

Existen numerosos casos de uso de varios maestros en Azure Cosmos DB: 

* **IoT**: la funcionalidad de varios maestros de Azure Cosmos DB permite la implementación distribuida simplificada del procesamiento de datos de IoT. Las implementaciones de Edge geográficamente distribuidas que usan CRDT (tipos de datos replicados sin conflicto), con frecuencia necesitan realizar el seguimiento de los datos de series temporales desde varias ubicaciones. Cada dispositivo se puede asignar a una de las regiones más cercanas, y un dispositivo puede viajar (por ejemplo, un automóvil) y reasignarse dinámicamente para escribir en otra región.  

* **Comercio electrónico**: para garantizar una experiencia excelente del usuario en escenarios de comercio electrónico, se necesita alta disponibilidad y resistencia a situaciones de error. En caso de error en una región, otra región debe recoger las sesiones de usuario, los carros de la compra y las listas de deseos activas íntegramente sin pérdida de estado. Mientras tanto, las actualizaciones que realiza el usuario se deben tratar de forma adecuada (por ejemplo, las acciones de agregar y quitar del carro de la compra se deben transferir). Con varios maestros, Azure Cosmos DB puede resolver tales escenarios de manera fácil, con una perfecta transición entre las regiones activas, mientras se mantiene una vista coherente desde el punto de vista del usuario. 

* **Detección de fraudes y anomalías**: con frecuencia, las aplicaciones que supervisan la actividad del usuario o de la cuenta están distribuidas globalmente y deben mantener el seguimiento de varios eventos a la vez. Al crear y mantener las puntuaciones de un usuario, las acciones de diferentes regiones geográficas deben actualizar simultáneamente estas puntuaciones para mantener la métricas de riesgo alineadas. Azure Cosmos DB puede garantizar que los desarrolladores no tienen que tratar con situaciones de conflicto en el nivel de aplicación. 

* **Colaboración**: para aplicaciones que clasifican en función de la popularidad de artículos como productos en venta o medios que se van a consumir, etc. Realizar el seguimiento de la popularidad entre regiones geográficas puede resultar complicado, en especial cuando es necesario pagar regalías o tomar decisiones sobre publicidad en tiempo real. El hecho de clasificar, ordenar y comunicar información entre varias regiones de todo el mundo y en tiempo real con Azure Cosmos DB permite a los desarrolladores proporcionar características con poco esfuerzo y sin comprometer las latencias. 

* **Medición**: el recuento y la regulación del uso (como llamadas API, transacciones por segundo, minutos usados) se pueden implementar globalmente de forma fácil con la funcionalidad de varios maestros de Azure Cosmos DB. La resolución de conflictos integrada garantiza la exactitud de los recuentos y la regulación en tiempo real. 

* **Personalización**: tanto si va a mantener contadores distribuidos geográficamente que desencadenan acciones, como concesiones de puntos de fidelización, o va a implementar vistas de sesión de usuario personalizadas, la alta disponibilidad y el recuento distribuido geográficamente simplificado que proporciona Azure Cosmos DB permiten a las aplicaciones entregar alto rendimiento de forma sencilla. 

## <a name="conflict-resolution-with-multi-master"></a>Resolución de conflictos con varios maestros 

Con varios maestros, la dificultad es, con frecuencia, que dos (o más) réplicas del mismo registro se puedan actualizar a la vez por diferentes escritores de dos o más regiones diferentes. Las escrituras simultáneas pueden llevar a dos versiones diferentes del mismo registro y, sin resolución de conflictos integrada, es la propia aplicación quien debe realizarla para resolver esta incoherencia.  

**Ejemplo**: supongamos que usa Azure Cosmos DB Como almacén de persistencia para la aplicación del carro de la compra y esta aplicación se implementa en dos regiones: Este de EE. UU. y Oeste de EE. UU.  Si, casi al mismo tiempo, un usuario de San Francisco agrega un artículo a este carro de la compra (por ejemplo, un libro) mientras un proceso de administración del inventario de la región Este de EE. UU. invalida otro artículo del carro de la compra (por ejemplo, un nuevo teléfono) de ese mismo usuario en respuesta a una notificación del proveedor de que la fecha de lanzamiento se ha retrasado, a la hora T1, los registros del carro de la compra de las dos regiones son diferentes. La base de datos usa su mecanismo de replicación y resolución de conflictos para resolver esta incoherencia y que finalmente una de las dos versiones del carro de la compra se pueda seleccionar. Con la heurística de resolución de conflictos que la mayor parte del tiempo aplican las bases de datos de varios maestros (por ejemplo, la última escritura gana), resulta imposible para el usuario o la aplicación predecir qué versión se seleccionará. En cualquier caso, puede producirse la pérdida de datos o un comportamiento inesperado. Si se selecciona la versión de la región Este, la selección del usuario de un nuevo artículo de compra (es decir, un libro) se pierde, y si se selecciona la región Oeste, el artículo anteriormente elegido (es decir, el teléfono) sigue en el carro. En cualquiera de los casos, se pierde información. Por último, cualquier otro proceso que inspecciona el carro de la compra entre las horas T1 y T2 se va a ver también como un comportamiento no determinista. Por ejemplo, un proceso en segundo plano que selecciona el almacén de cumplimiento y actualiza los costos del carro de la compra generaría resultados que entran en conflicto con el contenido final del carro. Si el proceso se ejecuta en la región Oeste y la alternativa 1 se cumple, se calcularían los costos del envío de dos artículos, aun cuando el carro podría tener pronto uno solo, el libro. 

Azure Cosmos DB implementa la lógica de tratar las escrituras en conflicto dentro del propio motor de base de datos. Azure Cosmos DB ofrece **compatibilidad completa y flexible con la resolución de conflictos** al ofrecer varios modelos de resolución de conflictos, como Automático (CRDT, tipos de datos replicados sin conflicto), Last Write Wins (LWW), donde la última escritura prevalece, Personalizado (procedimiento almacenado) y Manual para la resolución automática de conflictos. Los modelos de resolución de conflictos proporcionan garantía de exactitud y coherencia y eliminan la carga que supone para los desarrolladores tener que pensar en coherencia, disponibilidad, rendimiento, latencia de replicación y combinaciones complejas de eventos en situaciones de conmutaciones por error geográficas y conflictos de escritura entre regiones.  

  ![Resolución de conflictos de varios maestros](./media/multi-region-writers/multi-master-conflict-resolution-blade.png)

Azure Cosmos DB ofrece tres tipos de modelos de resolución de conflictos. La semántica de los modelos de resolución de conflictos es la siguiente: 

**Automático**: esta es la directiva de resolución de conflictos predeterminada. Cuando se selecciona esta directiva, Azure Cosmos DB resuelve automáticamente las actualizaciones en conflicto en el servidor y proporciona sólidas garantías de coherencia final. Internamente, Azure Cosmos DB implementa la resolución de conflictos automática al aprovechar los tipos de datos replicados sin conflicto (CRDT) dentro del motor de base de datos.  

**Last-Write-Wins (LWW)**: al elegir esta directiva, donde la última escritura prevalece, puede resolver conflictos en función de la propiedad de marca de tiempo sincronizada definida por el sistema o de una propiedad personalizada definida en la versión en conflicto de los registros. La resolución de conflictos tiene lugar en el servidor y la versión con la marca de tiempo más reciente se selecciona como ganadora.  

**Personalizado**: puede registrar la lógica de resolución de conflictos definida por una aplicación mediante el registro de un procedimiento almacenado. El procedimiento almacenado se invoca tras la detección de conflictos de actualización bajo los auspicios de una transacción de base de datos, en el servidor. Si selecciona la opción, pero no registra un procedimiento almacenado (o si el procedimiento almacenado produce una excepción en tiempo de ejecución), puede acceder a todas las versiones en conflicto mediante la fuente de conflictos y resolverlos de forma individual.  

## <a name="next-steps"></a>Pasos siguientes  

En este artículo, aprendió a usar una configuración de varios maestros distribuida globalmente con Azure Cosmos DB. A continuación, eche un vistazo a los siguientes recursos: 

* [Más información sobre la compatibilidad de Azure Cosmos DB con la distribución global](distribute-data-globally.md)  

* [Más información sobre las conmutaciones por error manuales y automáticas en Azure Cosmos DB](regional-failover.md)  

* [Más información sobre la coherencia global con Azure Cosmos DB](consistency-levels.md)  

* Desarrollo con varias regiones mediante [API de SQL](tutorial-global-distribution-sql-api.md), [API MongoDB](tutorial-global-distribution-mongodb.md) o [API Table](tutorial-global-distribution-table.md) en Azure Cosmos DB  