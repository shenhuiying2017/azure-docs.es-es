---
title: "Replicación de máquinas virtuales de Hyper-V en VMM con SAN mediante Azure Site Recovery | Microsoft Docs"
description: "En este artículo se describe cómo replicar máquinas virtuales de Hyper-V entre dos sitios con Azure Site Recovery mediante la replicación de SAN."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: eb480459-04d0-4c57-96c6-9b0829e96d65
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 75653b84d6ccbefe7d5230449bea81f498e10a98
ms.openlocfilehash: 09fb09bfdea2e18384851bb8ed9a4f8c08466dd2


---
# <a name="replicate-hyper-v-vms-in-vmm-clouds-to-a-secondary-site-with-azure-site-recovery-using-san"></a>Replicación de máquinas virtuales de Hyper-V (en nubes VMM) en un sitio secundario con Azure Site Recovery mediante SAN


Use este artículo si desea implementar [Site Recovery](site-recovery-overview.md) para administrar la replicación de máquinas virtuales de Hyper-V (administradas en nubes VMM de System Center) en un sitio VMM secundario.


Incluye una introducción al escenario, instrucciones para configurar el almacenamiento SAN en VMM y la configuración de la replicación en el portal de Site Recovery. Finaliza con una comprobación de la conmutación por error para asegurarse de que todo funcione según lo esperado.

Publique sus comentarios más abajo. Consiga respuestas a sus preguntas técnicas en el [Foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="why-replicate-with-san-and-site-recovery"></a>¿Por qué replicar con SAN y Site Recovery?

* SAN proporciona una solución de replicación escalable de nivel empresarial, de forma que un sitio principal que contenga Hyper-V con SAN pueda replicar LUN en un sitio secundario con una SAN. VMM administra el almacenamiento, y se organizan la replicación y la conmutación por error con Site Recovery.
* Site Recovery ha colaborado con varios [asociados de almacenamiento SAN](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) para proporcionar replicación entre almacenamiento de iSCSI y de canal de fibra.  
* Podrá aprovechar su infraestructura SAN existente para proteger aplicaciones críticas implementadas en clústeres de Hyper-V. Las máquinas virtuales se pueden replicar como grupo para que se pueda llevar a cabo la conmutación por error coherente de las aplicaciones de n niveles.
* La replicación de SAN garantiza la coherencia de la replicación entre distintos niveles de una aplicación, con replicación sincronizada para un bajo RTO y RPO, y la replicación no sincronizada para una gran flexibilidad (según las funcionalidades de la matriz de almacenamiento).  
* Puede administrar el almacenamiento SAN en el tejido de VMM y usar SMI-S en VMM para detectar almacenamiento existente.  

Observe lo siguiente:
- La replicación de sitio a sitio con SAN no está disponible en Azure Portal. Solo está disponible en el portal clásico. No se pueden crear almacenes en el portal clásico. Se pueden mantener los existentes.
- No se admite la replicación desde SAN en Azure.
- No se pueden replicar discos duros virtuales compartidos ni unidades lógicas (LUN) que estén conectadas directamente a máquinas virtuales mediante canal de fibra o iSCSI. Se pueden replicar clústeres invitados.


## <a name="architecture"></a>Arquitectura

![Arquitectura SAN](./media/site-recovery-vmm-san/architecture.png)

- **Azure**: configure un almacén de Site Recovery en Azure Portal.
- **Almacenamiento SAN**: el almacenamiento SAN se administra en el tejido de VMM. Agregue el proveedor de almacenamiento, cree clasificaciones de almacenamiento y configure bloques de almacenamiento.
- **VMM e Hyper-V**: se recomienda un servidor VMM en cada sitio. Configure nubes privadas VMM y coloque los clústeres de Hyper-V en ellas. Durante la implementación, el proveedor de Azure Site Recovery se instala en cada servidor VMM y el servidor se registra en el almacén. El proveedor se comunica con el servicio Site Recovery para administrar la replicación, la conmutación por error y la conmutación por recuperación.
- **Replicación**: después de configurar el almacenamiento en VMM y configurar la replicación de Site Recovery, esta se produce entre el almacenamiento SAN principal y el secundario. No se envía ningún dato de replicación a Site Recovery.
- **Conmutación por error**: habilite la conmutación por error en el portal de Site Recovery. No hay ninguna pérdida de datos durante la conmutación por error porque la replicación es sincrónica.
- **Conmutación por recuperación**: para conmutar por recuperación, habilite la replicación inversa para transferir los cambios diferenciales del sitio secundario al principal. Una vez completada la replicación inversa, ejecute una conmutación por error planeada del sitio secundario al principal. Esta conmutación por error planeada detiene las máquinas virtuales de réplica en el sitio secundario y las inicia en el principal.


## <a name="before-you-start"></a>Antes de comenzar


**Requisitos previos** | **Detalles**
--- | ---
**Las tablas de Azure** |Necesita una cuenta de [Microsoft Azure](https://azure.microsoft.com/) . Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) sobre los precios de Site Recovery. Cree un almacén de Azure Site Recovery para la configuración y la administración de la replicación y la conmutación por error.
**VMM** | Puede usar un solo servidor VMM y replicar entre nubes diferentes, pero se recomienda un servidor VMM en el sitio principal y otro en el secundario. Se puede implementar VMM como servidor físico o virtual independiente, o bien como clúster. <br/><br/>En el servidor VMM se debería ejecutar como mínimo System Center 2012 R2 con las últimas actualizaciones acumulativas.<br/><br/> Necesitará al menos una nube configurada en el servidor VMM principal que desee proteger y otra, en el servidor VMM secundario que desee usar para la conmutación por error.<br/><br/> La nube de origen debe incluir uno o varios grupos de hosts de VMM.<br/><br/> Todas las nubes VMM deben tener establecido el perfil de capacidad de Hyper-V.<br/><br/> Aprenda sobre cómo configurar las nubes VMM (https://technet.microsoft.com/es-es/system-center-docs/vmm/scenario/cloud-overview).
**Hyper-V** | Necesita uno o varios clústeres de Hyper-V en las nubes VMM principal y secundaria.<br/><br/> El clúster de Hyper-V de origen debería contener una o varias máquinas virtuales.<br/><br/> Los grupos de hosts de VMM en el sitio principal y el secundario deben contener al menos uno de los clústeres de Hyper-V.<br/><br/>Los servidores host y los servidores de Hyper-V de destino deben ejecutar como mínimo Windows Server 2012 con el rol Hyper-V y tener instaladas las actualizaciones más recientes.<br/><br/> Si está ejecutando Hyper-V en un clúster, tenga en cuenta que ese agente de clúster no se crea automáticamente si tiene un clúster basado en una dirección IP estática. Debe configurarlo manualmente. [Más información](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) en el artículo de Aidan Finn.
**Almacenamiento SAN** | Puede replicar máquinas virtuales agrupadas en clústeres invitados con almacenamiento de canal o iSCSI, o mediante discos duros virtuales (VHDX) compartidos.<br/><br/> Necesita dos matrices de SAN, una en el sitio principal y otra en el secundario.<br/><br/> Se debe configurar una infraestructura de red entre las matrices. Es necesario configurar la replicación y el emparejamiento. Licencias de replicación deben configurarse de acuerdo con los requisitos de la matriz de almacenamiento.<br/><br/>Configure las funciones de red entre los servidores host de Hyper-V y la matriz de almacenamiento, de forma que los hosts puedan comunicarse con los LUN de almacenamiento mediante ISCSI o canal de fibra.<br/><br/> Consulte las [matrices de almacenamiento compatibles](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).<br/><br/> Es necesario instalar los proveedores de SMI-S (de fabricantes de matrices de almacenamiento); asimismo, el proveedor deberá administrar las matrices de SAN. Configure el proveedor de acuerdo con las instrucciones del fabricante.<br/><br/>Asegúrese de que el proveedor de SMI-S de la matriz se encuentre en un servidor al que el servidor VMM pueda acceder a través de la red, con una dirección IP o un FQDN.<br/><br/> Cada matriz de SAN debe tener uno o varios bloques de almacenamiento disponibles.<br/><br/> El servidor VMM principal debe administrar la matriz principal y el servidor VMM secundario administrará la matriz secundaria.
**Asignación de red** | Configure la asignación de red para que las máquinas virtuales replicadas se coloquen de manera óptima en los servidores host de Hyper-V secundarios tras la conmutación por error y para que se conecten a las redes de máquina virtual adecuadas. Si no configura la asignación de red, las máquinas virtuales de réplica no se conectarán a ninguna red después de la conmutación por error.<br/><br/> Debe asegurarse de que las redes de VMM estén configuradas correctamente, para que pueda configurar la asignación de red durante la implementación de Site Recovery. En VMM, las máquinas virtuales del host de Hyper-V de origen debe estar conectadas a una red de máquina virtual de VMM. Esa red debe estar vinculada a una red lógica asociada con la nube.<br/><br/> La nube de destino debería tener configurada una red de máquina virtual correspondiente que, a su vez, debería estar vinculada a una red lógica correspondiente que esté asociada a la nube de destino.<br/><br/>[más información](site-recovery-network-mapping.md) sobre la asignación de red.

## <a name="step-1-prepare-the-vmm-infrastructure"></a>Paso 1: Preparación de la infraestructura de VMM
Para preparar la infraestructura de VMM debe:

1. Comprobar las nubes VMM
2. Integrar y clasificar el almacenamiento SAN en VMM
3. Crear LUN y asignar almacenamiento
4. Crear grupos de replicación
5. Configurar redes de máquinas virtuales

### <a name="verify-vmm-clouds"></a>Comprobar las nubes VMM

Asegúrese de que las nubes VMM estén configuradas correctamente antes de empezar la implementación de Site Recovery.

### <a name="integrate-and-classify-san-storage-in-the-vmm-fabric"></a>Integrar y clasificar el almacenamiento SAN en el tejido de VMM

1. En la consola VMM, haga clic en **Tejido** > **Almacenamiento** > **Agregar recursos** > **Dispositivos de almacenamiento**.
2. En el Asistente para agregar dispositivos de almacenamiento > **Seleccione un tipo de proveedor de almacenamiento**, seleccione **Dispositivos SAN y NAS detectados y administrados por un proveedor de SMI-S**.


    ![Tipo de proveedor](./media/site-recovery-vmm-san/provider-type.png)
3. En la página **Especifique el protocolo y la dirección del proveedor SMI-S de almacenamiento**, seleccione **SMI-S CIMXML** y especifique la configuración para conectar con el proveedor.
4. En **Provider IP address or FQDN** (Dirección IP o FQDN del proveedor) y **Puerto TCP/IP**, especifique la configuración para conectar con el proveedor. Solo puede utilizar una conexión SSL para SMI-S CIMXML.

    ![Conexión de proveedor](./media/site-recovery-vmm-san/connect-settings.png)
5. En **Cuenta de ejecución** , especifique una cuenta de ejecución de VMM que pueda tener acceso al proveedor, o bien cree una cuenta nueva.
6. En la página **Recopilar información** , VMM intenta detectar e importar la información del dispositivo de almacenamiento de forma automática. Para volver a intentar la detección, haga clic en **Proveedor de examen**. Si el proceso de detección se realiza correctamente, se mostrarán en la página las matrices de almacenamiento detectadas, los grupos de almacenamiento, el fabricante, el modelo y la capacidad.

    ![Detección de almacenamiento](./media/site-recovery-vmm-san/discover.png)
7. En **Seleccionar grupos de almacenamiento para administrar y asignar una clasificación**, seleccione los grupos de almacenamiento que VMM va a administrar y asigne una clasificación. La información de LUN se importará desde los grupos de almacenamiento. Cree LUN en función de las aplicaciones que necesita proteger, de sus requisitos de capacidad y de las necesidades de replicación.

    ![Clasificación de almacenamiento](./media/site-recovery-vmm-san/classify.png)

### <a name="create-luns-and-allocate-storage"></a>Crear LUN y asignar almacenamiento

Cree LUN en función de las aplicaciones que necesite proteger, de los requisitos de capacidad y de las necesidades de replicación conjunta.

1. Una vez que el almacenamiento aparezca en el tejido de VMM, puede [aprovisionar los LUN](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-storage-host-groups#create-a-lun-in-vmm).

     > [!NOTE]
     > No agregue discos duros virtuales para la máquina virtual que estén habilitados para la replicación en LUN si los LUN no se encuentran en un grupo de replicación de Site Recovery. Si lo hace, Site Recovery no los detectará.
     >
     
2. A continuación, asigne la capacidad de almacenamiento al clúster del host de Hyper-V para que VMM pueda implementar datos de las máquinas virtuales en el almacenamiento aprovisionado:

   * Antes de asignar el almacenamiento al clúster, deberá asignarlo al grupo de host de VMM en el que reside el clúster. Aprenda más sobre la [asignación de unidades lógicas de almacenamiento](https://technet.microsoft.com/library/gg610686.aspx) y [bloques de almacenamiento](https://technet.microsoft.com/library/gg610635.aspx) a un grupo host.</a>.
   * A continuación, asigne capacidad de almacenamiento al clúster, tal como se describe en [Cómo configurar el almacenamiento en un clúster de hosts de Hyper-V en VMM](https://technet.microsoft.com/library/gg610692.aspx)</a>.

    ![Tipo de proveedor](./media/site-recovery-vmm-san/provider-type.png)
3. En **Especifique el protocolo y la dirección del proveedor SMI-S de almacenamiento**, seleccione **CIMXML SMI-S**. Especifique la configuración para conectarse al proveedor. Solo puede utilizar una conexión SSL para SMI-S CIMXML.

    ![Conexión de proveedor](./media/site-recovery-vmm-san/connect-settings.png)
4. En **Cuenta de ejecución**, especifique una cuenta de ejecución de VMM que pueda acceder al proveedor o cree una.
5. En **Recopilar información**, VMM intenta de forma automática detectar e importar la información del dispositivo de almacenamiento. Si necesita volver a intentarlo, haga clic en **Proveedor de examen**. Cuando el proceso de detección se realice correctamente, se muestran en la página las matrices de almacenamiento, los bloques de almacenamiento, el fabricante, el modelo y la capacidad.

    ![Detección de almacenamiento](./media/site-recovery-vmm-san/discover.png)
7. En **Seleccionar bloques de almacenamiento para someterlos a administración y asignar una clasificación**, seleccione los bloques de almacenamiento que VMM va a administrar y asígneles una clasificación. La información de LUN se importará desde los grupos de almacenamiento.

    ![Clasificación de almacenamiento](./media/site-recovery-vmm-san/classify.png)


### <a name="create-replication-groups"></a>Crear grupos de replicación

Cree un grupo de replicación que incluya todas las LUN necesarias para la replicación conjunta.

1. En la consola VMM, abra la pestaña **Grupos de replicación** de las propiedades de la matriz de almacenamiento y haga clic en **Nuevo**.
2. Cree el grupo de replicación.


    ![Grupo de replicación de SAN](./media/site-recovery-vmm-san/rep-group.png)

### <a name="set-up-networks"></a>Configuración de redes

Si desea configurar la asignación de redes, haga lo siguiente:

1. [Más información sobre](site-recovery-network-mapping.md) la asignación de red.
2. Prepare las redes de máquinas virtuales en VMM:

   * [Configure redes lógicas](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-logical-networks).
   * [Configure redes de máquinas virtuales](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-vm-networks).


## <a name="step-2-create-a-vault"></a>Paso 2: Creación de un almacén

1. Inicie sesión en el [Portal de administración](https://portal.azure.com) desde el servidor VMM que desee registrar en el almacén.
2. Expanda **Data Services** > **Recovery Services** y haga clic en **Almacén de Site Recovery**.
3. Haga clic en **Crear nuevo	** > **Creación rápida**.
4. En **Nombre**, escriba un nombre descriptivo para identificar el almacén.
5. En **Región** , seleccione la región geográfica del almacén. Para comprobar las regiones admitidas, consulte Disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Haga clic en **Crear almacén**.

    ![Almacén nuevo](./media/site-recovery-vmm-san/create-vault.png)

Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como **Activo** en la página principal de **Recovery Services**.

### <a name="register-the-vmm-servers"></a>Registro de los servidores VMM

1. Abra la página Inicio rápido desde la página **Servicios de recuperación** . El inicio rápido también se puede abrir en cualquier momento mediante el icono.

    ![Icono de inicio rápido](./media/site-recovery-vmm-san/quick-start-icon.png)
2. En la lista desplegable, seleccione **Entre sitios locales de Hyper-V mediante la replicación de la matriz**.

    ![Clave de registro](./media/site-recovery-vmm-san/select-san.png)
3. En **Preparar servidores VMM**, descargue la versión más reciente del archivo de instalación del proveedor de Azure Site Recovery.
4. Ejecute este archivo en el servidor VMM de origen. Si VMM está implementado en un clúster y va a instalar el proveedor por primera vez, instálelo en un nodo activo y finalice la instalación para registrar el servidor VMM en el almacén. A continuación, instale el proveedor en los demás nodos. Si está actualizando el proveedor, debe actualizarlo en todos los nodos para que tengan la misma versión del proveedor.
5. El instalador comprueba los requisitos y solicita permiso para detener el servicio VMM y comenzar la instalación del proveedor. El servicio se reiniciará automáticamente cuando finalice la instalación. En un clúster de VMM, se le pedirá que detenga el rol Clúster.
6. En **Microsoft Update**, puede optar por recibir actualizaciones; las actualizaciones del proveedor se instalarán según la directiva de Microsoft Update.

    ![Microsoft Updates](./media/site-recovery-vmm-san/ms-update.png)
    
7. De manera predeterminada, la ubicación de instalación para el proveedor es <SystemDrive>\Archivos de programa\Microsoft System Center 2012 R2\Virtual Machine Manager\bin. Haga clic en **Instalar** para empezar.

    ![InstallLocation](./media/site-recovery-vmm-san/install-location.png)
    
8. Una vez instalado el proveedor, haga clic en **Registrar** para registrar el servidor VMM en el almacén.

    ![InstallComplete](./media/site-recovery-vmm-san/install-complete.png)
    
9. En **Conexión a Internet**, especifique cómo se conecta a Internet el proveedor. Seleccione **Use default system proxy settings** (Usar configuración predeterminada de proxy del sistema) si desea usar la configuración predeterminada de conexión a Internet en el servidor.

    ![Configuración de Internet](./media/site-recovery-vmm-san/proxy-details.png)

   * Si desea usar un proxy personalizado, configúrelo antes de instalar el proveedor. Cuando se configuran las opciones del proxy personalizado, se ejecuta una prueba para comprobar la conexión del proxy.
   * Si usa un proxy personalizado o el proxy predeterminado requiere autenticación, debería especificar los detalles del proxy, incluida la dirección y el puerto.
   * Estas [direcciones URL](site-recovery-best-practices.md#url-access) deben ser accesibles desde el servidor VMM.
   * Si utiliza un proxy personalizado, se creará una cuenta de ejecución de VMM (DRAProxyAccount) mediante el uso automático de las credenciales de proxy especificadas. Configure el servidor proxy para que esta cuenta pueda autenticarse. Puede modificar la configuración de la cuenta de ejecución en la consola VMM (**Configuración** > **Seguridad** > **Cuentas de ejecución** > **DRAProxyAccount**). Debe reiniciar el servicio VMM para que el cambio surta efecto.
10. En **Clave de registro**, seleccione la clave que ha descargado del portal y ha copiado en el servidor VMM.
11. En **Nombre del almacén**, compruebe el nombre del almacén en el que se registrará el servidor.

    ![Registro de servidor](./media/site-recovery-vmm-san/vault-creds.png)
12. La configuración de cifrado solo se utiliza para la replicación de VMM en Azure. Puede pasarla por alto.

    ![Registro de servidor](./media/site-recovery-vmm-san/encrypt.png)
13. En **Nombre del servidor**, especifique un nombre descriptivo para identificar el servidor VMM en el almacén. En una configuración de clúster, especifique el nombre del rol de clúster VMM.
14. En **Initial cloud metadata sync** (Sincronización inicial de metadatos de nube), seleccione si desea sincronizar los metadatos de todas las nubes en el servidor VMM. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar este parámetro sin marcar y sincronizar cada nube individualmente en las propiedades de la nube de la consola de VMM.


    ![Registro de servidor](./media/site-recovery-vmm-san/friendly-name.png)
15. Haga clic en **Next** para finalizar el proceso. Después del registro, la Recuperación del sitio de Azure recupera los metadatos del servidor VMM. El servidor se muestra en **Servidores** > **Servidores VMM** en el almacén.

### <a name="command-line-installation"></a>Instalación de la línea de comandos

El proveedor de Azure Site Recovery también puede instalarse mediante la siguiente línea de comandos. Este método se puede usar para instalar el proveedor en Server Core para Windows Server 2012 R2.

1. Descargue el archivo de instalación del proveedor y la clave de registro en una carpeta. Por ejemplo, C:\ASR.
2. Detenga el servicio VMM.
3. Extraiga el instalador del proveedor. Ejecute estos comandos como administrador:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
4. Instale el proveedor:

        C:\ASR> setupdr.exe /i
5. Registre el proveedor:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

Parámetros:

* **/Credentials**: parámetro obligatorio para la ubicación donde se encuentra el archivo de clave de registro.  
* **/FriendlyName**: parámetro obligatorio para el nombre del servidor host de Hyper-V que aparece en el portal de Azure Site Recovery.
* **/EncryptionEnabled**: parámetro opcional que solo se usa cuando se replica desde VMM en Azure.
* **/proxyAddress**: parámetro opcional que especifica la dirección del servidor proxy.
* **/proxyport**: parámetro opcional que especifica el puerto del servidor proxy.
* **/proxyUsername**: parámetro opcional que especifica el nombre de usuario de proxy (si el proxy requiere autenticación).
* **/proxyPassword**: parámetro opcional que especifica la contraseña para autenticarse con el servidor proxy (si el proxy requiere autenticación).

## <a name="step-3-map-storage-arrays-and-pools"></a>Paso 3: Asignación de grupos y matrices de almacenamiento

Asigne las matrices principal y secundaria para especificar qué bloque de almacenamiento secundario recibe datos de replicación desde el bloque principal. Asigne almacenamiento antes de configurar la replicación, ya que la información de asignación se usa al habilitar la protección de los grupos de replicación.

Antes de iniciar el proceso, asegúrese de que las nubes VMM aparezcan en el almacén. Las nubes se detectan cuando se sincronizan todas las nubes durante la instalación del proveedor o al sincronizar una nube específica en la consola VMM.

1. Haga clic en **Recursos** > **Almacenamiento del servidor** > **Asignar matrices de almacenamiento de origen y destino**.
    ![Registro de servidor](./media/site-recovery-vmm-san/storage-map.png)

2. Seleccione las matrices de almacenamiento en el sitio principal y asígnelas a matrices de almacenamiento en el sitio secundario. En **Bloques de almacenamiento**, seleccione un bloque de almacenamiento de origen y otro de destino para asignarlos.


   ![Registro de servidor](./media/site-recovery-vmm-san/storage-map-pool.png)

## <a name="step-4-configure-replication-settings"></a>Paso 4: Configuración de opciones de replicación

Una vez que los servidores VMM estén registrados, configure la protección de la nube.

1. En la página Inicio rápido, haga clic en **Configurar protección para nubes de VMM**.
2. En la pestaña **Elementos protegidos**, seleccione la nube > **Configuración**.
3. En **Destino**, seleccione **VMM**.
4. En **Ubicación de destino**, seleccione el servidor VMM que administra la nube que desea usar para la recuperación.
5. En **Nube de destino**, seleccione la nube de destino que desea usar para la conmutación por error de máquina virtual.
   * Recomendamos seleccionar una nube de destino que cumpla con los requisitos de recuperación para las máquinas virtuales que protegerá.
   * Una nube solo puede pertenecer a un único par de nubes, ya sea como nube principal o de destino.
6. Site Recovery comprueba que las nubes tengan acceso al almacenamiento de SAN y que las matrices de almacenamiento estén asignadas.
7. Si la comprobación se realiza correctamente, en **Tipo de replicación**, seleccione **SAN**.

Tras guardar la configuración, se crea un trabajo que se puede supervisar en la pestaña **Trabajos**. Puede modificar la configuración en la pestaña **Configurar**. Si desea modificar la ubicación de destino o la nube de destino, deberá eliminar la configuración de la nube y después volver a configurarla.

## <a name="step-5-enable-network-mapping"></a>Paso 5: habilitación de la asignación de redes

1. En la página de inicio rápido, haga clic en **Asignar redes**.
2. Seleccione el servidor VMM de origen y después el servidor VMM de destino al que se asignarán las redes. Se muestra la lista de redes de origen y sus redes de destino asociadas. En el caso de las redes no asignadas, aparece un valor en blanco. Haga clic en el icono de información junto a los nombres de las redes de origen y de destino para ver las subredes para cada de red.
3. Seleccione una red en **Red en origen** y haga clic en **Asignar**. El servicio detecta las redes de máquina virtual en el servidor de destino y las muestra.

    ![Arquitectura SAN](./media/site-recovery-vmm-san/network-map1.png)
4. Seleccione una de las redes de máquina virtual del servidor VMM de destino.


    ![Arquitectura SAN](./media/site-recovery-vmm-san/network-map2.png)
5. Al seleccionar una red de destino, se muestran las nubes protegidas que utilizan la red de origen. También se muestran las redes de destino disponibles. Se recomienda que seleccione una red de destino que esté disponible para todas las nubes que use para la replicación.
6. Haga clic en la marca de verificación para completar el proceso de asignación. Se inicia un trabajo para seguir el progreso. Puede consultarlo en la pestaña **Trabajos** .

## <a name="step-6-enable-replication-for-replication-groups"></a>Paso 6: Habilitación de la replicación para los grupos de replicación

Para poder habilitar la protección de máquinas virtuales, debe habilitar la replicación para grupos de replicación de almacenamiento.

1. En la página de propiedades de la nube principal en el portal de Site Recovery, abra la pestaña **Máquinas virtuales** y haga clic en **Agregar grupo de replicación**.
2. Seleccione uno o más grupos de replicación de VMM asociados con la nube, compruebe las matrices de origen y de destino y especifique la frecuencia de replicación.

Site Recovery, junto con VMM y los proveedores de SMI-S, aprovisionará los LUN de almacenamiento del sitio de destino y habilitará la replicación de almacenamiento. Si el grupo de replicación ya está replicado, Site Recovery reutiliza la relación de replicación existente y actualiza la información.

## <a name="step-7-enable-protection-for-virtual-machines"></a>Paso 7: Habilitación de la protección para las máquinas virtuales


Cuando la replicación del grupo de almacenamiento esté en curso, habilite la protección para máquinas virtuales en la consola VMM mediante uno de los métodos siguientes:

* **Nueva máquina virtual**: cuando cree una máquina virtual, habilite la replicación y asocie la máquina virtual con el grupo de replicación.
  Con esta opción, VMM usa la ubicación inteligente para colocar de forma óptima el almacenamiento de máquina virtual en los LUN del grupo de replicación. Site Recovery organiza la creación de una máquina virtual paralela en el sitio secundario y asigna capacidad para que las máquinas virtuales de réplica se puedan iniciar tras la conmutación por error.
* **Máquina virtual existente**: si ya hay una máquina virtual implementada en VMM, puede habilitar la replicación y realizar una migración del almacenamiento a un grupo de replicación. Tras finalizar, VMM y Site Recovery detectan la nueva máquina virtual y empiezan a administrarla en Site Recovery. Se crea una máquina virtual paralela en el sitio secundario y se asigna capacidad para que se pueda iniciar la máquina virtual de réplica tras la conmutación por error.


    ![Habilitar protección](./media/site-recovery-vmm-san/enable-protect.png)

Una vez habilitadas las máquinas virtuales para la replicación, aparecen en la consola de Site Recovery. Puede ver las propiedades de la máquina virtual, realizar un seguimiento del estado y conmutar por error grupos de replicación que contengan varias máquinas virtuales. En la replicación de SAN, todas las máquinas virtuales asociadas a un grupo de replicación deben realizar la conmutación por error de manera conjunta. Esto es porque la conmutación por error se realiza en la capa de almacenamiento en primer lugar. Es importante agrupar correctamente los grupos de replicación y colocar solo las máquinas virtuales asociadas juntas.

> [!NOTE]
> Después de habilitar la replicación para una máquina virtual, no debe agregar discos duros virtuales para ella a los LUN que no se encuentren en ningún grupo de replicación de Site Recovery. Si lo hace, Site Recovery no los detectará.
>
>

Puede seguir el progreso, incluida la replicación inicial, en la pestaña **Trabajos**. La máquina virtual está preparada para la conmutación por error después de que se finalice el trabajo Finalizar protección.

![Virtual machine protection job](./media/site-recovery-vmm-san/job-props.png)

## <a name="step-8-test-the-deployment"></a>Paso 8: Prueba de la implementación

Pruebe la implementación para asegurarse de que la conmutación por error de las máquinas virtuales se produzca según lo previsto. Para hacerlo, cree un plan de recuperación y ejecute una conmutación por error de prueba.

1. En la pestaña **Planes de recuperación**, haga clic en **Crear plan de recuperación**.
2. Especifique un nombre para el plan de recuperación, y los servidores VMM de origen y destino. El servidor de origen debe tener máquinas virtuales habilitadas para la conmutación por error y la recuperación. Seleccione **SAN** para ver solo las nubes configuradas para la replicación de SAN.

    ![Creación de un plan de recuperación](./media/site-recovery-vmm-san/r-plan.png)

3. En **Seleccionar máquina virtual**, seleccione grupos de replicación. Todas las máquinas virtuales asociadas al grupo se agregan al plan de recuperación. Estas máquinas virtuales se agregan al grupo predeterminado de planes de recuperación (Grupo 1). Puede agregar más grupos si es necesario. Tras la replicación, las máquinas virtuales se inician según el orden de los grupos de planes de recuperación.

    ![Agregar máquinas virtuales](./media/site-recovery-vmm-san/r-plan-vm.png)
4. Una vez creado un plan de recuperación, aparece en la lista de la pestaña **Planes de recuperación**. Seleccione el plan y haga clic en **Probar conmutación por error**.
5. En la página **Confirmar conmutación por error de prueba** seleccione **Ninguno**. Con esta opción habilitada, las máquinas virtuales de réplica conmutadas por error no estarán conectadas a ninguna red. Esto prueba si las máquinas virtuales conmutan por error según lo previsto, pero no prueba el entorno de red. [Lea más información](site-recovery-failover.md#run-a-test-failover) sobre otras opciones de red.

    ![Selección de la red de prueba](./media/site-recovery-vmm-san/test-fail1.png)


1. La máquina virtual de prueba se creará en el mismo host en el que existe la máquina virtual de réplica. No se agregará a la nube en la que se encuentra la máquina virtual de réplica.
6. La máquina virtual de prueba se crea en el mismo host que aquel donde existe la máquina virtual de réplica. No se agrega a la nube en la que se encuentra la máquina virtual de réplica.
2. Después de la replicación, la máquina virtual de réplica tendrá una dirección IP que difiere de la dirección IP de la máquina virtual principal. Si emite direcciones desde DHCP, se actualizará automáticamente. Si no usa DHCP y desea que las direcciones sean las mismas, debe ejecutar un par de scripts.
3. Ejecute este script para recuperar la dirección IP.


       $vm = Get-SCVirtualMachine -Name <VM_NAME>
       $na = $vm[0].VirtualNetworkAdapters>
       $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
       $ip.address  

4. Ejecute este script de ejemplo para actualizar DNS. Especifique la dirección IP que ha recuperado.

       [string]$Zone,
       [string]$name,
       [string]$IP
       )
       $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
       $newrecord = $record.clone()
       $newrecord.RecordData[0].IPv4Address  =  $IP
       Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


## <a name="next-steps"></a>Pasos siguientes

Cuando haya ejecutado una conmutación por error de prueba para comprobar que su entorno funciona según lo esperado, [conozca](site-recovery-failover.md) cuáles son los diferentes tipos de conmutaciones por error.




<!--HONumber=Jan17_HO5-->


