1. En el explorador, vaya a la máquina virtual de Jenkins. Puesto que el panel de Jenkins no es accesible a través de HTTP no segura, aparece un mensaje que indica que debe utilizar SSH para tunelizar la máquina virtual.

    ![Jenkins proporciona información que explica cómo utilizar SSH para conectarse a la máquina virtual de Jenkins.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ssh-instructions.png)

1. Abra una ventana de terminal o bash, con acceso a SSH.

1. Escriba el siguiente comando `ssh`, reemplazando los marcadores de posición **&lt;username>** y **&lt;domain>** con los valores especificados al crear la máquina virtual de Jenkins:

    ```bash
    ssh -L 127.0.0.1:8080:localhost:8080 <username>@<domain>.eastus.cloudapp.azure.com
    ```

1. Siga las indicaciones de `ssh` para iniciar sesión en la máquina virtual de Jenkins.

1. Escriba el siguiente comando para recuperar la contraseña inicial necesaria para desbloquear Jenkins.

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. Copiar en el portapapeles la contraseña que se muestra en la ventana de terminal o bash.

1. Abra el explorador y vaya a `http://localhost:8080`.

1. Pegue la contraseña que recuperó anteriormente en el campo **Contraseña administrativa** y, después, seleccione **Continuar**.

    ![Jenkins almacena una contraseña inicial que debe usar para desbloquear la máquina virtual de Jenkins la primera vez que se conecte a la misma.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-unlock.png)

1. Seleccione **Install suggested plugins** (Instalar complementos sugeridos).

    ![Jenkins le permite instalar fácilmente una colección de complementos sugeridos cuando entra por primera vez.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-customize.png)

1. En la página **Create First Admin User** (Primero usuario administrador), cree el usuario administrativo y una contraseña para el panel de Jenkins y seleccione **Guardar y finalizar**.

1. En la página **Jenkins is ready!** (Jenkins ya está listo), seleccione **Start using Jenkins** (Empezar a usar Jenkins).

    ![Una vez que Jenkins está instalado y configurado para el acceso, puede empezar a usarlo para crear y compilar trabajos.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ready.png)