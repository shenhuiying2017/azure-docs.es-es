<properties
 pageTitle="Clúster de Linux RDMA para ejecutar aplicaciones MPI | Microsoft Azure"
 description="Creación de un clúster de Linux de máquinas virtuales de tamaño A8 o A9 para usar RDMA para ejecutar aplicaciones MPI"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="05/09/2016"
 ms.author="danlep"/>

# Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Aprenda cómo configurar un clúster de Linux RDMA en Azure con [máquinas virtuales de tamaño A8 y A9](virtual-machines-linux-a8-a9-a10-a11-specs.md) para ejecutar aplicaciones de interfaz de paso de mensajes (MPI) paralelas. Al configurar un clúster de máquinas virtuales de tamaño A8 y A9 para ejecutar una distribución Linux HPC compatible y una implementación de MPI compatible, las aplicaciones de MPI se comunican eficazmente a través de una red de latencia baja y alto rendimiento en Azure basada en tecnología de acceso directo a memoria remota (RDMA).

>[AZURE.NOTE] Actualmente Azure Linux RDMA es compatible con la versión 5 de Intel MPI Library que se ejecuta en máquinas virtuales de tamaño A8 y A9 creadas a partir de una imagen SUSE Linux Enterprise Server 12 (SLES) o una imagen HPC 6.5 o 7.1 basada en CentOS de Azure Marketplace. Las imágenes de Marketplace de HPC basadas en CentOS instalan la versión MPI Intel 5.1.3.181.



## Opciones de implementación de clúster

A continuación se facilitan métodos que puede usar para crear un clúster de Linux RDMA con o sin un programador de trabajos.

* **HPC Pack**: cree un clúster de Microsoft HPC Pack en Azure y agregue nodos de proceso de tamaño A8 y A9 que ejecuten distribuciones de Linux compatibles para acceder a la red RDMA. Consulte [Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

* **Scripts de CLI de Azure**: como se muestra en los pasos del resto del artículo, use la [Interfaz de la línea de comandos de Azure](../xplat-cli-install.md) (CLI) para crear los scripts de implementación de una red virtual y los demás componentes necesarios para crear un clúster de máquinas virtuales de Linux de tamaño A8 y A9. La CLI en modo de Administración de servicios crea los nodos del clúster en serie en el modelo de implementación clásica, por lo que si va a implementar muchos nodos de proceso, es posible que se tarden varios minutos en completar la implementación.

* **Plantillas de Azure Resource Manager**: use el modelo de implementación de Resource Manager para implementar varias máquinas virtuales de Linux A8 y A9 y defina redes virtuales, direcciones IP estáticas, configuraciones DNS y otros recursos para crear un clúster de proceso que pueda aprovechar la red RDMA y ejecutar cargas de trabajo MPI. También puede [crear su propia plantilla](../resource-group-authoring-templates.md) o consultar [las plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/) para obtener plantillas proporcionadas por Microsoft o la comunidad para implementar la solución que desee. Las plantillas del Administrador de recursos proporcionan una manera rápida y confiable de implementar un clúster de Linux.

## Implementación de ejemplo en el modelo clásico

Los pasos siguientes le ayudarán a usar la CLI de Azure para implementar una máquina virtual de HPC de SUSE Linux Enterprise Server 12 desde Azure Marketplace, instalar la biblioteca MPI Intel y otras personalizaciones, crear una imagen de máquina virtual personalizada y después los script para implementar un clúster de máquinas virtuales A8 o A9.

>[AZURE.TIP]  Siga los mismos pasos para implementar un clúster de máquinas virtuales A8 o A9 basadas en la imagen de HPC 6.5 o 7.1 basada en CentOS en Azure Marketplace. Las diferencias se indican en los pasos. Por ejemplo, dado que las imágenes HPC basadas en CentOS incluyen Intel MPI, es necesario instalar Intel MPI por separado en las máquinas virtuales creadas desde esas imágenes.

### Requisitos previos

*   **Equipo cliente**: necesitará un equipo cliente basado en Mac, Linux o Windows para comunicarse con Azure. Estos pasos se supone que está usado un cliente Linux.

*   **Suscripción de Azure**: si no tiene ninguna, puede crear una [cuenta gratis](https://azure.microsoft.com/free/) en un par de minutos. En los clústeres más grandes, considere la posibilidad de una suscripción de pago por uso u otras opciones de compra.

*   **Cuota de núcleos**: es posible que tenga que aumentar la cuota de núcleos para implementar un clúster de máquinas virtuales A8 o A9. Por ejemplo, necesitará al menos 128 núcleos si desea implementar máquinas virtuales A8 o A9 tal y como se muestra en este artículo. Para aumentar una cuota, [abra una solicitud de soporte técnico al cliente en línea](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sin cargo alguno.

*   **CLI de Azure**: [instale](../xplat-cli-install.md) la CLI de Azure y [conéctela a su suscripción de Azure](../xplat-cli-connect.md) en el equipo cliente.

*   **Intel MPI**: para personalizar una imagen de máquina virtual de HPC de SLE 12 para el clúster (consulte los detalles más adelante en este artículo), descargue e instale el tiempo de ejecución actual de Intel MPI Library 5 desde el sitio [Intel.com](https://software.intel.com/es-ES/intel-mpi-library/). Para prepararse, después de registrarse con Intel, siga el vínculo del correo electrónico de confirmación a la página web relacionada y copie el vínculo de descarga para el archivo .tgz para obtener la versión adecuada de Intel MPI. Este artículo se basa en Intel MPI versión 5.0.3.048.

    >[AZURE.NOTE] Si utiliza la imagen HPC de CentOS 6.5 o CentOS 7.1 en Azure Marketplace para crear los nodos del clúster, se preinstala Intel MPI versión 5.1.3.181 en la máquina virtual.

### Aprovisionar una máquina virtual SLES 12

Después de iniciar sesión en Azure con la CLI de Azure, ejecute `azure config list` para confirmar que la salida muestra el modo de Administración del servicio de Azure. Si no es así, ejecute este comando para establecer el modo:


    azure config mode asm


Escriba lo siguiente para enumerar todas las suscripciones que está autorizado a usar:


    azure account list

La suscripción activa actual se identificará con `Current` establecido en `true`. Si esta no es la suscripción que desea usar para crear el clúster, establezca el número de suscripción adecuado como la suscripción activa:

    azure account set <subscription-number>

Para ver las imágenes de SLES 12 HPC disponibles públicamente en Azure, ejecute un comando similar al siguiente, asumiendo que el entorno de shell admite **grep**:


    azure vm image list | grep "suse.*hpc"

Ahora aprovisione una máquina virtual de tamaño A9 con una imagen de SLES 12 HPC disponible mediante la ejecución de un comando similar al siguiente:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708

donde

* el tamaño (A9 en este ejemplo) puede ser A8 o A9

* el número de puerto externo de SSH (22 en este ejemplo, que es el valor predeterminado de SSH) es cualquier número de puerto válido; el número de puerto interno de SSH se establecerá en 22

* se creará un nuevo servicio en la nube en la región de Azure especificada por la ubicación; especifique una ubicación como "Oeste de EE. UU." en la que estén disponibles las instancias A8 y A9

* el nombre de la imagen de SLES 12 puede ser `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` o `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-priority-v20150708` para que disponga de compatibilidad con SUSE (se aplican cargos)

    >[AZURE.NOTE]Si desea utilizar una imagen HPC basada en CentOS, los nombres de la imagen actual son `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-65-HPC-20160408` y `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-71-HPC-20160408`.

### Personalización de la máquina virtual

Después de que la máquina virtual complete el aprovisionamiento, SSH a la máquina virtual con la dirección IP externa de la máquina virtual (o el nombre DNS) y el número de puerto externo que ha configurado y personalícelo. Para obtener detalles de la conexión, consulte [Inicio de sesión en una máquina virtual con Linux](virtual-machines-linux-classic-log-on.md). Debe ejecutar los comandos como usuario que haya configurado en la máquina virtual, a menos que se requiera el acceso a la raíz para completar un paso.

>[AZURE.IMPORTANT]Microsoft Azure no proporciona acceso a la raíz a máquinas virtuales de Linux. Para obtener acceso administrativo al conectarse como un usuario a la máquina virtual, ejecute los comandos mediante `sudo`.

* **Actualizaciones**: instale las actualizaciones mediante **zypper**. También puede instalar utilidades NFS.

    >[AZURE.IMPORTANT]Si ha implementado un clúster HPC V de SLES 12, en este momento es recomendable no aplicar actualizaciones de kernel, que pueden causar problemas con los controladores de RDMA de Linux.
    >
    >En las imágenes HPC basadas en CentOS desde Marketplace, las actualizaciones del núcleo están deshabilitadas en el archivo de configuración **yum**. Esto se debe a que los controladores Linux RDMA se distribuyen en forma de paquete RPM y sus actualizaciones de estos podrían no funcionar si se actualiza el kernel.

* **Actualizaciones de controladores Linux RDMA**: si implementa una máquina virtual de HPC de SLES 12, tendrá que actualizar los controladores RDMA. Consulte [Acerca del uso de las instancias de proceso intensivo A8, A9, A10 y A11 con Windows](virtual-machines-linux-a8-a9-a10-a11.md#Linux-RDMA-driver-updates-for-SLES-12), para más información.

* **Intel MPI**: si ha implementado una máquina virtual de HPC de SLES 12, descargue e instale el tiempo de ejecución de Intel MPI Library 5 desde el sitio Intel.com mediante la ejecución de comandos similar a la siguiente. Este paso no es necesario si implementa una máquina virtual de HPC basada en CentOS 6.5 o 7.1.

        sudo wget <download link for your registration>

        sudo tar xvzf <tar-file>

        cd <mpi-directory>

        sudo ./install.sh

    Acepte la configuración predeterminada para instalar Intel MPI en la máquina virtual.

* **Bloquear memoria**: para que los códigos de MPI bloqueen la memoria disponible para RDMA, necesitará agregar o cambiar la configuración siguiente en el archivo /etc/security/limits.conf: (Necesitará acceso de raíz para editar este archivo). Si implementa una máquina virtual de HPC basada en CentOS 6.5 o 7.1, la configuración ya se ha agregado a este archivo.

        <User or group name> hard    memlock <memory required for your application in KB>

        <User or group name> soft    memlock <memory required for your application in KB>

    >[AZURE.NOTE]Para realizar pruebas, también puede establecer memlock en ilimitado. Por ejemplo: `<User or group name> hard memlock unlimited.

* **Claves SSH para máquinas virtuales de SLES 12**: genere claves SSH para establecer la confianza para la cuenta de usuario entre todos los nodos de proceso del clúster HPC de SLES 12 al ejecutar trabajos MPI. (Si ha implementado una máquina virtual de HPC basada en CentOS, no siga este paso. Consulte las instrucciones más adelante en este artículo para establecer una relación de confianza de SSH sin contraseña entre los nodos del clúster después de capturar la imagen y de implementar el clúster).

    Ejecute el comando siguiente para crear claves SSH. Presione Entrar para generar las claves en la ubicación predeterminada sin tener que establecer una frase de contraseña.

        ssh-keygen

    Agregue la clave pública al archivo authorized\_keys para las claves públicas conocidas.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    En el directorio ~/.ssh, edite o cree el archivo "config". Proporcione el intervalo de direcciones IP de la red privada que va a usar en Azure (10.32.0.0/16 en este ejemplo):

        host 10.32.0.*
        StrictHostKeyChecking no

    O bien, especifique la dirección IP de red privada de cada máquina virtual en el clúster como sigue:

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]Configurar `StrictHostKeyChecking no` puede crear un riesgo de seguridad si no se especifica una dirección IP específica o un intervalo como se mostró en estos ejemplos.

* **Aplicaciones**: instale todas las aplicaciones que necesite en esta máquina virtual o realice otras personalizaciones antes de capturar la imagen.

### Capturar la imagen

Para capturar la imagen, primero ejecute el comando siguiente en la máquina virtual de Linux: Esto desaprovisiona la máquina virtual, pero mantiene las cuentas de usuario y las claves SSH que configure.

```
sudo waagent -deprovision
```

A continuación, desde el equipo cliente, ejecute los siguientes comandos de CLI de Azure para capturar la imagen. Para obtener más información, consulte [Captura de una máquina virtual clásica con Linux para usarla como imagen](virtual-machines-linux-classic-capture-image.md).

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Después de ejecutar estos comandos, se captura la imagen de máquina virtual para su uso y se elimina la máquina virtual. Ahora tiene la imagen personalizada lista para implementar un clúster.

### Implementar un clúster con la imagen

Modifique el script Bash siguiente con los valores apropiados para su entorno y ejecútelo desde el equipo cliente. Debido a que Azure implementa las máquinas virtuales en serie en el modelo de implementación clásica, tardará unos minutos en implementar las máquinas virtuales A8 y A9 sugeridas en este script.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Select a region where A8 and A9 VMs are available, such as West US
# See Azure Pricing pages for prices and availability of A8 and A9 VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the A8 and A9 instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## Confianza SSH sin contraseña en un clúster de CentOS

Si ha implementado un clúster con una imagen HPC basada en CentOS, existen dos métodos para establecer la confianza entre los nodos de proceso: la autenticación basada en host y la autenticación basada en usuario. La autenticación basada en host está fuera del ámbito de este artículo y por lo general debe realizarse a través de un script de extensión durante la implementación. La autenticación basada en usuario es conveniente para establecer la confianza después de la implementación y requiere la generación y el uso compartido de claves SSH entre los nodos de proceso en el clúster. Esto se suele conocer como inicio de sesión SSH sin contraseña y es necesario al ejecutar trabajos de MPI.

Está disponible un script de ejemplo proporcionado por la comunidad en [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) para habilitar la autenticación de usuario sencilla en un clúster HPC basado en CentOS. Puede descargar y usar este script mediante los siguientes pasos. También puede modificar este script o utilizar cualquier otro método para establecer la autenticación de SSH sin contraseña entre los nodos del clúster de proceso.

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh
    
Para ejecutar el script, debe conocer el prefijo para las direcciones IP de subred. Se puede obtener ejecutando el siguiente comando en uno de los nodos del clúster. El resultado debe ser similar al de 10.1.3.5, y el prefijo es la parte 10.1.3.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Ahora ejecute el script con tres parámetros: el nombre de usuario común en los nodos de proceso, la contraseña común de ese usuario en los nodos de proceso y el prefijo de subred que se devolvió en el comando anterior.


    ./user_authentication.sh <myusername> <mypassword> 10.1.3

El script hace lo siguiente:

* Crea un directorio en el nodo de host de host denominado .ssh, que es necesario para el inicio de sesión sin contraseña.
* Crea un archivo de configuración en el directorio .ssh, que indica el inicio de sesión sin contraseña para permitir el inicio de sesión desde cualquier nodo del clúster.
* Crea archivos que contienen los nombres de nodo y las direcciones IP de nodo para todos los nodos del clúster. Estos archivos se mantienen después de ejecutar el script de referencia por el usuario.
* Crea un par de claves público y privado para cada nodo de clúster, incluido el nodo de host y comparte la información sobre el par de claves, y crea una entrada en el archivo authorized\_keys.

>[AZURE.WARNING]La ejecución de este script puede crear un posible riesgo de seguridad. Asegúrese de que no se distribuye la información de clave pública en ~/.ssh.


## Configurar y ejecutar Intel MPI

Para ejecutar aplicaciones de MPI en Azure Linux RDMA, necesitará configurar ciertas variables de entorno específicas de Intel MPI. A continuación se muestra un script Bash de ejemplo para configurar las variables y ejecutar una aplicación. Cambie la ruta de acceso a mpivars.sh según sea necesario para la instalación de Intel MPI.

```
#!/bin/bash -x

# For a SLES 12 HPC cluster

source /opt/intel/impi_latest/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh


export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

El formato del archivo host es el siguiente. Agregue una línea para cada nodo del clúster. Especifique las direcciones IP privadas de la red virtual definida anteriormente, no los nombres DNS. Por ejemplo, para dos hosts con direcciones IP 10.32.0.1 y 10.32.0.2, el archivo contiene lo siguiente:

```
10.32.0.1:16
10.32.0.2:16
```

## Comprobar un clúster de dos nodos básico después de instalar Intel MPI

Si aún no lo hizo, configure primero el entorno para Intel MPI.

```
# For a SLES 12 HPC cluster

source /opt/intel/impi_latest/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### Ejecución de un comando simple de MPI

Ejecute un comando simple de MPI en uno de los nodos de proceso para mostrar que MPI se instaló correctamente y puede comunicarse entre al menos dos nodos de proceso. El siguiente comando **mpirun** ejecuta el comando **hostname** en los dos nodos.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
La salida debe enumerar los nombres de todos los nodos que se pasaron como entrada para `-hosts`. Por ejemplo, un comando **mpirun** con dos nodos devolverá un resultado similar al siguiente:

```
cluster11
cluster12
```

### Ejecución de una prueba comparativa de MPI

El siguiente comando de Intel MPI comprueba la configuración del clúster y la conexión a la red RDMA mediante el uso de un banco de pruebas de ping-pong.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

Debería ver un resultado similar al siguiente en un clúster de trabajo con dos nodos: En la red RDMA de Azure, se espera una latencia igual o inferior a 3 microsegundos para los tamaños de mensaje de hasta 512 bytes.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## Pasos siguientes

* Intente implementar y ejecutar aplicaciones Linux MPI en el clúster de Linux.

* Consulte la [documentación de la biblioteca de Intel MPI](https://software.intel.com/es-ES/articles/intel-mpi-library-documentation/) para obtener orientación sobre Intel MPI.

* Pruebe una [plantilla de inicio rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) para crear un clúster de Intel Lustre mediante una imagen HPC basada en CentOS.

<!---HONumber=AcomDC_0629_2016-->