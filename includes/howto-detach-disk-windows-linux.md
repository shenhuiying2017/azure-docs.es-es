<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />
# Desacoplamiento de un disco de datos de una máquina virtual

#

* [Conceptos](#concepts)
* [Localización de discos acoplados a una máquina virtual](#finddisks)
* [Desacoplamiento de un disco de datos](#detachdisk)
## <a  id="concepts"> </a>Conceptos

Una máquina virtual en Azure utiliza distintos tipos de discos, como un
disco del sistema operativo, un disco temporal local y discos de datos
opcionales. Puede acoplar un disco de datos a una máquina virtual para
almacenar datos de la aplicación. Un disco de datos es un disco duro
virtual (VHD) que puede crear localmente con su propio equipo o en la
nube con Azure.

Puede acoplar y desacoplar discos de datos en el momento que desee, pero
cuenta con una limitación en cuanto al número de discos que puede
acoplar a una máquina virtual según el tamaño de la misma.

Cuando ya no necesite un disco de datos conectado a una máquina virtual,
podrá desacoplarlo fácilmente. Con este proceso no se elimina el disco
del almacenamiento. Si desea volver a usar los datos existentes en el
disco, puede acoplar fácilmente el disco de nuevo a la misma máquina
virtual o acoplarlo a una nueva máquina virtual.

Para obtener más información sobre el uso de discos de datos, consulte
esta información sobre [Administrar discos e imágenes][1].
## <a  id="finddisks"> </a>Localización de discos acoplados a una máquina virtual

Puede buscar los discos acoplados a una máquina virtual usando el Panel
o la página Disks de Máquinas virtuales.
### Uso del panel para buscar información sobre los discos acoplados

1.  Si no lo ha hecho todavía, inicie sesión en el [Portal de
    administración][2] de Azure.

2.  Haga clic en **Máquinas virtuales** y, a continuación, seleccione la
    máquina virtual correspondiente.

3.  Haga clic en **Panel**. En el panel de la máquina virtual puede
    encontrar el número de discos acoplados y los nombres de los discos.
    En el ejemplo siguiente se muestra un disco de datos acoplado a una
    máquina virtual:
    
    ![Buscar disco de
    datos](./media/howto-detach-disk-windows-linux/FindDataDisks.png)

**Nota:** al menos un disco se encuentra acoplado a todas las máquinas
virtuales. Cada máquina virtual dispone de un disco del sistema
operativo acoplado que no puede desacoplar sin eliminar la máquina
virtual. El disco local temporal no aparece en la sección de discos
porque no es persistente.
### Uso de la página Discos de Máquinas virtuales para buscar información sobre los discos conectados

1.  Si no lo ha hecho todavía, inicie sesión en el [Portal de administración][2] de Azure.

2.  Haga clic en **Máquinas virtuales** y, a continuación, haga clic en
    **Disk**. Esta página muestra una lista de todos los discos que se
    encuentran disponibles para usarse con máquinas virtuales y los
    discos que utilizan las máquinas virtuales. La lista es una
    combinación de discos de datos y discos del sistema operativo. Use
    el panel para diferenciar entre los dos tipos de discos acoplados a
    la máquina virtual.
    
    **Nota:** cuando acople un nuevo disco de datos a una máquina
    virtual, puede asignar un nombre al archivo .vhd para el disco, pero
    Azure asigna el nombre del disco. El nombre consta del nombre del
    servicio en la nube, el nombre de la máquina virtual y un
    identificador numérico.
## <a  id="detachdisk"> </a>Desacoplamiento de un disco de datos

Una vez que encuentre el nombre del disco que desea desacoplar de la
máquina virtual, puede realizar los siguientes pasos para completar el
proceso.

1.  Si no lo ha hecho todavía, inicie sesión en el Portal de
    administración de Azure.

2.  Haga clic en **Máquinas virtuales**, seleccione la máquina virtual
    que disponga del disco de datos que desea desacoplar y, a
    continuación, haga clic en **Detach The Disk**.

3.  Seleccione el disco de datos y, a continuación, haga clic en la
    marca de verificación para desacoplarlo.
    
    ![Detalles de desacoplamiento del
    disco](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)
    
    El disco permanece en el almacenamiento pero ya no está acoplado a
    una máquina virtual.

Ahora puede acoplar de nuevo el disco a la misma máquina virtual o a una
máquina nueva. Para conocer las instrucciones, consulte [Acoplamiento de
un disco de datos a una máquina
virtual](/en-us/manage/windows/how-to-guides/attach-a-disk/).



[1]: http://go.microsoft.com/fwlink/p/?LinkId=263439
[2]: http://manage.windowsazure.com
