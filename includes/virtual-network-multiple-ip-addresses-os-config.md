## <a name="a-nameos-configaadd-ip-addresses-to-a-vm-operating-system"></a><a name="os-config"></a>Incorporación de direcciones IP a un sistema operativo de la máquina virtual

Conéctese e inicie sesión en una máquina virtual que creó con múltiples direcciones IP privadas. Debe agregar manualmente todas las direcciones IP privadas (incluida la principal) que ha agregado a la máquina virtual. Complete los pasos siguientes para el sistema operativo de su máquina virtual:

### <a name="windows"></a>Windows

1. En un símbolo del sistema, escriba *ipconfig /all*.  Solo verá la dirección IP privada *principal* (por medio de DHCP).
2. Escriba *ncpa.cpl* en el símbolo del sistema para abrir la ventana **Conexiones de red**.
3. Abra las propiedades de la **Conexión de área local**.
4. Haga doble clic en el Protocolo de Internet versión 4 (IPv4).
5. Seleccione **Usar la siguiente dirección IP** y escriba los valores siguientes:

    * **Dirección IP**: escriba la dirección IP privada *principal* .
    * **Máscara de subred**: establezca este valor en función de su subred. Por ejemplo, si la subred es una subred /24, la máscara de subred es 255.255.255.0.
    * **Puerta de enlace predeterminada**: la primera dirección IP de la subred. Si la subred es 10.0.0.1/24, la dirección IP de la puerta de enlace es 10.0.0.0/24.
    * Haga clic en **Usar las siguientes direcciones de servidor DNS** y escriba los valores siguientes:
        * **Servidor DNS preferido:** escriba 168.63.129.16 si no usa su propio servidor DNS.  Si usa su propio servidor DNS, escriba la dirección IP de su servidor.
    * Haga clic en el botón **Avanzadas** y agregue más direcciones IP. Agregue cada una de las direcciones IP privadas secundarias indicadas en el paso 8 a la NIC con la misma subred especificada para la dirección IP principal.
    * Haga clic en **Aceptar** para cerrar la configuración de TCP/IP y en **Aceptar** otra vez para cerrar la configuración del adaptador. Se restablece la conexión RDP.
6. En un símbolo del sistema, escriba *ipconfig /all*. Se muestran todas las direcciones IP que agregó y DHCP está desactivado.
    
### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Abra una ventana del terminal.
2. Asegúrese de ser el usuario raíz. Si no lo es, escriba el siguiente comando:

    ```bash
    sudo -i
    ```

3. Actualice el archivo de configuración de la interfaz de red (suponiendo que sea "eth0").

    * Mantenga el elemento de línea existente para dhcp. La dirección IP principal permanece configurada que estaba.
    * Agregue una configuración para una dirección IP estática adicional con los siguientes comandos:

        ```bash
        cd /etc/network/interfaces.d/
        ls
        ```

    Debería ver un archivo .cfg.
4. Abra el archivo:

        ```bash
        vi eth0.cfg
        ```

    Debería ver las siguientes líneas al final del archivo:

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. Agregue las líneas siguientes después de las que existen en este archivo:

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    ```

6. Guarde el archivo mediante el comando siguiente:

    ```bash
    :wq
    ```

7. Restablezca la interfaz de red con el comando siguiente:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > Si usa una conexión remota, ejecute ifdown e ifup en la misma línea.
    >

8. Compruebe que la dirección IP se agregue a la interfaz de red con el comando siguiente:

    ```bash
    Ip addr list eth0
    ```

    Debería ver la dirección IP que agregó en la lista.
    
### <a name="linux-redhat-centos-and-others"></a>Linux (Redhat, CentOS y otros)

1. Abra una ventana del terminal.
2. Asegúrese de ser el usuario raíz. Si no lo es, escriba el siguiente comando:

    ```bash
    sudo -i
    ```

3. Escriba la contraseña y siga las instrucciones que aparezcan. Una vez que sea el usuario raíz, vaya a la carpeta de scripts de red con el comando siguiente:

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Muestra una lista de los archivos de ifcfg relacionados con el siguiente comando:

    ```bash
    ls ifcfg-*
    ```

    Debería ver *ifcfg-eth0* como uno de los archivos.

5. Para agregar una dirección IP, cree un archivo de configuración, tal y como se muestra a continuación. Tenga en cuenta que debe crearse un archivo para cada configuración de IP.

    ```bash
    touch ifcfg-eth0:0
    ```

6. Abra el archivo *ifcfg-eth0:0* con el comando siguiente:

    ```bash
    vi ifcfg-eth0:0
    ```

7. Agregue contenido al archivo, *eth0:0* en este caso, con el siguiente comando. Asegúrese de actualizar la información en función de su dirección IP.

    ```bash
    DEVICE=eth0:0
        BOOTPROTO=static
        ONBOOT=yes
        IPADDR=192.168.101.101
        NETMASK=255.255.255.0

    ```

8. Guarde el archivo mediante el comando siguiente:

    ```bash
    :wq
    ```

9. Reinicie los servicios de red y asegúrese de que los cambios sean correctos con los comandos siguientes:

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    Debería ver la dirección IP que agregó, *eth0:0*, en la lista que se devuelve.


<!--HONumber=Feb17_HO1-->


