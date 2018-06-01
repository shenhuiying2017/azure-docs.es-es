---
title: 'Tutorial de creación de la infraestructura para un clúster de Service Fabric en AWS: Azure Service Fabric | Microsoft Docs'
description: En este tutorial aprenderá a configurar la infraestructura de AWS para ejecutar un clúster de Service Fabric.
services: service-fabric
documentationcenter: .net
author: david-stanford
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dastanfo
ms.custom: mvc
ms.openlocfilehash: 6b7d2223d33abb429ab5f59b14c80d43c70598dc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209657"
---
# <a name="tutorial-create-aws-infrastructure-to-host-a-service-fabric-cluster"></a>Tutorial: creación de la infraestructura de AWS para hospedar un clúster de Service Fabric

Los clústeres de Service Fabric independientes ofrecen la opción de elegir un entorno propio y crear un clúster como parte del enfoque "Cualquier sistema operativo, cualquier nube" que toma Service Fabric. En esta serie de tutoriales se crea un clúster independiente hospedado en AWS y se instala en él una aplicación.

Este tutorial es la primera parte de una serie. En este artículo se generan los recursos de AWS necesarios para hospedar el clúster de Service Fabric independiente. En futuros artículos deberá instalar el conjunto de Service Fabric independiente, una aplicación de ejemplo en el clúster y, por último, limpiarlo.

En la primera parte de la serie, se aprende a:

> [!div class="checklist"]
> * Crear un conjunto de instancias de EC2
> * Modificar el grupo de seguridad
> * Iniciar sesión en una de las instancias
> * Preparar la instancia para Service Fabric

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial, deberá tener una cuenta de AWS.  Si aún no tiene una, vaya a la [consola de AWS](https://aws.amazon.com/) para crearla.

## <a name="create-ec2-instances"></a>Creación de instancias de EC2

Inicie sesión en la consola de AWS > Escriba **EC2** en el cuadro de búsqueda > **EC2 Virtual Servers in the Cloud** (Servidores virtuales de EC2 en la nube).

![Búsqueda de la consola AWS][aws-console]

Seleccione **Launch Instance** (Iniciar instancia), en la siguiente pantalla, elija **Seleccionar** junto a la base de Microsoft Windows Server 2016.

![Selección de la instancia de EC2][aws-ec2instance]

Seleccione **t2.medium** y **Next: Configure Instance Details** (Siguiente: configuración de los detalles de la instancia), en la siguiente pantalla, cambie el número de instancias a `3` y seleccione **Advanced Details** (Detalles avanzados) para expandir esa sección.

Para conectar las máquinas virtuales juntas en Service Fabric, las que hospedan la infraestructura deben tener las mismas credenciales.  Hay dos formas habituales de obtener credenciales coherentes: unirlas todas al mismo dominio o establecer la misma contraseña de administrador en todas las máquinas virtuales.  Para este tutorial se utiliza un script de datos de usuario para establecer las instancias de EC2 para que todas ellas tengan la misma contraseña.  En un entorno de producción, la combinación de los hosts en un dominio de Windows es más segura.

Escriba el siguiente script en el campo de datos de usuario en la consola:

```powershell
<powershell>
$user = [adsi]"WinNT://localhost/Administrator,user"
$user.SetPassword("serv1ceF@bricP@ssword")
$user.SetInfo()
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
</powershell>
```

Una vez haya escrito el script de PowerShell, seleccione **Review and Launch** (Revisar e iniciar).

![Revisión e inicio de EC2][aws-ec2configure2]

En la pantalla de revisión, seleccione **Launch** (Iniciar).  A continuación, cambie la lista desplegable a **Proceed without a key pair** (Continuar sin par de claves) y seleccione la casilla que indica que conoce la contraseña.

![Selección del par de claves de AWS][aws-keypair]

Por último, seleccione **Launch Instances** (Iniciar instancias) y **View Instances** (Ver instancias).  Ahora que tiene la base creada para el clúster de Service Fabric, debe agregar algunas configuraciones finales a las instancias en sí para prepararlas para la configuración de Service Fabric.

## <a name="modify-the-security-group"></a>Modificar el grupo de seguridad

Service Fabric requiere un número de puertos abiertos entre los hosts del clúster. Para abrir estos puertos en la infraestructura de AWS, seleccione una de las instancias que ha creado. A continuación, seleccione el nombre del grupo de seguridad, por ejemplo, **launch-wizard-1**. Ahora, seleccione la pestaña **Inbound** (Entrante).

Para evitar que se abran estos puertos a todo el mundo, en su lugar, ábralos solo para los hosts del mismo grupo de seguridad. Tome nota del identificador de grupo de seguridad, en el ejemplo, **sg-c4fb1eba**.  Después, seleccione **Edit** (Editar).

A continuación, agregue cuatro reglas al grupo de seguridad para las dependencias del servicio y tres más para el propio servicio Service Fabric. La primera regla es para permitir el tráfico ICMP, para las comprobaciones de conectividad básicas. Las demás reglas abren los puertos necesarios para habilitar SMB y el registro remoto.

Para la primera regla, seleccione **Add Rule** (Agregar regla), a continuación, del menú desplegable, seleccione **All ICMP - IPv4** (Todos los ICMP: IPv4). Seleccione la casilla junto a lo personalizado y escriba su identificador de grupo de seguridad anterior.

Para las tres últimas dependencias, debe seguir un proceso similar.  Seleccione **Add Rule** (Agregar regla), en la lista desplegable, seleccione **Custom TCP Rule** (Regla TCP personalizada) y, en el intervalo de puertos, escriba uno de `135`, `137-139`, y `445` para cada regla. Por último, en el cuadro origen, especifique el identificador de grupo de seguridad.

![Puertos de los grupos de seguridad][aws-ec2securityports]

Ahora que están abiertos los puertos para las dependencias, debe hacer lo mismo para los puertos que usa el propio servicio Service Fabric para comunicarse. Seleccione **Add Rule** (Agregar regla), en la lista desplegable, seleccione **Custom TCP Rule** (Regla TCP personalizada), en el intervalo de puertos `20001-20031` escriba el grupo de seguridad en el cuadro origen.

A continuación, agregue una regla para el intervalo de puertos efímeros.  Seleccione **Add Rule** (Agregar regla), en la lista desplegable, seleccione **Custom TCP Rule** (Regla TCP personalizada) y, en el intervalo de puertos, escriba `20606-20861`. Por último, en el cuadro origen, especifique el identificador de grupo de seguridad.

Para las dos últimas reglas para Service Fabric, ábralo a todo el mundo para administrar el clúster de Service Fabric desde su equipo. Seleccione **Add Rule** (Agregar regla), en la lista desplegable, seleccione **Custom TCP Rule** (Regla TCP personalizada) y, en el intervalo de puertos, escriba uno de `19000-19003` y `19080-19081`, y cambie la lista desplegable Source (Origen) a Anywhere (Cualquier sitio).

Por último, necesitamos abrir el puerto 8080 para ver la aplicación cuando se implementa. Seleccione **Add Rule** (Agregar regla), en la lista desplegable, seleccione **Custom TCP Rule** (Regla TCP personalizada) y, en el intervalo de puertos, escriba `8080`, y cambie la lista desplegable Source (Origen) a Anywhere (Cualquier sitio).

Ahora se incluyen todas las reglas. Seleccione **Guardar**.

## <a name="connect-to-an-instance-and-validate-connectivity"></a>Conexión a una instancia y confirmación de la conectividad

En la pestaña del grupo de seguridad, seleccione **Instances** (Instancias) en el menú izquierdo.  Seleccione cada una de las instancias que ha creado y anote sus direcciones IP privadas; en los siguientes ejemplos se usan `172.31.21.141` y `172.31.20.163`.

Una vez que tenga todas las direcciones IP, seleccione una de las instancias para conectarse, haga clic con el botón derecho en la instancia y seleccione **Connect** (Conectar).  Desde aquí puede descargar el archivo RDP para esta instancia concreta.  Seleccione **Download Remote Desktop File** (Descargar archivo de Escritorio remoto) y abra el archivo que se descargó para establecer la conexión a Escritorio remoto (RDP) para esta instancia.  Cuando se le solicite, escriba la contraseña `serv1ceF@bricP@ssword`.

![Download Remote Desktop File (Descargar archivo de Escritorio remoto)][aws-rdp]

Una vez que se ha conectado correctamente a la instancia, confirme que puede establecer una conexión entre ellos y compartir archivos.  Ha reunido las direcciones IP de todas las instancias, seleccione una a la que no esté conectado actualmente. Vaya a **Start** (Inicio), escriba `cmd` y seleccione **Command Prompt** (Símbolo del sistema).

En estos ejemplos, la conexión RDP se estableció con la siguiente dirección IP: 172.31.21.141. Toda la conectividad de prueba se produce a con la otra dirección IP: 172.31.20.163.

Para asegurarse de que la conectividad básica funciona, utilice el comando ping.

```
ping 172.31.20.163
```

Si la salida es similar a `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` repetida cuatro veces, la conexión entre las instancias funciona.  Ahora, asegúrese de que el recurso compartido de SMB funciona con el comando siguiente:

```
net use * \\172.31.20.163\c$
```

Debe devolver `Drive Z: is now connected to \\172.31.20.163\c$.` como salida.

## <a name="prep-instances-for-service-fabric"></a>Preparación de las instancias para Service Fabric

Si estuviera creando esto desde el principio, necesitaría seguir unos pasos adicionales.  Es decir, debería asegurarse de que el registro remoto se encontraba en ejecución, habilitar SMB y abrir los puertos necesarios para SMB y el registro remoto.

Para que sea más fácil, todo este trabajo se insertó al arrancar las instancias con el script de datos de usuario.

Para habilitar SMB, este es el comando de PowerShell que utilizó:

```powershell
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
```

Para abrir los puertos en el firewall, este es el comando de PowerShell:

```powershell
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
```

## <a name="next-steps"></a>Pasos siguientes

En la primera parte de la serie aprendió a iniciar tres instancias de EC2 y a configurarlas para la instalación de Service Fabric:

> [!div class="checklist"]
> * Crear un conjunto de instancias de EC2
> * Modificar el grupo de seguridad
> * Iniciar sesión en una de las instancias
> * Preparar la instancia para Service Fabric

Avance a la segunda parte de la serie para configurar Service Fabric en el clúster.

> [!div class="nextstepaction"]
> [Instalación de Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[aws-console]: ./media/service-fabric-tutorial-standalone-cluster/aws-console.png
[aws-ec2instance]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2instance.png
[aws-ec2configure2]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2configure2.png
[aws-rdp]: ./media/service-fabric-tutorial-standalone-cluster/aws-rdp.png
[aws-ec2securityports]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2securityports.png
[aws-keypair]: ./media/service-fabric-tutorial-standalone-cluster/aws-keypair.png