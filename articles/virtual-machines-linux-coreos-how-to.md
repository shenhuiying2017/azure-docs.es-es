<properties title="Uso de CoreOS en Azure" pageTitle="Uso de CoreOS en Azure" description="Describe CoreOS, c&oacute;mo crear una m&aacute;quina virtual de CoreOS en Azure y su uso b&aacute;sico." metaKeywords="linux, virtual machines, vm, azure, CoreOS, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/27/2014" ms.author="rasquill" />

<!--This is a basic template that shows you how to use mark down to create a topic that includes a TOC, sections with subheadings, links to other azure.microsoft.com topics, links to other sites, bold text, italic text, numbered and bulleted lists, code snippets, and images. For fancier markdown, find a published topic and copy the markdown or HTML you want. For more details about using markdown, see http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.--> <!--Properties section (above): this is required in all topics. Please fill it out! See http://sharepoint/sites/azurecontentguidance/wiki/Pages/Markdown%20tagging%20-%20add%20a%20properties%20section%20to%20your%20markdown%20file%20to%20specify%20metadata%20values.aspx for details. --> <!-- Tags section (above): this is required in all topics. Please fill it out! See http://sharepoint/sites/azurecontentguidance/wiki/Pages/Markdown%20tagging%20-%20add%20a%20tags%20section%20to%20your%20markdown%20file%20to%20specify%20metadata%20for%20reporting.aspx for details. --> <!--The next line, with one pound sign at the beginning, is the page title-->

# Uso de CoreOS en Microsoft Azure

Este tema describe [CoreOS][CoreOS] y muestra cómo crear un clúster de tres máquinas virtuales de CoreOS en Azure para que pueda comprender rápidamente el funcionamiento de CoreOS. Utiliza los elementos más básicos de las implementaciones de CoreOS y ejemplos de [CoreOS con Azure][CoreOS con Azure], [el tutorial sobre CoreOS de Tim Park][el tutorial sobre CoreOS de Tim Park] y [el tutorial sobre CoreOS de Patrick Chanezon][el tutorial sobre CoreOS de Patrick Chanezon] para demostrar lo fácil que es entender la estructura básica de una implementación CoreOS y conseguir que un clúster de tres máquinas virtuales se ejecute correctamente.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

Este tema contiene las siguientes secciones:

-   [CoreOS, clústeres y contenedores de Linux][CoreOS, clústeres y contenedores de Linux]
-   [Consideraciones sobre la seguridad][Consideraciones sobre la seguridad]
-   [Uso de CoreOS en Azure][Uso de CoreOS en Azure]
-   [Pasos siguientes][Pasos siguientes]

## <span id="intro"></span>CoreOS, clústeres y contenedores de Linux</a>

CoreOS es una versión ligera de Linux diseñada para la creación rápida de clústeres potencialmente muy grandes de máquinas virtuales que utilizan contenedores de Linux como único mecanismo de empaquetado, incluidos contenedores [Docker][Docker]. CoreOS está diseñado para admitir lo siguiente:

-   un nivel de automatización muy elevado
-   una implementación de aplicaciones más sencilla y coherente
-   escalabilidad a nivel de aplicación y de sistema

A un alto nivel, las características de CoreOS que consiguen estos objetivos son las siguientes:

1.  Un sistema de paquete: CoreOS ejecuta solo imágenes de contenedor de Linux que se ejecutan en contenedores de Linux para lograr velocidad, uniformidad y facilidad de explotación
2.  Actualizaciones de sistema operativo que se llevan a cabo atómicamente de manera que los sistemas operativos se actualizan como una entidad única y pueden devolverse fácilmente a un estado conocido
3.  Demonios (servicios) [etcd][etcd] y [fleet][fleet] para una comunicación y una administración dinámicas entre máquina virtual y clúster

Esta es una descripción muy general de CoreOS y sus características. Para obtener información más completa sobre CoreOS, consulte la [información general sobre CoreOS][información general sobre CoreOS].

## <span id="security"></span>Consideraciones sobre la seguridad</a>

Actualmente, CoreOS supone que aquellos que pueden aplicar SSH en el clúster tienen permiso para administrarlo. El resultado es que, sin modificación, los clústeres de CoreOS incluyen capacidades excelentes para entornos de prueba y desarrollo, pero debería aplicar más medidas de seguridad en cualquier entorno de producción.

## <span id="usingcoreos"></span>Uso de CoreOS en Azure</a>

Esta sección describe cómo crear un Servicio en la nube de Azure que contenga tres máquinas virtuales de CoreOS utilizando [la interfaz entre plataformas de Azure (xplat-cli)][la interfaz entre plataformas de Azure (xplat-cli)]. Los pasos básicos son los siguientes:

1.  Crear los certificados SSH y las claves para proteger la comunicación con la máquina virtual de CoreOS
2.  Obtener el identificador etcd del clúster para la intercomunicación
3.  Crear un archivo de configuración de la nube en formato [YAML][YAML]
4.  Utilizar xplat-cli para crear un nuevo Servicio en la nube de Azure y tres máquinas virtuales de CoreOS
5.  Probar su clúster de CoreOS desde una máquina virtual de Azure
6.  Probar su clúster de CoreOS desde localhost

### Crear claves públicas y privadas para la comunicación

Siga las instrucciones de [Utilización de SSH con Linux en Azure][Utilización de SSH con Linux en Azure] para crear una clave pública y privada para SSH. (Los pasos básicos se encuentran en las instrucciones a continuación). Va a utilizar estas claves para conectar con las máquinas virtuales en el clúster para verificar que funcionan y que pueden comunicarse entre sí.

> [WACOM.NOTE] Este tema supone que no tiene estas claves, y requiere que cree un archivo **`myPrivateKey.pem`** y**`myCert.pem`** para una mayor claridad. Si ya tiene un par de claves pública y privada guardado en **`~/.ssh/id_rsa`**, basta con que escriba `openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem` para obtener el archivo .pem que necesita cargar en Azure.

1.  En un directorio de trabajo, escriba `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem` para crear la clave privada y un certificado X.509 asociado a ella.

2.  Para afirmar que el propietario de la clave privada puede leer o escribir el archivo, escriba `chmod 600 myPrivateKey.key`.

Ahora debería tener un archivo **`myPrivateKey.key`** y otro **`myCert.pem`** en el directorio de trabajo.

### Obtener el identificador etcd del clúster

El demonio **etcd** de CoreOS requiere un identificador de detección para consultar todos los nodos del clúster de manera automática. Para recuperar el identificador de detección y guardarlo en un archivo **etcdid**, escriba

    curl https://discovery.etcd.io/new | grep ^http.* > etcdid

### Crear un archivo de configuración de la nube

Aún en el mismo directorio de trabajo, cree un archivo con el editor de texto que prefiera que contenga el texto siguiente y guárdelo como **`cloud-config.yaml`**. (Puede guardarlo con el nombre que desee, pero cuando cree las máquinas virtuales en el paso siguiente, tendrá que hacer referencia a este nombre de archivo en la opción **--custom-data** del comando **azure create vm**).

> [WACOM.NOTE] Recuerde escribir `cat etcdid` para recuperar el identificador de detección etcd del archivo `etcdid` que creó anteriormente y sustituya **`<token>`** en el siguiente archivo **cloud-config.yaml** por el número generado en el archivo `etcdid`. Si no puede validar el clúster al final, es posible que este sea uno de los pasos que ha pasado por alto.

    #cloud-config

    coreos:
      etcd:
        # generate a new token for each unique cluster from https://discovery.etcd.io/new
        discovery: https://discovery.etcd.io/<token>
        # deployments across multiple cloud services will need to use $public_ipv4
        addr: $private_ipv4:4001
        peer-addr: $private_ipv4:7001
      units:
        - name: etcd.service
          command: start
        - name: fleet.service
          command: start

(Para obtener información más completa acerca del archivo de configuración de nube, consulte [Uso de configuración de la nube][Uso de configuración de la nube] en la documentación de CoreOS).

### Utilizar xplat-cli para crear una máquina virtual de CoreOS

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

1.  Instale la [interfaz entre plataformas de Azure (xplat-cli)][la interfaz entre plataformas de Azure (xplat-cli)] si aún no lo ha hecho, e inicie sesión con un identificador de trabajo o escolar o bien descargue un archivo .publishsettings e impórtelo en su cuenta.
2.  Busque la imagen CoreOS. Actualmente, existe solo una imagen -- **`2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0`** -- pero para buscar las imágenes disponibles en cualquier momento, escriba `azure vm image list | grep .*CoreOS.*` y debería ver uno o varios resultados similares a:
    `data:    2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0              Public    Linux`
3.  Cree un Servicio en la nube para su clúster básico escribiendo
    `azure service create <cloud-service-name>`, donde *cloud-service-name* es el nombre del servicio en la nube de CoreOS. En este ejemplo se utiliza el nombre **`coreos-cluster`**; tendrá que utilizar de nuevo el nombre que seleccionó al crear las instancias de VM de CoreOS dentro del Servicio en la nube.

Una nota: si revisa el trabajo que lleva realizado hasta ahora en el [nuevo portal][nuevo portal], verá que el nombre de su servicio en la nube es tanto un grupo de recursos como un dominio, tal y como se aprecia en la imagen siguiente:

![][0]

1.  Conéctese a su servicio en la nube y cree en él una nueva VM de CoreOS utilizando el comando **azure vm create**. Pasará la ubicación de su certificado X.509 en la opción **--ssh-cert**. Cree su primera imagen de VM escribiendo lo siguiente (recuerde sustituir **coreos-cluster** por el nombre del servicio en la nube que ha creado):

<!-- -->

    azure vm create --custom-data=cloud-config.yaml --ssh=22 --ssh-cert=./myCert.pem --no-ssh-password --vm-name=node-1 --connect=coreos-cluster --location='West US' 2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0 core

1.  Cree el segundo nodo repitiendo el comando del paso 4, reemplazando el valor de **--vm-name** por **node-2** y el valor del puerto **--ssh** por 2022.

2.  Cree el tercer nodo repitiendo el comando del paso 4, reemplazando el valor de **--vm-name** por **node-3** y el valor del puerto **--ssh** por 3022.

Puede ver en la siguiente captura de pantalla el aspecto del clúster CoreOS en el nuevo portal.

![][1]

### Probar su clúster de CoreOS desde una máquina virtual de Azure

Para probar el clúster, asegúrese de que se encuentra en su directorio de trabajo y, a continuación, conéctese a **node-1** utilizando **ssh**, pasando la clave privada escribiendo lo siguiente:

`ssh core@coreos-cluster.cloudapp.net -p 22 -i ./myPrivateKey.key`

Una vez que se haya conectado, escriba `sudo fleetctl list-machines` para ver si el clúster ya ha identificado todas las máquinas virtuales del clúster. Debería recibir una respuesta similar a la siguiente:

    core@node-1 ~ $ sudo fleetctl list-machines
    MACHINE     IP      METADATA
    442e6cfb... 100.71.168.115  -
    a05e2d7c... 100.71.168.87   -
    f7de6717... 100.71.188.96   -

### Probar su clúster de CoreOS desde localhost

Por último, probemos el clúster de CoreOS desde el cliente de Linux local instalando **fleet**. **fleet** requiere **golang**; por tanto, es posible que tenga que instalarlo antes escribiendo lo siguiente:

`sudo apt-get install golang`

Luego clone el repositorio **fleet** desde github escribiendo lo siguiente:

`git clone https://github.com/coreos/fleet.git`

Compile **fleet** escribiendo

`./build`

Y por último, coloque **fleet** para un uso sencillo (dependiendo de su configuración, es posible que necesite o no utilizar **sudo**):

`cp bin/fleetctl /usr/local/bin`

Asegúrese de que **fleet** tiene acceso a su `myPrivateKey.key` en el directorio de trabajo escribiendo lo siguiente:

`ssh-add ./myPrivateKey.key`

> [WACOM.NOTE] Si ya está utilizando la clave **~/.ss h/id\_rsa**, entonces agréguela con `ssh-add ~/.ssh/id_rsa`.

Ahora está listo para probar remotamente utilizando el mismo comando **fleetctl** que utilizó de **node-1**, pero pasando más argumentos remotos:

`fleetctl --tunnel coreos-cluster.cloudapp.net:22 list-machines`

Los resultados deberían ser exactamente los mismos:

    MACHINE     IP      METADATA
    442e6cfb... 100.71.168.115  -
    a05e2d7c... 100.71.168.87   -
    f7de6717... 100.71.188.96   -

## Pasos siguientes

Ahora debería tener un clúster de CoreOS de tres nodos ejecutándose en Azure. A partir de aquí, puede explorar cómo crear clústeres más complejos y utilizar Docker y crear aplicaciones más interesantes leyendo[el tutorial sobre CoreOS de Tim Park][el tutorial sobre CoreOS de Tim Park], [el tutorial sobre CoreOS de Patrick Chanezon][el tutorial sobre CoreOS de Patrick Chanezon], la documentación de [Docker][Docker] y la [información general sobre CoreOS][información general sobre CoreOS].

<!--Anchors--> <!--Image references--> <!--Link references-->

  [CoreOS]: https://coreos.com/
  [CoreOS con Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
  [el tutorial sobre CoreOS de Tim Park]: https://github.com/timfpark/coreos-azure
  [el tutorial sobre CoreOS de Patrick Chanezon]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
  [CoreOS, clústeres y contenedores de Linux]: #intro
  [Consideraciones sobre la seguridad]: #security
  [Uso de CoreOS en Azure]: #usingcoreos
  [Pasos siguientes]: #next-steps
  [Docker]: http://docker.io
  [etcd]: https://github.com/coreos/etcd
  [fleet]: https://github.com/coreos/fleet
  [información general sobre CoreOS]: https://coreos.com/using-coreos/
  [la interfaz entre plataformas de Azure (xplat-cli)]: ../xplat-cli/
  [YAML]: http://yaml.org/
  [Utilización de SSH con Linux en Azure]: http://azure.microsoft.com/es-es/documentation/articles/virtual-machines-linux-use-ssh-key/
  [Uso de configuración de la nube]: https://coreos.com/docs/cluster-management/setup/cloudinit-cloud-config/
  [nuevo portal]: https://portal.azure.com
  [0]: ./media/virtual-machines-linux-coreos-how-to/cloudservicefromnewportal.png
  [1]: ./media/virtual-machines-linux-coreos-how-to/endresultemptycluster.png
