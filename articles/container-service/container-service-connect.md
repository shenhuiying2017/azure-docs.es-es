<properties
   pageTitle="Conexión a un clúster del servicio Contenedor de Azure | Microsoft Azure"
   description="Conexión a un clúster del servicio Contenedor de Azure mediante un túnel de SSH."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenedores, microservicios, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>


# Conexión a un clúster del servicio Contenedor de Azure

Los clústeres de Mesos y Swarm que implementa el servicio Contenedor de Azure exponen los puntos de conexión REST. Sin embargo, estos puntos de conexión no están abiertos al mundo exterior. Para administrar dichos puntos de conexión, es preciso crear un túnel de Secure Shell (SSH). Cuando se haya establecido un túnel SSH, puede ejecutar comandos contra los puntos de conexión y ver la interfaz de usuario del clúster a través de un explorador en su propio sistema. Este documento le guía en la creación de un túnel de SSH en Linux, OSX y Windows.

>[AZURE.NOTE] Puede crear una sesión de SSH con un sistema de administración de clústeres. Sin embargo, no es aconsejable. Si se trabaja directamente en un sistema de administración, es preciso asumir el riesgo de que se produzcan cambios involuntarios en la configuración.

## Creación de un túnel de SSH en Linux u OS X

Lo primero que se hace al crear un túnel de SSH en Linux u OS X es buscar el nombre DNS público de los patrones de carga equilibrada. Para ello, expanda el grupo de recursos de forma que se muestren todos los recursos. Busque y seleccione la dirección IP pública del patrón. Se abrirá una hoja que contiene información acerca de la dirección IP pública, que incluye el nombre DNS. Guarde este nombre para usarlo más adelante. <br />

![Nombre DNS público](media/pubdns.png)

Ahora, abra un shell y ejecute el siguiente comando, donde:

**PORT** es el puerto del punto de conexión que desea exponer. En el caso de Swarm, es el 2375. En el caso de Mesos, utilice el puerto 80. **USERNAME** es el nombre de usuario que se especificó cuando se implementó el clúster. **DNSPREFIX** es el prefijo DNS que proporcionó al implementar el clúster. **REGION** es la región en la que está ubicado el grupo de recursos.

```
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```
### Túnel de Mesos

Para abrir un túnel a los puntos de conexión relacionados con Mesos, ejecute un comando similar al siguiente:

```
ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Ya puede acceder a los puntos de conexión relacionados con Mesos en:

- Mesos: `http://localhost/mesos`
- Marathon: `http://localhost/marathon`
- Chronos: `http://localhost/chronos`

De igual forma, se puede acceder a las API de REST de cada aplicación a través de este túnel: Marathon: `http://localhost/marathon/v2`. Para más información acerca de las distintas API disponibles, consulte [Marathon REST API](https://mesosphere.github.io/marathon/docs/rest-api.html) (API de REST de Marathon) en la documentación de Mesosphere. Consulte la [Chronos Rest API](https://mesos.github.io/chronos/docs/api.html) (API de REST de Chronos) y [Scheduler HTTP API](http://mesos.apache.org/documentation/latest/scheduler-http-api/) (API de HTTP de programador) en la documentación de Apache.

### Túnel de Swarm

Para abrir un túnel al punto de conexión de Swarm, ejecute un comando parecido al siguiente:

```
ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Ya puede establecer la variable de entorno DOCKER\_HOST como se indica a continuación y seguir usando la interfaz de línea de comandos (CLI) de Docker de la manera habitual.

```
export DOCKER_HOST=:2375
```

## Creación de un túnel de SSH en Windows

Existen varias opciones para crear túneles de SSH en Windows. En este documento se describirá cómo usar PuTTY para hacerlo.

Descargue PuTTY en el sistema Windows y ejecute la aplicación.

Escriba un nombre de host que conste del nombre de usuario de administrador de clústeres y el nombre DNS público del primer patrón del clúster. El valor de **Nombre de host** se parecerá a este: `adminuser@PublicDNS`. Escriba 2200 en el campo **Puerto**.

![Configuración 1 de PuTTY](media/putty1.png)

Seleccione `SSH` y `Authentication`. Agregue el archivo de clave privada para la autenticación.

![Configuración 2 de PuTTY](media/putty2.png)

Seleccione `Tunnels` y configure los siguientes puertos reenviados:
- **Puerto de origen:** su preferencia (use 80 para Mesos o 2375 para Swarm).
- **Destino:** use localhost:80 para Mesos o localhost:2375 para Swarm.

En el siguiente ejemplo se configura para Mesos, pero su aspecto sería similar para Docker Swarm.

>[AZURE.NOTE] El puerto 80 no debe estar en uso cuando se cree este túnel.

![Configuración 3 de PuTTY](media/putty3.png)

Cuando haya terminado, guarde la configuración de conexión y conecte la sesión de PuTTY. Cuando se conecte, podrá ver la configuración del puerto en el registro de eventos de PuTTY.

![Registro de eventos de PuTTY](media/putty4.png)

Cuando haya configurado el túnel para Mesos, podrá acceder al punto de conexión relacionado en:

- Mesos: `http://localhost/mesos`
- Marathon: `http://localhost/marathon`
- Chronos: `http://localhost/chronos`

Cuando haya configurado el túnel para Docker y enjambre, podrá acceder al clúster de Swarm a través de la CLI de Docker. Primero será preciso que configure una variable de entorno de Windows denominada `DOCKER_HOST` cuyo valor será ` :2375`.

## Solución de problemas

### Después de crear el túnel e ir a la dirección url de mesos o marathon, obtengo el error 502 de puerta de enlace incorrecta...
La manera más fácil de resolverlo es eliminar el clúster y volver a implementarlo. También puede hacer lo siguiente para forzar a Zookeeper a repararse a sí mismo:

Inicie sesión en cada servidor maestro y haga lo siguiente:

```
sudo service nginx stop
sudo service marathon stop
sudo service chronos stop
sudo service mesos-dns stop
sudo service mesos-master stop 
sudo service zookeeper stop
```

Después, cuando todos los servicios se hayan detenido en todos los maestros:
```
sudo mkdir /var/lib/zookeeperbackup
sudo mv /var/lib/zookeeper/* /var/lib/zookeeperbackup
sudo service zookeeper start
sudo service mesos-master start
sudo service mesos-dns start
sudo service chronos start
sudo service marathon start
sudo service nginx start
```
Poco después de que se hayan reiniciado todos los servicios, debería poder trabajar con el clúster, tal como se describe en la documentación.

## Pasos siguientes

Implemente y administre contenedores con Mesos o Swarm.

- [Administración de contenedores con la API de REST](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0406_2016-->