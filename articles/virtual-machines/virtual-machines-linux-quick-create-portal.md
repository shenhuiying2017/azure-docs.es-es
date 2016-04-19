<properties
    pageTitle="Creación de una máquina virtual con Linux mediante el Portal de Azure | Microsoft Azure"
    description="Cree una máquina virtual con Linux mediante el Portal de Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="04/05/2016"
    ms.author="v-livech"
/>

# Creación de una máquina virtual con Linux mediante el Portal de Azure

En este artículo se muestra cómo utilizar el [Portal de Azure](https://portal.azure.com/) para crear ahora mismo una máquina virtual con Linux sin instalar nada. Los únicos requisitos son [una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/) y [archivos de clave pública y privada SSH](virtual-machines-linux-mac-create-ssh-keys.md).


1. Inicie sesión en el Portal de Azure con la identidad de la cuenta de Azure y haga clic en **+ Nuevo** en la esquina superior izquierda:

    ![screen1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

2. Haga clic en **Máquinas virtuales** en **Marketplace** y después en **Ubuntu Server 14.04 LTS** en la lista de imágenes **Aplicaciones destacadas**. Debería ver la siguiente pantalla:

    ![screen2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

3. Compruebe en la parte inferior que el modelo de implementación es `Resource Manager` y luego haga clic en **Crear**.

4. En la página **Básico**, escriba:
    - un nombre para la máquina virtual;
    - un nombre de usuario para el usuario administrador;
    - el tipo de autenticación, que debe ser **Clave pública SSH**;
    - la clave pública SSH como una cadena (de forma predeterminada, desde el directorio `~/.ssh/`);
    - un nombre de grupo de recursos (para crear un nuevo grupo de implementación) o seleccione un grupo existente;

    y haga clic en **Aceptar** para continuar y elegir el tamaño de la máquina virtual; debe ser similar a lo siguiente:

    ![screen3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

5. Elija el tamaño **DS1**, que instala Ubuntu en un SSD Premium y haga clic en **Seleccionar** para configurar los valores.

    ![screen4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

6. En **Configuración**, deje los valores predeterminados de almacenamiento y red y haga clic en **Aceptar** para ver el resumen.

    ![screen5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

7. Confirme la configuración de la nueva máquina virtual de Ubuntu y haga clic en **Aceptar**.

    ![screen6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

8. Abra el Panel del portal y, en **Interfaces de red**, elija su NIC.

    ![screen7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

9. Abra el menú de direcciones IP públicas en la configuración de NIC.

    ![screen8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

10. SSH en la dirección IP pública con la clave pública SSH

```
user@slackware$ ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## Pasos siguientes

Si lo desea, siga adelante y [agregue un disco](virtual-machines-linux-add-disk.md).

<!---HONumber=AcomDC_0413_2016-->