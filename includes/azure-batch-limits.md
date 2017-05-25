| **Recurso** | **Límite predeterminado** | **Límite máximo** |
| --- | --- | --- |
| Cuentas de Lote por región y suscripción | 3 |50 |
| Núcleos dedicados por cuenta de Batch (modo de servicio de Batch)<sup>1</sup> | 20 | N/A<sup>2</sup> |
| Núcleos de baja prioridad por cuenta de Batch (modo de servicio de Batch)<sup>3</sup> | 50 | N/A<sup>4</sup> |
| Trabajos activos y programaciones de trabajos<sup>5</sup> por cuenta de Batch | 20 | 5000<sup>6</sup> |
| Grupos por cuenta de Lote | 20 | 2.500 |

<sup>1</sup> las cuotas de núcleos dedicados mostradas son únicamente para las cuentas con el modo de asignación de grupos establecido en **servicio de Batch**. Para las cuentas con el modo establecido en **suscripción de usuario**, las cuotas de núcleos se basan en la cuota de núcleos de máquina virtual en un nivel regional o por familia de máquinas virtuales de su suscripción.

<sup>2</sup> Se puede aumentar el número de núcleos dedicados por cada cuenta de Batch, pero no se ha especificado el número máximo. Póngase en contacto con el soporte técnico de Azure para analizar las opciones de aumento.

<sup>3</sup> las cuotas de núcleos de baja prioridad mostradas son únicamente para las cuentas con el modo de asignación de grupos establecido en **servicio de Batch**. Los núcleos de baja prioridad no están disponibles para las cuentas con el modo de asignación de grupos establecido en **suscripción de usuario**.

<sup>4</sup> Se puede aumentar el número de núcleos de baja prioridad por cada cuenta de Batch, pero no se ha especificado el número máximo. Póngase en contacto con el soporte técnico de Azure para analizar las opciones de aumento.

<sup>5</sup> No se limitan los trabajos completados ni las programaciones de trabajos.

<sup>6</sup> Póngase en contacto con el soporte técnico de Azure si desea solicitar un aumento más allá de este límite.
