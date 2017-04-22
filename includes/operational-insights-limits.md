
>[!NOTE]
>Anteriormente Log Analytics se denominaba Operational Insights.
>
>

Los límites siguientes se aplican a los recursos de Log Analytics por suscripción:

| Recurso | Límite predeterminado | Comentarios
| --- | --- | --- |
| Número de áreas de trabajo gratuitas por suscripción | 10 | Este límite no puede aumentarse. |
| Número de áreas de trabajo de pago por suscripción | N/D | Limitado por el número de recursos de un grupo de recursos y el número de grupos de recursos por suscripción. | 


Los límites siguientes se aplican a cada área de trabajo de Log Analytics:

|  | Gratuito | Estándar | Premium | Independiente | OMS |
| --- | --- | --- | --- | --- | --- |
| Volumen de datos recopilado por día |500 MB<sup>1</sup> |None |None | None | None
| Período de retención de datos |7 días |1 mes |12 meses | 1 mes<sup>2</sup> | 1 mes<sup>2</sup>|

<sup>1</sup> Cuando los clientes alcanzan su límite de transferencia de datos diario de 500 MB, el análisis de datos se detiene y se reanuda al comienzo del día siguiente. Un día se basa en UTC.

<sup>2</sup> Se puede aumentar el período de retención de datos a 730 días para los planes de tarifa Independiente y OMS.

| Categoría | Límites | Comentarios
| --- | --- | --- |
| API de recopilador de datos | El tamaño máximo de una sola publicación es 30 MB<br>El tamaño máximo de los valores de campo es 32 KB | Dividir volúmenes más grandes en varias publicaciones<br>Los campos de más de 32 KB se truncan. |
| API de búsqueda | 5000 registros devueltos para los datos no agregados<br>500 000 registros para los datos agregados | Datos agregados es una búsqueda que incluye el comando `measure`
 
