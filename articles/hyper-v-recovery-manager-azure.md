<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Configure Azure Site Recovery to protect virtual machines on Hyper-V server located in VMM clouds" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in VMM clouds from one on-premises site to another. Azure Site Recovery can also replicate, failover, and recover Hyper-V virtual machine data between VMM clouds and Microsoft Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to Azure Protection" editor="jimbe" manager="cfreeman" authors="" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Introducción a la recuperación de sitios de Azure: protección de local a Azure

<div class="dev-callout"> 

<p>Utilice Azure Site Recovery para proteger las m&aacute;quinas virtuales que se ejecutan en hosts de Hyper-V ubicados en nubes de System Center Virtual Machine Manager (VMM). Puede configurar:</p>

<ul>
<li><b>Protecci&oacute;n de local a Azure</b>. Replique en Azure las m&aacute;quinas virtuales locales ubicadas en servidores host de Hyper-V de nubes de VMM. Configure y habilite la configuraci&oacute;n de protecci&oacute;n en los almacenes de Azure Site Recovery. Los datos de m&aacute;quinas virtuales se replican desde el servidor de Hyper-V local al almacenamiento de Azure.</li>

<li><b>Protecci&oacute;n de local a local</b>. Replique las m&aacute;quinas virtuales ubicadas en servidores host de Hyper-V de nubes de VMM de un sitio local a otro. Configure y habilite la configuraci&oacute;n de protecci&oacute;n en los almacenes de Azure Site Recovery. Los datos de m&aacute;quinas virtuales se replican desde un servidor de Hyper-V local a otro. Azure Site Recovery coordina simplemente el proceso.
Puede obtener m&aacute;s informaci&oacute;n sobre este escenario en <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Introducci&oacute;n a Azure Site Recovery: protecci&oacute;n de local a local</a>.</li>

</ul>
 
<h2><a id="about"></a>Acerca de este tutorial</h2>


<P>Este tutorial pretender ayudarle a implementar Azure Site Recovery a modo de prueba de concepto r&aacute;pida. Se utiliza la ruta m&aacute;s r&aacute;pida y la configuraci&oacute;n predeterminada siempre que es posible. Se incluyen los pasos para:
<ul>
<li>Configurar un almac&eacute;n de Azure Site Recovery: Cargue un certificado en el almac&eacute;n y config&uacute;relo en el servidor VMM de origen. A continuaci&oacute;n, genere una clave de almac&eacute;n. </li>
<li>Configurar el servidor VMM de origen y los servidores host de Hyper-V: Instale el proveedor de Azure Site Recovery en el servidor VMM de origen e instale el agente de los Servicios de recuperaci&oacute;n de Azure en los servidores host de Hyper-V.</li>
<li>Configurar las nubes de VMM: Configure los valores de protecci&oacute;n para las nubes en el servidor VMM de origen.</li>
<li>Habilitar las m&aacute;quinas virtuales: Habilite la protecci&oacute;n para las m&aacute;quinas virtuales.</li>
<li>Ejecutar una conmutaci&oacute;n por error: Cree un plan de recuperaci&oacute;n y ejecute una conmutaci&oacute;n por error de prueba.</li>
</ul>


Para obtener informaci&oacute;n sobre una implementaci&oacute;n completa, consulte:</P>

<UL>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planificaci&oacute;n de una implemetaci&oacute;n de Azure Site Recovery</a>: En este tema se describen los pasos de planificaci&oacute;n que debe completar antes de realizar una implementaci&oacute;n completa.</LI>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=402679">Implementaci&oacute;n de Azure Site Recovery: del entorno local a la protecci&oacute;n de Azure</a>: En este tema se proporcionan instrucciones paso a paso para realizar una implementaci&oacute;n completa.</LI>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=378272">Administraci&oacute;n y supervisi&oacute;n de Azure Site Recovery</a>: En este tema se describe el modo de ejecutar conmutaciones por error, y c&oacute;mo administrar y supervisar la implementaci&oacute;n.</LI>
</UL>
<P>Si experimenta problemas durante este tutorial, revise el art&iacute;culo de wiki <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: escenarios de error comunes y soluciones</a>, o publique sus preguntas en el <a href="http://go.microsoft.com/fwlink/?LinkId=313628">foro de Servicios de recuperaci&oacute;n de Azure</a>.</P>

</div>

## <span id="before"></span></a>Antes de empezar

<div class="dev-callout"> 
<P>Antes de empezar con este tutorial, compruebe los requisitos previos.</P>

<h3><a id="HVRMPrereq"></a>Requisitos previos de Azure</h3>

<UL>
<LI><b>Cuenta de Azure</b>: Necesitar&aacute; una cuenta de Azure. Si no tiene una, consulte <a href="http://aka.ms/try-azure">evaluaci&oacute;n gratuita de Azure</a>. Puede obtener informaci&oacute;n sobre precios en la p&aacute;gina de <a href="http://go.microsoft.com/fwlink/?LinkId=378268">detalles de precios de Azure Site Recovery Manager</a>.</LI>
<LI><b>Certificado</b>: Deber&aacute; cargar un certificado de administraci&oacute;n (.cer) con una clave p&uacute;blica en el almac&eacute;n. Este certificado se exportar&aacute; como un archivo .pfx (con clave privada) y se importar&aacute; en cada uno de los servidores de VMM que desea registrar en el almac&eacute;n. En este tutorial, usar&aacute; un certificado autofirmado. En el caso de una implementaci&oacute;n completa, puede usar un certificado SSL v&aacute;lido que se compila con los <a href="http://go.microsoft.com/fwlink/?LinkId=321294">requisitos de certificado</a> descritos en la gu&iacute;a de planificaci&oacute;n</a>. </LI>


</LI>

<LI><b>Cuenta de almacenamiento de Azure</b>: Necesitar&aacute; una cuenta de almacenamiento de Azure para almacenar los datos replicados en Azure. La cuenta debe tener habilitada la replicaci&oacute;n geogr&aacute;fica. Adem&aacute;s, debe estar en la misma regi&oacute;n que el servicio Azure Site Recovery y estar asociada a la misma suscripci&oacute;n. Para obtener m&aacute;s informaci&oacute;n sobre la configuraci&oacute;n del almacenamiento de Azure, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=398704">Introducci&oacute;n al almacenamiento de Microsoft Azure</a>.</LI>
</UL>

<h3><a id="VMMPrereq"></a>Requisitos previos de VMM</h3>

<UL>
<LI><b>Servidor VMM</b>: un servidor VMM que se ejecute en System Center 2012 R2.</LI>
<LI><b>Nubes VMM</b>: una nube al menos en el servidor VMM que contenga:
    <UL>
    <LI>Uno o m&aacute;s grupos de hosts de VMM</LI>
    <LI>Uno o m&aacute;s servidores host de Hyper-V o cl&uacute;steres en cada grupo de hosts</LI>
    <li>Una o varias m&aacute;quinas virtuales ubicadas en el servidor de Hyper-V de origen en la nube. Las m&aacute;quinas virtuales deben ser de la generaci&oacute;n 1.</li>
        </UL></LI>  
</UL>

<h3><a id="VMPrereq"></a>Requisitos previos de las m&aacute;quinas virtuales</h3>

<UL>
<LI><b>Generaci&oacute;n</b>: Azure solo admite m&aacute;quinas virtuales de la generaci&oacute;n 1.</LI>
<LI>Para obtener una lista completa de los requisitos de compatibilidad de las m&aacute;quinas virtuales para la conmutaci&oacute;n por error en Azure, lea <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Requisitos previos y compatibilidad</a> en la gu&iacute;a de planificaci&oacute;n. </LI>  
</UL>

<h2><a id="tutorial"></a>Pasos del tutorial</h2> 

Despu&eacute;s de comprobar los requisitos previos, realice las siguientes tareas:
<UL>
<LI><a href="#createcert">Paso 1: Obtener y configurar los certificados</a>. Obtenga un certificado .cer, exp&oacute;rtelo como un archivo .pfx e importe el archivo .pfx en los servidores VMM.</LI>
<LI><a href="#vault">Paso 2: Crear un almac&eacute;n</a>. Cree un almac&eacute;n de Azure Site Recovery.</LI>
<LI><a href="#upload">Paso 3: Configurar el almac&eacute;n</a>. Cargue un certificado de administraci&oacute;n en el almac&eacute;n y genere una clave para dicho almac&eacute;n. La clave se usa para tener la seguridad de que el proveedor ubicado en el servidor VMM solo ejecuta los comandos que env&iacute;a Azure Site Recovery.</LI>
<LI><a href="#download">Paso 4: Instalar la aplicaci&oacute;n del proveedor</a>. Ejecute la aplicaci&oacute;n del proveedor de Microsoft Azure Site Recovery en el servidor VMM. De esta forma se instala el proveedor y se registra el servidor VMM en el almac&eacute;n.</LI>
<LI><a href="#agent">Paso 5: Instalar la aplicaci&oacute;n del agente</a>. Instale el agente de los Servicios de recuperaci&oacute;n de Microsoft Azure en cada host de Hyper-V.</LI>
<LI><a href="#clouds">Paso 6: Configurar la protecci&oacute;n de la nube</a>. Configure la protecci&oacute;n para las nubes VMM.</LI>
<LI><a href="#NetworkMapping">Paso 7: Configurar la asignaci&oacute;n de red</a>. Opcionalmente puede configurar la asignaci&oacute;n de red para asignar redes VM de origen a redes Azure de destino.</LI>
<LI><a href="#virtualmachines">Paso 8: Habilitar la protecci&oacute;n para las m&aacute;quinas virtuales</a>. Habilite la protecci&oacute;n para las m&aacute;quinas virtuales ubicadas en nubes VMM protegidas.</LI>
<LI><a href="#recovery plans">Paso 9: Configurar y ejecutar planes de recuperaci&oacute;n</a>. Cree un plan de recuperaci&oacute;n y ejecute una conmutaci&oacute;n por error de prueba para el plan.</LI>
</UL>



<a name="createcert"></a> <h2>Paso 1: Obtenci&oacute;n y configuraci&oacute;n de certificados</h2>

Obtenga y configure certificados de la siguiente manera:
<OL>
<LI><a href="#obtaincert">Obtenci&oacute;n de un certificado autofirmado para el tutorial</a>. Obtenga un certificado con la herramienta MakeCert.</LI>
<LI><a href="#exportcert">Exportaci&oacute;n del certificado en formato .pfx</a>. En el servidor en que se cre&oacute; el certificado, exporte el archivo .cer como archivo .pfx (con la clave privada). </LI>
<LI><a href="#importcert">Importaci&oacute;n del certificado .pfx en el servidor VMM</a>. Tras la exportaci&oacute;n, importe el archivo .pfx en la carpeta Personal del almac&eacute;n del equipo local del servidor VMM que quiere registrar con el almac&eacute;n.</LI>
</OL>


<h3><a id="obtaincert"></a>Obtenci&oacute;n de un certificado autofirmado (.cer)</h3>
<P>Cree un certificado .cer x.509 que cumpla con todos los requisitos de certificado:</P>
<ol>
<LI>
En el equipo en el que desea ejecutar MakeCert, descargue la versi&oacute;n m&aacute;s reciente del <a href="http://go.microsoft.com/fwlink/?LinkId=378269">Windows SDK</a>. No es necesario instalar el SDK entero.</LI>
<ol>
<LI>En la p&aacute;gina para especificar ubicaci&oacute;n, seleccione <b>Instalar el Kit de desarrollo de software de Windows para Windows 8.1 en este equipo</b>.</LI>
<LI>En la p&aacute;gina para seleccionar las caracter&iacute;sticas que desea instalar, elimine todas las opciones, excepto <b>Kit de desarrollo de software de Windows</b>.</LI>
<LI>Una vez que se haya completado la instalaci&oacute;n, compruebe que el archivo makecert.exe aparece en la carpeta C:\ProgramFiles (x86)\Windows Kits\<i>Versi&oacute;nWindows</i>\bin\x64.</LI>
<LI>Abra una ventana del s&iacute;mbolo del sistema (cmd.exe) con privilegios de administrador y navegue a la carpeta makecert.exe.</LI> 
<LI>Ejecute el siguiente comando para crear su certificado autofirmado. Reemplace CertificateName por el nombre que desea utilizar para el certificado y especifique la fecha real de caducidad del certificado despu&eacute;s de -e:</LI>
<code>
makecert.exe -r -pe -n CN=CertificateName -ss my -sr localmachine -eku 1.3.6.1.5.5.7.3.2 -len 2048 -e 01/01/2016 CertificateName.cer</code>
</ol>
<P>El estado correcto indica que el certificado se ha creado. Est&aacute; almacenado en la misma carpeta que makecert.exe, pero puede moverlo a una ubicaci&oacute;n m&aacute;s accesible para la exportaci&oacute;n.</P>

<h3><a id="exportcert"></a>Exportaci&oacute;n del certificado en formato .pfx</h3>
<P>Realice los pasos de este procedimiento para exportar el archivo .cer en formato .pfx.</P>
<ol>
<li>El estado correcto indica que el certificado se ha creado. Est&aacute; almacenado en la misma carpeta que makecert.exe, pero puede moverlo a una ubicaci&oacute;n m&aacute;s accesible para la exportaci&oacute;n.</li>
<li>En el panel de detalles, haga clic en el certificado que desea administrar.</li>
<li>En el men&uacute; <b>Acci&oacute;n</b>, seleccione <b>Todas las tareas</b> y luego haga clic en <b>Exportar</b>. Aparece el Asistente para exportar certificados. Haga clic en <b>Next</b>.</li>
<li>En la p&aacute;gina <b>Exportar clave privada</b>, haga clic en <b>S&iacute;</b> para exportar la clave privada. Haga clic en <b>Next</b>. Observe que esta acci&oacute;n solo es necesaria si desea exportar la clave privada a otros servidores despu&eacute;s de la instalaci&oacute;n.</li>
<li>En la p&aacute;gina de formato de archivo de exportaci&oacute;n, seleccione <b>Intercambio de informaci&oacute;n personal: PKCS #12 (.PFX)</b>. Haga clic en <b>Next</b>.</li>
<li>En la p&aacute;gina <b>Contrase&ntilde;a</b>, escriba y confirme la contrase&ntilde;a que se utiliza para cifrar la clave privada. Haga clic en <b>Next</b>.</li>
<li>Siga las p&aacute;ginas del asistente para exportar el certificado en formato .pfx.</li>
</ol>

<h3><a id="importcert"></a>Importaci&oacute;n del certificado .pfx en los servidores VMM</h3>
<p>Tras la exportaci&oacute;n, copie el archivo .pfx en el servidor VMM y luego imp&oacute;rtelo. Observe que si ejecut&oacute; MakeCert.exe en un servidor VMM, no es necesario que importe el certificado en ese servidor.</p>
 
<ol>
<li>Copie los archivos del certificado de clave privada (.pfx) en una ubicaci&oacute;n del servidor local.</li>
<li>En el complemento Certificados de MMC, seleccione <b>Cuenta de equipo</b> y haga clic en <b>Siguiente</b>.</li>
<li>Seleccione <b>Equipo local</b> y haga clic en <b>Finalizar</b>.</li>
<li>En MMC, expanda <b>Certificados</b>, haga clic con el bot&oacute;n secundario en <b>Personal</b>, se&ntilde;ale <b>Todas las tareas</b> y luego haga clic en <b>Importar</b> para iniciar el Asistente para importar certificados.</li>
<li>En la p&aacute;gina de archivo para importar, haga clic en <b>Examinar</b> y busque la carpeta que contiene el archivo de certificado .pfx con el certificado que desea importar. Seleccione el archivo correspondiente y, a continuaci&oacute;n, haga clic en <b>Abrir</b>.</li>
<li>En la p&aacute;gina de contrase&ntilde;a, en el cuadro <b>Contrase&ntilde;a</b>, escriba la contrase&ntilde;a del archivo .pfx de clave privada y haga clic en <b>Siguiente</b>.</li>
<li>En la p&aacute;gina del almac&eacute;n de certificados, seleccione <b>Colocar todos los certificados en el siguiente almac&eacute;n</b>, haga clic en <b>Examinar</b>, seleccione el almac&eacute;n <b>Personal</b>, haga clic en <b>Aceptar</b> y luego en <b>Siguiente</b>. Realice los pasos del asistente.</li>
</ol>

Una vez que complete estos pasos, podr&aacute; elegir el certificado .cer para cargar cuando configure el almac&eacute;n y seleccionar el certificado .pfx cuando registre un servidor VMM durante la instalaci&oacute;n del proveedor.
</div>

<a name="vault"></a>

## Paso 2: Creación de un almacén

1.  Inicie sesión en el [Portal de administración][Portal de administración].


2. Expanda **Servicios de datos**, luego **Servicios de recuperación** y haga clic en **Almacén de Site Recovery**.

3.  Haga clic en **Crear nuevo** y luego en **Creación rápida**.

4.  En **Name**, escriba un nombre descriptivo para identificar el almacén.

5.  En **Región**, seleccione la región geográfica del almacén. Las regiones geográficas disponibles incluyen Asia Oriental, Europa Occidental, Oeste de los EE. UU., Este de EE. UU., Europa del Norte y Sudeste asiático.
6.  Haga clic en **Create vault**.

    ![Almacén nuevo][Almacén nuevo]

Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como **Activo** en la página principal de Servicios de recuperación.

<a name="upload"></a>

## Paso 3: Configuración del almacén


1.  En la página **Servicios de recuperación**, haga clic en el almacén para abrir la página de inicio rápido. El inicio rápido también se puede abrir en cualquier momento mediante el icono.

    ![Icono de inicio rápido][Icono de inicio rápido]

2.  En la lista desplegable **Configurar recuperación**, seleccione **Entre un sitio local y Microsoft Azure**.
3.  Para cargar el certificado (.cer) en el almacén, haga clic en **Administrar certificado**.

    ![Inicio rápido][Inicio rápido]

4.  En el cuadro de diálogo **Administrar certificado**, haga clic en **Buscar archivo** y seleccione el archivo .cer.

    ![Administrar certificado][Administrar certificado]

5.  Para generar una clave para el almacén, haga clic en **Obtener la clave del almacén**. La clave se genera automáticamente. Si regenera una clave, esta sobrescribirá la anterior. Tenga en cuenta que necesitará esta clave más adelante cuando instale el proveedor de Azure Site Recovery en el servidor VMM.

    ![Administrar certificado][1]

<a name="download"></a>

## Paso 4: Instalación del proveedor de Azure Site Recovery


1.  En la página **Inicio rápido**, haga clic en **Descargar proveedor** para obtener la versión más reciente del archivo de instalación del proveedor.

    ![Download Provider File][Download Provider File]

2.  Ejecute este archivo en el servidor VMM de origen.

    ![Descarga del agente][Descarga del agente]

3.  Tras la instalación del proveedor, continúe con la configuración para registrar el servidor en el almacén.
    ![Setup Complete][Setup Complete]
4.  En la página de conexión a Internet, especifique cómo se conecta a Internet el proveedor que se ejecuta en el servidor VMM. Haga clic en **Siguiente** para usar la configuración de conexión a Internet predeterminada definida en el servidor.
    ![Internet Settings][Internet Settings]
5.  En la página de registro del almacén, lleve a cabo los siguientes pasos:

    -   Seleccione la clave privada (.pfx) que ha importado en el servidor VMM.
    -   Seleccione el almacén en el que desea registrar el servidor.
    -   Especifique la clave del almacén. Esta es la clave que generó anteriormente. Corte y pegue el valor de la clave desde la página de inicio rápido.

    </p>
    ![Registro de certificado][Registro de certificado]

6.  En la página de cifrado de los datos, especifique si desea permitir la opción de cifrar los datos durante la replicación para una nube determinada. Si selecciona esta opción, se generará un certificado SSL automáticamente. Cuando ejecute una conmutación por error, necesitará seleccionar este certificado. Después de habilitar este valor, puede habilitar o deshabilitar el cifrado de los datos para una nube en el portal de Azure Site Recovery. En este tutorial, deje el valor predeterminado y haga clic en **Siguiente**.

    ![Almacén de certificados][Almacén de certificados]

7.  En la página del servidor VMM, lleve a cabo los siguientes pasos:

    -   Especifique un nombre descriptivo para el servidor VMM. Este nombre se utiliza para identificar el servidor en la consola de Azure Site Recovery.
    -   Seleccione **Sincronizar metadatos de nube con el almacén** para sincronizar la información sobre las nubes VMM con el almacén de Azure Site Recovery. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede publicar individualmente cada nube para así sincronizarla antes de configurar la protección de la nube.

8.  Haga clic en **Register** para finalizar el proceso.

    ![PublishCloud][PublishCloud]

Una vez que un servidor se registre correctamente, su nombre descriptivo aparecerá en la pestaña **Recursos** de la página de servidores en el almacén.

## <span id="storage"></span></a>Paso 5: Instalación del agente de los Servicios de recuperación de Azure

Instale el agente de los Servicios de recuperación de Azure en cada servidor host de Hyper-V situado en las nubes VMM que desea proteger.

1.  En la página de inicio rápido, haga clic en **Descargar el agente de los servicios de Azure Site Recovery e instalar en los hosts**, para obtener la última versión del archivo de instalación del agente.

    ![Install Recovery Services Agent][Install Recovery Services Agent]

2.  Ejecute el archivo de instalación en cada servidor host de Hyper-V situado en las nubes VMM que desea proteger.
3.  En la página de comprobación de requisitos previos, haga clic en **Siguiente**. Todos los requisitos previos que falten se instalarán automáticamente.

	![Prerequisites Recovery Services Agent][Prerequisites Recovery Services Agent]

1.  En la página de configuración de la instalación, especifique dónde desea instalar el agente y seleccione la ubicación de la caché en la que se instalarán los metadatos de copia de seguridad. Luego haga clic en **Instalar**.

En la página de conexión a Internet, especifique cómo se conecta a Internet el proveedor que se ejecuta en el servidor VMM. Haga clic en **Siguiente** para usar la configuración de conexión a Internet predeterminada definida en el servidor.

![Recovery Services Agent location][Recovery Services Agent location]

## <span id="clouds"></span></a>Paso 6: Configurar la protección de la nube

Una vez que los servidores VMM están registrados, puede configurar la protección de la nube. La opción **Sincronizar datos de nube con el almacén** se habilita cuando se instala el proveedor, de modo que todas las nubes del servidor VMM aparecerán en la pestaña **Elementos protegidos** del almacén.

![Nube publicada][Nube publicada]

Configure los valores de protección de la manera siguiente:

1.  En la página de inicio rápido, haga clic en **Configurar protección para nubes VMM**.

	![Enable cloud protection][Enable cloud protection]

1.  En la pestaña **Elementos protegidos**, haga clic en la nube que desea configurar y vaya a la pestaña **Configuración**.
2.  En **Destino**, seleccione **Microsoft Azure**.
3.  En **Cuenta de almacenamiento**, seleccione la cuenta de Azure que desea usar para almacenar las máquinas virtuales de Azure.
4.  Establezca **Cifrar datos almacenados** en **Desactivado**. Este valor especifica que los datos de deben cifrar replicados entre el sitio local y Azure.
5.  En **Copiar frecuencia**, deje la configuración predeterminada. Este valor especifica la frecuencia con que se deben sincronizar los datos entre las ubicaciones de origen y de destino.
6.  En **Retener puntos de recuperación para**, deje la configuración predeterminada. Con un valor predeterminado de cero, el punto de recuperación más reciente para una máquina virtual es el único que se almacena en un servidor host de réplica.
7.  En **Frecuencia de las instantáneas coherentes con la aplicación**, deje la configuración predeterminada. Este valor especifica la frecuencia de creación de instantáneas. Las instantáneas utilizan el Servicio de instantáneas de volumen (VSS) para asegurarse de que las aplicaciones se encuentren en un estado coherente cuando se captura la instantánea. Si establece un valor, asegúrese de que sea inferior al número de puntos de recuperación adicionales que configure.

    ![Configuración de la nube][Configuración de la nube]

8.  En **Hora de inicio de la replicación**, especifique cuándo debe comenzar la replicación de los datos en Azure. Se usará la zona horaria del servidor host de Hyper-V. Se recomienda que programe la replicación inicial durante las horas de menos actividad.

    ![Cloud replication settings][Cloud replication settings]

Después de guardar la configuración, se creará un trabajo, que puede supervisar en la pestaña **Trabajos**. Todos los servidores host de Hyper-V de la nube de origen VMM se configurarán para la replicación. Tras guardar, puede modificar la configuración de la nube en la pestaña **Configurar**. Para modificar la ubicación de destino o el almacenamiento de destino, deberá eliminar la configuración de la nube y luego volver a configurar la nube. Tenga en cuenta que si cambia la cuenta de almacenamiento, el cambio solo se aplicará a las máquinas virtuales que tengan la protección habilitada después de que la cuenta de almacenamiento se ha modificado. Las máquinas virtuales existentes no se migrarán a la nueva cuenta de almacenamiento.

## <span id="networkmapping"></span></a>Paso 7: Configurar asignación de red

De manera opcional, puede habilitar la asignación de red para asignar redes VM de origen a redes virtuales Azure de destino. Si no crea asignaciones de red, solo las máquinas virtuales que conmuten por error en el mismo plan de recuperación se podrán conectar entre sí en Azure. Si crea una asignación de red, entonces todas las máquinas virtuales que conmuten por error en la misma red se pueden conectar entre sí, con independencia del plan de recuperación al que pertenezcan. Además, si se configura una puerta de enlace de red en la red Azure de destino, las máquinas virtuales se podrán conectar a las máquinas virtuales locales. Si desea configurar una asignación de red como parte de este tutorial, consulte el tema sobre la [configuración de la asignación de red][configuración de la asignación de red] en la guía de implementación.

## <span id="virtualmachines"></span></a>Paso 8: Habilitar protección para las máquinas virtuales

Una vez que los servidores, las nubes y las redes se configuran correctamente, puede habilitar la protección para las máquinas virtuales en la nube.

Antes de habilitar la protección para una máquina virtual, compruebe y actualice su configuración si es necesario. Por ejemplo, el sistema operativo invitado en la máquina virtual debe ser Windows Server 2008 o posterior, o Linux. La máquina virtual debe ser de la generación 1 exclusivamente. Para obtener una lista completa de los requisitos de Azure Site Recovery, consulte [Requisitos previos y compatibilidad][Requisitos previos y compatibilidad] en la guía de planificación.

En la consola de VMM, compruebe y actualice los valores de configuración. Puede modificar la configuración del sistema operativo de la máquina virtual en la página General de propiedades de la máquina virtual. Para actualizar la configuración de discos del sistema operativo, use la página de configuración del hardware.

![Modify virtual machine properties][Modify virtual machine properties]

![Modify virtual machine properties][2]

1.  Para habilitar la protección, en la pestaña **Máquinas virtuales** de la nube en la que se encuentra la máquina virtual, haga clic en **Habilitar protección** y luego seleccione **Agregar máquinas virtuales**.
2.  En la lista de máquinas virtuales de la nube, seleccione la que desea proteger.

	![Enable virtual machine protection][Enable virtual machine protection]

Tras habilitarse la protección, se crean dos trabajos. Se ejecuta el trabajo Enable Protection. Luego, después de que finaliza la replicación inicial, se ejecuta el trabajo de finalización de protección. La máquina virtual estará preparada para la conmutación por error solo después de que estos trabajos finalicen correctamente. Puede supervisar el progreso en la pestaña **Trabajos**.

![Virtual machine protection job][Virtual machine protection job]

## <span id="recoveryplans"></span></a>Paso 9: Configuración y ejecución de los planes de recuperación

Un plan de recuperación reúne las máquinas virtuales en grupos para que puedan conmutar por error como una sola unidad. Para crear un plan de recuperación, realice las siguientes tareas:

1.  En la pestaña **Planes de recuperación**, haga clic en **Crear**.
2.  En la página para especificar el nombre y el destino del plan de recuperación, seleccione el servidor VMM de origen y Azure como destino.

    ![Creación del plan de recuperación][Creación del plan de recuperación]

3.  En la página de selección de máquinas virtuales, seleccione máquinas virtuales para agregar al plan de recuperación. Solo se muestran las máquinas virtuales con la protección habilitada. Estas se agregan al grupo predeterminado del plan de recuperación (Grupo 1).
4.  Haga clic en la marca de verificación para crear el plan de recuperación.

    ![Máquinas virtuales del plan de recuperación][Máquinas virtuales del plan de recuperación]

### <span id="run"></span></a>Prueba de una conmutación por error

Los planes de recuperación se pueden ejecutar como parte de una conmutación por error de prueba proactiva o de una conmutación por error no planificada. En este tutorial se describe cómo ejecutar una conmutación por error de prueba de VMM a Azure con el objeto de comprobar si la estrategia de conmutación por error funciona según lo esperado. La conmutación por error de prueba simula su mecanismo de conmutación por error y recuperación en una red aislada. Tenga en cuenta lo siguiente:

-   Si después de la conmutación por error desea conectarse a la máquina virtual de Azure mediante Escritorio remoto, habilite Conexión a Escritorio remoto en la máquina virtual antes de ejecutar la prueba.
-   Después de la conmutación por error, usará una dirección IP pública para conectarse a la máquina virtual de Azure mediante Escritorio remoto. Si desea realizar esto, asegúrese de no tener ninguna directiva de dominio que impida que se conecte a una máquina virtual mediante una dirección pública.


<span id="runtest"></span></a>Ejecución de la conmutación por error

</h5>
Ejecute una conmutación por error de prueba para un plan de recuperación de la siguiente manera:


1.  Antes de ejecutar el plan de recuperación, puede validar la configuración de las máquinas virtuales en el plan. Para ello, en la página de propiedades de la nube, haga clic en la máquina virtual. En la página de propiedades de origen y destino para la conmutación por error, compruebe la configuración. En especial, compruebe que el tamaño sugerido de la máquina virtual de destino de Azure sea correcto y que la configuración de red sea precisa. Para obtener una lista completa de los requisitos previos para las máquinas virtuales, consulte [Requisitos previos y compatibilidad][3].

	![Virtual Machine Properties][Virtual Machine Properties]

1.  En la pestaña **Planes de recuperación**, seleccione el plan de recuperación necesario.
2.  Para iniciar la conmutación por error, haga clic en el botón **Conmutación por error de prueba**.
3.  En la página de confirmación de la conmutación por error de prueba, seleccione la red de Azure a la que se conectarán sus máquinas virtuales tras la conmutación por error. Opcionalmente, puede seleccionar **Sin red**. Cuando este valor está seleccionado, las máquinas virtuales no se conectarán a una red después de la conmutación por error.

	![Test Failover][Test Failover]

	Siga el progreso de los trabajos de conmutación por error de prueba en la pestaña **Jobs**. Una vez que se complete la conmutación por error de prueba, realice las siguientes tareas:

1.  Compruebe que las máquinas virtuales se inician correctamente en Azure.
2.  Haga clic en **Notas** para registrar y guardar cualquier observación asociada con la conmutación por error de prueba.
3.  Además de los detalles de la pestaña **Trabajos**, cuando ejecuta una conmutación por error de prueba para un plan de recuperación, el proceso se muestra en la página de detalles del plan de recuperación. Puede ver los pasos de conmutación por error y el estado, así como ver o crear notas para este proceso.




<span id="runtest"></span></a>Supervisión de la actividad



Puede usar la pestaña **Trabajos** y el **Panel** para ver y supervisar los principales trabajos que se realizan en el almacén de Azure Site Recovery, como la configuración de la protección para una nube, la habilitación y deshabilitación de la protección para una máquina virtual, la ejecución de una conmutación por error (planificada, no planificada o de prueba) y la realización de una conmutación por error no planificada.


En la pestaña **Trabajos**, puede ver los trabajos, profundizar en los detalles y los errores del trabajo, ejecutar consultas del trabajos para recuperar aquellos que coincidan con criterios específicos, exportar trabajos a Excel y reiniciar los trabajos con errores.

En el **Panel**, puede descargar las versiones más recientes de los archivos de instalación del proveedor y el agente, obtener información de configuración del almacén, ver el número de máquinas virtuales cuya protección se gestiona en el almacén, ver los trabajos recientes, administrar el certificado del almacén y volver a sincronizar las máquinas virtuales.

Para obtener más información sobre la interacción con los trabajos y el panel, consulte la [guía de operaciones y supervisión][guía de operaciones y supervisión].

## <span id="next"></span></a>Pasos siguientes

-   Para planificar e implementar Azure Site Recovery en un entorno completo de producción, consulte [Guía de planificación de Azure Site Recovery][Planificación de una implemetación de Azure Site Recovery] y [Guía de implementación de Azure Site Recovery][Guía de implementación de Azure Site Recovery].
-   Si tiene preguntas, visite el [foro de Servicios de recuperación de Azure][foro de Servicios de recuperación de Azure].

  [Introducción a Azure Site Recovery: protección de local a local]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Planificación de una implemetación de Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Implementación de Azure Site Recovery: del entorno local a la protección de Azure]: http://go.microsoft.com/fwlink/?LinkId=402679
  [Administración y supervisión de Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=378272
  [Azure Site Recovery: escenarios de error comunes y soluciones]: http://go.microsoft.com/fwlink/?LinkId=389879
  [foro de Servicios de recuperación de Azure]: http://go.microsoft.com/fwlink/?LinkId=313628
  [evaluación gratuita de Azure]: http://aka.ms/try-azure
  [detalles de precios de Azure Site Recovery Manager]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Introducción al almacenamiento de Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=398704
  [Requisitos previos y compatibilidad]: http://go.microsoft.com/fwlink/?LinkId=402602
  [Paso 1: Obtener y configurar los certificados]: #createcert
  [Paso 2: Crear un almacén]: #vault
  [Paso 3: Configurar el almacén]: #upload
  [Paso 4: Instalar la aplicación del proveedor]: #download
  [Paso 5: Instalar la aplicación del agente]: #agent
  [Paso 6: Configurar la protección de la nube]: #clouds
  [Paso 7: Configurar la asignación de red]: #NetworkMapping
  [Paso 8: Habilitar la protección para las máquinas virtuales]: #virtualmachines
  [Paso 9: Configurar y ejecutar planes de recuperación]: #recovery%20plans
  [Obtención de un certificado autofirmado para el tutorial]: #obtaincert
  [Exportación del certificado en formato .pfx]: #exportcert
  [Importación del certificado .pfx en el servidor VMM]: #importcert
  [Windows SDK]: http://go.microsoft.com/fwlink/?LinkId=378269
  [Portal de administración]: https://manage.windowsazure.com
  [Almacén nuevo]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Icono de inicio rápido]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Inicio rápido]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStart.png
  [Administrar certificado]: ./media/hyper-v-recovery-manager-configure-vault/SR_ManageCert.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_VaultKey.png
  [Download Provider File]: ./media/hyper-v-recovery-manager-configure-vault/SR_DownloadProviderFile.png
  [Descarga del agente]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderInstall1.png
  [Setup Complete]: ./media/hyper-v-recovery-manager-configure-vault/SR_InstallWiz.png
  [Internet Settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Registro de certificado]: ./media/hyper-v-recovery-manager-configure-vault/SR_CertReg1.png
  [Almacén de certificados]: ./media/hyper-v-recovery-manager-configure-vault/SR_CertReg2.png
  [PublishCloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMServerName.png
  [Install Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_HyperVAgent.png
  [Prerequisites Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png
  [Recovery Services Agent location]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentInstallSettings.png
  [Nube publicada]: ./media/hyper-v-recovery-manager-configure-vault/SR_Clouds.png
  [Enable cloud protection]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableCloudProtection.png
  [Configuración de la nube]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudConfigureE2A1.png
  [Cloud replication settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_PublishCloudSetup2.png
  [configuración de la asignación de red]: http://go.microsoft.com/fwlink/?LinkId=402533
  [Modify virtual machine properties]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMVMPropsGeneral.png
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMVMPropsHW.png
  [Enable virtual machine protection]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Virtual machine protection job]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Creación del plan de recuperación]: ./media/hyper-v-recovery-manager-configure-vault/SR_RecoveryPlan1.png
  [Máquinas virtuales del plan de recuperación]: ./media/hyper-v-recovery-manager-configure-vault/SR_RecoveryPlan2.png
  [3]: http://go.microsoft.com/fwlink/?LinkId=402676
  [Virtual Machine Properties]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMPropertiesE2A.png
  [Test Failover]: ./media/hyper-v-recovery-manager-configure-vault/SR_TestFailover.png
  [guía de operaciones y supervisión]: http://go.microsoft.com/fwlink/?LinkId=398534
  [Guía de implementación de Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321295
