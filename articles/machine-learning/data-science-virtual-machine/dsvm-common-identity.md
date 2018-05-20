---
title: Configuración de una identidad común para Data Science Virtual Machine en Azure | Microsoft Docs
description: Configuración de una identidad común en entornos de DSVM de un equipo de empresa.
keywords: aprendizaje profundo, AI, herramientas de ciencia de datos, data science virtual machine, análisis geoespacial, proceso de ciencia de datos en equipo
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 431d3079c0d942c15ccbc8352261ccfe1f5f6e47
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="setup-common-identity-on-the-data-science-vm"></a>Configuración de una identidad común en la instancia de Data Science VM

De forma predeterminada, en las máquinas virtuales de Azure que incluyen Data Science VM (DSVM), se crean cuentas de usuario locales mientras se aprovisiona la máquina virtual y los usuarios se autentican en ella con estas credenciales. Si tiene varias máquinas virtuales a las que necesita acceder, este enfoque puede volverse complicado para administrar las credenciales. Las cuentas de usuario comunes y la administración mediante un proveedor de identidades basado en estándares le permiten usar un único conjunto de credenciales para acceder a varios recursos de Azure, lo que incluye varias DSVM. 

Active Directory (AD) es un conocido proveedor de identidades y se admite tanto en Azure como servicio como en el entorno local. Puede aprovechar AD o Azure AD para autenticar a los usuarios en una instancia de Data Science VM (DSVM) independiente o en un clúster de DSVM en un conjunto de escalado de máquinas virtuales de Azure. Para ello se unen las instancias de DSVM a un dominio de AD. Si ya tiene una instancia de Active Directory para administrar las identidades, puede usarla como proveedor de identidad común. En caso contrario, puede ejecutar una instancia de AD administrada en Azure mediante un servicio llamado [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS). 

En la documentación de [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/) se proporcionan [instrucciones](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity) detalladas para administrar Active Directory, como la conexión de Azure AD al directorio local, si tiene uno. 

En el resto de este artículo se describen los pasos para configurar un servicio de dominio de AD completamente administrado en Azure mediante Azure AD DS y unir las instancias de DSVM al dominio de AD administrado para permitir a los usuarios acceder a un grupo de instancias de DSVM (y otros recursos de Azure) con una cuenta de usuario y unas credenciales comunes. 

##  <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Configuración de un dominio de Active Directory completamente administrado en Azure

Azure AD DS simplifica el proceso de administrar las identidades al proporcionar un servicio completamente administrado en Azure. En este dominio de Active Directory, se administran usuarios y grupos.  Los pasos para configurar un dominio de AD hospedado en Azure y las cuentas de usuarios de su directorio son:

1. Agregue usuarios a Active Directory en el portal. 

    a. Inicie sesión en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
    
    b. Seleccione **Azure Active Directory** y, después, **Usuarios y grupos**.
    
    c. En **Usuarios y grupos**, seleccione **Todos los usuarios** y **Nuevo usuario**.
   
   ![Selección del comando Agregar](./media/add-user.png)
    
    d. Especifique los detalles del usuario, como el **nombre** y el **nombre de usuario**. La parte del nombre de dominio del nombre de usuario debe ser el nombre de dominio del nombre de dominio predeterminado inicial "[nombre de dominio].onmicrosoft.com" o un [nombre de dominio personalizado](../../active-directory/add-custom-domain.md) comprobado y no federado, como "contoso.com".
    
    e. Copie o anote la contraseña de usuario generada para poder proporcionársela al usuario cuando finalice este proceso.
    
    f. También puede abrir y rellenar la información de **Perfil**, **Grupos** o **Rol del directorio** del usuario. 
    
    g. En **Usuario**, seleccione **Crear**.
    
    h. Distribuya de manera segura la contraseña generada al nuevo usuario para que pueda iniciar sesión.

2.  Cree una instancia de Azure AD Domain Services.

    Para crear una instancia de Azure ADDS, siga las instrucciones del artículo "[Habilitación de Azure Active Directory Domain Services mediante Azure Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)" (tareas de 1 a 5). Es importante que las contraseñas de usuario existentes en Active Directory se actualicen para que la contraseña de Azure AD DS esté sincronizada. También es importante agregar el DNS a Azure AD DS como se indica en la tarea 4 del artículo anterior. 

3.  Cree una subred de DSVM independiente en la red virtual creada en la tarea 2 del paso anterior.
4.  Cree una o varias instancias de Data Science VM en la subred de DSVM. 
5.  Siga las [instrucciones](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) para agregar DSVM a AD. 
6.  A continuación, monte una instancia de Azure Files compartida para hospedar su directorio particular o de cuaderno de forma que el área de trabajo se pueda montar en cualquier máquina. (Si necesita permisos de nivel de archivo estrictos, necesitará un NFS que se ejecute en una o varias máquinas virtuales).

    a. [Cree un recurso compartido de archivos de Azure](../../storage/files/storage-how-to-create-file-share.md).
    
    b. Móntelo en la DSVM de Linux. Al hacer clic en el botón "Conectar" para Azure Files en la cuenta de almacenamiento, en Azure Portal, se mostrará el comando para ejecutar en el shell de Bash en la DSVM de Linux. El comando tendrá un aspecto similar al siguiente:
```
sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
```
7.  Supongamos que ha montado su instancia de Azure Files en /data/workspace. Ahora, cree directorios para cada uno de los usuarios del recurso compartido. /data/workspace/user1, /data/workspace/user2, etc. Cree un directorio ```notebooks``` en el área de trabajo de cada usuario. 
8. Cree vínculos simbólicos para ```notebooks``` en ```$HOME/userx/notebooks/remote```.   

Ahora, tiene a los usuarios de la instancia de Active Directory hospedados en Azure y puede iniciar sesión en cualquier DSVM (tanto SSH como Jupyterhub) que se haya unido a Azure AD DS mediante las credenciales de AD. Como el área de trabajo de usuario se encuentra en la instancia de Azure Files compartida, el usuario tendrá acceso a sus cuadernos y demás trabajo desde cualquier DSVM al usar Jupyterhub. 

Para el escalado automático, puede usar el conjunto de escalado de máquinas virtuales para crear un grupo de máquinas virtuales, todas ellas unidas al dominio de esta forma y con el disco compartido montado. Los usuarios pueden iniciar sesión en cualquier máquina disponible en el conjunto de escalado de máquinas virtuales y tener acceso al disco compartido donde se guardan sus cuadernos. 

## <a name="next-steps"></a>Pasos siguientes

* [Almacenamiento seguro de las credenciales para acceder a recursos en la nube](dsvm-secure-access-keys.md)



