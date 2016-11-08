---
title: Definición de directivas de laboratorio en Azure DevTest Labs | Microsoft Docs
description: Obtenga información acerca de cómo definir directivas de laboratorio como tamaños de máquina virtual, máximo de máquinas virtuales por usuario y automatización de apagado.
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: ''

ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: tarcher

---
# Definición de directivas de laboratorio en Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/How-to-set-VM-policies-in-a-DevTest-Lab/player]
> 
> 

Azure DevTest Labs le permite especificar importantes directivas que le ayudan a controlar los costos y desperdiciar lo mínimo posible en sus laboratorios. Estas directivas de laboratorio incluyen el número máximo de las máquinas virtuales creadas por usuario y laboratorio y varias opciones de apagado e inicio automático.

## Acceso a las directivas de laboratorio en Azure DevTest Labs
Los siguientes pasos le guiarán a través de la configuración de directivas para un laboratorio en Azure DevTest Labs:

Para ver (y cambiar) las directivas de un laboratorio, siga estos pasos:

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Seleccione **Más servicios** y luego seleccione **DevTest Labs** en la lista.
3. En la lista de laboratorios, seleccione el laboratorio que desee.
4. Seleccione **Configuración de directiva**.
5. La hoja **Configuración de directiva** contiene un menú de configuración que puede especificar:
   
    ![Hoja de configuración de directiva](./media/devtest-lab-set-lab-policy/policies.png)
   
    Para obtener más información acerca de cómo establecer una directiva, selecciónela en la lista siguiente:
   
   * [Tamaños de máquina virtual permitidos](#set-allowed-virtual-machine-sizes): seleccione la lista de tamaños de máquinas virtuales que se permiten en el laboratorio. Un usuario puede crear máquinas virtuales solo desde esta lista.
   * [Máquinas virtuales por usuario](#set-virtual-machines-per-user): especifique el número máximo de máquinas virtuales que se pueden crear por usuario.
   * [Máquinas virtuales por laboratorio](#set-virtual-machines-per-lab): especifique el número máximo de máquinas virtuales que se pueden crear por laboratorio.
   * [Apagado automático](#set-auto-shutdown): especifique la hora en que las máquinas virtuales del laboratorio actual se apagan automáticamente.
   * [Inicio automático](#set-auto-start): especifique la hora en que las máquinas virtuales del laboratorio actual se inician automáticamente.

## Establecimiento de tamaños de máquina virtual permitidos
La directiva para establecer los tamaños permitidos de la máquina virtual ayuda a minimizar la pérdida del laboratorio al permitirle especificar los tamaños de máquina virtual que se permiten en este. Si se activa esta directiva, los tamaños de máquina virtual de esta lista son los únicos que pueden utilizarse en la creación de tales máquinas.

1. En la hoja **Configuración de directiva**, elija **Allowed virtual machines sizes** (Tamaños de máquina virtual permitidos).
   
    ![Tamaños de máquina virtual permitidos](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)
2. Seleccione **Activado** para habilitar esta directiva, y **Desactivado** para deshabilitarla.
3. Si habilita esta directiva, seleccione uno o varios tamaños de máquina virtual que se pueden crear en el laboratorio.
4. Seleccione **Guardar**.

## Establecimiento de máquinas virtuales por usuario
La directiva de **Máquinas virtuales por usuario** le permite especificar el número máximo de máquinas virtuales que puede crear un usuario individual. Si un usuario intenta crear una máquina virtual una vez alcanzado el límite, aparece un mensaje de error que indica que la máquina virtual no se puede crear.

1. En la hoja **Configuración de directiva**, seleccione **Virtual machines per user** (Máquinas virtuales por usuario).
   
    ![Máquinas virtuales por usuario](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)
2. Seleccione **Activado** para habilitar esta directiva, y **Desactivado** para deshabilitarla.
3. Si habilita esta directiva, escriba un valor numérico que indique el número máximo de máquinas virtuales que se pueden crear por usuario. Si escribe un número que no sea válido, la interfaz de usuario muestra el número máximo permitido para este campo.
4. Seleccione **Guardar**.

## Establecimiento de máquinas virtuales por laboratorio
La directiva de **Máquinas virtuales por laboratorio** le permite especificar el número máximo de máquinas virtuales que se pueden crear para el laboratorio actual. Si un usuario intenta crear una máquina virtual una vez alcanzado el límite, aparece un mensaje de error que indica que la máquina virtual no se puede crear.

1. En la hoja **Configuración de directiva**, seleccione **Virtual machines per lab** (Máquinas virtuales por laboratorio).
   
    ![Máquinas virtuales por laboratorio](./media/devtest-lab-set-lab-policy/total-vms-allowed.png)
2. Seleccione **Activado** para habilitar esta directiva, y **Desactivado** para deshabilitarla.
3. Si habilita esta directiva, escriba un valor numérico que indique el número máximo de máquinas virtuales que se pueden crear para el laboratorio actual. Si escribe un número que no sea válido, la interfaz de usuario muestra el número máximo permitido para este campo.
4. Seleccione **Guardar**.

## Establecimiento del apagado automático
La directiva de apagado automático ayuda a minimizar la pérdida del laboratorio, ya que permite especificar la hora de apagado de la máquina virtual de este laboratorio.

1. En la hoja **Configuración de directiva**, seleccione **Auto-shutdown** (Apagado automático).
   
    ![Apagado automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
2. Seleccione **Activado** para habilitar esta directiva, y **Desactivado** para deshabilitarla.
3. Si habilita esta directiva, especifique la hora local para apagar todas las máquinas virtuales en el laboratorio actual.
4. Seleccione **Guardar**.
5. De manera predeterminada, una vez que se habilite, esta directiva se aplica a todas las máquinas virtuales del laboratorio actual. Para quitar esta configuración de una máquina virtual específica, abra la hoja de la máquina virtual y cambie la configuración de **Apagado automático**.

## Establecimiento del inicio automático
La directiva de inicio automático le permite especificar cuándo se deben iniciar las máquinas virtuales del laboratorio actual.

1. En la hoja **Configuración de directiva**, seleccione **Auto-start** (Inicio automático).
   
    ![Inicio automático](./media/devtest-lab-set-lab-policy/auto-start.png)
2. Seleccione **Activado** para habilitar esta directiva, y **Desactivado** para deshabilitarla.
3. Si habilita esta directiva, especifique a la hora local de inicio programado los días de la semana en los que se aplica esta hora.
4. Seleccione **Guardar**.
5. Una vez que se habilite, esta directiva no se aplica automáticamente a ninguna máquina virtual del laboratorio actual. Para aplicar esta configuración a una máquina virtual específica, abra la hoja de la máquina virtual y cambie su configuración de **Inicio automático**.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Pasos siguientes
Una vez que defina y aplique las distintas configuraciones de las directivas de máquina virtual correspondientes al laboratorio, puede intentar algunos de los siguientes pasos:

* [Configurar administración de costos](devtest-lab-configure-cost-management.md): se muestra el uso del gráfico de **tendencias de costo estimado mensual** para ver el costo estimado hasta la fecha del mes de calendario actual, así como el costo previsto para fin de mes.
* [Creación de una imagen personalizada](devtest-lab-create-template.md): cuando cree una máquina virtual, deberá especificar una base, que puede ser una imagen personalizada o una imagen de Marketplace. Este artículo muestra cómo crear una imagen personalizada desde un archivo VHD.
* [Configuración de imágenes de Marketplace](devtest-lab-configure-marketplace-images.md): Azure DevTest Labs admite la creación de máquinas virtuales basadas en imágenes de Azure Marketplace. En este artículo se muestra cómo especificar las imágenes de Azure Marketplace, si corresponde, que se pueden usar en el momento de crear máquinas virtuales en un laboratorio.
* [Creación de una máquina virtual en un laboratorio](devtest-lab-add-vm-with-artifacts.md): se muestra cómo crear una máquina virtual desde una imagen base (ya sea personalizada o de Marketplace) y cómo trabajar con artefactos en la máquina virtual.

<!---HONumber=AcomDC_0914_2016-->