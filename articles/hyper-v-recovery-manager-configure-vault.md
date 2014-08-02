<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-hyper-v-recovery-vault" pageTitle="Configure Hyper-V Recovery Manager to protect virtual machines in VMM clouds" metaKeywords="hyper-v recovery, VMM, clouds, disaster recovery" description="Azure Hyper-V Recovery Manager can help you protect applications and services by coordinating the replication and recovery of virtual machines located in System Center 2012 VMM private clouds." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Configure Azure Hyper-V Recovery Manager" editor="jimbe" manager="cfreeman" authors="" />

Configuración del Administrador de recuperación de Hyper-V de Azure
===================================================================

El Administrador de recuperación de Hyper-V de Azure coordina y administra la protección de máquinas virtuales de Hyper-V ubicadas en nubes privadas en servidores de Virtual Machine Manager (VMM) en System Center 2012 Service Pack 1 (SP1) o System Center 2012 R2. El Administrador de recuperación de Hyper-V organiza la conmutación por error de estas máquinas virtuales desde un servidor host local de Hyper-V a otro. Los almacenes del Administrador de recuperación de Hyper-V en Azure se utilizan para almacenar su configuración de protección.

Acerca de este tutorial
-----------------------

En este tutorial se brinda una guía rápida para la implementación del Administrador de recuperación de Hyper-V. Para obtener información más detallada, lea lo siguiente:

-   [Guía de planeación para el Administrador de recuperación de Hyper-V](http://go.microsoft.com/fwlink/?LinkId=321294): Proporciona información detallada acerca de los pasos de planeación que debe realizar antes de comenzar una implementación completa.
-   [Guía de implementación del Administrador de recuperación de Hyper-V](http://go.microsoft.com/fwlink/?LinkId=321295): Proporciona instrucciones paso a paso para realizar una implementación completa.
-   Si durante el transcurso de este tutorial se encuentra con problemas, revise [Hyper-V Recovery Manager: Common Error Scenarios and Resolutions](http://go.microsoft.com/fwlink/?LinkId=389879) o publique sus preguntas en el [foro de Servicios de recuperación de Azure](http://go.microsoft.com/fwlink/?LinkId=313628)

Antes de empezar
----------------

Antes de empezar con este tutorial, compruebe los requisitos previos.

### Requisitos previos del Administrador de recuperación de Hyper-V

-   \*\*Cuenta de Azure\*\*: Necesitará una cuenta de Azure que tenga habilitada la característica de Servicios de recuperación de Azure. Si no tiene una cuenta o si la característica no está registrada, consulte [Evaluación gratuita de Azure](http://aka.ms/try-azure) y [Detalles de precios del Administrador de recuperación de Hyper-V](http://go.microsoft.com/fwlink/?LinkId=378268).
-   \*\*Certificado (.cer)\*\*: Con el fin de registrar servidores VMM en un almacén del Administrador de recuperación de Hyper-V, necesitará cargar un certificado de administración (.cer) que contenga la clave pública en el almacén. Tenga en cuenta lo siguiente:
    -   Para el tutorial, puede utilizar un certificado autofirmado que se crea con la herramienta Makecert.exe. Si desea realizar una implementación completa, puede utilizar cualquier certificado SSL válido que cumpla con los requisitos previos de certificado descritos en [Requisitos previos y compatibilidad](%20http://go.microsoft.com/fwlink/?LinkId=386485) de la Guía de planeación.
    -   Cada almacén tiene asociado solo un certificado .cer en todo momento. Si es necesario, puede cargar un certificado nuevo que sobrescriba el existente.
-   \*\*Certificado (archivo .pfx)\*\*: Se debe exportar el certificado .cer como archivo .pfx (con la clave privada). Importe este archivo en cada servidor VMM que contenga las máquinas virtuales que desea proteger. Luego, durante la implementación, cuando instale el agente del proveedor del Administrador de recuperación de Hyper-V en cada servidor VMM, seleccione el archivo .pfx para registrar el servidor VMM con el almacén.

### Requisitos previos de VMM

-   Al menos un servidor VMM que se ejecute en System Center 2012 SP1 o en System Center 2012 R2.
-   Nubes privadas de VMM. Si ejecuta un servidor VMM, necesitará tener dos nubes configuradas. Si tiene dos o más servidores VMM, necesitará al menos una nube en el servidor VMM de origen que desea proteger y una nube en el servidor VMM de destino que utilizará para recuperación. La nube principal que desea proteger debe contener:
    -   Uno o más grupos de hosts de VMM
    -   Uno o más servidores host de Hyper-V en cada grupo de hosts
-   Si desea conectar máquinas virtuales a una red de VM después de la conmutación por error, configure la asignación de red en el Administrador de recuperación de Hyper-V. Antes de comenzar con la implementación, compruebe que:
    -   La máquina virtual en la nube del servidor VMM de origen esté conectada a una red de VM. Esa red de VM debe estar vinculada a una red lógica asociada con la nube.
    -   La nube de destino en el servidor VMM de destino tenga una red de VM correspondiente. Esa red de VM debe estar vinculada a una red lógica correspondiente asociada con la nube de destino.

Pasos del tutorial
------------------

Después de comprobar los requisitos previos, realice las siguientes tareas:

-   [Obtención y configuración de certificados](#createcert): Obtenga un certificado .cer, expórtelo como archivo .pfx e importe este archivo a los servidores VMM.
-   [Paso 1: Crear un almacén](#vault). Cree un almacén del Administrador de recuperación de Hyper-V en Azure.
-   [Paso 2: Cargar el certificado](#upload). Cargue el certificado de administración en el almacén.
-   [Paso 3: Descargar e instalar el proveedor](#download). Descargue e instale el agente del proveedor del Administrador de recuperación de Hyper-V en los servidores VMM que desea proteger. Luego registre los servidores VMM con el almacén.
-   [Paso 4: Configurar la protección de la nube](#clouds). Configure la protección para las nubes VMM.
-   [Paso 5: Configurar asignación de red](#networks). Asigne redes de VM desde los servidores VMM de origen a los servidores VMM de destino.
-   [Paso 6: Habilitar protección para las máquinas virtuales](#virtualmachines). Habilite la protección para las máquinas virtuales de Hyper-V ubicadas en las nubes VMM.
-   [Paso 7: Configurar y ejecutar planes de recuperación](#recovery%20plans). Cree y personalice planes de recuperación que agrupen máquinas virtuales para la conmutación por error. Luego ejecute el plan de recuperación.
-   [Paso 8: Supervisar](#jobs). Supervise la configuración, el estado y el progreso mediante el uso de las pestañas \*\*Jobs\*\* y \*\*Panel\*\*.

Obtención y configuración de certificados
-----------------------------------------

Obtenga y configure certificados de la siguiente manera:

1.  [Obtención de un certificado para el tutorial](#obtaincert): Obtenga un certificado con la herramienta MakeCert, o bien, use un certificado existente que cumpla con los [requisitos de certificado](http://go.microsoft.com/fwlink/?LinkId=321294).
2.  [Exportación del certificado en formato .pfx](#exportcert). En el servidor en que reside o se creó el certificado, exporte el archivo .cer como archivo .pfx (con la clave privada).
3.  [Importación del certificado .pfx en los servidores VMM](#importcert). Una vez que finaliza la exportación del archivo .pfx, impórtelo en el almacén de certificados personal en cada servidor VMM que desea registrar con el almacén.

### Obtención de un certificado autofirmado (.cer)

Utilice la herramienta MakeCert para crear un certificado x.509 (.cer) que cumpla con todos los requisitos de certificado, de la siguiente manera:

1.  En el equipo en el que desea ejecutar MakeCert, descargue la versión más reciente del [Windows SDK](http://go.microsoft.com/fwlink/?LinkId=378269). Observe que el comando makecert.exe es parte del Kit de desarrollo de software de Windows básico, por lo que no es necesario descargar e instalar el SDK completo.
2.  En la página para especificar ubicación, seleccione \*\*Instalar el Kit de desarrollo de software de Windows para Windows 8.1 en este equipo\*\*.
3.  En la página para unirse al Programa para la mejora de la experiencia del usuario (CEIP), seleccione la configuración de su preferencia.
4.  En la página del contrato de licencia, haga clic en \*\*Aceptar\*\* para aceptar los términos.
5.  En la página para seleccionar las características que desea instalar, elimine todas las opciones, excepto \*\*Kit de desarrollo de software de Windows\*\*.
6.  Una vez que se haya completado la instalación, compruebe que el archivo makecert.exe aparece en la carpeta C:\\ProgramFiles (x86)\\Windows Kits\\*VersiónWindows*\\bin\\x64.
7.  Abra una ventana del símbolo del sistema (cmd.exe) con privilegios de administrador y navegue a la carpeta makecert.exe.
8.  \>Ejecute el siguiente comando para crear su certificado autofirmado. Reemplace CertificateName por el nombre que desea utilizar para el certificado y especifique la fecha real de caducidad del certificado después de -e:

El certificado se creará y almacenará en la misma carpeta. Es posible que desee moverlo a una ubicación más accesible para así poder exportarlo durante el paso siguiente.

### Exportación del certificado en formato .pfx

Realice los pasos de este procedimiento para exportar el archivo .cer en formato .pfx.

1.  En la pantalla Inicio, escriba \*\*mmc.exe\*\* para iniciar Microsoft Management Console (MMC).
2.  En el menú \*\*Archivo\*\*, haga clic en \*\*Agregar o quitar complemento\*\*. Se abre el cuadro de diálogo \*\*Agregar o quitar complementos\*\*.
3.  En \*\*Complementos disponibles\*\*, haga clic en \*\*Certificados\*\* y, a continuación, en \*\*Agregar\*\*.
4.  Seleccione \*\*Cuenta de equipo\*\* y haga clic en \*\*Siguiente\*\*.
5.  Seleccione \*\*Equipo local\*\* y haga clic en \*\*Finalizar\*\*.
6.  En el árbol de la consola en MMC, expanda \*\*Certificados\*\* y, a continuación, expanda \*\*Personal\*\*.
7.  En el panel de detalles, haga clic en el certificado que desea administrar.
8.  En el menú \*\*Acción\*\*, señale \*\*Todas las tareas\*\* y, a continuación, haga clic en \*\*Exportar\*\*. Aparece el Asistente para exportar certificados. Haga clic en \*\*Siguiente\*\*.
9.  En la página \*\*Exportar clave privada\*\*, haga clic en \*\*Sí\*\* para exportar la clave privada. Haga clic en \*\*Siguiente\*\*. Observe que esta acción solo es necesaria si desea exportar la clave privada a otros servidores después de la instalación.
10. En la página de formato de archivo de exportación, seleccione \*\*Intercambio de información personal: PKCS \#12 (.PFX)\*\*. Haga clic en \*\*Siguiente\*\*.
11. En la página \*\*Contraseña\*\*, escriba y confirme la contraseña que se utiliza para cifrar la clave privada. Haga clic en \*\*Siguiente\*\*.
12. Siga las páginas del asistente para exportar el certificado en formato .pfx.

### Importación del certificado .pfx en los servidores VMM

Después de exportar el certificado, cópielo en cada servidor VMM que desee registrar en el almacén y, a continuación, impórtelo de la siguiente manera. Observe que si ejecutó MakeCert.exe en un servidor VMM no es necesario que importe el certificado en ese servidor.

1.  Copie los archivos del certificado de clave privada (.pfx) en una ubicación del servidor local.
2.  En el complemento Certificados de MMC, seleccione \*\*Cuenta de equipo\*\* y haga clic en Siguiente.
3.  Seleccione Equipo local y haga clic en \*\*Finalizar\*\*. En el cuadro de diálogo \*\*Agregar o quitar complementos\*\*, haga clic en \*\*Aceptar\*\*.
4.  En MMC, expanda \*\*Certificados\*\*, haga clic con el botón secundario en \*\*Personal\*\*, señale Todas las tareas y haga clic en \*\*Importar\*\* para iniciar el Asistente para importar certificados.
5.  En la página de bienvenida del Asistente para importar certificados, haga clic en \*\*Siguiente\*\*.
6.  En la página de archivo para importar, haga clic en \*\*Examinar\*\* y ubique la carpeta que contiene el archivo de certificado .pfx con el certificado que desea importar. Seleccione el archivo correspondiente y, a continuación, haga clic en \*\*Abrir\*\*.
7.  En la página de contraseña, en el cuadro \*\*Contraseña\*\*, escriba la contraseña del archivo de clave privada que especificó en el procedimiento anterior y, a continuación, haga clic en \*\*Siguiente\*\*.
8.  En la página del almacén de certificados, seleccione \*\*Colocar todos los certificados en el siguiente almacén\*\*, haga clic en \*\*Examinar\*\*, seleccione el almacén \*\*Personal\*\*, haga clic en \*\*Aceptar\*\* y luego en Siguiente.
9.  En la página de finalización del Asistente para importación de certificados, haga clic en \*\*Finalizar\*\*.

Una vez que complete estos pasos, podrá elegir el certificado .cer para cargarlo en el almacén y seleccionar el certificado .pfx cuando registre un servidor VMM durante la instalación del proveedor.

## Paso 1: Crear un almacén

1.  Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).

2.  Haga clic en **Servicios de datos** y, a continuación, en **Servicios de recuperación**.

    ![Vista previa del programa](./media/hyper-v-recovery-manager-configure-vault/RS_PreviewProgram.png)

3.  Haga clic en **Servicios de recuperación**, **Create New**, vaya a **Hyper-V Recovery Manager Vault** y, a continuación, haga clic en **Quick Create**.

    ![Almacén nuevo](./media/hyper-v-recovery-manager-configure-vault/RS_hvvault.png)

4.  En **Name**, escriba un nombre descriptivo para identificar el almacén.

5.  En **Región**, seleccione la región geográfica del almacén. Las regiones geográficas disponibles incluyen el sudeste de Asia Pacífico, Europa del Norte y Este de EE. UU.*\*\**

6.  En **Suscripción**, escriba los detalles de su suscripción.

7.  Haga clic en **Create vault**.

Compruebe el estado del almacén en las notificaciones que aparecen en la parte inferior del portal. Un mensaje confirmará que el almacén se creó correctamente. Aparecerá como \*\*En línea\*\* en la página de servicios de recuperación.

## Paso 2: Cargar el certificado (.cer)

1.  En la página **Servicios de recuperación**, abra el almacén requerido.
2.  Haga clic en el icono de inicio rápido para abrir la página de inicio rápido.

    ![Icono de inicio rápido](./media/hyper-v-recovery-manager-configure-vault/RS_QuickStartIcon.png)

3.  Haga clic en **Manage Certificate**.

    ![Inicio rápido](./media/hyper-v-recovery-manager-configure-vault/RS_QuickStart.png)

4.  En el cuadro de diálogo **Manage Certificate**, haga clic en **Browse For File** para ubicar el archivo .cer que desea cargar en el almacén.

    ![Administrar certificado](./media/hyper-v-recovery-manager-configure-vault/RS_ManageCert.png)

&lt;h2 data-morhtml="true"\>Paso 3: Descargar e instalar el proveedor&lt;/h2\> Instale el proveedor del Administrador de recuperación de Hyper-V en cada servidor VMM que desea registrar en el almacén. La versión más reciente del archivo de instalación del proveedor está almacenada en el Centro de descargas de Azure. Cuando ejecuta el archivo en un servidor VMM, se instala el proveedor y el servidor VMM se registra con el almacén.

1.  En la página **Quick Start**, haga clic en **Download Provider** para obtener el archivo .exe de instalación del proveedor. Ejecute este archivo en el servidor VMM para iniciar la instalación del proveedor.

    ![Descarga del agente](./media/hyper-v-recovery-manager-configure-vault/RS_installwiz.png)

2.  Siga los pasos para finalizar la instalación del proveedor.

    ![Instalación completa](./media/hyper-v-recovery-manager-configure-vault/RS_SetupComplete.png)

3.  Una vez que haya finalizado la instalación del proveedor, siga los pasos del asistente para registrar el servidor VMM con el almacén.
4.  En la página de conexión a Internet, especifique cómo se conecta a Internet el proveedor que se ejecuta en el servidor VMM. El proveedor puede utilizar la configuración predeterminada de la conexión a Internet, o bien, puede hacer clic en **Use a proxy server for Internet requests** para usar una configuración personalizada.

    ![Configuración de Internet](./media/hyper-v-recovery-manager-configure-vault/RS_ProviderProxy.png)

    Si desea utilizar una configuración personalizada durante este tutorial, lea la información que aparece en [Paso 2: Instalar el proveedor y registrar los servidores VMM](http://go.microsoft.com/fwlink/?LinkId=378266) de la Guía de implementación.

5.  En la página para registrar certificados, seleccione el archivo .pfx que corresponda al archivo .cer que cargó en el almacén.

    ![Registro de certificado](./media/hyper-v-recovery-manager-configure-vault/RS_CertReg1.png)

    ![Almacén de certificados](./media/hyper-v-recovery-manager-configure-vault/RS_CertReg2.png)

6.  En la página del servidor VMM, especifique un nombre descriptivo para el servidor VMM. Este nombre se utiliza para identificar el servidor en la consola del Administrador de recuperación de Hyper-V.
7.  Seleccione **Synchronize cloud data with the vault** para sincronizar los datos en todas las nubes privadas ubicadas en el servidor VMM con el almacén del Administrador de recuperación de Hyper-V. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede publicar individualmente cada nube para así sincronizarla antes de configurar la protección de la nube.
8.  Haga clic en **Register** para finalizar el proceso.

    ![Configuración de Internet](./media/hyper-v-recovery-manager-configure-vault/RS_PublishCloudSetup.png)

En esta etapa, el Administrador de recuperación de Hyper-V recupera metadatos del servidor VMM para así organizar la conmutación por error y la recuperación. Una vez que un servidor se registre correctamente, su nombre descriptivo aparecerá en la pestaña \*Resources\* de la página de servidores en el almacén.

Paso 4: Configurar la protección de la nube
-------------------------------------------

Una vez que los servidores VMM están registrados, puede configurar la protección de la nube. Si no habilitó la opción **Synchronize cloud data with the vault** cuando instaló el proveedor en el servidor VMM, deberá publicar la nube en el Administrador de recuperación de Hyper-V desde la consola VMM. Puede configurar la protección una vez que el Administrador de recuperación de Hyper-V detecta las nubes.

### Publicación de una nube

1.  En la consola VMM, abra el área de trabajo **VMs and Services**.
2.  En el panel **VMs and Services**, abra la nube que desea publicar.
3.  Para publicar la nube, en la página **General** de las propiedades de la nube seleccione **Send configuration data about this cloud to the Azure Hyper-V Recovery Manager**. Cuando la nube se publica, aparece en el almacén.

    ![Nubes](./media/hyper-v-recovery-manager-configure-vault/RS_PublishCloud.png)

    ![Nube publicada](./media/hyper-v-recovery-manager-configure-vault/RS_Clouds.png)

### Configuración de nubes

Para configurar la protección de las nubes, realice las siguientes tareas:

1.  En la página de inicio rápido, haga clic en **Configure Protection Settings**.
2.  En la pestaña **Protected Items**, seleccione la nube que desea configurar y vaya a la pestaña **Configuration**.

    ![Configuración de la nube](./media/hyper-v-recovery-manager-configure-vault/RS_CloudConfig.png)

3.  En **Target Location**, especifique el servidor VMM que administra la nube que desea utilizar para recuperación.
4.  En **Target Cloud**, especifique la nube de destino que desea utilizar para la conmutación por error de máquinas virtuales en la nube de origen.
5.  En **Copy Frequency**, deje la configuración predeterminada. Este valor especifica la frecuencia con que se deben sincronizar los datos entre las ubicaciones de origen y de destino. Solo es pertinente cuando el host de Hyper-V ejecuta Windows Server 2012 R2. En el caso de otros servidores, se utiliza una configuración predeterminada de cinco minutos.
6.  En **Additional Recovery Points**, deje la configuración predeterminada. Este valor especifica si desea crear puntos de recuperación adicionales. Con un valor predeterminado de cero, el punto de recuperación más reciente para una máquina virtual es el único que se almacena en un servidor host de réplica.
7.  En **Application-Consistent Snapshot Frequency**, deje la configuración predeterminada. Este valor especifica la frecuencia de creación de instantáneas. Las instantáneas utilizan el Servicio de instantáneas de volumen (VSS) para asegurarse de que las aplicaciones se encuentren en un estado coherente cuando se captura la instantánea. Si de todos modos desea definir este valor para el tutorial, asegúrese de definirlo en un número menor que la cantidad de puntos de recuperación adicionales que configure.
8.  En **Data Transfer Compression**, especifique si se deben comprimir los datos replicados que se transfieren.
9.  En **Authentication**, especifique cómo se autentica el tráfico entre el servidor host de Hyper-V principal y el servidor host de Hyper-V de recuperación. A menos que tenga configurado un entorno Kerberos en funcionamiento, recomendamos que utilice HTTPS para este tutorial. Si selecciona HTTPS, los servidores host se autenticarán entre sí con un certificado de servidor y el tráfico se cifrará. El Administrador de recuperación de Hyper-V configura certificados que se utilizarán para la autenticación HTTPS de manera automática. No se requiere configuración manual. Observe que esta configuración solo es pertinente para servidores host de Hyper-V que se ejecutan en Windows Server 2012 R2.
10. En **Port**, deje la configuración predeterminada. Este valor define el número de puerto en el que los equipos host de Hyper-V de origen y de destino escuchan el tráfico de replicación.
11. En **Initial Replication Settings**, especifique cómo se manejará la replicación inicial de los datos desde la ubicación de origen a la ubicación de destino, antes de que comience la replicación normal.
    -   **Over the network**. Copiar los datos sobre la red puede ser un proceso lento y que consume muchos recursos. Le recomendamos utilizar esta opción si la nube contiene máquinas virtuales con discos duros virtuales relativamente pequeños y si el servidor VMM principal está conectado al servidor VMM secundario a través de una conexión con un amplio ancho de banda. Puede especificar que la copia se inicie de inmediato, o bien, seleccionar una hora. Si utiliza la replicación de red, le recomendamos programarla evitando las horas de mayor consumo.
    -   **Offline**. Este método especifica que la replicación inicial se realizará con medios externos. Esta opción resulta útil si desea evitar la degradación del rendimiento de la red o en el caso de ubicaciones geográficamente remotas. Para utilizar este método, especifique la ubicación de exportación en la nube de origen y la ubicación de importación en la nube de destino. Cuando habilita la protección de una máquina virtual, el disco duro virtual se copia en la ubicación de exportación especificada. Usted lo envía al sitio de destino y lo copia en la ubicación de importación. El sistema copia la información importada en las máquinas virtuales de réplica. Si desea obtener una lista completa de los requisitos previos para la replicación sin conexión, consulte [Paso 3: Configurar la protección para nubes VMM](http://go.microsoft.com/fwlink/?LinkId=323469) en la Guía de implementación.

#### Ajustes después de configurar la protección

Después de configurar una nube, se configuran para la replicación todos los clústeres y los servidores host configurados en las nubes de origen y de destino. En particular, se configuran los siguientes aspectos:

-   Reglas de firewall que utiliza la réplica de Hyper-V para abrir los puertos para el tráfico de replicación.
-   Se instalan los certificados requeridos para la replicación.
-   Se configuran los ajustes de la réplica de Hyper-V.

Puede modificar la configuración de la nube en la pestaña **Configure**. Observe lo siguiente:

-   Recomendamos seleccionar una nube de destino que cumpla con los requisitos de recuperación para las máquinas virtuales que protegerá.
-   Una nube solo puede pertenecer a un único par de nubes, ya sea como nube principal o de destino.
-   Después de guardar la configuración de la nube, se creará un trabajo, que puede supervisar en la pestaña **Jobs**. Después de guardar la configuración, para modificar la ubicación de destino o la nube de destino debe eliminar la configuración de la nube y volver a configurarla después.

Paso 5: Configurar asignación de red
------------------------------------

En la pestaña **Networks**, configure la asignación entre redes de VM en los servidores VMM de origen y de destino. La asignación de red asegura que, después de la conmutación por error, las máquinas virtuales de réplica se conectan a redes adecuadas y que las máquinas virtuales de réplica se colocan en hosts que pueden tener acceso a las redes de VM. Compruebe los requisitos de red de VMM en [Requisitos previos y compatibilidad](%20http://go.microsoft.com/fwlink/?LinkId=386485) de la Guía de planeación. Recomendamos que asigne redes de la siguiente manera:

-   Asigne redes usadas por cada nube configurada para protección.
-   Realice la asignación de red antes de habilitar las máquinas virtuales para protección, debido a que la asignación se utiliza durante la colocación de máquinas virtuales de réplica. La asignación funcionará correctamente si la configura después de habilitar la protección, pero es posible que necesite migrar algunas máquinas virtuales de réplica.

Para asignar redes, realice las siguientes tareas:

1.  En la página de inicio rápido, haga clic en **Configure Network Protection**.
2.  En **Source Location**, seleccione el servidor VMM de origen.
3.  En **Target Location**, seleccione el servidor VMM de destino. Si está implementando el Administrador de recuperación de Hyper-V con un solo servidor VMM, el origen y el destino coincidirán. Aparece la lista de las redes de VM de origen y sus redes de VM de destino asociadas. En el caso de las redes no asignadas, aparece un valor en blanco.
4.  Seleccione una entrada sin asignar en la lista de origen y de destino y haga clic en **Map**. El servicio detecta las redes de VM en el servidor de destino y las muestra.

    ![Administrar certificado](./media/hyper-v-recovery-manager-configure-vault/RS_networks.png)

5.  En la página para seleccionar una red de destino, seleccione la red de VM de destino que desea utilizar en el servidor VMM de destino. ![Red de destino](./media/hyper-v-recovery-manager-configure-vault/RS_TargetNetwork.png)

6.  Haga clic en los iconos de información junto a los nombres de las redes de origen y de destino para ver las subredes y el tipo de cada red.

    Cuando selecciona una red de destino, se muestran las nubes protegidas que utilizan la red de origen y también aparece la disponibilidad de las redes de VM de destino asociadas con las nubes. Recomendamos seleccionar una red de destino que esté disponible para todas las nubes que se utilizan para protección.

7.  Haga clic en la marca de verificación para completar el proceso de asignación. Un trabajo comienza a realizar un seguimiento del progreso de la asignación. Puede ver este seguimiento en la pestaña **Jobs**.

    Esto conectará cualquier máquina virtual de réplica existente correspondiente a las máquinas virtuales conectadas a la red VM de origen con la red VM de destino. También conectará nuevas máquinas virtuales de réplica que se creen después de habilitar la replicación en las máquinas virtuales conectadas a la red VM de origen con la red VM de destino.

### Modificación de asignaciones de red

Puede modificar o quitar asignaciones de red en la pestaña **Networks**. Tenga en cuenta lo siguiente:

-   Si anula una asignación seleccionada, se elimina la asignación y las máquinas virtuales de réplica se desconectan de la red a la que se conectaron en el momento de la asignación.
-   Cuando modifique una asignación seleccionada, se actualizarán los cambios y las máquinas virtuales de réplica se conectarán a la nueva configuración de red proporcionada.

Paso 6: Habilitar protección para las máquinas virtuales
--------------------------------------------------------

Una vez que los servidores, las nubes y las redes se configuran correctamente, puede habilitar la protección para las máquinas virtuales en la nube. Para habilitar la protección, en la consola VMM haga clic con el botón secundario en cada máquina virtual que desee proteger y seleccione \*\*Habilitar recuperación\*\*.

Una vez que se haya habilitado la protección, podrá ver la máquina virtual en la lista de máquinas virtuales de la nube. Puede ver el progreso de la acción de habilitación de la protección en la pestaña \*\*Jobs\*\*.

![Máquinas virtuales](./media/hyper-v-recovery-manager-configure-vault/RS_Clouds.png)

Cuando habilita la protección de máquinas virtuales se crean tres trabajos. Se ejecuta el trabajo Enable Protection. Luego, después de que finaliza la replicación inicial, se ejecutan otros dos trabajos Finalize Protection. La máquina virtual estará preparada para la conmutación por error solo después de que estos tres trabajos finalicen correctamente.

Paso 7: Configurar y ejecutar planes de recuperación
----------------------------------------------------

Un plan de recuperación recopila y agrupa máquinas virtuales de tal manera que se pueden procesar como una sola unidad; además, especifica el orden en que los grupos realizarán la conmutación por error. Además de definir grupos de máquinas virtuales, puede personalizar los planes de recuperación para ejecutar scripts automáticos o para esperar la confirmación de acciones manuales. Para crear un plan de recuperación, realice las siguientes tareas:

1.  En la página de inicio rápido, haga clic en **Create Recovery Plan**.
2.  En la página para especificar el nombre de la página de recuperación y el destino, escriba un nombre y los servidores VMM de origen y de destino. Si está implementando el Administrador de recuperación de Hyper-V en un solo servidor VMM, el servidor de origen y de destino serán el mismo. El servidor VMM de origen debe contener máquinas virtuales con protección habilitada.

    ![Creación del plan de recuperación](./media/hyper-v-recovery-manager-configure-vault/RS_RecoveryPlan1.png)

3.  En la página de selección de máquinas virtuales, seleccione máquinas virtuales para agregar al plan de recuperación. Estas se agregan al grupo predeterminado de plan de recuperación (Grupo 1).
4.  Haga clic en la marca de verificación para crear el plan de recuperación. En la pestaña **Recovery Plans** puede eliminar los planes de recuperación que crea.

    ![Máquinas virtuales del plan de recuperación](./media/hyper-v-recovery-manager-configure-vault/RS_RecoveryPlan2.png)

Después de crear un plan de recuperación, puede realizar las siguientes acciones:

-   Personalice el plan. Puede agregar grupos nuevos a un plan de recuperación o agregar máquinas virtuales a un grupo. Puede definir acciones personalizadas en forma de scripts y acciones manuales que finalizan antes y después de los grupos especificados. Los nombres de los grupos van en incremento. Después del plan de recuperación predeterminado Grupo 1, se creará el Grupo 2, luego el Grupo 3, etc. Las máquinas virtuales realizan la conmutación por error según el orden del grupo. Para obtener más información acerca de la personalización de los planes de recuperación, consulte [Paso 6: Crear y personalizar los planes de recuperación](http://go.microsoft.com/fwlink/?LinkId=378271) en la Guía de implementación.
-   Ejecute una conmutación por error de prueba.

### Ejecución de planes de recuperación

Los planes de recuperación se pueden ejecutar como parte de una conmutación por error de prueba proactiva o de una conmutación por error no planificada. Este tutorial describe cómo ejecutar una conmutación por error de prueba. Para obtener más información acerca de los demás tipos de conmutación por error, lea la [Guía de operaciones y supervisión](hhttp://go.microsoft.com/fwlink/?LinkId=378272).

### Ejecución del plan

Ejecute una conmutación por error de prueba para comprobar el plan de recuperación en un entorno de prueba. Una conmutación por error de prueba es útil para comprobar que el plan de recuperación y la estrategia de conmutación por error de la máquina virtual funcionan según lo esperado. La conmutación por error de prueba simula su mecanismo de conmutación por error y recuperación en una red aislada.

##### Antes de comenzar

Cuando se desencadena una conmutación por error de prueba, se le solicita especificar cómo se deben conectar las máquinas virtuales de prueba a las redes después de la conmutación por error.

-   Si desea utilizar una red existente, le recomendamos crear una red lógica independiente que no se utilice en producción.
-   Si selecciona la opción de crear automáticamente una red de VM de prueba, las redes temporales y las máquinas virtuales de prueba se limpian automáticamente una vez que se completa la conmutación por error de prueba.
-   Si utiliza una red lógica basada en LAN virtual (VLAN), asegúrese de que los sitios de red que agregue a la red lógica estén aislados.
-   Si utiliza una red lógica basada en la virtualización de red de Windows, el Administrador de recuperación de Hyper-V creará automáticamente redes de VM aisladas.

##### Ejecución de una conmutación por error de prueba

Ejecute una conmutación por error de prueba para un plan de recuperación de la siguiente manera:

En la pestaña \*\*Recovery Plans\*\*, seleccione el plan de recuperación necesario.

Para iniciar la conmutación por error, haga clic en el botón \*\*Test Failover\*\*.

En la página de confirmación de la conmutación por error de prueba, especifique cómo se deben conectar las máquinas virtuales a las redes después de la conmutación por error de prueba, de la siguiente manera:

-   \*\*None\*\*. Seleccione esta configuración para especificar que no se deben utilizar redes de VM en la conmutación por error de prueba. Utilice esta opción si desea probar máquinas virtuales individuales en lugar de su configuración de red. También ofrece un vistazo rápido de la funcionalidad de la conmutación por error de prueba. Las máquinas virtuales de prueba no se conectarán a redes después de una conmutación por error.
-   \*\*Use existing\*\*. Utilice esta opción si ya creó y aisló una red de VM para utilizarla en la conmutación por error de prueba. Después de la conmutación por error, todas las máquinas virtuales de prueba utilizadas en la conmutación por error de prueba se conectarán a la red especificada en \*\*VM Network\*\*.
-   \*\*Create automatically\*\*. Seleccione esta configuración para especificar que el Administrador de recuperación de Hyper-V debe crear automáticamente una red de VM basándose en la configuración que especifique en Logical Network y sus sitios de red relacionados. Utilice esta opción si el plan de recuperación usa más de una red de VM. En el caso de las redes de virtualización de red de Windows, puede utilizar esta opción para crear automáticamente redes de VM con la misma configuración (subredes y grupos de direcciones IP) de las que se encuentran en la red de la máquina virtual de réplica. Estas redes de VM se limpian automáticamente una vez que se completa la conmutación por error de prueba.

Una vez que se complete la conmutación por error de prueba, realice las siguientes tareas:

1.  Compruebe que las máquinas virtuales se inician correctamente.
2.  Una vez que compruebe que las máquinas virtuales se inician correctamente, complete la conmutación por error de prueba para limpiar el entorno aislado. Si seleccionó la creación automática de redes de VM, la limpieza elimina todas las máquinas virtuales de prueba y las redes de prueba.
3.  Haga clic en \*\*Notes\*\* para registrar y guardar cualquier observación asociada con la conmutación por error de prueba.
4.  Cuando se inicia una conmutación por error de prueba para un plan de recuperación, el proceso de conmutación por error aparece en la página de detalles del plan de recuperación. Puede ver los pasos de conmutación por error de prueba individuales y sus estados, y también puede ver o crear notas asociadas con la conmutación por error de prueba. Además, si desea ver los detalles de los trabajos de conmutación por error, consulte el trabajo de conmutación por error en la pestaña \*\*Jobs\*\*. Los detalles muestran cada trabajo asociado con la conmutación por error, su estado, cuándo comenzó y su duración. Puede exportar un trabajo de la lista de conmutación por error a una hoja de cálculo de Excel.

Tenga en cuenta lo siguiente:

-   En la página de confirmación de la conmutación por error de prueba, se muestran los detalles del servidor VMM en el que se crearán las máquinas virtuales de prueba.
-   Siga el progreso de los trabajos de conmutación por error de prueba en la pestaña **Jobs**.

Paso 8: Supervisar
------------------

### Uso de la pestaña Jobs y Panel

#### Trabajos

La pestaña **Jobs** muestra las tareas principales que ha realizado el almacén del Administrador de recuperación de Hyper-V, incluida la configuración de la protección de una nube, la habilitación y deshabilitación de la protección de una máquina virtual, la ejecución de una conmutación por error (planeada, no planeada o de prueba), la confirmación de una conmutación por error no planificada y la configuración de replicación inversa.

En la pestaña **Jobs**, puede realizar las siguientes tareas:

-   **Run a job query**. Puede ejecutar una consulta para recuperar trabajos que coincidan con los criterios especificados. Puede filtrar por trabajos con los siguientes parámetros:

-   Encontrar trabajos que se ejecutaron en un servidor VMM específico.
-   Encontrar trabajos que se realizaron en una nube, máquina virtual, red, plan de recuperación en una nube o en todas estas instancias.
-   Identificar trabajos que ocurrieron durante un intervalo de fecha y hora específico.

Observe que una consulta puede devolver un máximo de 200 trabajos, por lo que le recomendamos restringir los parámetros de la consulta para devolver menos resultados que el máximo.

-   **Get job details**. Puede hacer clic en un trabajo en la lista **Jobs** para obtener más detalles. Los detalles incluyen el nombre del trabajo, su estado, cuándo comenzó y cuánto duró. En la pestaña Job details, puede exportar los detalles del trabajo a una hoja de cálculo de Excel o intentar reiniciar un trabajo con errores, omitido o cancelado.
-   **Export Jobs**. Puede exportar los resultados de una consulta de trabajo a una hoja de cálculo de Excel.
-   **Restart**. Puede reiniciar trabajos con errores para intentar volver a ejecutarlos.
-   **Error Details**. En el caso de trabajos con errores, puede hacer clic en **Error Details** para obtener una lista de los errores del trabajo. Para cada error se muestran posibles causas y recomendaciones. La descripción del error se puede copiar al portapapeles para solucionar problemas.

> La pestaña **Jobs** proporciona la siguiente información:

-   \*\*Name\*\*. El nombre del trabajo
-   \*\*Item\*\*. El nombre de la nube, el plan de recuperación, el servidor VMM o la máquina virtual donde se ejecutó el trabajo.
-   \*\*Status\*\*. El estado del trabajo: completado, cancelado, con errores u otro
-   \*\*Type\*\*. El tipo de trabajo
-   \*\*Started At\*\*. El inicio del trabajo
-   \*\*Task Time\*\*. La duración del trabajo

![Pestaña Trabajos](./media/hyper-v-recovery-manager-configure-vault/RS_Jobs.png)

#### Ejecución de una consulta de trabajo

Puede ejecutar consultas para recuperar trabajos que coincidan con criterios especificados, incluidos:

-   Trabajos que se ejecutaron en un servidor VMM específico.
-   Trabajos que se realizaron en una nube, máquina virtual, red, plan de recuperación específico o en todas estas instancias.
-   Trabajos que ocurrieron durante un intervalo de tiempo específico.

Para ejecutar una consulta de trabajo, realice las siguientes tareas:

1.  Abra la pestaña \*\*Jobs\*\*
2.  En \*\*Server\*\*, especifique el servidor VMM en que se ejecutó el trabajo
3.  En \*\*Type\*\*, especifique si el trabajo se realizó en una nube, una máquina virtual, una red o un plan de recuperación en una nube.
4.  En \*\*Status\*\*, especifique el estado del trabajo.
5.  En \*\*Duration\*\*, especifique el intervalo de tiempo en el que ocurrió el trabajo.

#### Panel

La pestaña \*\*Panel\*\* proporciona una introducción rápida al uso del Administrador de recuperación de Hyper-V en su organización. Proporciona una puerta de enlace centralizada para ver las máquinas virtuales protegidas por los almacenes del Administrador de recuperación de Hyper-V. El Panel proporciona la siguiente funcionalidad:

En la pestaña **Panel**, puede realizar las siguientes tareas:

-   **Download Provider**. Descargue el proveedor del Administrador de recuperación de Hyper-V para instalarlo en un servidor VMM.
-   **Manage Certificate**. Modifique la configuración del certificado asociado con el almacén.
-   **Delete**. Elimine un almacén.
-   **Resynchronize virtual machines**. Si el Administrador de recuperación de Hyper-V detecta que alguna máquina virtual, ya sea principal o de recuperación, no está sincronizada según lo esperado, puede ver una lista de las máquinas virtuales pertinentes y seleccionar cualquiera que desee intentar sincronizar de nuevo desde el panel.

La pestaña **Panel** proporciona la siguiente información:

-   \*\*Quick Glance\*\*. Muestra información de configuración esencial para los servicios de recuperación y el almacén del Administrador de recuperación de Hyper-V. Indica si el almacén está en línea, el certificado al que está asignado, la caducidad del certificado y los detalles de suscripción del servicio.
-   \*\*Recent Jobs\*\*. Muestra trabajos que se han realizado correctamente o con errores en las últimas 24 horas o los trabajos que se encuentran en curso o en espera de una acción.
-   \*\*Issues\*\*. El panel muestra información acerca de problemas con las conexiones de servidor VMM y problemas con la configuración de la nube o la sincronización de la replicación de máquinas virtuales. Puede obtener más detalles de un problema, ver los trabajos asociados con el problema o intentar sincronizar de nuevo las máquinas virtuales.

![Panel](./media/hyper-v-recovery-manager-configure-vault/RS_Dashboard.png)

Pasos siguientes
----------------

-   Para planear e implementar el Administrador de recuperación de Hyper-V en un entorno de producción completo, consulte la [Guía de planeación para el Administrador de recuperación de Hyper-V](http://go.microsoft.com/fwlink/?LinkId=321294) y la [Guía de implementación para el Administrador de recuperación de Hyper-V](http://go.microsoft.com/fwlink/?LinkId=321295).
-   Si tiene preguntas, visite el [foro de Servicios de recuperación de Azure](http://go.microsoft.com/fwlink/?LinkId=313628).

