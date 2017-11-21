El almacenamiento está limitado por el espacio en disco o e *número máximo* de índices o documentos, lo que ocurra primero.

| Recurso | Gratuito | Básica | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Contrato de nivel de servicio (SLA) |No <sup>1</sup> |Sí |Sí |Sí |Sí |Sí |
| Almacenamiento por partición |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Particiones por servicio |N/D |1 |12 |12 |12 |3 <sup>2</sup> |
| Tamaño de la partición |N/D |2 GB |25 GB |100 GB |200 GB |200 GB |
| Réplicas |N/D |3 |12 |12 |12 |12 |
| Índices máximos |3 |5 |50 |200 |200 |1000 GB por partición o 3000 por servicio |
| Indexadores máximos |3 |5 |50 |200 |200 |Indexador incompatible |
| Orígenes de datos máximos |3 |5 |50 |200 |200 |Indexador incompatible |
| Número máximo de documentos |10.000 |1 millón |15 millones por partición, 180 millones por servicio |60 millones por partición, 720 millones por servicio |120 millones por partición, 1400 millones por servicio |1 millón por índice, 200 millones por partición |

<sup>1</sup> Las versiones gratuitas y preliminares no incluyen contratos de nivel de servicio (SLA). Para todos los niveles facturables, los SLA tomarán efecto cuando se aprovisione suficiente redundancia para el servicio. Son necesarias dos o más réplicas para el SLA de consulta (lectura). Son necesarias tres o más réplicas para el SLA de consulta e indexación (lectura y escritura). El número de particiones no es una consideración del SLA. 

<sup>2</sup> S3 HD tiene un límite máximo de 3 particiones, que es inferior al límite de partición para S3. El límite inferior de la partición se impone porque el número de índice para S3 HD es mucho más alto. Como existen límites de servicio en ambos recursos informáticos (almacenamiento y procesamiento) y el contenido (índices y documentos), el límite de contenido se alcanza primero.
