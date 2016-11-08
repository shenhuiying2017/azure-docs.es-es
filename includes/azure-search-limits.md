| Recurso | Gratuito | Básico (versión preliminar) <sup>6</sup> | S1 | S2 <sup>7</sup> |
| --- | --- | --- | --- | --- |
| Servicios de búsqueda máximos <sup>8</sup> |1 por suscripción |12 por suscripción |12 por suscripción |1 por suscripción |
| Tamaño máximo de almacenamiento <sup>1</sup> |50 MB o 10 000 documentos |2 GB por servicio |25 GB por partición o 300 GB de documentos por servicio |100 GB por partición o 1,2 TB por servicio |
| Número máximo de documentos hospedados |10 000 en total |1 millón por servicio |15 millones por partición (hasta 180 millones de documentos por servicio) |60 millones por partición (720 millones de documentos por servicio) |
| Índices máximos |3 |5 |50 |200 |
| Indexadores máximos |3 |5 |50 |200 |
| Orígenes de datos máximos de indexador |3 |5 |50 |200 |
| Índice: campos máximos por índice |1000 |100 <sup>5</sup> |1000 |1000 |
| Índice: perfiles de puntuación máximos por índice |16 |16 |16 |16 |
| Índice: funciones máximas por perfil |8 |8 |8 |8 |
| Indexadores: carga máxima de indexación por invocación |10 000 documentos |Limitado solamente por el número máximo de documentos |Limitado solamente por el número máximo de documentos |Limitado solamente por el número máximo de documentos |
| Indexadores: tiempo de ejecución máximo |3 minutos |24 horas |24 horas |24 horas |
| Indexador de blobs: tamaño máximo de blob, MB |16 |16 |128 |256 |
| Indexador de blobs: número máximo de caracteres del contenido extraído de un blob |32 000 |64 000 |4 millones |4 millones |
| Consultas por segundo (QPS) <sup>2</sup> |N/D |~ 3 por réplica |~ 15 por réplica |~ 60 por réplica |
| Escalado horizontal: unidades de búsqueda máximas (SU) <sup>3</sup> |N/D |Hasta 3 unidades (3 réplicas y 1 partición) |36 unidades |36 unidades |
| Precios <sup>4</sup> |N/D |75 USD por SU/mes |250 USD por SU/mes |1000 USD por SU/mes |

<sup>1</sup> El tamaño de almacenamiento es una cantidad fija o el número de documentos por servicio, lo que ocurra primero.

<sup>2</sup> QPS es una aproximación basada en heurística, mediante cargas de trabajo de clientes reales y simuladas para obtener valores estimados. El rendimiento QPS exacto dependerá de los datos y de la naturaleza de la consulta.

<sup>3</sup> Las unidades de búsqueda son la unidad facturable para una réplica o una partición. Es necesario para las operaciones de almacenamiento, indexación y consulta. Consulte en [Planeación de la capacidad en Búsqueda de Azure](../articles/search/search-capacity-planning.md) las combinaciones válidas de réplicas y particiones que le mantienen dentro del límite máximo de 3 o 36 unidades, para Básico y Estándar, respectivamente.

<sup>4</sup> El precio está indicado para el mercado estadounidense, que muestra los costos relativos entre niveles alternativos. Los distintos mercados tienen diferentes precios. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/search/) para conocer las tarifas en otras divisas. La tasa es por unidad de búsqueda (SU). En el nivel de S1, una configuración de tres unidades de búsqueda (por ejemplo, tres réplicas y una partición) costaría 750 dólares al mes como promedio. Si reduce verticalmente a menos SU dentro del mes, la factura se prorrateará para que se le cobre solo por lo que utiliza.

<sup>5</sup> El nivel básico es el único nivel con un límite inferior de 100 campos por índice.

<sup>6</sup> El [nivel Básico](http://aka.ms/azuresearchbasic) está disponible con una tarifa inicial del 50 % del precio total durante el período de vista previa.

<sup>7</sup> S2 requiere un vale de servicio. No se puede aprovisionar en el portal. Póngase en contacto con el servicio de soporte técnico o envíe un correo electrónico a azuresearch_contact@microsoft.com para obtener ayuda.

<sup>8</sup> Puede aumentar el número máximo de servicios por suscripción para todos los niveles, excepto Gratis. Póngase en contacto con el servicio de soporte técnico o envíe un correo electrónico a azuresearch_contact@microsoft.com para obtener ayuda.

<!---HONumber=AcomDC_0608_2016-->