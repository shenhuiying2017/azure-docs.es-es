<properties
   pageTitle="Crear un FQDN para una máquina virtual en el Portal de Azure | Microsoft Azure"
   description="Aprenda a crear un nombre de dominio completo o FQDN para una máquina virtual basada en el administrador de recursos en el Portal de Azure."
   services="virtual-machines"
   documentationCenter=""
   authors="dsk-2015"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/21/2015"
   ms.author="dkshir"/>

# Crear un nombre de dominio completo en el Portal de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.


Cuando crea una máquina virtual en el [Portal de Azure](https://portal.azure.com) mediante el modelo de implementación del **Administrador de recursos**, el portal crea un recurso de IP pública para la máquina virtual. Puede usar esta dirección IP para obtener acceso remoto a la máquina virtual. Sin embargo, el portal no crea un [nombre de dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) o FQDN de forma predeterminada. Puesto que sería más sencillo recordar y usar un FQDN en lugar de una dirección IP, en este artículo se muestra cómo puede agregar uno a la máquina virtual.

En el artículo se supone que ha iniciado la sesión en su suscripción en el portal y que ha creado una máquina virtual con las imágenes disponibles con el **Administrador de recursos**. Cuando comience a ejecutarse la máquina virtual, siga estos pasos.

1.  Vea la configuración de la máquina virtual en el portal y haga clic en la dirección IP pública.

    ![buscar recurso de ip](media/virtual-machines-create-fqdn-on-portal/locatePublicIP.PNG)

2.  **Desasocie** la dirección IP pública de la máquina virtual. Tenga en cuenta que todavía no muestra un nombre de dominio. Tras hacer clic en el botón **Sí**, puede tardar unos segundos antes de finalizar la desasociación.

    ![desasociar recurso de ip](media/virtual-machines-create-fqdn-on-portal/dissociateIP.PNG)

    Asociaremos esta dirección IP pública con la máquina virtual después de los pasos siguientes. Si la dirección IP pública es una _IP pública dinámica_, perderá la dirección IPV4 y se asignará una nueva después de haber configurado el FQDN.

3.  Cuando se deshabilite el botón **Desasociar**, haga clic en la sección **Toda la configuración** de la IP pública y abra la pestaña **Configuración**. Escriba la etiqueta del nombre DNS que quiera. **Guarde** esta configuración.

    ![escribir etiqueta de nombre dns](media/virtual-machines-create-fqdn-on-portal/dnsNameLabel.PNG)

4.  Vuelva a la hoja de la máquina virtual en el portal y haga clic en **Toda la configuración de** para la máquina virtual. Abra la pestaña **Interfaces de red** y haga clic en el recurso de la interfaz de red asociado a esta máquina virtual. De esta manera se abrirá la hoja de la **Interfaz de red** en el portal.

    ![abrir interfaz de red](media/virtual-machines-create-fqdn-on-portal/openNetworkInterface.PNG)

5.  Tenga en cuenta que el campo **Dirección IP pública** de la interfaz de red está en blanco. Haga clic en la sección **Toda la configuración** sección para esta interfaz de red y abra la pestaña **Direcciones IP**. En la hoja **Direcciones IP**, haga clic en **Habilitada** para el campo **Dirección IP pública**. Seleccione la pestaña **Dirección IP Configurar los valores obligatorios** y la dirección IP predeterminada que tenía desasociada anteriormente. Haga clic en **Save**. Puede tardar varios minutos en volver a agregar el recurso de IP.

    ![configurar recurso de IP](media/virtual-machines-create-fqdn-on-portal/configureIP.PNG)

6.  Cierre todas las demás hojas y vuelva a la hoja **Máquina virtual**. Haga clic en el recurso de dirección de IP pública en la configuración. Observe que la hoja de IP pública muestra ahora el FQDN que quiere como el **nombre DNS**.

    ![Se crea el FQDN](media/virtual-machines-create-fqdn-on-portal/fqdnCreated.PNG)

    Ahora puede conectarse de manera remota a la máquina virtual con este nombre DNS. Por ejemplo, use `SSH adminuser@testdnslabel.eastus.cloudapp.azure.com` al conectarse a una máquina virtual de Linux que tiene el nombre de dominio completo nombre de `testdnslabel.eastus.cloudapp.azure.com` y el nombre de usuario de `adminuser`.

<!---HONumber=AcomDC_1203_2015-->