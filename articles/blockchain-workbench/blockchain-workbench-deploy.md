---
title: Implementación de Azure Blockchain Workbench
description: Implementación de Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/22/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 80a40cec8ebd062751e896f9b555c5ed5464d7a3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-azure-blockchain-workbench"></a>Implementación de Azure Blockchain Workbench

Azure Blockchain Workbench se implementa mediante una plantilla de solución de Azure Marketplace. La plantilla simplifica la implementación de los componentes necesarios para crear aplicaciones de cadena de bloques. Una vez implementado, Blockchain Workbench proporciona acceso a las aplicaciones cliente para crear y administrar usuarios y aplicaciones de cadena de bloques.

Para más información acerca de los componentes de Blockchain Workbench, consulte [Arquitectura de Azure Blockchain Workbench](blockchain-workbench-architecture.md).

## <a name="prepare-for-deployment"></a>Preparación de la implementación

Azure Blockchain Workbench necesita varios requisitos previos antes de la implementación. Entre estos requisitos previos se incluye la configuración de Azure AD y los registros de aplicaciones.

### <a name="blockchain-workbench-api-app-registration"></a>Registro de aplicación de API de Blockchain Workbench

La implementación de Blockchain Workbench requiere el registro de una aplicación de Azure AD. Necesita un inquilino de Azure Active Directory (Azure AD) para registrar la aplicación. Puede usar un inquilino existente o crear uno nuevo. Si va a usar un inquilino de Azure AD ya existente, necesitará suficientes permisos para registrar aplicaciones en un inquilino de Azure AD. Los registros de la aplicación deben estar en el inquilino del administrador de la suscripción en la que se ha implementado Workbench. Para más información sobre los inquilinos de Azure AD, consulte [Obtención de un inquilino de Azure Active Directory](../active-directory/develop/active-directory-howto-tenant.md) e [Integración de aplicaciones con Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Seleccione su cuenta en la esquina superior derecha y cambie al inquilino de Azure AD que desee. El inquilino debe ser el inquilino del administrador de la suscripción en la que está implementado Workbench y tener permisos suficientes para registrar aplicaciones.
3. En el panel de navegación izquierdo, seleccione el servicio **Azure Active Directory**. Seleccione **Registros de aplicaciones** > **Nuevo registro de aplicaciones**.

    ![Registro de aplicación](media/blockchain-workbench-deploy/app-registration.png)

4. Proporcione un **nombre** y una **dirección URL de inicio de sesión** para la aplicación. Puede usar valores de marcador de posición ya que los valores se cambian durante la implementación. 

    ![Crear el registro de aplicaciones](media/blockchain-workbench-deploy/app-registration-create.png)

    |Configuración  | Valor  |
    |---------|---------|
    |NOMBRE | `Blockchain API` |
    |Tipo de aplicación |Aplicación web/API|
    |URL de inicio de sesión | `https://blockchainapi` |

5. Seleccione **Crear** para registrar la aplicación de Azure AD.

### <a name="modify-application-manifest"></a>Modificación del manifiesto de aplicación

A continuación, debe modificar el manifiesto de aplicación para que use los roles de aplicación en Azure AD para especificar los administradores de Blockchain Workbench.  Para más información acerca de los manifiestos de aplicación, consulte [Manifiesto de aplicación de Azure Active Directory](../active-directory/develop/active-directory-application-manifest.md).

1. Para la aplicación que registró, seleccione **Manifiesto** en el panel de detalles de la aplicación registrada.
2. Genere un identificador único global. Puede usar el comando de PowerShell `[guid]::NewGuid()` o herramientas en línea para generar uno de estos identificadores. 
3. Va a actualizar la sección **appRoles** del manifiesto. En el panel Editar manifiesto, seleccione **Editar** y sustituya `"appRoles": []` por el código JSON que se proporciona. Asegúrese de reemplazar el valor del campo **ID** por el identificador único global que generó. 

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    ![Editar manifiesto](media/blockchain-workbench-deploy/edit-manifest.png)

    > [!IMPORTANT]
    > El valor **Administrador** es necesario para identificar los administradores de Blockchain Workbench.

4.  Haga clic en **Guardar** para guardar los cambios del manifiesto de aplicación.

### <a name="add-graph-api-key-to-application"></a>Incorporación de la clave de Graph API a la aplicación

Blockchain Workbench usa Azure AD como sistema principal de administración de identidades de los usuarios que interactúan con aplicaciones de cadena de bloques. Para que Blockchain Workbench acceda a Azure AD y recupere información sobre el usuario como nombres y correos electrónicos, debe agregar una clave de acceso. Blockchain Workbench utiliza la clave para la autenticación con Azure AD.

1. Para la aplicación que registró, seleccione **Configuración** en el panel de detalles de la aplicación registrada.
2. Seleccione **Claves**.
3. Agregue una nueva clave especificando una **descripción** de clave y seleccionando un valor de tiempo en **Expira**. 

    ![Crear clave](media/blockchain-workbench-deploy/app-key-create.png)

    |Configuración  | Valor  |
    |---------|---------|
    | DESCRIPCIÓN | `Service` |
    | Expira | Seleccione el momento de expiración |

4. Seleccione **Guardar**. 
5. Copie el valor de la clave y almacénelo para más tarde. Lo necesitará para la implementación.

    > [!IMPORTANT]
    >  Si no guarda la clave para la implementación, deberá generar una nueva. Más adelante, no se puede recuperar el valor de la clave desde el portal.

### <a name="add-graph-api-required-permissions"></a>Incorporación de los permisos necesarios de Graph API

La aplicación de API necesita solicitar permiso del usuario para acceder al directorio. Establezca el siguiente permiso necesario para la aplicación de API:

1. En el registro de la aplicación de API de Blockchain, seleccione **Configuración > Permisos necesarios > Seleccionar una API > Microsoft Graph**.

    ![Selección de una API](media/blockchain-workbench-deploy/client-app-select-api.png)

    Haga clic en **Seleccionar**.

2. En **Habilitar acceso** en **Permisos de la aplicación**, elija **Leer los perfiles completos de todos los usuarios**.

    ![Habilitar acceso](media/blockchain-workbench-deploy/client-app-read-perms.png)

    Haga clic en **Seleccionar** y, a continuación, en **Listo**.

3. En **Permisos necesarios**, seleccione **Conceder permisos** y, a continuación, seleccione **Sí** para el mensaje de comprobación.

   ![Concesión de permisos](media/blockchain-workbench-deploy/client-app-grant-permissions.png)

   La concesión de permisos permite a Blockchain Workbench acceder a los usuarios del directorio. Se requiere el permiso de lectura para buscar y agregar miembros a Blockchain Workbench.

### <a name="get-application-id"></a>Obtención del identificador de la aplicación

Se necesita el identificador de la aplicación y la información del inquilino para la implementación. Recopile y almacene la información para su uso durante la implementación.

1. Para la aplicación que registró, seleccione **Configuración** > **Propiedades**.
2.  En el panel **Propiedades**, copie y almacene los siguientes valores para su uso durante la implementación.

    ![Propiedades de la aplicación de API](media/blockchain-workbench-deploy/app-properties.png)

    | Configuración para almacenar  | Uso en la implementación |
    |------------------|-------------------|
    | Identificador de aplicación | Configuración de Azure Active Directory > Identificador de aplicación |

### <a name="create-an-azure-ad-key-vault-application"></a>Crear una aplicación de Key Vault en Azure AD

La implementación de Blockchain Workbench requiere el registro de una aplicación de Key Vault en Azure AD.

1. En el panel de navegación izquierdo de Azure Portal, seleccione el servicio **Azure Active Directory**. Seleccione **Registros de aplicaciones** > **Nuevo registro de aplicaciones**.
2. Proporcione un **nombre** y una **dirección URL de inicio de sesión** para la aplicación. Puede usar valores de marcador de posición ya que estos se pueden cambiar posteriormente durante la implementación.

    ![Creación del registro de la aplicación Key Vault](media/blockchain-workbench-deploy/key-vault-app-create.png)

    | Configuración  | Valor  |
    |---------|---------|
    | NOMBRE | `Blockchain Key Vault app` |
    | Tipo de aplicación | Aplicación web/API |
    | URL de inicio de sesión | `https://keyvaultclient |

5. Seleccione **Crear** para registrar la aplicación Key Vault en Azure AD.

### <a name="get-tenant-domain-name"></a>Obtención del nombre de dominio del inquilino

Recopile y almacene el nombre de dominio del inquilino de Active Directory donde se registran las aplicaciones. 

En el panel de navegación izquierdo, seleccione el servicio **Azure Active Directory**. Seleccione **Nombres de dominio personalizados**. Copie y almacene el nombre de dominio.

![Nombre de dominio](media/blockchain-workbench-deploy/domain-name.png)

## <a name="deploy-blockchain-workbench"></a>Implementación de Blockchain Workbench

Una vez que se han completado los pasos descritos en los requisitos previos, estará listo para implementar Blockchain Workbench. En las secciones siguientes se describe cómo implementar la plataforma.

1.  Inicie sesión en el [Azure Portal](https://portal.azure.com).
2.  Seleccione su cuenta en la esquina superior derecha y cambie al inquilino de Azure AD en el que desee implementar Azure Blockchain Workbench.
3.  En el panel izquierdo, seleccione **Crear un recurso**. Busque `Azure Blockchain Workbench` en la barra de búsqueda de **Buscar en el Marketplace**. 

    ![Barra de búsqueda en Marketplace](media/blockchain-workbench-deploy/marketplace-search-bar.png)

4.  Seleccione **Azure Blockchain Workbench**.

    ![Resultados de la búsqueda en Marketplace](media/blockchain-workbench-deploy/marketplace-search-results.png)

4.  Seleccione **Crear**.
5.  Complete la configuración básica.

    ![Creación de Azure Blockchain Workbench](media/blockchain-workbench-deploy/blockchain-workbench-settings-basic.png)

    | Configuración | DESCRIPCIÓN  |
    |---------|--------------|
    | Prefijo de recurso. | Identificador único corto para la implementación. Este valor se utiliza como base para asignar nombres a los recursos. |
    | Nombre de usuario de máquina virtual | El nombre de usuario se utiliza como administrador de todas las máquinas virtuales (VM). |
    | Tipo de autenticación | Seleccione si desea utilizar una contraseña o clave para conectarse a las máquinas virtuales. |
    | Password | La contraseña se usa para conectarse a las máquinas virtuales. |
    | SSH | Use una clave pública RSA en formato de una sola línea con **ssh-rsa** o utilice el formato PEM de varias líneas. Puede generar claves SSH mediante `ssh-keygen` en Linux y OS X o PuTTYGen en Windows. Para más información sobre las claves SSH, consulte [Uso de claves SSH con Windows en Azure](../virtual-machines/linux/ssh-from-windows.md). |
    | Contraseña de base de datos / Confirmar contraseña de la base de datos | Especifique la contraseña que se utilizará para acceder a la base de datos creada como parte de la implementación. |
    | La suscripción | Especifique la suscripción de Azure que desea usar para la implementación. |
    | Grupos de recursos | Cree un nuevo grupo de recursos seleccionando **Crear nuevo** y especifique un nombre de grupo de recursos único. |
    | Ubicación | Especifique la región en la que desea implementar la plataforma. |

6.  Seleccione **Aceptar** para finalizar la sección de configuración básica.

7.  Complete la **configuración de Azure Active Directory**.

    ![Configuración de Azure AD](media/blockchain-workbench-deploy/blockchain-workbench-settings-aad.png)

    | Configuración | DESCRIPCIÓN  |
    |---------|--------------|
    | Nombre de dominio | Use el inquilino de Azure AD que recopiló en la sección [Obtención del nombre de dominio del inquilino](#get-tenant-domain-name) de los requisitos previos. |
    | Identificador de aplicación | Use el identificador de aplicación del registro de aplicación cliente de Blockchain que recopiló en la sección [Obtención del identificador de la aplicación](#get-application-id) de los requisitos previos. |
    | Clave de aplicación | Use la clave de aplicación del registro de aplicación cliente de Blockchain que recopiló en la sección [Incorporación de la clave de Graph API a la aplicación](#add-graph-api-key-to-application) de los requisitos previos. |


8.  Haga clic en **Aceptar** para finalizar la sección de configuración de los parámetros de Azure AD.

9.  Complete la configuración de la opción **Tamaño y rendimiento de red**.

    ![Valores de red y de rendimiento](media/blockchain-workbench-deploy/blockchain-workbench-settings-network.png)

    | Configuración | DESCRIPCIÓN  |
    |---------|--------------|
    | Número de nodos de la cadena de bloques | Elija el número de nodos del control de servidor de validación Ethereum PoA que se van a implementar en la red. |
    | Rendimiento del almacenamiento | Elija el rendimiento de almacenamiento preferido de la máquina virtual para la red de cadena de bloques. |
    | Tamaño de la máquina virtual | Elija el tamaño preferido de la máquina virtual para la red de cadena de bloques. |

10. Haga clic en **Aceptar** para completar la sección sobre tamaño y rendimiento de red.

11. Complete la configuración de **Azure Monitor**.

    ![Azure Monitor](media/blockchain-workbench-deploy/blockchain-workbench-settings-oms.png)

    | Configuración | DESCRIPCIÓN  |
    |---------|--------------|
    | Supervisión | Elija si desea emplear Azure Monitor para supervisar la red de la cadena de bloques. |
    | Conexión a una instancia de OMS existente | Elija si desea usar una instancia de Operations Management Suite (OMS) ya existente o crear una nueva. 

12. Haga clic en **Aceptar** para completar la sección de Azure Monitor.

13. Revise el resumen para comprobar que los parámetros son precisos.

    ![Resumen](media/blockchain-workbench-deploy/blockchain-workbench-summary.png)

14. Seleccione **Crear** para aceptar los términos e implementar Azure Blockchain Workbench.

La implementación puede tardar hasta 90 minutos. Puede usar Azure Portal para supervisar el progreso. En el grupo de recursos recién creado, seleccione **Implementaciones > Información general** para ver el estado de los artefactos implementados.

## <a name="blockchain-workbench-web-url"></a>Dirección URL web de Blockchain Workbench

Cuando se haya completado la implementación de Blockchain Workbench, un nuevo grupo de recursos contendrá los recursos de este. Se puede acceder a los servicios de Blockchain Workbench a través de una dirección URL web. Los pasos siguientes muestran cómo recuperar la dirección URL web de la plataforma implementada.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el panel de navegación de la izquierda, seleccione **Grupos de recursos**.
3. Elija el nombre del grupo de recursos que especificó al implementar Blockchain Workbench.
4. Haga clic en el encabezado de columna **TIPO** para ordenar la lista alfabéticamente por tipo.
5. Hay dos recursos del tipo **App Service**. Seleccione el recurso del tipo **App Service** *sin* el sufijo "-api".

    ![Lista de App Service](media/blockchain-workbench-deploy/resource-group-list.png)

6.  En la sección **Información esencial** de App Service, copie el valor **URL** que representa la dirección URL web a la instancia de Blockchain Workbench implementada.

    ![Información esencial de App Service](media/blockchain-workbench-deploy/app-service.png)

## <a name="configuring-the-reply-url"></a>Configuración de la dirección URL de respuesta

Una vez que se ha implementado Azure Blockchain Workbench, el siguiente paso es asegurarse de que la aplicación cliente de Azure Active Directory (Azure AD) se ha registrado en la **dirección URL de respuesta** correcta de la dirección URL web de la instancia de Blockchain Workbench implementada.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Compruebe que se encuentra en el inquilino en el que se ha registrado la aplicación cliente de Azure AD.
3. En el panel de navegación izquierdo, seleccione el servicio **Azure Active Directory**. Seleccione **App registrations** (Registros de aplicaciones).
4. Seleccione la aplicación cliente de Azure AD que registró en la sección de requisitos previos.
5. Seleccione **Configuración > URL de respuesta**.
6. Especifique la dirección URL web principal de la implementación de Azure Blockchain Workbench que recuperó en la sección **Obtención de la dirección URL web de Azure Blockchain Workbench**. La dirección URL de respuesta tiene como prefijo `https://`.  Por ejemplo: `https://myblockchain2-7v75.azurewebsites.net`

    ![URL de respuesta](media/blockchain-workbench-deploy/configure-reply-url.png)

7. Seleccione **Guardar** para actualizar el registro del cliente.

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene Azure Blockchain Workbench implementado, el paso siguiente es [administrar los usuarios de Azure Blockchain Workbench](blockchain-workbench-manage-users.md).