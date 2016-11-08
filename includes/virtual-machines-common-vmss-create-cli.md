Gracias a los conjuntos de escalado de máquina virtual, podrá administrar varias máquinas virtuales como un conjunto. A nivel general, los conjuntos de escalado presentan las siguientes ventajas y desventajas:

Ventajas:

1. Alta disponibilidad. Cada conjunto de escalado coloca sus máquinas virtuales en un conjunto de disponibilidad con 5 dominios de error (FD) y 5 dominios de actualización (UD) para garantizar la disponibilidad (para obtener más información sobre los dominios de error y dominios de actualización, consulte la [disponibilidad de las máquinas virtuales](../articles/virtual-machines/virtual-machines-linux-manage-availability.md)).
2. Integración sencilla con el Equilibrador de carga y la Puerta de enlace de aplicaciones de Azure.
3. Integración de aplicaciones con el escalado automático de Azure.
4. Implementación, administración y limpieza de máquinas virtuales simplificadas.
5. Compatibilidad con los tipos comunes de Windows y Linux, así como con imágenes personalizadas.

Desventajas:

1. No se puede adjuntar discos de datos a instancias de máquina virtual en un conjunto de escala. En su lugar, debe utilizar Almacenamiento de blobs, Archivos de Azure, tablas de Azure u otra solución de almacenamiento.

## Creación rápida mediante CLI de Azure
Si no lo ha hecho aún, puede obtener una [evaluación gratuita de la suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/) y conectar la [CLI de Azure](../articles/xplat-cli-install.md) [a su cuenta de Azure](../articles/xplat-cli-connect.md). Una vez hecho, puede ejecutar los siguientes comandos para crear rápidamente un conjunto de escalado de máquina virtual:

```bash
# make sure we are in resource manager mode 
azure config mode arm

# quick-create a VM scale set
#
# generic syntax:
# azure vmss quick-create -n VMSS-NAME -g RESOURCE-GROUP-NAME -l LOCATION -u USERNAME -p PASSWORD -C INSTANCE-COUNT -Q IMAGE-URN
#
# example:
azure vmss quick-create -n negatvmss -g negatvmssrg -l westus -u negat -p P4$$w0rd -C 5 -Q Canonical:UbuntuServer:14.04.4-LTS:latest
```

Si desea personalizar la ubicación o image-urn, busque en los comandos `azure location list` y `azure vm image {list-publishers|list-offers|list-skus|list|show}`.

Cuando se haya devuelto este comando, se habrá creado el conjunto de escalado. Este conjunto de escalado tendrá un equilibrador de carga con reglas NAT que asignan el puerto 50 000+ i del equilibrador de carga al puerto 22 de la máquina virtual i. Por lo tanto, una vez que determinemos el FQDN del equilibrador de carga, podremos acceder mediante SSH a nuestras máquinas virtuales:

```bash
# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g nsgvmsrg | grep nsgvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is associated with it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LB-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g nsgvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g nsgvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can ssh on port 50,000+i to ssh into VM i (where i is 0-indexed)
#
# example to ssh into VM "0":
ssh -p 50000 $FQDN
```

## Pasos siguientes
Para obtener información general, consulte la [página de aterrizaje principal de los conjuntos de escalado de máquina virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Para consultar la documentación, consulte la [página de documentación principal de los conjuntos de escalado de máquina virtual](https://azure.microsoft.com/documentation/services/virtual-machines-scale-sets/).

Para las plantillas de Azure Resource Manager de ejemplo que usan conjuntos de escalado de máquina virtual, busque "vmss" en el [repositorio de GitHub de plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates).

<!---HONumber=AcomDC_0406_2016-->