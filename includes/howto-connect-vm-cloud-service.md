<properties authors="kathydav" editor="tysonn" manager="donaldg" />

# Conexión de máquinas virtuales en un Servicio en la nube

Cuando crea una máquina virtual, se crea automáticamente un servicio en la nube para dar cabida a esa máquina. Puede crear varias máquinas virtuales en el mismo servicio en la nube para permitir a las máquinas virtuales comunicarse entre sí, equilibrar la carga de tráfico de red entre las máquinas virtuales y mantener la alta disponibilidad de las máquinas.

Para obtener más información sobre la realización del equilibrio de carga de las máquinas virtuales, consulte [Equilibrio de carga de máquinas virtuales][]. Para obtener más información sobre la administración de la disponibilidad de la aplicación, consulte [Administración de la disponibilidad de las máquinas virtuales][].

Primero tendrá que crear una máquina virtual con un nuevo servicio en la nube y, a continuación, podrá conectar máquinas virtuales adicionales a la primera máquina virtual con el mismo servicio en la nube.

1.  Cree una máquina virtual siguiendo los pasos de [Creación de una máquina virtual personalizada][].

2.  Una vez que cree la primera máquina virtual personalizada, en la barra de comandos [Portal de administración][], haga clic en **New**.

    ![Crear una máquina virtual][]

3.  Haga clic en **Máquina virtual** y, a continuación, haga clic en **From Gallery**.

    ![Crear una máquina virtual personalizada][]

    Aparece el cuadro de diálogo **Select the virtual machine operating system**.

4.  En la página **Choose an image**, seleccione una imagen y, a continuación, haga clic en la flecha para continuar.

    Aparecerá la primera página de **Virtual machine configuration**.

5.  En **Virtual Machine Name**, escriba el nombre que desea usar para la máquina virtual.

6.  En **Size**, seleccione el tamaño que desea usar para la máquina virtual. El tamaño que seleccione depende de la cantidad de núcleos que se necesitan para su aplicación.

7.  En **New User Name**, escriba un nombre para la cuenta administrativa que desea usar para administrar el servidor.

8.  En **New Password**, escriba una contraseña segura para la cuenta administrativa. En **Confirm Password**, vuelva a escribir la contraseña.

9.  Puede proteger la máquina con una clave SSH en una máquina virtual que ejecute el sistema operativo Linux.

10. En **Servicio en la nube**, seleccione el servicio en la nube que contendrá la nueva máquina virtual.

11. En **Cuenta de almacenamiento**, seleccione una cuenta de almacenamiento en la que almacenar el archivo .vhd o deje el campo como está establecido de forma predeterminada para crear la cuenta de almacenamiento automáticamente. Solo se crea una cuenta de almacenamiento de forma automática. Todas las demás máquinas virtuales que crea con esta configuración se ubican en esta cuenta de almacenamiento. Tiene un límite de 20 cuentas de almacenamiento.

12. Para usar un conjunto de disponibilidad, seleccione la cuenta que creó al crear la primera máquina virtual.

13. Revise la configuración predeterminada del extremo y modifíquela si fuese necesario.

14. Haga clic en la marca de verificación para crear la máquina virtual conectada.

  [Equilibrio de carga de máquinas virtuales]: ../../articles/load-balance-virtual-machines/
  [Administración de la disponibilidad de las máquinas virtuales]: ../../articles/manage-availability-virtual-machines/
  [Creación de una máquina virtual personalizada]: ../../articles/virtual-machines-create-custom/
  [Portal de administración]: http://manage.windowsazure.com
  [Crear una máquina virtual]: ./media/howto-connect-vm-cloud-service/Create.png
  [Crear una máquina virtual personalizada]: ./media/howto-connect-vm-cloud-service/CreateNew.png
