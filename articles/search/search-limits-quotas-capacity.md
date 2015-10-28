<properties
	pageTitle="Límites de servicio en la Búsqueda de Azure"
	description="Límites de la Búsqueda Azure usados en la planificación de capacidad y límites máximos en solicitudes y respuestas."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article" 
	ms.tgt_pltfrm="na"
	ms.date="08/18/2015"
	ms.author="heidist"/>

#Límites de servicio en la Búsqueda de Azure

Los límites máximos del almacenamiento, las cargas de trabajo y las cantidades de índices, documentos y otros objetos dependen de cómo se suscriba a la Búsqueda de Azure. El servicio gratuito está pensado para evaluación y pruebas de concepto, con límites inferiores en todos los objetos y cargas de trabajo de manera que los recursos se pueden compartir de manera más equitativa.

Las ejecuciones estándar se ejecutan en máquinas dedicadas que solo se usan por el servicio. El uso exclusivo de un servicio dedicado le ofrece la capacidad de escalar o de reducir verticalmente, con más capacidad de almacenamiento y de procesamiento en todos los niveles, incluida la configuración mínima.

##Límites máximos para un servicio de búsqueda (compartido) gratuito

Los suscriptores de Azure pueden usar el servicio de búsqueda (multiempresa) compartido para desarrollo o aplicaciones de búsqueda muy pequeñas. El servicio compartido se incluye con su suscripción a Azure. Es una opción sin coste que le permite experimentar con el servicio antes de registrarse. Ofrece:

Objeto|Límite
------|-----
Número máximo de índices|3
Número máximo de campos por índice|1000
Número máximo de documentos|10\.000
Tamaño máximo de almacenamiento|50 MB
Número máximo de particiones|N/D
Número máximo de réplicas|N/D
Número máximo de unidades de Búsqueda|N/D
Número máximo de indexadores|3
Número máximo de orígenes de datos de indexador|3
Número máximo de documentos indizados por invocación de indizador|10\.000

Tenga en cuenta que no hay cuotas ni límites máximos asociados a las consultas. Las consultas por segundo (QPS) son variables, en función de la competición y del ancho de banda disponible para los recursos del sistema. Los recursos de proceso y almacenamiento de Azure que respaldan su servicio compartido se comparten por varios suscriptores, por lo que llevar a cabo QPS para su solución variará en función de cuántas otras cargas de trabajo se ejecutan al mismo tiempo.

##Límites máximos para un servicio de búsqueda estándar (dedicado)

En el nivel de precios estándar, un servicio de búsqueda dedicado solo almacena sus datos y ejecuta únicamente sus cargas de trabajo. A diferencia del servicio compartido, la asignación de recursos para un servicio de búsqueda dedicado es ajustable, escalando a cualquier nivel que necesite. Puede establecer de forma independiente los niveles de recursos para particiones (para escalar verticalmente el almacenamiento) y las réplicas (para ofrecer una alta disponibilidad y escalar verticalmente QPS y el rendimiento de la indización). Vea [Administrar la solución de búsqueda](search-manage.md) para investigar las diferentes configuraciones de recursos.

La tabla siguiente es una lista de límites superiores, pero debe revisar aún más el gráfico de matriz para conocer la capacidad en términos de [combinaciones de particiones y réplicas](#chart) permitidas.

Objeto|Límite
------|----
Número máximo de índices|50 por servicio Búsqueda
Número máximo de campos por índice|1000
Número máximo de documentos|15 millones por partición
Tamaño máximo de almacenamiento|25 GB por partición
Número máximo de particiones|12 por servicio Búsqueda
Número máximo de réplicas|6 por servicio Búsqueda
Número máximo de unidades de Búsqueda|36 por servicio Búsqueda
Número máximo de indexadores|50 por servicio Búsqueda
Número máximo de orígenes de datos de indexador|50 por servicio Búsqueda
Número máximo de documentos indizados por invocación de indizador|Sin límite

La capacidad en la Búsqueda de Azure se puede adquirir en incrementos denominados unidades de búsqueda. El nivel de precios estándar permite hasta 36 unidades de búsqueda por servicio de búsqueda. Este límite invalida los límites individuales de las particiones y réplicas. Por ejemplo, no se puede escalar verticalmente su servicio hasta 12 particiones y 6 réplicas, porque hacerlo le exigiría 72 unidades de búsqueda (12 x 6), superando así el límite de 36 unidades de búsqueda por servicio.

##Acerca de particiones y réplicas

Las **particiones** ofrecen almacenamiento y E/S. Un único servicio de búsqueda puede tener un máximo de 12 particiones. Cada partición se incluye con un límite máximo de 15 millones de documentos o 25 GB de almacenamiento, lo que ocurra primero. Si agrega particiones, su servicio de búsqueda puede cargar más documentos. Por ejemplo, un servicio con una partición única que almacena inicialmente hasta 25 GB de datos puede almacenar 50 GB cuando se agrega una segunda partición al servicio.

Las **réplicas** son copias del motor de búsqueda. Un servicio de búsqueda único puede tener un máximo de 6 réplicas. Necesita al menos 2 réplicas para disponibilidad (consultas) de lectura y al menos 3 réplicas para disponibilidad de lectura y escritura (consultas, indización).

Se ejecuta una copia de cada índice en cada réplica. Conforme agrega réplicas, se ponen en línea copias adicionales del índice para admitir mayores cargas de trabajo de consultas y para equilibrar la carga de las solicitudes por las diversas réplicas. Si tiene varios índices, digamos 6, y 3 réplicas, cada réplica tendrá una copia de todos los 6 índices.

Tenga en cuenta que no ofrecemos estimaciones finales sobre consultas por segundo (QPS), ya que la ejecución de consultas puede variar considerablemente según la complejidad de la consulta y las cargas de trabajo competitivas. De media, una réplica puede dar servicio a unas 15 QPS, pero el rendimiento será un algo mayor o menor en función de la complejidad de la consulta (las consultas por facetas son más complejas) y la latencia de red. Además, es importante reconocer que, aunque la adición de réplicas agregará definitivamente escala y rendimiento, el resultado final no será estrictamente lineal: la adición de 3 réplicas no garantiza el triple rendimiento. La latencia de consultas es un indicador de que se pueden necesitar réplicas adicionales.

<a id="chart"></a>
##Combinaciones admitidas de particiones y réplicas

Como se indicó anteriormente, el límite real en particiones y réplicas se basa en la combinación de recursos que seleccione, a la vez que se mantiene dentro del límite de 36 unidades de búsqueda por servicio. Los recursos se asignan en términos de unidades de búsqueda (SU). Un servicio de búsqueda dedicado se inicia con una réplica y una partición, como una unidad de búsqueda.

La capacidad adicional se calcula como particiones multiplicadas por réplicas, lo que produce un número total de unidades de búsqueda necesarias para admitir una configuración determinada.

La tabla siguiente es un gráfico que muestra las réplicas en el eje vertical y las particiones en el eje horizontal. La intersección muestra el número de unidades de búsqueda necesarias para admitir cada combinación, sujetas al límite de 36 unidades de búsqueda (SU) por servicio. Por ejemplo, si quiere 6 réplicas y 2 particiones, esta configuración requeriría 12 unidades de búsqueda. Para usar 4 réplicas y 2 particiones, necesitaría 8 unidades de búsqueda. Como norma general, la mayoría de las aplicaciones de búsqueda tienden a necesitar más réplicas que particiones.

<table cellspacing="0" border="1">
<tr><td><b>6 réplicas</b></td><td>6 unidades de búsqueda</td><td>12 unidades de búsqueda</td><td>18 unidades de búsqueda</td><td>24 unidades de búsqueda</td><td>36 unidades de búsqueda</td><td>N/D</td></tr>
<tr><td><b>5 réplicas</b></td><td>5 unidades de búsqueda</td><td>10 unidades de búsqueda</td><td>15 unidades de búsqueda</td><td>20 unidades de búsqueda</td><td>30 unidades de búsqueda</td><td>N/D</td></tr>
<tr><td><b>4 réplicas</b></td><td>4 unidades de búsqueda</td><td>8 unidades de búsqueda</td><td>12 unidades de búsqueda</td><td>16 unidades de búsqueda</td><td>24 unidades de búsqueda</td><td>N/D </td></tr>
<tr><td><b>3 réplicas</b></td><td>3 unidades de búsqueda</td><td>6 unidades de búsqueda</td><td>9 unidades de búsqueda</td><td>12 unidades de búsqueda</td><td>18 unidades de búsqueda</td><td>36 unidades de búsqueda</td></tr>
<tr><td><b>2 réplicas</b></td><td>2 unidades de búsqueda</td><td>4 unidades de búsqueda</td><td>6 unidades de búsqueda</td><td>8 unidades de búsqueda</td><td>12 unidades de búsqueda</td><td>24 unidades de búsqueda</td></tr>
<tr><td><b>1 réplica</b></td><td>1 unidad de búsqueda</td><td>2 unidades de búsqueda</td><td>3 unidades de búsqueda</td><td>4 unidades de búsqueda</td><td>6 unidades de búsqueda</td><td>12 unidades de búsqueda</td></tr>
<tr><td>N/D</td><td><b>1 partición</b></td><td><b>2 particiones</b></td><td><b>3 particiones</b></td><td><b>4 particiones</b></td><td><b>6 particiones</b></td><td><b>12 particiones</b></td></tr> 
</table>

En el sitio web de Azure se explican con detalle la capacidad, los precios y las unidades de búsqueda. Para obtener más información, vea [Detalles de precios](http://azure.microsoft.com/pricing/details/search/).

> [AZURE.NOTE]El número de particiones que elija debe dividirse en 12 uniformemente (de manera específica, 1, 2, 3, 4, 6, 12). Esto se debe a que la Búsqueda de Azure divide previamente cada índice en 12 particiones para que se pueda repartir entre las particiones. Por ejemplo, si su servicio tiene tres particiones y crea un nuevo índice, cada partición contendrá 4 particiones del índice. La manera en que la Búsqueda de Azure particiona un índice es un detalle de implementación, sujeto a cambios en la futura versión. Aunque el número es 12 hoy, no debe esperar que ese número se siempre 12 en el futuro.

##Elegir una combinación de particiones y de réplicas para alta disponibilidad

Dado que es sencillo y relativamente rápido escalar verticalmente, generalmente se recomienda que comience con una partición y una o dos réplicas, y que después escale verticalmente conforme se crean volúmenes de consulta. Para muchas implementaciones, una partición proporciona suficiente almacenamiento y E/S (en 15 millones de documentos por partición).

Sin embargo, las cargas de trabajo de consultas se basan en las réplicas. Podría requerir réplicas adicionales si necesita más rendimiento o alta disponibilidad.

Las recomendaciones generales para alta disponibilidad son:

- 2 réplicas para alta disponibilidad de cargas de trabajo de solo lectura (consultas)
- 3 o más réplicas para alta disponibilidad de cargas de trabajo de lectura-escritura (consultas e indización)

En la actualidad no hay ningún mecanismo integrado para la recuperación ante desastres. La adición de particiones o réplicas sería la estrategia equivocada para cumplir los objetivos de recuperación ante desastres. En su lugar, podría considerar la adición de redundancia en el nivel de servicio. Para obtener una explicación más profunda de las soluciones alternativas, vea [esta entrada del foro](https://social.msdn.microsoft.com/Forums/ee108a26-00c5-49f6-b1ff-64c66c8b828a/dr-and-high-availability-for-azure-search?forum=azuresearch).

> [AZURE.NOTE]Recuerde que la escalabilidad y los acuerdos de nivel de servicio son características del servicio estándar. El servicio gratuito se ofrece en un nivel de recurso fijo, con las réplicas y las particiones compartidas por varios suscriptores. Si ha iniciado con el servicio gratuito y ahora quiere actualizar, deberá crear un nuevo servicio de Búsqueda de Azure en el nivel estándar y luego volver a cargar los índices y los datos en el nuevo servicio. Vea [Crear un servicio Búsqueda de Azure en el portal](search-create-portal.md) para obtener instrucciones sobre el aprovisionamiento del servicio.

##Límites de la clave de API

Las claves de API se usan para la autenticación del servicio. Hay dos tipos. Las claves de administración se especifican en el encabezado de solicitud. Las claves de consulta se especifican en la dirección URL. Vea [Administración del servicio de búsqueda en Microsoft Azure](search-manage.md) para obtener más información sobre la administración de claves.

- Máximo de 2 claves de administración por servicio
- Máximo de 50 claves de consultas por servicio

##Límites de solicitud

- Máximo de 16 MB por solicitud
- Longitud máxima de dirección URL de 8 KB 
- Máximo de 1000 documentos por lote del índice de cargas de índices, combinaciones o eliminaciones
- Máximo de 32 campos en cláusula $orderby
- El tamaño máximo del término de búsqueda es de 32 766 bytes (32 KB menos 2 bytes) de texto con codificación UTF-8

##Límites de respuestas

- Máximo de 1000 documentos devueltos por página de resultados de búsqueda
- Máximo de 100 sugerencias devueltas por solicitud de Sugerir API

<!---HONumber=Oct15_HO3-->