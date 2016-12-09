---
title: Uso del complemento subordinado de Azure con Jenkins Continuous Integration | Microsoft Docs
description: Describe el uso del complemento subordinado de Azure con Jenkins Continuous Integration.
services: virtual-machines-linux
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 09233bd4-957d-41bf-bccc-9dd2355ed1bf
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: java
ms.topic: article
ms.date: 10/19/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8a40d829c65067dba90a212390e858b7995452b6


---
# <a name="how-to-use-the-azure-slave-plugin-with-jenkins-continuous-integration"></a>Uso del complemento subordinado de Azure con Jenkins Continuous Integration
El complemento subordinado de Azure para Jenkins facilita el aprovisionamiento de los nodos subordinados en Azure cuando se ejecutan compilaciones distribuidas, y admite la creación de lo siguiente:

* Elementos subordinados de Windows en la nube de Azure mediante SSH y el protocolo de inicio de red de Java (JNLP)
  
  * Para iniciar una imagen de Windows a través de SSH, es necesario haberla configurado previamente con SSH.
  * Para obtener información acerca de cómo preparar una imagen de Windows personalizada, vea [How to capture a Linux virtual machine to use as a Resource Manager template] (Captura de una máquina virtual con Windows en el modelo de implementación de Resource Manager)[windows-image-capture].
* Elementos subordinados de Linux en la nube de Azure mediante SSH
  
  * Para obtener información acerca de cómo preparar una imagen de Linux personalizada, vea [Captura de una máquina virtual con Linux para usarla como plantilla de Resource Manager][linux-image-capture].

### <a name="prerequisites"></a>Requisitos previos
Antes de comenzar con los pasos descritos en este artículo, tendrá que registrar y autorizar la aplicación cliente y, a continuación, recuperar el identificador y el secreto de cliente que se enviarán a Azure Active Directory durante la autenticación. Para obtener más información sobre estos requisitos previos, consulte los siguientes artículos:

* [Integración de aplicaciones con Azure Active Directory][integrate-apps-with-AAD]
* [Register a Client App](Registro de una aplicación cliente)[register-client-app]

Además, para completar los pasos descritos en la sección [Creación de un trabajo de Jenkins que se ejecute en un nodo subordinado en Azure](#create-jenkins-project) de este artículo, debe tener una configuración de proyecto en GitHub.

<a name="install-azure-slave-plugin"></a>

## <a name="install-the-azure-slave-plugin"></a>Instalación del complemento subordinado de Azure
1. En el panel de Jenkins, haga clic en **Manage Jenkins**(Administrar Jenkins).
   
    ![Administración de Jenkins][jenkins-dashboard-manage]
2. En la página **Manage Jenkins** (Administrar Jenkins), haga clic en **Manage Plugins** (Administrar complementos).
   
    ![Administración de complementos][jenkins-manage-plugins]
3. Haga clic en la pestaña **Available** (Disponible) y escriba "Azure" como filtro; a continuación, seleccione **Azure Slave Plugin** (Complemento subordinado de Azure). 
   
    ![Complemento subordinado de Azure][search-plugins]
   
    Si opta por desplazarse por la lista de complementos disponibles, encontrará el complemento subordinado de Azure en la sección **Cluster Management and Distributed Build** (Administración de clústeres y compilación distribuida).
4. Haga clic en **Install without restart** (Instalar sin reiniciar) o **Download now and install after restart** (Descargar ahora e instalar después de reiniciar).
   
    ![Instalación del complemento][install-plugin]

Ahora que está instalado el complemento, los siguientes pasos son configurarlo con el perfil de suscripción de Azure y crear una plantilla que se usará en la creación de la máquina virtual para el nodo subordinado.

## <a name="configure-the-azure-slave-plugin-to-use-your-subscription-profile"></a>Configuración del complemento subordinado de Azure para usar el perfil de suscripción
Un perfil de suscripción, también conocido como configuración de publicación, es un archivo XML que contiene credenciales seguras y alguna información adicional que necesitará para trabajar con Azure en el entorno de desarrollo. Para configurar el complemento subordinado de Azure, necesitará:

* Su id. de suscripción
* Un certificado de administración para la suscripción

Estos pueden encontrarse en su [perfil de suscripción], que puede ser similar al siguiente ejemplo:

        <?xml version="1.0" encoding="utf-8"?>
        <PublishData>
            <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">
                <Subscription
                    ServiceManagementUrl="https://management.core.windows.net"
                    Id="<Subscription ID value>"
                    Name="Pay-As-You-Go"
                    ManagementCertificate="<Management certificate value>" />
            </PublishProfile>
        </PublishData>

Una vez que tenga el perfil de suscripción, siga estos pasos para configurar el complemento subordinado de Azure:

1. En el panel de Jenkins, haga clic en **Manage Jenkins**(Administrar Jenkins).
   
    ![Administración de Jenkins][jenkins-dashboard-manage]
2. Haga clic en **Configure System**(Configurar sistema).
   
    ![Configuración del sistema][jenkins-configure-system]
3. Desplácese hacia abajo en la página para encontrar la sección **Cloud** (Nube).
4. Haga clic en **Add a new cloud** (Agregar una nube nueva) y, a continuación, seleccione **Microsoft Azure**.
   
    ![Incorporación de una nube nueva][cloud-section]
   
    Se mostrarán los campos donde debe especificar los detalles de suscripción.
   
    ![Configuración de la suscripción][subscription-configuration]
5. Copie los valores de certificado de administración e id. de suscripción del perfil de suscripción y péguelos en los campos correspondientes.
   
    Al copiar el certificado de administración y el id. de suscripción, no incluya las comillas que delimitan los valores.
6. Haga clic en **Verify Configuration** (Comprobar configuración) para comprobar si los parámetros especificados son válidos y, a continuación, haga clic en **Save** (Guardar).

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plugin"></a>Configuración de una plantilla de máquina virtual para el complemento subordinado de Azure
En esta sección agregará una plantilla de máquina virtual que define los parámetros que usará el complemento subordinado de Azure para crear un nodo subordinado en Azure. En los pasos siguientes, creará una plantilla para una máquina virtual de Ubuntu.

1. En el panel de Jenkins, haga clic en **Manage Jenkins**(Administrar Jenkins).
   
    ![Administración de Jenkins][jenkins-dashboard-manage]
2. Haga clic en **Configure System**(Configurar sistema).
   
    ![Configuración del sistema][jenkins-configure-system]
3. Desplácese hacia abajo en la página para encontrar la sección **Cloud** (Nube).
4. En la sección **Cloud** (Nube), busque **Add Azure Virtual Machine Template** (Agregar plantilla de máquina virtual de Azure) para la nube que agregó en la sección anterior y, a continuación, haga clic en **Add** (Agregar).
   
    ![Incorporación de plantilla de máquina virtual de Azure][add-vm-template]
   
    Jenkins mostrará un formulario que contiene los campos en los que se especifican los detalles acerca de la plantilla que está creando.
   
    ![Configuración general en blanco][blank-general-configuration]
5. Escriba la información de **configuración general** para la plantilla:
   
   1. En el cuadro **Name** (Nombre), escriba un nombre para la nueva plantilla; es solo para su uso, no se utiliza en el aprovisionamiento de una máquina virtual.
   2. En el cuadro **Description** (Descripción), escriba el texto que describe la plantilla que está creando. Esta información es solo para registros y no se usa en el aprovisionamiento de una máquina virtual.
   3. El campo **Labels** (Etiquetas) se usa para identificar la plantilla que está creando y posteriormente para hacer referencia a la plantilla al crear un trabajo de Jenkins. En nuestro caso, escriba "**linux**" en este cuadro.
   4. En la lista **Region** (Región), haga clic en la región de Azure donde se creará la máquina virtual.
   5. En la lista **Virtual Machine Size** (Tamaño de máquina virtual), elija el tamaño adecuado en el menú desplegable.
   6. En el cuadro **Storage Account Name** (Nombre de cuenta de almacenamiento), especifique una cuenta de almacenamiento donde se creará la máquina virtual. Si desea que Jenkins cree una cuenta de almacenamiento mediante el valor predeterminado "jenkinsarmst", puede dejar este cuadro en blanco.
   7. **Retention Time** (Tiempo de retención) especifica el número de minutos antes de que Jenkins elimine un subordinado inactivo; deje este campo en el valor predeterminado de 60.
      
      * También puede optar por apagar el subordinado en lugar de eliminarlo cuando está inactivo. Para ello, seleccione la casilla **Shutdown Only (Do Not Delete) After Retention Time** (Solo apagar [no eliminar] después del tiempo de retención).
      * También puede especificar 0 si no desea que los elementos subordinados inactivos se eliminen automáticamente.
   8. En la lista **Usage** (Uso), puede elegir entre las siguientes opciones:
      
      * **Utilize this node as much as possible** (Usar este nodo tanto como sea posible): Jenkins puede ejecutar cualquier trabajo en el elemento subordinado siempre que esté disponible.
      * **Leave this node for tied jobs only** (Dejar este nodo solo para los trabajos relacionados): Jenkins solo creará un proyecto (o trabajo) en este nodo cuando ese proyecto se relacione específicamente con ese nodo, lo que permite reservar un elemento subordinado para determinados tipos de trabajos.
      
      Por ahora, haga clic en **Utilize this node as much as possible** (Usar este nodo tanto como sea posible).
      
      En este punto, su formulario sería parecido al siguiente:
      
      ![Configuración de plantilla de general][general-template-config]
6. Especifique la **configuración de imagen** para la plantilla:
   
   1. Para la familia de imágenes, tiene dos opciones:
      
      * **Custom User Image** (Imagen de usuario personalizada): esta opción requiere que proporcione la dirección URL a una imagen personalizada que se encuentra en la misma cuenta de almacenamiento donde va a crear nodos subordinados.
      * **Image Reference** (Referencia de imagen): esta opción requiere que especifique los valores *Publisher* (Publicador), *Offer* (Oferta) y *SKU* para una imagen, que encontrará en [Catálogo de Máquinas virtuales][azure-images].
        
        Para este tutorial, elija **Image Reference** y utilice los siguientes valores:
      * **Image Publisher** (Publicador de imagen): Canonical
      * **Image Offer** (Oferta de imagen): UbuntuServer
      * **Image Sku** (SKU de imagen): 14.04.4-LTS
   2. Para la lista **Launch Method** (Método de inicio), tiene dos opciones: **SSH** o **JNLP**. Para este tutorial, elija **SSH**. Sin embargo, existen algunas advertencias que debe tener en cuenta al elegir el método de inicio:
      
      * Los elementos subordinados de Linux solo pueden iniciarse mediante SSH.
      * Los elementos subordinados de Windows puede usar SSH o JNLP; no obstante, si se utiliza SSH, la imagen debe estar preparada de manera personalizada con un servidor SSH.
        
        Si utiliza JNLP como método de inicio, debe asegurarse de que se configuran los siguientes elementos:
      * Es necesario que el elemento subordinado de Azure llegue a la dirección URL de Jenkins, por lo que necesita configurar los firewalls en consecuencia. Puede encontrar la dirección URL de Jenkins haciendo clic en **Manage Jenkins** (Administrar Jenkins) luego en **Configure System** (Configurar sistema) y buscando la sección **Jenkins Location** (Ubicación de Jenkins).
   3. Es necesario que el elemento subordinado de Azure que se inicia mediante JNLP alcance el puerto TCP de Jenkins; por esta razón, se recomienda que utilice un puerto TCP fijo para que pueda configurar los firewalls en consecuencia. Para especificar el puerto TCP de Jenkins, haga clic en **Manage Jenkins** (Administrar Jenkins), luego en **Configure Global Security** (Configurar seguridad global), active las opciones para **Enable security** (Habilitar seguridad) y usar un puerto de tipo **Fixed** (Fijo) y, por último, especifique el puerto que se va a utilizar.
   4. Para el **Init Script** (Script de inicialización), debe proporcionar un script de inicialización que se ejecutará después de crear la máquina virtual, teniendo en cuenta lo siguiente:
      
      * Como mínimo, el script debe instalar Java.
      * Si usa JNLP, el script de inicialización debe estar escrito en PowerShell.
      * Si se espera que el script de inicialización tarde mucho tiempo en ejecutarse (por ejemplo, al instalar una serie de gran tamaño de paquetes), se recomienda que prepare una imagen personalizada con todo el software necesario instalado previamente en lugar de proceder a la instalación a través del script de inicialización.
        
        Para este tutorial, copie el siguiente script y péguelo en el cuadro **Init script** (Script de inicialización):
        
              # Install Java
              sudo apt-get -y update
              sudo apt-get install -y openjdk-7-jdk
              sudo apt-get -y update --fix-missing
              sudo apt-get install -y openjdk-7-jdk
              # Install git
              sudo apt-get install -y git
              # Install ant
              sudo apt-get install -y ant
              sudo apt-get -y update --fix-missing
              sudo apt-get install -y ant
        
        En el ejemplo anterior, el script de inicialización instala *Java*, *Git* y *ant*.
   5. En los cuadros **Username** (Nombre de usuario) y **Password** (Contraseña), escriba los valores preferidos para la cuenta de administrador que se creará en la máquina virtual.
      
      En este punto, su formulario sería parecido al siguiente:
      
      ![Configuración de imagen de Jenkins][jenkins-image-configuration]
7. Haga clic en **Verify Template** (Comprobar plantilla) para comprobar si los parámetros especificados son válidos y, a continuación, haga clic en **Save** (Guardar).
   
    ![Guardar de plantilla de Jenkins][jenkins-save-template]

<a name="create-jenkins-project"></a>

## <a name="create-a-jenkins-job-which-runs-on-a-slave-node-on-azure"></a>Creación de un trabajo de Jenkins que se ejecute en un nodo subordinado en Azure
En esta sección, creará una tarea de Jenkins que se ejecutará en un nodo subordinado en Azure. Para completar estos pasos, debe tener un proyecto en GitHub.

1. En el panel de Jenkins, haga clic en **New Item**(Nuevo elemento).
   
    ![Nuevo elemento del panel de Jenkins][jenkins-dashboard-new-item]
2. Escriba un nombre para la tarea que está creando, haga clic en **Freestyle project** (Proyecto de estilo libre) para el tipo de proyecto y, a continuación, haga clic en **OK** (Aceptar).
   
    ![Crear nuevo elemento de Jenkins][jenkins-create-new-item]
3. En la página de configuración de la tarea, seleccione **Restrict where this project can be run** (Restringir dónde se puede ejecutar este proyecto) y escriba "**linux**" en el cuadro **Label Expression** (Expresión de etiqueta); este valor coincide con la etiqueta de la plantilla del elemento subordinado que creó en la sección anterior.
   
    ![Restringir nuevo elemento de Jenkins][jenkins-new-item-restrict]
4. En la sección **Build** (Compilar), haga clic en **Add build step** (Agregar paso de compilación) y seleccione **Execute shell** (Ejecutar shell).
   
    ![Nueva compilación de elemento de Jenkins][jenkins-new-item-build]
5. Edite el siguiente script; para ello, sustituya **(el nombre de la cuenta de github)**, **(el nombre del proyecto)** y **(el directorio del proyecto)** por los valores adecuados y pegue el script editado en el área de texto que aparece.
   
            # Clone from git repo
            currentDir="$PWD"
            if [ -e (your project directory) ]; then
                cd (your project directory)
                git pull origin master
            else
                git clone https://github.com/(your GitHub account name)/(your project name).git
            fi
            # change directory to project
            cd $currentDir/(your project directory)
            #Execute build task
            ant
   
    El formulario debe tener un aspecto similar al ejemplo siguiente:
   
    ![Nuevo script de elemento de Jenkins][jenkins-new-item-script]
6. Cuando se hayan completado todos los pasos de configuración, haga clic en **Save** (Guardar).
   
    ![Guardar nuevo elemento de Jenkins][jenkins-new-item-save]
7. En el panel de Jenkins, haga clic en la flecha de lista desplegable junto al proyecto recién creado y haga clic en **Build now** (Compilar ahora).
   
    ![Compilar ahora de Jenkins][jenkins-build-now]

Jenkins creará un nodo subordinado con la plantilla que creó en la sección anterior y ejecutará el script especificado en el paso de compilación de esta tarea.

<a name="image-template-considerations"></a>

## <a name="considerations-when-working-with-image-templates"></a>Consideraciones al trabajar con plantillas de imagen
Las secciones siguientes contienen información útil para configurar varias plantillas.

### <a name="when-you-are-using-ubuntu-image-templates"></a>Si se utilizan plantillas de imagen de Ubuntu
* Si se espera que el script de inicialización tarde mucho tiempo en ejecutarse (por ejemplo, al instalar una serie de gran tamaño de paquetes), se recomienda que prepare una imagen de Ubuntu personalizada con todo el software necesario instalado previamente en lugar de proceder a la instalación a través del script de inicialización.
* Como mínimo, el script debe instalar Java. Como se muestra en los ejemplos anteriores, puede utilizar el siguiente script para instalar Java, Git y ant.
  
        # Install Java
        sudo apt-get -y update
        sudo apt-get install -y openjdk-7-jdk
        sudo apt-get -y update --fix-missing
        sudo apt-get install -y openjdk-7-jdk
        # Install git
        sudo apt-get install -y git
        # Install ant
        sudo apt-get install -y ant
        sudo apt-get -y update --fix-missing
        sudo apt-get install -y ant

### <a name="when-you-are-using-windows-image-templates-and-jnlp-launch-method"></a>Si se utilizan plantillas de imagen de Windows y el método de inicio de JNLP
* Si el patrón Jenkins no tiene configurada la seguridad:
  
  * Deje el campo **Init Script** (Script de inicialización) en blanco para que el script predeterminado se ejecute en el elemento subordinado.
* Si el maestro de Jenkins tiene configurada la seguridad: 
  
  * Copie el script de [Windows Slaves Setup] (Programa de instalación de elementos subordinados de Windows)[windows-slaves-setup] y modifíquelo con sus credenciales de Jenkins.
  * Como mínimo, el script debe modificarse con un identificador de usuario de Jenkins y el token de API. Para recuperar el token de API de un usuario, siga estos pasos:
    
    1. En el panel de Jenkins, haga clic en **People** (Personas).
    2. Haga clic en la cuenta de usuario correspondiente.
    3. Haga clic en **Configure** (Configurar) en el menú de la izquierda.
    4. Haga clic en **Show API Token...** (Mostrar token de API...).

<a name="see-also"></a>

## <a name="see-also"></a>Otras referencias
Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure].

Para más información sobre el complemento subordinado de Jenkins, consulte el proyecto [Azure Slave Plugin] en GitHub.

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[Azure Slave Plugin]: https://github.com/jenkinsci/azure-slave-plugin/
[perfil de suscripción]: http://go.microsoft.com/fwlink/?LinkID=396395
[azure-images]: https://azure.microsoft.com/marketplace/virtual-machines/all/
[integrate-apps-with-AAD]: http://msdn.microsoft.com/library/azure/dn132599.aspx
[register-client-app]: http://msdn.microsoft.com/dn877542.aspx
[windows-slaves-setup]: https://gist.github.com/snallami/5aa9ea2c57836a3b3635

<!-- IMG List -->

[jenkins-dashboard-manage]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-dashboard-manage.png
[jenkins-manage-plugins]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-manage-plugins.png
[jenkins-configure-system]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-configure-system.png
[jenkins-dashboard-new-item]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-dashboard-new-item.png
[search-plugins]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/search-for-azure-plugin.png
[install-plugin]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/install-plugin.png
[jenkins-create-new-item]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-create-new-item.png
[cloud-section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add-vm-template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank-general-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[general-template-config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[jenkins-new-item-restrict]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-restrict.png
[jenkins-new-item-build]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-build.png
[jenkins-new-item-script]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-script.png
[jenkins-new-item-save]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-save.png
[jenkins-build-now]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-build-now.png
[jenkins-image-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-image-configuration.png
[jenkins-save-template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-save-template.png
[windows-image-capture]: ./virtual-machines-windows-classic-capture-image.md
[linux-image-capture]: ./virtual-machines-linux-capture-image.md



<!--HONumber=Nov16_HO3-->


