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
ms.date: 10/13/2017
ms.author: vidarmsft
ms.openlocfilehash: e60cc83f49f9e0d0f878d7f49333f1be34ce54a6
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
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
* StorSimple Cloud Appliance se creó en el administrador de Azure StorSimple. El dispositivo se puede mantener en un estado de apagado.
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
Si desea proteger las máquinas que ejecutan Active Directory y DNS para que estén disponibles en el sitio de recuperación ante desastres, debe protegerlas explícitamente (de modo que los servidores de archivos estén accesibles después de la conmutación por error con autenticación). Hay dos opciones recomendadas según la complejidad del entorno local del cliente.

#### <a name="option-1"></a>Opción 1
Si el cliente tiene un número pequeño de aplicaciones, un único controlador de dominio para todo el sitio local y realizará conmutaciones por error en todo el sitio, se recomienda utilizar la replicación de Azure Site Recovery para replicar la máquina del controlador de dominio en un sitio secundario (esto se aplica tanto de sitio a sitio como de sitio a Azure).

#### <a name="option-2"></a>Opción 2
Si el cliente tiene un gran número de aplicaciones, ejecuta un bosque de Active Directory y realizará conmutaciones por error de unas pocas aplicaciones a la vez, se recomienda configurar un controlador de dominio adicional en el sitio de recuperación ante desastres (ya sea un sitio secundario o Azure).

Consulte [Protección de Active Directory y DNS con Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) para obtener instrucciones sobre cómo poner un controlador de dominio disponible en el sitio de recuperación ante desastres. Para el resto de este documento, se supone que habrá un controlador de dominio disponible en el sitio de recuperación ante desastres.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Uso de Azure Site Recovery para habilitar la protección de la VM de servidor de archivos
Para llevar a cabo este paso, debe preparar el entorno del servidor de archivos local, crear y preparar un almacén de Azure Site Recovery y habilitar la protección de archivos de la máquina virtual.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Para preparar el entorno del servidor de archivos local
1. Establezca el valor de **Control de cuentas de usuario** como **No notificarme nunca**. Esto resulta necesario a fin de que pueda usar scripts de automatización de Azure para conectar los destinos iSCSI tras la conmutación por error de Azure Site Recovery.

   1. Presione la tecla Windows + Q y busque **UAC**.
   2. Seleccione **Cambiar configuración de Control de cuentas de usuario**.
   3. Arrastre la barra hacia la parte inferior, hacia **Never Notify**(No notificarme nunca).
   4. Haga clic en **Aceptar** y, después, seleccione **Sí** cuando se le solicite.

      ![Configuración de Control de cuentas de usuario](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png)
2. Instale al agente de máquina virtual en cada una de las máquinas virtuales de servidor de archivos. Esto resulta necesario para que pueda ejecutar scripts de automatización de Azure en las máquinas virtuales conmutadas por error.

   1. [Descargue el agente](http://aka.ms/vmagentwin) en `C:\\Users\\<username>\\Downloads`.
   2. Abra Windows PowerShell en modo de administrador (Ejecutar como administrador) y, después, escriba el siguiente comando para ir a la ubicación de descarga:

      `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

      > [!NOTE]
      > El nombre del archivo puede cambiar según la versión.
      >
      >
3. Haga clic en **Siguiente**.
4. Acepte los **Terms of Agreement** (Términos del contrato) y, después, haga clic en **Siguiente**.
5. Haga clic en **Finalizar**
6. Cree recursos compartidos de archivos con volúmenes extraídos del almacenamiento de StorSimple. Para obtener más información, consulte [Uso del servicio StorSimple Manager para administrar volúmenes](storsimple-manage-volumes.md).

   1. En las máquinas virtuales locales, presione la tecla Windows + Q y busque **iSCSI**.
   2. Seleccione **iniciador iSCSI**.
   3. Seleccione la pestaña **Configuración** y copie el nombre del iniciador.
   4. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
   5. Seleccione la pestaña **StorSimple** y, después, seleccione el servicio StorSimple Manager que contiene el dispositivo físico.
   6. Cree contenedores de volumen y, luego, volúmenes. (Estos volúmenes son para los recursos compartidos de archivos de las máquinas virtuales de servidor de archivos). Copie el nombre del iniciador y asigne un nombre adecuado a los registros de control de acceso al crear los volúmenes.
   7. Seleccione la pestaña **Configurar** y apunte la dirección IP del dispositivo.
   8. En las máquinas virtuales locales, vaya a la opción **iniciador iSCSI** de nuevo y escriba la dirección IP en la sección Conexión rápida. Haga clic en **Conexión rápida** (ahora debe aparecer conectado el dispositivo).
   9. Abra Azure Portal y seleccione la pestaña **Volúmenes y dispositivos** . Haga clic en **Autoconfigurar**. Debería aparecer el volumen que ha creado.
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
2. Seleccione [Habilitar protección de máquina virtual](../site-recovery/site-recovery-hyper-v-site-to-azure.md) de la máquina virtual de servidor de archivos desde el portal de Azure Site Recovery.
3. Cuando comience la sincronización inicial, podrá volver a conectar el destino. Vaya al iniciador iSCSI, seleccione el dispositivo StorSimple y haga clic en **Conectar**.
4. Cuando la sincronización se haya completado y el estado de la máquina virtual sea **Protegido**, seleccione la máquina virtual, seleccione la pestaña **Configurar** y actualice la red de la máquina virtual del mismo modo (esta es la red de la que formarán parte las máquinas virtuales conmutadas por error). Si la red no aparece, significa que la sincronización todavía está en curso.

### <a name="enable-protection-of-storsimple-volumes"></a>Habilitación de la protección de volúmenes de StorSimple
Si no ha seleccionado la opción **Habilitar una copia de seguridad predeterminada para este volumen** para los volúmenes de StorSimple, vaya a **Directivas de copia de seguridad** en el servicio StorSimple Manager y cree una directiva de copia de seguridad adecuada para todos los volúmenes. Se recomienda que establezca la frecuencia de las copias de seguridad en el objetivo de punto de recuperación (RPO) que le gustaría ver para la aplicación.

### <a name="configure-the-network"></a>Configuración de la red
Para la máquina virtual de servidor de archivos, configure las opciones de red en Azure Site Recovery de forma que las redes de VM estén conectadas a la red de recuperación ante desastres correcta después de la conmutación por error.

Puede seleccionar la VM en la pestaña **Elementos replicados** para configurar las opciones de red, como se muestra en la siguiente ilustración.

![Proceso y red](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Creación de un plan de recuperación
Puede crear un plan de recuperación en ASR para automatizar el proceso de conmutación por error de los recursos compartidos de archivos. Si se produce una interrupción, puede poner los recursos compartidos de archivos en funcionamiento en minutos con un solo clic. Para habilitar esta automatización, necesitará una cuenta de Automatización de Azure.

#### <a name="to-create-an-automation-account"></a>Para crear una cuenta de Automation
1. Vaya a Azure Portal, sección &gt; **Automation**.
2. Haga clic en el botón **+Agregar**, se abre la hoja siguiente.

   ![Agregar cuenta de Automation](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)

   * Nombre: escriba una nueva cuenta de Automation.
   * Suscripción: elija la suscripción.
   * Grupo de recursos: seleccione un grupo de recursos existente o cree uno nuevo.
   * Ubicación: seleccione la ubicación y manténgala en la misma geoárea o región en la que se crearon las cuentas de almacenamiento y StorSimple Cloud Appliance.
   * Crear cuenta de ejecución de Azure: seleccione la opción **Sí**.

3. Vaya a la cuenta de Automation y haga clic en **Runbooks** &gt; **Examinar galería** para importar todos los runbooks necesarios en la cuenta de automatización.
4. Agregue los siguientes runbooks buscando la etiqueta **Recuperación ante desastres** de la galería:

   * Limpieza de los volúmenes de StorSimple después de la conmutación por error de prueba (TFO)
   * Conmutación por error de contenedores de volúmenes de StorSimple
   * Montaje de volúmenes en el dispositivo StorSimple después de la conmutación por error
   * Desinstalación de la extensión de script personalizada en la máquina virtual de Azure
   * Inicio de StorSimple Virtual Appliance

     ![Examinar galería](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)

5. Publique todos los scripts seleccionando el runbook en la cuenta de Automation y haga clic en **Editar** &gt; **Publicar** y, a continuación, en **Sí** en el mensaje de comprobación. Después de este paso, aparecerá la pestaña **Runbooks** de la siguiente forma:

    ![Runbooks](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)

6. En la cuenta de Automation, haga clic en **Variables** &gt; **Agregar variable** y agregue las siguientes variables. Puede elegir cifrar estos activos. Estas variables son específicas del plan de recuperación. Si el plan de recuperación que creará en el paso siguiente se denomina TestPlan, las variables deben ser TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName y así sucesivamente.

   * **BaseUrl**: dirección URL de Resource Manager para la nube de Azure. Se obtiene mediante el cmdlet **Get-AzureRmEnvironment | Select-Object Name, ResourceManagerUrl**.
   * *RecoveryPlanName***-ResourceGroupName**: el grupo de Resource Manager que tiene el recurso de StorSimple.
   * *RecoveryPlanName***-ManagerName**: el recurso de StorSimple que tiene el dispositivo StorSimple.
   * *RecoveryPlanName***-DeviceName**: el dispositivo StorSimple que debe conmutarse por error.
   * *RecoveryPlanName***-DeviceIpAddress**: la dirección IP del dispositivo (se puede encontrar en la pestaña **Dispositivos** de la sección Administrador de dispositivos de StorSimple &gt; **Configuración** &gt; **Red** &gt; **Configuración DNS**).
   * *RecoveryPlanName***-VolumeContainers**: una cadena separada por comas de contenedores de volúmenes presentes en el dispositivo que deben conmutarse por error; por ejemplo, contDeVol1,contDeVol2,contDeVol3.
   * *nombreDelPlanDeRecuperación***-TargetDeviceName**: StorSimple Cloud Appliance en el que se deben conmutar por error los contenedores.
   * *RecoveryPlanName***-TargetDeviceIpAddress**: la dirección IP del dispositivo de destino (se puede encontrar en la sección **Máquina virtual** &gt; grupo **Configuración** pestaña &gt; **Red**).
   * *nombreDelPlanDeRecuperación***-StorageAccountName**: el nombre de la cuenta de almacenamiento en la que se almacenará el script (que debe ejecutarse en la máquina virtual conmutada por error). Puede tratarse de cualquier cuenta de almacenamiento que tenga suficiente espacio como para almacenar temporalmente el script.
   * *nombreDelPlanDeRecuperación***-StorageAccountKey**: la clave de acceso para la cuenta de almacenamiento anterior.
   * *nombreDelPlanDeRecuperación***-VMGUIDS**: al proteger una máquina virtual, Azure Site Recovery asigna a cada VM un identificador único que ofrece los detalles de la máquina virtual conmutada por error. Para obtener el VMGUID, seleccione la pestaña **Recovery Services** y haga clic en **Elemento protegido**&gt;**Grupos de protección**&gt;**Máquinas**&gt;**Propiedades**. Si tiene varias máquinas virtuales, después, agregue los GUID como una cadena separada por comas.

    Por ejemplo, si el nombre del plan de recuperación es fileServerpredayRP, las pestañas **Variables**, **Conexiones** y **Certificados** deberían aparecer de la siguiente forma después de agregar todos los recursos.

    ![Recursos](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

7. Cargue el módulo de Runbook de StorSimple serie 8000 en su cuenta de Automation. Utilice los pasos siguientes para agregar un módulo:

   a. Abra PowerShell, cree una nueva carpeta y cambie el directorio a la carpeta.
    
    ```
         mkdir C:\scripts\StorSimpleSDKTools
         cd C:\scripts\StorSimpleSDKTools
    ```
   b. Descargue la CLI de NuGet en la misma carpeta del paso 1.
      Hay varias versiones de nuget.exe disponibles en las [descargas de NuGet](https://www.nuget.org/downloads). Cada vínculo de descarga apunta directamente a un archivo .exe, así que no olvide hacer clic con el botón derecho y guardar el archivo en el equipo en lugar de ejecutarlo desde el explorador.

    ```
         wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

   c. Descargue el SDK dependiente.

    ```
         C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
         C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
         C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

   d. Cree un módulo de Runbook de Azure Automation para la administración de dispositivos de StorSimple serie 8000. Use los siguientes comandos para crear un archivo zip del módulo de Automation.

    ```
         # set path variables
         $downloadDir = "C:\scripts\StorSimpleSDKTools"
         $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

         #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
         mkdir "$moduleDir"

         copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
         copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
         copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
         copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
         copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
         copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

         #Don't change the name of the Archive
         compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

     e. Importe el archivo zip del módulo de Azure Automation (Microsoft.Azure.Management.StorSimple8000Series.zip) que creó en el paso anterior. Para ello, seleccione la cuenta de Automation, haga clic en **Módulos** en RECURSOS COMPARTIDOS y, a continuación, haga clic en **Agregar un módulo**.

    Después de importar el módulo de StorSimple serie 8000, debería aparecer la pestaña **Módulos** como se indica a continuación:

    ![Módulos](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

8. Vaya a la sección **Servicios de recuperación** y seleccione el almacén de Azure Site Recovery que creó anteriormente.
9. Seleccione la opción **Planes de recuperación (Site Recovery)** del grupo **Administrar** y cree un nuevo plan de recuperación de la siguiente forma:

   a.  Haga clic en el botón **+ Plan de recuperación** y se abrirá la hoja siguiente.

      ![Creación de un plan de recuperación](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)

   b.  Escriba un nombre de plan de recuperación, elija valores del modelo de origen, destino e implementación.

   c.  Seleccione las VM del grupo de protección que desea incluir en el plan de recuperación y haga clic en el botón **Aceptar**.

   d.  Seleccione el plan de recuperación que creó anteriormente y haga clic en el botón **Personalizar** para abrir la vista de personalización del plan de recuperación.

   e.  Haga clic con el botón derecho en **Cierre de todos los grupos** y haga clic en **Agregar acción anterior**.

   f.  Se abre la hoja Insertar acción, escriba un nombre, seleccione la opción **Lado principal** en donde ejecutar la opción, seleccione Cuenta de Automation (en la que se agregan los runbooks) y, a continuación, seleccione el runbook **Failover-StorSimple-Volume-Containers**.

   g.  Haga clic con el botón derecho en **Grupo 1: Iniciar** y haga clic en la opción **Agregar elementos protegidos**. Luego seleccione las VM que deban protegerse en el plan de recuperación y haga clic en el botón **Aceptar**. Opcional, si ya hay VM seleccionadas.

   h.  Haga clic con el botón derecho en **Grupo 1: Iniciar** y haga clic en **Acción posterior** y, a continuación, agregue todos los scripts siguientes:

   * Runbook Start-StorSimple-Virtual-Appliance
   * Runbook Fail over-StorSimple-volume-containers
   * Runbook Mount-volumes-after-failover
   * Runbook Uninstall-custom-script-extension

   i.  Agregue una acción manual después de los 4 scripts anteriores en la misma sección **Grupo 1: Pasos posteriores** . Esta acción es el punto en el que puede comprobar que todo funciona correctamente. Debe agregarse solo como parte de la conmutación por error de prueba (por tanto, seleccione solo la casilla **Probar conmutación por error**).

   j.  Después de la acción manual, agregue el script de **limpieza** con el mismo procedimiento que usó para los otros runbooks. **Guarde** el plan de recuperación.

    > [!NOTE]
    > Cuando ejecute una conmutación por error de prueba, debe comprobar todo en el paso de acción manual porque una vez completada la acción manual, se eliminarán como parte de la limpieza los volúmenes de StorSimple clonados en el dispositivo de destino.
    >

    ![Plan de recuperación](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Realización de una conmutación por error de prueba
Consulte la guía complementaria [Protección de Active Directory y DNS con Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) para obtener consideraciones específicas para Active Directory durante la conmutación por error de prueba. La configuración local no se ve afectada cuando se produce la conmutación por error de prueba. Los volúmenes de StorSimple conectados a la máquina virtual local se clonan en el StorSimple Cloud Appliance en Azure. Aparecerá una máquina virtual para fines de pruebas en Azure y se conectarán los volúmenes clonados a la máquina virtual.

#### <a name="to-perform-the-test-failover"></a>Para realizar la conmutación por error de prueba
1. En Azure Portal, seleccione su almacén de Site Recovery.
2. Haga clic en el plan de recuperación creado para la máquina virtual de servidor de archivos.
3. Haga clic en **Probar conmutación por error**.
4. Seleccione la instancia de Azure Virtual Network a la que se conectarán las máquinas virtuales de Azure después de la conmutación por error.

   ![Iniciar la conmutación por error](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
5. Haga clic en **Aceptar** para iniciar la conmutación por error. Puede realizar un seguimiento del progreso haciendo clic en la máquina virtual para abrir sus propiedades o en el trabajo **Probar conmutación por error** en nombre de almacén &gt; **Trabajos** &gt; **Trabajos de Site Recovery**.
6. Cuando se complete la conmutación por error, debería ver la máquina de réplica de Azure Portal &gt; **Virtual Machines**. Puede realizar sus validaciones.
7. Una vez completadas las validaciones, haga clic en **Validaciones completas**. Esto eliminará los volúmenes de StorSimple y cerrará StorSimple Cloud Appliance.
8. Una vez que haya terminado, haga clic en **Cleanup test failover** (Limpieza de conmutación por error de prueba) en el plan de recuperación. En Notas, anote y guarde todas las observaciones asociadas con la conmutación por error de prueba. Así se eliminará la máquina virtual que se creó durante la conmutación por error de prueba.

## <a name="perform-a-planned-failover"></a>Realización de una conmutación por error planeada
   Durante una conmutación por error planeada, la máquina virtual de servidor de archivos local se apaga correctamente y se realiza una instantánea de copia de seguridad en la nube de los volúmenes del dispositivo StorSimple. Estos se conmutan por error en el dispositivo virtual, aparece una máquina virtual de réplica en Azure y se conectan los volúmenes a la máquina virtual.

#### <a name="to-perform-a-planned-failover"></a>Para realizar una conmutación por error planeada
1. En Azure Portal, seleccione el almacén **Recovery Services** &gt; **Planes de recuperación (Site Recovery)** &gt; **recoveryplan_name** creado para la VM del servidor de archivos.
2. En la hoja Plan de recuperación, haga clic en **Más** &gt;  **Conmutación por error planeada**.

   ![Plan de recuperación](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
3. En la hoja **Confirmar conmutación por error planeada**, elija las ubicaciones de origen y de destino, y seleccione la red de destino y haga clic en el icono de verificación ✓ para iniciar el proceso de conmutación por error.
4. Una vez creadas las máquinas virtuales de réplica, pasan a estar en estado pendiente de confirmación. Haga clic en **Confirmar** para confirmar la conmutación por error.
5. Cuando se ha completado la replicación, las máquinas virtuales se inician en la ubicación secundaria.

## <a name="perform-a-failover"></a>Realización de una conmutación por error
Durante una conmutación por error no planeada, los volúmenes de StorSimple se conmutan por error en el dispositivo virtual, aparece una máquina virtual de réplica en Azure y se conectan los volúmenes a la máquina virtual.

#### <a name="to-perform-a-failover"></a>Para realizar una conmutación por error
1. En Azure Portal, seleccione el almacén **Recovery Services** &gt; **Planes de recuperación (Site Recovery)** &gt; **recoveryplan_name** creado para la VM del servidor de archivos.
2. En la hoja Plan de recuperación, haga clic en **Más** &gt;  **Conmutación por error**.
3. En la hoja **Confirmar conmutación por error**, elija las ubicaciones de origen y de destino.
4. Seleccione **Apagar máquinas virtuales y sincronizar los últimos datos** para especificar que Site Recovery debe intentar apagar la máquina virtual protegida y sincronizar los datos para que se realice la conmutación por error de la versión más reciente de los datos.
5. Después de la conmutación por error, las máquinas virtuales se encontrarán en un estado de confirmación pendiente. Haga clic en **Confirmar** para confirmar la conmutación por error.


## <a name="perform-a-failback"></a>Realización de una conmutación por recuperación
Durante una conmutación por recuperación, los contenedores de volúmenes de StorSimple se conmutan por error de vuelta al dispositivo físico después de realizar una copia de seguridad.

#### <a name="to-perform-a-failback"></a>Para realizar una conmutación por recuperación
1. En Azure Portal, seleccione el almacén **Recovery Services** &gt; **Planes de recuperación (Site Recovery)** &gt; **recoveryplan_name** creado para la VM del servidor de archivos.
2. En la hoja Plan de recuperación, haga clic en **Más** &gt;  **Conmutación por error planeada**.
3. Elija las ubicaciones de origen y destino y seleccione la sincronización de datos adecuada y las opciones de creación de VM.
4. Haga clic en el botón **Aceptar** para iniciar el proceso de conmutación por recuperación.

   ![Iniciar la conmutación por recuperación](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

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
* Tiempo de espera del trabajo de conmutación por error: se agotará el tiempo de espera del script de StorSimple si la conmutación por error de los contenedores de volúmenes tarda más que el límite de Azure Site Recovery por script (actualmente, 120 minutos).
* Tiempo de espera del trabajo de copia de seguridad: se agotará el tiempo de espera del script de StorSimple si la copia de seguridad de los volúmenes tarda más que el límite de Azure Site Recovery por script (actualmente, 120 minutos).

  > [!IMPORTANT]
  > Ejecute la copia de seguridad manualmente desde el portal de Azure y, después, ejecute de nuevo el plan de recuperación.

* Tiempo de espera del trabajo de clonación: se agotará el tiempo de espera del script de StorSimple si la clonación de los volúmenes tarda más que el límite de Azure Site Recovery por script (actualmente, 120 minutos).
* Error de sincronización de la hora: se produce un error en los scripts de StorSimple que afirma que las copias de seguridad no se realizaron correctamente, aunque se hayan llevado a cabo de forma correcta en el portal. Esto puede deberse a que la hora del aparato StorSimple no esté sincronizada con la hora actual de la zona horaria.

  > [!IMPORTANT]
  > Sincronice la hora del aparato con la hora actual de la zona horaria.

* Error de conmutación por error del aparato: el script de StorSimple puede presentar un error si se realiza una conmutación por error del aparato mientras se está ejecutando el plan de recuperación.

  > [!IMPORTANT]
  > Una vez completada la conmutación por error del aparato, vuelva a ejecutar el plan de recuperación.


## <a name="summary"></a>Resumen
Con Azure Site Recovery, puede crear un plan de recuperación ante desastres automatizado completo para una máquina virtual de servidor de archivos con recursos compartidos de archivos hospedados en el almacenamiento de StorSimple. Puede iniciar la conmutación por error en cuestión de segundos desde cualquier lugar si se produce una interrupción y poner en funcionamiento la aplicación en unos minutos.
