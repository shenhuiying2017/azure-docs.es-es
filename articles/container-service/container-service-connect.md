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
   ms.date="04/12/2016"
   ms.author="rogardle"/>


# Conexión a un clúster del servicio Contenedor de Azure

Los clústeres de DC/OS y Swarm que implementa el servicio Contenedor de Azure exponen los puntos de conexión REST. Sin embargo, estos puntos de conexión no están abiertos al mundo exterior. Para administrar dichos puntos de conexión, es preciso crear un túnel de Secure Shell (SSH). Cuando se haya establecido un túnel SSH, puede ejecutar comandos contra los puntos de conexión y ver la interfaz de usuario del clúster a través de un explorador en su propio sistema. Este documento le guía en la creación de un túnel de SSH en Linux, OSX y Windows.

>[AZURE.NOTE] Puede crear una sesión de SSH con un sistema de administración de clústeres. Sin embargo, no es aconsejable. Si se trabaja directamente en un sistema de administración, es preciso asumir el riesgo de que se produzcan cambios involuntarios en la configuración.

## Creación de un túnel de SSH en Linux u OS X

Lo primero que se hace al crear un túnel de SSH en Linux u OS X es buscar el nombre DNS público de los patrones de carga equilibrada. Para ello, expanda el grupo de recursos de forma que se muestren todos los recursos. Busque y seleccione la dirección IP pública del patrón. Se abrirá una hoja que contiene información acerca de la dirección IP pública, que incluye el nombre DNS. Guarde este nombre para usarlo más adelante. <br />


![Nombre DNS público](media/pubdns.png)

Ahora, abra un shell y ejecute el siguiente comando, donde:

**PORT** es el puerto del punto de conexión que desea exponer. En el caso de Swarm, es el 2375. En el de DC/OS, utilice el puerto 80. **USERNAME** es el nombre de usuario que se especificó cuando se implementó el clúster. **DNSPREFIX** es el prefijo DNS que proporcionó al implementar el clúster. **REGION** es la región en la que está ubicado el grupo de recursos.

> El puerto de conexión SSH es el 2200 y no el 22 estándar.

```bash
# ssh sample

ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```

### Túnel de DC/OS

Para abrir un túnel a los puntos de conexión relacionados con DC/OS, ejecute un comando similar al siguiente:

```bash
# ssh sample

ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Ya puede acceder a los puntos de conexión relacionados con DC/OS en:

- DC/OS: `http://localhost/`
- Marathon: `http://localhost/marathon`
- Mesos: `http://localhost/mesos`

De igual forma, se puede acceder a las API de REST de cada aplicación a través de este túnel.

### Túnel de Swarm

Para abrir un túnel al punto de conexión de Swarm, ejecute un comando parecido al siguiente:

```bash
# ssh sample

ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Ya puede establecer la variable de entorno DOCKER\_HOST como se indica a continuación y seguir usando la interfaz de línea de comandos (CLI) de Docker de la manera habitual.

```bash
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
- **Puerto de origen:** su preferencia (use 80 para DC/OS o 2375 para Swarm).
- **Destino:** use localhost:80 para DC/OS o localhost:2375 para Swarm.

En el siguiente ejemplo se configura para DC/OS, pero su aspecto sería similar para Docker Swarm.

>[AZURE.NOTE] El puerto 80 no debe estar en uso cuando se cree este túnel.

![Configuración 3 de PuTTY](media/putty3.png)

Cuando haya terminado, guarde la configuración de conexión y conecte la sesión de PuTTY. Cuando se conecte, podrá ver la configuración del puerto en el registro de eventos de PuTTY.

![Registro de eventos de PuTTY](media/putty4.png)

Cuando haya configurado el túnel para DC/OS, podrá acceder al punto de conexión relacionado en:

- DC/OS: `http://localhost/`
- Marathon: `http://localhost/marathon`
- Mesos: `http://localhost/mesos`

Cuando haya configurado el túnel para Docker y enjambre, podrá acceder al clúster de Swarm a través de la CLI de Docker. Primero será preciso que configure una variable de entorno de Windows denominada `DOCKER_HOST` cuyo valor será ` :2375`.

## Pasos siguientes

Implemente y administre contenedores con DC/OS o Swarm.

[Trabajo con el servicio Contenedor de Azure y DC/OS](./container-service-mesos-marathon-rest.md) [Trabajo con el servicio de contenedor de Azure y Docker Swarm](./container-service-docker-swarm.md)

<!---HONumber=AcomDC_0420_2016-->