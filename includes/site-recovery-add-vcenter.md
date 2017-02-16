* En **Add vCenter** (Agregar vCenter), especifique un nombre descriptivo para el host de vSphere o el servidor vCenter y especifique la dirección IP o el FQDN del servidor. Deje el puerto 443 a menos que los servidores de VMware estén configurados para escuchar las solicitudes en un puerto diferente. A continuación, seleccione la cuenta que va a conectar al servidor de VMware vCenter o vSphere ESXi. Haga clic en **Aceptar**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > Si agrega el servidor VMware vCenter o el host VMware vSphere con una cuenta que no tiene privilegios de administrador en el servidor vCenter o el servidor host, asegúrese de que la cuenta tiene habilitados los siguientes privilegios: Centro de datos, Almacén de datos, Carpeta, Host, Red, Recurso, Máquina virtual, Conmutador distribuido de vSphere. Además, el servidor VMware vCenter necesita el privilegio Vistas de almacenamiento.


<!--HONumber=Jan17_HO3-->


