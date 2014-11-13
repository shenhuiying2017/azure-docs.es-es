<properties title="Cambio de la letra de unidad del disco temporal de Windows" pageTitle="Cambio de la letra de unidad del disco temporal de Windows" description="Describe c&oacute;mo reasignar el disco temporal en una m&aacute;quina virtual de Windows en Azure." metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav"  manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="07/09/2014" ms.author="kathydav" />

# Cambio de la letra de unidad del disco temporal de Windows

Puede cambiar la letra de unidad del disco temporal si necesita usar la unidad D para otra finalidad. Lo más probable es que haga esto para admitir una aplicación o servicio que usa la unidad D como ubicación de almacenamiento permanente.

Antes de comenzar, asegúrese de tener lo siguiente:

-   Un disco de datos acoplado que puede usar para almacenar el archivo de paginación (pagefile.sys) de Windows durante este procedimiento. Para obtener instrucciones, consulte [Acoplamiento de un disco de datos a una máquina virtual de Windows][Acoplamiento de un disco de datos a una máquina virtual de Windows].
-   Un VHD cargado en la cuenta de almacenamiento, si desea usar un VHD de un disco de datos existente en la unidad D. Para obtener instrucciones, consulte los pasos 3 y 4 que se encuentran en [Creación y carga de un VHD de Windows Server en Azure][Creación y carga de un VHD de Windows Server en Azure].

## Cambio de la letra de unidad

1.  Inicie sesión en la máquina virtual.

2.  Mueva el archivo pagefile.sys de la unidad D a otra unidad.

3.  Reinicie la máquina virtual.

4.  Inicie sesión de nuevo y cambie la letra de unidad de D a E.

5.  En el [Portal de administración de Azure][Portal de administración de Azure], acople un disco de datos existente en un disco de datos vacío.

6.  Inicie sesión en la máquina virtual de nuevo, inicialice el disco y asigne la letra D como la letra de unidad para el disco que acaba de acoplar.

7.  Compruebe que la letra E está asignada al disco de almacenamiento temporal.

8.  Mueva el archivo pagefile.sys de la otra unidad a la unidad E.

## Recursos adicionales

[Inicio de sesión en una máquina virtual con Windows Server][Inicio de sesión en una máquina virtual con Windows Server]

[Desacoplamiento de un disco de datos de una máquina virtual][Desacoplamiento de un disco de datos de una máquina virtual]

[¿Qué es una cuenta de almacenamiento?][¿Qué es una cuenta de almacenamiento?]

<!--Link references-->

  [Acoplamiento de un disco de datos a una máquina virtual de Windows]: ../storage-windows-attach-disk
  [Creación y carga de un VHD de Windows Server en Azure]: ../virtual-machines-create-upload-vhd-windows-server/
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Inicio de sesión en una máquina virtual con Windows Server]: ../virtual-machines-log-on-windows-server/
  [Desacoplamiento de un disco de datos de una máquina virtual]: ../storage-windows-detach-disk/
  [¿Qué es una cuenta de almacenamiento?]: ../storage-whatis-account/
