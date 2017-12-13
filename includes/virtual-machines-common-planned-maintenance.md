Azure realiza periódicamente actualizaciones para mejorar la confiabilidad, el rendimiento y la seguridad de la infraestructura host de las máquinas virtuales. Estas actualizaciones van desde la aplicación de revisiones a componentes de software en el entorno de hospedaje (sistema operativo, hipervisor y varios agentes implementados en el host), hasta la retirada de hardware, pasando por la actualización de los componentes de red. La mayoría de estas actualizaciones se realizan sin afectar a las máquinas virtuales hospedadas. Sin embargo, hay casos en los que las actualizaciones tienen un impacto:

- Si el mantenimiento no requiere un reinicio, Azure usa la migración en contexto para pausar la máquina virtual mientras se actualiza el host.

- Si el mantenimiento requiere un reinicio, el usuario recibe un aviso de cuándo está programado el mantenimiento. En estos casos, se le asigna también un período de tiempo donde puede iniciar el mantenimiento a la hora que le sea más conveniente.

En esta página se describe cómo Microsoft Azure realiza ambos tipos de mantenimiento. Para más información sobre eventos no planeados (interrupciones), consulte el artículo sobre la administración de la disponibilidad de máquinas virtuales en [Windows] (../articles/virtual-machines/windows/manage-availability.md) o [Linux](../articles/virtual-machines/linux/manage-availability.md).

Las aplicaciones que se ejecutan en una máquina virtual pueden recopilar información sobre próximas actualizaciones mediante el servicio de metadatos de Azure para [Windows](../articles/virtual-machines/windows/instance-metadata-service.md) o [Linux] (../articles/virtual-machines/linux/instance-metadata-service.md).

Para obtener información de procedimientos sobre la administración del mantenimiento planeado, vea "Control de las notificaciones de mantenimiento planeadas" para [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) o [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="in-place-vm-migration"></a>Migración de máquina virtual en contexto

Cuando no es necesario reiniciar después de una actualización, se usa una migración en vivo en contexto. Durante la actualización, la máquina virtual se pausa durante unos 30 segundos, conservando la memoria RAM, mientras el entorno de hospedaje aplica las actualizaciones y las revisiones necesarias. A continuación, se reanuda la máquina virtual y su reloj se sincroniza automáticamente.

En las máquinas virtuales de conjuntos de disponibilidad, los dominios de actualización se actualizan de uno en uno. Todas las máquinas virtuales de un dominio de actualización (UD) se pausan, se actualizan y luego se reanudan antes de que el mantenimiento planeado se mueva al siguiente UD.

Algunas aplicaciones pueden resultar afectadas por estos tipos de actualizaciones. Las aplicaciones que realizan procesamiento de eventos en tiempo real, como transcodificación y streaming multimedia, o los escenarios de redes de alto rendimiento podrían no estar diseñados para tolerar una pausa de 30 segundos. <!-- sooooo, what should they do? --> 


## <a name="maintenance-requiring-a-reboot"></a>Mantenimiento que requiere un reinicio

Cuando las máquinas virtuales deben reiniciarse para el mantenimiento planeado, recibe una notificación de antemano. El mantenimiento planeado tiene dos fases: la ventana de autoservicio y una ventana de mantenimiento programado.

La **ventana de autoservicio** le permite iniciar el mantenimiento en sus máquinas virtuales. Durante este tiempo, puede consultar cada máquina virtual para ver su estado y comprobar el resultado de la última solicitud de mantenimiento.

Cuando se inicia el mantenimiento de autoservicio, la máquina virtual se mueve a un nodo que ya se ha actualizado y, a continuación, lo vuelve a activar. Como la máquina virtual se reinicia, se pierde el disco temporal y se actualizan las direcciones IP dinámicas asociadas con la interfaz de red virtual.

Si inicia el mantenimiento de autoservicio y hay un error durante el proceso, la operación se detiene, la máquina virtual no se actualiza y también se quita de la iteración de mantenimiento planeado. Más adelante se le ofrecerá una nueva programación y una nueva oportunidad de realizar el mantenimiento de autoservicio. 

Cuando ha transcurrido la ventana de autoservicio, comienza la **ventana de mantenimiento programado**. Durante esta ventana de tiempo, puede seguir realizando consultas dentro de la ventana de mantenimiento, pero ya no podrá iniciar el mantenimiento usted mismo.

## <a name="availability-considerations-during-planned-maintenance"></a>Consideraciones sobre disponibilidad durante el mantenimiento planeado 

Si decide esperar a la ventana de mantenimiento programado, hay algunas cosas que debe tener en cuenta para mantener la disponibilidad más elevada de sus máquinas virtuales. 

### <a name="paired-regions"></a>Regiones emparejadas

Cada región de Azure se empareja con otra región de la misma zona geográfica, que juntas forman un par de regiones. Durante el mantenimiento planeado, Azure solo actualiza las máquinas virtuales de una única región de un par de regiones. Por ejemplo, al actualizar las máquinas virtuales de la zona centro-norte de EE. UU., Azure no actualizará las máquinas virtuales de centro-sur de EE. UU. al mismo tiempo. Sin embargo, otras regiones como Europa del Norte pueden estar en mantenimiento al mismo tiempo que el Este de EE. UU. Comprender cómo funcionan los pares de regiones puede ayudar a distribuir mejor las máquinas virtuales entre regiones. Para más información, consulte [Regiones de Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

### <a name="availability-sets-and-scale-sets"></a>Conjuntos de disponibilidad y conjuntos de escalado

Al implementar una carga de trabajo en máquinas virtuales de Azure, puede crear las máquinas virtuales dentro de un conjunto de disponibilidad para proporcionar alta disponibilidad a su aplicación. Esto garantiza que durante un corte de suministro o un evento de mantenimiento, al menos haya una máquina virtual disponible.

En un conjunto de disponibilidad, las máquinas virtuales individuales se reparten entre 20 dominios de actualización (UD) como máximo. Durante el mantenimiento planeado, solo un dominio de actualización se ve afectado en un momento dado. Tenga en cuenta que el orden de los dominios de actualización afectados no siempre ocurre de manera secuencial. 

Los conjuntos de escalado de máquinas virtuales son un recurso informático de Azure que permite implementar y administrar un conjunto de máquinas virtuales idénticas como un recurso único. El conjunto de escalado se implementa automáticamente entre dominios de actualización, como las máquinas virtuales de un conjunto de disponibilidad. Igual que con los conjuntos de disponibilidad, con los conjuntos de escalado solo un dominio de actualización dado resulta afectado en un momento determinado.

Para más información sobre la configuración de máquinas virtuales para ofrecer alta disponibilidad, consulte Administración de la disponibilidad de las máquinas virtuales [Windows](../articles/virtual-machines/windows/manage-availability.md) o [Linux](../articles/virtual-machines/linux/manage-availability.md).
