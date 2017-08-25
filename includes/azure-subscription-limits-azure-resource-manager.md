| Recurso | Límite predeterminado | Límite máximo |
| --- | --- | --- |
| Máquinas virtuales por [suscripción](../articles/billing-buy-sign-up-azure-subscription.md) |10 000 <sup>1</sup> por región |10 000 por región |
| Total de núcleos de máquina virtual por [suscripción](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por región | Ponerse en contacto con soporte técnico |
| Máquina virtual por núcleos de serie (Dv2, F, etc.) por [suscripción](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por región | Ponerse en contacto con soporte técnico |
| [Coadministradores](../articles/billing-add-change-azure-subscription-administrator.md) por suscripción |Sin límite |Sin límite |
| [Cuentas de almacenamiento](../articles/storage/common/storage-create-storage-account.md) por suscripción |200 |200<sup>2</sup> |
| [Grupos de recursos](../articles/azure-resource-manager/resource-group-overview.md) por suscripción |800 |800 |
| [Conjuntos de disponibilidad](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) por suscripción |2000 por región |2000 por región |
| Lecturas de API del Administrador de recursos |15 000 por hora |15 000 por hora |
| Escrituras de API del Administrador de recursos |1200 por hora |1200 por hora |
| Tamaño de recursos de API de Administrador de recursos |4 194 304 bytes |4 194 304 bytes |
| Etiquetas por suscripción<sup>3</sup> |sin límite |sin límite |
| Cálculos de etiquetas únicas por suscripción<sup>3</sup> | 10.000 | 10.000 |
| [Servicios en la nube](../articles/cloud-services/cloud-services-choose-me.md) por suscripción |No procede<sup>4</sup> |No procede<sup>4</sup> |
| [Grupos de afinidad](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) por suscripción |No procede<sup>4</sup> |No procede<sup>4</sup> |

<sup>1</sup>Los límites predeterminados varían según el tipo de categoría de la oferta, por ejemplo, evaluación gratuita, pago por uso y series, como Dv2, F, G, etc.

<sup>2</sup> Esto incluye las cuentas de almacenamiento Estándar y Premium. Si necesita más de 200 cuentas de almacenamiento, realice una solicitud a través del [servicio de soporte técnico de Azure](https://azure.microsoft.com/support/faq/). El equipo de Azure Storage revisará su caso empresarial y podría aprobar hasta 250 cuentas de almacenamiento.

<sup>3</sup>Puede aplicar un número ilimitado de etiquetas por suscripción. El máximo de etiquetas por recurso o grupo de recursos es 15. Resource Manager solo devuelve una [lista de valores y nombres de etiqueta únicos](/rest/api/resources/tags#Tags_List) para la suscripción cuando el número de etiquetas es 10 000 o menos. Sin embargo, todavía puede encontrar un recurso por la etiqueta cuando el número sea mayor que 10 000.  

<sup>4</sup>Estas características ya no son necesarias con los grupos de recursos de Azure y Azure Resource Manager.

> [!NOTE]
> Es importante destacar que núcleos de la máquina virtual tienen un límite total regional, así como una configuración regional por límite de tamaño de serie (Dv2, F, etc.) que se aplica por separado.  Por ejemplo, considere una suscripción con un límite total de núcleos de máquinas virtuales de Este de EE. UU. de 30, un límite de núcleos de serie A de 30 y un límite de núcleos de serie D de 30.  Esta suscripción podría volver a implementar 30 máquinas virtuales A1, 30 máquinas virtuales D1 o una combinación de las dos, para que no se superen los 30 núcleos (por ejemplo, 10 máquinas virtuales A1 y 20 máquinas virtuales D1).  
> <!-- -->
> 
> 

