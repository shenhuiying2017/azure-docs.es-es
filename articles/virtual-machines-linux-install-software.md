<properties urlDisplayName="Install software on VM" pageTitle="Instalaci&oacute;n de software en una m&aacute;quina virtual de Linux - Azure" metaKeywords="" description="Aprenda a instalar software en su m&aacute;quina virtual de Linux en Azure utilizando CentOS/Red Hat o Ubuntu." metaCanonical="" services="virtual-machines" documentationCenter="" title="Instalaci&oacute;n del software en la m&aacute;quina virtual de Linux en Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Instalación del software en la máquina virtual de Linux en Azure

Las distribuciones de Linux suelen usar paquetes de software para instalar software. Estos paquetes se administran normalmente mediante un conjunto de comandos, como `apt` o `yum`. También puede instalar programas sin un paquete, como con *tarball* del código fuente.

Mostraremos cómo usar administradores de paquetes para algunas de las distribuciones comunes de Linux. Los pasos variarán según la distribución de Linux que use.

**Nota:** Según la forma en la que se configure el entorno, estos comandos pueden requerir que se ejecuten con privilegios raíz (a través de `sudo`).

## CentOS/Red Hat

CentOS incorpora `yum` para la administración de paquetes. Con esta herramienta, puede instalar, desinstalar, actualizar, enumerar paquetes instalados, etc. Consulte a continuación para ver la sintaxis para estos comandos.

### Instalación

Mediante este paso, se instalará un paquete y todos los paquetes de los que dependa. Debido a las dependencias, puede instalarse más de un paquete.

    yum install [package name]

### Desinstalación

Mediante este paso, se desinstalará un paquete de la máquina. Tenga en cuenta que no se quitarán las dependencias.

    yum remove [package name]

### Actualización

Mediante este paso, se actualizará un paquete a la versión más reciente. El paquete debe instalarse para poder actualizarlo.

    yum update [package name]

### Enumeración de los paquetes instalados

Mediante este paso, se mostrará una lista de los paquetes instalados en la máquina.

    yum list installed

## Ubuntu

Ubuntu incorpora `apt` (herramienta de empaquetado avanzado) para la administración de paquetes. Con esta herramienta, puede instalar, desinstalar, actualizar, enumerar paquetes instalados, etc. Consulte a continuación para ver la sintaxis para estos comandos.

### Instalación

Mediante este paso, se instalará un paquete y todos los paquetes de los que dependa. Debido a las dependencias, puede instalarse más de un paquete.

    apt-get install [package name]

### Desinstalación

Mediante este paso, se desinstalará un paquete de la máquina. Tenga en cuenta que no se quitarán las dependencias.

    apt-get remove [package name]

### Actualización

Para actualizar a una nueva versión, tendrá que usar dos comandos: uno para actualizar el índice del paquete y otro para actualizar los paquetes. Ejecute el siguiente comando para actualizar el índice del paquete.

    apt-get update

Una vez que se haya actualizado el índice del paquete, ejecute el siguiente comando para actualizar los paquetes:

    apt-get upgrade
