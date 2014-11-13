<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Introducci&oacute;n a la recuperaci&oacute;n de sitios de Azure: protecci&oacute;n de local a Azure" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordina la replicaci&oacute;n, la conmutaci&oacute;n por error y la recuperaci&oacute;n de m&aacute;quinas virtuales de Hyper-V ubicadas en nubes VMM o en instalaciones locales a Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Introducci&oacute;n a la recuperaci&oacute;n de sitios de Azure: protecci&oacute;n de local a Azure" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# Introducción a la recuperación de sitios de Azure: protección de local a Azure

<div class="dev-callout">

Utilice Azure Site Recovery para organizar la protección de máquinas virtuales en servidores host de Hyper-V locales ubicados en nubes VMM. Puede configurar:

-   **Protección de instalaciones locales a instalaciones de Azure**: Replicar máquinas virtuales locales a máquinas de Azure. Configure y habilite la configuración de protección en los almacenes de Azure Site Recovery. Los datos de máquinas virtuales se replican de un servidor Hyper-V local al almacenamiento de Azure.
-   **Protección de instalaciones locales a instalaciones locales**: Replicar máquinas virtuales locales a otras instalaciones locales. Configure y habilite la configuración de protección en los almacenes de Azure Site Recovery. Los datos de máquinas virtuales se replican de un servidor Hyper-V local a otro. Puede obtener más información sobre este escenario en [Introducción a Azure Site Recovery: protección de local a local][Introducción a Azure Site Recovery: protección de local a local].

## <span id="about"></span></a>Acerca de este tutorial

Utilice este tutorial para configurar una prueba de concepto rápida para Azure Site Recovery en una implementación de local a Azure. Se utiliza la ruta más rápida y la configuración predeterminada siempre que es posible. Creará un almacén de Azure Site Recovery, instalará el proveedor de Azure Site Recovery en el servidor VMM, instalará el agente de los Servicios de recuperación de Azure en servidores host de Hyper-V situados en nubes VMM, configurará los parámetros de protección de la nube, habilitará la protección de máquinas virtuales y comprobará su implementación.

Si desea obtener más información acerca de la implementación completa vea:

-   [Plan de implementación de Azure Site Recovery][Plan de implementación de Azure Site Recovery]: Describe los pasos del plan que deberá realizar antes de comenzar una implementación completa.
-   [Implementación de Azure Site Recovery: protección de instalaciones locales a Azure][Implementación de Azure Site Recovery: protección de instalaciones locales a Azure]: En este tema se proporcionan instrucciones paso a paso para realizar una implementación completa.

Si experimenta problemas durante este tutorial, revise el artículo de wiki [Azure Site Recovery: escenarios de error comunes y soluciones][Azure Site Recovery: escenarios de error comunes y soluciones], o publique sus preguntas en el [foro de Servicios de recuperación de Azure][foro de Servicios de recuperación de Azure].

</div>

## <span id="before"></span></a>Antes de empezar

<div class="dev-callout">

Antes de empezar con este tutorial, compruebe los requisitos previos.

### <span id="HVRMPrereq"></span></a>Requisitos previos

-   **Cuenta de Azure**: Necesitará una cuenta de Azure. Si no tiene una, consulte [evaluación gratuita de Azure][evaluación gratuita de Azure]. Puede obtener información sobre precios en la página de [detalles de precios de Azure Site Recovery Manager][detalles de precios de Azure Site Recovery Manager].
-   **Cuenta de almacenamiento de Azure**: Necesitará una cuenta de almacenamiento de Azure para almacenar los datos replicados en Azure. La cuenta debe tener habilitada la replicación geográfica. Además, debe estar en la misma región que el servicio Azure Site Recovery y estar asociada a la misma suscripción. Para obtener más información sobre la configuración del almacenamiento de Azure, consulte [Introducción al almacenamiento de Microsoft Azure][Introducción al almacenamiento de Microsoft Azure].
-   **Servidor VMM**: un servidor VMM que se ejecute en System Center 2012 R2.
-   **Nubes VMM**: una nube al menos en el servidor VMM que contenga:
    -   Uno o más grupos de hosts de VMM
    -   Uno o más servidores host de Hyper-V o clústeres en cada grupo de hosts
    -   Una o varias máquinas virtuales ubicadas en el servidor de Hyper-V de origen en la nube. Las máquinas virtuales deben ser de la generación 1.
-   **Máquina virtual**: necesitará máquinas virtuales que cumplan con los requisitos de Azure. Consulte [Requisitos previos y compatibilidad][Requisitos previos y compatibilidad] en la guía de planificación.
-   Para obtener una lista completa de los requisitos de máquinas virtuales para conmutación por error a Azure, lea

## <span id="tutorial"></span></a>Pasos del tutorial

Después de comprobar los requisitos previos, realice las siguientes tareas:

-   [Paso 1: Crear una almacén][Paso 1: Crear una almacén]: Crear un almacén para Azure Site Recovery.
-   [Paso 2: Instalar la aplicación del proveedor][Paso 2: Instalar la aplicación del proveedor]: Genere una clave de registro y ejecute la aplicación del proveedor de Microsoft Azure Site Recovery en el servidor VMM. De esta forma se instala el proveedor y se registra el servidor VMM en el almacén.
-   [Paso 3: Agregar una cuenta de almacenamiento de Azure][Paso 3: Agregar una cuenta de almacenamiento de Azure]: si no tiene una cuenta, cree una.
-   [Paso 4: Instalar la aplicación del agente][Paso 4: Instalar la aplicación del agente]: instale el agente de los Servicios de recuperación de Microsoft Azure en cada host de Hyper-V.
-   [Paso 5: Configurar la protección de la nube][Paso 5: Configurar la protección de la nube]: Configure los parámetros de protección para las nubes VMM.
-   [Paso 6: Configurar la asignación de red][Paso 6: Configurar la asignación de red]: opcionalmente puede configurar la asignación de red para asignar redes VM de origen a redes Azure de destino.
-   [Paso 7: Habilitar la protección para las máquinas virtuales][Paso 7: Habilitar la protección para las máquinas virtuales]: habilite la protección para las máquinas virtuales ubicadas en nubes VMM protegidas.
-   [Paso 8: Probar la implementación][Paso 8: Probar la implementación]: puede ejecutar una conmutación por error de prueba para una sola máquina virtual o puede crear un plan de recuperación y ejecutar una conmutación por error de prueba para el plan.

<a name="vault"></a>

## Paso 1: Creación de un almacén

</p>
1.  Inicie sesión en el [Portal de administración][Portal de administración].

2.  Expanda **Servicios de datos**, luego **Servicios de recuperación** y haga clic en **Almacén de Site Recovery**.

3.  Haga clic en **Crear nuevo** y luego en **Creación rápida**.

4.  En **Name**, escriba un nombre descriptivo para identificar el almacén.

5.  En **Región**, seleccione la región geográfica del almacén. Las regiones geográficas disponibles incluyen Asia Oriental, Europa Occidental, Oeste de los EE. UU., Este de EE. UU., Europa del Norte y Sudeste asiático.
6.  Haga clic en **Create vault**.

    ![Almacén nuevo][Almacén nuevo]

Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como **Activo** en la página principal de Servicios de recuperación.

<a name="download"></a>

## Paso 2: Generación de una clave de registro e instalación del proveedor de Azure Site Recovery

</p>
1.  En la página **Servicios de recuperación**, haga clic en el almacén para abrir la página de inicio rápido. El inicio rápido también se puede abrir en cualquier momento mediante el icono.

    ![Icono de inicio rápido][Icono de inicio rápido]

2.  En la lista desplegable seleccione **Entre un sitio Hyper-V local y Microsoft Azure**.
3.  En **Preparar servidores VMM**, haga clic en el archivo **Generar clave de registro**. La clave será válida para 5 días a partir del momento en que se genera. Copie el archivo al servidor VMM. Lo necesitará al configurar el proveedor.

    ![Clave de registro][Clave de registro]

4.  En la página de **Inicio rápido**, en **Preparar servidores VMM**, haga clic en **Descargar el proveedor de Microsoft Azure Site Recovery para la instalación en servidores VMM** a fin de obtener la versión más reciente del archivo de instalación del proveedor.

5.  Ejecute este archivo en el servidor VMM de origen.

6.  En la **Comprobación de requisitos previos** seleccione detener el servicio VMM para iniciar la configuración del proveedor. Este servicio se detendrá y se volverá a iniciar automáticamente cuando finalice la configuración.

    ![Requisitos previos][Requisitos previos]

7.  En **Microsoft Update** puede optar por recibir actualizaciones. Con esta configuración habilitada, las actualizaciones del proveedor se instalarán según su directiva de Microsoft Update.

    ![Microsoft Updates][Microsoft Updates]

Una vez instalado el proveedor, siga con la instalación para registrar el servidor en el almacén.

1.  En **Conexión a Internet**, especifique cómo se conecta a Internet el proveedor que se ejecuta en el servidor VMM. Seleccione **Utilizar la configuración proxy del sistema predeterminado** para usar la configuración predeterminada de conexión a Internet establecida en el servidor.

    ![Configuración de Internet][Configuración de Internet]

2.  En **Clave de registro**, seleccione la que ha descargado de Azure Site Recovery y copiado en el servidor VMM.
3.  En **Nombre del almacén**, compruebe el nombre del almacén en el que se registrará el servidor.
4.  En **Nombre del servidor**, especifique un nombre descriptivo para identificar el servidor VMM en el almacén.

    ![Registro de servidor][Registro de servidor]

5.  En la sincronización de **Metadatos de la nube inicial** seleccione si desea sincronizar los metadatos de todas las nubes en el servidor de VMM con el almacén. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar este parámetro sin marcar y sincronizar cada nube individualmente en las propiedades de la nube de la consola de VMM.

6.  En **Cifrado de datos**, especifique una ubicación para guardar un certificado SSL que se genera automáticamente para el cifrado de datos. Este certificado se usa si habilita el cifrado de datos para una nube protegida por Azure en el portal de Azure Site Recovery. Mantenga el certificado en un lugar seguro. Cuando ejecute una conmutación por error a Azure, lo seleccionará para descifrar los datos cifrados.
    Esta opción no es relevante si replica desde un sitio local a otro.

    ![Registro de servidor][1]

7.  Haga clic en **Register** para finalizar el proceso. Después del registro, la Recuperación del sitio de Azure recupera los metadatos del servidor VMM. El servidor se mostrará en el extremo de la pestaña **Recursos** en la página **Servidores** del almacén.

## <span id="storage"></span></a>Paso 3: Crear una cuenta de almacenamiento de Azure

Si no dispone de una cuenta de almacenamiento de Azure, haga clic en **Agregar una cuenta de almacenamiento de Azure**. La cuenta debe tener habilitada la replicación geográfica. Además, debe estar en la misma región que el servicio Azure Site Recovery y estar asociada a la misma suscripción.

Utilice este tutorial para configurar una prueba de concepto rápida para Azure Site Recovery en una implementación de local a Azure. Se utiliza la ruta más rápida y la configuración predeterminada siempre que es posible. Creará un almacén de Azure Site Recovery, instalará el proveedor de Azure Site Recovery en el servidor VMM, instalará el agente de los Servicios de recuperación de Azure en servidores host de Hyper-V situados en nubes VMM, configurará los parámetros de protección de la nube, habilitará la protección de máquinas virtuales y comprobará su implementación.

![Cuenta de almacenamiento][Cuenta de almacenamiento]

## <span id="agent"></span></a>Paso 4: Instalación del agente de los Servicios de recuperación de Azure en servidores host de Hyper-V

Instale el agente de los Servicios de recuperación de Azure en cada servidor host de Hyper-V situado en las nubes VMM que desea proteger.

1.  En la página de inicio rápido, haga clic en **Descargar el agente de los servicios de Azure Site Recovery e instalar en los hosts** para obtener la última versión del archivo de instalación del agente.

    ![Install Recovery Services Agent][Install Recovery Services Agent]

2.  Ejecute el archivo de instalación en cada servidor host de Hyper-V situado en las nubes VMM que desea proteger.
3.  En la página **Comprobación de requisitos previos**, haga clic en **Siguiente**. Todos los requisitos previos que falten se instalarán automáticamente.

    ![Prerequisites Recovery Services Agent][Prerequisites Recovery Services Agent]

4.  En la página **Configuración de la instalación**, especifique dónde desea instalar el agente y seleccione la ubicación de la caché en la que se instalarán los metadatos de copia de seguridad. Luego haga clic en **Instalar**.

## <span id="clouds"></span></a>Paso 5: Configurar la protección de la nube

Una vez que los servidores VMM están registrados, puede configurar la protección de la nube. La opción **Sincronizar datos de nube con el almacén** se habilita cuando se instala el proveedor, de modo que todas las nubes del servidor VMM aparecerán en la pestaña **Elementos protegidos** del almacén.

![Nube publicada][Nube publicada]

1.  En la página de inicio rápido, haga clic en **Configurar protección para nubes VMM**.

2.  En la pestaña **Elementos protegidos**, haga clic en la nube que desea configurar y vaya a la pestaña **Configuración**.
3.  En **Destino**, seleccione **Microsoft Azure**.
4.  En **Cuenta de almacenamiento**, seleccione la cuenta de Azure que desea usar para almacenar las máquinas virtuales de Azure.
5.  Establezca **Cifrar datos almacenados** en **Desactivado**. Este valor especifica que los datos de deben cifrar replicados entre el sitio local y Azure.
6.  En **Copiar frecuencia**, deje la configuración predeterminada. Este valor especifica la frecuencia con que se deben sincronizar los datos entre las ubicaciones de origen y de destino.
7.  En **Retener puntos de recuperación para**, deje la configuración predeterminada. Con un valor predeterminado de cero, el punto de recuperación más reciente para una máquina virtual es el único que se almacena en un servidor host de réplica.
8.  En **Frecuencia de las instantáneas coherentes con la aplicación**, deje la configuración predeterminada. Este valor especifica la frecuencia de creación de instantáneas. Las instantáneas utilizan el Servicio de instantáneas de volumen (VSS) para asegurarse de que las aplicaciones se encuentren en un estado coherente cuando se captura la instantánea. Si establece un valor, asegúrese de que sea inferior al número de puntos de recuperación adicionales que configure.
9.  En **Hora de inicio de la replicación**, especifique cuándo debe comenzar la replicación de los datos en Azure. Se usará la zona horaria del servidor host de Hyper-V. Se recomienda que programe la replicación inicial durante las horas de menos actividad.

    ![Cloud replication settings][Cloud replication settings]

Después de guardar la configuración, se creará un trabajo, que puede supervisar en la pestaña **Trabajos**. Todos los servidores host de Hyper-V de la nube de origen VMM se configurarán para la replicación. Tras guardar, puede modificar la configuración de la nube en la pestaña **Configurar**. Para modificar la ubicación de destino o el almacenamiento de destino, deberá eliminar la configuración de la nube y luego volver a configurar la nube. Tenga en cuenta que si cambia la cuenta de almacenamiento, el cambio solo se aplicará a las máquinas virtuales que tengan la protección habilitada después de que la cuenta de almacenamiento se ha modificado. Las máquinas virtuales existentes no se migrarán a la nueva cuenta de almacenamiento.

## <span id="networkmapping"></span></a>Paso 6: Configurar asignación de red

En este tutorial se describe la ruta más sencilla para implementar Azure Site Recovery en un entorno de prueba. Para configurar la asignación de red como parte de este tutorial, lea [Preparar la asignación de red][Preparar la asignación de red] en la Guía de planeación. Para configurar la asignación siga los pasos hasta [Configurar la asignación de red][Configurar la asignación de red] en la guía de implementación.

## <span id="virtualmachines"></span></a>Paso 7: Habilitar protección para las máquinas virtuales

Una vez que los servidores, las nubes y las redes se configuran correctamente, puede habilitar la protección para las máquinas virtuales en la nube. Tenga en cuenta lo siguiente:

-   Las máquinas virtuales de deben cumplir los requisitos de Azure. Consúltelos en [Requisitos previos y compatibilidad][Requisitos previos y compatibilidad] en la guía de planificación.
-   Para habilitar la protección del sistema operativo y el disco del sistema operativo, deben establecerse las propiedades de la máquina virtual. Al crear una máquina virtual en VMM con una plantilla de máquina virtual puede establecer la propiedad. También puede establecer estas propiedades para máquinas virtuales existentes en las pestañas \*\*General\*\* y \*\*Configuración del hardware\*\* de las propiedades de la máquina virtual. Si no ve estas propiedades en VMM, podrá configurarlas en el portal de Azure Site Recovery.

![Create virtual machine][Create virtual machine]

![Modify virtual machine properties][Modify virtual machine properties]

1.  Para habilitar la protección, en la pestaña **Máquinas virtuales** de la nube en la que se encuentra la máquina virtual, haga clic en **Habilitar protección** y luego seleccione **Agregar máquinas virtuales**.
2.  En la lista de máquinas virtuales de la nube, seleccione la que desea proteger.

    ![Enable virtual machine protection][Enable virtual machine protection]

3.  Compruebe las propiedades de la máquina virtual y modifíquelas según sea necesario.

    ![Verify virtual machines][Verify virtual machines]

Siga el progreso de la acción de habilitación de la protección en la pestaña \*\*Jobs\*\*, incluida la replicación inicial. La máquina virtual estará preparada para la conmutación por error después de que finalice el trabajo de Finalizar protección. Después de habilitar la protección y replicar las máquinas virtuales, podrá verlas en Azure.

![Virtual machine protection job][Virtual machine protection job]

## <span id="test"></span></a>Paso 8: Prueba de la implementación

Para probar la implementación puede realizar una prueba de conmutación por error para una máquina virtual individual o crear un plan de recuperación que incluya numerosas máquinas virtuales y realizar una conmutación por error de prueba para el plan. La conmutación por error de prueba simula su mecanismo de conmutación por error y recuperación en una red aislada. Tenga en cuenta lo siguiente:

-   Si después de la conmutación por error desea conectarse a la máquina virtual de Azure mediante Escritorio remoto, habilite Conexión a Escritorio remoto en la máquina virtual antes de ejecutar la prueba.
-   Después de la conmutación por error, usará una dirección IP pública para conectarse a la máquina virtual de Azure mediante Escritorio remoto. Si desea realizar esto, asegúrese de no tener ninguna directiva de dominio que impida que se conecte a una máquina virtual mediante una dirección pública.

-   Para obtener instrucciones acerca de la creación de un plan de recuperación vea [Crear y personalizar los planes de recuperación De local a Azure][Crear y personalizar los planes de recuperación De local a Azure].
-   Para obtener instrucciones sobre la ejecución de una conmutación por error de prueba, consulte [Prueba de una implementación de instalaciones locales a Azure][Prueba de una implementación de instalaciones locales a Azure].

</p>
### <span id="runtest"></span></a>Supervisión de la actividad

Puede usar la pestaña **Trabajos** y el **Panel** para ver y supervisar los principales trabajos que se realizan en el almacén de Azure Site Recovery, como la configuración de la protección para una nube, la habilitación y deshabilitación de la protección para una máquina virtual, la ejecución de una conmutación por error (planificada, no planificada o de prueba) y la realización de una conmutación por error no planificada.

En la pestaña **Trabajos**, puede ver los trabajos, profundizar en los detalles y los errores del trabajo, ejecutar consultas del trabajos para recuperar aquellos que coincidan con criterios específicos, exportar trabajos a Excel y reiniciar los trabajos con errores.

En el **Panel**, puede descargar las versiones más recientes de los archivos de instalación del proveedor y el agente, obtener información de configuración del almacén, ver el número de máquinas virtuales cuya protección se gestiona en el almacén, ver los trabajos recientes, administrar el certificado del almacén y volver a sincronizar las máquinas virtuales.

Para obtener más información sobre la interacción con los trabajos y el panel, consulte la [guía de operaciones y supervisión][guía de operaciones y supervisión].

## <span id="next"></span></a>Pasos siguientes

-   Para planificar e implementar Azure Site Recovery en un entorno completo de producción, consulte [Guía de planificación de Azure Site Recovery][Plan de implementación de Azure Site Recovery] y [Guía de implementación de Azure Site Recovery][Guía de implementación de Azure Site Recovery].
-   Si tiene preguntas, visite el [foro de Servicios de recuperación de Azure][foro de Servicios de recuperación de Azure].

</div>

  [Introducción a Azure Site Recovery: protección de local a local]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Plan de implementación de Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Implementación de Azure Site Recovery: protección de instalaciones locales a Azure]: http://go.microsoft.com/fwlink/?LinkId=402679
  [Azure Site Recovery: escenarios de error comunes y soluciones]: http://go.microsoft.com/fwlink/?LinkId=389879
  [foro de Servicios de recuperación de Azure]: http://go.microsoft.com/fwlink/?LinkId=313628
  [evaluación gratuita de Azure]: http://aka.ms/try-azure
  [detalles de precios de Azure Site Recovery Manager]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Introducción al almacenamiento de Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=398704
  [Requisitos previos y compatibilidad]: http://go.microsoft.com/fwlink/?LinkId=402602
  [Paso 1: Crear una almacén]: #vault
  [Paso 2: Instalar la aplicación del proveedor]: #download
  [Paso 3: Agregar una cuenta de almacenamiento de Azure]: #storage
  [Paso 4: Instalar la aplicación del agente]: #agent
  [Paso 5: Configurar la protección de la nube]: #clouds
  [Paso 6: Configurar la asignación de red]: #NetworkMapping
  [Paso 7: Habilitar la protección para las máquinas virtuales]: #virtualmachines
  [Paso 8: Probar la implementación]: #test
  [Portal de administración]: https://manage.windowsazure.com
  [Almacén nuevo]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Icono de inicio rápido]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Clave de registro]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ARegisterKey.png
  [Requisitos previos]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Microsoft Updates]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [Configuración de Internet]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Registro de servidor]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [Cuenta de almacenamiento]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2AStorageAgent.png
  [Install Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2AInstallHyperVAgent.png
  [Prerequisites Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png
  [Nube publicada]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Cloud replication settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2A.png
  [Preparar la asignación de red]: http://go.microsoft.com/fwlink/?LinkId=324817
  [Configurar la asignación de red]: http://go.microsoft.com/fwlink/?LinkId=402533
  [Create virtual machine]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ANew.png
  [Modify virtual machine properties]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2AExisting.png
  [Enable virtual machine protection]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ASelectVM.png
  [Verify virtual machines]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVME2AProps.png
  [Virtual machine protection job]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Crear y personalizar los planes de recuperación De local a Azure]: http://go.microsoft.com/fwlink/?LinkId=511492
  [Prueba de una implementación de instalaciones locales a Azure]: http://go.microsoft.com/fwlink/?LinkId=511494
  [guía de operaciones y supervisión]: http://go.microsoft.com/fwlink/?LinkId=398534
  [Guía de implementación de Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321295
