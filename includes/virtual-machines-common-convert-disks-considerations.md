* Si el disco no administrado está en una cuenta de almacenamiento que se cifró previamente mediante el cifrado del servicio Azure Storage, no podrá convertirlo en disco administrado. Para ver los pasos para copiar y usar estos discos duros virtuales en discos administrados, consulte [más adelante en este artículo](#managed-disks-and-azure-storage-service-encryption).

* La conversión requiere reiniciar la máquina virtual, por lo que debe programar la migración de las máquinas virtuales durante una ventana de mantenimiento existente previamente. 

* La conversión no es reversible. 

* Asegúrese de probar la conversión. Migre una máquina virtual de prueba antes de realizar la migración en producción.

* Durante la conversión, se desasigna la VM. La VM recibe una nueva dirección IP cuando se inicia después de la conversión. Si es necesario, puede [asignar una dirección IP estática](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) a la máquina virtual.

* No se eliminan los discos duros virtuales originales ni la cuenta de almacenamiento usada por la máquina virtual antes de la conversión. Seguirán acumulando cargos. Para evitar que se le facture por estos artefactos, elimine los blobs de los discos duros virtuales originales después de comprobar que la conversión esté completa.