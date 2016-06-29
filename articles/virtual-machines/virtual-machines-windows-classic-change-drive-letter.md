<properties
	pageTitle="Conversión de la unidad D de una máquina virtual en un disco de datos | Microsoft Azure"
	description="Se describe cómo cambiar las letras de unidad de una máquina virtual de Windows para poder usar la unidad D: como unidad de datos."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2016"
	ms.author="cynthn"/>

# Uso de la unidad de disco D como unidad de datos en una máquina virtual de Windows 

Si su aplicación necesita usar la unidad D para almacenar datos, siga estas instrucciones para usar una unidad distinta para el disco temporal. Nunca use el disco temporal para almacenar los datos que desee conservar.

Si se cambia el tamaño o se selecciona **Detener (desasignar)** una máquina virtual, se puede desencadenar la colocación de la máquina virtual en un hipervisor nuevo. Un evento de mantenimiento planificado o no planificado también puede desencadenar esta colocación. En este escenario, el disco temporal se reasignará a la primera letra de unidad disponible. Si tiene una aplicación que necesita específicamente la unidad D:, debe seguir estos pasos para mover temporalmente el archivo pagefile.sys, adjuntar un nuevo disco de datos, asignarle la letra D y devolver el archivo pagefile.sys a la unidad temporal. Cuando lo haya hecho, Azure no devolverá D: si la máquina virtual se desplaza a un hipervisor diferente.

Para obtener más información sobre cómo usa Azure el disco temporal, consulte [Understanding the temporary drive on Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/) (Descripción de la unidad temporal en máquinas virtuales de Microsoft Azure).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Conectar el disco de datos

En primer lugar, deberá conectar el disco de datos a la máquina virtual.

- Para usar el portal, consulte [How to attach a data disk in the Azure portal](virtual-machines-windows-attach-disk-portal.md) (Cómo conectar un disco de datos en el Portal de Azure).
- Para usar el portal clásico, consulte [How to attach a data disk to a Windows virtual machine](virtual-machines-windows-classic-attach-disk.md) (Cómo conectar un disco de datos a una máquina virtual de Windows). 


## Mover temporalmente el archivo pagefile.sys a la unidad C

1. Conexión a una máquina virtual. 

2. Haga clic con el botón derecho en el menú **Inicio** y seleccione **Sistema**.

3. En el menú que aparece a la izquierda, seleccione **Configuración avanzada del sistema**.

4. En la sección **Rendimiento**, seleccione **Configuración**.

5. Seleccione la pestaña **Opciones avanzadas**.

5. En la sección **Memoria virtual**, seleccione **Cambiar**.

6. Seleccione la unidad **C**, luego haga clic en **Tamaño administrado por el sistema** y, a continuación, haga clic en **Establecer**.

7. Seleccione la unidad **D**, luego haga clic en **Sin archivo de paginación** y, a continuación, haga clic en **Establecer**.

8. Haga clic en Aplicar. Aparecerá una advertencia que indica que se debe reiniciar el equipo para que los cambios entren en vigor.

9. Reinicie la máquina virtual.




## Cambiar las letras de las unidades 

1. Una vez que se reinicia la máquina virtual, vuelva a iniciar sesión en ella.

2. Haga clic en el menú **Inicio**, escriba **diskmgmt.msc** y presione Entrar. Se iniciará la Administración de discos.

3. Haga clic con el botón derecho en **D**, la unidad de almacenamiento temporal, y seleccione **Cambiar la letra y rutas de acceso de unidad**.

4. En Letra de unidad, seleccione la unidad **G** y haga clic en **Aceptar**.

5. Haga clic con el botón derecho en el disco de datos y seleccione **Cambiar la letra y rutas de acceso de unidad**.

6. En Letra de unidad, seleccione la unidad **D** y haga clic en **Aceptar**.

7. Haga clic con el botón derecho en **G**, la unidad de almacenamiento temporal, y seleccione **Cambiar la letra y rutas de acceso de unidad**.

8. En Letra de unidad, seleccione la unidad **E** y haga clic en **Aceptar**.

> [AZURE.NOTE] Si la máquina virtual tiene otros discos o unidades, use el mismo método para reasignar las letras de unidad de los demás discos y unidades. La configuración de disco puede estar en las siguientes ubicaciones:
>- C: disco del sistema operativo  
>- D: disco de datos  
>- E: disco temporal



## Devolver el archivo pagefile.sys a la unidad de almacenamiento temporal 

1. Haga clic con el botón derecho en el menú **Inicio** y seleccione **Sistema**.

2. En el menú que aparece a la izquierda, seleccione **Configuración avanzada del sistema**.

3. En la sección **Rendimiento**, seleccione **Configuración**.

4. Seleccione la pestaña **Opciones avanzadas**.

5. En la sección **Memoria virtual**, seleccione **Cambiar**.

6. Seleccione la unidad del sistema operativo **C**, haga clic en **Sin archivo de paginación** y, a continuación, haga clic en **Establecer**.

7. Seleccione la unidad de almacenamiento temporal **E**, haga clic en **Tamaño administrado por el sistema** y, a continuación, haga clic en **Establecer**.

8. Haga clic en **Apply**. Aparecerá una advertencia que indica que se debe reiniciar el equipo para que los cambios entren en vigor.

9. Reinicie la máquina virtual.




## Pasos siguientes
- Puede aumentar el almacenamiento disponible para la máquina virtual [conectando un disco de datos adicional](virtual-machines-windows-attach-disk-portal.md).

<!---HONumber=AcomDC_0615_2016-->