<properties 
	pageTitle="Configuración de la protección entre sitios VMM locales" 
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales de Hyper-V entre sitios VMM locales.." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/19/2015" 
	ms.author="raynew"/>

# Configuración de la protección entre sitios VMM locales


## Información general


Azure Site Recovery contribuye a la estrategia de continuidad de negocio y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales en varios escenarios de implementación. Para obtener una lista completa de los escenarios de implementación, consulte [Información general de Azure Site Recovery](hyper-v-recovery-manager-overview.md).

Esta guía del escenario describe cómo implementar la recuperación del sitio para coordinar y automatizar la protección de las cargas de trabajo que se ejecutan en máquinas virtuales en los servidores de host de Hyper-V que se encuentran en las nubes privadas VMM. En este escenario, las máquinas virtuales se replican de un sitio VMM principal a un sitio VMM secundario mediante Réplica de Hyper-V.

Esta guía incluye los requisitos previos del escenario y muestra cómo configurar un almacén de recuperación del sitio, obtener el proveedor de Azure Site Recovery instalado en los servidores VMM de origen y destino, registrar los servidores en el almacén, configurar los valores de la protección para las nubes VMM que se aplicarán a todas las máquinas virtuales protegidas y, a continuación, habilitar la protección de dichas máquinas virtuales. Finalice mediante la prueba de la conmutación por error para asegurarse de que todo funciona según lo esperado.

Si tiene problemas al configurar este escenario, publique sus preguntas en el [foro de Servicios de recuperación de Azure](http://go.microsoft.com/fwlink/?LinkId=313628).


## Antes de comenzar
Asegúrese de que cumple con estos requisitos previos:
### Requisitos previos de Azure

- Necesitará una cuenta de [Microsoft Azure](http://azure.microsoft.com/). Si no tiene una, comience con una [evaluación gratuita](http://aka.ms/try-azure). Además puede consultar los [precios de Azure Site Recovery Manager](http://go.microsoft.com/fwlink/?LinkId=378268).
- Para entender cómo se utilizan los datos y la información, consulte la [declaración de privacidad de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899) así como la <a href="#privacy">información de privacidad de Site Recovery</a> adicional, que se encuentra al final de este tema.

### Requisitos previos de VMM
- Necesitará al menos un servidor VMM.
- El servidor VMM debe ejecutar al menos System Center 2012 SP1 con las últimas actualizaciones acumulativas. 
- Los servidores VMM que contienen máquinas virtuales que desea proteger debe ejecutar el proveedor de Azure Site Recovery. Se instala durante la implementación de Azure Site Recovery.
- Si desea configurar la protección con un único servidor VMM, necesitará al menos dos nubes configuradas en el servidor.
- Si desea implementar la protección con dos servidores VMM, cada servidor debe tener al menos una nube configurada en el servidor VMM principal que desea proteger y otra nube configurada en el servidor VMM secundario que desee utilizar para la protección y recuperación
- Todas las nubes VMM deben tener establecido el perfil de capacidad de Hyper-V.
- La nube de origen que desea proteger debe contener:
	- Uno o más grupos de hosts de VMM
	- Uno o más servidores host de Hyper-V en cada grupo de hosts
	- Una o más máquinas virtuales en el servidor host 
- Más información acerca de cómo configurar las nubes de VMM:
	- Obtenga más información acerca de las nubes privadas de VMM en [Novedades de la nube privada con System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) y en [VMM 2012 y las nubes](http://go.microsoft.com/fwlink/?LinkId=324956). 
	- Obtenga información acerca de cómo [configurar el tejido de la nube de VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric).
	- Cuando los elementos del tejido de nube están configurados, obtenga información acerca de cómo crear nubes privadas en [Creación de una nube privada en VMM](http://go.microsoft.com/fwlink/?LinkId=324953) y [Tutorial: Creación de nubes privadas con VMM de System Center 2012 SP1](http://go.microsoft.com/fwlink/?LinkId=324954).

### Requisitos previos de Hyper-V

- El host y los servidores de Hyper-V de destino deben estar ejecutando al menos Windows Server 2012 con el rol Hyper-V y tener instaladas las actualizaciones más recientes.
- Si está ejecutando Hyper-V en un clúster, tenga en cuenta que ese agente de clúster no se crea automáticamente si tiene un clúster basado en una dirección IP estática. Tendrá que configurar manualmente el agente de clúster. Para obtener instrucciones, consulte [Configuración del agente de réplicas de Hyper-V](http://go.microsoft.com/fwlink/?LinkId=403937).
- Cualquier servidor o clúster del host de Hyper-V para el que desee administrar la protección debe incluirse en una nube de VMM.
 
### Requisitos previos de asignación de redes
La asignación de redes asegura que las máquinas virtuales de réplica se colocan de manera óptima en los servidores host de Hyper-V tras la conmutación por error y que se pueden conectar a las redes de máquina virtual adecuadas. Si no configura la asignación de redes, las máquinas virtuales de réplica no se conectarán a las redes de máquina virtual después de la conmutación por error. Si desea implementar la asignación de redes, necesitará lo siguiente:

- Las máquinas virtuales que desea proteger en el servidor VMM de origen deben estar conectadas a una red de máquina virtual. Esa red debe estar vinculada a una red lógica asociada con la nube.
- La nube de destino en el servidor VMM secundario que se utiliza para la recuperación debe tener configurada una red de máquina virtual correspondiente y, a su vez, debe estar vinculada a una red lógica correspondiente que esté asociada a la nube de destino.

Obtenga más información acerca de la asignación de redes:

- [Configuración de redes lógicas en VMM](http://go.microsoft.com/fwlink/?LinkId=386307)
- [Configuración de redes de máquina virtual y puertas de enlace en VMM](http://go.microsoft.com/fwlink/?LinkId=386308)

### Requisitos previos de asignación de almacenamiento
De forma predeterminada cuando se replica una máquina virtual en un servidor host de Hyper-V de origen a un servidor host de Hyper-V de destino, los datos replicados se almacenan en la ubicación predeterminada indicada para el host de Hyper-V de destino en el Administrador de Hyper-V. Para obtener más control sobre dónde se almacenan los datos replicados, puede configurar la asignación de almacenamiento. Para ello, deberá configurar las clasificaciones de almacenamiento en los servidores VMM de origen y destino antes de comenzar la implementación.
Para obtener instrucciones, consulte [Creación de clasificaciones de almacenamiento en VMM](http://go.microsoft.com/fwlink/?LinkId=400937).


## Paso 1: Creación de un almacén de recuperación del sitio

1. Inicie sesión en el [Portal de administración](https://portal.azure.com) desde el servidor VMM que desee registrar.

2. Expanda **Servicios de datos** > **Servicios de recuperación** y haga clic en **Almacén de recuperación del sitio**.


3. Haga clic en **Crear nuevo** > **Creación rápida**.
	
4. En **Name**, escriba un nombre descriptivo para identificar el almacén.

5. En **Región**, seleccione la región geográfica del almacén. Para comprobar las regiones admitidas, consulte Disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](http://go.microsoft.com/fwlink/?LinkId=389880).</a>

6. Haga clic en **Crear almacén**.

	![Create vault](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CreateVault.png)

En la barra de estado, compruebe que se ha creado el almacén. El almacén aparecerá como **Activo** en la página principal de Servicios de recuperación.

## Paso 2: Generación de una clave de registro de almacén

Generación de una clave de registro en el almacén. Después de descargar el proveedor de Azure Site Recovery y de instalarlo en el servidor VMM, usará esta clave para registrar el servidor VMM en el almacén.

1. En la página <b>Servicios de recuperación</b>, haga clic en el almacén para abrir la página de inicio rápido. El inicio rápido también se puede abrir en cualquier momento mediante el icono.

	![Quick Start Icon](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_QuickStartIcon.png)

2. En la lista desplegable seleccione **Entre dos sitios Hyper-V locales**.
3. En **Preparar servidores VMM**, haga clic en **Generar archivo de clave de registro**. El archivo de clave se genera automáticamente y es válido durante 5 días después de su generación. Si no tiene acceso al Portal de Azure desde el servidor VMM, tendrá que copiar este archivo en el servidor. 

	![Registration key](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_E2ERegisterKey.png)
	
## Paso 3: Instalación del proveedor de Azure Site Recovery	

1. En la página de <b>Inicio rápido</b>, en **Preparar servidores VMM**, haga clic en <b>Descargar el proveedor de Microsoft Azure Site Recovery para la instalación en servidores VMM</b> a fin de obtener la versión más reciente del archivo de instalación del proveedor.

2. Ejecute el archivo en servidores VMM de origen y de destino. Si VMM está implementado en un clúster y va a instalar el proveedor por primera vez, instálelo en un nodo activo y finalice la instalación para registrar el servidor VMM en el almacén. A continuación, instale el proveedor en los demás nodos. Tenga en cuenta que si está actualizando el proveedor, tendrá que actualizarlo en todos los nodos, ya que deben ejecutar la misma versión del proveedor.

3. En **Comprobación de requisitos previos** seleccione detener el servicio VMM para iniciar la configuración del proveedor. Este servicio se detendrá y se volverá a iniciar automáticamente cuando finalice la configuración. Si va a instalarlo en un clúster VMM, se le pedirá que detenga el rol de clúster.

	![Prerequisites](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_ProviderPrereq.png)

4. En **Microsoft Update** puede optar por recibir actualizaciones. Con esta configuración habilitada, las actualizaciones del proveedor se instalarán según su directiva de Microsoft Update.

	![Microsoft Updates](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_ProviderUpdate.png)

Una vez instalado el proveedor, siga con la instalación para registrar el servidor en el almacén.

5. En la página de conexión a Internet, especifique cómo se conecta a Internet el proveedor que se ejecuta en el servidor VMM. Seleccione <b>Utilizar la configuración proxy del sistema predeterminado</b> para utilizar los parámetros de conexión a Internet predeterminados configurados en el servidor.

	![Internet Settings](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_ProviderProxy.png)

	- Si desea utilizar un proxy personalizado, debe configurarlo antes de instalar el proveedor. Al configurar las opciones del proxy personalizado, se ejecuta una prueba para comprobar la conexión del proxy.
	- Si utiliza a un proxy personalizado o el proxy predeterminado requiere autenticación, tendrá que especificar los detalles del proxy, incluida la dirección y el puerto del proxy.
	- Debe excluir las direcciones siguientes del enrutamiento a través del proxy:
		- La dirección URL para conectarse a Azure Site Recovery: *.hypervrecoverymanager.windowsazure.com
		- *.accesscontrol.windows.net
		- *.backup.windowsazure.com
		- *.blob.core.windows.net 
		- *.store.core.windows.net 
	- Si debe permitir conexiones salientes en un controlador de dominio de Azure, permita las direcciones IP que se describen en [Intervalos de direcciones IP del centro de datos de Azure](http://go.microsoft.com/fwlink/?LinkId=511094) y permita los protocolos HTTPS (443) y HTTP (80). 
	- Si utiliza un proxy personalizado, se creará una cuenta de ejecución de VMM (DRAProxyAccount) mediante el uso automático de las credenciales de proxy especificado. Configure el servidor proxy para que esta cuenta pueda autenticarse correctamente. La configuración de la cuenta de ejecución de VMM puede modificarse en la consola VMM. Para ello, abra el área de trabajo Configuración, expanda Seguridad, haga clic en Cuentas de ejecución y, a continuación, modifique la contraseña de DRAProxyAccount. Deberá reiniciar el servicio VMM para que esta configuración surta efecto.
6. En **Clave de registro**, seleccione la que ha descargado de Azure Site Recovery y copiado en el servidor VMM.
7. En **Nombre del almacén**, compruebe el nombre del almacén en el que se registrará el servidor.
8. En **Nombre del servidor**, especifique un nombre descriptivo para identificar el servidor VMM en el almacén. En una configuración de clúster, especifique el nombre del rol de clúster VMM. 

	![Server registration](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_ProviderRegKeyServerName.png)


9. En la sincronización de **Metadatos de la nube inicial** seleccione si desea sincronizar los metadatos de todas las nubes en el servidor de VMM con el almacén. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar este parámetro sin marcar y sincronizar cada nube individualmente en las propiedades de la nube de la consola de VMM.


7. La opción **Cifrado de datos** no es relevante para la protección local a local.

	![Server registration](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_ProviderSyncEncrypt.png)

8. Haga clic en <b>Register</b> para finalizar el proceso. Azure Site Recovery recupera los metadatos del servidor VMM. El servidor se muestra en la pestaña **Recursos** de la página **Servidores** del almacén.

Después del registro, puede cambiar la configuración del proveedor en la consola VMM o en la línea de comandos.

## Paso 4: Configurar la protección de la nube

Una vez que los servidores VMM están registrados, puede configurar la protección de la nube. Si ha habilitado la opción **Sincronizar datos de nube con el almacén** cuando se instala el proveedor para que todas las nubes del servidor VMM aparezcan en la pestaña <b>Elementos protegidos</b> del almacén. Si no se puede sincronizar una nube específica con Azure Site Recovery en la pestaña **General** de la página de propiedades de nube en la consola VMM.

![Published Cloud](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CloudsList.png)

1. En la página de inicio rápido, haga clic en **Configurar protección para nubes VMM**.
2. En la pestaña **Nubes de VMM**, seleccione la nube que desea configurar y vaya a la pestaña **Configuración**. 
3. En <b>Destino</b>, seleccione <b>VMM</b>.
4. En <b>Ubicación de destino</b>, seleccione el servidor VMM que administra la nube que desea utilizar para recuperación.
4. En <b>Nube de destino</b>, seleccione la nube de destino que desea utilizar para la conmutación por error de máquinas virtuales en la nube de origen. Observe lo siguiente:
	- Recomendamos seleccionar una nube de destino que cumpla con los requisitos de recuperación para las máquinas virtuales que protegerá.
	- Una nube solo puede pertenecer a un único par de nubes, ya sea como nube principal o de destino.
6. En <b>Copiar frecuencia</b> especifique con qué frecuencia se deben sincronizar datos entre las ubicaciones de origen y de destino. Observe que esta configuración solo es pertinente cuando el host de Hyper-V ejecuta Windows Server 2012 R2. En el caso de otros servidores, se utiliza una configuración predeterminada de cinco minutos.
7. En <b>Puntos de recuperación adicionales</b> especifique si desea crear puntos de recuperación adicionales. El valor predeterminado de cero especifica que el punto de recuperación más reciente para una máquina virtual es el único que se almacena en un servidor host de réplica. Tenga en cuenta que al habilitar varios puntos de recuperación necesita almacenamiento adicional para las instantáneas almacenadas en cada punto de recuperación. De forma predeterminada, los puntos de recuperación se crean cada hora, por lo que cada punto de recuperación contiene datos equivalentes a una hora. El valor del punto de recuperación que asigne a la máquina virtual en la consola VMM no debe ser menor que el valor asignado en la consola de Azure Site Recovery.
8. En <b>Frecuencia de las instantáneas coherentes con la aplicación</b> especifique la frecuencia de creación de estas instantáneas. Hyper-V usa dos tipos de copias de seguridad, una copia de seguridad estándar que proporciona una copia de seguridad incremental de toda la máquina virtual y una instantánea coherente con la aplicación que toma una instantánea en un momento concreto de los datos de la aplicación dentro de la máquina virtual. Las instantáneas coherentes con la aplicación utilizan el Servicio de instantáneas de volumen (VSS) para asegurarse de que las aplicaciones se encuentren en un estado coherente cuando se captura la instantánea. Tenga en cuenta que si habilita las instantáneas coherentes con la aplicación, se verá afectado el rendimiento de aplicaciones que se ejecutan en las máquinas virtuales de origen. Asegúrese de que el valor establecido es menor que el número de puntos de recuperación adicionales configurados.

	![Configure protection settings](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CloudSettings.png)

9. En <b>Compresión de transferencia de datos</b> especifique si se deben comprimir los datos replicados que se transfieren. 
10. En <b>Autenticación</b>, especifique cómo se autentica el tráfico entre el servidor host de Hyper-V principal y el servidor host de Hyper-V de recuperación. Seleccione HTTPS a menos que tenga un entorno de Kerberos configurado en funcionamiento. Azure Site Recovery configurará automáticamente los certificados para la autenticación de HTTPS. No se requiere configuración manual. Si selecciona Kerberos, se usará un vale de Kerberos para la autenticación mutua de los servidores host. De forma predeterminada, se abrirán los puertos 8083 y 8084 (para los certificados) en el Firewall de Windows en los servidores hosts de Hyper-V. Observe que esta configuración solo es pertinente para servidores host de Hyper-V que se ejecutan en Windows Server 2012 R2.
11. En <b>Puerto</b> modifique el número de puerto en el que los equipos host de origen y de destino escuchan el tráfico de replicación. Por ejemplo, puede modificar la configuración si desea aplicar el límite de ancho de banda de red de Calidad de servicio (QoS) para el tráfico de replicación. Compruebe que el puerto no lo está usando ninguna otra aplicación y que está abierto en la configuración del firewall.
12. En <b>Método de replicación</b>, especifique cómo se manejará la replicación inicial de los datos desde la ubicación de origen a la ubicación de destino, antes de que comience la replicación normal. 
	- <b>A través de la red</b>: copiar los datos sobre la red puede ser un proceso lento y que consume muchos recursos. Le recomendamos utilizar esta opción si la nube contiene máquinas virtuales con discos duros virtuales relativamente pequeños y si el servidor VMM principal está conectado al servidor VMM secundario a través de una conexión con un amplio ancho de banda. Puede especificar que la copia se inicie de inmediato, o bien, seleccionar una hora. Si utiliza la replicación de red, le recomendamos programarla evitando las horas de mayor consumo.
	- <b>Sin conexión</b>: este método especifica que la replicación inicial se realizará mediante el uso de medios externos. Esta opción resulta útil si desea evitar la degradación del rendimiento de la red o en el caso de ubicaciones geográficamente remotas. Para utilizar este método, especifique la ubicación de exportación en la nube de origen y la ubicación de importación en la nube de destino. Cuando habilita la protección de una máquina virtual, el disco duro virtual se copia en la ubicación de exportación especificada. Usted lo envía al sitio de destino y lo copia en la ubicación de importación. El sistema copia la información importada en las máquinas virtuales de réplica. Si desea obtener una lista completa de los requisitos previos para la replicación sin conexión, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=323469">Paso 3: Configurar la protección para nubes VMM</a> en la Guía de implementación.
13. Seleccione **Eliminar máquina virtual de réplica** para especificar la máquina virtual de réplica que se debe eliminar si deja de proteger la máquina virtual al seleccionar la opción **Eliminar la protección para la máquina virtual** de la pestaña de máquinas virtuales de las propiedades de la nube. Con esta configuración deshabilitada, cuando deshabilite la protección, la máquina virtual se elimina de Azure Site Recovery, la configuración de Site Recovery para la máquina virtual se elimina de la consola VMM y la réplica se elimina.
	![Configure protection settings](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CloudSettingsRep.png)

<p>Tras guardar la configuración, se creará un trabajo que se podrá supervisar en la pestaña <b>Trabajos</b>. Todos los servidores host de Hyper-V de la nube de origen VMM se configurarán para la replicación. Puede modificar la configuración de la nube en la pestaña <b>Configure</b>. Si desea modificar la ubicación de destino o la nube de destino, deberá eliminar la configuración de la nube y después volver a configurarla.</p>

### Preparación para la replicación inicial sin conexión

Deberá realizar una serie de acciones para preparar la replicación inicial sin conexión:

- En el servidor de origen, especifique una ubicación de ruta de acceso desde la que se llevará a cabo la exportación de datos. Asigne Control total a NTFS y Compartir permisos con el servicio VMM en la ruta de acceso de exportación. En el servidor de destino, especifique una ubicación de ruta de acceso desde la cual tendrá lugar la importación de datos. Asigne los mismos permisos en esta ruta de acceso de importación. 
- Si se comparte la ruta de acceso de importación o exportación, asigne la pertenencia de grupo de administrador, usuario avanzado, operador de impresión o de operador de servidor a la cuenta de servicio de VMM en el equipo remoto en el que se encuentra el recurso compartido.
- Si utiliza cualquier cuenta de ejecución para agregar hosts, en las rutas de acceso de importación y exportación, asigne permisos de lectura y escritura a las cuentas de ejecución de VMM.
- Los recursos compartidos de importación y exportación no deben ubicarse en ningún equipo utilizado como servidor host de Hyper-V, porque la configuración de bucle invertido no es compatible con Hyper-V.
- En Active Directory en cada servidor host de Hyper-V que contiene máquinas virtuales que desea proteger, habilite y configure la delegación restringida para confiar en los equipos remotos en los que se ubican las rutas de acceso de importación y exportación, de la forma siguiente:
	1. En el controlador de dominio, abra **Usuarios y equipos de Active Directory**.
	2. En el árbol de consola, haga clic en **Nombre de dominio** > **Equipos**.
	3. Haga clic con el botón secundario en el nombre del servidor host de Hyper-V > **Propiedades**.
	4. En la pestaña **Delegación**, haga clic en **Confiar en este equipo para la delegación solo a los servicios especificados**.
	5. Haga clic en **Usar cualquier protocolo de autenticación**.
	6. Haga clic en **Agregar** > **Usuarios y equipos**.
	7. Escriba el nombre del equipo que hospeda la ruta de acceso de exportación > **Aceptar**. En la lista de servicios disponibles, mantenga presionada la tecla CTRL y haga clic en **cifs** > **Aceptar**. Repita el proceso para el nombre del equipo que hospeda la ruta de acceso de importación. Repita según sea necesario para servidores host de Hyper-V adicionales.
	
## Paso 5: Configurar asignación de red
1. En la página de inicio rápido, haga clic en **Asignar redes**.
2. Seleccione el servidor VMM de origen desde el que desea asignar las redes y, a continuación, el servidor VMM de destino al que se asignarán las redes. Se muestra la lista de redes de origen y sus redes de destino asociadas. En el caso de las redes no asignadas, aparece un valor en blanco. Haga clic en el icono de información junto a los nombres de las redes de origen y de destino para ver las subredes para cada de red.
3. Seleccione una red en **Red en origen** > **Asignar**. El servicio detecta las redes de VM en el servidor de destino y las muestra. 

	![Configure network mapping](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_NetworkMapping1.png)

4. En el cuadro de diálogo, seleccione una de las redes de máquina virtual desde el servidor VMM de destino.

	![Select a target network](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_NetworkMapping2.png)

5. Al seleccionar una red de destino, se muestran las nubes protegidas que utilizan la red de origen. También se muestran las redes de destino disponibles asociadas a las nubes que se utilizan para protección. Se recomienda que seleccione una red de destino que esté disponible para todas las nubes que esté utilizando para protección.
6. Haga clic en la marca de verificación para completar el proceso de asignación. Un trabajo comienza a realizar un seguimiento del progreso de la asignación. Puede consultarlo en la pestaña **Trabajos**.


## Paso 6: Configurar la asignación de almacenamiento
De forma predeterminada cuando se replica una máquina virtual en un servidor host de Hyper-V de origen a un servidor host de Hyper-V de destino, los datos replicados se almacenan en la ubicación predeterminada indicada para el host de Hyper-V de destino en el Administrador de Hyper-V. Para obtener más control sobre dónde se almacenan los datos replicados, puede configurar la asignación de almacenamiento de la manera siguiente:

- Defina las clasificaciones de almacenamiento en los servidores VMM de origen y destino. Para obtener instrucciones, consulte [Creación de clasificaciones de almacenamiento en VMM](http://go.microsoft.com/fwlink/?LinkId=400937). Las clasificaciones deben estar disponibles para los servidores host de Hyper-V en las nubes de origen y destino. Las clasificaciones no necesitan tener el mismo tipo de almacenamiento. Por ejemplo puede asignar una clasificación de origen que contenga los recursos compartidos de SMB a una clasificación de destino que contenga CSV.
- Cuando estén definidas las clasificaciones, podrá crear las asignaciones.
1. En la página **Inicio rápido** > **Asignar almacenamiento**.
1. Haga clic en la pestaña **Almacenamiento** > **Asignar clasificaciones de almacenamiento**.
1. En la pestaña **Asignar clasificaciones de almacenamiento**, seleccione las clasificaciones en los servidores VMM de origen y destino. Guarde la configuración.

	![Select a target network](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_StorageMapping1.png)


## Paso 7: Enable virtual machine protection
Una vez que los servidores, las nubes y las redes se configuran correctamente, puede habilitar la protección para las máquinas virtuales en la nube.

1. En la pestaña **Máquinas virtuales**, haga clic en la nube en la que se encuentra la máquina virtual, haga clic en **Habilitar la protección** > **Agregar máquinas virtuales**. 
2. En la lista de máquinas virtuales de la nube, seleccione la que desea proteger.


![Enable virtual machine protection](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_EnableProtectionVM.png)

Siga el progreso de la acción de habilitación de la protección en la pestaña **Trabajos**, incluida la replicación inicial. La máquina virtual estará preparada para la conmutación por error después de que finalice el trabajo de Finalizar protección. Después de habilitar la protección y replicar las máquinas virtuales, podrá verlas en Azure.

Tenga en cuenta que también puede habilitar la protección de las máquinas virtuales en la consola VMM. Haga clic en Habilitar protección en la barra de herramientas de la pestaña Azure Site Recovery en las propiedades de la máquina virtual.


![Virtual machine protection job](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_VMJobs.png)

### Incorporación de máquinas virtuales existentes
Si tiene máquinas virtuales en VMM que se replican mediante Réplica de Hyper-V, necesitará incorporarlas para la protección de Azure Site Recovery de la forma siguiente:
1. Compruebe que dispone de nubes principales y secundarias. Asegúrese de que el servidor de Hyper-V que hospeda la máquina virtual existente se encuentra en la nube principal y que el servidor de Hyper-V que hospeda la máquina virtual de réplica se encuentra en la nube secundaria. Asegúrese de que ha configurado las opciones de protección para las nubes. La configuración debe coincidir con la configurada para Réplica de Hyper-V. En caso contrario, es posible que la replicación de las máquinas virtuales no funcione según lo esperado.
2. Después habilite la protección de la máquina virtual principal. Azure Site Recovery y VMM se asegurarán de que se detecta el mismo host de réplica y la máquina virtual, y Azure Site Recovery reutilizará y restablecerá la replicación mediante los valores configurados durante la configuración de la nube.


## Prueba de la implementación

Para probar la implementación puede realizar una prueba de conmutación por error para una máquina virtual individual o crear un plan de recuperación que incluya numerosas máquinas virtuales y realizar una conmutación por error de prueba para el plan.  La conmutación por error de prueba simula su mecanismo de conmutación por error y recuperación en una red aislada.

### Creación de un plan de recuperación

1. En la pestaña **Planes de recuperación**, haga clic en **Crear plan de recuperación**.
2. Especifique un nombre para el plan de recuperación, y los servidores VMM de origen y destino. El servidor de origen debe tener máquinas virtuales habilitadas para conmutación por error y recuperación. Seleccione **Hyper-V** para ver solo las nubes que están configuradas para la replicación de Hyper-V.

	![Create recovery plan](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_RP1.png)

3. En **Seleccionar máquina virtual**, seleccione grupos de replicación. Se seleccionarán todas las máquinas virtuales asociadas al grupo de replicación y se agregarán al plan de recuperación. Estas máquinas virtuales se agregan al grupo predeterminado del plan de recuperación: grupo 1. Puede agregar más grupos si es necesario. Tenga en cuenta que tras la replicación las máquinas virtuales se iniciarán según el orden de los grupos del plan de recuperación.

	![Add virtual machines](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_RP2.png)	

Cuando se haya creado un plan de recuperación, aparecerá en la lista de la pestaña **Planes de recuperación**. 

###Ejecución de una conmutación por error de prueba

1. En la pestaña **Planes de recuperación**, seleccione el plan y haga clic en **Conmutación por error de prueba**.
2. En la página **Confirmar conmutación por error de prueba** seleccione **Ninguno**. Tenga en cuenta que con esta opción habilitada las máquinas virtuales de réplica de conmutación por error no se conectarán a ninguna red. Esto probará que la máquina virtual realiza un conmutación por error de la manera esperada pero no prueba su entorno de red de replicación. Si desea ejecutar una conmutación por error de prueba más amplia, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=522291">Prueba de una implementación local en MSDN</a>.

	![Select test network](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_TestFailover1.png)


7. La máquina virtual de prueba se creará en el mismo host en el que existe la máquina virtual de réplica. No se agregará a la nube en la que se encuentra la máquina virtual de réplica.

### Ejecución de un plan de recuperación
Después de la replicación, la máquina virtual de réplica tendrá una dirección IP que no es la misma que la dirección IP de la máquina virtual principal. Si está emitiendo direcciones desde DHCP, DNS se actualizará de manera automática. Si no utiliza DHCP y desea asegurarse de que las direcciones son las mismas, deberá ejecutar un par de scripts.

#### Script para recuperar la dirección IP
Ejecute este script de ejemplo para recuperar la dirección IP.
    **$vm = Get-SCVirtualMachine -Name <VM_NAME>
	$na = $vm[0].VirtualNetworkAdapters>
	$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
	$ip.address**  

#### Script para actualizar DNS
Ejecute este script de ejemplo para actualizar DNS, especificando la dirección IP que ha recuperado con el script de ejemplo anterior.

	**[string]$Zone,
	[string]$name,
	[string]$IP
	)
	$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
	$newrecord = $record.clone()
	$newrecord.RecordData[0].IPv4Address  =  $IP
	Set-DnsServerResourceRecord -zonename com -OldInputObject $record -NewInputObject $Newrecord**



<a name="privacy"></a><h2>Información de privacidad para Site Recovery</h2>

En esta sección se proporciona información adicional de privacidad del servicio Microsoft Azure Site Recovery ("servicio"). Para consultar la declaración de privacidad de los servicios de Microsoft Azure, consulte
[Declaración de privacidad de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899)

**Característica: Registro**

- **Qué hace**: Registra el servidor con el servicio para que las máquinas virtuales se puedan proteger.
- **Información recopilada**: Después de registrar el servicio, recopila, procesa y transmite la información del certificado de administración desde el servidor VMM, designado para proporcionar recuperación ante desastres mediante el nombre de servicio del servidor VMM y el nombre de las nubes de máquinas virtuales en el servidor VMM.
- **Uso de la información**:
	- Certificado de administración: se utiliza para ayudar a identificar y autenticar el servidor VMM registrado para el acceso al servicio. El servicio utiliza la parte de clave pública del certificado para proteger un token al que solo puede obtener acceso el servidor VMM registrado. El servidor debe utilizar este token para obtener acceso a las características del servicio.
	- Nombre del servidor VMM: el nombre del servidor VMM es necesario para identificar y comunicarse con el servidor VMM adecuado en donde se ubican las nubes.
	- Nombres de nubes del servidor VMM: el nombre de nube es obligatorio cuando se utiliza la característica de emparejar o desemparejar las nubes del servicio descrita a continuación. Si decide emparejar la nube de un centro de datos principal con otra nube en el centro de datos de recuperación, se muestran los nombres de todas las nubes del centro de datos de recuperación.

- **Opción**: Esta información es una parte esencial del proceso de registro del servicio, ya que ayuda al usuario y al servicio a identificar el servidor VMM para el que desea proporcionar protección de Azure Site Recovery, así como para identificar el servidor VMM registrado correcto. Si no desea enviar esta información al servicio, no lo utilice. Si registra el servidor y después desea anular el registro, puede hacerlo eliminando la información del servidor VMM desde el portal de servicio (que es el Portal de Azure).

**Característica: Habilitación de la protección de Azure Site Recovery**

- **Qué hace**: El proveedor de Azure Site Recovery instalado en el servidor VMM es el conducto para comunicarse con el servicio. El proveedor es una biblioteca de vínculos dinámicos (DLL) que se hospeda en el proceso VMM. Después de instalado el proveedor, se habilita la característica "Recuperación de centro de datos" en la consola de administrador de VMM. Las máquinas virtuales nuevas o existentes en una nube puede habilitar una propiedad denominada "Recuperación de centro de datos" para ayudar a proteger la máquina virtual. Una vez establecida esta propiedad, el proveedor envía el nombre y el identificador de la máquina virtual al servicio. La protección virtual se habilita mediante la tecnología de replicación Hyper-V de Windows Server 2012 o Windows Server 2012 R2. Los datos de la máquina virtual se replican de un host de Hyper-V a otro (ubicado por lo general en otro centro de datos de "recuperación").

- **Información recopilada**: El servicio recopila, procesa y transmite los metadatos a la máquina virtual, que incluye el nombre, el identificador, la red virtual y el nombre de la nube 
a la que pertenece.

- **Uso de la información**: El servicio utiliza la información anterior para rellenar la información de la máquina virtual en el portal del servicio.

- **Opción**: Es una parte esencial del servicio y no se puede desactivar. Si no desea que esta información se envíe al servicio, no habilite la protección de Azure Site Recovery en cualquier máquina virtual. Tenga en cuenta que todos los datos enviados por el proveedor al servicio se envían a través de HTTPS.

**Característica: Plan de recuperación**

- **Qué hace**: Esta característica le ayuda a crear un plan de coordinación para el centro de datos de "recuperación". Puede definir el orden en el que se deben iniciar las máquinas virtuales o un grupo de máquinas virtuales en el sitio de recuperación. También puede especificar los scripts automatizados que se pueden ejecutar, o cualquier acción manual que deba emprenderse, en el momento de la recuperación para cada máquina virtual. Normalmente se activa la conmutación por error (que se describe en la sección siguiente) en el nivel de plan de recuperación para la recuperación coordinada.

- **Información recopilada**: El servicio recopila, procesa y transmite los metadatos para el plan de recuperación, incluidos los metadatos de la máquina virtual y los metadatos de todos los scripts de automatización y las notas de la acción manual.

- **Uso de la información**: Los metadatos descritos anteriormente se utilizan para generar el plan de recuperación en el portal del servicio.

- **Opción**: Es una parte esencial del servicio y no se puede desactivar. Si no desea enviar esta información al servicio, no cree planes de recuperación en este servicio.

**Característica: Asignación de redes**

- **Qué hace**: Esta característica permite asignar la información de red desde el centro de datos principal al centro de datos de recuperación. Cuando se recuperan las máquinas virtuales en el sitio de recuperación, esta asignación permite establecer la conectividad de red para ellas.

- **Información recopilada**: Como parte de la característica de asignación de redes, el servicio recopila, procesa y transmite los metadatos de las redes lógicas a cada sitio (principal y centro de datos).

- **Uso de la información**: el servicio usa los metadatos para rellenar el portal del servicio donde puede asignar la información de red.

- **Opción**: Es una parte esencial del servicio y no se puede desactivar. Si no desea que esta información se envíe al servicio, no utilice la característica de asignación de redes.

**Característica: Conmutación por error - Planeada, no planeada, prueba**

- **Qué hace**: Esta característica permite la conmutación por error de una máquina virtual de un centro de datos administrados de VMM a otro centro de datos administrados de VMM. La acción de conmutación por error la desencadena el usuario en el portal del servicio. Las posibles razones para una conmutación por error incluyen un evento no planeado (por ejemplo, en el caso de un desastre natural; un evento planeado (por ejemplo un equilibrio de carga del centro de datos); una conmutación por error de prueba (por ejemplo un ensayo del plan de recuperación).

El proveedor en el servidor VMM recibe notificaciones de los eventos del servicio y ejecuta una acción de conmutación por error en el host de Hyper-V a través de las interfaces VMM. La conmutación por error real de la máquina virtual desde un host de Hyper-V a otro (se ejecuta por lo general en otro centro de datos de "recuperación") se controla mediante la tecnología de replicación de Hyper-V de Windows Server 2012 o Windows Server 2012 R2. Una vez completada la conmutación por error, el proveedor instalado en el servidor VMM del centro de datos de "recuperación" envía la información correcta al servicio.

- **Información recopilada**: El servicio utiliza la información anterior para rellenar el estado de la información de la acción de conmutación por error en el portal del servicio.

- **Uso de la información**: El servicio utiliza la información anterior de la forma siguiente:

	- Certificado de administración: se utiliza para ayudar a identificar y autenticar el servidor VMM registrado para el acceso al servicio. El servicio utiliza la parte de clave pública del certificado para proteger un token al que solo puede obtener acceso el servidor VMM registrado. El servidor debe utilizar este token para obtener acceso a las características del servicio.
	- Nombre del servidor VMM: el nombre del servidor VMM es necesario para identificar y comunicarse con el servidor VMM adecuado en donde se ubican las nubes.
	- Nombres de nubes del servidor VMM: el nombre de nube es obligatorio cuando se utiliza la característica de emparejar o desemparejar las nubes del servicio descrita a continuación. Si decide emparejar la nube de un centro de datos principal con otra nube en el centro de datos de recuperación, se muestran los nombres de todas las nubes del centro de datos de recuperación.

- **Opción**: Es una parte esencial del servicio y no se puede desactivar. Si no desea que esta información se envíe al servicio, no lo utilice.


<!--HONumber=49-->