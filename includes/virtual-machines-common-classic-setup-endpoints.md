
Cada punto de conexión cuenta con un *puerto público* y un *puerto privado*:

* El puerto público que usa el equilibrador de carga de Azure para escuchar el tráfico que entra a la máquina virtual desde Internet.
* La máquina virtual usa el puerto privado para escuchar el tráfico entrante, normalmente destinado a una aplicación o servicio que se ejecuta en la máquina virtual.

Se proporcionan los valores predeterminados del protocolo IP y de los puertos TCP o UDP de los protocolos de red conocidos al crear puntos de conexión con Azure Portal. Para los extremos personalizados, deberá especificar el protocolo IP correcto (TCP o UDP) y los puertos públicos y privados. Para distribuir el tráfico entrante de forma aleatoria entre varias máquinas virtuales, deberá crear un conjunto con equilibrio de carga que conste de varios extremos.

Tras la creación de un extremo, puede utilizar una lista de control de acceso (ACL) para definir reglas que permitan o denieguen el tráfico entrante al puerto público del extremo, en función de su dirección IP de origen. Sin embargo, si la máquina virtual está en una red virtual de Azure, debería usar grupos de seguridad de red en su lugar. Para obtener más información, consulte [Información sobre los grupos de seguridad de red](../articles/virtual-network/virtual-networks-nsg.md).

> [!NOTE]
> La configuración del firewall para máquinas virtuales de Azure se realiza automáticamente para los puertos asociados con los puntos de conexión de conectividad remotos que Azure configura automáticamente. Para los puertos especificados para todos los demás extremos, no se realiza ninguna configuración automáticamente en el firewall de la máquina virtual. Cuando se crea un extremo para la máquina virtual, deberá asegurarse de que el firewall de la máquina virtual también permite el tráfico para el protocolo y el puerto privado correspondiente a la configuración del extremo. Para configurar el firewall, consulte la documentación o la Ayuda en línea para el sistema operativo que se ejecuta en la máquina virtual.
>
>

## <a name="create-an-endpoint"></a>Creación de un extremo
1. Si aún no lo ha hecho, inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Haga clic en **Máquinas virtuales**y haga clic en el nombre de la máquina virtual que desea configurar.
3. Haga clic en **Puntos de conexión** en el grupo **Configuración**. En la página **Puntos de conexión** se enumeran todos los puntos de conexión actuales de la máquina virtual. (Este ejemplo es una VM Windows. Una VM Linux mostrará de forma predeterminada un punto de conexión para SSH).

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![Extremos](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)

4. En la barra de comandos, encima de las entradas de puntos de conexión, haga clic en **Agregar**.
5. En la página **Agregar punto de conexión**, escriba el nombre del punto de conexión en **Nombre**.
6. En **Protocolo**, elija **TCP** o **UDP**.
7. En **Puerto público**, escriba el número de puerto para el tráfico que entra desde Internet. En **Puerto privado**, escriba el número de puerto en el que escucha la máquina virtual. Estos números de puerto pueden ser diferentes. Asegúrese de que el firewall de la máquina virtual se ha configurado para que permita el tráfico correspondiente al protocolo (en el paso 6) y el puerto privado.
10. Haga clic en **Aceptar**.

El nuevo punto de conexión se mostrará en la página **Puntos de conexión** .

![Creación correcta del extremo](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>Administración de la ACL en un extremo
Para definir el conjunto de equipos que pueden enviar tráfico, la ACL en un extremo puede restringir el tráfico en función de la dirección IP de origen. Siga estos pasos para agregar, modificar o quitar una ACL en un extremo.

> [!NOTE]
> Si el extremo forma parte de un conjunto con equilibrio de carga, los cambios que realice en la ACL en un extremo se aplican a todos los extremos del conjunto.
>
>

Si la máquina virtual está en una red virtual de Azure, es recomendable usar grupos de seguridad de red en lugar de ACL. Para obtener más información, consulte [Información sobre los grupos de seguridad de red](../articles/virtual-network/virtual-networks-nsg.md).

1. Si aún no lo ha hecho, inicie sesión en el Portal de Azure.
2. Haga clic en **Máquinas virtuales**y haga clic en el nombre de la máquina virtual que desea configurar.
3. Haga clic en **Extremos**. Seleccione el extremo apropiado de la lista. La lista de ACL está en la parte inferior de la página.

   ![Especificar los detalles de ACL](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. Use las filas de la lista para agregar, eliminar o editar las reglas de una ACL y cambiar su orden. El valor de la **Subred remota** es un intervalo de direcciones IP para el tráfico entrante de Internet que el equilibrador de carga de Azure usa para permitir o denegar el tráfico en función de la dirección IP de origen. Asegúrese de especificar el intervalo de direcciones IP en formato CIDR, también conocido como formato de prefijo de dirección. Un ejemplo es `10.1.0.0/8`.

 ![Nueva entrada de ACL](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


Puede usar reglas para permitir solo el tráfico desde equipos específicos correspondientes a los equipos en Internet o para denegar el tráfico desde intervalos concretos de direcciones conocidas.

Las reglas se evalúan en orden, comenzando por la primera regla y terminando por la última. Esto significa que las reglas deben estar ordenadas de menos restrictivas a más restrictivas. Para obtener ejemplos y más información, consulte [¿Qué es una lista de control de acceso (ACL) de extremo?](../articles/virtual-network/virtual-networks-acl.md)
