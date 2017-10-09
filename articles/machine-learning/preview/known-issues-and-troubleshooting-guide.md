---
title: "Guía de solución de problemas conocidos | Microsoft Docs"
description: "Lista de problemas conocidos y una guía para ayudar a solucionar problemas"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ef5e058d81b64c46e8bad6f85c0bef0c69fb1512
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench: Guía de solución de problemas conocidos 
En este artículo se le ayuda a buscar y corregir los errores que se producen como parte del uso de la aplicación de Azure Machine Learning Workbench. 

> [!NOTE]
> Al comunicarse con el equipo de soporte técnico a través de mensajes de correo electrónico o mensajes del foro, resulta útil tener el número de compilación. Puede averiguar el número de compilación de la aplicación si hace clic en el menú **Ayuda**. Al hacer clic en el número de compilación, este se copia en el portapapeles para que pueda pegarlo en mensajes de correo electrónico o foros de soporte técnico e informar de los problemas más fácilmente.

![comprobar número de versión](media/known-issues-and-troubleshooting-guide/version.png)

## <a name="windows-and-mac"></a>Windows y Mac
### <a name="centos-based-azure-data-science-virtual-machine"></a>Máquina virtual de ciencia de datos de Azure basada en CentOS 
* No se admite el envío de trabajos a la Máquina virtual de ciencia de datos (DSVM) de Azure basada en CentOS. El destino también puede ser [DSVM basada en Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

### <a name="docker-error"></a>Error de Docker 
* Cuando se realiza una ejecución en un contenedor de Docker local, si ve el siguiente error puede corregirlo si cambia el servidor DNS de Docker de automático a fijo 8.8.8.8. 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```
![Imagen](media/known-issues-and-troubleshooting-guide/docker_dns.png)


### <a name="linux-vm-password"></a>Contraseña de la máquina virtual Linux 
* Si cambia la contraseña de Azure Portal en una VM de Ubuntu Linux, podría recibir el siguiente error al ejecutar un trabajo en ella:
  ```
  sudo: no tty present and no askpass program specified.
  ``` 

  La solución consiste en agregar un archivo (ejemplo _myDockerUsers_) en _/etc/sudoers.d_ con el contenido siguiente:
  ```
  <your_username> ALL = NOPASSWD: /usr/bin/docker, /usr/bin/nvidia-docker
  ```

### <a name="ssh-keys"></a>Claves SSH 
* No se admiten claves SSH al conectarse a una máquina remota o a un clúster de Spark mediante SSH. Solo se admite el modo de usuario y contraseña.

## <a name="windows-only"></a>Solo Windows 
### <a name="sharing-c-drive-in-docker"></a>Uso compartido de la unidad C en Docker 
* Compruebe el uso compartido de la unidad C con el Explorador de archivos.
* Abra la configuración del adaptador de red y desinstale o reinstale "Compartir impresoras y archivos para redes Microsoft" para vEthernet.
* Abra la configuración de Docker y comparta la unidad C en la configuración de Docker.
* Los cambios en la contraseña de Windows afectan al uso compartido. Abra el Explorador de archivos, vuelva a compartir la unidad C y escriba la nueva contraseña.
* También puede producirse un error de firewall al intentar compartir la unidad C con Docker. Esta [publicación acerca de Stack Overflow](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) puede resultar útil.
* Al compartir la unidad C con credenciales de dominio, el uso compartido podría dejar de funcionar en las redes donde el controlador de dominio no es accesible (por ejemplo, una red doméstica, una red Wi-Fi pública, etc.). Para obtener más información, consulte [esta publicación](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).



## <a name="mac-only"></a>Solo Mac 
* Las transformaciones de agrupaciones en clústeres de texto no son compatibles con Mac.
* La biblioteca de RevoScalePy no es compatible con Mac.

## <a name="azure-machine-learning-service-limits"></a>Límites del servicio de Azure Machine Learning

- El tamaño máximo de la carpeta de proyecto es 25 MB.
    >[!Note]
    >Este límite no se aplica a las carpetas `.git`, `docs` ni `outputs`. Estos nombres de carpeta distinguen entre mayúsculas y minúsculas.

- El tiempo máximo de ejecución permitido del experimento son 7 días.
- El tamaño máximo del archivo de seguimiento de la carpeta `outputs` después de una ejecución es 512 MB. 

>[!NOTE]
>Si está trabajando con archivos grandes, consulte [Persisting Changes and Deal with Large Files](how-to-read-write-files.md) (Cambios persistentes y manejo de archivos grandes).

## <a name="other-issues"></a>Otros problemas
Si es consciente de otros problemas no documentados, envíenos sus comentarios mediante _Enviar una sonrisa o desaprobación_. 




