<properties linkid="dev-java-vm-application-server" urlDisplayName="Tomcat on Virtual Machine" pageTitle="Tomcat on a virtual machine - Azure tutorial" metaKeywords="Azure vm, creating vm Tomcat, configuring vm Tomcat" description="Learn how to create a Windows Virtual machine and configure the machine to run a Apache Tomcat application server." metaCanonical="" services="virtual-machines" documentationCenter="Java" title="How to run a Java application server on a virtual machine" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-windows" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Ejecución de un servidor de aplicaciones Java en una máquina virtual

Con Azure, puede utilizar una máquina virtual para proporcionar funciones de servidor. Por ejemplo, una máquina virtual que se ejecuta en Azure se puede configurar para hospedar un servidor de aplicaciones Java, como Apache Tomcat. Al finalizar esta guía, adquirirá una comprensión de cómo crear una máquina virtual que se ejecute en Azure y configurarla para ejecutar un servidor de aplicaciones Java.

Aprenderá a:

-   Crear una máquina virtual que tenga ya instalado un JDK.
-   Iniciar sesión de manera remota en la máquina virtual
-   Instalar un servidor de aplicaciones Java en la máquina virtual.
-   Crear un extremo para la máquina virtual.
-   Abrir un puerto en el firewall para el servidor de aplicaciones.

En este tutorial, se va a instalar un servidor de aplicaciones Apache Tomcat en una máquina virtual. La instalación completada dará como resultado una instalación de Tomcat como la siguiente.

![Máquina virtual que ejecuta Apache Tomcat][Máquina virtual que ejecuta Apache Tomcat]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Para crear una máquina virtual

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].
2.  Haga clic sucesivamente en **New**, **Proceso**, **Máquina virtual** y, a continuación, en **From Gallery**.
3.  En el cuadro de diálogo **Selección de imágenes de máquina virtual**, seleccione **JDK 7 Windows Server 2012**.
    Observe que **JDK 6 Windows Server 2012** está disponible si tiene aplicaciones heredadas que aún no están preparadas para ejecutarse en JDK 7.
4.  Haga clic en **Next**.
5.  En el cuadro de diálogo **Virtual machine configuration**:

    1.  Especifique un nombre para la máquina virtual.
    2.  Especifique el tamaño que se va a utilizar para la máquina virtual.
    3.  Escriba un nombre para el administrador en el campo **User Name**. Recuerde este nombre y la contraseña que va a escribir a continuación ya que los utilizará cuando inicie sesión de forma remota en la máquina virtual.
    4.  Escriba una contraseña en el campo **New password** y confírmela en el campo **Confirm**. Esta es la contraseña de la cuenta de administrador.
    5.  Haga clic en **Next**.

6.  En el cuadro de diálogo siguiente **Virtual machine configuration**:

    1.  En **Servicio en la nube**, utilice la opción predeterminada **Create a new cloud service**.
    2.  El valor de **Cloud service DNS name** debe ser único en cloudapp.net. Si es necesario, modifique este valor para que Azure indique que es único.
    3.  Especifique una región, un grupo de afinidad o una red virtual. En este tutorial, especifique una región como **Oeste de EE. UU**.
    4.  En **Cuenta de almacenamiento**, seleccione **Use an Automatically Generated Storage Account**.
    5.  En **Conjunto de disponibilidad**, seleccione **(None)**.
    6.  Haga clic en **Next**.

7.  En el cuadro de diálogo final **Virtual machine configuration**:

    1.  Acepte las entradas de extremo predeterminadas.
    2.  Haga clic en **Complete**.

## Para iniciar sesión de manera remota en la máquina virtual

1.  Inicie sesión en el [Portal de administración][Portal de administración de Azure].
2.  Haga clic en **Máquinas virtuales**.
3.  Haga clic en el nombre de la máquina virtual en la que desea iniciar sesión.
4.  Haga clic en **Connect**.
5.  Siga las indicaciones, según sea necesario, para conectarse a la máquina virtual. Cuando se le pida el nombre y la contraseña del administrador, utilice los valores que proporcionó cuando creó la máquina virtual.

## Para instalar un servidor de aplicaciones Java en la máquina virtual

Puede copiar un servidor de aplicaciones Java en la máquina virtual o instalarlo a través de un instalador.

En este tutorial, se instalará Tomcat.

1.  Mientras tiene una sesión iniciada en la máquina virtual, abra una sesión de explorador a <http://tomcat.apache.org/download-70.cgi>.
2.  Haga doble clic en el vínculo de **32-bit/64-bit Windows Service Installer**. Mediante esta técnica, Tomcat se instalará como servicio de Windows.
3.  Cuando se le pida, elija ejecutar el instalador.
4.  En el asistente **Apache Tomcat Setup**, siga las indicaciones para instalar Tomcat. En este tutorial, es adecuado aceptar los valores predeterminados. Cuando llegue al cuadro de diálogo **Completing the Apache Tomcat Setup Wizard**, puede activar opcionalmente **Run Apache Tomcat** para que Tomcat se inicie ahora. Haga clic en **Finish** para finalizar el proceso de instalación de Tomcat.

## Para iniciar Tomcat

Si no ha elegido ejecutar Tomcat en el cuadro de diálogo **Completing the Apache Tomcat Setup Wizard**, abra un símbolo del sistema en la máquina virtual para iniciarlo y ejecute **net start Tomcat7**.

Ahora, si ejecuta el explorador de la máquina virtual y abre <http://localhost:8080>, verá que Tomcat se está ejecutando.

Para ver que Tomcat se ejecuta desde máquinas externas, deberá crear un extremo y abrir un puerto.

## Para crear un extremo para la máquina virtual

1.  Inicie sesión en el [Portal de administración][Portal de administración de Azure].
2.  Haga clic en **Máquinas virtuales**.
3.  Haga clic en el nombre de la máquina virtual que ejecuta el servidor de aplicaciones Java.
4.  Haga clic en **Endpoints**.
5.  Haga clic en **Add**.
6.  En el cuadro de diálogo **Add endpoint**, asegúrese de que la casilla **Add standalone endpoint** esté activada y, a continuación, haga clic en **Next**.
7.  En el cuadro de diálogo **New endpoint details**:

    1.  Especifique un nombre para el extremo; por ejemplo, **HttpIn**.
    2.  Especifique **TCP** para el protocolo.
    3.  Especifique **80** para el puerto público.
    4.  Especifique **8080** para el puerto privado.
    5.  Haga clic en el botón **Complete** para cerrar el cuadro de diálogo. Ahora se creará el extremo.

## Para abrir un puerto en el firewall para la máquina virtual

1.  Inicie sesión en la máquina virtual.
2.  Haga clic en **inicio de Windows**.
3.  Haga clic en **Panel de control**.
4.  Haga clic en **Sistema y seguridad**, **Firewall de Windows** y, a continuación, en **Configuración avanzada**.
5.  Haga clic en **Reglas de entrada** y, a continuación, en **Nueva regla**.

![Nueva regla de entrada][Nueva regla de entrada]

1.  Para la nueva regla, seleccione **Puerto** en **Tipo de regla** y, a continuación, haga clic en **Siguiente**.

![Puerto de nueva regla de entrada][Puerto de nueva regla de entrada]

1.  Seleccione **TCP** para el protocolo y especifique **8080** para el puerto; a continuación, haga clic en **Siguiente**.

![Nueva regla de entrada][1]

1.  Elija **Permitir la conexión** y haga clic en **Siguiente**.

![Acción de nueva regla de entrada][Acción de nueva regla de entrada]

1.  Asegúrese de que las casillas **Dominio**, **Privado** y **Público** estén activadas para el perfil y, a continuación, haga clic en **Siguiente**.

![Perfil de nueva regla de entrada][Perfil de nueva regla de entrada]

1.  Especifique un nombre para la regla, por ejemplo **HttpIn** (sin embargo, no es necesario que el nombre de la regla coincida con el nombre del extremo) y haga clic en **Finalizar**.

![Nombre de la nueva regla de entrada][Nombre de la nueva regla de entrada]

En este momento, el sitio web de Tomcat se debería ver desde un explorador externo, usando una URL con el formato **http://*su\_nombre\_DNS*.cloudapp.net**, donde ***su\_nombre\_DNS*** es el nombre DNS que especificó cuando creó la máquina virtual.

## Consideraciones acerca del ciclo de vida de las aplicaciones

-   Puede crear su propio archivo web de aplicación (WAR) y agregarlo a la carpeta **webapps**. Por ejemplo, cree un proyecto web dinámico JSP (Java Service Page) básico y expórtelo como un archivo WAR; copie el archivo WAR en la carpeta **webapps** de Apache Tomcat en la máquina virtual y, a continuación, ejecútelo en un explorador.
-   De forma predeterminada, cuando el servicio Tomcat está instalado, estará configurado para iniciarse manualmente. Puede cambiarlo para que se inicie automáticamente mediante el complemento Servicios. Para iniciar el complemento Servicios, haga clic en **inicio de Windows**, **Herramientas administrativas** y, a continuación, en **Servicios**. Para configurar Tomcat para que se inicie automáticamente, haga doble clic en el servicio **Apache Tomcat** en el complemento Servicios y configure **Tipo de inicio** como **Automático**, según se muestra a continuación.

    ![Configurar un servicio para que se inicie automáticamente][Configurar un servicio para que se inicie automáticamente]

    La ventaja de que Tomcat se inicie automáticamente es que se iniciará de nuevo si se inicia la máquina virtual (por ejemplo, después de instalar actualizaciones de software que requieren un reinicio).

## Pasos siguientes

-   Para obtener más información acerca de otros servicios, como Almacenamiento de Azure, bus de servicio, Base de datos SQL, etc., que desee incluir con sus aplicaciones Java, consulte la información disponible en [http://www.windowsazure.com/es-es/develop/java/][http://www.windowsazure.com/es-es/develop/java/].

  [Máquina virtual que ejecuta Apache Tomcat]: ./media/virtual-machines-java-run-tomcat-application-server/WA_VirtualMachineRunningApacheTomcat.png
  [Portal de administración de Azure]: https://manage.windowsazure.com
  [Nueva regla de entrada]: ./media/virtual-machines-java-run-tomcat-application-server/NewInboundRule.png
  [Puerto de nueva regla de entrada]: ./media/virtual-machines-java-run-tomcat-application-server/NewRulePort.png
  [1]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProtocol.png
  [Acción de nueva regla de entrada]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleAction.png
  [Perfil de nueva regla de entrada]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProfile.png
  [Nombre de la nueva regla de entrada]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleName.png
  [Configurar un servicio para que se inicie automáticamente]: ./media/virtual-machines-java-run-tomcat-application-server/WA_TomcatServiceAutomaticStart.png
  [http://www.windowsazure.com/es-es/develop/java/]: http://www.windowsazure.com/es-es/develop/java/
