---
title: "Configuración de Apache Tomcat en una máquina virtual de Linux | Microsoft Docs"
description: "Obtenga información acerca de cómo configurar Apache Tomcat7 con una máquina virtual Azure con Linux."
services: virtual-machines-linux
documentationcenter: 
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 45ecc89c-1cb0-4e80-8944-bd0d0bbedfdc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: ningk
ms.openlocfilehash: b76f6f7f53bc649fbc740a79e182f81f3fd983c7
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2017
---
# <a name="set-up-tomcat7-on-a-linux-virtual-machine-with-azure"></a>Configuración de Tomcat7 en una máquina virtual con Linux con Azure
Apache Tomcat (o simplemente Tomcat, anteriormente denominado Jakarta Tomcat) es un servidor web y contenedor de servlet de código abierto desarrollado por Apache Software Foundation (ASF). Tomcat implementa el servlet de Java y las especificaciones de JavaServer Pages (JSP) de Sun Microsystems. Además, proporciona un entorno de servidor web HTTP Java puro en el que se va a ejecutar el código de Java. En la configuración más sencilla, Tomcat se ejecuta en un proceso de sistema operativo único. Este proceso ejecuta una máquina virtual de Java (JVM). Todas las solicitudes HTTP desde un explorador para Tomcat se procesan como un subproceso independiente en el proceso de Tomcat.  

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [Azure Resource Manager y el modelo clásico](../../../resource-manager-deployment-model.md). Este artículo trata sobre cómo utilizar el modelo de implementación clásico. Se recomienda que las implementaciones más recientes usen el modelo Resource Manager. Para usar una plantilla de Resource Manager para implementar una máquina virtual de Ubuntu con Open JDK y Tomcat, consulte [este artículo](https://azure.microsoft.com/documentation/templates/openjdk-tomcat-ubuntu-vm/).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

En este artículo, instalará Tomcat7 en una imagen de Linux y la implementará en Azure.  

Aprenderá a:  

* Crear una máquina virtual en Azure.
* Preparar la máquina virtual para Tomcat7.
* Instalar Tomcat7.

Se supone que ya tiene una suscripción a Azure.  Si no es así, puede registrarse para obtener una evaluación gratuita en el [sitio web de Azure](https://azure.microsoft.com/). Si tiene una suscripción de MSDN, consulte [Precios especiales de Microsoft Azure: Ventajas de MSDN, MPN y Bizspark](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Para obtener más información acerca de Azure, consulte [¿Qué es Azure?](https://azure.microsoft.com/overview/what-is-azure/).

En este artículo se supone que tiene conocimientos prácticos básicos de Tomcat y Linux.  

## <a name="phase-1-create-an-image"></a>Fase 1: Creación de una imagen
En esta fase, creará una máquina virtual mediante una imagen de Linux en Azure.  

### <a name="step-1-generate-an-ssh-authentication-key"></a>Paso 1: Generación de una clave de autenticación SSH
SSH es una herramienta importante para los administradores del sistema. Pero no se recomienda configurar la seguridad del acceso mediante una contraseña determinada por personas. Los usuarios malintencionados pueden acceder a su sistema si está protegido por un nombre de usuario y una contraseña débiles.

La buena noticia es que hay una manera de dejar abierto el acceso remoto sin preocuparse por las contraseñas. Este método consiste en la autenticación con criptografía asimétrica. La clave privada del usuario es la que concede la autenticación. Incluso puede bloquear la cuenta del usuario para denegar la autenticación de contraseña.

Otra ventaja de este método es que no es necesario disponer de distintas contraseñas para iniciar sesión en distintos servidores. Puede realizar la autenticación utilizando la clave privada personal en todos los servidores, con lo que evitará tener que recordar varias contraseñas.



Siga estos pasos para generar la clave de autenticación SSH.

1. Descargue e instale Puttygen desde la siguiente ubicación: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Ejecute Puttygen.exe.
3. Haga clic en **Generar** para generar las claves. En el proceso puede aumentar la aleatoriedad al mover el mouse sobre el área en blanco en la ventana.  
   ![Captura de pantalla de PuTTY Key Generator que muestra el botón de generación de nueva clave][1]
4. Después del proceso de generación, Puttygen.exe mostrará la nueva clave pública.  
   ![Captura de pantalla de PuTTY Key Generator que muestra el botón de nueva clave pública y de guardado de clave privada][2]
5. Seleccione y copie la clave pública y guárdela en un archivo denominado publicKey.pem. No haga clic en **Guardar clave pública**, porque el formato de archivo de la clave pública guardada es diferente de la clave pública que queremos.
6. Haga clic en **Guardar clave privada** y guárdela en un archivo denominado privateKey.ppk.

### <a name="step-2-create-the-image-in-the-azure-portal"></a>Paso 2: Creación de la imagen en el portal de Azure
1. En el [portal](https://portal.azure.com/), haga clic en **Nuevo** en la barra de tareas para crear una imagen. Después elija una imagen de Linux que se adapte a sus necesidades. En el ejemplo siguiente se utiliza la imagen de Ubuntu 14.04.
![Captura de pantalla del portal que muestra el botón Nuevo][3]

2. En **Nombre de host** , especifique el nombre de la dirección URL que tanto usted como los clientes de Internet van a usar para tener acceso a esta máquina virtual. Defina la última parte del nombre DNS, por ejemplo, tomcatdemo. Azure generará entonces la dirección URL como tomcatdemo.cloudapp.net.  

3. En **SSH Authentication Key** (Clave de autenticación SSH), copie el valor de clave del archivo publicKey.pem, que contiene la clave pública generada por PuTTYgen.  
![Cuadro de la clave de autenticación de SSH en el portal][4]

4. Configure otras opciones según sea necesario y, luego, haga clic en **Crear**.  

## <a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Fase 2: Preparación de la máquina virtual para Tomcat7
En esta fase, configurará un punto de conexión para el tráfico de Tomcat y después se conectará a la nueva máquina virtual.

### <a name="step-1-open-the-http-port-to-allow-web-access"></a>Paso 1: Apertura del puerto HTTP para permitir el acceso web
Los puntos de conexión en Azure constan de un protocolo TCP o UDP, junto con un puerto público y privado. El puerto privado es el puerto que el servicio está escuchando en la máquina virtual. El puerto público es el puerto al que el servicio en la nube de Azure está escuchando externamente para el tráfico entrante basado en Internet.  

El puerto TCP 8080 es el número de puerto predeterminado que Tomcat utiliza para escuchar. Si se abre este puerto con un punto de conexión de Azure, usted y otros clientes de Internet pueden acceder a páginas de Tomcat.  

1. En el portal, haga clic en **Examinar** > **Máquinas virtuales** y, luego, haga clic en la máquina virtual que creó.  
   ![Captura de pantalla del directorio de máquinas virtuales][5]
2. Para agregar un extremo a la máquina virtual, haga clic en el cuadro **Extremos** .
   ![Captura de pantalla que muestra el cuadro de puntos de conexión][6]
3. Haga clic en **Agregar**.  

   1. Escriba un nombre para el punto de conexión en **Punto de conexión** y luego escriba 80 en **Puerto público**.  

      Si se establece con el valor 80, no necesitará incluir el número de puerto en la dirección URL que se usa para acceder a Tomcat. Por ejemplo, http://tomcatdemo.cloudapp.net.    

      Si se establece en otro valor como, por ejemplo, el 81, deberá agregar el número de puerto a la dirección URL para tener acceso a Tomcat. Por ejemplo, http://tomcatdemo.cloudapp.net:81/.
   2. Escriba 8080 en **Puerto privado**. De manera predeterminada, Tomcat escucha en el puerto TCP 8080. Si se cambió el puerto de escucha predeterminado de Tomcat, deberá actualizar el **Puerto privado** para que sea que el mismo puerto de escucha de Tomcat.  
      ![Captura de pantalla de interfaz de usuario que muestra Agregar comando, Puerto público y Puerto privado][7]
4. Haga clic en **Aceptar** para agregar el extremo a la máquina virtual.

### <a name="step-2-connect-to-the-image-you-created"></a>Paso 2: Conexión a la imagen creada
Puede elegir cualquier herramienta SSH para conectarse a la máquina virtual. En este ejemplo, se usa PuTTY.  

1. Obtenga el nombre DNS de la máquina virtual desde el portal.
    1. Haga clic en **Examinar** > **Máquinas virtuales**.
    2. Seleccione el nombre de la máquina virtual y después haga clic en **Propiedades**.
    3. En el icono **Propiedades**, busque el cuadro **Nombre de dominio** para obtener la DNS.  

2. Obtenga el número de puerto para las conexiones SSH desde el cuadro **SSH** .  
![Captura de pantalla que muestra el número de puerto de conexión SSH][8]

3. Descargue [PuTTY](http://www.putty.org/).  

4. Después de descargarlo, haga clic en el archivo ejecutable Putty.exe. En la configuración de PuTTY, configure las opciones básicas con el nombre de host y el número de puerto obtenido de las propiedades de la máquina virtual.   
![Captura de pantalla que muestra las opciones de nombre de host y puerto de la configuración de PuTTY][9]

5. En el panel izquierdo, haga clic en **Conexión** > **SSH** > **Aut.** y después haga clic en **Examinar** para especificar la ubicación del archivo privateKey.ppk. El archivo privateKey.ppk contiene la clave privada que generó PuTTYgen anteriormente en la sección "Fase 1: Creación de una imagen" de este artículo.  
![Captura de pantalla que muestra la jerarquía de directorios de conexión y el botón Examinar][10]

6. Haga clic en **Abrir**. Puede recibir una alerta en un cuadro de mensaje. Si ha configurado el nombre DNS y el número de puerto correctamente, haga clic en **Sí**.
![Captura de pantalla que muestra la notificación][11]

7. Se le solicitará escribir el nombre de usuario.  
![Captura de pantalla que muestra dónde escribir nombre de usuario][12]

8. Escriba el nombre de usuario que usó para crear la máquina virtual en la sección anterior de este artículo "Fase 1: Creación de una imagen". Verá algo parecido a lo siguiente:   
![Captura de pantalla que muestra la confirmación de autenticación][13]

## <a name="phase-3-install-software"></a>Fase 3: Instalación del software
En esta fase, instalará Java Runtime Environment, Tomcat7 y otros componentes de Tomcat7.  

### <a name="java-runtime-environment"></a>Java Runtime Environment
Tomcat está escrito en Java. Existen dos tipos de kits de desarrollo de Java (JDK): OpenJDK y JDK de Oracle. Puede elegir el que prefiera.  

> [!NOTE]
> Ambos JDK tienen casi el mismo código para las clases de la API de Java, aunque el código de la máquina virtual es diferente. OpenJDK suele usar bibliotecas abiertas; mientras que JDK de Oracle tiende a usar las bibliotecas cerradas. JDK de Oracle tiene más clases y algunos errores corregidos. Además, JDK de Oracle es más estable que OpenJDK.

#### <a name="install-openjdk"></a>Instalación de OpenJDK  

Use el comando siguiente para descargar OpenJDK.   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  


* Para crear un directorio que contenga los archivos JDK:  

        sudo mkdir /usr/lib/jvm  
* Para extraer los archivos JDK en el directorio /usr/lib/jvm/:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/

#### <a name="install-oracle-jdk"></a>Instalación de JDK de Oracle


Use el siguiente comando para descargar JDK de Oracle desde el sitio web de Oracle.  

     wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  
* Para crear un directorio que contenga los archivos JDK:  

        sudo mkdir /usr/lib/jvm  
* Para extraer los archivos JDK en el directorio /usr/lib/jvm/:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  
* Para establecer el JDK de Oracle como la máquina virtual de Java predeterminada:  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  

        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

#### <a name="confirm-that-java-installation-is-successful"></a>Confirmación de que la instalación de Java es correcta
Puede usar un comando similar al siguiente para comprobar si el entorno de tiempo de ejecución de Java está instalado correctamente:  

    java -version  

Si instaló OpenJDK, verá un mensaje similar al siguiente: ![mensaje de instalación OpenJDK correcta][14]

Si instaló JDK de Oracle, verá un mensaje similar al siguiente: ![mensaje de instalación JDK de Oracle correcta][15]

### <a name="install-tomcat7"></a>Instalación de Tomcat7
Use el comando siguiente para instalar Tomcat7.  

    sudo apt-get install tomcat7  

Si no usa Tomcat7, use la variación adecuada de este comando.  

#### <a name="confirm-that-tomcat7-installation-is-successful"></a>Confirmación de que la instalación de Tomcat7 es correcta
Para comprobar si se instaló correctamente Tomcat7, busque el nombre DNS de su servidor de Tomcat . En este artículo, la dirección URL es http://tomcatexample.cloudapp.net/. Si ve un mensaje similar al siguiente, Tomcat7 está instalado correctamente.
![Mensaje de instalación Tomcat7 correcta][16]

### <a name="install-other-tomcat7-components"></a>Instalación de otros componentes de Tomcat7
Hay otros componentes de Tomcat opcionales que puede instalar.  

Use el comando **sudo apt-cache search tomcat7** para ver todos los componentes disponibles. Use los comandos siguientes para instalar algunos componentes útiles.  

    sudo apt-get install tomcat7-admin      #admin web applications

    sudo apt-get install tomcat7-user         #tools to create user instances  

## <a name="phase-4-configure-tomcat7"></a>Fase 4: Configuración de Tomcat7
En esta fase es donde se administra Tomcat.

### <a name="start-and-stop-tomcat7"></a>Inicio y detención de Tomcat7
El servidor Tomcat7 se inicia automáticamente cuando lo instala. También puede iniciarlo con el comando siguiente:   

    sudo /etc/init.d/tomcat7 start

Para detener Tomcat7:

    sudo /etc/init.d/tomcat7 stop

Para ver el estado de Tomcat7:

    sudo /etc/init.d/tomcat7 status

Para reiniciar los servicios de Tomcat: 

    sudo /etc/init.d/tomcat7 restart

### <a name="tomcat7-administration"></a>Administración de Tomcat7
Puede editar el archivo de configuración de usuario de Tomcat para configurar las credenciales de administrador. Use el comando siguiente:  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Aquí tiene un ejemplo:  
![Captura de pantalla que muestra el resultado del comando vi de sudo][17]  

> [!NOTE]
> Cree una contraseña segura para el nombre de usuario de administrador.  

Después de editar este archivo, deberá reiniciar los servicios de Tomcat7 con el siguiente comando para asegurarse de que los cambios surtan efecto:  

    sudo /etc/init.d/tomcat7 restart  

Abra el explorador y escriba la URL **http://<your tomcat server DNS name>/manager/html**. Por ejemplo, en este artículo, la dirección URL es http://tomcatexample.cloudapp.net/manager/html.  

Después de conectarse, debería ver algo parecido a lo siguiente:   
![Captura de pantalla del administrador de aplicaciones web de Tomcat][18]

## <a name="common-issues"></a>Problemas comunes
### <a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>No se puede tener acceso a la máquina virtual con Tomcat y Moodle desde Internet
#### <a name="symptom"></a>Síntoma  
  Tomcat se está ejecutando, pero no puede ver la página predeterminada de Tomcat con el explorador.
#### <a name="possible-root-cause"></a>Posible causa principal   

  * El puerto de escucha de Tomcat no es el mismo que el puerto privado del punto de conexión de su máquina virtual para el tráfico de Tomcat.  

     Compruebe la configuración del punto de conexión del puerto público y del puerto privado. Asegúrese de que el puerto privado es el mismo que el puerto de escucha de Tomcat. Consulte la sección de este artículo "Fase 1: Crear una imagen" para obtener instrucciones acerca de cómo configurar puntos de conexión para la máquina virtual.  

     Para determinar el puerto de escucha de tomcat, abra /etc/httpd/conf/httpd.conf (versión de Red Hat) o /etc/tomcat7/server.xml (versión Debian). De manera predeterminada, el puerto de escucha de Tomcat es el 8080. Aquí tiene un ejemplo:  

        <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"   URIEncoding="UTF-8"            redirectPort="8443" />  

     Si usa una máquina virtual como Debian o Ubuntu y desea cambiar el valor predeterminado de puerto de escucha de Tomcat (por ejemplo, 8081), deberá abrir el puerto para el sistema operativo. En primer lugar, abra el perfil:  

        sudo vi /etc/default/tomcat7  

     A continuación, deberá quitarse la marca de comentario de la última línea y cambiar "no" por "yes".  

        AUTHBIND=yes
  2. El firewall deshabilitó el puerto de escucha de Tomcat.

     Solo puede ver la página predeterminada de Tomcat desde el host local. El problema es probablemente que el puerto, al que escucha Tomcat, está bloqueado por el firewall. Puede usar la herramienta w3m para explorar la página web. Los siguientes comandos instalan w3m y permiten examinar la página predeterminada de Tomcat:  


        sudo yum install w3m w3m-img


        w3m http://localhost:8080  
#### <a name="solution"></a>Solución

  * Si el puerto de escucha de Tomcat no es el mismo que el puerto privado del punto de conexión para el tráfico a la máquina virtual, es necesario cambiar el puerto privado para que sea el mismo que el puerto de escucha de Tomcat.   
  2. Si el problema se debe a los firewall o iptables, agregue las siguientes líneas a /etc/sysconfig/iptables. La segunda línea solo es necesaria para el tráfico https:  

      -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT

      -A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

     > [!IMPORTANT]
     > Asegúrese de que las líneas anteriores se ubican sobre cualquier otra línea que pueda restringir el acceso a nivel global, como la siguiente: -A INPUT -j REJECT --reject-with icmp-host-prohibited



Para volver a cargar iptables, ejecute el comando siguiente:

    service iptables restart

Esto se ha probado en CentOS 6.3.

### <a name="permission-denied-when-you-upload-project-files-to-varlibtomcat7webapps"></a>Permiso denegado al cargar archivos de proyecto a /var/lib/tomcat7/webapps/
#### <a name="symptom"></a>Síntoma
  Al usar cualquier cliente SFTP (por ejemplo, FileZilla) para conectarse a la máquina virtual y navegar a /var/lib/tomcat7/webapps/ para publicar el sitio, se obtiene un mensaje de error similar al siguiente:  

     status:    Listing directory /var/lib/tomcat7/webapps
     Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
     Error:    /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
     Error:    File transfer failed
#### <a name="possible-root-cause"></a>Posible causa principal
  No tiene permiso para tener acceso a la carpeta /var/lib/tomcat7/webapps.  
#### <a name="solution"></a>Solución  
  Necesita obtener permiso de la cuenta raíz. Puede cambiar la propiedad de esa carpeta de raíz al nombre de usuario que se usó al aprovisionar la máquina. Este es un ejemplo con el nombre de cuenta de azureuser:  

     sudo chown azureuser -R /var/lib/tomcat7/webapps

  Utilice la opción -R para aplicar los permisos a todos los archivos en un directorio.  

  Este comando también funciona para directorios. La opción -R cambia los permisos de todos los archivos y directorios dentro del directorio. Aquí tiene un ejemplo:  

     sudo chown -R username:group directory  

  Este comando cambia la propiedad (usuario y grupo) de todos los archivos y directorios que hay dentro del directorio.  

  El siguiente comando solo cambia los permisos del directorio de la carpeta. Los archivos y carpetas que hay en el directorio no cambian.  

     sudo chown username:group directory

[1]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
