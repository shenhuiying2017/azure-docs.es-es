<properties pageTitle="Uso de la extensión de VM Docker para Linux en Azure" description="Describe Docker y las extensiones de máquinas virtuales de Azure y muestra cómo crear máquinas virtuales en Azure mediante programación que son host de Docker desde la línea de comandos usando la interfaz de comandos azure-cli." services="virtual-machines" documentationCenter="" authors="squillace" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/21/2014" ms.author="rasquill"/>
# Uso de la extensión de VM Docker desde la interfaz entre plataformas de Azure (xplat-cli)
En este tema se describe cómo crear una máquina virtual con la extensión de VM Docker desde xplat-cli en cualquier plataforma. [Docker](https://www.docker.com/) es uno de los enfoques de virtualización más populares que utilizan los [contenedores de Linux](http://en.wikipedia.org/wiki/LXC) en lugar de máquinas virtuales como una forma de aislar datos y calcular recursos compartidos. Puede usar la extensión de VM Docker para que el [agente Linux de Azure](http://azure.microsoft.com/es-es/documentation/articles/virtual-machines-linux-agent-user-guide/) cree una VM Docker que hospede cualquier número de contenedores para sus aplicaciones de Azure. Para ver un análisis de alto nivel de contenedores y sus ventajas, consulte [Documento de alto nivel de Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

+ [Uso de la extensión de VM Docker con Azure]
+ [Extensiones de máquina virtual para Linux y Windows] 
+ [Recursos de contenedor y de gestión de contenedores para Azure]
+ [Pasos siguientes]



## <a id='How to use the Docker VM Extension with Azure'>Uso de la extensión de VM Docker con Azure</a>
Para usar la extensión de VM Docker con Azure, debe instalar una versión de la [interfaz de línea de comandos entre plataformas de Azure](https://github.com/Azure/azure-sdk-tools-xplat) (llamada **xplat-cli** en este tema) superior a la 0.8.6 (a partir de este documento, la versión actual es la 0.8.10). Puede instalar xplat-cli en Mac, Linux y Windows. 

> [AZURE.NOTE] Aunque puede instalar xplat-cli en Microsoft Windows, Docker se creó con dependencias de kernel específicas para Linux. Por tanto, para usar Windows como un cliente Docker, debe hospedar una distribución de Linux completa como máquina virtual dentro de Hyper-V u otro hipervisor. Una vez que haya hecho esto, puede usar los comandos de xplat-cli y de Docker de este documento y los de Docker. Docker tiene un programa de instalación propio para Windows, [Boot2Docker](https://docs.docker.com/installation/windows/) que puede utilizar para automatizar esta misma instalación.

El proceso completo para usar Docker en Azure es simple:

+ Instale las herramientas de la línea de comando xplat-cli y sus dependencias en el equipo desde el que desee controlar Azure (en Windows, será una distribución de Linux que se ejecuta como una máquina virtual)
+ Utilice los comandos xplat-cli de Docker para crear un host de Docker de VM en Azure
+ Utilice los comandos Docker locales para gestionar sus contenedores de Docker en su VM Docker en Azure.

> [AZURE.NOTE] La xplat-cli (interfaz de la línea de comandos) actualmente es la única forma de crear una VM controlada por Docker en Azure para hospedar contenedores de Docker. 

### Instalación de la interfaz de línea de comandos entre plataformas (xplat-cli)
Para instalar y configurar la interfaz de línea de comandos entre plataformas, consulte [Instalación de la interfaz de la línea de comandos entre plataformas de Azure](http://azure.microsoft.com/es-es/documentation/articles/xplat-cli/#install). Para confirmar la instalación, escriba  `azure` en el símbolo del sistema y, tras un breve instante, debería ver el arte ASCII de xplat-cli, que enumera los comandos básicos que tiene a su disposición. Si la instalación ha funcionado correctamente, debería poder escribir  `azure help vm` y ver que uno de los comandos que aparecen en la lista es "docker".

> [AZURE.NOTE] Si está utilizando una instalación LTS de Ubuntu 14.04, esa imagen tiene una instalación de nodo ligeramente diferente que puede requerir trabajo adicional. Una sugerencia que parece que funciona bien se encuentra [aquí](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-an-ubuntu-14-04-server) en la sección **Instalación mediante un PPA**, que describe cómo instalar la versión más reciente de nodejs directamente y parece que funciona bien en una distribución LTS de Ubuntu 14.04. 

### Conexión de xplat-cli con su cuenta de Azure
Antes de poder utilizar xplat-cli debe asociar las credenciales de su cuenta de Azure con xplat-cli en su plataforma. La sección [Conexión con su suscripción de Azure](http://azure.microsoft.com/es-es/documentation/articles/xplat-cli/#configure) explica cómo descargar e importar su archivo **.publishsettings** o asociar su línea de comandos xplat-cli con un identificador de organización. 

> [AZURE.NOTE] Existen algunas diferencias de comportamiento al utilizar uno u otro método de autenticación, por eso debe asegurarse de leer el documento anterior para comprender las diferentes funcionalidades. 

### Instalación de Docker y uso de la extensión de VM Docker para Azure
Siga las [instrucciones de instalación de Docker](https://docs.docker.com/installation/#installation) para instalar Docker de forma local en su equipo. Para la mayoría de los sistemas operativos y distribuciones, esto significa escribir  `apt-get install docker.io`. Confirme que la versión de Docker es la 1.0 o superior.

Para usar Docker con una máquina virtual de Azure, la imagen de Linux usada para la máquina virtual debe tener el [agente de máquina virtual de Linux de Azure](http://azure.microsoft.com/es-es/documentation/articles/virtual-machines-linux-agent-user-guide/) instalado. Actualmente, solamente hay dos tipos de imágenes que proporcionan esto:

+ Una imagen Ubuntu de la galería de imágenes de Azure o 

+ Una imagen de Linux personalizada que ha creado con el agente de máquina virtual de Linux de Azure instalado y configurado. Consulte [Agente de máquina virtual de Linux de Azure](http://azure.microsoft.com/es-es/documentation/articles/virtual-machines-linux-agent-user-guide/) para obtener más información sobre cómo crear una máquina virtual personalizada de con el agente máquina virtual de Azure.

### Uso de la galería de imágenes de Azure

Desde una sesión de Bash o Terminal, use el siguiente comando xplat-cli para localizar la imagen Ubuntu más reciente en la galería de máquina virtual para usar escribiendo

`azure vm image list | grep Ubuntu-14_04`

y seleccione uno de los nombres de imagen, como por ejemplo  `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-es-es-30GB`. Por último, use el siguiente comando para crear una nueva máquina virtual usando esa imagen. 

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-es-es-30GB" <username> <password>
``` 

donde:

+ *&lt;vm-cloudservice name&gt;* es el nombre de la máquina virtual que se convertirá en el equipo host del contenedor de Docker en Azure.

+  *&lt;username&gt;* es el nombre de usuario del usuario raíz predeterminado de la máquina virtual.

+ *&lt;password&gt;* es la contraseña de la cuenta  *username* que cumple con las normas de complejidad de Azure. 
 
> [AZURE.NOTE] Actualmente, una contraseña debe tener al menos 8 caracteres, contener un carácter en minúscula y otro en mayúscula, un número y un carácter especial como uno de los siguientes: `!@#$%^&+=`. No, el punto al final de la frase anterior NO es un carácter especial. 

Si el comando es correcto, debería ver algo como lo siguiente, dependiendo de los argumentos y opciones precisas que haya utilizado:

![](./media/virtual-machines-docker/dockercreateresults.png)

> [AZURE.NOTE] Crear una máquina virtual puede llevar unos minutos, pero después de que se haya aprovisionado, el demonio de Docker (el servicio Docker) se inicia y puede conectarse al host del contenedor de Docker.

Para probar la VM Docker que ha creado en Azure, escriba

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:4243 info`

donde *<vm-name-you-used>* es el nombre de la máquina virtual que ha utilizado en su llamada a `azure vm docker create`. Debe ver algo similar a lo siguiente, lo que significa que en su máquina virtual de host de Docker está activa y en funcionamiento en Azure y esperando sus comandos.

![](./media/virtual-machines-docker/connectingtodockerhost.png)

### Autenticación de máquina virtual de host de Docker
Además de crear la VM Docker, el comando  `azure vm docker create` también crea automáticamente los certificados necesarios para permitir a su equipo cliente de Docker conectarse al host del contenedor de Azure mediante HTTPS y los certificados se almacenan tanto en el equipo cliente como en el host, según corresponda. En las siguientes ejecuciones, se vuelven a utilizar los certificados existentes y se comparten con el nuevo host.

De forma predeterminada, los certificados se colocan en `~/.docker` y Docker se configurará para ejecutarse en el puerto **4243**. Si desea utilizar un puerto o directorio diferente, entonces puede utilizar una de las siguientes opciones de línea de comando  `azure vm docker create` para configurar su VM host de contenedor Docker para que utilice un puerto diferente o diferentes certificados para conectarse con los clientes:

```
-dp, --docker-port [port]              Port to use for docker [4243]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

El demonio de Docker del host está configurado para que realice escuchas y autenticaciones de las conexiones de cliente en el puerto especificado mediante los certificados generados por el comando  `azure vm docker create`. El equipo cliente debe tener estos certificados para obtener acceso al host de Docker. 

> [AZURE.NOTE] Un host de red que se ejecuta sin estos certificados será vulnerable a cualquiera que se pueda conectar al equipo. Antes de modificar la configuración predeterminada, asegúrese de que comprende los riesgos de sus equipos y aplicaciones.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

Está preparado para ir a la [Guía del usuario de Docker] y usar su VM Docker. Para crear una máquina virtual con la funcionalidad Docker en el nuevo portal, consulte [Uso de la extensión de VM Docker con el portal].

<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Pasos siguientes]: #next-steps

[Uso de la extensión de VM Docker con Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Extensiones de máquina virtual para Linux y Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
[Recursos de contenedor y de gestión de contenedores para Azure]: #Container-and-Container-Management-Resources-for-Azure

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-tutorial/
[Vínculo 2 a otro tema de documentación de azure.microsoft.com]: ../web-sites-custom-domain-name/
[Vínculo 3 a otro tema de documentación de azure.microsoft.com]: ../storage-whatis-account/
[Uso de la extensión de VM Docker con el portal]: http://azure.microsoft.com/es-es/documentation/articles/virtual-machines-docker-with-portal/

[Guía del usuario de Docker]: https://docs.docker.com/userguide/




<!--HONumber=42-->
