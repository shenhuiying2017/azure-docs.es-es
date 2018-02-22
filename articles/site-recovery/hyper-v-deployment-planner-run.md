---
title: Azure Site Recovery Deployment Planner en el escenario de Hyper-V a Azure | Microsoft Docs
description: "En este artículo se describe el modo de ejecutar Azure Site Recovery Deployment Planner cuando quiere pasar de Hyper-V a Azure."
services: site-recovery
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: nisoneji
ms.openlocfilehash: ae539f136578c6461ef7f680d553fbd76b10ae98
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2018
---
# <a name="run-azure-site-recovery-deployment-planner-for-hyper-v-to-azure"></a>Ejecución de Azure Site Recovery Deployment Planner en el escenario de Hyper-V a Azure

## <a name="modes-of-running-the-deployment-planner"></a>Modos de ejecutar Deployment Planner
Puede ejecutar la herramienta de línea de comandos (ASRDeploymentPlanner.exe) en cualquiera de los cuatro modos siguientes: 
-   [Obtención de la lista de máquinas virtuales (VM)](#get-vm-list-for-profiling-hyper-v-vms)
-   [Perfil](#profile-hyper-v-vms)
-   [Generación de un informe](#generate-report)
-   [Obtención de rendimiento](#get-throughput)

En primer lugar, ejecute la herramienta para obtener la lista de máquinas virtuales de uno o varios hosts de Hyper-V. A continuación, ejecute la herramienta en modo de generación de perfiles para recopilar la actividad de datos y las IOPS de la máquina virtual. Después, ejecute la herramienta para generar el informe para ver los requisitos de ancho de banda de red y de almacenamiento.

## <a name="get-the-vm-list-for-profiling-hyper-v-vms"></a>Obtención de la lista de máquinas virtuales para la generación de perfiles de máquinas virtuales de Hyper-V
En primer lugar, se necesita una lista de las máquinas virtuales cuyo perfil se va a generar. Use el modo GetVMList de la herramienta Deployment Planner para generar la lista de máquinas virtuales presentes en varios hosts de Hyper-V en un solo comando. Una vez que se genera la lista completa, puede quitar del archivo de salida las máquinas virtuales cuyos perfiles no quiere generar. A continuación, use el archivo de salida en todas las operaciones: generación de perfiles, generación de informes y obtención de rendimiento.

Para generar la lista de máquinas virtuales, puede hacer que la herramienta apunte a un clúster de Hyper-V, a un host de Hyper-V o una combinación de todo.

### <a name="command-line-parameters"></a>Parámetros de línea de comandos
La tabla siguiente contiene una lista de parámetros obligatorios y opcionales de la herramienta para ejecutarlos en modo GetVMList. 
```
ASRDeploymentPlanner.exe -Operation GetVMList /?
```
| Nombre de parámetro | DESCRIPCIÓN |
|---|---|
| -Operation | GetVMList |
| -User | El nombre de usuario para conectarse al host de Hyper-V o al clúster de Hyper-V. El usuario necesita tener acceso administrativo.|
|-ServerListFile | El archivo con la lista de servidores que contiene las máquinas virtuales para las que se van a generar perfiles. La ruta de acceso del archivo puede ser absoluta o relativa. Este archivo debe contener uno de los siguientes datos en cada línea:<ul><li>Nombre o dirección IP del host de Hyper-V</li><li>Nombre o dirección IP del clúster de Hyper-V</li></ul><br>**Ejemplo:** El archivo ServerList.txt contiene los siguientes servidores:<ul><li>Host_1</li><li>10.8.59.27</li><li>Cluster_1</li><li>Host_2</li>|
| -Directory|(Opcional) La convención de nomenclatura universal (UNC) o la ruta de acceso del directorio local para almacenar los datos generados durante esta operación. Si no se especifica un nombre, el directorio llamado ProfiledData de la ruta de acceso actual se usa como directorio predeterminado.|
|-OutputFile| (Opcional) El archivo donde se guarda la lista de máquinas virtuales obtenidas de los servidores de Hyper-V especificados. Si no se menciona ningún nombre, los detalles se almacenarán en VMList.txt.  Use el archivo para iniciar la generación de perfiles después de quitar las máquinas virtuales donde no se van a generar perfiles.|
|-Password|(Opcional) La contraseña para conectarse al host de Hyper-V. Si no la especifica como un parámetro, se le pedirá que lo haga al ejecutar el comando.|

### <a name="getvmlist-discovery"></a>Detección de GetVMList
**Clúster de Hyper-V**: cuando se da el nombre del clúster de Hyper-V en el archivo de lista de servidores, la herramienta busca todos los nodos de Hyper-V del clúster y obtiene las máquinas virtuales presentes en cada uno de los host de Hyper-V.

**Host de Hyper-V**: cuando se da el nombre del host de Hyper-V, la herramienta comprueba primero si pertenece a un clúster. En caso afirmativo, la herramienta captura los nodos que pertenecen al clúster. Luego, obtiene las máquinas virtuales de cada host de Hyper-V. 

También puede elegir enumerar en un archivo los nombres descriptivos o las direcciones IP de las máquinas virtuales para las que desea generar perfiles de forma manual.

Abra el archivo de salida en el Bloc de notas y, después, copie en otro archivo los nombres de todas las máquinas virtuales cuyo perfil desea generar (por ejemplo, ProfileVMList.txt). Use un nombre de máquina virtual por línea. Este archivo se usa como entrada para el parámetro -VMListFile de la herramienta en todas las demás operaciones: generación de perfiles, generación de informes y obtención del rendimiento.

### <a name="examples"></a>Ejemplos

#### <a name="store-the-list-of-vms-in-a-file"></a>Almacenamiento de la lista de máquinas virtuales en un archivo
```
ASRDeploymentPlanner.exe -Operation GetVMlist -ServerListFile “E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1 -OutputFile "E:\Hyper-V_ProfiledData\VMListFile.txt"
```

#### <a name="store-the-list-of-vms-at-the-default-location--directory-path"></a>Almacenamiento de la lista de máquinas virtuales en la ubicación predeterminada, es decir, la ruta de acceso de directorio
```
ASRDeploymentPlanner.exe -Operation GetVMList -Directory "E:\Hyper-V_ProfiledData" -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1
```

## <a name="profile-hyper-v-vms"></a>Generación de perfiles de máquinas virtuales de Hyper-V
En el modo de generación de perfiles, la herramienta Deployment Planner conecta con cada uno de los hosts de Hyper-V para recopilar datos de rendimiento sobre las máquinas virtuales. 

La generación de perfiles no afecta al rendimiento de las máquinas virtuales de producción porque no se realiza ninguna conexión directa con ellas. Todos los datos de rendimiento se recopilan del host de Hyper-V.

La herramienta consulta el host de Hyper-V una vez cada 15 segundos, para garantizar la precisión de la generación de perfiles. Permite almacenar la media de los datos del contador de rendimiento cada minuto.

La herramienta administra sin problemas la migración de las máquinas virtuales de un nodo a otro nodo del clúster y la migración del almacenamiento dentro de un host.

### <a name="getting-the-vm-list-to-profile"></a>Obtención de la lista de máquinas virtuales para la generación de perfiles
Consulte la operación [GetVMList](#get-vm-list-for-profiling-hyper-v-vms) para crear una lista de máquinas virtuales para la generación de perfiles.

Una vez que tenga la lista de máquinas virtuales cuyo perfil se va a generar, puede ejecutar la herramienta en modo de generación de perfiles. 

### <a name="command-line-parameters"></a>Parámetros de línea de comandos
La tabla siguiente enumera los parámetros opcionales y obligatorios de la herramienta para ejecutar en el modo de generación de perfiles. La herramienta es habitual en los escenarios de cambio de VMware a Azure y de Hyper-V a Azure. Estos parámetros son aplicables a Hyper-V. 
```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```
| Nombre de parámetro | DESCRIPCIÓN |
|---|---|
| -Operation | Inicio de la generación de perfiles |
| -User | El nombre de usuario para conectarse al host de Hyper-V o al clúster de Hyper-V. El usuario necesita tener acceso administrativo.|
| -VMListFile | El archivo con la lista de máquinas virtuales para las que se va a realizar la generación de perfiles. La ruta de acceso del archivo puede ser absoluta o relativa. En Hyper-V, este archivo es el archivo de salida de la operación GetVMList. Si va a realizar la preparación de forma manual, el archivo debe contener un nombre de servidor o una dirección IP seguidos de un nombre de máquina virtual, (separados por una barra diagonal inversa "\" por línea). El nombre de la máquina virtual especificado en el archivo debe ser el mismo que el nombre de la máquina virtual del host de Hyper-V.<br><br>**Ejemplo:** El archivo VMList.txt contiene las siguientes máquinas virtuales:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-NoOfMinutesToProfile|Número de minutos durante los cuales se va a ejecutar la generación de perfiles. El mínimo es 30 minutos.|
|-NoOfHoursToProfile|Número de horas durante las cuales se va a ejecutar la generación de perfiles.|
|-NoOfDaysToProfile |Número de días durante los cuales se va a ejecutar la generación de perfiles. Se recomienda ejecutar la generación de perfiles durante más de 7 días. Esa duración sirve para asegurarse de que el patrón de carga de trabajo en su entorno durante el período especificado se observa y se usa para proporcionar una recomendación adecuada.|
|-Virtualization|El tipo de virtualización (VMware o Hyper-V).|
|-Directory|(Opcional) UNC o ruta de acceso del directorio local para almacenar los datos de generación de perfiles que se han producido durante esta. Si no se especifica un nombre, el directorio llamado "ProfiledData" de la ruta de acceso actual se usa como el directorio predeterminado.|
|-Password|(Opcional) La contraseña para conectarse al host de Hyper-V. Si no la especifica como un parámetro, se le pedirá que lo haga al ejecutar el comando.|
|-StorageAccountName|(Opcional) El nombre de la cuenta de almacenamiento que se usa para ver el rendimiento que se puede obtener en la replicación de datos desde una ubicación local a Azure. La herramienta carga los datos de prueba en esta cuenta de almacenamiento para calcular el rendimiento. La cuenta de almacenamiento debe ser v1 de uso general o v2 de uso general.|
|-StorageAccountKey|(Opcional) La clave que se utiliza para acceder a esa cuenta. Vaya a Azure Portal > **Cuentas de almacenamiento** > *Nombre de cuenta de almacenamiento* > **Configuración** > **Claves de acceso** > **Key1** (o una clave de acceso principal para una cuenta de almacenamiento clásico).|
|-Environment|(Opcional) Su entorno de destino para la cuenta de Azure Storage. Puede ser uno de estos tres valores: AzureCloud, AzureUSGovernment y AzureChinaCloud. El valor predeterminado es AzureCloud. Use el parámetro si la región de destino se corresponde con Azure US Government o Azure China.|

Se recomienda generar perfiles de las máquinas virtuales durante más de 7 días. Si el patrón de actividad varía en un mes, se recomienda que realice la generación de perfiles durante la semana en que contemple la actividad máxima. La mejor manera es generar perfiles durante 31 días para obtener la mejor recomendación. 

Durante el período de generación de perfiles ASRDeploymentPlanner.exe sigue ejecutándose. La herramienta admite la entrada de tiempo de generación de perfiles en días. Para realizar una prueba rápida de la herramienta o una prueba de concepto, puede generar perfiles durante algunas horas o minutos. El tiempo mínimo de generación de perfiles permitido es de 30 minutos. 

Durante la generación de perfiles, también se pueden pasar un nombre y una clave de cuenta de almacenamiento para hallar el rendimiento que puede lograr Azure Site Recovery en el momento de la replicación desde el servidor de Hyper-V a Azure. Si el nombre y la clave de la cuenta de almacenamiento no se pasan durante la generación de perfiles, la herramienta no calculará el rendimiento que se puede obtener.

Puede ejecutar varias instancias de la herramienta para varios conjuntos de máquinas virtuales. Asegúrese de que los nombres de máquina virtual no se repiten en ninguno de los conjuntos de generación de perfiles. Por ejemplo, supongamos que ha generado los perfiles de 10 máquinas virtuales (de VM1 a VM10). Después de varios días, desea generar los perfiles de otras 5 máquinas virtuales (de VM11 a VM15). Puede ejecutar la herramienta desde otra consola de la línea de comandos para el segundo conjunto de máquinas virtuales (de VM11 a VM15). 

Asegúrese de que el segundo conjunto de máquinas virtuales no contiene ningún nombre de máquina virtual de la primera instancia de generación de perfiles o de que usa un directorio de salida diferente para la segunda ejecución. Si se usan dos instancias de la herramienta para generar los perfiles de las mismas máquinas virtuales y se usa el mismo directorio de salida, el informe generado no será correcto. 

De forma predeterminada, la herramienta está configurada tanto para generar perfiles como para generar informes de hasta 1000 máquinas virtuales. Para cambiar el límite, cambie el valor de la clave MaxVMsSupported en el archivo ASRDeploymentPlanner.exe.config.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Con la configuración predeterminada, para generar perfiles de 1500 máquinas virtuales (por ejemplo), cree dos archivos VMList.txt. Uno tiene 1000 máquinas virtuales y el otro tiene 500. Ejecute las dos instancias de Azure Site Recovery Deployment Planner, una con VMList1.txt y la otra con VMList2.txt. Puede usar la misma ruta de acceso de directorio para almacenar los datos de los perfiles de las máquinas virtuales de ambos VMList. 

En función de la configuración de hardware del servidor (sobre todo el tamaño de la RAM) desde el que se ejecuta la herramienta para generar el informe, la operación puede no completarse debido a que la memoria no es suficiente. Si el hardware es bueno, puede asignar cualquier valor MaxVMsSupported, por grande que sea.  

Las configuraciones de las máquinas virtuales se capturan una vez al principio de la operación de generación de perfiles y se almacena en un archivo denominado VMDetailList.xml. Esta información se usa cuando se genera el informe. Los cambios en la configuración de una máquina virtual (por ejemplo, un mayor número de núcleos, discos o NIC) que se realizan desde el principio hasta el final de la generación de perfiles no se capturan. Si una configuración de máquina virtual para la que se han generado perfiles ha cambiado durante esta operación, esta es la solución alternativa para obtener los detalles más recientes de la máquina virtual al generar el informe:

* Realice una copia de seguridad de VMdetailList.xml y elimine el archivo de su ubicación actual.
* Pase los argumentos -User y -Password en el momento de la generación de informes.

El comando de generación de perfiles genera varios archivos en el directorio de generación de perfiles. No elimine ninguno de los archivos, ya que la generación de informes puede resultar afectada.

### <a name="examples"></a>Ejemplos

#### <a name="profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Generación de perfiles de máquinas virtuales durante 30 días y búsqueda del rendimiento desde una ubicación local en Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile 30 -User Contoso\HyperVUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="profile-vms-for-15-days"></a>Generación de perfiles de máquinas virtuales durante 15 días
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  15  -User contoso\HypreVUser1
```

#### <a name="profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Generación de perfiles de máquinas virtuales durante 60 minutos para realizar una prueba rápida de la herramienta
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60 -User Contoso\HyperVUser1
```

#### <a name="profile-vms-for-2-hours-for-a-proof-of-concept"></a>Generación de perfiles de máquinas virtuales durante 2 horas para una prueba de concepto
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfHoursToProfile 2 -User Contoso\HyperVUser1
```

### <a name="considerations-for-profiling"></a>Consideraciones para la generación de perfiles

Si el servidor en el que se ejecuta la herramienta se reinicia o se ha bloqueado, o si cierra la herramienta con Ctrl + C, se conservan los datos de los perfiles generados. Sin embargo, es posible que falten los últimos 15 minutos de los datos de los perfiles generados. En tales casos, vuelva a ejecutar la herramienta en modo de generación de perfiles después de que se reinicie el servidor.

Cuando se pasan el nombre y la clave de la cuenta de almacenamiento, la herramienta mide el rendimiento en el último paso de la generación de perfiles. Si la herramienta se cierra antes de que se complete la generación de perfiles, no se calcula el rendimiento. Para hallar el rendimiento antes de generar el informe, puede ejecutar la operación de GetThroughput desde la consola de línea de comandos. Si no lo hace, el informe generado no contendrá la información de rendimiento.

Azure Site Recovery no es compatible con máquinas virtuales que tengan discos iSCSI y de acceso directo. Sin embargo, la herramienta no puede detectar ni generar perfiles de discos iSCSI y de acceso directo que estén asociados a máquinas virtuales.

## <a name="generate-a-report"></a>Generación de un informe
La herramienta genera un archivo de Microsoft Excel habilitado para macros (archivo XLSM) como la salida del informe. Este resume todas las recomendaciones de implementación. El informe se denomina DeploymentPlannerReport_*identificador numérico único*.xlsm y se coloca en el directorio especificado.

Una vez que se completa la generación de perfiles, se puede ejecutar la herramienta en modo de generación de informes. 

### <a name="command-line-parameters"></a>Parámetros de línea de comandos
La siguiente tabla contiene una lista de los parámetros obligatorios y opcionales de la herramienta que se ejecutan en modo de generación de informes. La herramienta es habitual en los escenarios de cambio de VMware a Azure y de Hyper-V a Azure. Los parámetros siguientes son aplicables a Hyper-V.
```
ASRDeploymentPlanner.exe -Operation GenerateReport /?
```
| Nombre de parámetro | DESCRIPCIÓN |
|---|---|
| -Operation | GenerateReport |
|-VMListFile | El archivo que contiene la lista de máquinas virtuales con perfiles para las que se va a generar el informe. La ruta de acceso del archivo puede ser absoluta o relativa. En Hyper-V, este archivo es el archivo de salida de la operación GetVMList. Si va a realizar la preparación de forma manual, el archivo debe contener un nombre de servidor o una dirección IP seguidos de un nombre de máquina virtual, (separados por una barra diagonal inversa "\" por línea). El nombre de la máquina virtual especificado en el archivo debe ser el mismo que el nombre de la máquina virtual del host de Hyper-V.<br><br>**Ejemplo:** El archivo VMList.txt contiene las siguientes máquinas virtuales:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Virtualization|El tipo de virtualización (VMware o Hyper-V).|
|-Directory|(Opcional) El UNC o la ruta de acceso del directorio local en que se almacenan los datos de la generación de perfiles (los archivos que se crean en la generación de perfiles). Estos datos son necesarios para generar el informe. Si no se especifica un nombre, el directorio llamado "ProfiledData" de la ruta de acceso actual se usa como el directorio predeterminado.|
| -User | (Opcional) El nombre de usuario para conectarse al host de Hyper-V o al clúster de Hyper-V. El usuario necesita tener acceso administrativo. El usuario y la contraseña se usan para recuperar los cambios más recientes en la información de configuración de las máquinas virtuales (como el número de discos, el número de núcleos o el número de tarjetas NIC) que se usarán en el informe. Si no se proporciona, se usa la información de configuración recopilada durante la generación de perfiles.|
|-Password|(Opcional) La contraseña para conectarse al host de Hyper-V. Si no la especifica como un parámetro, se le pedirá que lo haga al ejecutar el comando.|
| -DesiredRPO | (Opcional) El objetivo del punto de recuperación deseado (RPO), en minutos. El valor predeterminado es 15 minutos.|
| -Bandwidth | (Opcional) El ancho de banda en megabits por segundo. Use este parámetro para calcular el RPO que se puede lograr para el ancho de banda especificado. |
| -StartDate | (Opcional) La fecha y hora de inicio en DD-MM-AAAA:HH:MM (formato de 24 horas). StartDate se debe especificar junto con EndDate. Cuando se especifica StartDate, se genera el informe de los datos de generación de perfiles que se recopilan entre StartDate y EndDate. |
| -EndDate | (Opcional) La fecha y hora de finalización en DD-MM-AAAA:HH:MM (formato de 24 horas). EndDate se debe especificar junto con StartDate. Cuando se especifica EndDate, se genera el informe para los datos de la generación de perfiles recopilados entre StartDate y EndDate. |
| -GrowthFactor | (Opcional) El factor de crecimiento, expresado en forma de porcentaje. El valor predeterminado es 30 %. |
| -UseManagedDisks | (Opcional) UseManagedDisks: Yes/No. El valor predeterminado es "yes" (sí). El cálculo del número de máquinas virtuales que se pueden colocar en una única cuenta de almacenamiento se realiza teniendo en cuenta si se ha realizado la conmutación por error o la conmutación por error de prueba en discos administrados y no en discos sin administrar. |
|-SubscriptionId |(Opcional) El GUID de la suscripción. Use este parámetro para generar el informe de estimación de costos con el precio más reciente según su suscripción, la oferta asociada a la suscripción y la región de Azure de destino concreta en la moneda especificada.|
|-TargetRegion|(Opcional) La región de Azure que es el destino de la replicación. Como Azure tiene diferentes costos por región, para generar un informe con la región de Azure de destino específica, use este parámetro. El valor predeterminado es WestUS2 o la región de destino usada por última vez. Consulte la lista de [regiones de destino admitidas](hyper-v-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Opcional) La oferta asociada a la suscripción. El valor predeterminado es MS-AZR-0003P (pago por uso).|
|-Currency|(Opcional) La moneda en la que se muestra el costo en el informe generado. El valor predeterminado es el dólar estadounidense ($) o la moneda usada por última vez. Consulte la lista de [monedas admitidas](hyper-v-deployment-planner-cost-estimation.md#supported-currencies).|

De forma predeterminada, la herramienta está configurada tanto para generar perfiles como para generar informes de hasta 1000 máquinas virtuales. Para cambiar el límite, cambie el valor de la clave MaxVMsSupported en el archivo ASRDeploymentPlanner.exe.config.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```

### <a name="examples"></a>Ejemplos
#### <a name="generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Generación de un informe con los valores predeterminados cuando los datos de generación de perfiles están en la unidad local
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”
```

#### <a name="generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Generación de un informe cuando los datos de generación de perfiles están en un servidor remoto
El usuario debe tener acceso de lectura y escritura en el directorio remoto.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “\\PS1-W2K12R2\Hyper-V_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="generate-a-report-with-a-specific-bandwidth-that-you-will-provision-for-the-replication"></a>Generación de un informe con un ancho de banda específico que se aprovisionará en la replicación
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -Bandwidth 100
```

#### <a name="generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Generación de un informe con un factor de crecimiento del 5 %, en lugar del valor predeterminado del 30 % 
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="generate-a-report-with-a-subset-of-profiled-data"></a>Generación de un informe con un subconjunto de datos de la generación de perfiles
Por ejemplo, tiene 30 días de datos de generación de perfiles y desea generar un informe de solo 20 días.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="generate-a-report-for-a-5-minute-rpo"></a>Generación de un informe para un RPO de 5 minutos
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="generate-a-report-for-the-south-india-azure-region-with-indian-rupee-and-a-specific-offer-id"></a>Generación de un informe para la región de Azure de India del Sur con la rupia india y un identificador de oferta específico
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```


### <a name="percentile-value-used-for-the-calculation"></a>Valor del percentil usado para el cálculo
Cuando la herramienta genera un informe, de manera predeterminada su valor de percentil es 95 para las IOPS de lectura y escritura, las IOPS de escritura y la actividad de datos. Estos valores se recopilan durante la generación de perfiles de todas las máquinas virtuales. Esta métrica garantiza que el pico del percentil 100 que pueden ver las máquinas virtuales debido a eventos temporales no se utiliza para determinar los requisitos de la cuenta de almacenamiento de destino y de ancho de banda de origen. Por ejemplo, un evento temporal podría ser un trabajo de copia de seguridad que se ejecuta una vez al día, una actividad periódica de indexación de base de datos o de generación de informes de análisis, u otros eventos de corta duración que se producen en un momento dado.

El uso de un valor del percentil 95 ofrece una imagen real de las verdaderas características de las cargas de trabajo y ofrece el mejor rendimiento cuando dichas cargas se ejecutan en Azure. No se espera que haga falta cambiar este número. Si cambia el valor (por ejemplo, al percentil 90), puede actualizar el archivo de configuración ASRDeploymentPlanner.exe.config en la carpeta predeterminada y guardarlo para generar un nuevo informe sobre los datos de generación de perfiles existentes.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

### <a name="considerations-for-growth-factor"></a>Consideraciones sobre el factor de crecimiento
Es fundamental tener en cuenta el crecimiento en las características de la carga de trabajo y suponer que el uso puede aumentar con el tiempo. Una vez instaurada la protección, si las características de la carga de trabajo cambian, no podrá cambiar a otra cuenta de almacenamiento sin tener que deshabilitar y volver a habilitar la protección.

Por ejemplo, supongamos que en la actualidad la máquina virtual se ajusta a una cuenta de replicación de almacenamiento estándar. En los tres próximos meses, es probable que se produzcan estos cambios:

1. Aumentará el número de usuarios de la aplicación que se ejecuta en la máquina virtual.
2. El aumento de actividad resultante en la máquina virtual requerirá que la máquina virtual use Premium Storage para que la replicación de Azure Site Recovery pueda mantener el ritmo.
3. En consecuencia, tendrá que deshabilitar y volver a habilitar la protección en una cuenta de Premium Storage.

Se recomienda encarecidamente que planee el crecimiento durante el planeamiento de la implementación. Aunque el valor predeterminado es el 30 por ciento, usted es el experto en patrones de uso de las aplicaciones y proyecciones de crecimiento. Puede cambiar este número en consecuencia mientras genera un informe. Además, puede generar varios informes con varios factores de crecimiento con los mismos datos de generación de perfiles. A continuación, puede determinar qué almacenamiento de destino y recomendaciones de ancho de banda de origen funcionan mejor en su caso. 

El informe de Microsoft Excel generado contiene la siguiente información:

* [Resumen local](hyper-v-deployment-planner-analyze-report.md#on-premises-summary)
* [Recomendaciones](hyper-v-deployment-planner-analyze-report.md#recommendations)
* [Selección de ubicación de almacenamiento](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
* [VM compatibles](hyper-v-deployment-planner-analyze-report.md#compatible-vms)
* [VM incompatibles](hyper-v-deployment-planner-analyze-report.md#incompatible-vms)
* [Requisito de almacenamiento local](hyper-v-deployment-planner-analyze-report.md#on-premises-storage-requirement)
* [Procesamiento por lotes de IR](hyper-v-deployment-planner-analyze-report.md#initial-replication-batching)
* [Estimación de costos](hyper-v-deployment-planner-cost-estimation.md)

![Informe de Deployment Planner](media/hyper-v-deployment-planner-run/deployment-planner-report-h2a.png)


## <a name="get-throughput"></a>Obtención de rendimiento
Para calcular el rendimiento que Azure Site Recovery puede lograr durante la replicación desde una ubicación local a Azure, ejecute la herramienta en modo de GetThroughput. La herramienta calcula el rendimiento desde el servidor en el que se ejecuta la herramienta. Idealmente, este servidor es el servidor de Hyper-V cuyas máquinas virtuales se van a proteger. 

### <a name="command-line-parameters"></a>Parámetros de línea de comandos 
Abra una consola de línea de comandos y vaya a la carpeta de la herramienta Azure Site Recovery Deployment Planner. Ejecute ASRDeploymentPlanner.exe con los siguientes parámetros.
```
ASRDeploymentPlanner.exe -Operation GetThroughput /?
```
 Nombre de parámetro | DESCRIPCIÓN |
|---|---|
| -Operation | GetThroughput |
|-Virtualization|El tipo de virtualización (VMware o Hyper-V).|
|-Directory|(Opcional) El UNC o la ruta de acceso del directorio local en que se almacenan los datos de la generación de perfiles (los archivos que se crean en la generación de perfiles). Estos datos son necesarios para generar el informe. Si no se especifica un nombre, el directorio llamado "ProfiledData" de la ruta de acceso actual se usa como el directorio predeterminado.|
| -StorageAccountName | El nombre de la cuenta de almacenamiento que se usa para hallar el ancho de banda consumido durante la replicación de datos desde una ubicación local a Azure. La herramienta carga los datos de prueba en esta cuenta de almacenamiento para calcular el ancho de banda consumido. La cuenta de almacenamiento debe ser v1 de uso general o v2 de uso general.|
| -StorageAccountKey | La clave de la cuenta de almacenamiento utilizada para acceder a dicha cuenta. Vaya a Azure Portal > **Cuentas de almacenamiento** > *nombre de la cuenta de almacenamiento* > **Configuración** > **Claves de acceso** > **Key1**.|
| -VMListFile | El archivo que contiene la lista de máquinas virtuales de las que se va a generar el perfil para calcular el ancho de banda consumido. La ruta de acceso del archivo puede ser absoluta o relativa. En Hyper-V, este archivo es el archivo de salida de la operación GetVMList. Si va a realizar la preparación de forma manual, el archivo debe contener un nombre de servidor o una dirección IP seguidos de un nombre de máquina virtual, (separados por una barra diagonal inversa "\" por línea). El nombre de la máquina virtual especificado en el archivo debe ser el mismo que el nombre de la máquina virtual del host de Hyper-V.<br><br>**Ejemplo:** El archivo VMList.txt contiene las siguientes máquinas virtuales:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Environment|(Opcional) Su entorno de destino para la cuenta de Azure Storage. Puede ser uno de estos tres valores: AzureCloud, AzureUSGovernment y AzureChinaCloud. El valor predeterminado es AzureCloud. Use el parámetro si la región de Azure de destino se corresponde con Azure US Government o Azure China.|

### <a name="example"></a>Ejemplo
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Virtualization Hyper-V -Directory E:\Hyp-erV_ProfiledData -VMListFile E:\Hyper-V_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

### <a name="throughput-considerations"></a>Consideraciones de rendimiento

La herramienta crea varios archivos llamados asrvhdfile*number*.vhd de 64 MB (donde *number* es el número de archivos) en el directorio especificado. La herramienta carga los archivos en la cuenta de almacenamiento para hallar el rendimiento. Después de medir el rendimiento, la herramienta elimina todos estos archivos de la cuenta de almacenamiento y del servidor local. Si la herramienta se termina por cualquier motivo mientras calcula el rendimiento, no elimina los archivos de la cuenta de almacenamiento ni del servidor local. Tendrá que eliminarlos manualmente.

El rendimiento se mide en un momento determinado del tiempo. Es el rendimiento máximo que puede lograr Azure Site Recovery durante la replicación, si todos los demás factores son los mismos. Por ejemplo, si alguna aplicación empieza a consumir más ancho de banda en la misma red, el rendimiento real variará durante la replicación. El resultado de rendimiento medido es distinto si la operación de GetThroughput se ejecuta cuando las máquinas virtuales protegidas tienen una actividad de datos alta. 

Se recomienda ejecutar la herramienta en varios momentos de la generación de perfiles para saber qué niveles de rendimiento se pueden lograr en cada uno de ellos. En el informe, la herramienta muestra el último rendimiento medido.

> [!NOTE]
> Ejecute la herramienta en un servidor que tenga las mismas características de almacenamiento y CPU que el servidor de Hyper-V.

En la replicación, establezca el ancho de banda recomendado de forma que cubra el RPO el 100 % del tiempo. Después de establecer el ancho de banda correcto, si no percibe un aumento en el rendimiento obtenido que notifica la herramienta, siga estos pasos:

1. Realice las comprobaciones necesarias para determinar si hay algún problema de calidad de servicio (QoS) de la red que limite el rendimiento de Azure Site Recovery.
2. Realice las comprobaciones necesarias para determinar si el almacén de Azure Site Recovery está en la región física de Microsoft Azure más cercana admitida para minimizar la latencia de red.
3. Compruebe las características del almacenamiento local para determinar si puede mejorar el hardware (por ejemplo, pasar de HDD a SSD).

    
## <a name="next-steps"></a>pasos siguientes
* [Análisis del informe generado](hyper-v-deployment-planner-analyze-report.md)
