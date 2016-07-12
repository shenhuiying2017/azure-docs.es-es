<properties
   pageTitle="Modelos de diseño para aplicaciones SaaS multiempresa con Base de datos SQL de Azure | Microsoft Azure" 
   description="En este artículo se explican los requisitos y modelos de arquitectura de datos comunes de las aplicaciones de base de datos multiinquilino que se ejecutan en un entorno de nube que debe tener en cuenta y los distintos inconvenientes de estos modelos. También se explica cómo ayuda el servicio de base de datos SQL de Azure con sus grupos de bases de datos elásticas y herramientas elásticas a afrontar estos requisitos de forma garantizada."
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-design"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# Modelos de diseño para las aplicaciones SaaS multiinquilino con base de datos SQL de Azure

En este artículo aprenderá sobre los requisitos y patrones de arquitectura de datos comunes de aplicaciones de base de datos de software como servicio (SaaS) multiinquilino que se ejecutan en un entorno de nube que debe tener en cuenta y los distintos inconvenientes de estos modelos. También se explica cómo ayuda el servicio de base de datos SQL de Azure con sus grupos de bases de datos elásticas y herramientas elásticas a afrontar estos requisitos de forma garantizada.

## Introducción

Al haber trabajado con proveedores de aplicaciones de SaaS en los últimos años, vemos que los programadores suelen tomar decisiones en contra de sus intereses a largo plazo al diseñar los modelos de arrendamiento para las capas de datos de sus aplicaciones multiinquilino. Al menos inicialmente, la facilidad de desarrollo y el menor costo del proveedor de nube se percibe como más importante que el aislamiento de inquilinos o la escalabilidad de las aplicaciones. Esto conduce a menudo a insatisfacción del cliente y correcciones de curso costosas más adelante.

En el contexto de este artículo, una aplicación multiinquilino es una aplicación hospedada en un entorno de nube y proporciona el mismo conjunto de servicios para varios inquilinos (cientos o miles) que no comparten ni ven los datos de la otra. Una categoría arquetípica de dicho servicio es una aplicación de SaaS que ofrece servicios a sus inquilinos en un entorno hospedado en la nube.

## Aplicaciones multiinquilino

Las aplicaciones multiinquilino son un ejemplo visible de un tipo de aplicación cuyos datos y cargas de trabajo se pueden dividir fácilmente. Por ejemplo, con las aplicaciones multiinquilino, los datos y la carga de trabajo normalmente se pueden dividir a lo largo de los límites del inquilino, puesto que la mayoría de las solicitudes se refieren a un inquilino. En el resto del artículo se analiza qué propiedad es inherente a los datos y la carga de trabajo, y favorece a los patrones de las aplicaciones.

Encontramos que dichas aplicaciones se extienden por todo el espectro de aplicaciones basadas en la nube, incluidos
- Aplicaciones de base de datos de ISV en transición a la nube como aplicaciones de SaaS
- Aplicaciones de SaaS elaboradas para la nube desde cero
- Aplicaciones directas al consumidor/para el usuario final
- Aplicaciones empresariales para los empleados

Tanto las aplicaciones de SaaS surgidas en la nube como las aplicaciones de SaaS basadas en aplicaciones de base de datos de ISV suelen derivar en aplicaciones multiinquilino. Estas aplicaciones de SaaS ofrecen una aplicación de software especializado como servicio a sus inquilinos. Los inquilinos tienen acceso al servicio de la aplicación y la propiedad total de los datos asociados almacenados como parte de la aplicación. Pero para poder aprovechar las ventajas de SaaS, sus inquilinos deben entregar un nivel de control sobre sus propios datos, al confiar en el proveedor de SaaS para que los mantenga seguros y aislados de los datos de otros inquilinos. Ejemplos típicos son MyOB, SnelStart, Salesforce, etc. Todas estas aplicaciones permiten la creación de particiones a lo largo de los límites del inquilino y, por tanto, admiten los patrones de las aplicaciones que se tratan en las siguientes secciones de este artículo.

Las aplicaciones que proporcionan un servicio directo a los consumidores o empleados dentro de una organización (a menudo denominados usuarios, en lugar de inquilinos) forman otra categoría en el espectro de la aplicación multiinquilino. Los clientes se suscriben al servicio y no poseen los datos recopilados y almacenados por el proveedor de servicios. Los proveedores de servicios tienen requisitos menos estrictos para mantener los datos de sus clientes aislados entre sí, aparte de los impuestos por el gobierno sobre normas de privacidad. Ejemplos típicos son los proveedores de contenido multimedia como Netflix, Spotify, Xbox live, etc. Otros ejemplos de aplicaciones que se pueden dividir fácilmente se encuentran con aplicaciones para internet orientadas al cliente o a internet de las cosas (IoT), donde cada cliente o dispositivo sirve de partición y se pueden dibujar límites de partición entre distintos usuarios o dispositivos.

Sin embargo, también reconocemos que no todas las aplicaciones se dividen fácilmente a lo largo de una sola propiedad como inquilino, cliente, usuario o dispositivo. Considere, por ejemplo, complejas aplicaciones ERP (Enterprise Resource Planning) con los productos, pedidos y clientes. Normalmente tienen una estructura compleja con miles de tablas muy interconectadas. Ninguna estrategia de partición única se aplicará a todas las tablas y funcionará con toda la carga de trabajo. Por lo tanto, explícitamente las excluimos de nuestro debate en el resto de este artículo.

Los modelos de diseño multiinquilino que exploramos en las secciones siguientes se aplican a todos los modelos de aplicación anteriores con cargas de trabajo y datos que se dividen fácilmente. Sin embargo, para facilitar la presentación, nos referimos a todos ellos como aplicaciones de SaaS multiinquilino.

## Diseño de aplicación multiinquilino: ventajas y desventajas

Para los desarrolladores que crean aplicaciones multiinquilino en la nube, existen las siguientes dimensiones principales:

-	***Aislamiento de inquilinos***: los desarrolladores deben garantizar que ningún inquilino obtiene acceso sin autorización a los datos de otros inquilinos. Este requisito de aislamiento se extiende a otras propiedades como la protección de vecinos ruidosos, la capacidad de restaurar los datos de un inquilino determinado, la personalización específica de inquilinos, etc.
-	***Costo de los recursos de nube***: la aplicación de SaaS debe ser rentable. Según esto, los desarrolladores optimizan las aplicaciones de SaaS para reducir el costo del uso de recursos de nube (proceso, almacenamiento, etc.) al diseñar las aplicaciones multiinquilino.
-	***Facilidad de DevOps***: los proveedores de aplicaciones multiinquilino deben crear una protección de aislamiento, mantener sus aplicaciones o el esquema de la base de datos, supervisar su estado y solucionar los problemas de sus inquilinos. La complejidad del desarrollo y el funcionamiento de las aplicaciones se traduce directamente en el costo añadido y la menor satisfacción del inquilino.
-	***Escalabilidad***: agregar gradualmente más inquilinos es primordial para el funcionamiento correcto de SaaS, así como también lo es agregar más capacidad para los inquilinos que necesiten más recursos.

Entre estas dimensiones existen ventajas y desventajas. La opción de nube de menor costo puede no ofrecer necesariamente la experiencia de desarrollo más práctica. Con frecuencia vemos a los desarrolladores realizar elecciones sin información en el espacio identificado por las cuatro dimensiones anteriores.

Para las aplicaciones de SaaS multiinquilino que se encargan de otras empresas, el aislamiento de inquilinos suele ser un requisito fundamental. Se ve a menudo a los desarrolladores tentados por las ventajas percibidas con respecto a la simplicidad y el costo sobre el aislamiento de inquilinos y la escalabilidad. Esta opción resulta compleja y costosa, según crece el servicio y los requisitos de aislamiento de inquilinos se vuelven más importantes y necesitan tratarse a nivel de aplicación.

Para las aplicaciones multiinquilino que proporcionan un servicio orientado al consumidor directo para los usuarios finales, el aislamiento de inquilinos puede perder prioridad frente a la optimización del costo de los recursos de nube.

Un modelo de desarrollo conocido es empaquetar varios inquilinos en una o varias bases de datos. Las ventajas (percibidas) con este enfoque es reducir el costo al pagar solo por algunas bases de datos y mantener las cosas sencillas al trabajar únicamente en el menor número de bases de datos posible. Para las aplicaciones de SaaS multiinquilino, con el tiempo, esta decisión acarrea desventajas importantes en relación con el aislamiento de inquilinos y la escalabilidad. Si se necesita aislamiento de inquilinos, supondrá un esfuerzo adicional para proteger los datos del inquilino en el almacenamiento compartido del acceso no autorizado o los vecinos ruidosos que puede mejorar considerablemente el desarrollo de aplicaciones y los costos de mantenimiento de aislamiento. De forma similar, cuando se agregan a nuevos inquilinos, escalar correctamente la capa de datos de la aplicación normalmente requiere conocimientos de desarrollo para redistribuir los datos del inquilino en las bases de datos.

## Modelos de datos multiinquilino 

Las prácticas de diseño comunes para colocar los datos del inquilino siguen estos tres modelos distintos.


  ![modelos de datos multiinquilino](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png) Figura 1: Prácticas de diseño comunes para los modelos de datos multiinquilino
  
1.	***Base de datos por inquilino***: este enfoque coloca cada inquilino en su propia base de datos. Todos los datos específicos del inquilino se limitan a su base de datos y se asilan de otros inquilinos y sus datos.
2.	***Bases de datos divididas compartidas***: este enfoque utiliza varias bases de datos con varios inquilinos que las comparten, es decir, a cada base de datos se asigna un conjunto distinto de los inquilinos mediante una estrategia de división como hash, intervalo o particiones de lista. Esta estrategia de distribución de datos a menudo se conoce como particionamiento.
3.	***Única base de datos compartida***: esta solución utiliza una única base de datos, a veces grande, que contiene datos de todos los inquilinos sin ambigüedad con una columna de identificador del inquilino.
  
> [AZURE.NOTE] A veces, varios inquilinos también se colocan en esquemas de base de datos diferentes donde el nombre de este se utiliza para eliminar la ambigüedad entre inquilinos. No es un enfoque recomendado, ya que normalmente requiere el uso de SQL dinámico y no podrá usar eficazmente el almacenamiento en caché de plan. Por lo tanto, el resto de este artículo se centra en el enfoque de tabla compartida en esta categoría.
 
## Caracterización de modelos de datos multiinquilino populares

Al evaluar el uso de estos modelos de datos multiinquilino es importante enmarcarlos en cuanto a ventajas y desventajas del diseño de la aplicación explicadas en la sección anterior.

-	***Aislamiento***: el grado de aislamiento entre los inquilinos como medida de cuánto aislamiento logra un modelo de datos, y
-	***Costo de los recursos de nube***: la cantidad de recursos compartidos entre inquilinos para optimizar el costo de los recursos de nube. Un recurso puede definirse como el costo de proceso y de almacenamiento.
-	***Costo de DevOps***: facilidad de desarrollo de aplicaciones, la implementación y la administración reducen el costo operativo global de SaaS.

Con estas dimensiones podemos identificar los modelos de datos multiinquilino descritos anteriormente y su uso de la base de datos con el espacio del cuadrante que se muestra en la figura 2. El grado de aislamiento de inquilinos y la cantidad de uso compartido de recursos describen las dimensiones de eje X e Y del espacio. La gran flecha diagonal en el medio indica los costos de DevOps.

![modelos populares](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png) Figura 2: Caracterización de modelos de datos multiinquilino populares

El cuadrante inferior derecho de la figura 2 muestra cómo usar un enfoque de base de datos única, potencialmente grande y compartida junto con la tabla compartida (o esquema independiente) como uno de los modelos de aplicación. Proporciona excelentes recursos compartidos, ya que todos los inquilinos usan los mismos recursos de base de datos (CPU, memoria, E/S...) en una sola base de datos. Sin embargo, el aislamiento de inquilinos es limitado. Se necesitan pasos adicionales para proteger a los inquilinos entre sí a nivel de aplicación, lo que puede aumentar considerablemente los costos de DevOps en la administración y el desarrollo de aplicaciones. Además, la escalabilidad está limitada por la escala del hardware que hospeda la base de datos.

La sección central muestra a varios inquilinos divididos en varias bases de datos diferentes (las unidades de escalado de hardware suelen diferir); cada base de datos hospeda un subconjunto de inquilinos que soluciona el problema de escalabilidad del modelo anterior. Si la nueva capacidad se requiere para más inquilinos, pueden colocarse fácilmente en las nuevas bases de datos asignadas a las nuevas unidades de escalado de hardware. Sin embargo, se reduce la cantidad de uso compartido de recursos, ya que solo los inquilinos de las mismas unidades de escala comparten los recursos. Además, el enfoque supone pocas mejoras de aislamiento de inquilinos, ya que muchos inquilinos todavía se colocan en los mismos lugares sin estar protegidos automáticamente de las acciones de los demás. La complejidad de la aplicación permanece alta. Este enfoque a menudo se denomina "particionamiento".

El cuadrante superior izquierdo de la figura 2 es el tercer enfoque. Coloca a cada inquilino en su propia base de datos. Este enfoque tiene propiedades de aislamiento de inquilinos excelente, pero permite pocos recursos de uso compartido cuando cada base de datos proporciona sus propios recursos específicos. Este enfoque es fantástico si todos los inquilinos presentan cargas de trabajo predecibles. Sin embargo, en cuanto las cargas de trabajo de los inquilinos se vuelvan menos predecibles (lo más común para el SaaS), el uso compartido de recursos no se puede optimizar, lo que provoca que el proveedor aprovisione en exceso para satisfacer las demandas o la menor cantidad de recursos derive en mayores costos o menos satisfacción de los inquilinos. Como resultado, es mejor un mayor grado de uso compartido entre los inquilinos de recursos pasa a ser deseable para que la solución sea más rentable. El aumento del número de bases de datos también aumenta el costo de DevOps en la implementación y el mantenimiento de aplicaciones. Volveremos a estos temas en la sección siguiente, pero es importante recordar que este método proporciona la mejor y más fácil solución de aislamiento de inquilinos en comparación con los demás.

La elección del cliente del modelo de diseño también se ve influida por estos otros factores:

-	***Propiedad de los datos de los inquilinos***: si los inquilinos conservan la propiedad de sus datos, esto favorece el modelo de base de datos única por inquilino
-	***Escala***: si la aplicación está destinada a cientos de miles o millones de inquilinos, favorece los enfoques de bases de datos compartidas como el particionamiento, pero los requisitos de aislamiento pueden plantear dificultades.
-	***Modelo y valor empresarial***: por ingresos del inquilino si son muy reducidos (menos de un dólar), los requisitos de aislamiento se vuelven menos críticos y compartir la base de datos tiene sentido. Si son unos dólares o más, la base de datos por inquilino será factible y se puede reducir el costo de DevOps de la aplicación.

Dadas las ventajas y desventajas anteriores tal como se muestra en la figura 2, un modelo multiinquilino ideal necesitaría incorporar propiedades de aislamiento de inquilinos excelentes con un uso óptimo de los recursos compartidos entre los inquilinos, es decir, un modelo que se ajuste al cuadrante superior derecho.

## Compatibilidad con la arquitectura multiinquilino con la base de datos SQL de Azure

La base de datos SQL de Azure es compatible con todos los modelos de aplicación multiinquilino descritos en la figura 2. Con los grupos de bases de datos elásticas, ahora también admite un nuevo modelo de aplicación que combina uso compartido excelente de los recursos y las ventajas del aislamiento de la base de datos por inquilino (cuadrante superior derecho en verde en la figura 3). Las herramientas de bases de datos elásticas y las funcionalidades de base de datos SQL de Azure ayudan con los costos de DevOps en el desarrollo y el funcionamiento de una aplicación con muchas bases de datos (lo que se muestra en la parte sombreada en la figura 3). Estas herramientas ayudan a crear y administrar aplicaciones al adoptar cualquiera de los modelos de varias bases de datos. En las siguientes subsecciones se describen las funcionalidades de la base de datos SQL y cómo ayuda a estos modelos multiinquilino con más detalle.

![modelo de base de datos SQL](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png) Figura 3: Modelos de aplicación multiinquilino con la base de datos SQL de Azure

## Modelo de base de datos por inquilino con grupos elásticos y herramientas

La base de datos SQL de Azure proporciona "grupos de bases de datos elásticas" para mejorar la compatibilidad con el enfoque de la "base de datos por inquilino" que combina el aislamiento de inquilinos con recursos compartidos entre las bases de datos de los inquilinos. Está diseñada como una solución de capa de datos para que los proveedores de SaaS creen aplicaciones multiinquilino. Cuando se combina con ofertas de herramientas de bases de datos elásticas, la funcionalidad multiinquilino está integrada y la carga de recursos compartidos entre los inquilinos se mueve desde la aplicación hasta el nivel de servicio de la base de datos. La complejidad de administración y de consulta a escala entre bases de datos se simplifica con trabajos elásticos, consultas, transacciones y la biblioteca de cliente de base de datos elástica.

| Requisitos de la aplicación | Funcionalidades de Base de datos SQL |
| ------------------------ | ------------------------- |
| Aislamiento de inquilinos y uso compartido de recursos | [Grupos de bases de datos elásticas:](sql-database-elastic-pool.md) esta característica permite asignar un grupo de recursos SQLDB y compartirlos entre varias bases de datos. Además, las bases de datos individuales pueden sacar todos los recursos necesarios del grupo para acomodar los picos de demanda de capacidad debido a cambios en las cargas de trabajo de los inquilinos y el grupo elástico sí se puede escalar hacia arriba o hacia abajo según sea necesario. Los grupos elásticos también facilitan la administración, la supervisión y la solución de problemas a nivel de grupo. |
| Facilidad de DevOps entre bases de datos | [Grupos de bases de datos elásticas:](sql-database-elastic-pool.md) como se indica anteriormente.|
||[Consulta elástica:](sql-database-elastic-query-horizontal-partitioning.md) proporciona capacidad de consulta entre las bases de datos para los informes o el análisis entre inquilinos.|
||[Trabajos elásticos:](sql-database-elastic-jobs-overview.md) proporciona capacidad para empaquetar e implementar de forma fiable las operaciones de mantenimiento de bases de datos o los cambios de esquema de base de datos en varias bases de datos.|
||[Transacciones elásticas:](sql-database-elastic-scale.md) proporciona capacidad para procesar los cambios en varias bases de datos de forma atómica y aislada. Esto es necesario cuando las aplicaciones necesitan garantías de "todo o nada" para varias operaciones de base de datos. |
||[Biblioteca de cliente de base de datos elástica:](sql-database-elastic-database-client-library.md) esta característica permite administrar las distribuciones de los datos y la asignación de inquilinos para las bases de datos. |
||||

## Modelos compartidos

Como se describió anteriormente, para la mayoría proveedores de SaaS, los enfoques de "modelo compartido" pueden plantear problemas con el aislamiento de los inquilinos, así como complejidades de desarrollo y mantenimiento de la aplicación. Sin embargo, para las aplicaciones multiinquilino que proporcionan un servicio directamente a los consumidores finales, los requisitos de aislamiento de inquilinos pueden no ser tener tanta prioridad como la necesidad de optimizar el costo. Pueden empaquetar los inquilinos en una o más bases de datos de muy alta densidad para reducir los costos. Los modelos de base de datos compartida con una o varias bases de datos divididas pueden añadir eficacia al uso compartido de recursos y reducir el costo general. Base de datos SQL de Azure proporciona algunas características que ayudan a los clientes a crear aislamiento para la seguridad y la administración a escala en la capa de datos.

| Requisitos de la aplicación | Funcionalidades de Base de datos SQL |
| ------------------------ | ------------------------- |
| Características de aislamiento de seguridad | [Seguridad de nivel de fila](https://msdn.microsoft.com/library/dn765131.aspx) |
|| [Crear un esquema de la base de datos](https://msdn.microsoft.com/library/dd207005.aspx) |
| Facilidad de DevOps entre bases de datos | [Consulta elástica](sql-database-elastic-query-horizontal-partitioning.md) |
|| [Trabajos elásticos](sql-database-elastic-jobs-overview.md) |
|| [Transacciones elásticas](sql-database-elastic-transactions-overview.md) |
|| [Biblioteca de cliente de base de datos elástica](sql-database-elastic-database-client-library.md) |
|| [Dividir/combinar bases de datos elásticas](sql-database-elastic-scale-overview-split-and-merge.md) |
||||

## Conclusiones

Los requisitos de aislamiento de inquilinos son muy importantes para la mayoría de las aplicaciones de SaaS multiinquilino. La mejor opción para proporcionar aislamiento se basa básicamente en el enfoque de "base de datos por inquilino". Los otros dos enfoques requieren inversiones de capa de aplicación complejas que requieren personal de desarrollo especializado para proporcionar aislamiento. Esto puede aumentar considerablemente el costo y el riesgo. Si los requisitos de aislamiento no se tienen en cuenta temprano en el desarrollo de los servicios, puede ser incluso más costoso en los dos primeros modelos ajustarlos con carácter retroactivo. Los inconvenientes principales asociados con el modelo de "base de datos por inquilino" están relacionados con los mayores costos de los recursos de nube debidos al reducido uso compartido, mantenimiento y administración de gran cantidad de bases de datos. Los desarrolladores de aplicaciones de SaaS a menudo tienen problemas con estos aspectos.

Aunque estos aspectos pueden ser principales obstáculos para la mayoría de proveedores de servicios de base de datos de nube, el servicio de bases de datos SQL de Azure elimina estas barreras con sus "funcionalidades de base de datos elástica" y su "grupo de bases de datos elásticas". Los desarrolladores de SaaS pueden combinar las características de aislamiento del modelo de base de datos por inquilino y optimizar el uso compartido de recursos y la capacidad de administración mejorada de gran número de bases de datos con grupos elásticos y sus herramientas asociadas.

Para los proveedores de aplicaciones multiinquilino sin requisitos de aislamiento de inquilinos y que pueden empaquetar los inquilinos en una base de datos de muy alta densidad para reducir los costos, los modelos de datos "compartidos" pueden añadir eficacia al uso compartido de recursos y reducir el costo total. Las herramientas de bases de datos elásticas de base de datos SQL de Azure, las bibliotecas de particionamiento y las características de seguridad ayudan a los proveedores de SaaS a crear y administrar las aplicaciones multiinquilino.

## Pasos siguientes

Para ver una aplicación de ejemplo que demuestra la biblioteca de cliente, consulte [Introducción a las herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md).

Si quiere ver una aplicación de ejemplo que proporciona una solución para un escenario de Software como solución (SaaS) que aprovecha los grupos elásticos con el fin de ofrecer un back-end de base de datos escalable y rentable de una aplicación SaaS, consulte [Elastic Pool Custom Dashboard for Saas](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) (Panel personalizado de grupos elásticos para SaaS).

Para convertir las bases de datos existentes con el fin de emplear las herramientas, consulte [Migrate existing databases to scale-out (Migrar bases de datos existentes de escalado horizontal)](sql-database-elastic-convert-to-use-elastic-tools.md).

Para crear un nuevo grupo, consulte el tutorial [Creación de un nuevo grupo de bases de datos elásticas con el Portal de Azure](sql-database-elastic-pool-create-portal.md).

Para supervisar y administrar un grupo de bases de datos elásticas, consulte [Supervisión y administración de un grupo de bases de datos elásticas con el Portal de Azure](sql-database-elastic-pool-manage-portal.md).

## Recursos adicionales

- [¿Qué es un grupo de bases de datos elásticas de Azure?](sql-database-elastic-pool.md)
- [Escalado horizontal con Base de datos SQL de Azure](sql-database-elastic-scale-introduction.md)
- [Aplicaciones de múltiples inquilinos con herramientas de bases de datos elásticas y seguridad de nivel de fila](sql-database-elastic-tools-multi-tenant-row-level-security.md)
- [Authentication in multitenant apps, using Azure AD and OpenID Connect (Autenticación en aplicaciones multiinquilino con Azure AD y OpenID Connect)](../guidance/guidance-multitenant-identity-authenticate.md)
- [Aplicación Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)
- [Inicios rápidos de soluciones](sql-database-solution-quick-starts.md)

## Preguntas y solicitudes de características

Si tiene alguna pregunta, póngase en contacto con nosotros en el [foro de Base de datos SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Para efectuar solicitudes de características, agréguelas en el [foro de comentarios sobre Base de datos SQL](https://feedback.azure.com/forums/217321-sql-database/).









	

<!---HONumber=AcomDC_0629_2016-->