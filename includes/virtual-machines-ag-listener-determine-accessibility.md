Es importante saber que hay dos formas de configurar un agente de escucha del grupo de disponibilidad en Azure. Estos métodos se diferencian en el tipo de equilibrador de carga de Azure que utilizas al crear el agente de escucha. La siguiente tabla muestra las diferencias:

| Equilibrador de carga | Implementación | Cuándo utilizarlo |
| ------------- | -------------- | ----------- |
| **Externo** | Usa la **dirección IP Virtual pública** del servicio en la nube que hospeda las máquinas virtuales. | Cuando necesites tener acceso al agente de escucha del exterior de la red virtual, incluso desde Internet. |
| **Interno** | Usa el **Equilibrio de carga interno (ILB)** con una dirección privada del agente de escucha. | Cuando solo accedes al agente de escucha desde la misma red virtual. Esto incluye la VPN de sitio a sitio en escenarios híbridos. |

>[AZURE.IMPORTANT] No se le cobrarán cargos de salida por utilizar un agente de escucha que use la VIP del servicio en la nube (equilibrador de carga externo), siempre y cuando el cliente, el agente de escucha y las bases de datos se encuentren en la misma región de Azure. En caso contrario, los datos que devuelve el agente de escucha se consideran de salida y se cobran según las tarifas de transferencia de datos normales.

El ILB solo se puede configurar en redes virtuales con un ámbito regional. Las redes virtuales existentes que se han configurado para un grupo de afinidad no pueden usar ILB. Para obtener más información, consulta el tema [Equilibrador de carga interno](../articles/load-balancer/load-balancer-internal-overview.md).

