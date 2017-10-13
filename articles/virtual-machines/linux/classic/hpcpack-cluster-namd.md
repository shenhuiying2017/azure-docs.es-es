---
title: "NAMD con Microsoft HPC Pack en máquinas virtuales Linux | Microsoft Docs"
description: "Implementación de un clúster de Microsoft HPC Pack en Azure y ejecución de una simulación de NAMD con charmrun en varios nodos de proceso de Linux"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 76072c6b-ac35-4729-ba67-0d16f9443bd7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/13/2016
ms.author: danlep
ms.openlocfilehash: 0c0b9875b4153edcc0ec0096577d041d394a842f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Ejecución de NAMD con Microsoft HPC Pack en nodos de proceso de Linux en Azure
Este artículo muestra una manera de ejecutar una carga de trabajo de informática de alto rendimiento (HPC) de Linux en máquinas virtuales de Azure. Aquí configurará un clúster de [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) en Azure con nodos de ejecución de Linux y ejecutará una simulación [NAMD](http://www.ks.uiuc.edu/Research/namd/) para calcular y visualizar la estructura de un sistema biomolecular grande.  

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD** (siglas del programa Nanoscale Molecular Dynamics) es un paquete de dinámica molecular paralelo diseñado para la simulación de alto rendimiento de sistemas biomoleculares grandes que contienen hasta millones de átomos. Algunos ejemplos de estos sistemas son virus, estructuras celulares y proteínas grandes. NAMD se escala a cientos de núcleos en las simulaciones típicas y a más de 500.000 núcleos en las simulaciones de mayor tamaño.
* **Microsoft HPC Pack** proporciona características para ejecutar HPC y aplicaciones paralelas a gran escala, en clústeres de equipos locales o de máquinas virtuales de Azure. HPC Pack, que se desarrolló originalmente como una solución para cargas de trabajo de Windows HPC, ahora admite la ejecución de las aplicaciones HPC Linux en máquinas virtuales de nodos de proceso de Linux implementadas en un clúster de HPC Pack. Consulte [Introducción a los nodos de ejecución de Linux en un clúster de HPC Pack en Azure](hpcpack-cluster.md) para obtener información.

## <a name="prerequisites"></a>Requisitos previos
* **Clúster de HPC Pack con nodos de ejecución de Linux**: implemente un clúster de HPC Pack con nodos de proceso de Linux en Azure utilizando una [plantilla de Azure Resource Manager](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) o un [script de Azure PowerShell](hpcpack-cluster-powershell-script.md). Consulte [Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure](hpcpack-cluster.md) para ver los requisitos previos y los pasos para cada opción. Si elige la opción de implementación de scripts de PowerShell, consulte el archivo de configuración de ejemplo en los archivos de ejemplo al final de este artículo. Este archivo configura un clúster basado en Azure HPC Pack que consta de un nodo principal de Windows Server 2012 R2 y cuatro nodos de ejecución de tamaño grande CentOS 6.6. Adapte este archivo según las necesidades de su entorno.
* **Archivos de software y tutorial NAMD** : descargue el software NAMD para Linux desde el sitio [NAMD](http://www.ks.uiuc.edu/Research/namd/) (hay que registrarse). Este artículo se basa en NAMD versión 2.10 y usa el archivo [Linux-x86_64 (64-bit Intel/AMD con Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310). Descargue también los [archivos del tutorial de NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd). Dado que las descargas son archivos .tar, necesita una herramienta de Windows para extraer los archivos en el nodo principal del clúster. Para extraer los archivos, siga las instrucciones que aparecen más adelante en este artículo. 
* **VMD** (opcional): para ver los resultados del trabajo de NAMD, descargue e instale el programa de visualización molecular [VMD](http://www.ks.uiuc.edu/Research/vmd/) en un equipo de su elección. La versión actual es 1.9.2. Consulte el sitio de descargas de VMD para comenzar.  

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Configuración de la confianza mutua entre nodos de proceso
La ejecución de un trabajo entre nodos en varios nodos de Linux requiere que los nodos confíen unos en otros (a través de **rsh** o **ssh**). Al crear el clúster de HPC Pack con el script de implementación IaaS de Microsoft HPC Pack, el script establece automáticamente una confianza mutua permanente con la cuenta de administrador que especifique. Para los usuarios sin privilegios de administrador que ha creado en el dominio del clúster, tiene que configurar la confianza mutua temporal entre los nodos cuando se les asigna un trabajo. Luego, destruir la relación una vez completado el trabajo. Para hacerlo para cada usuario, proporcione un par de claves RSA al clúster que usa HPC Pack para establecer la relación de confianza. A continuación figuran las instrucciones.

### <a name="generate-an-rsa-key-pair"></a>Generación de un par de claves RSA
Es fácil generar un par de claves RSA, con clave pública y clave privada, mediante la ejecución del comando de Linux **ssh keygen** .

1. Inicie sesión en un equipo con Linux.
2. Ejecute el siguiente comando:
   
   ```bash
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > Presione **Entrar** para usar la configuración predeterminada hasta que se complete el comando. No escriba aquí una frase de contraseña. Cuando se le pida una contraseña, solo tiene que presionar **Entrar**.
   > 
   > 
   
   ![Generación de un par de claves RSA][keygen]
3. Cambie el directorio al directorio ~/.ssh. La clave privada se almacena en id_rsa y la clave pública en id_rsa.pub.
   
   ![Claves públicas y privadas][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Adición del par de claves al clúster de HPC Pack
1. [Conéctese mediante el Escritorio remoto](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a la máquina virtual de nodo principal con las credenciales de dominio proporcionadas cuando implementó el clúster (por ejemplo, hpc\clusteradmin). Usted administra el clúster desde el nodo principal.
2. Use los procedimientos estándar de Windows Server para crear una cuenta de usuario de dominio en el dominio de Active Directory del clúster. Por ejemplo, use la herramienta Usuario y equipos de Active Directory en el nodo principal. En los ejemplos de este artículo se asume que crea un usuario de dominio denominado hpcuser en el dominio hpclab (hpclab\hpcuser).
3. Agregue el usuario de dominio al clúster de HPC Pack como un usuario de clúster. Para obtener instrucciones, consulte [Add or remove cluster users](https://technet.microsoft.com/library/ff919330.aspx)(Adición o eliminación de usuarios de clúster).
4. Cree un archivo denominado C:\cred.xml y copie los datos de la clave RSA en él. Puede encontrar un ejemplo en los archivos de ejemplo, al final de este artículo.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
5. Abra un símbolo del sistema y escriba el siguiente comando para establecer los datos de credenciales para la cuenta hpclab\hpcuser. Use el parámetro **extendeddata** para pasar el nombre del archivo C:\cred.xml que creó para los datos clave.
   
   ```command
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   Este comando se completa correctamente sin salida. Después de establecer las credenciales para las cuentas de usuario que se necesitan para ejecutar los trabajos, guarde el archivo cred.xml en una ubicación segura o elimínelo.
6. Si generó el par de claves RSA en uno de los nodos de Linux, no se olvide de eliminar las claves después de acabar de usarlas. HPC Pack no establece una confianza mutua si encuentra un archivo id_rsa o id_rsa.pub existente.

> [!IMPORTANT]
> No se recomienda ejecutar un trabajo de Linux como administrador de clústeres en un clúster compartido, porque un trabajo enviado por un administrador se ejecuta bajo la cuenta raíz en los nodos de Linux. Un trabajo enviado por un usuario sin privilegios de administrador se ejecuta en una cuenta de usuario de Linux local con el mismo nombre que el usuario de trabajo. En este caso, HPC Pack establece la confianza mutua para este usuario de Linux en todos los nodos asignados al trabajo. Puede configurar el usuario de Linux manualmente en los nodos de Linux antes de ejecutar el trabajo o bien HPC Pack crea el usuario automáticamente cuando se envía el trabajo. Si HPC Pack crea el usuario, HPC Pack lo elimina una vez finalizado el trabajo. Para reducir las amenazas de seguridad, se quitan las claves después de la finalización del trabajo en los nodos.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configuración de un recurso compartido de archivos para nodos de Linux
Ahora, configure un recurso compartido de archivos SMB y monte la carpeta compartida en todos los nodos de Linux para permitir que accedan a los archivos NAMD con una ruta de acceso común. Estos son los pasos para montar una carpeta compartida en el nodo principal. Se recomienda un recurso compartido para distribuciones como CentOS 6.6 que actualmente no es compatible con el servicio de archivos de Azure. Si los nodos de Linux admiten un recurso compartido de archivos de Azure, consulte [Uso de Almacenamiento de archivos de Azure con Linux](../../../storage/files/storage-how-to-use-files-linux.md). Para consultas las opciones de uso compartido de archivos con HPC Pack, consulte [Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure](hpcpack-cluster.md).

1. Cree una carpeta en el nodo principal y compártala con todos los usuarios estableciendo privilegios de lectura y escritura. En este ejemplo,\\\\CentOS66HN\Namd es el nombre de la carpeta, donde CentOS66HN es el nombre de host del nodo principal.
2. Cree una subcarpeta denominada namd2 en la carpeta compartida. En namd2, cree otra subcarpeta denominada namdsample.
3. Extraiga los archivos NAMD de la carpeta mediante una versión de Windows de **tar** o cualquier otra utilidad de Windows que funcione con archivos .tar. 
   
   * Extraiga el archivo tar de NAMD en \\\\CentOS66HN\Namd\namd2.
   * Extraiga los archivos del tutorial en \\\\CentOS66HN\Namd\namd2\namdsample.
4. Abra una ventana de Windows PowerShell y ejecute los siguientes comandos para montar la carpeta compartida en los nodos de Linux.
   
    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

El primer comando crea una carpeta denominada /namd2 en todos los nodos del grupo LinuxNodes. El segundo comando monta la carpeta compartida //CentOS66HN/Namd/namd2 en la carpeta con los bits dir_mode y file_mode establecidos en 777. El *nombre de usuario* y la *contraseña* del comando deben ser las credenciales de un usuario en el nodo principal.

> [!NOTE]
> El símbolo “\`” en el segundo comando es un símbolo de escape de PowerShell. “\`,” significa que “,” (carácter de coma) forma parte del comando.
> 
> 

## <a name="create-a-bash-script-to-run-a-namd-job"></a>Creación de un script de Bash para ejecutar un trabajo NAMD
Su trabajo NAMD necesita un archivo *nodelist* para que **charmrun** determine el número de nodos que usará al iniciar los procesos de NAMD. Use un script de Bash que genere el archivo de lista de nodos y ejecute **charmrun** con este archivo de lista de nodos. Después puede enviar un trabajo NAMD al administrador del clúster de HPC que llama a este script.

Con el editor de texto que prefiera, cree un script de Bash en la carpeta /namd2 que contiene los archivos de programa NAMD y asígnele el nombre "hpccharmrun.sh". Para una rápida de prueba de concepto, copie el script de ejemplo hpccharmrun.sh que se proporciona al final de este artículo y vaya a [Envío de un trabajo NAMD](#submit-a-namd-job).

> [!TIP]
> Guarde el script como un archivo de texto con terminaciones de línea de Linux (solo LF, no CR LF). Esto garantiza que se ejecuta correctamente en los nodos de Linux.
> 
> 

A continuación, encontrará información sobre lo que hace este script de Bash. 

1. Defina algunas variables.
   
   ```bash
   #!/bin/bash
   
   # The path of this script
   SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
   # Charmrun command
   CHARMRUN=${SCRIPT_PATH}/charmrun
   # Argument of ++nodelist
   NODELIST_OPT="++nodelist"
   # Argument of ++p
   NUMPROCESS="+p"
   ```
2. Obtenga información de los nodos de las variables de entorno. $NODESCORES almacena una lista de palabras divididas de CCP_NODES_CORES $. $COUNT es el tamaño de $NODESCORES.
   
   ```
   # Get node information from the environment variables
   NODESCORES=(${CCP_NODES_CORES})
   COUNT=${#NODESCORES[@]}
   ```    
   
   El formato de la variable $CCP_NODES_CORES es el siguiente:
   
   ```
   <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
   ```
   
   Esta variable muestra el número total de nodos, nombres de nodo y número de núcleos de cada nodo que se asignan al trabajo. Por ejemplo, si el trabajo necesita 10 núcleos para ejecutarse, el valor de $CCP_NODES_CORES es similar al siguiente:
   
   ```
   3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
   ```
3. Si no se establece la variable $CCP_NODES_CORES, inicie **charmrun** directamente. (Esto solo debe producirse al ejecutar este script directamente en los nodos de Linux).
   
   ```
   if [ ${COUNT} -eq 0 ]
   then
     # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
     #echo ${CHARMRUN} $*
     ${CHARMRUN} $*
   ```
4. O bien cree un archivo de lista de nodos para **charmrun**.
   
   ```
   else
     # Create the nodelist file
     NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$
   
     # Write the head line
     echo "group main" > ${NODELIST_PATH}
   
     # Get every node name and number of cores and write into the nodelist file
     I=1
     while [ ${I} -lt ${COUNT} ]
     do
         echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
         let "I=${I}+2"
     done
   ```
5. Ejecute **charmrun** con el archivo de lista de nodos, obtenga su estado de retorno y quite el archivo de lista de nodos al final.
   
   ${CCP_NUMCPUS} es otra variable de entorno establecida por el nodo principal de HPC Pack. Almacena el número de núcleos totales asignados a este trabajo. Se usa para especificar el número de procesos de charmrun.
   
   ```
   # Run charmrun with nodelist arg
   #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   
   RTNSTS=$?
   rm -f ${NODELIST_PATH}
   fi
   
   ```
6. Salga con el estado de retorno de **charmrun** .
   
   ```
   exit ${RTNSTS}
   ```

A continuación se indica la información del archivo de lista de nodos, que generará el script:

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Por ejemplo:

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>Submit a NAMD job
Ahora está listo para enviar un trabajo NAMD en el administrador de clústeres de HPC.

1. Conéctese al nodo principal del clúster e inicie el administrador de clústeres de HPC.
2. En **Administración de recursos**, asegúrese de que los nodos de ejecución de Linux están en el estado **En línea**. Si no lo están, selecciónelos y haga clic en **Conectar**.
3. En **Administración de trabajos**, haga clic en **Nuevo trabajo**.
4. Escriba un nombre para el trabajo como *hpccharmrun*.
   
   ![Nuevo trabajo HPC][namd_job]
5. En la página **Detalles del trabajo**, en **Recursos del trabajo**, seleccione el tipo de recurso como **Nodo** y establezca el valor de **Mínimo** en 3. , se ejecuta el trabajo en tres nodos de Linux y cada nodo tiene cuatro núcleos.
   
   ![Recursos del trabajo][job_resources]
6. Haga clic en **Editar tareas** en el panel de navegación izquierdo y, luego, haga clic en **Agregar** para agregar una tarea al trabajo.    
7. En la página **Task Details and I/O Redirection** (Detalles de la tarea y redirección de E/S), establezca los valores siguientes:
   
   * **Línea de comandos**-
     `/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`
     
     > [!TIP]
     > La línea de comandos anterior es un comando único sin saltos de línea. Se ajusta para aparecer en varias líneas de **Línea de comandos**.
     > 
     > 
   * **Directorio de trabajo** - /namd2
   * **Mínimo** - 3
     
     ![Detalles de la tarea][task_details]
     
     > [!NOTE]
     > El directorio de trabajo se establece aquí porque **charmrun** intenta navegar hasta el mismo directorio de trabajo en cada nodo. Si no se configuró el directorio de trabajo, HPC Pack inicia el comando en una carpeta con nombre aleatorio creada en uno de los nodos de Linux. Esto causa el siguiente error en los otros nodos: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` Para evitar este problema, especifique una ruta de carpeta a la que puedan acceder todos los nodos como el directorio de trabajo.
     > 
     > 
8. Haga clic en **Aceptar** y, a continuación, en **Enviar** para ejecutar este trabajo.
   
   De forma predeterminada, HPC Pack envía el trabajo como cuenta del usuario que inició la sesión actual. Un cuadro de diálogo puede pedirle que escriba el nombre de usuario y la contraseña después de hacer clic en **Enviar**.
   
   ![Credenciales del trabajo][creds]
   
   En determinadas condiciones, HPC Pack recuerda la información de usuario que escribió y no muestra este cuadro de diálogo. Para que HPC Pack lo muestre de nuevo, escriba el siguiente comando en el símbolo del sistema y envíe el trabajo.
   
   ```command
   hpccred delcreds
   ```
9. El trabajo tarda varios minutos en finalizar.
10. Encuentre el registro del trabajo en \\<headnodeName>\Namd\namd2\namd2_hpccharmrun.log y los archivos de salida en \\<headnodeName>\Namd\namd2\namdsample\1-2-sphere\.
11. Si lo desea, inicie VMD para ver los resultados del trabajo. Los pasos para visualizar los archivos de salida de NAMD (en este caso, una molécula de proteína ubiquitina en una esfera de agua) quedan fuera del ámbito de este artículo. Consulte el [tutorial de NAMD](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) para obtener más información.
    
    ![Resultados del trabajo][vmd_view]

## <a name="sample-files"></a>Archivos de ejemplo
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Archivo de configuración XML de ejemplo para la implementación de clústeres mediante scripts de PowerShell
```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>    
```

### <a name="sample-credxml-file"></a>Archivo cred.xml de ejemplo
```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```

### <a name="sample-hpccharmrunsh-script"></a>Script hpccharmrun.sh de ejemplo
```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 

<!--Image references-->
[keygen]:media/hpcpack-cluster-namd/keygen.png
[keys]:media/hpcpack-cluster-namd/keys.png
[namd_job]:media/hpcpack-cluster-namd/namd_job.png
[job_resources]:media/hpcpack-cluster-namd/job_resources.png
[creds]:media/hpcpack-cluster-namd/creds.png
[task_details]:media/hpcpack-cluster-namd/task_details.png
[vmd_view]:media/hpcpack-cluster-namd/vmd_view.png
