Si no lo ha hecho todavía, puede obtener una [evaluación gratuita de susscripción de Azure](https://azure.microsoft.com/pricing/free-trial/) y conectar la [CLI de Azure](../articles/xplat-cli-install.md) [ a su cuenta de Azure](../articles/xplat-cli-connect.md). Asegúrese de que la CLI de Azure está en modo de Resource Manager como se indica a continuación:

```azurecli
azure config mode arm
```

Ahora cree un conjunto de escalado mediante el comando `azure vmss quick-create`. En el ejemplo siguiente se crea un conjunto de escalado de Linux denominado `myVMSS` con 5 instancias de máquina virtual en el grupo de recursos denominado `myResourceGroup`:

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q Canonical:UbuntuServer:16.04.0-LTS:latest
```

En el ejemplo siguiente se crea un conjunto de escalado de Windows con la misma configuración:

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest
```

Si desea personalizar la ubicación o el URN de la imagen, use los comandos `azure location list` y `azure vm image {list-publishers|list-offers|list-skus|list|show}`.

Cuando se haya devuelto este comando, se habrá creado el conjunto de escalado. Este conjunto de escalado tendrá un equilibrador de carga con reglas NAT que asignan el puerto 50 000+ i del equilibrador de carga al puerto 22 de la máquina virtual i. Por lo tanto, una vez que determinemos el FQDN del equilibrador de carga, podremos conectarnos mediante SSH a nuestras máquinas virtuales:

```bash
# (if you decide to run this as a script, please invoke using bash)

# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g negatvmssrg | grep negatvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is 
# associated to it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LOAD-BALANCER-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g negatvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g negatvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can use ssh on port 50,000+i to connect to VM i (where i is 0-indexed)
#
# example to connct via ssh into VM "0":
ssh -p 50000 negat@$FQDN
```

<!--HONumber=Feb17_HO3-->


