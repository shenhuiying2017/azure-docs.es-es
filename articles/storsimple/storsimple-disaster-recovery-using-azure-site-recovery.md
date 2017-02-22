---
title: "Automatización de recuperación ante desastres para recursos compartidos de archivos de StorSimple con Azure Site Recovery | Microsoft Docs"
description: "Se describen los pasos y las prácticas recomendadas para crear una solución de recuperación ante desastres para recursos compartidos de archivos hospedados en el almacenamiento de Microsoft Azure StorSimple."
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/16/2016
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: c78eda33a64d630ba18aba6a174db372eb41dde9
ms.openlocfilehash: a0817ac21afb83745ec2cc213fc6029fa395d479


---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Solución de recuperación ante desastres automatizada con Azure Site Recovery para recursos compartidos de archivos alojados en StorSimple
## <a name="overview"></a>Información general
Microsoft Azure StorSimple es una solución de almacenamiento en la nube híbrida que aborda las dificultades de los datos no estructurados asociados normalmente a recursos compartidos de archivos. StorSimple usa el almacenamiento en la nube como una extensión de la solución local y organiza los datos en niveles automáticamente entre el almacenamiento local y el almacenamiento en la nube. La protección de datos integrada, con instantáneas locales y de nube, elimina la necesidad de una extensa infraestructura de almacenamiento.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) es un servicio basado en Azure que proporciona capacidades de recuperación ante desastres (DR) mediante la organización de la replicación, la conmutación por error y la recuperación de máquinas virtuales. Azure Site Recovery es compatible con una serie de tecnologías de replicación para replicar, proteger y conmutar por error de forma coherente y sin problemas máquinas virtuales y aplicaciones en nubes públicas o privadas, u hospedadas.

Con Azure Site Recovery, la replicación de máquinas virtuales y las capacidades de instantánea de nube de StorSimple, puede proteger el entorno del servidor de archivos al completo. En caso de una interrupción, basta con un solo clic para poner los recursos compartidos de archivos en línea en Azure en unos minutos.

En este documento se explica en detalle cómo crear una solución de recuperación ante desastres para los recursos compartidos de archivos hospedados en el almacenamiento de StorSimple, así como realizar conmutaciones por error planeadas, no planeadas y de prueba usando un plan de recuperación de un solo clic. Básicamente, se muestra cómo se puede modificar el plan de recuperación en el almacén de Azure Site Recovery para habilitar las conmutaciones por error de StorSimple durante escenarios de desastre. Además, se describen los requisitos previos y las configuraciones compatibles. En este documento se da por supuesto que está familiarizado con los aspectos básicos de las arquitecturas de Azure Site Recovery y StorSimple.

## <a name="supported-azure-site-recovery-deployment-options"></a>Opciones de implementación de Azure Site Recovery compatibles
Los clientes pueden implementar servidores de archivos como servidores físicos o máquinas virtuales (VM) que se ejecutan en Hyper-V o VMware y, después, crear recursos compartidos de archivos desde volúmenes extraídos del almacenamiento de StorSimple. Azure Site Recovery puede proteger las implementaciones físicas y virtuales en un sitio secundario o en Azure. En este documento se abordan los detalles de una solución de recuperación ante desastres con Azure como el sitio de recuperación para una VM de servidor de archivos hospedada en Hyper-V y con recursos compartidos de archivos en el almacenamiento de StorSimple. Los distintos escenarios en los que la VM de servidor de archivos está en una VM de VMware o en una máquina física se pueden implementar de forma similar.

## <a name="prerequisites"></a>Requisitos previos
Para implementar una solución de recuperación ante desastres con un solo clic que usa Azure Site Recovery para recursos compartidos de archivos hospedados en el almacenamiento de StorSimple, se deben cumplir los siguientes requisitos previos:

* VM de servidor de archivos Windows Server 2012 R2 local hospedada en Hyper-V, VMware o en una máquina física
* Dispositivo de almacenamiento StorSimple local registrado con Azure StorSimple Manager
* StorSimple Cloud Appliance creado en Azure StorSimple Manager (puede mantenerse apagado)
* Recursos compartidos de archivos hospedados en los volúmenes configurados en el dispositivo de almacenamiento de StorSimple
* [Almacén de los servicios de Azure Site Recovery](../site-recovery/site-recovery-vmm-to-vmm.md) creado en una suscripción de Microsoft Azure

Además, si Azure es su sitio de recuperación, ejecute la [herramienta de evaluación de disponibilidad de máquinas virtuales de Azure](http://azure.microsoft.com/downloads/vm-readiness-assessment/) en las máquinas virtuales para asegurarse de que son compatibles con las máquinas virtuales de Azure y los servicios de Azure Site Recovery.

Para evitar problemas de latencia (que podrían ocasionar mayores costes), asegúrese de que crea su StorSimple Cloud Appliance, la cuenta de automatización y las cuentas de almacenamiento en la misma región.

## <a name="enable-dr-for-storsimple-file-shares"></a>Habilitación de la recuperación ante desastres para recursos compartidos de archivos de StorSimple
Cada componente del entorno local debe protegerse para permitir una replicación y recuperación completas. En esta sección se describe cómo llevar a cabo las siguientes acciones:

* Configuración de la replicación de DNS y Active Directory (opcional)
* Uso de Azure Site Recovery para habilitar la protección de la VM de servidor de archivos
* Habilitación de la protección de volúmenes de StorSimple
* Configuración de la red

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Configuración de la replicación de DNS y Active Directory (opcional)
Si desea proteger los equipos que ejecutan Active Directory y DNS para que estén disponibles en el sitio de recuperación ante desastres, debe protegerlos explícitamente (de modo que los servidores de archivos estén accesibles después de la conmutación por error con autenticación). Hay dos opciones recomendadas según la complejidad del entorno local del cliente.

#### <a name="option-1"></a>Opción 1
Si el cliente tiene un número pequeño de aplicaciones, un único controlador de dominio para todo el sitio local y realizará conmutaciones por error en todo el sitio, se recomienda utilizar la replicación de Azure Site Recovery para replicar la máquina del controlador de dominio en un sitio secundario (esto se aplica tanto de sitio a sitio como de sitio a Azure).

#### <a name="option-2"></a>Opción 2
Si el cliente tiene un gran número de aplicaciones, ejecuta un bosque de Active Directory y realizará conmutaciones por error de unas pocas aplicaciones a la vez, se recomienda configurar un controlador de dominio adicional en el sitio de recuperación ante desastres (ya sea un sitio secundario o Azure).

Consulte [Protección de Active Directory y DNS con Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) para obtener instrucciones sobre cómo poner un controlador de dominio disponible en el sitio de recuperación ante desastres. Para el resto de este documento, supondremos que habrá un controlador de dominio disponible en el sitio de recuperación ante desastres.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Uso de Azure Site Recovery para habilitar la protección de la VM de servidor de archivos
Para llevar a cabo este paso, debe preparar el entorno del servidor de archivos local, crear y preparar un almacén de Azure Site Recovery y habilitar la protección de archivos de la máquina virtual.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Para preparar el entorno del servidor de archivos local
1. Establezca el valor de **Control de cuentas de usuario** como **No notificarme nunca**. Esto resulta necesario a fin de que pueda usar scripts de automatización de Azure para conectar los destinos iSCSI tras la conmutación por error de Azure Site Recovery.

   1. Presione la tecla Windows + Q y busque **UAC**.
   2. Seleccione **Cambiar configuración de Control de cuentas de usuario**.
   3. Arrastre la barra hacia la parte inferior, hacia **Never Notify**(No notificarme nunca).
   4. Haga clic en **Aceptar** y, después, seleccione **Sí** cuando se le solicite.

      ![](./media/storsimple-dr-using-asr/image1.png)
2. Instale al agente de máquina virtual en cada una de las máquinas virtuales de servidor de archivos. Esto resulta necesario para que pueda ejecutar scripts de automatización de Azure en las máquinas virtuales conmutadas por error.

   1. [Descargue el agente](http://aka.ms/vmagentwin) en `C:\\Users\\<username>\\Downloads`.
   2. Abra Windows PowerShell en modo de administrador (Ejecutar como administrador) y, después, escriba el siguiente comando para ir a la ubicación de descarga:

      `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

      > [!NOTE]
      > El nombre del archivo puede cambiar según la versión.
      >
      >
3. Haga clic en **Next**.
4. Acepte los **Terms of Agreement** (Términos del contrato) y, después, haga clic en **Siguiente**.
5. Haga clic en **Finalizar**
6. Cree recursos compartidos de archivos con volúmenes extraídos del almacenamiento de StorSimple. Para obtener más información, consulte [Uso del servicio StorSimple Manager para administrar volúmenes](storsimple-manage-volumes.md).

   1. En las máquinas virtuales locales, presione la tecla Windows + Q y busque **iSCSI**.
   2. Seleccione **iniciador iSCSI**.
   3. Seleccione la pestaña **Configuración** y copie el nombre del iniciador.
   4. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/).
   5. Seleccione la pestaña **StorSimple** y, después, seleccione el servicio StorSimple Manager que contiene el dispositivo físico.
   6. Cree contenedores de volumen y, luego, volúmenes. (Estos volúmenes son para los recursos compartidos de archivos de las máquinas virtuales de servidor de archivos). Copie el nombre del iniciador y asigne un nombre adecuado a los registros de control de acceso al crear los volúmenes.
   7. Seleccione la pestaña **Configurar** y apunte la dirección IP del dispositivo.
   8. En las máquinas virtuales locales, vaya a la opción **iniciador iSCSI** de nuevo y escriba la dirección IP en la sección Conexión rápida. Haga clic en **Conexión rápida** (ahora debe aparecer conectado el dispositivo).
   9. Abra el Portal de administración de Azure y seleccione la pestaña **Volúmenes y dispositivos** . Haga clic en **Autoconfigurar**. Debería aparecer el volumen que acaba de crear.
   10. En el portal, seleccione la pestaña **Dispositivos** y, después, seleccione **Create a New Virtual Device** (Crear un nuevo dispositivo virtual). (Este dispositivo virtual se utilizará si se produce una conmutación por error). Este nuevo dispositivo virtual puede mantenerse en un estado sin conexión para evitar costos adicionales. Para desconectar el dispositivo virtual, vaya a la sección **Máquinas virtuales** en el Portal y apáguelo.
   11. Vuelva a las máquinas virtuales locales y abra Administración de discos (presione la tecla Windows + X y seleccione **Administración de discos**).
   12. Observará algunos discos adicionales (en función del número de volúmenes que haya creado). Haga clic con el botón derecho en el primero, seleccione **Inicializar disco** y, luego, **Aceptar**. Haga clic con el botón derecho en la sección **No asignado**, seleccione **Nuevo volumen simple**, asígnele una letra de unidad y finalice el asistente.
   13. Repita el paso l para todos los discos. Ahora podrá ver todos los discos en **Este equipo** en el Explorador de Windows.
   14. Utilice el rol Servicios de archivos y almacenamiento para crear recursos compartidos de archivos en estos volúmenes.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Para crear y preparar un almacén de Azure Site Recovery
Consulte la [documentación de Azure Site Recovery](../site-recovery/site-recovery-hyper-v-site-to-azure.md) para empezar a trabajar con Azure Site Recovery antes de proteger la máquina virtual de servidor de archivos.

#### <a name="to-enable-protection"></a>Para habilitar la protección
1. Desconecte los destinos iSCSI de las máquinas virtuales locales que desea proteger a través de Azure Site Recovery:

   1. Presione la tecla Windows + Q y busque **iSCSI**.
   2. Seleccione **Configurar iniciador iSCSI**.
   3. Desconecte el dispositivo StorSimple que ha conectado anteriormente. También puede desactivar el servidor de archivos durante unos minutos mientras habilita la protección.

   > [!NOTE]
   > Esto provocará que los recursos compartidos de archivos no estén disponibles temporalmente.
   >
   >
2. Seleccione [Habilitar protección de máquina virtual](../site-recovery/site-recovery-hyper-v-site-to-azure.md#step-6-enable-replication) de la máquina virtual de servidor de archivos desde el portal de Azure Site Recovery.
3. Cuando comience la sincronización inicial, podrá volver a conectar el destino. Vaya al iniciador iSCSI, seleccione el dispositivo StorSimple y haga clic en **Conectar**.
4. Cuando la sincronización se haya completado y el estado de la máquina virtual sea **Protegido**, seleccione la máquina virtual, seleccione la pestaña **Configurar** y actualice la red de la máquina virtual del mismo modo (esta es la red de la que formarán parte las máquinas virtuales conmutadas por error). Si la red no aparece, significa que la sincronización todavía está en curso.

### <a name="enable-protection-of-storsimple-volumes"></a>Habilitación de la protección de volúmenes de StorSimple
Si no ha seleccionado la opción **Habilitar una copia de seguridad predeterminada para este volumen** para los volúmenes de StorSimple, vaya a **Directivas de copia de seguridad** en el servicio StorSimple Manager y cree una directiva de copia de seguridad adecuada para todos los volúmenes. Se recomienda que establezca la frecuencia de las copias de seguridad en el objetivo de punto de recuperación (RPO) que le gustaría ver para la aplicación.

### <a name="configure-the-network"></a>Configuración de la red
Para la máquina virtual de servidor de archivos, configure las opciones de red en Azure Site Recovery de forma que las redes de VM estén conectadas a la red de recuperación ante desastres correcta después de la conmutación por error.

Puede seleccionar la máquina virtual en **Nube VMM** o **Grupo de protección** para configurar las opciones de red, como se muestra en la siguiente ilustración.

![](./media/storsimple-dr-using-asr/image2.png)

## <a name="create-a-recovery-plan"></a>Creación de un plan de recuperación
Puede crear un plan de recuperación en ASR para automatizar el proceso de conmutación por error de los recursos compartidos de archivos. Si se produce una interrupción, puede poner los recursos compartidos de archivos en funcionamiento en minutos con un solo clic. Para habilitar esta automatización, necesitará una cuenta de Automatización de Azure.

#### <a name="to-create-the-account"></a>Para crear la cuenta
1. Vaya al Portal de Azure clásico y a la sección **Automatización** .
2. Cree una nueva cuenta de automatización. Manténgala en la misma geoárea o región en la que se crearon las cuentas de almacenamiento y el StorSimple Cloud Appliance.
3. Haga clic en **Nuevo**&gt;**App Services**&gt;**Automatización**&gt;**Runbook**&gt;**De la galería** para importar todos los runbooks necesarios en la cuenta de automatización.

   ![](./media/storsimple-dr-using-asr/image3.png)
4. Agregue los siguientes runbooks desde el panel **Recuperación ante desastres** de la galería:

   * Conmutación por error de contenedores de volúmenes de StorSimple
   * Limpieza de los volúmenes de StorSimple después de la conmutación por error de prueba (TFO)
   * Montaje de volúmenes en el dispositivo StorSimple después de la conmutación por error
   * Inicio de StorSimple Virtual Appliance
   * Desinstalación de la extensión de script personalizada en la máquina virtual de Azure

     ![](./media/storsimple-dr-using-asr/image4.png)
5. Publique los scripts seleccionando el runbook en la cuenta de automatización y yendo a la pestaña **Autor** . Después de este paso, aparecerá la pestaña **Runbooks** de la siguiente forma:

    ![](./media/storsimple-dr-using-asr/image5.png)
6. En la cuenta de automatización, vaya a la pestaña **Recursos**, haga clic en **Agregar configuración** &gt; **Agregar credenciales** y agregue sus credenciales de Azure. Asigne al recurso el nombre credencialAzure.

   Utilice la credencial de Windows PowerShell. Debe tratarse de una credencial que contenga un nombre de usuario de identificador de organización y una contraseña con acceso a esta suscripción de Azure y con la autenticación multifactor deshabilitada. Esto resulta necesario para realizar la autenticación en nombre del usuario durante las conmutaciones por error y para que aparezcan los volúmenes del servidor de archivos en el sitio de recuperación ante desastres.
7. En la cuenta de automatización, seleccione la pestaña **Recursos** y, después, haga clic en **Agregar configuración**&gt;**Agregar variable** y agregue las siguientes variables. Puede elegir cifrar estos activos. Estas variables son específicas del plan de recuperación. Si el plan de recuperación (que creará en el paso siguiente) se denomina planDePrueba, las variables deben ser planDePrueba-StorSimClaveReg, planDePrueba-nombreDeSuscripciónDeAzure y así sucesivamente.

   * *nombreDelPlanDeRecuperación***-StorSimRegKey**: la clave de registro para el servicio StorSimple Manager.
   * *nombreDelPlanDeRecuperación***-AzureSubscriptionName**: el nombre de la suscripción de Azure.
   * *nombreDelPlanDeRecuperación***-ResourceName**: el nombre del recurso de StorSimple que tiene el dispositivo StorSimple.
   * *nombreDelPlanDeRecuperación***-DeviceName**: el dispositivo que debe conmutarse por error.
   * *nombreDelPlanDeRecuperación***-TargetDeviceName**: StorSimple Cloud Appliance en el que se deben conmutar por error los contenedores.
   * *nombreDelPlanDeRecuperación***-VolumeContainers**: una cadena separada por comas de contenedores de volúmenes presentes en el dispositivo que deben conmutarse por error; por ejemplo, contDeVol1,contDeVol2,contDeVol3.
   * nombreDelPlanDeRecuperación**-TargetDeviceDnsName**: el nombre de servicio del dispositivo de destino (se puede encontrar en la sección **Máquina virtual**: el nombre de servicio es el mismo que el de DNS).
   * *nombreDelPlanDeRecuperación***-StorageAccountName**: el nombre de la cuenta de almacenamiento en la que se almacenará el script (que debe ejecutarse en la máquina virtual conmutada por error). Puede tratarse de cualquier cuenta de almacenamiento que tenga suficiente espacio como para almacenar temporalmente el script.
   * *nombreDelPlanDeRecuperación***-StorageAccountKey**: la clave de acceso para la cuenta de almacenamiento anterior.
   * *nombreDelPlanDeRecuperación***-ScriptContainer**: el nombre del contenedor en el que se almacenará el script en la nube. Si el contenedor no existe, se creará.
   * *nombreDelPlanDeRecuperación***-VMGUIDS**: al proteger una máquina virtual, Azure Site Recovery asigna a cada VM un identificador único que ofrece los detalles de la máquina virtual conmutada por error. Para obtener el VMGUID, seleccione la pestaña **Servicios de recuperación** y, después, haga clic en **Elemento protegido**&gt;**Grupos de protección**&gt;**Máquinas**&gt;**Propiedades**. Si tiene varias máquinas virtuales, después, agregue los GUID como una cadena separada por comas.
   * *nombreDelPlanDeRecuperación***-AutomationAccountName**: el nombre de la cuenta de automatización en la que ha agregado los runbooks y los recursos.

   Por ejemplo, si el nombre del plan de recuperación es fileServerpredayRP, la pestaña **Activos** debería aparecer de la siguiente forma después de agregar todos los activos.

   ![](./media/storsimple-dr-using-asr/image6.png)
8. Vaya a la sección **Servicios de recuperación** y seleccione el almacén de Azure Site Recovery que creó anteriormente.
9. Seleccione la pestaña **Planes de recuperación** y cree un nuevo plan de recuperación de la siguiente forma:

   a.  Especifique un nombre y seleccione el **Grupo de protección**adecuado.

   b.  Seleccione las máquinas virtuales del grupo de protección que desea incluir en el plan de recuperación.

   c.  Tras crear el plan de recuperación, selecciónelo para abrir la vista de personalización del plan de recuperación.

   d.  Seleccione **Cierre de todos los grupos**, haga clic en **Script** y elija **Add a primary side script before all Group shutdown** (Agregar un script del primario antes del cierre de todos los grupos).

   e.  Seleccione la cuenta de automatización (en la que ha agregado los runbooks) y, después, seleccione el runbook **Fail over-StorSimple-Volume-Containers** .

   f.  Haga clic en **Grupo 1: Inicio**, seleccione **Máquinas virtuales** y agregue aquellas que deben protegerse en el plan de recuperación.

   g.  Haga clic en **Grupo 1: Inicio**, seleccione **Script** y agregue todos los siguientes scripts en orden, como pasos de **After Group 1** (Tras el grupo 1).

   * Runbook Start-StorSimple-Virtual-Appliance
   * Runbook Fail over-StorSimple-volume-containers
   * Runbook Mount-volumes-after-failover
   * Runbook Uninstall-custom-script-extension
10. Agregue una acción manual después de los 4 scripts anteriores en la misma sección **Grupo 1: Pasos posteriores** . Esta acción es el punto en el que puede comprobar que todo funciona correctamente. Debe agregarse solo como parte de la conmutación por error de prueba (por tanto, seleccione solo la casilla **Probar conmutación por error** ).
11. Después de la acción manual, agregue el script de limpieza con el mismo procedimiento que usó para los otros runbooks. Guarde el plan de recuperación.

    > [!NOTE]
    > Cuando ejecute una conmutación por error de prueba, debe comprobar todo en el paso de acción manual porque una vez completada la acción manual, se eliminarán como parte de la limpieza los volúmenes de StorSimple clonados en el dispositivo de destino.
    >
    >

    ![](./media/storsimple-dr-using-asr/image7.png)

## <a name="perform-a-test-failover"></a>Realización de una conmutación por error de prueba
Consulte la guía complementaria [Protección de Active Directory y DNS con Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) para obtener consideraciones específicas para Active Directory durante la conmutación por error de prueba. La configuración local no se ve afectada cuando se produce la conmutación por error de prueba. Los volúmenes de StorSimple conectados a la máquina virtual local se clonan en el StorSimple Cloud Appliance en Azure. Aparecerá una máquina virtual para fines de pruebas en Azure y se conectarán los volúmenes clonados a la máquina virtual.

#### <a name="to-perform-the-test-failover"></a>Para realizar la conmutación por error de prueba
1. En el Portal de Azure clásico, seleccione su almacén de recuperación del sitio.
2. Haga clic en el plan de recuperación creado para la máquina virtual de servidor de archivos.
3. Haga clic en **Probar conmutación por error**.
4. Seleccione la red virtual para iniciar el proceso de conmutación por error de prueba.

   ![](./media/storsimple-dr-using-asr/image8.png)
5. Cuando el entorno secundario esté activo, podrá realizar sus validaciones.
6. Una vez completadas las validaciones, haga clic en **Validations Complete**(Validaciones completas). Se limpiará el entorno de conmutación por error de prueba y se completará la operación de TFO.

## <a name="perform-an-unplanned-failover"></a>Realización de una conmutación por error no planeada
Durante una conmutación por error no planeada, los volúmenes de StorSimple se conmutan por error en el dispositivo virtual, aparece una máquina virtual de réplica en Azure y se conectan los volúmenes a la máquina virtual.

#### <a name="to-perform-an-unplanned-failover"></a>Para realizar una conmutación por error no planeada
1. En el Portal de Azure clásico, seleccione su almacén de recuperación del sitio.
2. Haga clic en el plan de recuperación creado para la máquina virtual de servidor de archivos.
3. Haga clic en **Conmutación por error** y, después, seleccione **Conmutación por error no planeada**.

   ![](./media/storsimple-dr-using-asr/image9.png)
4. Seleccione la red de destino y, después, haga clic en el icono de marca de verificación ✓ para iniciar el proceso de conmutación por error.

## <a name="perform-a-planned-failover"></a>Realización de una conmutación por error planeada
Durante una conmutación por error planeada, la máquina virtual de servidor de archivos local se apaga correctamente y se realiza una instantánea de copia de seguridad en la nube de los volúmenes del dispositivo StorSimple. Estos se conmutan por error en el dispositivo virtual, aparece una máquina virtual de réplica en Azure y se conectan los volúmenes a la máquina virtual.

#### <a name="to-perform-a-planned-failover"></a>Para realizar una conmutación por error planeada
1. En el Portal de Azure clásico, seleccione su almacén de recuperación del sitio.
2. Haga clic en el plan de recuperación creado para la máquina virtual de servidor de archivos.
3. Haga clic en **Conmutación por error** y, después, seleccione **Conmutación por error planeada**.
4. Seleccione la red de destino y, después, haga clic en el icono de marca de verificación ✓ para iniciar el proceso de conmutación por error.

## <a name="perform-a-failback"></a>Realización de una conmutación por recuperación
Durante una conmutación por recuperación, los contenedores de volúmenes de StorSimple se conmutan por error de vuelta al dispositivo físico después de realizar una copia de seguridad.

#### <a name="to-perform-a-failback"></a>Para realizar una conmutación por recuperación
1. En el Portal de Azure clásico, seleccione su almacén de recuperación del sitio.
2. Haga clic en el plan de recuperación creado para la máquina virtual de servidor de archivos.
3. Haga clic en **Conmutación por error** y seleccione **Conmutación por error planeada** o **Conmutación por error no planeada**.
4. Haga clic en **Cambiar dirección**.
5. Seleccione la sincronización de datos adecuada y las opciones de creación de máquinas virtuales.
6. Haga clic en el icono de marca de verificación ✓ para iniciar el proceso de conmutación por recuperación.

   ![](./media/storsimple-dr-using-asr/image10.png)

## <a name="best-practices"></a>Prácticas recomendadas
### <a name="capacity-planning-and-readiness-assessment"></a>Evaluación de disponibilidad y planeamiento de capacidad
#### <a name="hyper-v-site"></a>Sitio de Hyper-V
Utilice la [herramienta de planeamiento de capacidad de usuario](http://www.microsoft.com/download/details.aspx?id=39057) para diseñar el servidor, el almacenamiento y la infraestructura de red de su entorno de réplica de Hyper-V.

#### <a name="azure"></a>Las tablas de Azure
Puede ejecutar la [herramienta de evaluación de disponibilidad de máquinas virtuales de Azure](http://azure.microsoft.com/downloads/vm-readiness-assessment/) en las máquinas virtuales para asegurarse de que son compatibles con las máquinas virtuales de Azure y los servicios de Azure Site Recovery. La herramienta de evaluación de disponibilidad comprueba las configuraciones de máquina virtual y emite una advertencia cuando las configuraciones son incompatibles con Azure. Por ejemplo, emite una advertencia si la unidad C: es mayor de 127 GB.

El planeamiento de capacidad se compone de al menos dos procesos importantes:

* Asignación de máquinas virtuales Hyper-V locales a tamaños de máquina virtual de Azure (como A6, A7, A8 y A9).
* Determinación del ancho de banda de Internet necesario.

## <a name="limitations"></a>Limitaciones
* Actualmente solo se puede conmutar por error un dispositivo StorSimple (en un único StorSimple Cloud Appliance). Aún no se admite un servidor de archivos que abarque varios dispositivos StorSimple.
* Si recibe un error al habilitar la protección de una máquina virtual, asegúrese de que se han desconectado los destinos iSCSI.
* Todos los contenedores de volúmenes que se han agrupado debido a las directivas de copia de seguridad que abarcan distintos contenedores de volúmenes se conmutarán por error de forma conjunta.
* Todos los volúmenes de los contenedores de volúmenes que ha elegido se conmutarán por error.
* Los volúmenes que suman más de 64 TB no se pueden conmutar por error, ya que la capacidad máxima de un único StorSimple Cloud Appliance es de 64 TB.
* Si se produce un error en la conmutación por error planeada o no planeada y las máquinas virtuales se crean en Azure, no limpie las máquinas virtuales. En su lugar, realice una conmutación por recuperación. Si elimina las máquinas virtuales, no se podrán volver a activar las máquinas virtuales locales.
* Después de una conmutación por error, si no se pueden ver los volúmenes, vaya a las máquinas virtuales, abra Administración de discos, vuelva a examinar los discos y póngalos en línea.
* En algunos casos, las letras de unidad del sitio de recuperación ante desastres pueden ser diferentes de las letras locales. En tales casos, debe corregir manualmente el problema una vez finalizada la conmutación por error.
* Debe deshabilitar la autenticación multifactor para la credencial de Azure que se ha especificado en la cuenta de automatización como un activo. Si no se deshabilita esta autenticación, no se podrán ejecutar automáticamente los scripts y se producirá un error en el plan de recuperación.
* Tiempo de espera del trabajo de conmutación por error: se agotará el tiempo de espera del script de StorSimple si la conmutación por error de los contenedores de volúmenes tarda más que el límite de Azure Site Recovery por script (actualmente, 120 minutos).
* Tiempo de espera del trabajo de copia de seguridad: se agotará el tiempo de espera del script de StorSimple si la copia de seguridad de los volúmenes tarda más que el límite de Azure Site Recovery por script (actualmente, 120 minutos).

  > [!IMPORTANT]
  > Ejecute la copia de seguridad manualmente desde el portal de Azure y, después, ejecute de nuevo el plan de recuperación.
  >
  >
* Tiempo de espera del trabajo de clonación: se agotará el tiempo de espera del script de StorSimple si la clonación de los volúmenes tarda más que el límite de Azure Site Recovery por script (actualmente, 120 minutos).
* Error de sincronización de la hora: se produce un error en los scripts de StorSimple que afirma que las copias de seguridad no se realizaron correctamente, aunque se hayan llevado a cabo de forma correcta en el portal. Esto puede deberse a que la hora del aparato StorSimple no esté sincronizada con la hora actual de la zona horaria.

  > [!IMPORTANT]
  > Sincronice la hora del aparato con la hora actual de la zona horaria.
  >
  >
* Error de conmutación por error del aparato: el script de StorSimple puede presentar un error si se realiza una conmutación por error del aparato mientras se está ejecutando el plan de recuperación.

  > [!IMPORTANT]
  > Una vez completada la conmutación por error del aparato, vuelva a ejecutar el plan de recuperación.
  >
  >

## <a name="summary"></a>Resumen
Con Azure Site Recovery, puede crear un plan de recuperación ante desastres automatizado completo para una máquina virtual de servidor de archivos con recursos compartidos de archivos hospedados en el almacenamiento de StorSimple. Puede iniciar la conmutación por error en cuestión de segundos desde cualquier lugar si se produce una interrupción y poner en funcionamiento la aplicación en unos minutos.



<!--HONumber=Jan17_HO4-->


