<properties  writer="josephd" editor="tysonn" manager="dongill" />
# Equilibrio de carga de máquinas virtuales

Todas las máquinas virtuales que se crean en Azure pueden comunicarse
automáticamente mediante un canal de red privada con otras máquinas
virtuales del mismo servicio en la nube o de la misma red virtual. El
resto de comunicación entrante, como el tráfico iniciado desde hosts de
Internet o máquinas virtuales de otros servicios de nube o redes
virtuales, requiere un extremo.

Los extremos pueden utilizarse con diferentes objetivos. El uso y la
configuración predeterminados de extremos en una máquina virtual que
cree con el Portal de administración de Azure son el Protocolo de
Escritorio remoto y el tráfico de sesión de Windows PowerShell remoto.
Estos extremos le permiten administrar la máquina virtual de manera
remota a través de Internet.

Otro uso de los extremos es la configuración del equilibrador de carga
de Azure para distribuir un tipo específico de tráfico entre varios
servicios o máquinas virtuales. Por ejemplo, puede extender la carga del
tráfico de solicitudes web entre varios servidores web o roles web.

A cada extremo definido para una máquina virtual se le asigna un puerto
público y privado, del tipo TCP o UDP. Los hosts de Internet envían su
tráfico entrante a la dirección de IP pública del servicio de la nube y
un puerto público. Las máquinas virtuales y los servicios en el servicio
de la nube escuchan en su dirección IP y puerto privados. El
equilibrador de carga asigna la dirección IP y el número de puerto
públicos de tráfico entrante a la dirección IP y el número de puerto
privados de la máquina virtual y viceversa para el tráfico de respuesta
desde la máquina virtual.

Al configurar el equilibrio de carga del tráfico entre varias máquinas
virtuales o servicios, Azure distribuye el tráfico entrante de manera
aleatoria.

Para los servicios en la nube que contienen instancias de roles web o de
roles de trabajo, puede definir un extremo público en la definición del
servicio. Para los servicios en la nube que contienen máquinas
virtuales, puede agregar un extremo a una máquina virtual en el momento
de crearla o bien puede agregar el extremo en un momento posterior.

En la siguiente imagen se muestra un extremo con equilibrio de carga
para tráfico web estándar (sin cifrar) compartido entre tres máquinas
virtuales para el puerto TCP público y privado de 80. Estas tres
máquinas virtuales se encuentran en un conjunto con equilibrio de carga.

![equilibrio de carga](./media/load-balancing-vms/LoadBalancing.png)

Cuando los clientes de Internet envían solicitudes de página web a la
dirección IP pública del servicio de la nube y el puerto 80 de TCP, el
equilibrador de carga realiza un equilibrio aleatorio de estas
solicitudes entre las tres máquinas virtuales del conjunto con
equilibrio de carga.

Para crear un conjunto con equilibrio de carga de máquinas virtuales
Azure, siga estos pasos:

* [Paso 1: Crear la primera máquina virtual](#firstmachine)
* [Paso 2: Crear máquinas virtuales adicionales en el mismo servicio en
  la nube](#addmachines)
* [Paso 3: Crear un conjunto con equilibrio de carga con la primera
  máquina virtual](#loadbalance)
* [Paso 4: Agregar máquinas virtuales al conjunto con equilibrio de
  carga](#addtoset)
## <a  id="firstmachine"> </a>Paso 1: Crear la primera máquina virtual

Si no lo ha hecho todavía, inicie sesión en el [Portal de administración
de Azure][1]. Para crear la primera máquina virtual, puede utilizar From
Gallery o el método Quick Create.

* **From Gallery**: Este método le permite crear extremos cuando crea
  la máquina virtual, y le permite especificar un nombre para el
  servicio en la nube que se crea al crear la máquina virtual. Para
  obtener instrucciones, consulte [Creación de una máquina virtual que
  ejecuta Linux](../virtual-machines-linux-tutorial) o [Creación de una
  máquina virtual que ejecuta Windows
  Server](../virtual-machines-windows-tutorial).

* **Quick Create**: Para crear una máquina virtual se selecciona una
  imagen de la Galería de imágenes y se proporciona información básica.
  Si utiliza este método, deberá agregar el extremo después de crear la
  máquina virtual. Este método también crea un servicio en la nube
  usando un nombre predeterminado. Para obtener más información,
  consulte [Creación rápida de una máquina
  virtual](../virtual-machines-quick-create).

**Nota**: después de crear la máquina virtual con Quick Create, la
página Servicios en la nube del Portal de administración muestra el
nombre del servicio en la nube junto con otra información acerca del
servicio.
## <a  id="addmachines"> </a>Paso 2: Crear máquinas virtuales adicionales en el mismo servicio en la nube

Cree las máquinas virtuales adicionales en el mismo servicio de nube que
la primera máquina virtual utilizando el método From Gallery.
## <a  id="loadbalance"> </a>Paso 3: Crear un conjunto con equilibrio de carga con la primera máquina virtual

1.  En el Portal de administración de Azure, haga clic en **Máquinas
    virtuales** y, a continuación, en el nombre de la primera máquina
    virtual.

2.  Seleccione **Endpoints** y, a continuación, haga clic en **Add**.

3.  En la página Add an endpoint to a virtual machine, haga clic en la
    flecha derecha.

4.  En la página Specify the details of the endpoint:
    
    * En **Name**, escriba un nombre para el extremo o seleccione uno de
      la lista de extremos predefinidos para protocolos comunes.
    * En **Protocol**, seleccione el protocolo que requiere el tipo de
      extremo, TCP o UDP, según sea necesario.
    * En **Public Port** y **Private Port**, escriba los números de
      puerto que desee que utilice la máquina virtual, según sea
      necesario. Puede utilizar el puerto privado y las reglas de
      firewall en la máquina virtual para redirigir el tráfico de la
      manera más adecuada para su aplicación. El puerto privado puede
      ser el mismo que el puerto público. Por ejemplo, para un extremo
      para tráfico web (HTTP), puede asignar el puerto 80 al puerto
      público y el privado.

5.  Haga clic en **Create a load-balanced set** y, a continuación, en la
    flecha derecha.

6.  En la página Configure the load-balanced set, especifique un nombre
    para el conjunto con equilibrio de carga y, a continuación, asigne
    los valores para probar el comportamiento del equilibrador de carga
    de Azure. El equilibrador de carga utiliza pruebas para determinar
    si las máquinas virtuales del conjunto con equilibrio de carga están
    disponibles para recibir tráfico entrante.

7.  Haga clic en la marca de verificación para crear el extremo con
    equilibrio de carga. Verá **Yes** en la columna **Load-balanced set
    name** de la página **Endpoints** de la máquina virtual.
## <a  id="addtoset"> </a>Paso 4: Agregar máquinas virtuales al conjunto con equilibrio de carga

Después de crear el conjunto con equilibrio de carga, agréguele las
demás máquinas virtuales. Para cada máquina virtual del mismo servicio
en la nube:

1.  En el Portal de administración, haga clic en **Máquinas virtuales**,
    en el nombre de la nueva máquina virtual, en **Endpoints** y,
    finalmente, en **Add**.

2.  En la página Add an endpoint to a virtual machine, haga clic en
    **Add endpoint to an existing load-balanced set**, seleccione el
    nombre del conjunto con equilibrio de carga y, a continuación, haga
    clic en la flecha derecha.

3.  En la página Specify the details of the endpoint, escriba un nombre
    para el extremo y, a continuación, haga clic en la marca de
    verificación.

<!-- LINKS -->



[1]: http://manage.windowsazure.com
