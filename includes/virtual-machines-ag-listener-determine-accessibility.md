Es importante saber que hay dos formas de configurar un agente de escucha del grupo de disponibilidad en Azure. Estos métodos se diferencian en el tipo de equilibrador de carga de Azure que se usa al crear el agente de escucha. La siguiente tabla muestra las diferencias:

| Tipo de equilibrador de carga | Implementación | Se debe utilizar si: |
| --- | --- | --- |
| **Externo** |Usa la *dirección IP virtual pública* del servicio en la nube que hospeda las máquinas virtuales. |Es necesario tener acceso al agente de escucha desde el exterior de la red virtual, incluso desde Internet. |
| **Interno** |Usa un *equilibrador de carga interno* con una dirección privada para el agente de escucha. |Solo se puede acceder al agente de escucha desde la misma red virtual. Este acceso incluye una VPN de sitio a sitio en escenarios híbridos. |

> [!IMPORTANT]
> No incurrirá en cargos de salida por utilizar un agente de escucha que use la dirección VIP pública del servicio en la nube (equilibrador de carga externo), siempre y cuando el cliente, el agente de escucha y las bases de datos se encuentren en la misma región de Azure. En caso contrario, los datos que devuelve el agente de escucha se consideran de salida y se cobran según las tarifas de transferencia de datos normales. 
> 
> 

Un ILB solo se puede configurar en redes virtuales con un ámbito regional. Las redes virtuales existentes que se han configurado para un grupo de afinidad no pueden usar un ILB. Para más información, consulte [Información general sobre el equilibrador de carga interno](../articles/load-balancer/load-balancer-internal-overview.md).

