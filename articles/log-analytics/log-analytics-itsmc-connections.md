---
title: Conexiones compatibles con IT Service Management Connector en Azure Log Analytics | Microsoft Docs
description: Conecte los productos o servicios de ITSM con IT Service Management Connector en Azure Log Analytics para supervisar y administrar de forma centralizada los elementos de trabajo de ITSM.
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 8231b7ce-d67f-4237-afbf-465e2e397105
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: v-jysur
ms.openlocfilehash: bbec5773987b29eb62d10d17b88efcda29889612
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector-preview"></a>Conexión de productos o servicios de ITSM con IT Service Management Connector (versión preliminar)
En este artículo se proporciona información sobre cómo conectar su producto o servicio de ITSM a IT Service Management Connector en OMS y administrar de forma centralizada los elementos de trabajo. Para más información sobre IT Service Management Connector, vea [Introducción](log-analytics-itsmc-overview.md).

Se admiten los siguientes productos y servicios:

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-oms)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-oms)
- [Provance](#connect-provance-to-it-service-management-connector-in-oms)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-oms"></a>Conexión de System Center Service Manager con IT Service Management Connector en OMS

En las secciones siguientes se ofrecen detalles sobre cómo conectar el producto de System Center Service Manager con IT Service Management Connector en OMS.

### <a name="prerequisites"></a>Requisitos previos

Asegúrese de que se cumplen los siguientes requisitos previos:

- IT Service Management Connector se ha instalado.
Más información:  [Agregar la solución IT Service Management Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Se implementa y se configura la aplicación web de Service Manager (aplicación web). [Aquí](#create-and-deploy-service-manager-web-app-service) se puede obtener información sobre la aplicación web.
- Conexión híbrida creada y configurada Más información: [Configuración de la conexión híbrida](#configure-the-hybrid-connection).
- Versiones admitidas de Service Manager: 2012 R2 o 2016.
- Rol de usuario: [operador avanzado](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Procedimiento de conexión

Use el siguiente procedimiento para conectar la instancia de System Center Service Manager con IT Service Management Connector:

1. Vaya a **OMS** >**Configuración** > **Orígenes conectados**.
2. Seleccione **Conector ITSM**, haga clic en **Agregar nueva conexión**.

    ![Service Manager ](./media/log-analytics-itsmc/itsmc-service-manager-connection.png)
3. Proporcione la información tal como se describe en la tabla siguiente y haga clic en **Guardar** para crear la conexión:

> [!NOTE]
> Todos estos parámetros son obligatorios.

| **Campo** | **Descripción** |
| --- | --- |
| **Name**   | Escriba un nombre para la instancia de System Center Service Manager que quiere conectar con IT Service Management Connector.  Usará este nombre más adelante cuando configure los elementos de trabajo en el análisis de registros detallados de la instancia o vista. |
| **Selección de tipo de conexión**   | Seleccione **System Center Service Manager**. |
| **Dirección URL del servidor**   | Escriba la dirección URL de la aplicación web de Service Manager. [Aquí](#create-and-deploy-service-manager-web-app-service) podrá obtener más información sobre la aplicación web de Service Manager.
| **Id. de cliente**   | Escriba el identificador de cliente que ha generado (mediante el script automático) para autenticar la aplicación web. [Aquí](log-analytics-itsmc-service-manager-script.md) podrá obtener más información sobre el script automatizado.|
| **Secreto de cliente**   | Escriba el secreto de cliente generado para este identificador.   |
| **Ámbito de sincronización de datos**   | Seleccione los elementos de trabajo de Service Manager que quiere sincronizar mediante IT Service Management Connector.  Estos elementos de trabajo se importan en Log Analytics. **Opciones:** incidentes, solicitudes de cambio.|
| **Sincronización de datos** | Escriba el número de días pasados de los que desea los datos. **Límite máximo**: 120 días. |
| **Creación de un elemento de configuración de solución ITSM** | Seleccione esta opción si desea crear los elementos de configuración en el producto ITSM. Cuando se selecciona, OMS crea los elementos de configuración afectados como elementos de configuración (en el caso de que no existan) en el sistema ITSM compatible. **Valor predeterminado**: deshabilitado. |

Cuando se ha conectado y sincronizado correctamente:

- Los elementos de trabajo seleccionados de Service Manager se importan en **Log Analytics** de OMS. Puede ver el resumen de estos elementos de trabajo en el icono de IT Service Management Connector.

- Desde OMS puede crear incidentes de alertas de OMS o de búsqueda de registros en esta instancia de Service Manager.

Más información: [Creación de elementos de trabajo de ITSM para alertas de OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts) y [Creación de elementos de trabajo de ITSM desde registros de OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Creación e implementación del servicio de aplicaciones web de Service Manager

Para conectar la instancia de Service Manager local con IT Service Management Connector en OMS, Microsoft ha creado una aplicación web de Service Manager en GitHub.

Para configurar la aplicación web de ITSM para Service Manager, haga lo siguiente:

- **Implemente la aplicación web**: implemente la aplicación web, establezca las propiedades y autentíquese con Azure AD. Puede implementar la aplicación web mediante el [script automatizado ](log-analytics-itsmc-service-manager-script.md) que Microsoft ha proporcionado.
- **Configure la conexión híbrida** - [Configure esta conexión](#configure-the-hybrid-connection), manualmente.

#### <a name="deploy-the-web-app"></a>Implementación de la aplicación web
Use el [script](log-analytics-itsmc-service-manager-script.md) automatizado para implementar la aplicación web, establecer las propiedades y autenticarse con Azure AD.

Ejecute el script proporcionando los siguientes detalles necesarios:

- Detalles de la suscripción de Azure
- Definición de un nombre de grupo de recursos
- Ubicación
- Detalles del servidor de Service Manager (nombre del servidor, dominio, nombre de usuario y contraseña)
- Prefijo de nombre de sitio de la aplicación web
- Espacio de nombres de ServiceBus.

El script crea la aplicación web con el nombre que especificó (junto con algunas cadenas adicionales para hacerlo único). Genera la **dirección URL de la aplicación web**, el **id. de cliente** y el **secreto de cliente**.

Guarde los valores, ya que se usan al crear una conexión con IT Service Management Connector.

**Comprobación de la instalación de la aplicación web**

1. Vaya a **Azure Portal** > **Recursos**.
2. Seleccione la aplicación web y haga clic en **Configuración** > **Configuración de la aplicación**.
3. Confirme la información sobre la instancia de Service Manager que ha proporcionado en el momento de la implementación de la aplicación a través del script.

### <a name="configure-the-hybrid-connection"></a>Configuración de la conexión híbrida

Use el procedimiento siguiente para configurar la conexión híbrida que conecta la instancia de Service Manager con IT Service Management Connector en OMS.

1. Busque la aplicación web de Service Manager, **Recursos de Azure**.
2. Haga clic en **Configuración** > **Redes**.
3. En **Conexiones híbridas**, haga clic en **Configure los puntos de conexión de la conexión híbrida**.

    ![Redes de conexión híbrida](./media/log-analytics-itsmc/itsmc-hybrid-connection-networking-and-end-points.png)
4. En la hoja **Conexiones híbridas**, haga clic en **Agregar conexión híbrida**.

    ![Adición de conexión híbrida](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-add.png)

5. En la hoja **Agregar conexiones híbridas**, haga clic en **Crear conexión híbrida nueva**.

    ![Conexión híbrida nueva](./media/log-analytics-itsmc/itsmc-create-new-hybrid-connection.png)

6. Escriba los valores siguientes:

    - **Nombre del punto de conexión**: especifique un nombre para la conexión híbrida nueva.
    -  **Host del punto de conexión**: nombre de dominio completo del servidor de administración de Service Manager.
    - **Puerto del punto de conexión**: escriba 5724.
    - **Espacio de nombres de bus de servicio**: use un espacio de nombres de bus de servicio existente o cree uno.
    - **Ubicación**: seleccione la ubicación.
    -  **Nombre**: especifique un nombre para el bus de servicio si lo está creando.

    ![Valores de la conexión híbrida](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-values.png)
6. Haga clic en **Aceptar** para cerrar la hoja **Crear conexión híbrida** y empiece a crearla.

    Una vez creada, se muestra debajo de la hoja.

7. Cuando se crea la conexión híbrida, seleccione la conexión y haga clic en **Agregar conexión híbrida seleccionada**.

    ![Conexión híbrida nueva](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Configuración del programa de instalación del agente de escucha

Utilice el procedimiento siguiente para configurar el programa de instalación del agente de escucha para la conexión híbrida.

1. En la hoja **Conexiones híbridas**, haga clic en **Descargar administrador de conexión** e instálelo en el equipo donde se ejecuta la instancia de System Center Service Manager.

    Una vez finalizada la instalación, la opción **Hybrid Connection Manager UI** (IU de administración de conexión híbrida) está disponible en el menú **Iniciar**.

2. Haga clic en **Hybrid Connection Manager UI** (IU de administración de conexión híbrida), se le pedirán las credenciales de Azure.

3. Inicie sesión con sus credenciales de Azure y seleccione la suscripción en la que se creó la conexión híbrida.

4. Haga clic en **Guardar**.

La conexión híbrida se ha conectado correctamente.

![conexión híbrida correcta](./media/log-analytics-itsmc/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]

> Después de crear la conexión híbrida, compruebe y pruebe la conexión visitando la aplicación web de la instancia de Service Manager implementado. Asegúrese de que la conexión es correcta antes de intentar conectarse con IT Service Management Connector en OMS.

En la siguiente imagen se muestran los detalles de una conexión correcta:

![Prueba de conexión híbrida](./media/log-analytics-itsmc/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-oms"></a>Conexión de ServiceNow con IT Service Management Connector en OMS

En las secciones siguientes se ofrecen detalles sobre cómo conectar el producto de ServiceNow con IT Service Management Connector en OMS.

### <a name="prerequisites"></a>Requisitos previos

Asegúrese de que se cumplen los siguientes requisitos previos:

- IT Service Management Connector se ha instalado. Más información: [Agregar la solución IT Service Management Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Versiones admitidas de ServiceNow: Fuji, Ginebra, Helsinki.

Los administradores de ServiceNow deben realizar lo siguiente en la instancia de ServiceNow:
- Generar el identificador y el secreto del cliente para el producto de ServiceNow. Para más información sobre cómo generar el identificador y el secreto del cliente, consulte [el programa de instalación de OAuth](http://wiki.servicenow.com/index.php?title=OAuth_Setup).
- Instalar la aplicación de usuario para la integración de Microsoft OMS (aplicación de ServiceNow). [Más información](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.0 ).
- Crear el rol de usuario de integración para la aplicación de usuario instalada. [Aquí](#create-integration-user-role-in-servicenow-app) puede encontrar más información sobre cómo crear el rol de usuario de integración.


### <a name="connection-procedure"></a>**Procedimiento de conexión**

Use el procedimiento siguiente para crear una nueva conexión a ServiceNow.

1. Vaya a **OMS** > **Configuración** > **Orígenes conectados**.
2. Seleccione **Conector ITSM**, haga clic en **Agregar nueva conexión**.

    ![Conexión de ServiceNow](./media/log-analytics-itsmc/itsmc-servicenow-connection.png)

3. Proporcione la información tal como se describe en la tabla siguiente y haga clic en **Guardar** para crear la conexión:

> [!NOTE]
> Todos estos parámetros son obligatorios.

| **Campo** | **Descripción** |
| --- | --- |
| **Name**   | Escriba un nombre para la instancia de ServiceNow que quiere conectar con IT Service Management Connector.  Usará este nombre más adelante en OMS cuando configure los elementos de trabajo en el análisis de registros detallados de ITSM o vista. |
| **Selección de tipo de conexión**   | Seleccione **ServiceNow**. |
| **Nombre de usuario**   | Escriba el nombre del usuario de integración que ha creado en la aplicación de ServiceNow para que admita la conexión con IT Service Management Connector. Más información: [Creación de un rol de usuario de integración de aplicación de ServiceNow](#create-integration-user-role-in-servicenow-app).|
| **Password**   | Escriba la contraseña asociada con este nombre de usuario. **Nota**: El nombre de usuario y la contraseña se utilizan para generar únicamente tokens de autenticación y no se almacenan en ningún lugar dentro del servicio OMS.  |
| **Dirección URL del servidor**   | Escriba la dirección URL de la instancia de ServiceNow que quiere conectar con IT Service Management Connector. |
| **Id. de cliente**   | Escriba el identificador de cliente que desea utilizar para la autenticación de OAuth2 y que ha generado anteriormente.  Para más información acerca de cómo generar el identificador y el secreto del cliente, consulte el [programa de instalación de OAuth](http://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Secreto de cliente**   | Escriba el secreto de cliente generado para este identificador.   |
| **Ámbito de sincronización de datos**   | Seleccione los elementos de trabajo de ServiceNow que quiere sincronizar con OMS mediante IT Service Management Connector.  Los valores seleccionados se importan en Log Analytics.   **Opciones:** incidentes y solicitudes de cambio.|
| **Sincronización de datos** | Escriba el número de días pasados de los que desea los datos. **Límite máximo**: 120 días. |
| **Creación de un elemento de configuración de solución ITSM** | Seleccione esta opción si desea crear los elementos de configuración en el producto ITSM. Cuando se selecciona, OMS crea los elementos de configuración afectados como elementos de configuración (en el caso de que no existan) en el sistema ITSM compatible. **Valor predeterminado**: deshabilitado. |


Cuando se ha conectado y sincronizado correctamente:

- Los elementos de trabajo seleccionados de la conexión de ServiceNow se importan en Log Analytics de OMS.  Puede ver el resumen de estos elementos de trabajo en el icono de IT Service Management Connector.
- Puede crear incidentes, alertas y eventos desde alertas de OMS o búsqueda de registros en esta instancia de ServiceNow.  


Más información: [Creación de elementos de trabajo de ITSM para alertas de OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts) y [Creación de elementos de trabajo de ITSM desde registros de OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs).

### <a name="create-integration-user-role-in-servicenow-app"></a>Creación de un rol de usuario de integración de aplicación de ServiceNow

Utilice el siguiente procedimiento:

1.  Visite la [tienda de ServiceNow](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.0) e instale la **aplicación de usuario para la integración de OMS de Microsoft y ServiceNow** en la instancia de ServiceNow.
2.  Después de la instalación, visite la barra de navegación izquierda de la instancia de ServiceNow, y busque y seleccione el integrador de OMS de Microsoft.  
3.  Haga clic en **Installation Checklist** (Lista de comprobación de instalación).

    El estado se muestra como **Not complete** (Sin completar) si aún no se creó el rol de usuario.

4.  En los cuadros de texto, junto a **Create integration user** (Crear usuario de integración), escriba el nombre de usuario para el usuario que puede conectarse a IT Service Management Connector en OMS.
5.  Escriba la contraseña para este usuario y haga clic en **Aceptar**.  

>[!NOTE]

> Utilice estas credenciales para realizar la conexión de ServiceNow en OMS.

El usuario recién creado se muestra con los roles predeterminados asignados.

Roles predeterminados:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.User
-   itil
-   template_editor
-   view_changer

Cuando el usuario se crea correctamente, el estado de **Check Installation Checklist** (Comprobar lista de comprobación de instalación) pasa a Completed (Completado) y se muestran los detalles del rol de usuario creado para la aplicación.

> [!NOTE]

> Para permitir al usuario crear **alertas** y **eventos** en ServiceNow desde OMS:

> - Asegúrese de que tiene el módulo de administración de eventos instalado en su instancia de ServiceNow.

> - Agregue los roles siguientes al usuario de integración:
>      - evt_mgmt_integration
>      - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-oms"></a>Conexión de Provance con IT Service Management Connector en OMS

En las secciones siguientes se ofrecen detalles sobre cómo conectar el producto de Provance con IT Service Management Connector en OMS.

### <a name="prerequisites"></a>Requisitos previos

Asegúrese de que se cumplen los siguientes requisitos previos:


- IT Service Management Connector se ha instalado. Más información: [Agregar la solución IT Service Management Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- La aplicación Provance debe registrarse con Azure AD y estará disponible el identificador de cliente. Para una información detallada, consulte el tema sobre [cómo configurar la autenticación de Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

- Rol de usuario: administrador.

### <a name="connection-procedure"></a>Procedimiento de conexión

Use el procedimiento siguiente para crear una conexión a Provance:

1. Vaya a **OMS** > **Configuración** > **Orígenes conectados**.
2. Seleccione **Conector ITSM**, haga clic en **Agregar nueva conexión**.  

    ![Conexión a Provance](./media/log-analytics-itsmc/itsmc-provance-connection.png)
3. Proporcione la información tal como se describe en la tabla siguiente y haga clic en **Guardar** para crear la conexión.

> [!NOTE]
> Todos estos parámetros son obligatorios.

| **Campo** | **Descripción** |
| --- | --- |
| **Name**   | Escriba un nombre para la instancia de Provance que quiere conectar con IT Service Management Connector.  Usará este nombre más adelante en OMS cuando configure los elementos de trabajo en el análisis de registros detallados de ITSM o vista. |
| **Selección de tipo de conexión**   | Seleccione **Provance**. |
| **Nombre de usuario**   | Escriba el nombre de usuario que puede conectarse a IT Service Management Connector.    |
| **Password**   | Escriba la contraseña asociada con este nombre de usuario. **Nota**: El nombre de usuario y la contraseña se utilizan para generar únicamente tokens de autenticación y no se almacenan en ningún lugar dentro del servicio OMS.|
| **Dirección URL del servidor**   | Escriba la dirección URL de la instancia de Provance que quiere conectar con IT Service Management Connector. |
| **Id. de cliente**   | Escriba el identificador de cliente para autenticar esta conexión, que genera en la instancia de Provance.  Para más información sobre el identificador de cliente, consulte el tema sobre [cómo configurar la autenticación de Active Directory](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md). |
| **Ámbito de sincronización de datos**   | Seleccione los elementos de trabajo de Provance que quiere sincronizar con OMS mediante IT Service Management Connector.  Estos elementos de trabajo se importan en Log Analytics.   **Opciones:** incidentes, solicitudes de cambio.|
| **Sincronización de datos** | Escriba el número de días pasados de los que desea los datos. **Límite máximo**: 120 días. |
| **Creación de un elemento de configuración de solución ITSM** | Seleccione esta opción si desea crear los elementos de configuración en el producto ITSM. Cuando se selecciona, OMS crea los elementos de configuración afectados como elementos de configuración (en el caso de que no existan) en el sistema ITSM compatible. **Valor predeterminado**: deshabilitado.|

Cuando se ha conectado y sincronizado correctamente:

- Los elementos de trabajo seleccionados de la conexión a Provance se importan en **Log Analytics** de OMS.  Puede ver el resumen de estos elementos de trabajo en el icono de IT Service Management Connector.
- Puede crear incidentes y eventos desde alertas de OMS o búsqueda de registros en esta instancia de Provance.

Más información: [Creación de elementos de trabajo de ITSM para alertas de OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts) y [Creación de elementos de trabajo de ITSM desde registros de OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs).

## <a name="connect-cherwell-to-it-service-management-connector-in-oms"></a>Conexión de Cherwell con IT Service Management Connector en OMS

En las secciones siguientes se ofrecen detalles sobre cómo conectar el producto de Cherwell con IT Service Management Connector en OMS.

### <a name="prerequisites"></a>Requisitos previos

Asegúrese de que se cumplen los siguientes requisitos previos:

- IT Service Management Connector se ha instalado. Más información: [Agregar la solución IT Service Management Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Identificador de cliente generado. Más información: [Generación del identificador de cliente para Cherwell](#generate-client-id-for-cherwell).
- Rol de usuario: administrador.

### <a name="connection-procedure"></a>Procedimiento de conexión

Use el procedimiento siguiente para crear una conexión a Cherwell:

1. Vaya a **OMS** >  **Configuración** > **Orígenes conectados**.
2. Seleccione **Conector ITSM**, haga clic en **Agregar nueva conexión**.  

    ![Identificador de usuario de Cherwell](./media/log-analytics-itsmc/itsmc-cherwell-connection.png)

3. Proporcione la información tal como se describe en la tabla siguiente y haga clic en **Guardar** para crear la conexión.

> [!NOTE]
> Todos estos parámetros son obligatorios.

| **Campo** | **Descripción** |
| --- | --- |
| **Name**   | Escriba un nombre para la instancia de Cherwell que quiere conectar con IT Service Management Connector.  Usará este nombre más adelante en OMS cuando configure los elementos de trabajo en el análisis de registros detallados de ITSM o vista. |
| **Selección de tipo de conexión**   | Seleccione **Cherwell.** |
| **Nombre de usuario**   | Escriba el nombre de usuario de Cherwell que puede conectarse a IT Service Management Connector. |
| **Password**   | Escriba la contraseña asociada con este nombre de usuario. **Nota**: El nombre de usuario y la contraseña se utilizan para generar únicamente tokens de autenticación y no se almacenan en ningún lugar dentro del servicio OMS.|
| **Dirección URL del servidor**   | Escriba la dirección URL de la instancia de Cherwell que quiere conectar con IT Service Management Connector. |
| **Id. de cliente**   | Escriba el identificador de cliente para autenticar esta conexión, que genera en la instancia de Cherwell.   |
| **Ámbito de sincronización de datos**   | Seleccione los elementos de trabajo de Cherwell que quiere sincronizar mediante IT Service Management Connector.  Estos elementos de trabajo se importan en Log Analytics.   **Opciones:** incidentes, solicitudes de cambio. |
| **Sincronización de datos** | Escriba el número de días pasados de los que desea los datos. **Límite máximo**: 120 días. |
| **Creación de un elemento de configuración de solución ITSM** | Seleccione esta opción si desea crear los elementos de configuración en el producto ITSM. Cuando se selecciona, OMS crea los elementos de configuración afectados como elementos de configuración (en el caso de que no existan) en el sistema ITSM compatible. **Valor predeterminado**: deshabilitado. |

Cuando se ha conectado y sincronizado correctamente:

- Los elementos de trabajo seleccionados de la conexión de Cherwell se importan en Log Analytics de OMS. Puede ver el resumen de estos elementos de trabajo en el icono de IT Service Management Connector.
- Puede crear incidentes y los eventos de esta instancia de Cherwell en OMS. Más información: Creación de elementos de trabajo de ITSM para alertas de OMS y Creación de elementos de trabajo de ITSM desde registros de OMS.

Más información: [Creación de elementos de trabajo de ITSM para alertas de OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts) y [Creación de elementos de trabajo de ITSM desde registros de OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs).

### <a name="generate-client-id-for-cherwell"></a>Generación del identificador de cliente para Cherwell

Para generar el identificador o clave de cliente para Cherwell, utilice el procedimiento siguiente:

1. Inicie sesión en la instancia de Cherwell como administrador.
2. Haga clic en **Security** > **Edit REST API client settings** (Seguridad > Editar configuración de cliente de API de REST).
3. Seleccione **Create new client** > **client secret** (Crear nuevo cliente > Secreto de cliente).

    ![Identificador de usuario de Cherwell](./media/log-analytics-itsmc/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Pasos siguientes
 - [Creación de elementos de trabajo de ITSM para alertas de OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts)

 - [Creación de elementos de trabajo de ITSM desde registros de OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs)

- [Ver análisis de registros para la conexión](log-analytics-itsmc-overview.md#using-the-solution)
