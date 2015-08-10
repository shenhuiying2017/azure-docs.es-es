<properties
   pageTitle="Descarga, instalación y registro del agente de Copia de seguridad de Azure | Microsoft Azure"
   description="Aprenda cómo y dónde descargar el agente de Copia de seguridad de Azure, los pasos de instalación y cómo registrar el agente de Copia de seguridad de Azure mediante las credenciales de almacén"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""/>
 <tags ms.service="backup" ms.devlang="na" ms.topic="article" ms.tgt\_pltfrm="na" ms.workload="storage-backup-recovery" ms.date="07/01/2015" ms.author="jimpark"; "aashishr"/>

# Descarga, instalación y registro del agente de Copia de seguridad de Azure
Después de crear el almacén de Copia de seguridad de Azure, debe instalarse un agente en cada uno de sus servidores locales (Windows Server, el cliente de Windows o el servidor de System Center Data Protection Manager) que le permite crear copias de seguridad de datos y aplicaciones en Azure. Este artículo describe los pasos necesarios para configurar el agente de Copia de seguridad de Azure de un servidor de Windows o de una máquina cliente de Windows.

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com/).

2. Haga clic en **Servicios de recuperación** y seleccione el almacén de copia de seguridad que desee registrar en un servidor. Aparecerá la página de inicio rápido del almacén de copia de seguridad.

    ![Inicio rápido](./media/backup-azure-backup-download-register/quickstart.png)

3. En la página Inicio rápido, haga clic en **Para Windows Server, System Center Data Protection Manager o cliente de Windows** en la opción **Descargar agente**. Haga clic en **Guardar** para copiarlo en la máquina local.

    ![Guardar agente](./media/backup-azure-backup-download-register/agent.png)

4. Una vez instalado el agente, haga doble clic en MARSAgentInstaller.exe para iniciar la instalación del agente de Copia de seguridad de Azure.

5. Elija la carpeta de instalación y la carpeta temporal requerido para el agente. La ubicación de caché especificada debe tener un espacio libre de al menos el 5% de los datos de copia de seguridad.

6.	Si usa un servidor proxy para conectarse a Internet, en la pantalla **Configuración de proxy**, especifique los detalles del servidor proxy. Si utiliza a un servidor proxy autenticado, escriba los detalles de nombre y la contraseña del usuario en esta pantalla.

7. El agente de Copia de seguridad de Azure instalará .NET Framework 4.5 y Windows PowerShell (si aún no está disponible) para completar la instalación.

8.	Una vez instalado el agente, haga clic en el botón **Continuar con el registro** para continuar con el flujo de trabajo.

    ![Registro](./media/backup-azure-backup-download-register/register.png)

9. En la pantalla de credenciales del almacén, busque y seleccione el archivo de credenciales del almacén que se descargó anteriormente.

    ![Credenciales de almacén](./media/backup-azure-backup-download-register/vc.png)

    > [AZURE.NOTE]El archivo de almacén de credenciales solo es válido durante 48 horas (después de descargarlo desde el portal). Si encuentra algún error en esta pantalla (por ejemplo "El archivo de credenciales de almacén proporcionado ha caducado"), inicie sesión en el portal de Azure y vuelva a descargar el archivo de almacén de credenciales.

    Asegúrese de que el archivo de almacén de credenciales está disponible en una ubicación a la que puede tener acceso la aplicación de instalación. Si encuentra errores relacionados con el acceso, copie el archivo de almacén de credenciales en una ubicación temporal en esta máquina y vuelva a intentar la operación.

    Si se produce un error de credencial de almacén no válida (por ejemplo, "Credenciales de almacén proporcionadas no válidas. El archivo está dañado o no tiene las últimas credenciales asociadas al servicio de recuperación", vuelva a intentar la operación después de descargar un nuevo archivo de credenciales de almacén desde el portal. Este error suele aparecer si el usuario hace clic en la opción Descargar credencial de almacén en el portal de Azure, en sucesión rápida. En este caso, solo es válido el segundo archivo de credenciales de almacén.

10. En la pantalla **Configuración de cifrado**, puede generar una frase de contraseña o proporcionarla (16 caracteres, como mínimo); no olvide guardar la frase de contraseña en una ubicación segura.

    ![Cifrado](./media/backup-azure-backup-download-register/encryption.png)

    > [AZURE.WARNING]Si la frase de contraseña se pierde u olvida; Microsoft no puede ayudar a recuperar los datos de copia de seguridad. El usuario final posee la frase de contraseña de cifrado y Microsoft no tiene ninguna visibilidad de la frase de contraseña que utiliza el usuario final. Guarde el archivo en una ubicación, ya que puede ser necesario durante una operación de recuperación.

11. Al hacer clic en el botón **Finalizar**, la máquina se ha registrado correctamente en el almacén y ahora está lista para iniciar la copia de seguridad en Microsoft Azure. Puede modificar la configuración especificada durante el flujo de trabajo de registro haciendo clic en la opción **Cambiar propiedades** en el complemento MMC de Copia de seguridad de Azure.

    ![Cambiar propiedades](./media/backup-azure-backup-download-register/change.png)

## Pasos siguientes
- Consulte [Programar copias de seguridad](backup-azure-backup-and-recover.md) para obtener información sobre la creación de copias de seguridad de datos y aplicaciones en Azure.

<!---HONumber=July15_HO5-->