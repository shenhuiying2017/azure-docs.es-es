# Dirección IP interna estática

Las máquinas virtuales de IaaS (máquinas virtuales) y las instancias de rol de PaaS de una red virtual reciben automáticamente una dirección IP interna de un intervalo que especifique. Esa dirección la conservan las máquinas virtuales y las instancias de rol, hasta que se retiren. Retira una máquina virtual o una instancia de rol deteniéndola desde PowerShell, la CLI de Azure o el portal de Azure. En esos casos, cuando la máquina virtual o la instancia de rol se inicia de nuevo, recibirá una dirección IP disponible de la infraestructura de Azure, que puede que no sea la misma que la que tenía anteriormente. En determinados casos, no desea que una máquina virtual o una instancia de rol tenga una dirección IP dinámica, por ejemplo, si la máquina virtual va a ejecutar DNS o va a ser un controlador de dominio.

>[AZURE.NOTE]Una dirección IP interna dinámica permanece con la máquina virtual si apaga la máquina virtual desde el sistema operativo invitado. Las máquinas virtuales solo se retiran cuando se detienen desde el portal de Azure, PowerShell o la CLI de Azure.

<!---HONumber=Sept15_HO2-->