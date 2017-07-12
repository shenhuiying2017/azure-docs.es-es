---
title: "Creación del primer servidor maestro de Jenkins en una máquina virtual Linux (Ubuntu) en Azure"
description: "Aproveche la plantilla de solución para implementar Jenkins."
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: multiple
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 748f1994d0ee5b6c6a988bca8e5b694b29c0b8c5
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017

---

<a id="create-your-first-jenkins-master-on-a-linux-ubuntu-vm-on-azure" class="xliff"></a>

# Creación del primer servidor maestro de Jenkins en una máquina virtual Linux (Ubuntu) en Azure

Esta guía de inicio rápido muestra cómo instalar la versión estable más reciente de Jenkins en una máquina virtual Linux (Ubuntu 14.04 LTS) junto con las herramientas y los complementos configurados para que funcionen con Azure. Estas herramientas son:
<ul>
<li>Git para el control de código fuente</li>
<li>Complemento de credenciales de Azure para una conexión segura</li>
<li>Complemento de agentes de máquina virtual de Azure para compilación elástica, prueba e integración continua</li>
<li>Complemento de Azure Storage para almacenar artefactos</li>
<li>CLI de Azure para implementar aplicaciones mediante scripts</li>
</ul>

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una cuenta de Azure gratis.
> * Cree un servidor maestro de Jenkins en una máquina virtual de Azure con una plantilla de solución. 
> * Realizar la configuración inicial para Jenkins.
> * Instalar los complementos sugeridos.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

<a id="create-the-vm-in-azure-by-deploying-the-solution-template-for-jenkins" class="xliff"></a>

## Creación de la máquina virtual en Azure mediante la implementación de la plantilla de solución para Jenkins

Las plantillas de inicio rápido de Azure le permiten implementar de forma rápida y confiable tecnología compleja en Azure.  Azure Resource Manager le permite aprovisionar sus aplicaciones mediante una [plantilla declarativa](https://azure.microsoft.com/en-us/resources/templates/?term=jenkins). En una plantilla, puede implementar varios servicios junto con sus dependencias. Use la misma plantilla para implementar su aplicación de forma repetida durante cada fase de su ciclo de vida.

Vea la información sobre [planes y precios](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.jenkins?tab=PlansAndPrice) para esta plantilla para entender las opciones de costos.

Vaya a [la imagen de Marketplace para Jenkins](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview), haga clic en **OBTENERLA AHORA**  

En Azure Portal, haga clic en **Crear**.  Esta plantilla requiere el uso de Resource Manager, por lo que la lista desplegable de modelos de plantilla está deshabilitada.
   
![Cuadro de diálogo de Azure Portal](./media/install-jenkins-solution-template/ap-create.png)

En la pestaña **Configuración básica**:

![Configuración básica](./media/install-jenkins-solution-template/ap-basic.png)

* Proporcione un nombre a la instancia de Jenkins.
* Seleccione un tipo de disco de máquina virtual.  Para las cargas de trabajo de producción, elija una máquina virtual de mayor tamaño y SSD para mejorar el rendimiento.  Puede leer más sobre los tipos de discos de Azure [aquí.](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage)
* Nombre de usuario: debe cumplir los requisitos de longitud y no incluir palabras reservadas ni caracteres no admitidos. No se permiten nombres como "administrador".  Para más información, consulte [aquí](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq) para ver los requisitos de nombre de usuario y contraseña.
* Tipo de autenticación: cree una instancia que esté protegida por una contraseña o [clave pública SSH](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows). Si usa una contraseña, esta debe reunir 3 de los siguientes requisitos de complejidad: contener al menos una minúscula, una mayúscula, un número y un carácter especial.
* Mantenga el tipo de versión de Jenkins como **LTS**
* Seleccione una suscripción.
* Cree un grupo de recursos o use uno existente.
* Seleccione una ubicación.

En la pestaña **Configurar opciones adicionales**:

![Configurar opciones adicionales](./media/install-jenkins-solution-template/ap-addtional.png)

* Proporcione una etiqueta de nombre de dominio para identificar de forma exclusiva el servidor maestro de Jenkins.

Haga clic en **Aceptar** para ir al paso siguiente. 

Una vez que la validación es correcta, haga clic en **Aceptar** para descargar la plantilla y los parámetros. 

A continuación, seleccione **Adquirir** para aprovisionar todos los recursos.

<a id="setup-ssh-port-forwarding" class="xliff"></a>

## Configuración del desvío de puertos SSH

De forma predeterminada, la instancia de Jenkins usa el protocolo http y escucha en el puerto 8080. Los usuarios no deben autenticarse a través de protocolos no seguros.
    
Configure el desvío de puertos para ver la interfaz de usuario de Jenkins en la máquina local.

<a id="if-you-are-using-windows" class="xliff"></a>

### Si está usando Windows:

Instale PuTTY y ejecute este comando si usa contraseña para proteger Jenkins:
```
putty.exe -ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* Para iniciar sesión, escriba la contraseña.

![Para iniciar sesión, escriba la contraseña.](./media/install-jenkins-solution-template/jenkins-pwd.png)

Si usa SSH, ejecute este comando:
```
putty -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

<a id="if-you-are-using-linux-or-mac" class="xliff"></a>

### Si usa Linux o Mac:

Si utiliza una contraseña para proteger el servidor maestro de Jenkins, ejecute este comando:
```
ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* Para iniciar sesión, escriba la contraseña.

Si usa SSH, ejecute este comando:
```
ssh -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

<a id="connect-to-jenkins" class="xliff"></a>

## Conexión a Jenkins
Después de haber iniciado el túnel, vaya a http://localhost:8080/ en la máquina local.

Desbloquee el panel de Jenkins por primera vez con la contraseña de administrador inicial.

![Desbloquear jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

Para obtener un token, SSH en la máquina virtual y ejecute `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.

![Desbloquear jenkins](./media/install-jenkins-solution-template/jenkins-ssh.png)

Se le pedirá que instale los complementos sugeridos.

A continuación, cree un usuario administrador para el servidor maestro de Jenkins.

Con esto, la instancia de Jenkins ya está lista para usarse. Puede acceder a una vista de solo lectura si va a http://\<nombre DNS público de la instancia que acaba de crear\>.

![Jenkins ya está listo.](./media/install-jenkins-solution-template/jenkins-welcome.png)

<a id="next-steps" class="xliff"></a>

## Pasos siguientes

En este tutorial, hizo lo siguiente:

> [!div class="checklist"]
> * Creó un servidor maestro de Jenkins con la plantilla de solución.
> * Realizó la configuración inicial de Jenkins.
> * Instaló los complementos.

Siga este vínculo para ver cómo utilizar los agentes de máquina virtual de Azure para la integración continua con Jenkins.

> [!div class="nextstepaction"]
> [Máquinas virtuales de Azure como agentes de Jenkins](jenkins-azure-vm-agents.md)

