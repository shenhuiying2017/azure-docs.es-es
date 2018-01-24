
* La conversión requiere reiniciar la máquina virtual, por lo que debe programar la migración de las máquinas virtuales durante una ventana de mantenimiento existente previamente. 

* La conversión no es reversible. 

* Asegúrese de probar la conversión. Migre una máquina virtual de prueba antes de realizar la migración en producción.

* Durante la conversión, se desasigna la VM. La VM recibe una nueva dirección IP cuando se inicia después de la conversión. Si es necesario, puede [asignar una dirección IP estática](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) a la máquina virtual.

* No se eliminan los discos duros virtuales originales ni la cuenta de almacenamiento usada por la máquina virtual antes de la conversión. Seguirán acumulando cargos. Para evitar que se le facture por estos artefactos, elimine los blobs de los discos duros virtuales originales después de comprobar que la conversión esté completa.

* Revise la versión mínima del agente de máquina virtual de Azure necesario para admitir el proceso de conversión. Para más información acerca de cómo comprobar y actualizar la versión del agente, consulte [Soporte de versión mínima para los agentes de la máquina virtual en Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)