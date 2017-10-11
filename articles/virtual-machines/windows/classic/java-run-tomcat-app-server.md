---
title: "Ejecución de un servidor de aplicaciones Java en una máquina virtual de Azure clásico | Microsoft Docs"
description: "En este tutorial se usan los recursos creados con el modelo de implementación clásico, y se muestra cómo crear una máquina virtual Windows y configurarla para ejecutar el servidor de aplicaciones de Apache Tomcat."
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: d627aa09-f7d6-4239-8110-f8fc5111b939
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 03/16/2017
ms.author: robmcm
ms.openlocfilehash: 6e02f42613808bcb13c0057e9f8fcc1c02273e77
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>Ejecución de un servidor de aplicaciones Java en una máquina virtual creada con el modelo de implementación clásico
> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Para obtener una plantilla de Resource Manager para implementar una aplicación web con Java 8 y Tomcat, consulte [aquí](https://azure.microsoft.com/documentation/templates/201-web-app-java-tomcat/).

Con Azure, puede utilizar una máquina virtual para proporcionar funciones de servidor. Por ejemplo, una máquina virtual que se ejecuta en Azure se puede configurar para hospedar un servidor de aplicaciones Java, como Apache Tomcat.

Después de finalizar esta guía, adquirirá una comprensión de cómo crear una máquina virtual que se ejecute en Azure y configurarla para ejecutar un servidor de aplicaciones Java. Aprenderá a realizar las siguientes tareas:

* Crear una máquina virtual que tenga un kit de desarrollo de Java (JDK) ya instalado.
* Iniciar sesión de manera remota en la máquina virtual.
* Instalar un servidor de aplicaciones Java, Apache Tomcat, en la máquina virtual.
* Crear un extremo para la máquina virtual.
* Abrir un puerto en el firewall para el servidor de aplicaciones.

Los resultados de la instalación completos cuando se ejecuta Tomcat en una máquina virtual.

![Máquina virtual que ejecuta Apache Tomcat][virtual_machine_tomcat]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Para crear una máquina virtual
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).  
2. Haga clic en **Nuevo**, en **Proceso** y, a continuación en **Ver todo** en **Aplicaciones destacadas**.
3. Haga clic en **JDK** y en **JDK 8** en el panel **JDK**.  
   Las imágenes de máquina virtual que son compatibles con **JDK 6** y **JDK 7** se encuentran disponibles si dispone de aplicaciones heredadas que no están preparadas para ejecutarse en JDK 8.
4. En el panel de JDK 8, seleccione **Clásico** y, a continuación, haga clic en **Crear**.
5. En la hoja **Básico**:
   1. Especifique un nombre para la máquina virtual.
   2. Escriba un nombre para el administrador en el campo **Nombre de usuario** . Recuerde este nombre y la contraseña asociada que aparece en el siguiente campo. Las necesitará cuando inicie sesión de forma remota en la máquina virtual.
   3. Escriba una contraseña en el campo **New password** (Contraseña nueva) y confírmela en el campo **Confirm password** (Confirmar contraseña). Esta es la contraseña de la cuenta de administrador.
   4. Seleccione la **suscripción**adecuada.
   5. Para el **Grupo de recursos**, haga clic en **Crear nuevo** y especifique el nombre de un nuevo grupo de recursos. También puede hacer clic en **Usar existente** y seleccionar uno de los grupos de recursos disponibles.
   6. Seleccione una ubicación en la que esté la máquina virtual, como **Centro-sur de EE. UU**.
6. Haga clic en **Siguiente**.
7. En la hoja **Tamaño de la imagen de máquina virtual**, seleccione **Estándar A1** o cualquier otra imagen apropiada.
8. Haga clic en **Seleccionar**.

9. En la hoja **Configuración**, haga clic en **Aceptar**. Puede usar los valores predeterminados proporcionados por Azure.  
10. En la hoja **Resumen**, haga clic en **Aceptar**.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>Para iniciar sesión de manera remota en la máquina virtual
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Haga clic en **Virtual Machines (clásico)**. Si es necesario, haga clic en **Más servicios** en la esquina inferior izquierda en las categorías de servicio. La entrada **Virtual Machines (clásico)** aparece en el grupo **Proceso**.
3. Haga clic en el nombre de la máquina virtual en la que desea iniciar sesión.
4. Una vez que la máquina virtual se haya iniciado, aparecerá un menú en la parte superior del panel que permitirá las conexiones.
5. Haga clic en **Conectar**.
6. Siga las indicaciones, según sea necesario, para conectarse a la máquina virtual. Normalmente, debe guardar o abrir el archivo .rdp que contiene los detalles de conexión. Es posible que tenga que copiar el valor de url:port de la parte final de la primera línea del archivo .rdp y pegarlo en una aplicación remota de inicio de sesión.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>Para instalar un servidor de aplicaciones Java en la máquina virtual
Puede copiar un servidor de aplicaciones Java en la máquina virtual o instalarlo a través de un instalador.

Este tutorial usa Tomcat como servidor de la aplicación de Java que instalar.

1. Cuando haya iniciado sesión en la máquina virtual, abra una sesión del explorador en [Apache Tomcat](http://tomcat.apache.org/download-80.cgi).
2. Haga doble clic en el vínculo del **instalador del servicio de Windows de 32 bits y 64 bits**. Mediante esta técnica, Tomcat se instala como servicio de Windows.
3. Cuando se le pida, elija ejecutar el instalador.
4. En el asistente para la **instalación de Apache Tomcat** , siga las indicaciones para instalar Tomcat. En este tutorial, es adecuado aceptar los valores predeterminados. Cuando llegue al cuadro de diálogo **Completing the Apache Tomcat Setup Wizard** (Finalización del asistente para la instalación de Apache Tomcat), si lo desea, puede activar **Run Apache Tomcat** (Ejecutar Apache Tomcat) para iniciar Tomcat ahora. Haga clic en **Finalizar** para finalizar el proceso de instalación de Tomcat.

## <a name="to-start-tomcat"></a>Para iniciar Tomcat

Puede iniciar manualmente Tomcat abriendo un símbolo del sistema en la máquina virtual y ejecutando el comando **net&nbsp;start&nbsp;Tomcat8**.

Una vez que se está ejecutando Tomcat, puede tener acceso a Tomcat escribiendo la dirección URL <http://localhost:8080> en el explorador de la máquina virtual.

Para ver que Tomcat se ejecuta desde máquinas externas, deberá crear un extremo y abrir un puerto.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>Para crear un extremo para la máquina virtual
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Virtual Machines (clásico)**.
3. Haga clic en el nombre de la máquina virtual que ejecuta el servidor de aplicaciones Java.
4. Haga clic en **Extremos**.
5. Haga clic en **Agregar**.
6. Aparecerá el cuadro de diálogo **Agregar punto de conexión**:
   1. Especifique un nombre para el extremo; por ejemplo, **HttpIn**.
   2. Seleccione **TCP** para el protocolo.
   3. Especifique **80** para el puerto público.
   4. Especifique **8080** para el puerto privado.
   5. Seleccione **Deshabilitado** para la dirección IP flotante.
   6. Deje la lista de control de acceso tal y como está.
   7. Haga clic en el botón **Aceptar** para cerrar el cuadro de diálogo y crear el punto de conexión.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>Para abrir un puerto en el firewall para la máquina virtual
1. Inicie sesión en la máquina virtual.
2. Haga clic en **Inicio de Windows**.
3. Haga clic en **Panel de control**.
4. Haga clic en **Sistema y seguridad**, **Firewall de Windows** y luego en **Configuración avanzada**.
5. Haga clic en **Reglas de entrada** y después en **Nueva regla**.  
   ![Nueva regla de entrada][NewIBRule]
6. En **Tipo de regla**, seleccione **Puerto** y haga clic en **Siguiente**.  
   ![Puerto de nueva regla de entrada][NewRulePort]
7. En la pantalla **Protocolo y puertos**, seleccione **TCP**, especifique **8080** como **Puerto local específico** y luego haga clic en **Siguiente**.  
  ![Nueva regla de entrada][NewRuleProtocol]
8. En la pantalla **Acción**, seleccione **Permitir la conexión** y haga clic en **Siguiente**.
   ![Acción de nueva regla de entrada][NewRuleAction]
9. En la pantalla **Perfil**, asegúrese de que **Dominio**, **Privado** y **Público** estén activados y después haga clic en **Siguiente**.
   ![Perfil de nueva regla de entrada][NewRuleProfile]
10. En la pantalla **Nombre**, especifique un nombre para la regla, por ejemplo **HttpIn** (sin embargo, no es necesario que el nombre de la regla coincida con el nombre del punto de conexión) y haga clic en **Finalizar**.  
    ![Nombre de la nueva regla de entrada][NewRuleName]

En este momento, el sitio web de Tomcat debe ser visible desde un explorador externo. En la ventana de dirección del explorador, escriba una dirección URL del formulario  **http://*su\_DNS\_nombre*. cloudapp.net**, donde ***su\_DNS\_nombre*** es el nombre DNS que especificó cuando creó la máquina virtual.

## <a name="application-lifecycle-considerations"></a>Consideraciones acerca del ciclo de vida de las aplicaciones
* Puede crear su propio archivo de aplicación web (WAR) y agregarlo a la carpeta **webapps** . Por ejemplo, cree un proyecto web dinámico de Java Service Page (JSP) básico y expórtelo como archivo WAR. A continuación, copie el archivo WAR a la carpeta **webapps** de Apache Tomcat en la máquina virtual y, a continuación, ejecútelo en un explorador.
* De forma predeterminada, cuando el servicio Tomcat está instalado, está configurado para iniciarse manualmente. Puede cambiarlo para que se inicie automáticamente mediante el complemento Servicios. Inicie el complemento Servicios, haga clic en **Inicio de Windows**, **Herramientas administrativas** y luego en **Servicios**. Haga doble clic en el servicio **Apache Tomcat** y establezca **Tipo de inicio** en **Automático**.

    ![Configurar un servicio para que se inicie automáticamente][service_automatic_startup]

    La ventaja de que Tomcat se inicie automáticamente es que comienza a funcionar cuando se reinicia la máquina virtual (por ejemplo, después de instalar actualizaciones de software que requieren un reinicio).

## <a name="next-steps"></a>Pasos siguientes
Puede obtener más información sobre otros servicios (como Azure Storage, Service Bus y SQL Database) que quiera incluir con las aplicaciones de Java. Vea la información disponible en [Centro para desarrolladores de Java](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]:media/java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]:media/java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]:media/java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]:media/java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]:media/java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]:media/java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]:media/java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]:media/java-run-tomcat-app-server/NewRuleProfile.png


<!-- Deleted from the "To create an ednpoint for your virtual mache" 3/17/2017,
     to use the new portal.
6. In the **Add endpoint** dialog box, ensure **Add standalone endpoint** is selected, and then click **Next**.
7. In the **New endpoint details** dialog box:
-->
