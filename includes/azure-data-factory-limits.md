Data Factory es un servicio multiinquilino que tiene los siguientes límites predeterminados para asegurarse de que las suscripciones de cliente están protegidas frente a las cargas de trabajo de los demás. Se pueden aumentar muchos de los límites para su suscripción fácilmente hasta el límite máximo; para ello, póngase en contacto con el soporte técnico.

| **Recurso** | **Límite predeterminado** | **Límite máximo** |
| --- | --- | --- |
| data factories en una suscripción de Azure |50 |[Ponerse en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| canalizaciones dentro de una factoría de datos |2.500 |[Ponerse en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| conjuntos de datos dentro de una factoría de datos |5000 |[Ponerse en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| fragmentos simultáneos por conjunto de datos |10 |10 |
| bytes por objeto para objetos de canalización <sup>1</sup> |200 KB |200 KB |
| bytes por objeto para objetos de conjunto de datos y de servicio vinculados <sup>1</sup> |100 KB |2000 KB |
| Núcleos de clúster a petición de HDInsight con una suscripción <sup>2</sup> |60 |[Ponerse en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Unidades de movimiento de datos de nube<sup>3</sup> |32 |[Ponerse en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Número de reintentos de ejecuciones de la actividad Canalización |1000 |MaxInt (32 bits) |

<sup>1</sup> Los objetos de canalización, de conjunto de datos y de servicio vinculado representan una agrupación lógica de la carga de trabajo. Los límites de estos objetos no se corresponden relacionados con la cantidad de datos que se pueden mover y procesar con el servicio Factoría de datos de Azure. Factoría de datos está diseñado para poder escalarse para manejar petabytes de datos.

<sup>2</sup>Los núcleos de HDInsight a petición se asignan fuera de la suscripción que contiene Data Factory. En consecuencia, el límite superior es el límite de núcleos aplicados de Data Factory para los núcleos de HDInsight a petición y es diferente del límite de núcleos asociado a su suscripción de Azure.

<sup>3</sup> La unidad de movimiento de datos de nube (DMU) se usa en una operación de copia de la nube a la nube. Es una medida que representa la eficacia (una combinación de CPU, memoria y asignación de recursos de red) de una única unidad en Data Factory. Poder usar más DMU en algunos escenarios permite lograr una mayor capacidad de procesamiento de copia. Consulte la sección [Unidades de movimiento de datos en la nube](../articles/data-factory/data-factory-copy-activity-performance.md#cloud-data-movement-units) para más información.

| **Recurso** | **Límite inferior predeterminado** | **Límite mínimo** |
| --- | --- | --- |
| Intervalo de programación |15 minutos |15 minutos |
| Intervalo entre reintentos |1 segundo |1 segundo |
| Valor de tiempo de espera de reintento |1 segundo |1 segundo |

### <a name="web-service-call-limits"></a>Límites de llamadas de servicio web
Azure Resource Manager tiene límites para las llamadas de API. Se pueden realizar llamadas API a un ritmo que esté dentro de los [límites de la API del Administrador de recursos de Azure](../articles/azure-subscription-service-limits.md#resource-group-limits).
