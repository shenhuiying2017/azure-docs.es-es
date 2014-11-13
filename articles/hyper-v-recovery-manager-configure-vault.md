<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Introducci&oacute;n a la recuperaci&oacute;n de sitios de Azure: Protecci&oacute;n de instalaciones locales a instalaciones locales" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordina la replicaci&oacute;n, la conmutaci&oacute;n por error y la recuperaci&oacute;n de m&aacute;quinas virtuales de Hyper-V ubicadas en nubes VMM o en instalaciones locales a otro sitio local." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Introducci&oacute;n a la recuperaci&oacute;n de sitios de Azure: Protecci&oacute;n de instalaciones locales a instalaciones locales" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# Introducción a la recuperación de sitios de Azure: Protección de instalaciones locales a instalaciones locales

<div class="dev-callout">

Utilice Azure Site Recovery para organizar la protección de máquinas virtuales en servidores host de Hyper-V locales ubicados en nubes VMM. Puede configurar:

-   **Protección de instalaciones locales a instalaciones locales**: Replicar máquinas virtuales locales a otras instalaciones locales. Configure y habilite la configuración de protección en los almacenes de Azure Site Recovery. Los datos de máquinas virtuales se replican de un servidor Hyper-V local a otro. Puede obtener más información sobre este escenario en [Introducción a Azure Site Recovery: protección de local a local][Introducción a Azure Site Recovery: protección de local a local].
-   **Protección de instalaciones locales a instalaciones de Azure**: Replicar máquinas virtuales locales a máquinas de Azure. Configure y habilite la configuración de protección en los almacenes de Azure Site Recovery. Los datos de máquinas virtuales se replican de un servidor Hyper-V local al almacenamiento de Azure. Puede obtener más información sobre este escenario en [Introducción a Azure Site Recovery: Protección de instalaciones locales a Azure][Introducción a Azure Site Recovery: Protección de instalaciones locales a Azure]

## <span id="about"></span></a>Acerca de este tutorial

Utilice este tutorial para configurar una prueba de concepto rápida para Azure Site Recovery en una implementación de local a Azure. Se utiliza la ruta más rápida y la configuración predeterminada siempre que es posible. Creará un almacén de Azure Site Recovery, instalará el proveedor de Azure Site Recovery en el servidor VMM, configurará los parámetros de protección de la nube, habilitará la protección de máquinas virtuales y comprobará su implementación.

Si desea obtener más información acerca de la implementación completa vea:

-   [Plan de implementación de Azure Site Recovery][Plan de implementación de Azure Site Recovery]: Describe los pasos del plan que debe realizar antes de comenzar una implementación completa.
-   [Implementación de Azure Site Recovery: Protección de instalaciones locales a instalaciones locales][Implementación de Azure Site Recovery: Protección de instalaciones locales a instalaciones locales]: Proporciona instrucciones paso a paso para realizar una implementación completa.

Si experimenta problemas durante este tutorial, revise el artículo de wiki [Azure Site Recovery: escenarios de error comunes y soluciones][Azure Site Recovery: escenarios de error comunes y soluciones], o publique sus preguntas en el [foro de Servicios de recuperación de Azure][foro de Servicios de recuperación de Azure].

</div>

## <span id="before"></span></a>Antes de empezar

<div class="dev-callout">

Antes de empezar con este tutorial, compruebe los requisitos previos.

### <span id="HVRMPrereq"></span></a>Requisitos previos

-   **Cuenta de Azure**: Necesitará una cuenta de Azure. Si no tiene una, consulte [evaluación gratuita de Azure][evaluación gratuita de Azure]. Puede obtener información sobre precios en la página de [detalles de precios de Azure Site Recovery Manager][detalles de precios de Azure Site Recovery Manager].
-   **Servidor VMM**: Necesitará al menos un servidor VMM que se ejecute en System Center 2012 SP1 o en System Center 2012 R2.
-   **Nubes VMM**: necesitará al menos una nube en el servidor VMM de origen que desea proteger y otra en el servidor VMM de destino. Si utiliza un servidor VMM, necesitará dos nubes. La nube principal que desea proteger debe contener:
    -   Uno o más grupos de hosts de VMM
    -   Uno o más servidores host de Hyper-V o clústeres en cada grupo de hosts
    -   Una o varias máquinas virtuales ubicadas en el servidor de Hyper-V de origen en la nube.

## <span id="tutorial"></span></a>Pasos del tutorial

Después de comprobar los requisitos previos, realice las siguientes tareas:

-   [Paso 1: Crear una almacén][Paso 1: Crear una almacén]: Crear un almacén para Azure Site Recovery.
-   [Paso 2: Instalar la aplicación del proveedor][Paso 2: Instalar la aplicación del proveedor]: Genere una clave de registro y ejecute la aplicación del proveedor de Microsoft Azure Site Recovery en el servidor VMM. De esta forma se instala el proveedor y se registra el servidor VMM en el almacén.
-   [Paso 3: Configurar la protección de la nube][Paso 3: Configurar la protección de la nube]: Configure los parámetros de protección para las nubes VMM.
-   <a href="#networkmapping">Paso 5: Configurar la asignación de red: Opcionalmente, puede configurar la asignación de red para signar redes VM de origen a redes VM de destino.
-   [Paso 6: Configurar la asignación de almacenamiento][Paso 6: Configurar la asignación de almacenamiento]: Opcionalmente, puede configurar la asignación de almacenamiento para asignar clasificaciones de almacenamiento en el servidor VM de origen a clasificaciones de almacenamiento en el servidor VM de destino.
-   [Paso 7: Habilitar protección para las máquinas virtuales][Paso 7: Habilitar protección para las máquinas virtuales]: Habilite la protección para las máquinas virtuales ubicadas en las nubes VMM.
-   [Paso 8: Configurar y ejecutar planes de recuperación][Paso 8: Configurar y ejecutar planes de recuperación]: Cree un plan de recuperación y ejecute una conmutación por error de prueba para el plan.

<a name="vault"></a>

## Paso 1: Creación de un almacén

</p>
1.  Inicie sesión en el [Portal de administración][Portal de administración].

2.  Expanda **Servicios de datos**, luego **Servicios de recuperación** y haga clic en **Almacén de Site Recovery**.

3.  Haga clic en **Crear nuevo** y luego en **Creación rápida**.

4.  En **Name**, escriba un nombre descriptivo para identificar el almacén.

5.  En **Región**, seleccione la región geográfica del almacén. Para comprobar las regiones admitidas vea Disponibilidad geográfico en [Detalles de precios de Azure Site Recovery][Detalles de precios de Azure Site Recovery]

6.  Haga clic en **Create vault**.

    ![Almacén nuevo][Almacén nuevo]

Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como **Activo** en la página principal de Servicios de recuperación.

<a name="upload"></a>

## Paso 2: Configuración del almacén

</p>
1.  En la página **Servicios de recuperación**, haga clic en el almacén para abrir la página de inicio rápido. El inicio rápido también se puede abrir en cualquier momento mediante el icono.

    ![Icono de inicio rápido][Icono de inicio rápido]

2.  En la lista desplegable seleccione **Entre dos sitio Hyper-V locales**.
3.  En **Preparar servidores VMM**, haga clic en el archivo **Generar clave de registro**. La clave será válida para 5 días a partir del momento en que se genera. Copie el archivo al servidor VMM. Lo necesitará al configurar el proveedor.

    ![Clave de registro][Clave de registro]

<a name="download"></a>

## Paso 3: Instalación del proveedor de Azure Site Recovery

</p>
1.  En la página de **Inicio rápido**, en **Preparar servidores VMM**, haga clic en **Descargar el proveedor de Microsoft Azure Site Recovery para la instalación en servidores VMM** a fin de obtener la versión más reciente del archivo de instalación del proveedor.

2.  Ejecute el archivo en servidores VMM de origen y de destino.

3.  En la **Comprobación de requisitos previos** seleccione detener el servicio VMM para iniciar la configuración del proveedor. Este servicio se detendrá y se volverá a iniciar automáticamente cuando finalice la configuración.

    ![Requisitos previos][Requisitos previos]

4.  En **Microsoft Update** puede optar por recibir actualizaciones. Con esta configuración habilitada, las actualizaciones del proveedor se instalarán según su directiva de Microsoft Update.

    ![Microsoft Updates][Microsoft Updates]

Una vez instalado el proveedor, siga con la instalación para registrar el servidor en el almacén.

1.  En la página de conexión a Internet, especifique cómo se conecta a Internet el proveedor que se ejecuta en el servidor VMM. Seleccione **Utilizar la configuración proxy del sistema predeterminado** para utilizar los parámetros de conexión a Internet predeterminados configurados en el servidor.

    ![Configuración de Internet][Configuración de Internet]

2.  En **Clave de registro**, seleccione la que ha descargado de Azure Site Recovery y copiado en el servidor VMM.
3.  En **Nombre del almacén**, compruebe el nombre del almacén en el que se registrará el servidor.
4.  En **Nombre del servidor**, especifique un nombre descriptivo para identificar el servidor VMM en el almacén.

    ![Registro de servidor][Registro de servidor]

5.  En la sincronización de **Metadatos de la nube inicial** seleccione si desea sincronizar los metadatos de todas las nubes en el servidor de VMM con el almacén. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar este parámetro sin marcar y sincronizar cada nube individualmente en las propiedades de la nube de la consola de VMM.

6.  En **Cifrado de datos** puede generar un certificado que se utiliza para cifrar datos protegidos en Azure.
    Esta opción no es relevante si está replicando desde un sitio local a otro.

    ![Registro de servidor][1]

7.  Haga clic en **Register** para finalizar el proceso. Después del registro, la Recuperación del sitio de Azure recupera los metadatos del servidor VMM. El servidor se mostrará en el extremo de la pestaña **Recursos** en la página **Servidores** del almacén.

## <span id="clouds"></span></a>Paso 4: Configurar la protección de la nube

Una vez que los servidores VMM están registrados, puede configurar la protección de la nube. La opción **Sincronizar datos de nube con el almacén** se habilita cuando se instala el proveedor, de modo que todas las nubes del servidor VMM aparecerán en la pestaña **Elementos protegidos** del almacén.

![Nube publicada][Nube publicada]

1.  En la página de inicio rápido, haga clic en **Configurar protección para nubes VMM**.
2.  En la pestaña **Protected Items**, seleccione la nube que desea configurar y vaya a la pestaña **Configuration**. Observe lo siguiente:
3.  En **Target**, seleccione **VMM**.
4.  En **Target location**, seleccione el servidor VMM que administra la nube que desea utilizar para recuperación.
5.  En **Target cloud**, seleccione la nube de destino que desea utilizar para la conmutación por error de máquinas virtuales en la nube de origen. Observe lo siguiente:

    -   Recomendamos seleccionar una nube de destino que cumpla con los requisitos de recuperación para las máquinas virtuales que protegerá.
    -   Una nube solo puede pertenecer a un único par de nubes, ya sea como nube principal o de destino.

6.  En **Copiar frecuencia**, deje la configuración predeterminada. Este valor especifica la frecuencia con que se deben sincronizar los datos entre las ubicaciones de origen y de destino. Solo es pertinente cuando el host de Hyper-V ejecuta Windows Server 2012 R2. En el caso de otros servidores, se utiliza una configuración predeterminada de cinco minutos.
7.  En **Additional recovery points**, deje la configuración predeterminada. Este valor especifica si desea crear puntos de recuperación adicionales. El valor predeterminado de cero especifica que el punto de recuperación más reciente para una máquina virtual es el único que se almacena en un servidor host de réplica.
8.  En **Frecuencia de las instantáneas coherentes con la aplicación**, deje la configuración predeterminada. Este valor especifica la frecuencia de creación de instantáneas. Las instantáneas utilizan el Servicio de instantáneas de volumen (VSS) para asegurarse de que las aplicaciones se encuentren en un estado coherente cuando se captura la instantánea. Si de todos modos desea definir este valor para el tutorial, asegúrese de definirlo en un número menor que la cantidad de puntos de recuperación adicionales que configure.
    ![Configure protection settings][Configure protection settings]
9.  En **Data transfer compressed**, especifique si se deben comprimir los datos replicados que se transfieren.
10. En **Authentication**, especifique cómo se autentica el tráfico entre el servidor host de Hyper-V principal y el servidor host de Hyper-V de recuperación. Para este tutorial, seleccione HTTPS, a no ser que tenga configurado un entorno Kerberos activo. Azure Site Recovery configurará automáticamente los certificados para la autenticación de HTTPS. No se requiere configuración manual. Observe que esta configuración solo es pertinente para servidores host de Hyper-V que se ejecutan en Windows Server 2012 R2.
11. En **Port**, deje la configuración predeterminada. Este valor define el número de puerto en el que los equipos host de Hyper-V de origen y de destino escuchan el tráfico de replicación.
12. En **Replication method**, especifique cómo se manejará la replicación inicial de los datos desde la ubicación de origen a la ubicación de destino, antes de que comience la replicación normal.

    -   **Over the network**: Copiar los datos sobre la red puede ser un proceso lento y que consume muchos recursos. Le recomendamos utilizar esta opción si la nube contiene máquinas virtuales con discos duros virtuales relativamente pequeños y si el servidor VMM principal está conectado al servidor VMM secundario a través de una conexión con un amplio ancho de banda. Puede especificar que la copia se inicie de inmediato, o bien, seleccionar una hora. Si utiliza la replicación de red, le recomendamos programarla evitando las horas de mayor consumo.
    -   **Offline**: este método especifica que la replicación inicial se realizará usando medios externos. Esta opción resulta útil si desea evitar la degradación del rendimiento de la red o en el caso de ubicaciones geográficamente remotas. Para utilizar este método, especifique la ubicación de exportación en la nube de origen y la ubicación de importación en la nube de destino. Cuando habilita la protección de una máquina virtual, el disco duro virtual se copia en la ubicación de exportación especificada. Usted lo envía al sitio de destino y lo copia en la ubicación de importación. El sistema copia la información importada en las máquinas virtuales de réplica. Si desea obtener una lista completa de los requisitos previos para la replicación sin conexión, consulte [Paso 3: Configurar la protección para nubes VMM][Paso 3: Configurar la protección para nubes VMM] en la Guía de implementación.

13. Seleccione **Delete Replica Virtual Machine** para especificar la máquina virtual de réplica que se debe eliminar si deja de proteger la máquina virtual al seleccionar la opción **Delete protection for the virtual machine** en la pestaña de máquinas virtuales de las propiedades de la nube. Con esta configuración deshabilitada, cuando deshabilite la protección, la máquina virtual se elimina de Azure Site Recovery, la configuración de Site Recovery para la máquina virtual se elimina de la consola VMM y la réplica se elimina.
    ![Configure protection settings][2]

Después de guardar la configuración, se creará un trabajo, que puede supervisar en la pestaña **Trabajos**. Todos los servidores host de Hyper-V de la nube de origen VMM se configurarán para la replicación. Puede modificar la configuración de la nube en la pestaña **Configure**. Si desea modificar la ubicación de destino o la nube de destino, deberá eliminar la configuración de la nube y después volver a configurarla.

## <span id="networkmapping"></span></a>Paso 5: Configurar asignación de red

En este tutorial se describe la ruta más sencilla para implementar Azure Site Recovery en un entorno de prueba. Para configurar la asignación de red como parte de este tutorial, lea [Preparar la asignación de red][Preparar la asignación de red] en la Guía de planeación. Para configurar la asignación siga los pasos hasta [Configurar la asignación de red][Configurar la asignación de red] en la guía de implementación.

## <span id="storagemapping"></span></a>Paso 6: Configurar la asignación de almacenamiento

En este tutorial se describe la ruta más sencilla para implementar Azure Site Recovery en un entorno de prueba. Para configurar la asignación de almacenamiento como parte de este tutorial, lea [Configurar la asignación de almacenamiento][Configurar la asignación de almacenamiento] en la guía de implementación.

## <span id="enablevirtual"></span></a>Paso 7: Enable virtual machine protection

Una vez que los servidores, las nubes y las redes se configuran correctamente, puede habilitar la protección para las máquinas virtuales en la nube.

1.  En la pestaña **Virtual Machines** en la nube en la que está situada la máquina virtual, haga clic en **Enable protection** y después seleccione **Add virtual machines**.
2.  En la lista de máquinas virtuales de la nube, seleccione la que desea proteger.

![Enable virtual machine protection][Enable virtual machine protection]

Siga el progreso de la acción de habilitación de la protección en la pestaña \*\*Jobs\*\*, incluida la replicación inicial. La máquina virtual estará preparada para la conmutación por error después de que finalice el trabajo de Finalizar protección. Después de habilitar la protección y replicar las máquinas virtuales, podrá verlas en Azure.

![Virtual machine protection job][Virtual machine protection job]

## <span id="recoveryplans"></span></a>Paso 8: Prueba de la implementación

Para probar la implementación puede realizar una prueba de conmutación por error para una máquina virtual individual o crear un plan de recuperación que incluya numerosas máquinas virtuales y realizar una conmutación por error de prueba para el plan. La conmutación por error de prueba simula su mecanismo de conmutación por error y recuperación en una red aislada.

-   Para obtener instrucciones acerca de la creación de un plan de recuperación vea [Crear y personalizar los planes de recuperación De local a Azure][Crear y personalizar los planes de recuperación De local a Azure].
-   Para obtener instrucciones acerca de la realización de conmutaciones por error de prueba vea [Prueba de una implementación de instalaciones locales a instalaciones locales][Prueba de una implementación de instalaciones locales a instalaciones locales].

### <span id="runtest"></span></a>Supervisión de la actividad

Puede usar la pestaña **Trabajos** y el **Panel** para ver y supervisar los principales trabajos que se realizan en el almacén de Azure Site Recovery, como la configuración de la protección para una nube, la habilitación y deshabilitación de la protección para una máquina virtual, la ejecución de una conmutación por error (planificada, no planificada o de prueba) y la realización de una conmutación por error no planificada.

En la pestaña **Trabajos**, puede ver los trabajos, profundizar en los detalles y los errores del trabajo, ejecutar consultas del trabajos para recuperar aquellos que coincidan con criterios específicos, exportar trabajos a Excel y reiniciar los trabajos con errores.

En el **Panel**, puede descargar las versiones más recientes de los archivos de instalación del proveedor y el agente, obtener información de configuración del almacén, ver el número de máquinas virtuales cuya protección se gestiona en el almacén, ver los trabajos recientes, administrar el certificado del almacén y volver a sincronizar las máquinas virtuales.

Para obtener más información sobre la interacción con los trabajos y el panel, consulte la [guía de operaciones y supervisión][guía de operaciones y supervisión].

## <span id="next"></span></a>Pasos siguientes

-   Para planificar e implementar Azure Site Recovery en un entorno completo de producción, consulte [Guía de planificación de Azure Site Recovery][Plan de implementación de Azure Site Recovery] y [Guía de implementación de Azure Site Recovery][Implementación de Azure Site Recovery: Protección de instalaciones locales a instalaciones locales].
-   Si tiene preguntas, visite el [foro de Servicios de recuperación de Azure][foro de Servicios de recuperación de Azure].

</div>

  [Introducción a Azure Site Recovery: protección de local a local]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Introducción a Azure Site Recovery: Protección de instalaciones locales a Azure]: http://go.microsoft.com/fwlink/?LinkId=398764
  [Plan de implementación de Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Implementación de Azure Site Recovery: Protección de instalaciones locales a instalaciones locales]: http://go.microsoft.com/fwlink/?LinkId=321295
  [Azure Site Recovery: escenarios de error comunes y soluciones]: http://go.microsoft.com/fwlink/?LinkId=389879
  [foro de Servicios de recuperación de Azure]: http://go.microsoft.com/fwlink/?LinkId=313628
  [evaluación gratuita de Azure]: http://aka.ms/try-azure
  [detalles de precios de Azure Site Recovery Manager]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Paso 1: Crear una almacén]: #vault
  [Paso 2: Instalar la aplicación del proveedor]: #download
  [Paso 3: Configurar la protección de la nube]: #clouds
  [Paso 6: Configurar la asignación de almacenamiento]: #storagemapping
  [Paso 7: Habilitar protección para las máquinas virtuales]: #enablevirtual
  [Paso 8: Configurar y ejecutar planes de recuperación]: #recovery%20plans
  [Portal de administración]: https://manage.windowsazure.com
  [Detalles de precios de Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=389880
  [Almacén nuevo]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Icono de inicio rápido]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Clave de registro]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png
  [Requisitos previos]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Microsoft Updates]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [Configuración de Internet]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Registro de servidor]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [Nube publicada]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Configure protection settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png
  [Paso 3: Configurar la protección para nubes VMM]: http://go.microsoft.com/fwlink/?LinkId=323469
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png
  [Preparar la asignación de red]: http://go.microsoft.com/fwlink/?LinkId=324817
  [Configurar la asignación de red]: http://go.microsoft.com/fwlink/?LinkId=402534
  [Configurar la asignación de almacenamiento]: http://go.microsoft.com/fwlink/?LinkId=402535
  [Enable virtual machine protection]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Virtual machine protection job]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Crear y personalizar los planes de recuperación De local a Azure]: http://go.microsoft.com/fwlink/?LinkId=511492
  [Prueba de una implementación de instalaciones locales a instalaciones locales]: http://go.microsoft.com/fwlink/?LinkId=511493
  [guía de operaciones y supervisión]: http://go.microsoft.com/fwlink/?LinkId=398534
