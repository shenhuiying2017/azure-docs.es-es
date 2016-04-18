<properties
   pageTitle="Guía de creación de particiones de datos | Microsoft Azure"
   description="Orientaciones sobre cómo separar las particiones para administrarlas y tener acceso a ellas por separado."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/26/2016"
   ms.author="masashin"/>

# Guía de creación de particiones de datos

![](media/best-practices-data-partitioning/pnp-logo.png)

## Información general

En muchas soluciones a gran escala, los datos se dividen en particiones independientes que se pueden administrar y a las que se puede acceder por separado. Debe elegir cuidadosamente la estrategia de partición para maximizar los beneficios y minimizar los efectos adversos. La creación de particiones puede ayudar a mejorar la escalabilidad, reducir la contención y optimizar el rendimiento. Otra ventaja de este proceso es que puede proporcionar un mecanismo para dividir los datos por el patrón de uso. Por ejemplo, puede archivar los datos más antiguos y menos activos en el nivel de almacenamiento de datos más barato.

## ¿Por qué es recomendable crear particiones de datos?

La mayoría de los servicios y aplicaciones de nube almacenan y recuperan datos como parte de sus operaciones. El diseño de los almacenes de datos que utiliza una aplicación puede tener una incidencia significativa en el rendimiento y la escalabilidad de un sistema. Una técnica que se aplica normalmente en sistemas de gran escala es dividir los datos en particiones independientes.

> El término _creación de particiones_ usado en esta guía hace referencia al proceso de dividir físicamente los datos en almacenes de datos independientes. No es lo mismo que la creación de particiones de tablas de SQL Server, que remite a un concepto diferente.

Crear particiones de datos puede ofrecer una serie de ventajas. Por ejemplo, se puede aplicar para:

- **Mejorar la escalabilidad**. Al escalar verticalmente un sistema de base de datos única, este podría alcanzar un límite de hardware físico. Si divide datos en varias particiones, cada una de las cuales se hospeda en un servidor independiente, tiene la posibilidad de escalar horizontalmente el sistema de manera casi indefinida.
- **Mejorar el rendimiento**. Las operaciones de acceso a datos en cada partición se realizan en un volumen de datos menor. Siempre que los datos se dividan de forma adecuada, las creación de particiones puede aumentar la eficiencia del sistema. Las operaciones que afectan a más de una partición pueden ejecutarse en paralelo. Cada partición puede estar cerca de la aplicación que la utiliza para minimizar la latencia de red.
- **Mejorar la disponibilidad**. Separar los datos en varios servidores evita un punto único de error. Si se produce un error en un servidor o están realizando realizando tareas de mantenimiento planeada, solo estarán disponibles los datos de esa partición. Las operaciones en otras particiones pueden continuar. Aumentar el número de particiones reduce el impacto relativo de un error de servidor único reduciendo el porcentaje de los datos que no van a estar disponibles. Replicar cada partición puede reducir todavía más la posibilidad de que se produzca un error en una única partición que afecte a las operaciones. También permite separar los datos críticos que deben estar disponibles continuamente de los datos menos valiosos que tienen requisitos de baja disponibilidad (por ejemplo, los archivos de registro).
- **Mejorar la seguridad**. Según la naturaleza de los datos y de cómo estén particionados, es posible separar los datos confidenciales y no confidenciales en particiones distintas y, por tanto, en diferentes servidores o almacenes de datos. Por lo tanto, es posible optimizar la seguridad de manera específica para los datos confidenciales.
- **Proporcionan flexibilidad operativa**. La creación de particiones ofrece muchas oportunidades para ajustar con precisión las operaciones, maximizar la eficacia administrativa y minimizar los costes. Por ejemplo, puede definir diferentes estrategias para la administración, la supervisión, la creación de copias de seguridad y la restauración, así como otras tareas administrativas, en función de la importancia de los datos de cada partición.
- **Adaptación del almacén de datos al patrón de uso**. La creación de particiones permite la implementación de cada partición en un tipo de almacén de datos diferente en función de costo y las características integradas que ofrece el almacén de datos. Por ejemplo, podrían almacenarse datos binarios de grandes dimensiones en un almacén de datos blob, aunque se podrían mantener datos más estructurados en una base de datos de documentos. Para más información, consulte [Building a polyglot solution] (Creación de una solución políglota) en la guía de patrones y prácticas y [Data access for highly-scalable solutions: Using SQL, NoSQL, and polyglot persistence] (Acceso a datos para soluciones altamente escalables: uso de SQL, NoSQL y persistencia políglota) en el sitio web de Microsoft.

Algunos sistemas no implementan la creación de particiones porque se considera un costo adicional en lugar de una ventaja. Entre las causas más comunes de este razonamiento se incluyen:

- Muchos sistemas de almacenamiento de datos no admiten combinaciones entre las particiones, y es posible que resulte difícil de mantener la integridad referencial en un sistema con particiones. A menudo es necesario implementar combinaciones y comprobaciones de integridad en el código de la aplicación (en la capa de partición), que puede dar lugar a E/S adicionales y al aumento de la complejidad de la aplicación.
- El mantenimiento de las particiones no siempre es una tarea trivial. En un sistema en el que los datos son volátiles, puede que necesite volver a equilibrar las particiones periódicamente para reducir la contención y las zonas demasiado activas.
- Algunas herramientas comunes no funcionan de manera natural con datos con particiones.

## Diseño de particiones

Los datos se pueden dividir en particiones de maneras diferentes: horizontal, vertical o funcionalmente. La estrategia que elija depende de la razón por la que efectúa la partición de los datos y los requisitos de las aplicaciones y servicios que usarán los datos.

> [AZURE.NOTE] Los esquemas de creación de particiones descritos en esta guía se explican de manera independiente a la tecnología de almacenamiento de datos subyacente. Se pueden aplicar a muchos tipos de almacenes de datos, incluidas las bases de datos relacionales y NoSQL.

### Estrategias de creación de particiones

Las tres estrategias típicas de creación de particiones de datos son:

- **Particiones horizontales** (a menudo denominadas _particionamiento_). En esta estrategia, cada partición es un almacén de datos por derecho propio, pero todas las particiones tienen el mismo esquema. Cada división se conoce como _partición_ y contiene un subconjunto específico de los datos, como todos los pedidos de un conjunto específico de clientes en una aplicación de comercio electrónico.
- **Particiones verticales**. En esta estrategia, cada partición contiene un subconjunto de los campos de elementos del almacén de datos. Los campos se dividen según su patrón de uso. Por ejemplo, los campos a los que se accede con frecuencia pueden colocarse en una partición vertical y los campos que se utilizan más raramente en otra.
- **Creación de particiones funcional**. En esta estrategia, los datos se agregan en función de cómo los usa cada contexto limitado en el sistema. Por ejemplo, un sistema de comercio electrónico que implementa funciones empresariales independientes para la facturación y administración de inventarios de productos puede almacenar datos de la factura en una partición y datos del inventario de productos en la otra.

Es importante tener en cuenta que se pueden combinar las tres estrategias que se describen aquí. No son mutuamente excluyentes, y es recomendables considerarlas todas cuando se diseña un esquema de creación de particiones. Por ejemplo, puede dividir los datos en particiones y, a continuación, usar particiones verticales para subdividir todavía más los datos de cada partición. De forma similar, los datos de una partición funcional se pueden dividir en particiones (que pueden también dividirse verticalmente).

Sin embargo, los diferentes requisitos de cada estrategia pueden plantear una serie de problemas en conflicto. Debe evaluar y equilibrar todos ellos al diseñar un esquema de partición que cumpla los objetivos de rendimiento del procesamiento de datos global para su sistema. Las secciones siguientes exploran cada una de las estrategias más detalladamente.

### Creación de particiones horizontales (particionamiento)

En la figura 1 se muestra una visión general de la creación de particiones horizontales o particionamiento. En este ejemplo, los datos del inventario de productos se dividen en particiones según la clave del producto. Cada partición contiene los datos de un intervalo contiguo de claves de partición (A-G y H-Z), organizadas alfabéticamente.

![Creación de particiones horizontales de datos basadas en una clave de partición](media/best-practices-data-partitioning/DataPartitioning01.png)

_Ilustración 1: Creación de particiones horizontales de datos basadas en una clave de partición_

El particionamiento le ayuda a distribuir la carga entre más equipos, lo cual reduce la contención y mejora el rendimiento. Puede escalar el sistema horizontalmente agregando más particiones que se ejecutan en servidores adicionales.

El factor más importante al implementar esta estrategia de partición es la opción de clave de particionamiento. Puede resultar difícil cambiar la clave después de que el sistema está en funcionamiento. La clave debe asegurarse de que los datos están divididos en particiones para que la carga de trabajo sea lo más uniforme posible entre las particiones.

Tenga en cuenta que no es necesario que las distintas particiones contengan volúmenes de datos similares. En realidad, lo más importante es equilibrar el número de solicitudes. Algunas particiones podrían ser muy grandes pero contener un número de elementos que reciben un número reducido de operaciones de acceso. En el caso opuesto, otras particiones podrían ser más pequeñas pero contener un número de elementos a los que se accede con mayor frecuencia. También es importante asegurarse de que una sola partición no supere los límites de escala (en términos de capacidad y recursos de procesamiento) del almacén de datos que se utiliza para hospedar esa partición.

Si usa un esquema de particionamiento, evite la creación de zonas muy activas (o particiones calientes) que pueden afectar al rendimiento y la disponibilidad. Por ejemplo, si usa un algoritmo hash de un identificador de cliente en lugar de la primera letra de su nombre, evitará la distribución desequilibrada que resulta del uso de letras iniciales comunes y menos comunes. Se trata de una técnica habitual que ayuda a distribuir los datos de forma más equitativa entre las particiones.

Elija una clave de particionamiento que minimice cualquier necesidad futura de dividir particiones grandes en partes más pequeñas, fusionar particiones pequeñas en particiones mayores o cambiar el esquema que describe los datos almacenados en un conjunto de particiones. Estas operaciones pueden llevar mucho tiempo y pueden requerir desconectar una o más particiones sin conexión mientras se llevan a cabo.

Si se replican las particiones, debería ser posible conservar algunas de las réplicas en línea mientras otras se dividen, se combinan o se vuelven a configurar. Sin embargo, el sistema podría requerir la limitación de las operaciones que se pueden realizar en los datos de esas particiones mientras lleva a cabo la reconfiguración. Por ejemplo, los datos de las réplicas podrían marcarse como de solo lectura para limitar el ámbito de las incoherencias que podrían producirse mientras se están reestructurando las particiones.

> Para más información e instrucciones sobre muchas de estas consideraciones y técnicas de prácticas recomendadas de diseño de almacenes de datos que implementan particiones horizontales, consulte [Sharding pattern] (Patrón de particionamiento).

### Particiones verticales

El uso más común para la partición vertical es reducir los costes de E/S y de rendimiento asociados con la recopilación de los elementos a los que se tiene acceso con más frecuencia. La ilustración 2 muestra un ejemplo de creación de particiones verticales. En este ejemplo, se mantienen diferentes propiedades para cada elemento de datos en diferentes particiones. Una partición contiene datos a los que se tiene acceso con mayor frecuencia, incluidos el nombre, la descripción y la información de precios de los productos. Otra contiene el volumen en existencias y la fecha del último pedido.

![Creación de particiones verticales de datos por su patrón de uso](media/best-practices-data-partitioning/DataPartitioning02.png)

_Ilustración 2: Creación de particiones verticales de datos por su patrón de uso_

En este ejemplo, la aplicación consulta periódicamente el nombre del producto, la descripción y el precio al mostrar los detalles de los productos a los clientes. El nivel de existencias y la fecha en la que se efectuó el último pedido del producto al fabricante se mantienen en una partición independiente porque estos dos elementos se utilizan conjuntamente.

Este esquema de partición tiene la ventaja agregada de que los datos de movimiento relativamente lento (nombre de producto, descripción y precio) están separados de los datos más dinámicos (nivel de existencias y fecha del último pedido). Una aplicación podría encontrar beneficioso almacenar en la caché los datos de movimiento lento de la memoria si se tiene acceso a ellos con frecuencia.

Otro escenario típico de esta estrategia de partición es maximizar la seguridad de los datos confidenciales. Por ejemplo, puede hacerlo mediante el almacenamiento de los números de tarjetas de crédito y los números correspondientes de comprobación de la seguridad de la tarjeta correspondientes en particiones independientes.

La creación de particiones verticales también puede reducir la cantidad de acceso simultáneo necesario a los datos.

> La creación de particiones verticales funciona a nivel de entidad dentro de un almacén de datos, normalizando parcialmente una entidad para dividirla de un elemento _amplio_ en un conjunto de elementos _reducido_. Es ideal para almacenes de datos orientados a columnas como HBase y Cassandra. Si es poco probable que los datos de una colección de columnas cambien, también puede considerar el uso de almacenes de columnas en SQL Server.

### Creación de particiones funcional

Para los sistemas en los que es posible identificar un contexto limitado para cada área de negocio o servicio independiente en la aplicación, la creación de particiones funcional proporciona una técnica para mejorar el rendimiento de acceso a datos y el aislamiento. Otro uso común de la creación de particiones funcional es separar los datos de lectura y escritura de los datos de solo lectura que se utilizan para elaborar informes. En la figura 3 se muestra una visión general de la creación de particiones donde se separan los datos de inventario de los datos del cliente.

![Creación de particiones de datos funcionalmente por contexto o subdominio vinculado](media/best-practices-data-partitioning/DataPartitioning03.png)

_Ilustración 3: Creación de particiones de datos funcionalmente por contexto o subdominio vinculado_

Esta estrategia de creación de particiones puede ayudar a reducir la contención de acceso a datos a través de distintas partes de un sistema.

## Diseño de particiones para obtener escalabilidad

Es fundamental considerar el tamaño y la carga de trabajo de cada partición y equilibrarlos para que los datos se distribuyan y logren alcanzar una máxima escalabilidad. Sin embargo, también debe particionar los datos para que no superen los límites de escala de un almacén de una única partición.

Siga estos pasos durante el diseño de particiones para obtener escalabilidad:

1. Analice la aplicación para comprender los patrones de acceso a datos, como el tamaño del conjunto de resultados devuelto por cada consulta, la frecuencia de acceso, la latencia inherente y los requisitos de procesamiento del lado del servidor. En muchos casos, unas cuantas entidades principales exigirán la mayoría de los recursos de procesamiento.
2. Utilice este análisis para determinar los objetivos de escalabilidad actuales y futuros, como el tamaño de los datos y la carga de trabajo. A continuación, distribuya los datos entre las particiones para cumplir el objetivo de escalabilidad. En la estrategia de creación de particiones horizontal, es importante elegir la partición adecuada para asegurarse de que la distribución es equilibrada. Para más información, consulte el [patrón particionamiento].
3. Asegúrese de que los recursos disponibles de cada partición sean suficientes para controlar los requisitos de escalabilidad en términos de rendimiento y tamaño de datos. Por ejemplo, el nodo que hospeda una partición puede imponer un límite riguroso en la cantidad de espacio de almacenamiento, la potencia de procesamiento o el ancho de banda de red que proporciona. Si los requisitos de almacenamiento y procesamiento de datos suelen superar estos límites, podría ser necesario perfeccionar la estrategia de creación de particiones o dividir todavía más los datos. Por ejemplo, un planteamiento orientado a la escalabilidad podría ser la separación de los datos de registro de las características principales de la aplicación. Puede hacerlo mediante el uso de almacenes de datos independientes para evitar que los requisitos de almacenamiento de datos total superen el límite de escala del nodo. Si el número total de almacenes de datos supera el límite de nodos, puede ser necesario usar nodos de almacenamiento independientes.
4. Supervise el sistema en uso para comprobar que los datos se distribuyen según lo previsto y que las particiones pueden controlar la carga impuesta en ellos. Es posible que el uso no coincida con el uso previsto por el análisis. En ese caso, quizá se pueda volver a equilibrar las particiones. En caso contrario, puede ser necesario volver a diseñar algunas partes del sistema para obtener el equilibrio necesario.

Tenga en cuenta que algunos entornos de nube asignan recursos en términos de límites de infraestructura. Asegúrese de que los límites seleccionados proporcionen suficiente espacio para cualquier crecimiento previsto en el volumen de datos, en términos de ancho de banda, potencia de procesamiento y almacenamiento de datos.

Por ejemplo, si usa Almacenamiento de tablas de Azure, una partición ocupada podría requerir más recursos de los que están disponibles para una sola partición a fin de dar respuesta a las solicitudes. (Existe un límite en el volumen de solicitudes que pueden administrarse mediante una sola partición en un período de tiempo concreto. Para más información, consulte la página [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure] en el sitio web de Microsoft).

 En este caso, es posible que se necesite crear una partición adicional dentro de la propia partición para repartir la carga. Si el tamaño total o el rendimiento de estas tablas supera la capacidad de una cuenta de almacenamiento, puede ser necesario crear cuentas de almacenamiento adicionales y repartir las tablas entre estas cuentas. Si el número de cuentas de almacenamiento supera el número de cuentas que están disponibles para una suscripción, es posible que resulte necesario usar varias suscripciones.

## Diseño de particiones para aumentar el rendimiento de las consultas

El rendimiento de las consultas a menudo puede ampliarse mediante el uso de conjuntos de datos más pequeños y la ejecución de consultas en paralelo. Cada partición debe contener una pequeña proporción de todo el conjunto de datos. Esta reducción en el volumen puede mejorar el rendimiento de las consultas. Sin embargo, la partición no es una alternativa para diseñar y configurar una base de datos de forma adecuada. Por ejemplo, asegúrese de que tiene los índices necesarios establecidos si está usando una base de datos relacional.

Al diseñar las particiones para aumentar el rendimiento de las consultas, siga estos pasos:

1. Examine los requisitos de la aplicación y el rendimiento:
	- Use los requisitos empresariales para determinar las consultas más importantes que siempre deben ejecutarse rápidamente.
	- Supervise el sistema para identificar las consultas que se ejecutan lentamente.
	- Establezca las consultas que se realizan con mayor frecuencia. Una sola instancia de cada consulta podría tener un coste mínimo, pero el consumo acumulado de recursos podría ser importante. Puede ser beneficioso separar los datos recuperados por estas consultas en una partición distinta o incluso una caché.
2. Para crear una partición en los datos que están provocando un rendimiento lento, haga lo siguiente:
	- Limitar el tamaño de cada partición para que el tiempo de respuesta de la consulta se encuentre dentro del objetivo.
	- Diseñar la clave de partición de manera que la aplicación pueda encontrar fácilmente la partición si está implementando la creación de particiones horizontal. Esto evita que la consulta tenga que examinar cada partición.
	- Considerar la ubicación de una partición. Si es posible, intente mantener los datos en particiones que estén geográficamente cerca de las aplicaciones y los usuarios que acceden a ellos.
3. Si una entidad tiene requisitos de rendimiento y de rendimiento de consultas, use la creación de particiones funcional basada en esa entidad. Si esto todavía no logra satisfacer los requisitos, aplique también la creación de particiones horizontal. En la mayoría de los casos bastará una estrategia de partición única, pero en algunos casos, resulta más eficaz combinar ambas estrategias.
4. Considere utilizar consultas asincrónicas que se ejecutan en paralelo entre las particiones para mejorar el rendimiento.

## Diseño de particiones para obtener disponibilidad

Crear particiones de datos puede mejorar la disponibilidad de aplicaciones asegurándose de que todo el conjunto de datos no constituye un punto de error único y que subconjuntos individuales del conjunto de datos pueden administrarse de forma independiente. La replicación de las particiones que contienen datos críticos también puede mejorar la disponibilidad.

Al diseñar e implementar las particiones, tenga en cuenta los siguientes factores que afectan a la disponibilidad:

- **Lo importante que son los datos para las operaciones empresariales**. Algunos datos pueden contener información empresarial crítica como detalles de facturas o transacciones bancarias. Otros datos pueden ser simplemente datos operativos menos importantes, como archivos de registro, seguimientos de rendimientos, etc. Después de identificar cada tipo de datos, considere lo siguiente:
	- Almacenar datos críticos en las particiones de alta disponibilidad con un plan de copia de seguridad adecuado.
	- Establecer mecanismos de administración y supervisión independientes o procedimientos para los diferentes elementos más importantes de cada conjunto de datos. Coloque los datos con el mismo nivel de importancia en la misma partición para que pueda efectuar una copia de seguridad de estos conjuntamente con una frecuencia adecuada. Por ejemplo, es posible que las particiones que contienen datos de transacciones bancarias necesiten copias de seguridad con más frecuencia que las particiones que contienen información de seguimiento o registro.
- **La manera de administrar particiones individuales**. Diseñar particiones para admitir el mantenimiento y administración independientes ofrece varias ventajas. Por ejemplo:
	- Si se produce un error en una partición, puede recuperarse de forma independiente sin afectar a las instancias de aplicaciones que tienen acceso a datos de otras particiones.
	- Es posible que la creación de particiones de datos por área geográfica permita la realización de tareas de mantenimiento programadas en horas de poco tráfico para cada ubicación. Asegúrese de que las particiones no sean demasiado grandes para evitar que se complete cualquier tarea de mantenimiento planeada durante este período.
- **Si va a replicar datos críticos en particiones**. Esta estrategia puede mejorar la disponibilidad y rendimiento, aunque también puede originar problemas de coherencia. Los cambios realizados en los datos de una partición tardan algún tiempo en sincronizarse con cada réplica. Durante este período, diferentes particiones contendrán diferentes valores de datos.

## Descripción de cómo afecta la creación de particiones al diseño y el desarrollo

El uso de particiones agrega complejidad al diseño y el desarrollo del sistema. Considere la creación de particiones como una parte fundamental del diseño del sistema, incluso si el sistema contiene una única partición inicialmente. Si se plantea la creación de particiones en un momento posterior, cuando el sistema empieza a sufrir problemas de rendimiento y escalabilidad, la dificultad será mayor porque ya tiene un sistema activo que mantener.

Si actualiza el sistema para incorporar la creación de particiones en este entorno, será necesario modificar la lógica de acceso a datos. También puede requerir la migración de grandes cantidades de datos existentes para distribuirlos entre las particiones, y a menudo los usuarios esperan seguir utilizando el sistema en este tiempo.

En algunos casos, la creación de particiones no se considera importante porque el conjunto de datos inicial es pequeño y puede controlarse fácilmente mediante un solo servidor. Esto puede ocurrir en un sistema que no se espera que aumente más allá de su tamaño inicial; sin embargo, muchos sistemas comerciales requieren una ampliación a medida que aumenta el número de usuarios. Esta expansión normalmente viene acompañada de un crecimiento en el volumen de datos.

También es importante entender que la creación de particiones no siempre está disponible en almacenes de datos de gran tamaño. Por ejemplo, es posible que cientos de clientes accedan de manera intensa a un almacén de datos pequeño a la vez. Crear particiones de los datos en esta situación puede ayudar a reducir la contención y a mejorar el rendimiento.

Tenga en cuenta los siguientes puntos cuando se diseña un esquema de partición de datos:

- **Siempre que sea posible, mantenga los datos para las operaciones de base de datos más habituales juntos en cada partición para minimizar las operaciones de acceso a datos entre particiones**. Realizar consultas en varias particiones puede llevar más tiempo que efectuar consultas solo dentro de una sola partición, pero la optimización de las particiones para un conjunto de consultas puede afectar adversamente a otros conjuntos de consultas. Cuando la realización de consultas en varias particiones sea inevitable, minimice el tiempo de consulta mediante la ejecución de consultas en paralelo y la adición de los resultados en la aplicación. Es posible que no sea posible aplicar este planteamiento en algunos casos, como cuando es necesario obtener un resultado de una consulta y usarlo en la consulta siguiente.
- **Si las consultas hacen uso de datos de referencia relativamente estáticos, como tablas de códigos postales o listas de productos, considere la posibilidad de replicar estos datos en todas las particiones para reducir la necesidad de realizar diferentes operaciones de búsqueda en las distintas particiones**. Este enfoque también puede reducir la probabilidad de que los datos de referencia se conviertan en un conjunto de datos excesivamente activo que soporte un tráfico intenso desde las diferentes partes del sistema. Sin embargo, la sincronización de los cambios que pudieran producirse en estos datos de referencia conlleva un costo asociado.
- **Siempre que sea posible, minimice los requisitos de integridad referencial en las particiones verticales y funcionales**. En estos esquemas, la propia aplicación es responsable de mantener la integridad referencial entre las particiones cuando se actualizan y consumen datos. Las consultas que deben combinar los datos de varias particiones se ejecutan más lentamente que las consultas que solo combinan datos de la misma partición porque la aplicación normalmente necesita realizar consultas consecutivas basadas en una clave y, a continuación, en una clave externa. En su lugar, considere la posibilidad de replicar o anular la normalización de los datos pertinentes. Para minimizar el tiempo de consulta donde son necesarias las combinaciones entre particiones, ejecute consultas en paralelo en las particiones y combine los datos dentro de la aplicación.
- **Tenga en cuenta el efecto que podría tener el esquema de creación de particiones en la coherencia de los datos en las particiones.** Evalúe si realmente es necesaria una alta coherencia. En su lugar, un enfoque común en la nube es implementar la coherencia eventual. Los datos de cada partición se actualizan por separado, y la lógica de aplicación garantiza que todas las actualizaciones se completen correctamente. También controla las incoherencias que pueden surgir de la consulta de datos mientras se esté ejecutando una operación finalmente coherente. Para obtener más información acerca la implementación de la coherencia eventual, consulte [Data consistency primer] (Información básica sobre la coherencia de datos).
- **Tenga en cuenta cómo buscan las consultas la partición correcta**. Si una consulta debe analizar todas las particiones para localizar los datos necesarios, habrá un impacto significativo en el rendimiento, incluso cuando se ejecuten varias consultas en paralelo. Las consultas utilizadas con las estrategias de creación de particiones verticales y funcionales pueden especificar las particiones de manera natural. Sin embargo, cuando se utiliza la creación de particiones horizontal, encontrar un elemento puede resultar difícil porque cada partición tiene el mismo esquema. Una solución típica de particionamiento consiste en mantener una asignación que puede usarse para buscar la ubicación de la partición para elementos específicos de datos. Esta asignación puede implementarse en la lógica de particionamiento de la aplicación o mantenerse por el almacén de datos si admite el particionamiento transparente.
- **Cuando se utiliza una estrategia de partición horizontal, considere la posibilidad de reequilibrar periódicamente las particiones**. Esto ayuda a distribuir los datos uniformemente por tamaño y por carga de trabajo para minimizar las zonas excesivamente activas, maximizar el rendimiento de las consultas y evitar las limitaciones de almacenamiento físico. Sin embargo, esta es una tarea compleja que requiere a menudo el uso de una herramienta personalizada o un proceso.
- **Si se replica cada partición, se proporciona protección adicional frente a errores**. Si se produce un error en una única réplica, las consultas se pueden dirigir hacia una copia de trabajo.
- **Si se alcanzan los límites físicos de una estrategia de creación de particiones, es posible que necesite ampliar la escalabilidad a un nivel diferente**. Por ejemplo, si la creación de particiones se produce en el nivel de base de datos, puede que tenga que buscar o replicar las particiones en varias bases de datos. Si la creación de particiones ya está al nivel de la base de datos y las limitaciones físicas son un problema, quizá tenga que ubicar o replicar particiones en varias cuentas de hospedaje.
- **Evite las transacciones que accedan a datos en varias particiones**. Algunos almacenes de datos implementan la coherencia transaccional y la integridad de las operaciones que modifican datos, pero solo cuando los datos se encuentran en una sola partición. Si necesita compatibilidad transaccional en varias particiones, probablemente necesitará implementar esto como parte de la lógica de la aplicación, porque la mayoría de los sistemas de creación de particiones no proporcionan compatibilidad nativa.

Todos los almacenes de datos requieren algunas operaciones de administración operativa y de supervisión de las actividades. Las tareas pueden oscilar entre la carga de datos, la realización de copias de seguridad y la restauración de datos, la reorganización de datos y asegurarse de que el sistema funciona de manera correcta y eficaz.

Tenga en cuenta los siguientes factores que afectan a la administración operativa:

- **Cómo implementar las tareas operativas y de administración adecuadas cuando se crean particiones en los datos**. Estas tareas pueden incluir la realización de copias de seguridad y su restauración, el archivado de datos, la supervisión del sistema y otras tareas administrativas. Por ejemplo, mantener la coherencia lógica durante las operaciones de copia de seguridad y restauración puede ser un desafío.
- **Cómo cargar los datos en varias particiones y agregar datos nuevos que llegan desde otros orígenes**. Algunas herramientas y utilidades podrían no admitir operaciones de datos con particiones, como la carga de datos en la partición correcta. Esto implica que es posible que deba crear u obtener nuevas herramientas y utilidades.
- **Cómo archivar y eliminar los datos de forma periódica**. Para evitar un aumento excesivo de las particiones, debe archivar y eliminar datos de forma regular (quizás mensualmente). Puede ser necesario transformar los datos para que se adapten a un esquema de almacenamiento diferente.
- **Cómo localizar problemas de integridad de datos**. Considere la posibilidad de ejecutar un proceso periódico para localizar cualquier problema de integridad de datos; por ejemplo, la existencia de datos en una partición que hacen referencia a una información que falta en otra. El proceso podría intentar corregir estos problemas automáticamente o generar una alerta a un operador para corregirlos manualmente. Por ejemplo, en una aplicación de comercio electrónico, es posible que se mantenga la información de los pedidos en una partición pero que los artículos de la línea que componen cada pedido se encuentren en la otra. El proceso de realización de pedidos requiere la adición de datos a ambas particiones. Si este proceso da error, podría haber elementos de línea almacenados para los que no haya ningún pedido correspondiente.

Las tecnologías de almacenamiento de datos diferentes suelen proporcionan sus propias características para admitir la creación de particiones. Las siguientes secciones resumen las opciones que se implementan por los almacenes de datos usados habitualmente por las aplicaciones de Azure. También se describen aspectos que debe tener en cuenta para diseñar aplicaciones que puedan aprovechar al máximo estas características.

## Estrategias de creación de particiones de la Base de datos SQL de Azure

La Base de datos SQL de Azure es una base de datos como servicio relacional que se ejecuta en la nube. Se basa en Microsoft SQL Server. Una base de datos relacional divide la información en tablas y cada tabla contiene información sobre entidades como una serie de filas. Cada fila contiene columnas que contienen los datos de los campos individuales de una entidad. La página [¿Qué es Base de datos SQL?] del sitio web de Microsoft ofrece documentación detallada sobre la creación y el uso de bases de datos SQL.

## Particiones horizontales con Base de datos elástica

Una única base de datos SQL puede contener un volumen de datos limitado. El rendimiento está restringido por factores arquitectónicos y el número de conexiones simultáneas que admite. La característica Base de datos elástica de Base de datos SQL admite el escalado horizontal de una base de datos SQL. Mediante Base de datos elástica es posible dividir los datos en particiones que se reparten en varias bases de datos SQL. También puede agregar o quitar particiones a medida que crezca y disminuya el volumen de datos que necesita administrar. El uso de Base de datos elástica también puede ayudar a reducir la contención al distribuir la carga entre las bases de datos.

> [AZURE.NOTE] Base de datos elástica es un sustituto de la característica Federaciones de Base de datos SQL de Azure. Las instalaciones existentes de federación de Base de datos SQL se pueden migrar a Base de datos elástica mediante la utilidad de migración de federaciones. También puede implementar su propio mecanismo de creación de particiones si su escenario no se presta naturalmente a las características proporcionadas por Base de datos elástica.

Cada partición se implementa como una base de datos SQL. Una partición puede contener más de un conjunto de datos (denominado _shardlet_). Cada base de datos mantiene metadatos que describen los shardlets que contiene. Un shardlet puede ser un único elemento de datos, o puede ser un grupo de elementos que comparte la misma clave de shardlet. Por ejemplo, si va a particionar datos en una aplicación de varios inquilinos, la clave del shardlet podría ser el id. de inquilino y todos los datos de un inquilino determinado se mantendrían como parte del mismo shardlet. Los datos de otros inquilinos se mantendrían en shardlets diferentes.

Es responsabilidad del programador asociar un conjunto de datos con una clave de shardlet. Una base de datos SQL independiente actúa como administrador global de mapas de particiones. Esta base de datos contiene una lista de todas las particiones y shardlets del sistema. Una aplicación cliente que tiene acceso a datos se conecta primero a la base de datos del administrador global del mapa de particiones para obtener una copia de este (lista de particiones y shardlets) que almacena luego en la caché localmente.

A continuación, la aplicación usa esta información para enrutar las solicitudes de datos a la partición apropiada. Esta funcionalidad se oculta detrás de una serie de API contenidas en la biblioteca de cliente de Base de datos elástica de Base de datos SQL de Azure, disponible como un paquete de NuGet. En la página [Información general de las características de Base de datos elástica] del sitio web de Microsoft se proporciona una introducción más completa a Base de datos elástica.

> [AZURE.NOTE] Puede replicar la base de datos de administrador global del mapa de particiones para reducir la latencia y mejorar la disponibilidad. Si implementa la base de datos mediante uno de los planes de tarifa Premium, puede configurar la replicación geográfica activa para copiar continuamente datos a bases de datos de diferentes regiones. Cree una copia de la base de datos en cada región en la que estén basados los usuarios. A continuación, configure la aplicación para conectarse a esta copia a fin de obtener el mapa de particiones.

> Un enfoque alternativo es usar SQL Data Sync de Azure o una canalización de Data Factory de Azure para replicar la base de datos de administrador de mapa de particiones entre regiones. Esta forma de replicación se ejecuta periódicamente y es más adecuada si el mapa de particiones no cambia con frecuencia. Además, la base de datos del administrador del mapa de particiones no debe crearse con un plan de tarifa Premium.

Base de datos elástica proporciona dos esquemas para asignar datos a shardlets y para almacenarlos en particiones:

- Un **mapa de particiones de lista** describe una asociación entre una clave única y un shardlet. Por ejemplo, en un sistema de varios inquilinos, los datos de cada inquilino podrían asociarse a una clave única y almacenarse en su propio shardlet. Para garantizar la privacidad y el aislamiento (esto es, impedir que un inquilino agote los recursos de almacenamiento de datos disponibles para otros usuarios), cada shardlet puede mantenerse dentro de su propia partición.

![Uso de un mapa de particiones de lista para almacenar datos de inquilino en particiones independientes](media/best-practices-data-partitioning/PointShardlet.png)

_Ilustración 4: Uso de un mapa de particiones de lista para almacenar datos de inquilino en particiones independientes_

- Un **mapa de particiones de intervalo** describe una asociación entre un conjunto de valores de claves contiguos y un shardlet. En el ejemplo de varios inquilinos que se ha descrito anteriormente, una alternativa a la implementación de shardlets específicos podría ser agrupar los datos de un conjunto de inquilinos (cada uno con su propia clave) en el mismo shardlet. Este esquema es menos costoso que el primero (los inquilinos comparten los recursos de almacenamiento de datos), pero con riesgo de reducir el aislamiento y la privacidad de los datos.

![Uso de un mapa de particiones de intervalo para almacenar los datos de un intervalo de inquilinos de una partición](media/best-practices-data-partitioning/RangeShardlet.png)

_Ilustración 5: Uso de un mapa de particiones de intervalo para almacenar los datos de un intervalo de inquilinos de una partición_

Tenga en cuenta que una sola partición puede contener los datos de varios shardlets. Por ejemplo, puede utilizar shardlets de lista para almacenar datos de varios inquilinos no contiguos en la misma partición. También puede mezclar shardlets de intervalo y de lista en la misma partición, aunque se tratarán a través de diferentes mapas en la base de datos de administrador global del mapa de particiones (la base de datos de administrador global del mapa de particiones puede contener varios mapas de particiones). En la Figura 6 se describe este enfoque.

![Implementación de varios mapas de particiones](media/best-practices-data-partitioning/MultipleShardMaps.png)

_Ilustración 6. Implementación de varios mapas de particiones_

El esquema de partición que se implementa puede tener una incidencia significativa en el rendimiento del sistema. También puede afectar a la frecuencia con que deben agregarse o quitarse particiones, o con que se deben volver a particionar los datos entre las diferentes particiones. Al utilizar Base de datos elástica para particionar los datos, tenga en cuenta los aspectos siguientes:

- Agrupe los datos que se utilizan conjuntamente en la misma partición y evite las operaciones que necesitan tener acceso a los datos almacenados en varias particiones. Tenga en cuenta que, con Base de datos elástica, una partición es una base de datos SQL por derecho propio, y Base de datos SQL de Azure no admite combinaciones entre bases de datos (que tienen que realizarse en el cliente). Recuerde también que en la Base de datos SQL de Azure, las restricciones de integridad referencial, los desencadenadores y los procedimientos almacenados de una base de datos no pueden hacer referencia a objetos de otra. Por tanto, no diseñe un sistema que tenga dependencias entre las particiones. No obstante, una Base de datos SQL sí puede contener tablas que conservan copias de datos de referencia utilizados con frecuencia por consultas y otras operaciones. No es necesario que estas tablas pertenezcan a un shardlet específico. Si replica estos datos entre particiones, la combinación de datos que se encuentren en varias bases de datos puede dejar de ser necesaria. Lo ideal es que dichos datos sean estáticos o lentos para minimizar el esfuerzo de replicación y reducir las posibilidades de que se vuelvan obsoletos.

	> [AZURE.NOTE] Aunque Base de datos SQL no admite combinaciones entre bases de datos, puede realizar consultas entre particiones con la API de Base de datos elástica. Estas consultas pueden recorrer de forma transparente los datos contenidos en todos los shardlets a los que se hace referencia en un mapa de particiones. La API de Base de datos elástica divide las consultas efectuadas entre particiones en una serie de consultas individuales (una para cada base de datos) y luego combina los resultados. Para más información, consulte la página [Consultas a través de particiones múltiples] en el sitio web de Microsoft.

- Los datos almacenados en shardlets que pertenecen al mismo mapa de partición deben tener el mismo esquema. Por ejemplo, no cree un mapa de particiones de lista que esté orientado a algunos shardlet que contengan datos del inquilino y otros shardlets que contengan información del producto. Esta regla no se aplica mediante Base de datos elástica, pero la administración y la consulta de datos se vuelven muy complejas si cada shardlet tiene un esquema diferente. En el ejemplo anteriormente citado, es interesante crear dos mapas de particiones de lista: uno que haga referencia a datos de inquilinos y otro que señale a la información del producto. Recuerde que los datos que pertenecen a diferentes shardlets pueden almacenarse en la misma partición.

	> [AZURE.NOTE] La funcionalidad de consulta entre particiones de la API de Base de datos elástica depende de cada shardlet en el mapa de particiones que contiene el mismo esquema.

- Las operaciones transaccionales solamente se admiten para datos contenidos dentro de la misma partición y no entre las particiones. Las transacciones pueden abarcar shardlets siempre que formen parte de la misma partición. Por lo tanto, si la lógica de negocios debe realizar transacciones, almacene los datos afectados en la misma partición o implemente la coherencia eventual. Para más información, consulte [Data consistency primer] (Información básica sobre la coherencia de datos).
- Coloque las particiones cerca de los usuarios que tienen acceso a los datos de esas particiones (en otras palabras, ubique geográficamente las particiones). Esta estrategia ayuda a reducir la latencia.
- Evite tener una mezcla de particiones muy activas (zonas activas) y relativamente inactivas. Pruebe a distribuir la carga uniformemente entre las particiones. Esto puede requerir la aplicación de algoritmos hash a las claves de shardlet.
- Si está geolocalizando las particiones, asegúrese de que las claves hash se asignan a shardlets de particiones almacenadas cerca de los usuarios que tienen acceso a esos datos.
- Actualmente, solo se admite un conjunto limitado de tipos de datos SQL como claves de shardlet; _int, bigint, varbinary_ y _uniqueidentifier_. Los tipos _int_ y _bigint_ de SQL se corresponden a los tipos de datos _int_ y _long_ en C#, y tienen los mismos intervalos. El tipo _varbinary_ de SQL puede controlarse mediante el uso de una matriz _Byte_ en C# y el tipo _uniqueidentier_ de SQL se corresponde con la clase _Guid_ de .NET Framework.

Como su nombre implica, Base de datos elástica permite que un sistema agregue y quite particiones a medida que el volumen de datos disminuye y crece. Las API de la biblioteca de cliente de Base de datos elástica de Base de datos SQL de Azure permiten a una aplicación crear y eliminar particiones de forma dinámica (y actualizar el administrador del mapa de la particiones de forma transparente); sin embargo, eliminar una partición es una operación destructiva que también requiere la eliminación de todos los datos de esa partición.

Si una aplicación necesita dividir una partición en dos particiones independientes o combinar particiones, Base de datos elástica proporciona un servicio independiente de división y combinación. Este servicio se ejecuta en un servicio hospedado en la nube (que debe crearse el desarrollador) y migra los datos de forma segura entre las particiones. Para más información, consulte el tema [Escalado con la herramienta de división y combinación de Base de datos elástica] en el sitio web de Microsoft.

## Estrategias de creación de particiones para el almacenamiento de Azure

Almacenamiento de Azure proporciona tres abstracciones para administrar los datos:

- Almacenamiento de tablas, que implementa el almacenamiento escalable de estructuras. Una tabla contiene una colección de entidades, cada una de los cuales puede incluir un conjunto de propiedades y valores.
- Almacenamiento de blobs, que proporciona almacenamiento para objetos y archivos grandes.
- Colas de almacenamiento, que admiten la mensajería asincrónica confiable entre aplicaciones.

Almacenamiento de tablas y Almacenamiento de blobs son esencialmente almacenes de clave-valor optimizados para almacenar datos estructurados y no estructurados, respectivamente. Las Colas de almacenamiento proporcionan un mecanismo para crear aplicaciones de acoplamiento flexible y escalables. Almacenamiento de tablas, Almacenamiento de blobs y Colas de almacenamiento se crean dentro del contexto de una cuenta de Almacenamiento de Azure. Las cuentas de Almacenamiento de Azure admiten tres formas de redundancia:

- **Almacenamiento con redundancia local**, que mantiene tres copias de datos dentro de un único centro de datos. Esta forma de redundancia protege contra fallos de hardware, pero no frente a un desastre que abarca todo el centro de datos.
- **Almacenamiento con redundancia de zona**, que mantiene tres copias de los datos repartidas entre diferentes centros de datos de la misma región (o entre dos regiones cercanas geográficamente). Esta forma de redundancia puede proteger frente a desastres que se producen dentro de un único centro de datos, pero no puede proteger frente a las desconexiones de red a gran escala que afectan a toda una región. Tenga en cuenta que el almacenamiento con redundancia de zona solo está disponible actualmente para blobs en bloques.
- **Almacenamiento con redundancia geográfica**, que mantiene seis copias de datos: tres copias en una región (su región local) y otras tres copias en una región remota. Esta forma de redundancia proporciona el máximo nivel de protección ante desastres.

Microsoft ha publicado los objetivos de escalabilidad para el Almacenamiento de Azure. Para más información, consulte la página [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure] en el sitio web de Microsoft. Actualmente, la capacidad total de la cuenta de almacenamiento no puede superar los 500 TB (esto incluye el tamaño de los datos que se mantienen en el Almacenamiento de tablas y el Almacenamiento de blobs, así como los mensajes pendientes que se encuentran en la cola de almacenamiento).

La tasa de solicitud máxima (suponiendo un tamaño de 1 KB por entidad, blob o mensaje) es de 20 KB por segundo. Si es probable que su sistema supere estos límites, considere la posibilidad de crear particiones en la carga entre varias cuentas de almacenamiento. Una sola suscripción de Azure puede crear hasta 100 cuentas de almacenamiento. Sin embargo, tenga en cuenta que estos límites pueden cambiar con el tiempo.

## Creación de particiones en el almacenamiento de tablas de Azure

El Almacenamiento de tablas de Azure es un almacén del par clave-valor diseñado en torno a la creación de particiones. Todas las entidades se almacenan en una partición y las particiones son administradas internamente por el almacenamiento de tablas de Azure. Cada entidad que se almacena en una tabla debe proporcionar una clave de dos partes que incluye lo siguiente:

- **La clave de la partición**. Se trata de un valor de cadena que determina en qué partición el Almacenamiento de tablas de Azure colocará la entidad. Todas las entidades con la misma clave de partición se almacenarán en la misma partición.
- **La clave de la fila**. Se trata de otro valor de cadena que identifica la entidad dentro de la partición. Todas las entidades dentro de una partición se ordenan léxicamente, en orden ascendente, por parte de esta clave. La combinación de clave de fila/partición debe ser única para cada entidad y no puede superar 1 KB de longitud.

El resto de los datos de una entidad se compone de campos definidos por la aplicación. No se aplica ningún esquema concreto, y cada fila puede contener un conjunto diferente de campos definidos por la aplicación. La única limitación es que el tamaño máximo de una entidad (incluidas las claves de partición y fila) actualmente es de 1 MB. El tamaño máximo de una tabla es de 200 TB, aunque estas cifras podrían cambiar en el futuro (consulte la página [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure] en el sitio web de Microsoft para conocer la información más reciente sobre estos límites).

Si intenta almacenar entidades que superen esta capacidad, considere la posibilidad de dividirlas en varias tablas. Utilice particiones verticales para dividir los campos en los grupos a los que se vaya a acceder en conjunto con mayor probabilidad.

En la ilustración 7 se muestra la estructura lógica de una cuenta de almacenamiento de ejemplo (Contoso Data) para una aplicación de comercio electrónico ficticia. La cuenta de almacenamiento contiene tres tablas: información del cliente, información del producto e información del pedido. Cada tabla tiene varias particiones.

En la tabla de información del cliente, se particionan los datos según la ciudad en la que está ubicado el cliente y la clave de fila contiene el identificador de cliente. En la tabla de información del producto, se particionan los productos por categoría de producto y la clave de fila contiene el número de producto. En la tabla de información del pedido, se particionan los pedidos por la fecha en la que se efectuaron y la clave de fila especifica el momento en el que se ha recibido el pedido. Tenga en cuenta que todos los datos se ordenan por la clave de fila de cada partición.

![Las tablas y las particiones de una cuenta de almacenamiento de ejemplo](media/best-practices-data-partitioning/TableStorage.png)

_Ilustración 7. Las tablas y las particiones de una cuenta de almacenamiento de ejemplo_

> [AZURE.NOTE] Almacenamiento de tablas de Azure también agrega un campo de marca de tiempo a cada entidad. El campo de marca de tiempo es mantenido por el almacenamiento de tablas y se actualiza cada vez que la entidad se modifica y se escribe de nuevo en una partición. El servicio Almacenamiento de tablas utiliza este campo para implementar simultaneidad optimista (cada vez que una aplicación vuelve a escribir una entidad en el almacenamiento de tablas, este servicio compara el valor de la marca de tiempo de la entidad que se está escribiendo con el valor conservado en el almacenamiento de tablas). Si los valores son distintos, otra aplicación debe haber modificado la entidad desde que se recuperó por última vez y se produce un error en la operación de escritura. No modifique este campo en su propio código y no especifique un valor para este campo cuando se cree una nueva entidad.

Almacenamiento de tablas de Azure usa la clave de partición para determinar cómo almacenar los datos. Si se agrega una entidad a una tabla con una clave de partición no usada anteriormente, Almacenamiento de tablas de Azure crea una nueva partición para esta entidad. Otras entidades con la misma clave de partición se almacenarán en la misma partición.

Este mecanismo implementa de forma efectiva una estrategia de escalado automático. Cada partición se almacena en un servidor único en un centro de datos de Azure para ayudar a garantizar que las consultas que recuperan datos de una sola partición se ejecutan rápidamente. Sin embargo, las distintas particiones pueden distribuirse entre varios servidores. Además, un único servidor puede alojar varias particiones si estas particiones tienen un tamaño limitado.

Considere los siguientes puntos cuando diseñe las entidades para el almacenamiento de tablas de Azure:

- La selección de valores de claves de fila y de partición debe basarse en la forma en que la que se accede a los datos. Elija una combinación de claves de partición y fila que admita la mayoría de las consultas. Las consultas más eficaces recuperan los datos especificando la clave de partición y la clave de fila. Las consultas que especifican una clave de partición y un intervalo de claves de fila pueden completarse mediante el análisis de una sola partición. Esto es relativamente rápido porque los datos se mantienen en el orden de la clave de fila. Si las consultas no especifican qué partición se debe analizar, la clave de partición puede requerir que Almacenamiento de tablas de Azure busque los datos en todas las particiones.

	> [AZURE.TIP] Si una entidad tiene una clave natural, a continuación, úsela como clave de partición y especifique una cadena vacía como clave de fila. Si una entidad tiene una clave compuesta que incluye dos propiedades, seleccione la propiedad que cambie más despacio como clave de partición y la otra como clave de fila. Si una entidad tiene más de dos propiedades de clave, use una concatenación de propiedades para proporcionar las claves de partición y fila.

- Si realiza regularmente consultas que buscan datos con campos que no sean las claves de partición y fila, considere la posibilidad de implementar el [patrón de tabla de índice].
- Si se generan claves de partición mediante una secuencia monotónica creciente o decreciente (por ejemplo, "0001", "0002", "0003",...) y cada partición contiene solo una cantidad limitada de datos, es posible que el almacenamiento de tablas de Azure agrupe físicamente estas particiones en el mismo servidor. Este mecanismo supone que es más probable que la aplicación realice consultas en un intervalo contiguo de particiones (consultas por rango) y está optimizada para este caso. Sin embargo, este enfoque puede provocar la aparición de puntos con mucho tráfico centrados en un solo servidor, ya que es probable que todas las inserciones de nuevas entidades se concentren en uno u otro extremo de los intervalos contiguos. También puede reducir la escalabilidad. Para distribuir la carga más uniformemente entre servidores, considere la posibilidad de aplicar un algoritmo hash a la clave de partición para que la secuencia sea más aleatoria.
- El almacenamiento de tablas de Azure admite operaciones transaccionales para entidades que pertenecen a la misma partición. Esto significa que una aplicación puede realizar varias operaciones de inserción, actualización, eliminación, sustitución o combinación como una unidad atómica (siempre que la transacción no incluya más de 100 entidades y que la carga de la solicitud no supere los 4 MB de tamaño). Las operaciones que abarcan varias particiones no son transaccionales y pueden requerir la implementación de la coherencia eventual, tal y como se describe en [Data consistency primer] (Información básica sobre la coherencia de datos). Para más información sobre el almacenamiento y las transacciones de tablas, visite la página [Realizar transacciones con grupos de entidades] en el sitio web de Microsoft.
- Preste mucha atención a la granularidad de la clave de partición por los siguientes motivos:
	- El uso de la misma clave de partición para todas las entidades hace que el servicio Almacenamiento de tablas cree una sola partición de gran tamaño que se conserva en un servidor. Esto impide el escalado horizontal y centra la carga en un único servidor. Como resultado, este enfoque solo es adecuado para sistemas que administran un pequeño número de entidades. Sin embargo, este enfoque garantiza que todas las entidades puedan participar en transacciones de grupo de entidades.
	- El uso de una clave de partición única para cada entidad provoca que el servicio Almacenamiento de tablas cree una partición independiente para cada entidad, lo cual puede dar lugar a un número elevado de particiones pequeñas (dependiendo del tamaño de las entidades). Este enfoque es más escalable que el uso de una clave de partición única, pero no es posible realizar transacciones de grupo de entidad. Además, las consultas que capturan más de una entidad podrían implicar lecturas desde más de un servidor. Sin embargo, si la aplicación realiza consultas por rango, el uso de una secuencia monotónica para generar las claves de partición puede ayudarle a optimizar estas consultas.
	- Compartir la clave de partición en un subconjunto de entidades permite agrupar entidades relacionadas en la misma partición. Se pueden realizar operaciones que implican entidades relacionadas con transacciones de grupo de entidades, y es posible dar respuesta a las consultas que capturan un conjunto de entidades relacionadas mediante el acceso a un único servidor.

Para obtener información adicional sobre la creación de particiones de datos en Almacenamiento de tablas de Azure, consulte el artículo [Guía de diseño de la tabla de almacenamiento de Azure: diseño escalable y tablas de rendimiento] en el sitio web de Microsoft.

## Creación de particiones en el almacenamiento de blobs de Azure

El Almacenamiento de blobs de Azure permite almacenar objetos binarios grandes: actualmente hasta 200 GB de tamaño para los blobs en bloques o 1 TB para los blobs en páginas (para obtener la información más actualizada, vaya a la página [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure] en el sitio web de Microsoft). Use blobs en bloques en escenarios como los de transmisión por secuencias donde sea necesario cargar o descargar grandes volúmenes de datos rápidamente. Use blobs en páginas para aplicaciones que requieran acceso aleatorio en lugar de acceso de serie a partes de los datos.

Cada blob (en bloque o en página) se mantiene en un contenedor de una cuenta de almacenamiento de Azure. Puede usar contenedores para agrupar blobs relacionados que tengan los mismos requisitos de seguridad, aunque esta agrupación es más lógica que física. Dentro de un contenedor, cada blob tiene un nombre único.

El almacenamiento de blobs se divide automáticamente en función del nombre de blob. Cada blob se mantiene en su propia partición. Los blobs del mismo contenedor no comparten una partición. Esta arquitectura ayuda al almacenamiento de blobs de Azure a equilibrar la carga entre servidores de forma transparente, ya que blobs diferentes dentro del mismo contenedor pueden distribuirse en servidores diferentes.

Las acciones de escritura de un solo bloque (blob en bloques) o página (blob en páginas) son atómicas, pero no las operaciones que abarcan bloques, páginas o blobs. Si necesita asegurar la coherencia cuando se realizan operaciones de escritura en bloques, páginas y blobs, realice un bloqueo de escritura mediante el uso de una concesión de blob.

Almacenamiento de blobs de Azure admite velocidades de transferencia de hasta 60 MB por segundo o hasta 500 solicitudes por segundo para cada blob. Si prevé que va a superar estos límites y los datos de blob están relativamente estáticos, considere la posibilidad de replicar blobs mediante el uso de la red de entrega de contenido (CDN) de Azure. Para obtener más información, consulte la página [Using Content Delivery Network for Azure] (Uso de red de entrega de contenido para Azure) en el sitio web de Microsoft. Para obtener instrucciones y otras consideraciones, consulte el artículo [Uso de la red CDN en Azure].

## Creación de particiones en colas de almacenamiento de Azure

Las colas de almacenamiento de Azure le permiten implementar mensajería asincrónica entre procesos. Una cuenta de almacenamiento de Azure puede contener cualquier número de colas y cada una de ellas puede contener cualquier número de mensajes. La única limitación es el espacio disponible en la cuenta de almacenamiento. El tamaño máximo de un mensaje individual es de 64 KB. Si necesita mensajes de mayor tamaño, considere la posibilidad de usar Colas del Bus de servicio en su lugar.

Cada cola de almacenamiento tiene un nombre único dentro de la cuenta de almacenamiento en la que se encuentra. Azure crea colas de particiones basadas en el nombre. Todos los mensajes de la misma cola se almacenan en la misma partición, que se controla mediante un solo servidor. Distintas colas pueden ser administradas por distintos servidores para ayudar a equilibrar la carga. La asignación de colas a servidores es transparente para las aplicaciones y usuarios.

 En una aplicación a gran escala, no utilice la misma cola de almacenamiento para todas las instancias de la aplicación porque esto puede provocar que el servidor que hospeda la cola soporte una carga excesiva. En su lugar, use colas diferentes para distintas áreas funcionales de la aplicación. Las colas de almacenamiento de Azure no admiten transacciones, por lo que dirigir los mensajes a distintas colas debería tener poco impacto en la coherencia de los mensajes.

Una cola de almacenamiento de Azure puede controlar hasta 2000 mensajes por segundo. Si necesita procesar los mensajes a una velocidad mayor, puede crear varias colas. Por ejemplo, en una aplicación global, cree colas de almacenamiento independientes en cuentas de almacenamiento separadas para controlar las instancias de aplicación que se ejecutan en cada región.

## Estrategias de creación de particiones para el Bus de servicio de Azure

El Bus de servicio de Azure usa un agente de mensajes para controlar los mensajes enviados a una cola o un tema del Bus de servicio. De forma predeterminada, todos los mensajes enviados a una cola o tema se controlan mediante el mismo proceso de agente de mensaje. Esta arquitectura puede colocar una limitación en el rendimiento general de la cola de mensajes. Sin embargo, también puede dividir una cola o tema cuando se crea. Para ello, establezca la propiedad _EnablePartitioning_ de la descripción de la cola o el tema en _true_.

Una cola o tema con particiones se divide en varios fragmentos, cada uno de ellos respaldado por un almacén de mensajes y un agente de mensajes independientes. Bus de servicio asume la responsabilidad de crear y administrar estos fragmentos. Cuando una aplicación envía un mensaje a una cola o tema particionado, Bus de servicio asigna el mensaje a un fragmento de esa cola o tema. Cuando una aplicación recibe un mensaje de una cola o suscripción, Bus de servicio comprueba cada fragmento para detectar el siguiente mensaje disponible y, a continuación, lo pasa a la aplicación para su procesamiento.

Esta estructura le ayuda a distribuir la carga entre los agentes de mensajes y los almacenes de mensajes, aumentando la escalabilidad y mejorando la disponibilidad. Si el almacén de mensajes o el agente de mensajes de un fragmento no está disponible temporalmente, el Bus de servicio puede recuperar mensajes de uno de los demás fragmentos disponibles.

Bus de servicio asigna un mensaje a un fragmento de la siguiente manera:

- Si el mensaje pertenece a una sesión, se envían todos los mensajes con el mismo valor para la propiedad \_ SessionId\_ al mismo fragmento.
- Si el mensaje no pertenece a una sesión pero el remitente ha especificado un valor para la propiedad _PartitionKey_, todos los mensajes con el mismo valor _PartitionKey_ se enviarán al mismo fragmento.

	> [AZURE.NOTE] Si se especifican las propiedades _SessionId_ y _PartitionKey_, se deben establecer en el mismo valor; de lo contrario, se rechazará el mensaje.
- Si las propiedades _SessionId_ y _PartitionKey_ de un mensaje no se han especificado pero se habilita la detección de duplicados, se usará la propiedad _MessageId_. Todos los mensajes con el mismo _MessageId_ se dirigirán al mismo fragmento.
- Si los mensajes no incluyen una propiedad _SessionId, PartitionKey_ o _MessageId_, el Bus de servicio asigna los mensajes a los fragmentos de manera secuencial. Si un fragmento no está disponible, Bus de servicio pasará al siguiente. Esto significa que un error temporal de la infraestructura de mensajería no provoca un error en la operación de envío de mensajes.

Debe tener en cuenta los siguientes puntos cuando decida si crear una partición en un tema o una cola de mensajes de Bus de servicio o un tema, y cómo realizarla:

- Los temas y colas de Bus de servicio se crean dentro del ámbito de un espacio de nombres de Bus de servicio. Actualmente Bus de servicio permite hasta 100 colas o temas particionados por espacio de nombres.
- Cada espacio de nombres del Bus de servicio impone cuotas en los recursos disponibles, como el número de suscripciones por tema, el número de solicitudes de envío y recepción simultáneas por segundo y el número máximo de conexiones simultáneas que pueden establecerse. Estas cuotas se documentan en el sitio web de Microsoft en la página [Cuotas de Bus de servicio]. Si espera que superar estos valores, cree espacios de nombres adicionales con sus propios temas y colas y distribuya el trabajo en estos espacios de nombres. Por ejemplo, en una aplicación global, cree espacios de nombres independientes en cada región y configure instancias de la aplicación para usar las colas y temas en el espacio de nombres más cercano.
- Los mensajes que se envían como parte de una transacción deben especificar una clave de partición. Esta puede ser una propiedad _SessionId_, _PartitionKey_ o _MessageId_. Todos los mensajes que se envían como parte de la misma transacción deben especificar la misma clave de partición porque deben tratarse mediante el mismo proceso de agente de mensaje. No se pueden enviar mensajes a diferentes colas o temas de dentro de la misma transacción.
- Temas y colas con particiones no pueden configurarse para eliminarse automáticamente cuando se queden inactivas.
- Si está creando soluciones multiplataforma o híbridas, en este momento no podrá utilizar colas o temas con particiones con el protocolo Advanced Message Queuing Protocol (AMQP).

## Estrategias de creación de particiones para bases de datos de Azure DocumentDB

Azure DocumentDB es una base de datos NoSQL que puede almacenar documentos. Un documento en una base de datos de DocumentDB es una representación serializada de un objeto o de otro elemento de datos. No se aplican esquemas fijos, excepto que cada documento debe contener un identificador único.

Los documentos se organizan en colecciones. Puede agrupar documentos relacionados entre sí en una colección. Por ejemplo, en un sistema que mantiene las entradas de un blog, puede almacenar el contenido de cada entrada como un documento de una colección. También puede crear colecciones para cada tipo de asunto. O bien, en una aplicación para varios inquilinos, como un sistema donde diferentes autores controlan y administran sus propias publicaciones de blog, podría crear particiones en los blogs por autor y crear una colección independiente para cada autor. El espacio de almacenamiento asignado a las colecciones es elástico y puede reducirse o ampliarse según sea necesario.

Las colecciones de documentos proporcionan un mecanismo natural para dividir los datos de una base de datos única. Internamente, una base de datos DocumentDB puede abarcar varios servidores e intentar distribuir la carga al distribuir colecciones entre servidores. La manera más sencilla de implementar las particiones es crear una colección para cada partición.

> [AZURE.NOTE] Cada base de datos de DocumentDB tiene un _nivel de rendimiento_ que determina la cantidad de recursos que obtiene. Los niveles de rendimiento están asociados a un límite de velocidad de _unidad de solicitud_ (RU). El límite de velocidad de unidad de solicitud especifica el volumen de los recursos reservados y disponibles para el uso exclusivo de esa colección. El costo de una colección depende del nivel de rendimiento seleccionado para esa colección. Cuanto mayor sea el nivel re rendimiento (y el límite de velocidad de la unidad de solicitud), mayor será el costo. Puede ajustar el nivel de rendimiento de una colección mediante el Portal de Azure. Para obtener más información, consulte la página [Niveles de rendimiento en DocumentDB] en el sitio web de Microsoft.

Todas las bases de datos se crean en el contexto de una cuenta de DocumentDB. Una sola cuenta de DocumentDB puede contener varias bases de datos, y especifica en qué región se crean las bases de datos. Cada cuenta DocumentDB también impone su propio control de acceso. Puede utilizar cuentas de DocumentDB para localizar geográficamente particiones (colecciones dentro de bases de datos) cerca de los usuarios que necesitan tener acceso a ellas y aplicar restricciones de modo que solo esos usuarios puedan conectarse.

Cada cuenta DocumentDB tiene una cuota que limita el número de bases de datos, las colecciones que puede contener y la cantidad de almacenamiento de documentos disponible. Estos límites están sujetos a cambios, pero se describen en la página [Cuotas y límites de DocumentDB] en el sitio web de Microsoft. Teóricamente, es posible que si se implementa un sistema donde todas las particiones pertenecen a la misma base de datos, se podría alcanzar el límite de capacidad de almacenamiento de información de la cuenta.

En este caso, puede que necesite crear bases de datos y cuentas de DocumentDB adicionales y distribuir las particiones en estas bases de datos. Sin embargo, aunque es poco probable que alcance la capacidad de almacenamiento de una base de datos, es recomendable utilizar varias bases de datos. El motivo es que cada base de datos tiene su propio conjunto de usuarios y permisos, y puede utilizar este mecanismo para aislar el acceso a las colecciones en función de cada una de las bases de datos.

La figura 8 muestra la estructura de alto nivel de la arquitectura DocumentDB.

![La estructura de DocumentDB](media/best-practices-data-partitioning/DocumentDBStructure.png)

_Ilustración 8. La estructura de la arquitectura de DocumentDB_

Es responsabilidad de la aplicación cliente dirigir las solicitudes a la partición apropiada, normalmente mediante la implementación de su propio mecanismo de asignación en función de algunos atributos de los datos que definen la clave de partición. La ilustración 9 muestra dos bases de datos DocumentDB, cada una con dos colecciones que actúan como particiones. Los datos se particionan por identificador de inquilino y contienen los datos de un inquilino específico. Las bases de datos se crean en cuentas de DocumentDB independientes. Estas cuentas se encuentran en la misma región que los inquilinos para los que contienen los datos. La lógica de enrutamiento de la aplicación cliente usa el Id. de inquilino como la clave de partición.

![Implementación de particionamiento con Azure DocumentDB](media/best-practices-data-partitioning/DocumentDBPartitions.png)

_Ilustración 9. Implementación del particionamiento mediante una base de datos de Azure DocumentDB_

A la hora de decidir cómo particionar los datos con una base de datos de DocumentDB, tenga en cuenta los siguientes puntos:

- **Los recursos disponibles para una base de datos de DocumentDB están sujetos a las limitaciones de cuota de la cuenta de DocumentDB**. Cada base de datos puede contener un número de colecciones (de nuevo, hay un límite) y cada colección está asociada a un nivel de rendimiento que rige el límite de velocidad de RU (rendimiento reservado) para esa colección. Para más información, visite la página [Límites y cuotas de DocumentDB] en el sitio web de Microsoft.
- **Cada documento debe tener un atributo que pueda usarse para identificar de manera única dicho documento dentro de la colección en la que se encuentra**. Este atributo es diferente de la clave de partición, que define en qué colección se encuentra el documento. Una colección puede contener un gran número de documentos. En teoría, solo está limitada por la longitud máxima del identificador del documento. El identificador del documento puede contener hasta 255 caracteres.
- **Todas las operaciones de un documento se realizan en el contexto de una transacción. Las transacciones en las bases de datos de DocumentDB se limitan a la colección que contiene el documento.** Si se produce un error en una operación, se revierte el trabajo que ha realizado. Mientras se realiza una operación sobre un documento, los cambios realizados están sujetos a aislamiento a nivel de instantánea. Este mecanismo garantiza que si, por ejemplo, se produce un error en una solicitud para crear un nuevo documento, otro usuario que consulte la base de datos al mismo tiempo no verá un documento parcial que luego se elimine.
- **Las consultas de base de datos de DocumentDB también se limitan al nivel de colección**. Una sola consulta solo puede recuperar datos de una colección. Si necesita recuperar datos de varias colecciones, debe consultar cada colección individualmente y combinar los resultados en el código de aplicación.
- **Las bases de datos de DocumentDB admiten elementos programables que pueden almacenarse en una colección junto con los documentos**. Estos incluyen procedimientos almacenados, funciones definidas por el usuario y desencadenadores (escritos en JavaScript). Estos elementos pueden tener acceso a cualquier documento en la misma colección. Además, estos elementos se ejecutan dentro del ámbito de la transacción de ambiente (en el caso de un desencadenador que se activa como resultado de una operación de crear, eliminar o reemplazar realizada en un documento), o iniciando una nueva transacción (en el caso de un procedimiento almacenado que se ejecuta como resultado de una solicitud de cliente explícita). Si el código de un elemento programable produce una excepción, la transacción se revierte. Puede usar procedimientos almacenados y desencadenadores para mantener la integridad y la coherencia entre los documentos, pero estos documentos deben formar parte de la misma colección.
- **Debe procurar que las colecciones que desea almacenar en las bases de datos de una cuenta de DocumentDB no superen los límites de rendimiento definidos por los niveles de rendimiento de las colecciones**. Estos límites se describen en la página [Más información sobre almacenamiento de documentos y capacidad en DocumentDB] en el sitio web de Microsoft. Si prevé alcanzar estos límites, considere la posibilidad de dividir las colecciones entre bases de datos en diferentes cuentas de DocumentDB para reducir la carga de cada colección.

## Estrategias de creación de particiones para Búsqueda de Azure

La función de búsqueda de datos suele ser el método principal de navegación y exploración proporcionado por muchas aplicaciones web. Permite a los usuarios encontrar recursos rápidamente (por ejemplo, los productos de una aplicación de comercio electrónico) según determinadas combinaciones de criterios de búsqueda. El servicio de Búsqueda de Azure proporciona capacidades de búsqueda de texto completo a través de contenido web e incluye características como las consultas sugeridas de escritura anticipada basadas en coincidencias cercanas y en la navegación por facetas. Se puede encontrar una descripción completa de estas funcionalidades en la página [¿Qué es Búsqueda de Azure?] en el sitio web de Microsoft.

Búsqueda de Azure almacena contenido en el que es posible realizar búsquedas como documentos JSON en una base de datos. El usuario es quien define los índices que especifican los campos de búsqueda de estos documentos y proporciona estas definiciones al servicio Búsqueda de Azure. Cuando se emite una solicitud de búsqueda, Búsqueda de Azure usa los índices adecuados para buscar los elementos coincidentes.

Para reducir la contención, es posible dividir el almacenamiento utilizado por Búsqueda de Azure en 1, 2, 3, 4, 6 o 12 particiones, y cada partición se puede replicar hasta 6 veces. El producto del número de particiones multiplicado por el número de réplicas se denomina _unidad de búsqueda_ (SU). Una única instancia de Búsqueda de Azure puede contener un máximo de 36 unidades de búsqueda (una base de datos con 12 particiones solo admite un máximo de 3 réplicas).

Se le facturará cada SU que se asigne a su servicio. A medida que crezca el volumen de contenido sobre el que es posible realizar búsquedas o la tasa de solicitudes de búsqueda, puede agregar unidades de búsqueda a una instancia existente de Búsqueda de Azure para administrar la carga adicional. El propio servicio Búsqueda de Azure distribuye los documentos uniformemente entre las particiones. Actualmente no se admite la aplicación de ninguna estrategia de partición manual.

Cada partición puede contener un máximo de 15 millones de documentos u ocupar 300 GB de espacio de almacenamiento (lo que sea menor). Puede crear hasta 50 índices. El rendimiento del servicio varía y depende de la complejidad de los documentos, los índices disponibles y los efectos de la latencia de la red. De media, una única réplica (1 unidad de búsqueda) debe ser capaz de administrar 15 consultas por segundo, aunque es recomendable realizar pruebas comparativas realizadas con sus propios datos para obtener una medida más precisa del rendimiento. Para más información, consulte la página [Límites de servicio en la Búsqueda de Azure] en el sitio web de Microsoft.

> [AZURE.NOTE] Puede almacenar un conjunto limitado de tipos de datos en documentos que se pueden buscar, incluidas cadenas, valores booleanos, datos numéricos, datos de fecha y hora y algunos datos geográficos. Para más información, consulte la página [Tipos de datos admitidos (Búsqueda de Azure)] en el sitio web de Microsoft.

Tiene control limitado sobre cómo divide en particiones el servicio de Búsqueda de Azure los datos de cada instancia del servicio. Sin embargo, en un entorno global puede mejorar el rendimiento y reducir la latencia y la contención aún más mediante la partición del propio servicio; para ello, aplique alguna de las estrategias siguientes:

- Cree una instancia de Búsqueda de Azure en cada región geográfica y asegúrese de que las aplicaciones cliente se dirigen hacia la instancia disponible más cercana. Esta estrategia requiere la replicación de las actualizaciones de contenido de búsqueda de manera oportuna en todas las instancias del servicio.

- Cree dos niveles en Búsqueda de Azure:
    - Un servicio local en cada región que contenga los datos a los que se accede con mayor frecuencia por parte de los usuarios de esa región. Los usuarios podrá dirigir solicitudes aquí para obtener resultados rápidos pero limitados.
    - Un servicio global que abarque todos los datos. Los usuarios podrán dirigir solicitudes aquí para obtener resultados más lentos pero más completos.

Este enfoque es más adecuado cuando existe una variación regional considerable en los datos que se están buscando.

## Estrategias de creación de particiones para Caché en Redis de Azure

Caché en Redis de Azure proporciona un servicio de almacenamiento en caché compartido en la nube que se basa en el almacén de datos de clave-valor de Redis. Como su nombre implica, el servicio Caché en Redis de Azure está concebido como una solución de almacenamiento en caché. Úselo solo para almacenar datos provisionales y no como un almacén de datos permanentes. Las aplicaciones que usan Caché en Redis de Azure podrán seguir funcionando si la caché no está disponible. Caché en Redis de Azure admite la replicación principal y secundaria para proporcionar alta disponibilidad, pero actualmente limita el tamaño de caché máximo a 53 GB. Si necesita más espacio que este, deberá crear cachés adicionales. Para más información, visite la página [Caché en Redis de Azure] en el sitio web de Microsoft.

Crear particiones en un almacén de datos Redis implica dividir los datos entre instancias del servicio Redis. Cada instancia constituye una sola partición. Caché en Redis de Azure abstrae los servicios de Redis detrás de una fachada y no los expone directamente. La manera más sencilla de implementar la creación de particiones es crear varias instancias de Caché en Redis de Azure y repartir los datos entre ellas.

Puede asociar cada uno de los elementos de datos con un identificador (una clave de partición) que especifica en qué caché se almacenan. La lógica de la aplicación cliente puede usar luego este identificador para enrutar las solicitudes a la partición apropiada. Este esquema es muy sencillo, pero si cambia el esquema de creación de particiones (por ejemplo, si se crean instancias de Caché en Redis de Azure adicionales), es posible que las aplicaciones cliente deban volver a configurarse.

Redis nativo (no Caché en Redis de Azure) admite particiones de servidor basadas en la agrupación en clústeres de Redis. En este enfoque, los datos se dividen uniformemente entre servidores mediante un mecanismo hash. Cada servidor Redis almacena metadatos que describen el intervalo de claves hash que contiene la partición, y también contiene información acerca de qué claves hash se encuentran en las particiones de otros servidores.

Las aplicaciones cliente simplemente envían solicitudes a cualquiera de los servidores Redis participantes (probablemente el más cercano). El servidor Redis examina la solicitud del cliente. Si se puede resolver localmente, este realiza la operación solicitada. De lo contrario, reenvía la solicitud al servidor apropiado.

Este modelo se implementa mediante el uso de clústeres de Redis y se describe con más detalle en la página [Tutorial de clúster Redis] en el sitio web de Redis. La agrupación en clústeres de Redis es transparente para las aplicaciones cliente. Es posible agregar servidores Redis adicionales al clúster (y es posible volver a crear particiones en los datos) sin necesidad de volver a configurar los clientes.

> [AZURE.IMPORTANT] Actualmente Caché en Redis de Azure no admite la agrupación en clústeres Redis. Si desea implementar este enfoque con Azure, deberá implementar entonces sus propios servidores Redis instalando Redis en un conjunto de máquinas virtuales de Azure y configurándolas manualmente. La página [Running Redis on a CentOS Linux VM in Azure] (Ejecución de Redis en una máquina virtual Linux de CentOS en Azure) del sitio web de Microsoft muestra un ejemplo de cómo crear y configurar un nodo Redis que se ejecuta como una máquina virtual de Azure.

La página [Partitioning: how to split data among multiple Redis instances] (Creación de particiones: cómo dividir los datos entre varias instancias de Redis) del sitio web de Redis ofrece más información sobre cómo implementar la creación de particiones con Redis. En el resto de esta sección se supone que se está implementando las particiones del lado cliente o asistidas por el proxy.

Deberá tener en cuenta los siguientes puntos a la hora de decidir cómo crear particiones en los datos con la Caché en Redis de Azure:

- El servicio Caché en Redis de Azure no está pensado para actuar como un almacén de datos permanentes, por lo que, independientemente del esquema de particiones que implemente, el código de la aplicación debe estar preparado para recuperar datos desde una ubicación distinta de la caché.
- Los datos a los que se suele acceder en conjunto deben mantenerse en la misma partición. Redis es un eficaz almacén de clave-valor que proporciona varios mecanismos enormemente optimizados para estructurar los datos. Estos mecanismos pueden ser uno de los siguientes elementos:
    - Cadenas simples (datos binarios de hasta 512 MB)
    - Tipos de agregado como listas (que pueden actuar como colas y pilas)
    - Conjuntos (ordenados y desordenados)
    - Algoritmos hash (que pueden agrupar campos relacionados, como los elementos que representan los campos de un objeto)

- Los tipos de agregado permiten asociar muchos valores relacionados con la misma clave. Una clave de Redis identifica una lista, un conjunto o un valor hash en lugar de los elementos de datos que contiene. Estos tipos están disponibles con Caché en Redis de Azure y se describen en la página [Data types] (Tipos de datos) del sitio web de Redis. Por ejemplo, en la parte de un sistema de comercio electrónico que realiza el seguimiento de los pedidos realizados por los clientes, los detalles de cada cliente podrían almacenarse en un hash de Redis con clave utilizando el identificador de cliente. Cada valor hash puede contener una colección de identificadores de pedido para el cliente. Un conjunto Redis independiente podría contener los pedidos, estructurados como algoritmos hash y codificados por medio del identificador de pedido. La figura 10 muestra esta estructura. Tenga en cuenta que Redis no implementa ningún tipo de integridad referencial, por lo que es responsabilidad del desarrollador mantener las relaciones entre clientes y pedidos.

![Estructura sugerida en el almacenamiento de Redis para registrar los pedidos de clientes y sus detalles](media/best-practices-data-partitioning/RedisCustomersandOrders.png)

_Ilustración 10. Estructura sugerida en el almacenamiento de Redis para registrar los pedidos de clientes y sus detalles_

> [AZURE.NOTE] En Redis, todas las claves son valores de datos binarios (como cadenas Redis) y pueden contener hasta 512 MB de datos. En teoría, una clave puede contener casi cualquier información. No obstante, debe adoptar una convención de nomenclatura coherente para las claves que sea descriptiva del tipo de datos y que identifique la entidad, pero que no sea demasiado larga. Es habitual utilizar claves con el formato "tipo\_entidad: ID". Por ejemplo, puede usar "cliente: 99" para indicar la clave de un cliente con el identificador 99.

- Puede implementar las particiones verticales almacenando información relacionada en agregaciones diferentes en la misma base de datos. Por ejemplo, en una aplicación de comercio electrónico podría almacenar información a la que accede frecuentemente acerca de los productos en un hash de Redis y la información detallada usada con menos frecuencia en otro. Ambos valores hash pueden utilizar el mismo identificador de producto como parte de la clave. Por ejemplo, puede utilizar "producto: _nn_" (donde _nn_ es el identificador del producto) para la información del producto y "detalles\_producto: _nn_" para los datos detallados. Esta estrategia puede ayudar a reducir el volumen de datos que es probable que recuperen la mayoría de las consultas.
- Puede volver a particionar un almacén de datos de Redis, pero tenga en cuenta que es una tarea compleja y lenta. La agrupación en clústeres de Redis puede volver a crear particiones de datos automáticamente, pero esta función no está disponible con Caché en Redis de Azure. Por lo tanto, al diseñar el esquema de partición, deje suficiente espacio libre en cada partición para permitir el crecimiento de datos que se espera con el tiempo. Sin embargo, recuerde que Caché en Redis de Azure está diseñado para almacenar datos en la caché temporalmente y que los datos que se mantienen en la memoria caché pueden tener una duración limitada especificada como valor de período de vida (TTL). En el caso de los datos relativamente volátiles, el período de vida debe ser corto; sin embargo, para datos estáticos, el período de vida puede ser mucho más largo. Evite almacenar grandes cantidades de datos de larga duración en la caché si el volumen de estos datos es probable que llene la caché. Puede especificar una directiva de expulsión que provoque que Caché en Redis de Azure elimine datos si el espacio es crítico.

	> [AZURE.NOTE] Cuando utiliza Caché en Redis de Azure, puede especificar el tamaño máximo de caché (de 250 MB a 53 GB) seleccionando el plan de tarifa adecuado. Sin embargo, una vez que se ha creado una Caché en Redis de Azure, no podrá aumentar (ni reducir) su tamaño.

- Los lotes y las transacciones de Redis no pueden abarcar varias conexiones, por lo que todos los datos afectados por un lote o transacción deben permanecer en la misma base de datos (partición).

	> [AZURE.NOTE] Una secuencia de operaciones en una transacción Redis no es necesariamente atómica. Los comandos que componen una transacción se comprueban y se ponen en cola antes de ejecutarse. Si se produce un error durante esta fase, se descarta toda la cola. Sin embargo, una vez que la transacción se haya enviado correctamente, se ejecutarán los comandos en cola en secuencia. Si se produce un error en algún comando, únicamente ese comando deja de ejecutarse. Se ejecutarán todos los comandos situados delante y detrás en la cola. Para más información, visite la página [Transactions] (Transacciones) en el sitio web de Redis.

- Redis admite un número limitado de operaciones atómicas. Las únicas operaciones de este tipo que admiten varias claves y valores son operaciones MGET y MSET. Las operaciones MGET devuelven una colección de valores para una lista especificada de claves, y las operaciones MSET almacenan una colección de valores para una lista especificada de claves. Si necesita usar estas operaciones, los pares clave-valor a los que hacen referencia los comandos MSET y MGET deben almacenarse en la misma base de datos.

## Reequilibrio de particiones

A medida que un sistema empieza a crecer y se empiezan a entender mejor los patrones de uso, es posible que se deba ajustar el esquema de partición. Por ejemplo, es posible que algunas particiones individuales atraigan un volumen de tráfico desproporcionado y se conviertan en populares, dando lugar a una contención excesiva. Además, es posible que haya subestimado el volumen de datos de algunas particiones y se esté acercando a los límites de la capacidad de almacenamiento en estas particiones. Independientemente de la causa, a veces es necesario volver a equilibrar las particiones para distribuir la carga uniformemente.

En algunos casos, los sistemas de almacenamiento de datos que no exponen públicamente la manera en que los datos se asignan a los servidores pueden reequilibrar automáticamente las particiones dentro de los límites de los recursos disponibles. En otras situaciones, el reequilibrio es una tarea administrativa que consta de dos fases:

1. Determinación de la nueva estrategia de partición para determinar:
    - Qué particiones es necesario dividir (o posiblemente combinar).
    - Cómo asignar datos a estas nuevas particiones mediante el diseño de nuevas claves de partición.
2. Migrar los datos afectados desde el esquema de partición antiguo al nuevo conjunto de particiones.

> [AZURE.NOTE] La asignación de colecciones de base de datos de DocumentDB a los servidores es transparente, pero todavía podría alcanzar los límites de capacidad y rendimiento de almacenamiento de una cuenta de DocumentDB. Si esto ocurriera, puede que necesite volver a diseñar su esquema de partición y migrar los datos.

Dependiendo de la tecnología de almacenamiento de datos y del diseño de su sistema de almacenamiento de datos, es posible que pueda migrar datos entre particiones mientras están en uso (migración en línea). Si esto no es posible, puede que necesite establecer las particiones afectadas como no disponibles temporalmente mientras se cambia la ubicación de los datos (migración sin conexión).

## Migración sin conexión

Se podría afirmar que la migración sin conexión es el planteamiento más sencillo porque reduce las posibilidades de que se produzca contención. No realice ningún cambio en los datos mientras se mueven y reestructuran.

Conceptualmente, este proceso consta de los siguientes pasos:

1. Marcar la partición sin conexión.
2. Dividir y combinar y mover los datos a las nuevas particiones.
3. Comprobar los datos.
4. Establecer las nuevas particiones en línea.
5. Quitar la partición antigua.

Para conservar algo de disponibilidad, puede marcar la partición original como de solo lectura en el paso 1 en lugar de establecerla como no disponible. Esto permite a las aplicaciones leer los datos mientras se mueven, pero no cambiarlos.

## Migración en línea

La migración en línea es más difícil de realizar, pero es menos problemática para los usuarios, ya que los datos permanecen disponibles durante todo el procedimiento. El proceso es similar al usado por la migración sin conexión, salvo que la partición original no está marcada como sin conexión (paso 1). Según la granularidad del proceso de migración (por ejemplo, si se hace elemento por elemento o partición por partición), es posible que el código de acceso a datos de las aplicaciones cliente tenga que controlar la lectura y escritura de los datos que se encuentran en dos ubicaciones (la partición original y la nueva partición)

Para ver un ejemplo de una solución que admita la migración en línea, consulte [Escalado con la herramienta de división y combinación de bases de datos elásticas] en el sitio web de Microsoft.

## Orientación y patrones relacionados

Es posible que los siguientes modelos también resulten pertinentes para su escenario al considerar las estrategias para implementar la coherencia de los datos:

- En la página [Data consistency primer] (Información básica sobre la coherencia de datos) se describen las estrategias para mantener la coherencia en un entorno distribuido, como la nube.
- La página [Data partitioning guidance] (Guía de creación de particiones de datos) proporciona una visión general de cómo diseñar particiones para cumplir varios criterios en una solución distribuida.
- En [Sharding Pattern] (Patrón de particionamiento), descrito en el sitio web de Microsoft, se resumen algunas estrategias comunes para el particionamiento de los datos.
- En [Index Table Pattern] (Patrón de la tabla de índice), descrito en el sitio web de Microsoft, se ilustra cómo crear índices secundarios sobre los datos. Con este enfoque, una aplicación puede recuperar rápidamente los datos mediante el uso de consultas que no hacen referencia a la clave principal de una colección.
- En [Materialized View Pattern] (Patrón de vista materializada), descrito en el sitio web de Microsoft, se describe cómo generar vistas rellenadas previamente que resuman los datos para admitir operaciones de consulta rápida. Este enfoque puede ser útil en un almacén de datos con particiones si las particiones que contienen los datos que se resumen se distribuyen entre varios sitios.
- En el artículo [Uso de la red CDN en Azure] del sitio web de Microsoft, se ofrecen instrucciones adicionales sobre cómo configurar y usar la red de entrega de contenido con Azure.

## Más información

- La página [¿Qué es Base de datos SQL?] del sitio web de Microsoft ofrece documentación detallada en la que se describe la creación y el uso de bases de datos SQL.
- En la página [Información general de las características de Base de datos elástica] del sitio web de Microsoft se proporciona una introducción completa a Base de datos elástica.
- La página [Escalado con la herramienta de división y combinación de bases de datos elásticas] del sitio web de Microsoft contiene información sobre el uso del servicio de división y combinación para administrar particiones de Base de datos elástica.
- La página [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dn249410.aspx) del sitio web de Microsoft documenta los límites de tamaño y rendimiento actuales de Almacenamiento de Azure.
- La página [Realizar transacciones con grupos de entidades] del sitio web de Microsoft proporciona información detallada sobre la implementación de operaciones transaccionales sobre entidades almacenadas en Almacenamiento de tablas de Azure.
- El artículo [Guía de diseño de la tabla de almacenamiento de Azure: diseño escalable y tablas de rendimiento] del sitio web de Microsoft contiene información detallada sobre la creación de particiones de datos en Almacenamiento de tablas de Azure.
- La página [Uso de la red CDN en Azure] del sitio web de Microsoft describe cómo replicar los datos almacenados en Almacenamiento de blobs de Azure mediante el uso de la Red de entrega de contenido (CDN) de Azure.
- La página [Más información sobre almacenamiento de documentos y capacidad en DocumentDB] del sitio web de Microsoft contiene información sobre cómo Azure DocumentDB asigna recursos a bases de datos.
- La página [¿Qué es Búsqueda de Azure?] del sitio web de Microsoft proporciona una descripción completa de las funciones disponibles con el servicio Búsqueda de Azure.
- La página [Límites de servicio en Búsqueda de Azure] del sitio web de Microsoft contiene información sobre la capacidad de cada instancia del servicio Búsqueda de Azure.
- La página [Tipos de datos admitidos (Búsqueda de Azure)] del sitio web de Microsoft resume los tipos de datos que puede usar en los documentos y los índices en los que se pueden hacer búsquedas.
- La página [Caché en Redis de Azure] del sitio web de Microsoft proporciona una introducción a Caché en Redis de Azure.
- La página [Partitioning: how to split data among multiple Redis instances] (Creación de particiones: cómo dividir los datos entre varias instancias de Redis) del sitio web de Redis proporciona información sobre cómo implementar las particiones con Redis.
- La página [Running Redis on a CentOS Linux VM in Azure] (Ejecución de Redis en una máquina virtual Linux de CentOS en Azure) del sitio web de Microsoft muestra un ejemplo de cómo crear y configurar un nodo Redis que se ejecuta como una máquina virtual de Azure.
- La página [Data types] (Tipos de datos) del sitio web de Redis describe los tipos de datos que están disponibles con Redis y Caché en Redis de Azure.

[Caché en Redis de Azure]: http://azure.microsoft.com/services/cache/
[Objetivos de escalabilidad y rendimiento del almacenamiento de Azure]: storage/storage-scalability-targets.md
[Guía de diseño de la tabla de almacenamiento de Azure: diseño escalable y tablas de rendimiento]: storage/storage-table-design-guide.md
[Building a Polyglot Solution]: https://msdn.microsoft.com/library/dn313279.aspx
[Data Access for Highly-Scalable Solutions: Using SQL, NoSQL, and Polyglot Persistence]: https://msdn.microsoft.com/library/dn271399.aspx
[Data consistency primer]: http://aka.ms/Data-Consistency-Primer
[Data Partitioning Guidance]: https://msdn.microsoft.com/library/dn589795.aspx
[Data Types]: http://redis.io/topics/data-types
[Cuotas y límites de DocumentDB]: documentdb/documentdb-limits.md
[Límites y cuotas de DocumentDB]: documentdb/documentdb-limits.md
[Información general de las características de Base de datos elástica]: sql-database/sql-database-elastic-scale-introduction.md
[Federations Migration Utility]: https://code.msdn.microsoft.com/vstudio/Federations-Migration-ce61e9c1
[Index Table Pattern]: http://aka.ms/Index-Table-Pattern
[patrón de tabla de índice]: http://aka.ms/Index-Table-Pattern
[Más información sobre almacenamiento de documentos y capacidad en DocumentDB]: documentdb/documentdb-manage.md
[Materialized View Pattern]: http://aka.ms/Materialized-View-Pattern
[Consultas a través de particiones múltiples]: sql-database/sql-database-elastic-scale-multishard-querying.md
[Partitioning: how to split data among multiple Redis instances]: http://redis.io/topics/partitioning
[Niveles de rendimiento en DocumentDB]: documentdb/documentdb-performance-levels.md
[Realizar transacciones con grupos de entidades]: https://msdn.microsoft.com/library/azure/dd894038.aspx
[Tutorial de clúster Redis]: http://redis.io/topics/cluster-tutorial
[Running Redis on a CentOS Linux VM in Azure]: http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx
[Escalado con la herramienta de división y combinación de Base de datos elástica]: sql-database/sql-database-elastic-scale-overview-split-and-merge.md
[Escalado con la herramienta de división y combinación de bases de datos elásticas]: sql-database/sql-database-elastic-scale-overview-split-and-merge.md
[Uso de la red CDN en Azure]: cdn/cdn-how-to-use-cdn.md
[Cuotas de Bus de servicio]: service-bus/service-bus-quotas.md
[Límites de servicio en Búsqueda de Azure]: search/search-limits-quotas-capacity.md
[Límites de servicio en la Búsqueda de Azure]: search/search-limits-quotas-capacity.md
[Sharding pattern]: http://aka.ms/Sharding-Pattern
[patrón particionamiento]: http://aka.ms/Sharding-Pattern
[Tipos de datos admitidos (Búsqueda de Azure)]: https://msdn.microsoft.com/library/azure/dn798938.aspx
[Transactions]: http://redis.io/topics/transactions
[¿Qué es Búsqueda de Azure?]: search/search-what-is-azure-search.md
[¿Qué es Base de datos SQL?]: sql-database/sql-database-technical-overview.md

<!---HONumber=AcomDC_0406_2016-->