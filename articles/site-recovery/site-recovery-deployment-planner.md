---
title: Azure Site Recovery Deployment Planner para VMware en Azure| Microsoft Docs
description: "Esta es la guía del usuario de Azure Site Recovery Deployment Planner."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 2/21/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 4e444deaa84c7f02608f4910e31f7033df51a73b
ms.lasthandoff: 03/06/2017


---
#<a name="azure-site-recovery-deployment-planner"></a>Azure Site Recovery Deployment Planner
Esta es la guía del usuario de Azure Site Recovery Deployment Planner para implementaciones de producción de VMware en Azure.


##<a name="overview"></a>Información general

Antes de proteger cualquier máquina virtual de VMware con Azure Site Recovery, debe asignar suficiente ancho de banda según la frecuencia de cambio de datos diaria para cumplir con el RPO deseado. Debe implementar localmente el número correcto de servidores de configuración y de proceso. También debe crear el tipo y el número adecuado de cuentas de Azure Storage de destino, ya sean Estándar o Premium, siempre teniendo en cuenta un crecimiento de los servidores de producción de origen debido a un mayor uso con el tiempo. El tipo de almacenamiento se decide para cada máquina virtual según las características de la carga de trabajo (IOPS de lectura/escritura, actividad de datos) y de los límites de Azure Site Recovery.  

La versión preliminar pública de Azure Site Recovery Deployment Planner es una herramienta de línea de comandos que solo está disponible actualmente para escenarios de VMware en Azure. Puede generar de forma remota un perfil para las máquinas virtuales de VMware mediante esta herramienta (sin ningún efecto en la producción) para averiguar el ancho de banda y los requisitos de Azure Storage necesarios para realizar una correcta replicación y conmutación por error de prueba.  Puede ejecutar la herramienta sin necesidad de instalar ningún componente de Azure Site Recovery localmente, aunque para obtener unos resultados de rendimiento adecuados, se recomienda ejecutar Deployment Planner en una instancia de Windows Server que cumpla los requisitos mínimos del servidor de configuración de Azure Site Recovery que deberá implementar como uno de los primeros pasos de la implementación de producción.

La herramienta proporciona los detalles siguientes:

**Evaluación de compatibilidad**<br>
* Evaluación de la idoneidad de la máquina virtual en función del número de discos, tamaño del disco, IOPS y actividad

**Necesidad de ancho de banda de red frente a evaluación de RPO**<br>
* Ancho de banda de red necesario para la replicación diferencial<br>
* Rendimiento que Azure Site Recovery puede obtener desde local a Azure<br>
* Número de máquinas virtuales que desea agrupar en función del ancho de banda estimado para completar la replicación inicial en un período determinado de tiempo<br>

**Requisitos de infraestructura de Microsoft Azure**<br>
* Requisito de tipo de almacenamiento (Estándar o Premium) para cada máquina virtual<br>
* Número total de cuentas de almacenamiento estándar y premium que desea aprovisionar para la replicación<br>
* Sugerencias de nomenclatura para las cuentas de almacenamiento según la guía de Azure Storage<br>
* La selección de ubicación de la cuenta de almacenamiento de cada máquina virtual<br>
* Número de núcleos de Microsoft Azure que desea aprovisionar antes de realizar una conmutación por error (de prueba o no) en la suscripción<br>
* Tamaño recomendado de máquina virtual de Microsoft Azure para cada máquina virtual local<br>

**Requisitos de la infraestructura local**<br>
* El número necesario de servidores de configuración y de proceso que se van a implementar localmente.<br>

>[!IMPORTANT]
>
>Todos estos cálculos de la herramienta se realizan suponiendo un factor de crecimiento del 30% en las características de la carga de trabajo, debido a un posible mayor uso con el tiempo, que ocupa el percentil 95 de todas las métricas de generación de perfiles (IOPS de lectura/escritura, actividad, etc.) Ambos parámetros, el cálculo de percentil y el factor de crecimiento, son configurables. Obtenga más información sobre el [factor de crecimiento](site-recovery-deployment-planner.md#growth-factor) y el [valor de percentil utilizado para el cálculo](site-recovery-deployment-planner.md#percentile-value-used-for-the-calculation).
>


## <a name="requirements"></a>Requisitos
La herramienta tiene dos fases principales: la generación de perfiles y la generación de informes. También hay una tercera opción para calcular solo el rendimiento. A continuación, se muestran los requisitos para el servidor desde donde se inicia la medición de la generación de perfiles o del rendimiento.

| Requisito | Descripción|
|---|---|
|Medición de la generación de perfiles y el rendimiento| <br>Sistema operativo: Microsoft Windows Server 2012 R2 <br>Lo ideal es que coincida al menos con el siguiente [tamaño](https://aka.ms/asr-v2a-on-prem-components) del servidor de configuración<br>Configuración de la máquina: 8 vCPus, 16 GB RAM, 300 GB HDD<br [Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)<br>[VMware vSphere PowerCLI 6.0 R3](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)<br>[Microsoft Visual C++ Redistributable para Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)<br> Acceso de Internet a Microsoft Azure desde este servidor<br> Una cuenta de almacenamiento de Microsoft Azure<Br>Acceso de administrador en el servidor<br>Espacio mínimo en disco de 100 GB (suponiendo 1000 máquinas virtuales con un promedio de 3 discos cada una con perfiles generados durante 30 días)|
| Generación de informes| Cualquier equipo Windows PC o Windows Server con Microsoft Excel 2013 o versiones posteriores |
| Permisos de usuario | Permiso de solo lectura para la cuenta de usuario utilizada para acceder al servidor de VMware vCenter o vSphere durante la generación de perfiles|


> [!NOTE]
>
> La herramienta solo puede generar perfiles de máquinas virtuales con discos VMDK y RDM. No se pueden generar perfiles de máquinas virtuales con iSCSI o discos NFS. Aunque Azure Site Recovery admite iSCSI y discos NFS para los servidores de VMware, como Deployment Planner no está situado dentro del invitado y no genera perfiles únicamente mediante contadores de rendimiento de vCenter, la herramienta no tiene visibilidad en estos tipos de disco.
>


##<a name="download"></a>Descargar
[Descargue](https://aka.ms/asr-deployment-planner) la versión más reciente de la versión preliminar pública de Azure Site Recovery Deployment Planner.  La herramienta se empaqueta en formato zip.  La versión actual de la herramienta admite únicamente el escenario de VMware a Azure.

Copie el archivo zip en la instancia de Windows Server en la que desea ejecutar la herramienta. Aunque puede ejecutar la herramienta desde cualquier instancia de Windows Server 2012 R2 que tenga acceso a la red para conectarse con el servidor VMware vCenter o con el host de VMware vSphere ESXi que contiene las máquinas virtuales cuyos perfiles se van a generar, se recomienda ejecutar la herramienta en un servidor cuya configuración de hardware sea como la descrita en [Recomendaciones de tamaño del servidor de configuración](https://aka.ms/asr-v2a-on-prem-components).  Si ya ha implementado los componentes de Azure Site Recovery de forma local, debe ejecutar la herramienta desde el servidor de configuración. Se recomienda tener la misma configuración de hardware que el servidor de configuración (que tiene un servidor de proceso incorporado) en el servidor donde se ejecuta la herramienta para que el rendimiento obtenido que notifica la herramienta se corresponda con el rendimiento real que Azure Site Recovery puede lograr durante la replicación: el cálculo de rendimiento depende del ancho de banda de red disponible en el servidor y de la configuración de hardware (CPU, almacenamiento, etc.) de este. Si ejecuta la herramienta desde cualquier otro servidor, el rendimiento se calculará desde ese servidor a Microsoft Azure. Además, puede que la configuración de hardware del servidor sea diferente a la del servidor de configuración y, por tanto, el rendimiento obtenido que notifica la herramienta no será preciso.

Extraiga la carpeta .zip. Puede ver varios archivos y subcarpetas. El ejecutable es ASRDeploymentPlanner.exe y está en la carpeta primaria.

Ejemplo: copie el archivo .zip en la unidad E:\ y extráigalo.
E:\ASR Deployment Planner-Preview_v1.0.zip

E:\ASR Deployment Planner-Preview_v1.0\ ASR Deployment Planner-Preview_v1.0\ ASRDeploymentPlanner.exe

##<a name="capabilities"></a>Capacidades
La herramienta de línea de comandos (ASRDeploymentPlanner.exe) se puede ejecutar en cualquiera de los tres modos siguientes:

1.    Generación de perfiles  
2.    Generación de informes
3.    Obtención de rendimiento

En primer lugar debe ejecutar la herramienta en el modo de generación de perfiles para recopilar la actividad de datos y la información de IOPS de las máquinas virtuales.  A continuación, ejecute la herramienta para generar el informe para encontrar los requisitos de ancho de banda de red y de almacenamiento.

##<a name="profiling"></a>Generación de perfiles
En el modo de generación de perfiles, la herramienta Deployment Planner se conecta al servidor vCenter Server o a los hosts de vSphere ESXi para recopilar datos de rendimiento de la máquina virtual.

* La generación de perfiles no afecta al rendimiento de las máquinas virtuales de producción ya que no se establece ninguna conexión directa a la máquina virtual de producción. Todos los datos de rendimiento se recopilan del servidor vCenter o del host de vSphere ESXi.
* Se realiza una consulta al servidor de vCenter o host de vSphere EXSi cada 15 minutos, para asegurarse de que solo haya un efecto insignificante en el servidor debido a la generación de perfiles. Sin embargo, esto no afecta a la precisión de la generación de perfiles ya que la herramienta almacena los datos de los contadores de rendimiento cada minuto.

####<a name="create-a-list-of-virtual-machines-to-profile"></a>Creación de una lista de las máquinas virtuales de las que desea crear el perfil
En primer lugar, debe tener la lista de las máquinas virtuales de las que desea crear el perfil. Puede obtener todos los nombres de las máquinas virtuales de un host de VMware vCenter o VMware vSphere ESXi mediante los siguientes comandos de VMware vSphere PowerCLI. Como alternativa, puede enumerar solo los nombres descriptivos o direcciones IP de las máquinas virtuales de las que desea generar el perfil manualmente en un archivo.

1.    Inicie sesión en la máquina virtual en la que está instalado VMware vSphere PowerCLI
2.    Abra la consola de VMware vSphere PowerCLI
3.    Asegúrese de que la directiva de ejecución no está deshabilitada para el script. Si está deshabilitada, inicie la consola de VMware vSphere PowerCLI en modo de administrador y ejecute el siguiente comando para habilitarla:

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4.    Ejecute los dos comandos siguientes para obtener todos los nombres de las máquinas virtuales de un host de VMware vCenter o VMware vSphere ESXi y almacenarlos en un archivo .txt.
Reemplace &lsaquo;server name&rsaquo; (nombre del servidor), &lsaquo;user name&rsaquo; (nombre de usuario), &lsaquo;password&rsaquo; (contraseña), &lsaquo;outputfile.txt&rsaquo;; (archivo de salida.txt) por sus entradas.

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>


5.    Abra el archivo de salida en el Bloc de notas. Copie los nombres de todas las máquinas virtuales de las que desee generar el perfil a otro archivo (por ejemplo, ProfileVMList.txt), con un nombre de máquina virtual por cada línea. Este archivo se usará como entrada para el parámetro VMListFile de la herramienta de línea de comandos

    ![Deployment Planner](./media/site-recovery-deployment-planner/profile-vm-list.png)


####<a name="start-profiling"></a>Inicio de la generación de perfiles
Una vez que tiene la lista de máquinas virtuales de las que va a generar el perfil, ya puede ejecutar la herramienta en modo de generación de perfiles. Esta es la lista de parámetros obligatorios y opcionales de la herramienta para que se ejecute en modo de generación de perfiles. Los parámetros entre [] son opcionales.

ASRDeploymentPlanner.exe -Operation StartProfiling /?

| Nombre del parámetro | Descripción |
|---|---|
| -Operation |      Inicio de la generación de perfiles |
| -Server | Nombre de dominio completo o dirección IP del host del servidor vCenter o host ESXi que contiene las máquinas virtuales de las que va a generar el perfil.|
| -User | Nombre de usuario para conectarse al servidor de vCenter o host ESXi. El usuario debe tener al menos un acceso de solo lectura.|
| -VMListFile |    El archivo con la lista de máquinas virtuales de las que desea generar el perfil. La ruta de acceso del archivo puede ser absoluta o relativa. Este archivo debe contener un nombre de máquina virtual o dirección IP por línea. El nombre de la máquina virtual especificada en el archivo debe ser el mismo que el nombre de la máquina virtual del servidor vCenter o host ESXi. <br> Por ejemplo: el archivo "VMList.txt" contiene las siguientes máquinas virtuales:<br>virtual_machine_A <br>10.150.29.110<br>virtual_machine_B |
| -NoOfDaysToProfile | Número de días durante el cual se va a ejecutar la generación de perfiles. Se recomienda ejecutar la generación de perfiles durante más de 15 días para asegurarse de que el patrón de carga de trabajo en su entorno durante el período especificado se observa y se usa para proporcionar una recomendación adecuada |
| [-Directory] |    UNC o ruta de acceso del directorio local para almacenar los datos de generación de perfiles que se han producido durante esta. Si no se especifica, se usará el directorio denominado "ProfiledData" en la ruta de acceso actual como directorio predeterminado. |
| [-Password ] | Contraseña para conectarse al servidor de vCenter o host ESXi. Si no se especifica ahora, se le pedirá que lo haga cuando se ejecute el comando.|
|  [-StorageAccountName]  | Nombre de la cuenta de Azure Storage para hallar el rendimiento obtenible para la replicación de datos desde una ubicación local a Azure. La herramienta carga los datos de prueba en esta cuenta de almacenamiento para calcular el rendimiento.|
| [-StorageAccountKey] | La clave de la cuenta de Azure Storage utilizada para acceder a la cuenta de almacenamiento. Vaya a Azure Portal > Cuentas de almacenamiento > [nombre de la cuenta de almacenamiento] > Configuración > Claves de acceso > Key1 (o clave de acceso principal para la cuenta de almacenamiento clásico). |

Se recomienda generar los perfiles de las máquinas virtuales durante al menos 15 a 30 días. Durante el período de generación de perfiles ASRDeploymentPlanner.exe sigue ejecutándose. La herramienta admite la entrada de tiempo de generación de perfiles en días. Si desea generar perfiles solo durante algunas horas o minutos para una prueba rápida de la herramienta, en la versión preliminar pública debe convertir el tiempo a la medida equivalente en días.  Por ejemplo, para generar perfiles durante 30 minutos, la entrada deberá ser 30 / (60*24) = 0,021 días.  El tiempo mínimo permitido de generación de perfiles es de 30 minutos.

Durante la generación de perfiles, también puede pasar un nombre y una clave de cuenta de Azure Storage para ver el rendimiento que Azure Site Recovery puede lograr en el momento de la replicación desde el servidor de configuración o de procesos a Azure. Si el nombre y la clave de la cuenta de Azure Storage no se pasan durante la generación de perfiles, la herramienta no calculará el rendimiento obtenible.


Puede ejecutar varias instancias de la herramienta para distintos conjuntos de máquinas virtuales. Asegúrese de que los nombres de máquina virtual no se repiten en ninguno de los conjuntos de generación de perfiles. Por ejemplo, si ha generado el perfil de diez máquinas virtuales (VM1 a VM10) y después de algunos días desea generar el perfil de otras cinco máquinas virtuales (VM11 a VM15), puede ejecutar la herramienta desde otra consola de línea de comandos para el segundo conjunto de máquinas virtuales (VM11 a VM15). Pero asegúrese de que el segundo conjunto de máquinas virtuales no tiene ninguno de los nombres de máquina virtual de la primera instancia de generación de perfiles o utilice un directorio de salida diferente para la segunda ejecución. Si se usan dos instancias de la herramienta para generar los perfiles de las mismas máquinas virtuales y se usa el mismo directorio de salida, el informe generado será incorrecto.

La configuración de las máquinas virtuales se captura una vez al principio de la operación de generación de perfiles y se almacena en un archivo denominado VMDetailList.xml. Esta información se usará en el momento de la generación de informes. Cualquier cambio en la configuración de máquina virtual (por ejemplo, un aumento en el número de núcleos, discos, NIC, etc.) desde el momento en que se inició la generación de perfiles hasta que finalizó, no se capturará. Esta es la solución alternativa para obtener la información de máquina virtual más reciente al generar perfiles en caso de que se le presente una situación en la que la configuración de la máquina virtual de la que está generando el perfil haya cambiado en el transcurso de la generación del informe.   

* Realice una copia de seguridad de "VMdetailList.xml" y elimine el archivo de su ubicación actual.
* Pase los argumentos -User y -Password en el momento de la generación de informes.

El comando de generación de perfiles generará varios archivos en el directorio correspondiente. No elimine ninguno de ellos o afectará a la generación de informes.

#####<a name="example-1-to-profile-virtual-machines-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Ejemplo 1: Generación de perfiles de máquinas virtuales durante 30 días y búsqueda del rendimiento desde una ubicación local a Azure
ASRDeploymentPlanner.exe **-Operation** StartProfiling -Directory “E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  30  **-User** vCenterUser1 **-StorageAccountName**  asrspfarm1 **-StorageAccountKey** Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==

#####<a name="example-2-to-profile-virtual-machines-for-15-days"></a>Ejemplo 2: Generación de perfiles de máquinas virtuales durante 15 días
ASRDeploymentPlanner.exe **-Operation** StartProfiling **-Directory** “E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  15  -User vCenterUser1

#####<a name="example-3-to-profile-virtual-machines-for-1-hour-for-a-quick-test-of-the-tool"></a>Ejemplo 3: Generación de perfiles de máquinas virtuales durante una hora para una prueba rápida de la herramienta
ASRDeploymentPlanner.exe **-Operation** StartProfiling **-Directory** “E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  0.04  **-User** vCenterUser1


>[!NOTE]
>
> * Si el servidor desde donde se ejecuta la herramienta se reinicia o se bloquea, o si sale de la herramienta con Ctrl + C, se conservarán los datos de los perfiles generados. Es posible que falten los últimos 15 minutos de los datos de los perfiles generados debido a esto. Debe volver a ejecutar la herramienta en modo de generación de perfiles después de que el servidor comience la copia de seguridad.
>
> * Cuando se pasa el nombre y la clave de la cuenta de Azure Storage, la herramienta mide el rendimiento en el último paso de la generación de perfiles. Si la herramienta se termina antes de que finalice correctamente la generación de perfiles, el rendimiento no se calculará. Siempre puede ejecutar la operación GetThroughput desde la consola de línea de comandos para hallar el rendimiento antes de generar el informe. En caso contrario, el informe generado no tendrá la información sobre el rendimiento obtenido.
>

##<a name="generate-report"></a>Generación de informes
La herramienta genera un archivo XLSM (archivo de Microsoft Excel habilitado para macros) como salida de informe que resume todas las recomendaciones de implementación. El informe se llama DeploymentPlannerReport_<Unique Numeric Identifier>.xlsm y se coloca en el directorio especificado.

Una vez completada la generación de perfiles, puede ejecutar la herramienta en modo de generación de informes. Esta es la lista de parámetros obligatorios y opcionales de la herramienta para que se ejecute en modo de generación de informes. Los parámetros entre [] son opcionales.

ASRDeploymentPlanner.exe -Operation GenerateReport /?

|Nombre del parámetro | Descripción |
|-|-|
| -Operation | GenerateReport |
| -Server |  Nombre de dominio completo del servidor de vCenter o vSphere, o la dirección IP (use el nombre exacto o la dirección IP que utilizó durante la generación de perfiles) donde se encuentran las máquinas virtuales de las que va a generar los perfiles cuyo informe va a generar. Tenga en cuenta que si usa un servidor de vCenter en el momento de la generación de perfiles, no podrá usar un servidor de vSphere para la generación de informes y viceversa.|
| -VMListFile | El archivo con la lista de las máquinas virtuales de las que se ha generado el perfil para las que se va a generar el informe. La ruta de acceso del archivo puede ser absoluta o relativa. Este archivo debe contener un nombre de máquina virtual o dirección IP por línea. Los nombres de máquina virtual especificados en el archivo deben ser los mismos que los nombres de las máquinas virtuales del servidor de vCenter o host ESXi y coincidir con los que se usaron durante la generación de perfiles.|
| [-Directory] | UNC o ruta de acceso del directorio local donde se almacenan los datos de las máquinas virtuales de las que se ha generado el perfil (archivos creados durante la generación de perfiles). Estos datos son necesarios para generar el informe. Si no se especifica, se usará el directorio 'ProfiledData'. |
| [-GoalToCompleteIR] |    Número de horas en el que se debe completar la replicación inicial de las máquinas virtuales de las que va a generar el perfil. El informe generado le proporcionará el número de máquinas virtuales para las que se puede completar la replicación inicial en el tiempo especificado. El valor predeterminado es 72 horas. |
| [-User] | Nombre de usuario para conectarse al servidor de vCenter o vSphere. Este se utiliza para capturar la información de configuración más reciente de las máquinas virtuales como, por ejemplo, el número de discos, el número de núcleos, el número de NIC, etc. que se usarán en el informe. Si no se especifica, se usará la información de configuración recopilada al principio de la generación de perfiles. |
| [-Password] | Contraseña para conectarse al servidor de vCenter o host ESXi. Si no se especifica como parámetro, se le pedirá más tarde cuando se ejecute el comando. |
| [-DesiredRPO] | El objetivo deseado de punto de recuperación (RPO) en minutos. El valor predeterminado es 15 minutos.|
| [-Bandwidth] | Ancho de banda en Mbps. Este se usa para calcular el RPO que se puede conseguir para el ancho de banda especificado. |
| [-StartDate]  | Fecha y hora de inicio en MM-DD-AAAA:HH:MM (en formato de 24 horas). "StartDate" se debe especificar junto con "EndDate". Cuando se especifica, se genera el informe para los datos de la generación de perfiles recopilados entre StartDate y EndDate. |
| [-EndDate] | Fecha y hora de finalización en MM-DD-AAAA:HH:MM (en formato de 24 horas). "EndDate" se debe especificar junto con "StartDate". Cuando se especifica, se genera el informe para los datos de la generación de perfiles recopilados entre StartDate y EndDate. |
| [-GrowthFactor] |Factor de crecimiento en porcentaje. El valor predeterminado es 30%.  |


##### <a name="example-1-to-generate-report-with-default-values-when-profiled-data-is-on-the-local-drive"></a>Ejemplo 1: Generación de informes con los valores predeterminados cuando los datos de generación de perfiles están en la unidad local
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”


##### <a name="example-2-to-generate-report-when-profiled-data-is-on-a-remote-server-user-should-have-readwrite-access-on-the-remote-directory"></a>Ejemplo 2: Generación de informes cuando los datos de generación de perfiles están en un servidor remoto. El usuario debe tener acceso de lectura y escritura en el directorio remoto.
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “\\PS1-W2K12R2\vCenter1_ProfiledData” **-VMListFile** “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”

##### <a name="example-3-generate-report-with-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Ejemplo 3: Generación de informes con ancho de banda y objetivo específicos para finalizar IR en el tiempo especificado
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-Bandwidth** 100 **-GoalToCompleteIR** 24

##### <a name="example-4-generate-report-with-5-growth-factor-instead-of-the-default-30"></a>Ejemplo 4: Generación de informes con un factor de crecimiento del 5% en lugar del valor predeterminado del 30%
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-GrowthFactor** 5

##### <a name="example-5-generate-report-with-a-subset-of-profiled-data-say-you-have-30-days-of-profiled-data-and-want-to-generate-the-report-for-only-20-days"></a>Ejemplo 5: Generación de informes con un subconjunto de datos de la generación de perfiles. Supongamos que tiene 30 días de datos de generación de perfiles y desea generar el informe de solo 20 días.
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-StartDate**  01-10-2017:12:30 -**EndDate** 01-19-2017:12:30

##### <a name="example-6-generate-report-for-5-minutes-rpo"></a>Ejemplo 6: Generación de informes para un RPO de 5 minutos.
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-DesiredRPO** 5

### <a name="percentile-value-used-for-the-calculation"></a>Valor del percentil usado para el cálculo
**¿Qué valor de percentil predeterminado de las métricas de rendimiento recopiladas durante la generación de perfiles se utiliza durante la generación de informes?**

La herramienta usa el percentil 95 como valor predeterminado de IOPS de lectura/escritura, IOPS de escritura y actividad de los datos recopilados durante la generación de perfiles de todas las máquinas virtuales. Esto garantiza que el pico de percentil 100 que pueden ver las máquinas virtuales debido a eventos temporales como un trabajo de copia de seguridad que se ejecuta una vez al día, una indexación de base de datos periódica o una actividad de generación de informes de análisis, o cualquier otro evento breve de naturaleza similar que tiene lugar durante el período de generación de perfiles, no se usa para determinar los requisitos de Azure Storage y de ancho de banda de origen. Usar valores de percentil 95 ofrece una visión verdadera de las características de la carga de trabajo real y le ofrece el mejor rendimiento cuando estas cargas de trabajo se ejecutan en Microsoft Azure. Normalmente, este número no se cambiará a menudo, pero si decide usar un valor menor, por ejemplo, el percentil 90, puede actualizar este archivo de configuración "ASRDeploymentPlanner.exe.config" en la carpeta predeterminada y guardarlo para generar un nuevo informe en los datos de generación de perfiles existentes.

        &lsaquo;add key="WriteIOPSPercentile" value="95" /&rsaquo;>      
        &lsaquo;add key="ReadWriteIOPSPercentile" value="95" /&rsaquo;>      
        &lsaquo;add key="DataChurnPercentile" value="95" /&rsaquo;

### <a name="growth-factor"></a>Factor de crecimiento
**¿Por qué se debe tener en cuenta el factor de crecimiento al planear las implementaciones?**

Es fundamental tener en cuenta el crecimiento de las características de la carga de trabajo suponiendo un posible aumento del uso con el tiempo. Esto se debe a que, una vez protegido, si posteriormente cambian las características de la carga de trabajo, no hay ningún medio actualmente para cambiar a otra cuenta de Azure Storage y obtener protección sin tener que deshabilitar y volver a habilitar la protección. Por ejemplo, Si en el momento presente, una máquina virtual se ajusta a una cuenta de replicación de almacenamiento estándar y, dentro de tres meses, debido a un aumento en el número de usuarios de la aplicación que se ejecuta en la máquina virtual y a un aumento de la actividad de datos, es necesario cambiar a una cuenta de almacenamiento premium para que la replicación de Azure Site Recovery pueda hacer frente a la nueva actividad de mayor tamaño, tendrá que deshabilitar y volver a habilitar la protección al pasar a la cuenta de almacenamiento premium. Por tanto, es muy recomendable prever un crecimiento durante la planeación de la implementación y el valor predeterminado es un crecimiento del 30%. El usuario conoce mejor que nadie los patrones de uso de sus aplicaciones y las proyecciones de crecimiento y puede cambiar este número según sea necesario durante la generación de un informe. De hecho, puede generar varios informes con distintos factores de crecimiento con los mismos datos de generación de perfiles y ver qué recomendaciones de Azure Storage y de ancho de banda de origen funcionan mejor en su caso.

El informe generado de Microsoft Excel tiene las siguientes hojas

* [Entrada](site-recovery-deployment-planner.md#input)
* [Recomendaciones](site-recovery-deployment-planner.md#recommendations-with-desired-rpo-as-input)
* [Entrada de ancho de banda-Recomendaciones](site-recovery-deployment-planner.md#recommendations-with-available-bandwidth-as-input)
* [VM<->Selección de ubicación de almacenamiento](site-recovery-deployment-planner.md#vm-storage-placement)
* [VM compatibles](site-recovery-deployment-planner.md#compatible-vms)
* [VM incompatibles](site-recovery-deployment-planner.md#incompatible-vms)

![Deployment Planner](./media/site-recovery-deployment-planner/dp-report.png)


##<a name="get-throughput"></a>Obtención de rendimiento
Para calcular el rendimiento que Azure Site Recovery puede lograr durante la replicación desde una ubicación local a Azure, ejecute la herramienta en modo de GetThroughput. La herramienta calcula el rendimiento del servidor donde se ejecuta la herramienta (idealmente un servidor según las recomendaciones descritas en la guía de tamaño del servidor de configuración).  Si ya ha implementado los componentes de la infraestructura de Azure Site Recovery de forma local, ejecute la herramienta en el servidor de configuración.

Abra una consola de línea de comandos y vaya a la carpeta de la herramienta de planeación de la implementación de ASR.  Ejecute ASRDeploymentPlanner.exe con los siguientes parámetros. Los parámetros entre [] son opcionales.

ASRDeploymentPlanner.exe -Operation GetThroughput /?

|Nombre del parámetro | Descripción |
|-|-|
| -operation | GetThroughput |
| [-Directory] | UNC o ruta de acceso del directorio local donde se almacenan los datos de las máquinas virtuales de las que se ha generado el perfil (archivos creados durante la generación de perfiles). Estos datos son necesarios para generar el informe. Si no se especifica, se usará el directorio 'ProfiledData'.  |
| -StorageAccountName | Nombre de la cuenta de Azure Storage para hallar el ancho de banda consumido durante la replicación de datos desde una ubicación local a Azure. La herramienta carga los datos de prueba en esta cuenta de almacenamiento para calcular el ancho de banda consumido. |
| -StorageAccountKey | La clave de la cuenta de Azure Storage utilizada para acceder a la cuenta de almacenamiento. Vaya a Azure Portal > Cuentas de almacenamiento > [nombre de la cuenta de almacenamiento] > Configuración > Claves de acceso > Key1 (o clave de acceso principal para la cuenta de almacenamiento clásico). |
| -VMListFile | El archivo con la lista de máquinas virtuales de las que se va a generar el perfil para calcular el ancho de banda consumido. La ruta de acceso del archivo puede ser absoluta o relativa. Este archivo debe contener un nombre de máquina virtual o dirección IP por línea. Los nombres de máquina virtual especificados en el archivo deben ser los mismos que los nombres de las máquinas virtuales del servidor de vCenter o host ESXi.<br>Por ejemplo, el archivo "VMList.txt" contiene las siguientes máquinas virtuales:<br>virtual machine_A <br>10.150.29.110<br>virtual machine_B|

La herramienta crea varios archivos llamados "asrvhdfile<#>.vhd" de 64 MB (donde # es el número) en el directorio especificado.  La herramienta carga estos archivos en la cuenta de Azure Storage para calcular el rendimiento. Una vez que se mide el rendimiento, se eliminan todos estos archivos de la cuenta de Azure Storage y del servidor local. Si la herramienta se cierra por algún motivo en mitad del proceso de cálculo del rendimiento, no se eliminarán los archivos de Azure Storage ni del servidor local y tendrá que eliminarlos manualmente.

El rendimiento se mide en un momento determinado y es el rendimiento máximo que puede lograr Azure Site Recovery durante la replicación siempre que todos los demás factores permanezcan igual. Por ejemplo, si cualquier aplicación comienza a consumir más ancho de banda en la misma red, el rendimiento real variará durante la replicación. Si está ejecutando el comando GetThroughput desde un servidor de configuración, la herramienta no detecta las máquinas virtuales protegidas ni la replicación en curso. El resultado del rendimiento medido será diferente si se ejecuta la operación de GetThroughput en un momento en que las máquinas virtuales protegidas tengan una alta actividad de datos en lugar de una baja.  Se recomienda ejecutar la herramienta en distintos puntos de tiempo durante la generación de perfiles para entender qué rendimiento se puede lograr en diferentes momentos. En el informe, la herramienta muestra el último rendimiento medido.


##### <a name="example"></a>Ejemplo
ASRDeploymentPlanner.exe **-Operation** GetThroughput **-Directory**  E:\vCenter1_ProfiledData **-VMListFile** E:\vCenter1_ProfiledData\ProfileVMList1.txt  **-StorageAccountName**  asrspfarm1 **-StorageAccountKey** by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==

>[!NOTE]
>
> * Ejecute la herramienta en un servidor que tenga las mismas características de almacenamiento y de CPU que el servidor de configuración
>
> * En la replicación, aprovisione el ancho de banda que se recomienda para satisfacer el RPO en el 100% de las ocasiones. Incluso después de aprovisionar el ancho de banda adecuado, si no ve algún aumento en el rendimiento obtenido notificado por esta herramienta, compruebe lo siguiente:
>
> a. Compruebe si hay algún requisito de calidad de servicio (QoS) de la red que esté limitando el rendimiento de Azure Site Recovery
>
> b. Compruebe que el almacén de Azure Site Recovery está en la región física admitida más cercana de Microsoft Azure para minimizar la latencia de red
>
> c. Compruebe las características de almacenamiento local e intente mejorar el hardware (por ejemplo, de HDD a SSD, etc.)
>
> d. Cambie la configuración de Azure Site Recovery en el servidor de procesos para [aumentar la cantidad de ancho de banda de red utilizado para la replicación](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
>

##<a name="recommendations-with-desired-rpo-as-input"></a>Recomendaciones con el RPO deseado como entrada

###<a name="profiled-data"></a>Datos de generación de perfiles

![Deployment Planner](./media/site-recovery-deployment-planner/profiled-data-period.png)

El **período de datos de generación de perfiles** es el período de tiempo durante el que se ejecutará la generación de perfiles. De forma predeterminada, la herramienta usará todos los datos de generación de perfiles para el cálculo a menos que el informe se genere solo para un período específico mediante las opciones StartDate y EndDate durante la generación de informes.

**Nombre del servidor** es el nombre o dirección IP del servidor VMware vCenter o host ESXi cuyo informe de máquinas virtuales se genera.

**RPO deseado** es el objetivo de punto de recuperación (RPO) deseado de la implementación. De forma predeterminada, se calcula el ancho de banda de red necesario para los valores de RPO de 15, 30 y 60 minutos. En función de la selección, los valores afectados se actualizan en la hoja. Si ha usado el parámetro DesiredRPOinMin al generar el informe, ese valor se mostrará en esta lista desplegable de RPO deseado.

###<a name="profiling-overview"></a>Información general sobre la generación de perfiles

![Deployment Planner](./media/site-recovery-deployment-planner/profiling-overview.png)

El **total de máquinas virtuales de las que se ha generado el perfil** es el número total de máquinas virtuales cuyos datos de generación de perfil están disponibles. Si VMListFile contiene los nombres de todas las máquinas virtuales de las que no se ha generado el perfil, esas máquinas virtuales no se tendrán en cuenta a la hora de generar los informes y se excluirán del recuento total de máquinas virtuales de las que se ha generado el perfil.

**Máquinas virtuales compatibles** es el número de máquinas virtuales que se pueden proteger en Azure con Azure Site Recovery. Es el número total de máquinas virtuales compatibles para el que se calculan el ancho de banda de red, el número de cuentas de Azure Storage, el número de núcleos de Microsoft Azure y el número de servidores de configuración y de servidores de proceso adicionales necesarios. Los detalles de todas las máquinas virtuales compatibles están disponibles en la hoja correspondiente del informe.

**Máquinas virtuales incompatibles** es el número de máquinas virtuales de las que se ha generado el perfil que no son compatibles para la protección con Azure Site Recovery. Los motivos de incompatibilidad se indican en la sección de máquinas virtuales incompatibles que aparece a continuación. Si VMListFile contiene los nombres de todas las máquinas virtuales de las que no se ha generado el perfil, esas máquinas virtuales se excluirán del recuento total de máquinas virtuales incompatibles. Estas máquinas virtuales aparecerán en la lista junto con la anotación "No se encontraron datos" al final de la hoja de máquinas virtuales incompatibles.

**RPO deseado** es el RPO deseado en minutos. El informe se genera para tres valores de RPO: 15, 30 y 60 minutos, siendo el valor predeterminado de 15 minutos. La recomendación de ancho de banda en el informe se cambiará en función de su selección en la lista desplegable de RPO deseado situada en la parte superior derecha de la hoja. Si ha generado el informe mediante el parámetro "-DesiredRPO" con un valor personalizado, este valor personalizado se mostrará como valor predeterminado en la lista desplegable de RPO deseado.

###<a name="required-network-bandwidth-mbps"></a>Ancho de banda de red requerido (Mbps)

![Deployment Planner](./media/site-recovery-deployment-planner/required-network-bandwidth.png)

**Para satisfacer el RPO en el 100% de las ocasiones:** es el ancho de banda recomendado en Mbps que debe asignar para satisfacer el RPO deseado en todas las ocasiones. Esta cantidad de ancho de banda debe estar dedicada para la replicación diferencial de estado estable de todas las máquinas virtuales compatibles para evitar las infracciones de RPO.

**Para satisfacer el RPO en el 90% de las ocasiones**: debido a los precios del ancho de banda o por cualquier otra razón, si no se puede proporcionar el ancho de banda necesario para satisfacer el RPO deseado en el 100% de los casos, puede decidir aprovisionar una cantidad de ancho de banda inferior que le permita satisfacer el RPO deseado en el 90% de las ocasiones. Para entender las implicaciones de aprovisionar este ancho de banda inferior, el informe proporciona el análisis de hipótesis del número y la duración de las infracciones de RPO que es previsible.

**Rendimiento obtenido:** es el rendimiento del servidor donde ha ejecutado el comando GetThroughput para la región de Microsoft Azure en la que se encuentra la cuenta de Azure Storage. Indica el rendimiento aproximado que se puede lograr al proteger las máquinas virtuales compatibles con Azure Site Recovery, siempre y cuando las características de red y de almacenamiento del servidor de configuración o el servidor de procesos sean las mismas que aquellas del servidor en el que se ha ejecutado la herramienta.    

En la replicación, debe aprovisionar el ancho de banda que se recomienda para satisfacer el RPO en el 100% de las ocasiones. Incluso después de aprovisionar el ancho de banda adecuado, si no ve algún aumento en el rendimiento obtenido notificado por esta herramienta, compruebe lo siguiente:

a.    Compruebe si hay algún requisito de calidad de servicio (QoS) de la red que esté limitando el rendimiento de Azure Site Recovery

b.    Compruebe que el almacén de Azure Site Recovery está en la región física admitida más cercana de Microsoft Azure para minimizar la latencia de red

c.    Compruebe las características de almacenamiento local e intente mejorar el hardware (por ejemplo, de HDD a SSD, etc.)

d. Cambie la configuración de Azure Site Recovery en el servidor de procesos para [aumentar la cantidad de ancho de banda de red utilizado para la replicación](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

En caso de que la herramienta se ejecute en un servidor de configuración o servidor de proceso que ya tiene máquinas virtuales protegidas, ejecute la herramienta varias veces ya que el número del rendimiento obtenido cambiará según la cantidad de actividad de datos que se esté procesando en ese momento determinado.

Para todas las implementaciones empresariales de Azure Site Recovery se recomienda [ExpressRoute](https://aka.ms/expressroute).

###<a name="required-azure-storage-accounts"></a>Cuentas de Azure Storage necesarias
Este gráfico muestra el número total de cuentas de Azure Storage (estándar y premium) necesarias para proteger todas las máquinas virtuales compatibles.  Haga clic en [Recommended VM placement plan](site-recovery-deployment-planner.md#vm-storage-placement) (Plan de selección de ubicación de VM recomendado) para saber qué cuenta de almacenamiento se debe usar para cada máquina virtual.  

![Deployment Planner](./media/site-recovery-deployment-planner/required-azure-storage-accounts.png)

###<a name="required-number-of-azure-cores"></a>Número necesario de núcleos de Azure
Este es el número total de núcleos que se debe aprovisionar antes de realizar una conmutación por error o conmutación por error de prueba de todas las máquinas virtuales compatibles. Si no hay núcleos suficientes en la suscripción, se producirá un error de Azure Site Recovery al crear las máquinas virtuales en el momento de realizar una conmutación por error de prueba o una conmutación por error.

![Deployment Planner](./media/site-recovery-deployment-planner/required-number-of-azure-cores.png)

###<a name="required-on-premises-infrastructure"></a>Infraestructura local necesaria
Es el número total de servidores de configuración y servidores de proceso adicionales que se deben configurar para proteger todas las máquinas virtuales compatibles. En función de los [límites](https://aka.ms/asr-v2a-on-prem-components) admitidos en la configuración más grande, ya sea el límite de actividad por día o el número máximo de máquinas virtuales suponiendo un promedio de tres discos por cada máquina virtual (el que se alcance primero en el servidor de configuración o el servidor de procesos adicional), la herramienta recomendará servidores adicionales. Los detalles de la actividad total por día y el número total de discos protegidos se pueden consultar en la hoja [Entrada](site-recovery-deployment-planner.md#input).

![Deployment Planner](./media/site-recovery-deployment-planner/required-on-premises-infrastructure.png)

###<a name="what-if-analysis"></a>Análisis de hipótesis
Este análisis describe cuantas infracciones podrían producirse durante el período de generación de perfiles si aprovisiona un ancho de banda menor para satisfacer el RPO deseado solo en un 90% de las ocasiones. Se pueden producir una o más infracciones de RPO en un día determinado: el gráfico muestra el RPO máximo del día.
En función de este análisis, puede decidir si el número de infracciones de RPO a lo largo de todos los días y el límite máximo de RPO por día es aceptable para el ancho de banda menor especificado. Si el resultado es aceptable, puede asignar el ancho de banda menor para la replicación. En caso contrario, asigne el ancho de banda mayor como se sugirió anteriormente para satisfacer el RPO deseado en el 100% de las ocasiones.

![Deployment Planner](./media/site-recovery-deployment-planner/what-if-analysis.png)

###<a name="recommended-vm-batch-size-for-initial-replication"></a>Tamaño de lote de máquinas virtuales recomendado para la replicación inicial
En esta sección se recomienda el número de máquinas virtuales que se pueden proteger en paralelo para completar la replicación inicial en un plazo de 72 horas (valor configurable: use el parámetro GoalToCompleteIR en el momento de la generación del informe para cambiarlo) aprovisionando el ancho de banda sugerido para satisfacer el RPO deseado en el 100% de las ocasiones.  El gráfico muestra un intervalo de valores de ancho de banda y el recuento del tamaño de lote de máquinas virtuales que se calcula que son necesarias para completar la replicación inicial en 72 horas en función del tamaño medio de las máquinas virtuales detectadas entre todas las máquinas virtuales compatibles.  

En la versión preliminar pública, el informe no especifica las máquinas virtuales que deben incluirse en un lote. Puede usar el tamaño del disco que se muestra en la hoja de máquinas virtuales compatibles para buscar el tamaño de cada máquina virtual y seleccionar las máquinas virtuales de un lote o seleccionarlas en función de las características de carga de trabajo conocidas.  El tiempo de finalización de la replicación inicial cambia proporcionalmente en función del tamaño real de los discos de las máquinas virtuales, el espacio de disco usado y el rendimiento de red disponible.

![Deployment Planner](./media/site-recovery-deployment-planner/recommended-vm-batch-size.png)

###<a name="growth-factor-and-percentile-values-used"></a>Factor de crecimiento y valores de percentil utilizados
Esta sección de la parte inferior de la hoja muestra el valor de percentil que se utiliza para todos los contadores de rendimiento de las máquinas virtuales de las que se ha generado el perfil (el percentil 95 es el valor predeterminado), y el factor de crecimiento en porcentaje usado en todos los cálculos (el valor predeterminado es el 30%).

![Deployment Planner](./media/site-recovery-deployment-planner/max-iops-and-data-churn-setting.png)

##<a name="recommendations-with-available-bandwidth-as-input"></a>Recomendaciones relacionadas con el ancho de banda disponible como entrada

![Deployment Planner](./media/site-recovery-deployment-planner/profiling-overview-bandwidth-input.png)

Puede darse el caso de que sepa que no puede aprovisionar un ancho de banda de más de x Mbps para la replicación de Azure Site Recovery. La herramienta le permite especificar el ancho de banda disponible (mediante el parámetro -Bandwidth durante la generación de informes) y obtener el RPO factible en minutos. Con este valor de RPO factible, puede decidir si necesita aprovisionar más ancho de banda o si está conforme con tener una solución de recuperación ante desastres con este RPO.

![Deployment Planner](./media/site-recovery-deployment-planner/achievable-rpos.png)

##<a name="input"></a>Entrada
La página de entrada proporciona información general sobre el entorno de VMware en el que se han generado perfiles.

![Deployment Planner](./media/site-recovery-deployment-planner/Input.png)

**Fecha de inicio y fecha de finalización** son las fechas de inicio y finalización de los datos de generación de perfiles que se tienen en cuenta para la generación de informes. De forma predeterminada, la fecha de inicio es la fecha en que comenzó la generación de perfiles y la de finalización, la fecha en la que esta se detiene.  Esta se puede especificar mediante los valores "StartDate" y "EndDate" si el informe se genera con estos parámetros. Fecha de inicio y fecha de finalización: estas son las fechas de inicio y finalización de los datos de generación de perfiles que se tienen en cuenta para la generación de informes. De forma predeterminada, la fecha de inicio es la fecha en que comenzó la generación de perfiles y la de finalización, la fecha en la que esta se detiene.  Esta se puede especificar mediante los valores "StartDate" y "EndDate" si el informe se genera con estos parámetros.

**Número total de días de generación de perfiles** es el número total de días de generación de perfiles comprendido entre las fechas de inicio y de finalización para el que se genera el informe. Número total de días de generación de perfiles es el número total de días de generación de perfiles comprendido entre las fechas de inicio y de finalización para el que se genera el informe.

**Número de máquinas virtuales compatibles** es el número total de máquinas virtuales compatibles para el que se calculan el ancho de banda de red, el número de cuentas de Azure Storage, el número de núcleos de Microsoft Azure y el número de servidores de configuración y de servidores de proceso adicionales necesarios.
El número total de discos entre todas las máquinas virtuales compatibles es el número total de discos entre todas las máquinas virtuales compatibles. Este número se utiliza como una de las entradas para decidir el número de servidores de configuración y servidores de proceso adicionales que se usarán en la implementación.

**Número medio de discos por máquina virtual compatible** es el número medio de discos calculado para todas las máquinas virtuales compatibles.

**Tamaño medio de disco (GB)** es el tamaño medio de disco calculado entre todas las máquinas virtuales compatibles.

**RPO deseado (en minutos)** es el valor predeterminado del RPO o el valor pasado para el parámetro "DesiredRPO" durante la generación de informes para calcular el ancho de banda necesario.

**Ancho de banda deseado (Mbps)** es el valor que ha pasado para el parámetro "Bandwidth" durante la generación de informes para calcular el RPO factible.

**Actividad de datos normal observada por día (GB)** es el valor promedio de la actividad de datos observada entre todos los días de generación de perfiles. Este número se utiliza como una de las entradas para decidir el número de servidores de configuración y servidores de proceso adicionales que se usarán en la implementación.


##<a name="vm-storage-placement"></a>VM-Selección de ubicación de almacenamiento

![Deployment Planner](./media/site-recovery-deployment-planner/vm-storage-placement.png)

**Tipo de almacenamiento en disco** es la cuenta de Azure Storage, "Estándar" o "Premium", usada para replicar todas las máquinas virtuales correspondientes que se mencionan en la columna "Máquinas virtuales a colocar".

**Prefijo sugerido** es el prefijo de tres caracteres sugerido que se puede usar para asignar nombres a la cuenta de Azure Storage. Siempre puede utilizar sus propios prefijos, pero lo que la herramienta sugiere es seguir la [convención de nomenclatura de particiones de las cuentas de Azure Storage](https://aka.ms/storage-performance-checklist).

**Nombre de cuenta sugerido** indica cómo debe ser el nombre de cuenta de Azure Storage después de incluir el prefijo sugerido. Sustituya el nombre entre < > por los datos personalizados.

**Cuenta de almacenamiento del registro:** todos los registros de la replicación se almacenan en una cuenta Estándar de Azure Storage. Para las máquinas virtuales que se replican en una cuenta Premium de Azure Storage, debe aprovisionar una cuenta Estándar de Azure Storage adicional para el almacenamiento de los registros. Se puede usar una única cuenta de almacenamiento de registros estándar para varias cuentas de almacenamiento de replicación premium. Las máquinas virtuales replicadas en las cuentas de almacenamiento estándar usan la misma cuenta de almacenamiento para los registros.

**Nombre de cuenta de registros sugerido** indica cómo debe ser el nombre de la cuenta de Azure Storage de registros después de incluir el prefijo sugerido. Sustituya el nombre entre < > por los datos personalizados.

**Resumen de la selección de ubicación** proporciona un resumen de la carga total de máquinas virtuales en la cuenta de Azure Storage durante la replicación y la conmutación por error o la conmutación por error de prueba. Incluye el número total de máquinas virtuales asignadas a la cuenta de Azure Storage, el total de IOPS de lectura/escritura en todas las máquinas virtuales que se colocan en esta cuenta de Azure Storage, el valor de IOPS total de escritura (replicación), el tamaño total aprovisionado entre todos los discos y el número total de discos.

**Máquinas virtuales que se van a colocar** enumera todas las máquinas virtuales que se deben colocar en la cuenta de Azure Storage especificada para obtener un rendimiento óptimo.

##<a name="compatible-vms"></a>Máquinas virtuales compatibles
![Deployment Planner](./media/site-recovery-deployment-planner/compatible-vms.png)

**Nombre de máquina virtual** es el nombre de la máquina virtual o la dirección IP como se utiliza en VMListFile durante la generación de informes. Esta columna también muestra los discos (VMDK) conectados a las máquinas virtuales.

**Compatibilidad de máquina virtual** tiene dos valores: Sí / Sí*. Sí* es para los casos en los que la máquina virtual es adecuada para una cuenta [Premium de Azure Storage](https://aka.ms/premium-storage-workload) con una alta actividad de datos de generación de perfiles o un disco de IOPS que se puede incluir en la categoría P20 o P30, pero el tamaño del disco hace que se asigne a una categoría P10 o P20. Azure Storage decide qué tipo de disco de almacenamiento premium asignar a un disco según su tamaño. Por ejemplo, < 128 GB pertenece a una categoría P10, de 128 a 512 GB pertenece a una categoría P20 y de 512 a 1023 GB, a una categoría P30. Por tanto, si las características de la carga de trabajo de un disco lo sitúan en una categoría P20 o P30, pero el tamaño le asigna un tipo de disco de almacenamiento premium inferior, la herramienta marcará la máquina virtual con una marca Sí * y le recomendará que cambie el tamaño del disco de origen para adecuarse al tipo de disco de almacenamiento premium recomendado correcto o que cambie el tipo de disco de destino después de la conmutación por error.
El tipo de cuenta de Azure Storage es Estándar o Premium.

**Prefijo sugerido** es el prefijo de tres caracteres de la cuenta de Azure Storage

**Cuenta de Azure Storage** es el nombre de la cuenta que incluye el prefijo sugerido

**IOPS de lectura/escritura (con factor de crecimiento)** es el valor de IOPS máximo de la carga de trabajo en el disco (el percentil 95 es el predeterminado) incluido el factor de crecimiento futuro (el valor predeterminado es el 30%). Tenga en cuenta que el total de IOPS de lectura/escritura de la máquina virtual no siempre va a ser la suma de IOPS de lectura/escritura de los discos individuales de la máquina virtual, ya que el máximo de IOPS de lectura/escritura de la máquina virtual es el máximo de la suma de sus IOPS de lectura/escritura de los discos individuales durante cada minuto del período de generación de perfiles.

**Actividad de datos (con factor de crecimiento)** es el valor de actividad máximo en el disco (el percentil 95 es el predeterminado) incluido el factor de crecimiento futuro (el valor predeterminado es el 30%). Tenga en cuenta que la actividad de datos total de la máquina virtual no siempre va a ser la suma de la actividad de datos de los discos individuales de la máquina virtual, ya que la actividad de datos máxima de la máquina virtual es el máximo de la suma de las actividades de los discos individuales durante cada minuto del período de generación de perfiles.

**Tamaño de la máquina virtual de Azure** es el tamaño ideal de la máquina virtual de Azure Compute asignada para esta máquina virtual local. Esta asignación se realiza en función de la memoria de la máquina virtual local, el número de discos, núcleos o NIC y el valor de IOPS de lectura/escritura. La recomendación es siempre usar el menor tamaño de máquina virtual de Azure que coincida con todas estas características de la máquina virtual local.

**Número de discos** es el número total de discos (VMDK) de la máquina virtual

**Tamaño de disco (GB)** es el tamaño total aprovisionado de todos los discos de la máquina virtual. La herramienta también muestra el tamaño del disco para los discos individuales de la máquina virtual.

**Núcleos** es el número de núcleos de CPU de la máquina virtual.

**Memoria (MB)** es la RAM de la máquina virtual.

**NIC** es el número de NIC de la máquina virtual.

##<a name="incompatible-vms"></a>Máquinas virtuales incompatibles

![Deployment Planner](./media/site-recovery-deployment-planner/incompatible-vms.png)

**Nombre de máquina virtual** es el nombre de la máquina virtual o la dirección IP como se utiliza en VMListFile durante la generación de informes. Esta columna también muestra los discos (VMDK) conectados a las máquinas virtuales.

**Compatibilidad de la máquina virtual** indica por qué la máquina virtual especificada no es compatible para su uso con Azure Site Recovery. Los motivos se especifican para cada disco incompatible de la máquina virtual y puede ser uno de los siguientes en función de los [límites](https://aka.ms/azure-storage-scalbility-performance) de almacenamiento publicados de Azure Storage.

* Tamaño de disco > 1023 GB: Azure Storage no admite actualmente tamaños de disco > 1 TB
* El tamaño total de máquina virtual (replicación + TFO) supera el límite de tamaño admitido de la cuenta de Azure Storage (35 TB). Esto suele suceder cuando hay un único disco en la máquina virtual que tiene algunas características de rendimiento que superan el límite máximo admitido de almacenamiento estándar de Microsoft Azure o Azure Site Recovery que hace que la máquina virtual quede incluida en la categoría de almacenamiento Premium. Sin embargo, el tamaño máximo admitido de una cuenta Premium de Azure Storage es de 35 TB y no se puede proteger una única máquina virtual protegida en varias cuentas de almacenamiento. Tenga en cuenta también que cuando se ejecuta una TFO (conmutación por error de prueba) en una máquina virtual protegida, se ejecuta en la misma cuenta de almacenamiento donde se está realizando la replicación. Por ello, necesitaremos aprovisionar el doble de tamaño del disco para que, de forma paralela, continúe la replicación y la conmutación por error de prueba sea correcta.
* El valor de IOPS de origen supera el límite admitido de Azure Storage de 5000 por disco
* El valor de IOPS de origen supera el límite admitido de Azure Storage de 80 000 por máquina virtual
* La actividad de datos media supera el límite admitido de Azure Site Recovery de 10 MBps para el tamaño medio de E/S de disco
* La actividad de datos total en todos los discos de la máquina virtual supera el límite máximo admitido de Azure Site Recovery de 54 MBps por máquina virtual
* El valor de IOPS de escritura efectiva medio supera el límite admitido de Azure Site Recovery de 840 por disco
* El almacenamiento de instantáneas estimado supera el límite admitido de 10 TB

**IOPS de lectura/escritura (con factor de crecimiento)** es el valor de IOPS máximo de la carga de trabajo en el disco (el percentil 95 es el predeterminado) incluido el factor de crecimiento futuro (el valor predeterminado es el 30%). Tenga en cuenta que el total de IOPS de lectura/escritura de la máquina virtual no siempre va a ser la suma de IOPS de lectura/escritura de los discos individuales de la máquina virtual, ya que el máximo de IOPS de lectura/escritura de la máquina virtual es el máximo de la suma de sus IOPS de lectura/escritura de los discos individuales durante cada minuto del período de generación de perfiles.

**Actividad de datos (con factor de crecimiento)** es el valor de actividad máximo en el disco (el percentil 95 es el predeterminado) incluido el factor de crecimiento futuro (el valor predeterminado es el 30%). Tenga en cuenta que la actividad de datos total de la máquina virtual no siempre va a ser la suma de la actividad de datos de los discos individuales de la máquina virtual, ya que la actividad de datos máxima de la máquina virtual es el máximo de la suma de las actividades de los discos individuales durante cada minuto del período de generación de perfiles.

**Número de discos** es el número total de discos (VMDK) de la máquina virtual

**Tamaño de disco (GB)** es el tamaño total aprovisionado de todos los discos de la máquina virtual. La herramienta también muestra el tamaño del disco para los discos individuales de la máquina virtual.

**Núcleos** es el número de núcleos de CPU de la máquina virtual.

**Memoria (MB)** es la RAM de la máquina virtual.

**NIC** es el número de NIC de la máquina virtual.


##<a name="azure-site-recovery-limits"></a>Límites de Azure Site Recovery

**Destino de almacenamiento de la replicación** | **Tamaño medio de E/S de disco de origen** |**Actividad de datos media de disco de origen** | **Actividad de datos de disco de origen total por día**
---|---|---|---
Standard storage | 8 KB    | 2 MB/s | 168 GB por disco
Disco P10 Premium | 8 KB    | 2 MB/s | 168 GB por disco
Disco P10 Premium | 16 KB | 4 MB/s |    336 GB por disco
Disco P10 Premium | 32 KB o superior | 8 MB/s | 672 GB por disco
Disco P20/P30 Premium | 8 KB    | 5 MB/s | 421 GB por disco
Disco P20/P30 Premium | 16 KB o superior |10 MB/s    | 842 GB por disco


Estos son los números promedio suponiendo una superposición de E/S del 30%. Azure Site Recovery es capaz de administrar un mayor rendimiento en función de la relación de superposición, los tamaños más grandes de escritura y el comportamiento real de E/S de la carga de trabajo. Los números anteriores presuponen un trabajo pendiente típico de aproximadamente 5 minutos, es decir, se procesarán los datos una vez cargados y se creará un punto de recuperación en menos de 5 minutos.

Los límites publicados anteriores se basan en nuestras pruebas, pero no pueden cubrir todas las combinaciones de E/S posibles de la aplicación. Los resultados reales variarán en función de la combinación de E/S de la aplicación. Para obtener mejores resultados, incluso después de la planeación de la implementación, es aconsejable que realice pruebas en profundidad a la aplicación mediante la conmutación por error de prueba para obtener una imagen de rendimiento real.

##<a name="release-notes"></a>Notas de la versión
La versión pública preliminar de Azure Site Recovery Deployment Planner 1.0 presenta los siguientes problemas conocidos que se solucionarán en próximas actualizaciones.

* La herramienta funciona solo para el escenario de VMware a Azure pero no para las implementaciones de Hyper-V en Azure. Para el escenario de Hyper-V en Azure se utiliza la [herramienta de planeación de capacidad de Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
* No se admite la operación GetThroughput en las regiones de Microsoft Azure de gobierno de Estados Unidos y China.
* La herramienta no puede generar perfiles de máquinas virtuales si vCenter tiene dos o más máquinas virtuales con el mismo nombre o dirección IP entre los diferentes hosts de ESXi. En esta versión, la herramienta omite la generación de perfiles para aquellos nombres o direcciones IP de máquinas virtuales que aparezcan duplicados en VMListFile. La solución alternativa consiste en generar el perfil de las máquinas virtuales con el host de ESXi en lugar de con el servidor de vCenter. Debe ejecutar una instancia para cada host de ESXi.

