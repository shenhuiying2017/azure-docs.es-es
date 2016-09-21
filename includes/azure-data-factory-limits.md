Data Factory es un servicio multiinquilino que tiene los siguientes límites predeterminados para asegurarse de que las suscripciones de cliente están protegidas frente a las cargas de trabajo de los demás. Se pueden aumentar muchos de los límites para su suscripción fácilmente hasta el límite máximo; para ello, póngase en contacto con el soporte técnico.

**Recurso** | **Límite predeterminado** | **Límite máximo**
-------- | ------------- | -------------
data factories en una suscripción de Azure | 50 | [Ponerse en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
canalizaciones dentro de una factoría de datos | 100 | 2\.500
conjuntos de datos dentro de una factoría de datos | 500 | 5000
fragmentos simultáneos por conjunto de datos | 10 | 10
bytes por objeto para objetos de canalización <sup>1</sup> | 200 KB | 2000 KB
bytes por objeto para objetos de conjunto de datos y de servicio vinculados <sup>1</sup> | 30 KB | 2000 KB
campos por objeto | 100 | [Ponerse en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
bytes por nombre o identificador de campo | 2 KB | [Ponerse en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
bytes por campo | 30 KB | [Ponerse en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Núcleos de clúster a petición de HDInsight con una suscripción <sup>2</sup> | 48 | [Ponerse en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Número de reintentos de ejecuciones de la actividad Canalización | 1000 | MaxInt (32 bits)

<sup>1</sup> Los objetos de canalización, de conjunto de datos y de servicio vinculado representan una agrupación lógica de la carga de trabajo. Los límites de estos objetos no se corresponden relacionados con la cantidad de datos que se pueden mover y procesar con el servicio Factoría de datos de Azure. Factoría de datos está diseñado para poder escalarse para manejar petabytes de datos.

<sup>2</sup>Los núcleos de HDInsight a petición se asignan fuera de la suscripción que contiene Data Factory. En consecuencia, el límite superior es el límite de núcleos aplicados de Data Factory para los núcleos de HDInsight a petición y es diferente del límite de núcleos asociado a su suscripción de Azure.


**Recurso** | **Límite inferior predeterminado** | **Límite mínimo**
-------- | ------------------- | -------------
Intervalo de programación | 15 minutos | 15 minutos
Intervalo entre reintentos | 1 segundo | 1 segundo
Valor de tiempo de espera de reintento | 1 segundo | 1 segundo


### Límites de llamadas de servicio web

El Administrador de recursos de Azure tiene límites para las llamadas API. Se pueden realizar llamadas API a un ritmo que esté dentro de los [límites de la API del Administrador de recursos de Azure](../azure-subscription-service-limits.md#resource-group-limits).

<!---HONumber=AcomDC_0907_2016-->