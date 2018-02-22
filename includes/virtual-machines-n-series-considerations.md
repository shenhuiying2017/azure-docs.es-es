## <a name="deployment-considerations"></a>Consideraciones de la implementación

* Para ver la disponibilidad de máquinas virtuales de la serie N, consulte [Productos disponibles por región](https://azure.microsoft.com/en-us/regions/services/).

* Las máquinas virtuales de la serie N solo se pueden implementar en el modelo de implementación de Resource Manager.

* Las máquinas virtuales de serie N difieren en el tipo de Azure Storage que admiten en sus discos. Las máquinas virtuales NC y NV solo admiten discos de máquina virtual respaldados por Disk Storage (HDD) estándar. Las máquinas virtuales NCv2, ND y NCv3 (versión preliminar) solo admiten discos de máquina virtual respaldados por Disk Storage (SSD) premium.

* Si desea implementar más de un pequeño número de máquinas virtuales de la serie N, considere la posibilidad de usar una suscripción de pago por uso u otras opciones de compra. Si usa una [cuenta gratuita de Azure](https://azure.microsoft.com/free/), solo puede usar un número limitado de núcleos de proceso de Azure.

* Es posible que necesite aumentar la cuota de núcleos (por región) de la suscripción de Azure y la cuota independiente para núcleos NC, NCv2, ND o NV. Para solicitar un aumento de cuota, [abra una solicitud de soporte técnico al cliente en línea](../articles/azure-supportability/how-to-create-azure-support-request.md) sin cargo alguno. Los límites predeterminados pueden variar según la categoría de suscripción.

* Una imagen de máquina virtual que se puede implementar en máquinas virtuales de la serie de N es la [máquina virtual de ciencia de datos de Azure](../articles/machine-learning/data-science-virtual-machine/overview.md). La máquina virtual de ciencia de datos preinstala y configura muchas herramientas de ciencia de datos y aprendizaje profundo conocidas. También preinstala controladores de GPU de NVIDIA Tesla.





