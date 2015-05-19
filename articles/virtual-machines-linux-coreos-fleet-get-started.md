<properties 
	pageTitle="Introducción a Fleet en CoreOS en Azure" 
	description="Proporciona ejemplos básicos sobre el uso de Fleet y Docker en una máquina virtual de Linux con CoreOS en Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"
	editor="madhana"/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services" 
	ms.date="03/17/2015" 
	ms.author="danlep"/>


<!--The next line, with one pound sign at the beginning, is the page title-->
# Introducción a Fleet en CoreOS en Azure

En este artículo se proporcionan dos ejemplos rápidos sobre el uso de [Fleet](https://github.com/coreos/fleet) y [Docker](https://www.docker.com/) para ejecutar aplicaciones en un clúster de máquinas virtuales [CoreOS].

Para usar estos ejemplos, primero configure un CoreOS de tres nodos de CoreOS tal como se describe en [Uso de CoreOS en Azure]. Después de hacerlo, entenderá los elementos básicos de las implementaciones de CoreOS y tendrá un equipo cliente y clúster en funcionamiento. Usaremos exactamente el mismo nombre de clúster en estos ejemplos. Además, estos ejemplos dan por supuesto que está usando un host Linux local para ejecutar sus comandos de **Fleet**.




## <a id='simple'>Ejemplo 1: Hello World con Docker</a>

A continuación se muestra una aplicación «Hello World» sencilla que se ejecuta en un solo contenedor Docker. Este usa la [imagen de concentrador de Docker BusyBox].

En el equipo cliente de Linux, use el editor de texto que prefiera para crear el siguiente archivo de unidad **systemd** y asígnele el nombre `helloworld.service`. \(Para obtener más información sobre la sintaxis, vea [Archivos de unidad]\).

```
[Unit]
Description=HelloWorld
After=docker.service
Requires=docker.service

[Service]

TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill busybox1
ExecStartPre=-/usr/bin/docker rm busybox1
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --name busybox1 busybox /bin/sh -c "while true; do echo Hello World; sleep 1; done"
ExecStop=/usr/bin/docker stop busybox1

```

Ahora, conéctese al clúster de CoreOS e inicie la unidad ejecutando el siguiente comando **fleetctl**. La salida muestra que la unidad se inicia y dónde se encuentra.


```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start helloworld.service


Unit helloworld.service launched on 62f0f66e.../100.79.86.62
```

>[AZURE.NOTE]Para ejecutar los comandos **fleetctl** remotos sin el parámetro **--tunnel**, puede establecer la variable de entorno FLEETCTL\_TUNNEL para tunelizar las solicitudes. Por ejemplo: `export FLEETCTL_TUNNEL=coreos-cluster.cloudapp.net:22`.


Puede conectarse al contenedor para ver la salida del servicio:

```
fleetctl --tunnel coreos-cloudapp.cluster.net:22 journal helloworld.service

Mar 04 21:29:26 node-1 docker[57876]: Hello World
Mar 04 21:29:27 node-1 docker[57876]: Hello World
Mar 04 21:29:28 node-1 docker[57876]: Hello World
Mar 04 21:29:29 node-1 docker[57876]: Hello World
Mar 04 21:29:30 node-1 docker[57876]: Hello World
Mar 04 21:29:31 node-1 docker[57876]: Hello World
Mar 04 21:29:32 node-1 docker[57876]: Hello World
Mar 04 21:29:33 node-1 docker[57876]: Hello World
Mar 04 21:29:34 node-1 docker[57876]: Hello World
Mar 04 21:29:35 node-1 docker[57876]: Hello World
```

Para limpiar, detenga y descargue la unidad.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop helloworld.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload helloworld.service
```


## <a id='highavail'>Ejemplo 2: Servidor Apache de alta disponibilidad</a>

Una ventaja de usar CoreOS, Docker, y **Fleet** es que resulta fácil ejecutar servicios de una manera altamente disponible. En este ejemplo implementará un servicio que consta de tres contenedores idénticos que se ejecutan en el servidor web Apache. Los contenedores se ejecutarán en las tres máquinas virtuales del clúster. Este ejemplo es parecido a uno de [Iniciar contenedores con Fleet] y usa la [imagen de concentrador de Docker CoreOS / Apache ].

>[AZURE.NOTE]Para ejecutar el servidor Apache de alta disponibilidad, deberá configurar un extremo HTTP de carga equilibrada en las máquinas virtuales \(puerto público 80, puerto privado 80\). Puede hacerlo después de crear el clúster de CoreOS, mediante el Portal de administración de Azure o el comando **azure vm endpoint**. Consulte [Configurar un conjunto de carga equilibrada] para obtener más información.

En el equipo cliente, use el editor de texto que prefiera para crear un archivo de unidad de plantilla **systemd**, denominado apache@.service. Usará esa plantilla para iniciar tres instancias independientes, denominadas apache@1.service, apache@2.service y apache@3.service:

```
[Unit]
Description=High Availability Apache
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill apache1
ExecStartPre=-/usr/bin/docker rm apache1
ExecStartPre=/usr/bin/docker pull coreos/apache
ExecStart=/usr/bin/docker run -rm --name apache1 -p 80:80 coreos/apache /usr/sbin/apache2ctl -D FOREGROUND
ExecStop=/usr/bin/docker stop apache1

[X-Fleet]
X-Conflicts=apache@*.service
```

>[AZURE.NOTE]El atributo `X-Conflicts` le indica a CoreOS que solo se puede ejecutar una instancia de este contenedor en un determinado host CoreOS. Para obtener información detallada, consulte [Archivos de unidad].

Ahora inicie las instancias de unidad en el clúster de CoreOS. Debería ver que se están ejecutando en tres equipos distintos:

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start apache@{1,2,3}.service

unit apache@3.service launched on 00c927e4.../100.79.62.16
unit apache@1.service launched on 62f0f66e.../100.79.86.62
unit apache@2.service launched on df85f2d1.../100.78.126.15

```
Para ponerse en contacto con el servidor Apache que se ejecuta en una de las unidades, envíe una solicitud simple al servicio en la nube que hospeda el clúster de CoreOS.

`curl http://coreos-cluster.cloudapp.net`

Verá que el texto predeterminado que devuelve el servidor Apache es similar a lo siguiente:

```
\<htm\l>\<body\>\<h1\>It works!\</h1\>
\<p\>This is the default web page for this server.\</p\>
\<p\>The web server software is running but no content has been added, yet.\</p\>
\</body\>\</html\>
```

Puede intentar apagar una o varias máquinas virtuales en clúster para comprobar que el servicio Apache sigue ejecutándose.

Cuando acabe, detenga y descargue las unidades.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop apache@{1,2,3}.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload apache@{1,2,3}.service

```

## Pasos siguientes

Puede sacar más partido del clúster de CoreOS de tres nodos en Azure. Dedíquese a explorar cómo crear clústeres más complejos y usar Docker y crear aplicaciones más interesantes leyendo el [tutorial sobre CoreOS de Tim Park], el [tutorial sobre CoreOS de Patrick Chanezon], la documentación de [Docker] y la [información general sobre CoreOS].

Vea también [Informática de código abierto y Linux en Azure] para obtener más información sobre el uso de entornos de código abierto en máquinas virtuales de Linux en Azure.

<!--Link references-->
[Azure Cross-Platform Interface (xplat-cli)]: xplat-cli.md
[CoreOS]: https://coreos.com/
[información general sobre CoreOS]: https://coreos.com/using-coreos/
[CoreOS with Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[tutorial sobre CoreOS de Tim Park]: https://github.com/timfpark/coreos-azure
[tutorial sobre CoreOS de Patrick Chanezon]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Uso de CoreOS en Azure]: virtual-machines-linux-coreos-how-to.md
[Configurar un conjunto de carga equilibrada]: http://msdn.microsoft.com/library/azure/dn655055.aspx
[Iniciar contenedores con Fleet]: https://coreos.com/docs/launching-containers/launching/launching-containers-fleet/
[Archivos de unidad]: https://coreos.com/docs/launching-containers/launching/fleet-unit-files/
[imagen de concentrador de Docker BusyBox]: https://registry.hub.docker.com/_/busybox/
[imagen de concentrador de Docker CoreOS / Apache ]: https://registry.hub.docker.com/u/coreos/apache/
[Informática de código abierto y Linux en Azure]: virtual-machines-linux-opensource.md
<!--HONumber=54-->