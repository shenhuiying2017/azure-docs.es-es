---
title: "Tutorial: configuración de Workday para aprovisionar automáticamente usuarios con Active Directory y Azure Active Directory locales | Microsoft Docs"
description: Aprenda a usar Workday como origen de datos de identidad para Active Directory y Azure Active Directory.
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/26/2017
ms.author: asmalser
ms.openlocfilehash: f267a59fadb7f402ac81f43b5465b6ac1f28943e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning-with-on-premises-active-directory-and-azure-active-directory"></a>Tutorial: configuración de Workday para aprovisionar automáticamente usuarios con Active Directory y Azure Active Directory locales
El objetivo de este tutorial consiste en mostrarle los pasos que debe llevar a cabo para importar usuarios de Workday a Active Directory y Azure Active Directory, con la reescritura opcional de algunos atributos en Workday. 



## <a name="overview"></a>Información general

El [servicio de aprovisionamiento de usuarios de Azure Active Directory](active-directory-saas-app-provisioning.md) se integra con la [API de recursos humanos de Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) para poder aprovisionar cuentas de usuario. Azure AD usa esta conexión para habilitar los siguientes flujos de trabajo de aprovisionamiento de usuarios:

* **Aprovisionamiento de usuarios en Active Directory**: sincronice los conjuntos de usuarios seleccionados de Workday con uno o varios bosques de Active Directory. 

* **Aprovisionamiento de usuarios que solo están en la nube en Azure Active Directory**: los usuarios híbridos que existen tanto en Active Directory como en Azure Active Directory se pueden aprovisionar en este último mediante [AAD Connect](connect/active-directory-aadconnect.md), aunque los usuarios que solo están en la nube se pueden aprovisionar directamente desde Workday a Azure Active Directory mediante el servicio de aprovisionamiento de usuarios de Azure AD.

* **Reescritura de las direcciones de correo electrónico en Workday**: el servicio de aprovisionamiento de usuarios de Azure AD puede reescribir los atributos de usuario de Azure AD seleccionados en Workday (por ejemplo, la dirección de correo electrónico).

### <a name="scenarios-covered"></a>Escenarios descritos

El flujo de trabajo de aprovisionamiento de usuarios de Workday compatible con el servicio de aprovisionamiento de usuarios de Azure AD permite la automatización de los siguientes escenarios de recursos humanos y de administración del ciclo de vida de identidades:

* **Contratación de nuevos empleados**: cuando se agrega un nuevo empleado a Workday, se crea automáticamente una cuenta de usuario en Active Directory, Azure Active Directory y, opcionalmente, en Office 365 y en [otras aplicaciones SaaS compatibles con Azure AD](active-directory-saas-app-provisioning.md), con reescritura de la dirección de correo electrónico en Workday.

* **Actualizaciones de atributos y de perfil de los empleados**: si se actualiza un registro de empleado en Workday (por ejemplo, el nombre, el cargo o el administrador), su cuenta de usuario se actualizará automáticamente en Active Directory, Azure Active Directory y, opcionalmente, en Office 365 y en [otras aplicaciones SaaS compatibles con Azure AD](active-directory-saas-app-provisioning.md).

* **Ceses de empleados**: cuando se prescinde de un empleado en Workday, su cuenta de usuario se deshabilita automáticamente de Active Directory, Azure Active Directory y, opcionalmente, de Office 365 y de [otras aplicaciones SaaS compatibles con Azure AD](active-directory-saas-app-provisioning.md).

* **Recontrataciones de empleados**: cuando se vuelve a contratar a un empleado en Workday, se puede reactivar o volver a aprovisionar automáticamente su cuenta antigua (en función de sus preferencias) en Active Directory, Azure Active Directory y, opcionalmente, en Office 365 y en [otras aplicaciones SaaS compatibles con Azure AD](active-directory-saas-app-provisioning.md).


## <a name="planning-your-solution"></a>Planeamiento de la solución

Antes de iniciar la integración de Workday, compruebe los requisitos previos siguientes y lea las siguientes instrucciones sobre cómo ajustar su arquitectura actual de Active Directory y los requisitos de aprovisionamiento de usuarios con las soluciones proporcionadas por Azure Active Directory.

### <a name="prerequisites"></a>Requisitos previos

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción válida a Azure AD Premium P1 con acceso de administrador global
* Un inquilino de implementación de Workday para fines de pruebas y de integración
* Permisos de administrador en Workday para crear un usuario de integración del sistema y efectuar cambios para probar los datos de los empleados con fines de pruebas
* Para el aprovisionamiento de usuarios en Active Directory se necesita un servidor unido a un dominio que ejecute Windows Service 2012 o una versión posterior para poder hospedar el [agente de sincronización local](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](connect/active-directory-aadconnect.md) para la sincronización entre Active Directory y Azure AD.


### <a name="solution-architecture"></a>Arquitectura de la solución

Azure AD ofrece una amplia variedad de conectores de aprovisionamiento para que pueda resolver el aprovisionamiento y la administración del ciclo de vida de identidades de Workday a Active Directory, Azure AD, aplicaciones SaaS, etc. Las características que va a usar y la configuración que aplicará a la solución variarán según el entorno y los requisitos de su organización. Como primer paso, valore cuántos de los siguientes elementos están presentes e implementados en su organización:

* ¿Cuántos bosques de Active Directory están en uso?
* ¿Cuántos dominios de Active Directory están en uso?
* ¿Cuántas unidades organizativas (UO) de Active Directory están en uso?
* ¿Cuántos inquilinos de Azure Active Directory están en uso?
* ¿Hay usuarios que necesiten aprovisionarse en Active Directory y en Azure Active Directory (por ejemplo, los usuarios "híbridos")?
* ¿Hay usuarios que necesiten aprovisionarse en Azure Active Directory pero no en Active Directory (por ejemplo, los usuarios que solo están en la nube)?
* ¿Las direcciones de correo electrónico de los usuarios se tienen que reescribir en Workday?

Una vez que tenga respuesta a estas preguntas, podrá planear la implementación de aprovisionamiento de Workday siguiendo las instrucciones que se detallan a continuación.

#### <a name="using-provisioning-connector-apps"></a>Uso de aplicaciones de conectores de aprovisionamiento

Azure Active Directory admite los conectores de aprovisionamiento previamente integrados para Workday y muchas otras aplicaciones SaaS. 

Los conectores de aprovisionamiento interactúan con la API de un sistema de origen y permiten aprovisionar datos en un sistema de destino. La mayoría de los conectores de aprovisionamiento compatibles con Azure AD son para un sistema de origen y de destino (por ejemplo, Azure AD en ServiceNow) y se pueden configurar agregando la aplicación en cuestión desde la galería de aplicaciones de Azure AD (por ejemplo, ServiceNow). 

Hay una relación unívoca entre las instancias de los conectores de aprovisionamiento y las instancias de aplicación en Azure AD:

| Sistema de origen | Sistema de destino |
| ---------- | ---------- | 
| Inquilino de Azure AD | Aplicación SaaS |


Pero al trabajar con Workday y Active Directory, hay varios sistemas de origen y de destino que se deben tener en cuenta:

| Sistema de origen | Sistema de destino | Notas |
| ---------- | ---------- | ---------- |
| Workday | Bosque de Active Directory | Cada bosque se trata como un sistema de destino distinto |
| Workday | Inquilino de Azure AD | Según sea necesario para los usuarios que solo están en la nube |
| Bosque de Active Directory | Inquilino de Azure AD | Actualmente, este flujo está controlado por AAD Connect |
| Inquilino de Azure AD | Workday | Para la reescritura de direcciones de correo electrónico |

Para facilitar estos diversos flujos de trabajo en varios sistemas de origen y de destino, Azure AD proporciona varias aplicaciones de conectores de aprovisionamiento que puede agregar desde la galería de aplicaciones de Azure AD:

![Galería de aplicaciones de AAD](./media/active-directory-saas-workday-inbound-tutorial/WD_Gallery.PNG)

* **Aprovisionamiento de Workday en Active Directory**: esta aplicación facilita el aprovisionamiento de cuentas de usuario desde Workday a un bosque de Active Directory. Si tiene varios bosques, puede agregar una instancia de esta aplicación desde la galería de aplicaciones de Azure AD para cada bosque de Active Directory en el que deba efectuar un aprovisionamiento.

* **Aprovisionamiento de Workday en Azure AD**: mientras AAD Connect es la herramienta que se debe usar para sincronizar los usuarios de Active Directory con Azure Active Directory, esta aplicación se puede usar para facilitar el aprovisionamiento de usuarios que solo están en la nube desde Workday a un inquilino de Azure Active Directory.

* **Reescritura de Workday**: esta aplicación facilita la reescritura de direcciones de correo electrónico del usuario de Azure Active Directory a Workday.

> [!TIP]
> La aplicación "Workday" normal se usa para configurar el inicio de sesión único entre Workday y Azure Active Directory. 

El modo de instalación y configuración de estas aplicaciones de conectores de aprovisionamiento especiales es el tema de las secciones restantes de este tutorial. Las aplicaciones que elija para la configuración dependerán de los sistemas que deba aprovisionar y de la cantidad de bosques de Active Directory y de inquilinos de Azure AD que haya en su entorno.

![Información general](./media/active-directory-saas-workday-inbound-tutorial/WD_Overview.PNG)

## <a name="configure-a-system-integration-user-in-workday"></a>Configurar un usuario de integración de sistemas en Workday
Un requisito común de todos los conectores de aprovisionamiento de Workday es que requieren credenciales para una cuenta de integración de sistemas en Workday para poder conectarse a la API de recursos humanos de Workday. En esta sección se describe cómo crear una cuenta de integrador de sistemas en Workday.

> [!NOTE]
> Se puede omitir este procedimiento y usar una cuenta de administrador global de Workday como cuenta de integración de sistemas. Puede funcionar correctamente en las demostraciones, pero no se recomienda en las implementaciones de producción.

### <a name="create-an-integration-system-user"></a>Creación de un usuario del sistema de integración

**Para crear un usuario del sistema de integración:**

1. Inicie sesión en su inquilino de Workday usando una cuenta de administrador. En **Workday Workbench** (Área de trabajo de Workbench), escriba create user (crear usuario) en el cuadro de búsqueda y haga clic en **Create Integration System User** (Crear usuario del sistema de integración). 
   
    ![Creación de usuarios](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Creación de usuarios")
2. Para completar la tarea **Create Integration System User** (Crear usuario del sistema de integración), especifique el nombre de usuario y la contraseña del nuevo usuario del sistema de integración.  
 * Deje la opción **Require New Password at Next Sign In** (Solicitar una nueva contraseña en el siguiente inicio de sesión) sin activar, ya que este usuario iniciará sesión mediante programación. 
 * Deje **Session Timeout Minutes** (Minutos de tiempo de espera de la sesión) en 0 (su valor predeterminado), con el fin de evitar que las sesiones del usuario agoten el tiempo de espera de manera prematura. 
   
    ![Crear usuario de sistema de integración](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Crear usuario de sistema de integración")

### <a name="create-a-security-group"></a>Creación de un grupo de seguridad
Debe crear un grupo de seguridad del sistema de integración sin restricciones y asignarle el usuario.

**Para crear un grupo de seguridad:**

1. Escriba create security group (crear grupo de seguridad) en el cuadro de búsqueda y luego haga clic en **Create Security Group**(Crear grupo de seguridad). 
   
    ![Crear grupo de seguridad](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "Crear grupo de seguridad")
2. Complete la tarea **Create Security Group** (Crear grupo de seguridad).  
3. En la lista desplegable **Type of Tenanted Security Group** (Tipo de grupo de seguridad con inquilinos), seleccione Integration System Security Group—Unconstrained (Grupo de seguridad del sistema de integración: sin restringir).
4. Crear un grupo de seguridad al que se agregarán explícitamente los miembros. 
   
    ![Crear grupo de seguridad](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "Crear grupo de seguridad")

### <a name="assign-the-integration-system-user-to-the-security-group"></a>Asignación del usuario del sistema de integración al grupo de seguridad

**Para asignar al usuario del sistema de integración:**

1. Escriba edit security group (editar grupo de seguridad) en el cuadro de búsqueda y luego haga clic en **Edit Security Group**(Editar grupo de seguridad). 
   
    ![Editar grupo de seguridad](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Editar grupo de seguridad")
2. Busque y seleccione el nuevo grupo de seguridad de integración por nombre. 
   
    ![Editar grupo de seguridad](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Editar grupo de seguridad")
3. Agregue el nuevo usuario del sistema de integración al nuevo grupo de seguridad. 
   
    ![Grupo de seguridad del sistema](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "Grupo de seguridad del sistema")  

### <a name="configure-security-group-options"></a>Configuración de las opciones del grupo de seguridad
En este paso, se conceden al nuevo grupo de seguridad permisos para las operaciones **Get** y **Put** en los objetos protegidos por las siguientes directivas de seguridad del dominio:

* External Account Provisioning
* Worker Data: Public Worker Reports
* Worker Data: All Positions
* Worker Data: Current Staffing Information
* Worker Data: Business Title on Worker Profile

**Para configurar las opciones del grupo de seguridad:**

1. Escriba directivas de seguridad de dominio en el cuadro de búsqueda y, a continuación, haga clic en el vínculo **Domain Security Policies for Functional Area** (Directivas de seguridad de dominio para área funcional).  
   
    ![Directivas de seguridad de dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Directivas de seguridad de dominio")  
2. Busque el sistema y seleccione el área funcional **Sistema** .  Haga clic en **Aceptar**.  
   
    ![Directivas de seguridad de dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Directivas de seguridad de dominio")  
3. En la lista de directivas de seguridad del área funcional System (Sistema), expanda **Security Administration** (Administración de seguridad) y seleccione la directiva de seguridad de dominio, **External Account Provisioning** (Aprovisionamiento de cuentas externas).  
   
    ![Directivas de seguridad de dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Directivas de seguridad de dominio")  
4. Haga clic en **Edit Permissions** (Editar permisos) y, en la página de diálogo **Edit Permissions** (Editar permisos), agregue el nuevo grupo de seguridad a la lista de grupos de seguridad con permisos de integración de **Get** y **Put**. 
   
    ![Editar permiso](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Editar permiso")  
5. Repita el paso 1 anterior para volver a la pantalla para seleccionar las áreas funcionales y, esta vez, busque personal, seleccione el área funcional **Staffing** (Personal) y haga clic en el botón **OK** (Aceptar).
   
    ![Directivas de seguridad de dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Directivas de seguridad de dominio")  
6. En la lista de directivas de seguridad del área funcional Staffing (Personal), expanda **Worker Data: Staffing** y repita el paso 4 anterior en cada una de las restantes directivas de seguridad:

   * Worker Data: Public Worker Reports
   * Worker Data: All Positions
   * Worker Data: Current Staffing Information
   * Worker Data: Business Title on Worker Profile
   
7. Repita el paso 1 para volver a la pantalla de selección de áreas funcionales y, esta vez, busque **Contact Information** (Información de contactos), seleccione el área funcional Staffing (Personal) y haga clic en **OK** (Aceptar).

8.  En la lista de directivas de seguridad del área funcional Staffing (Personal), expanda **Worker Data: Work Contact Information** (Datos de trabajadores: Información de contacto de trabajo) y repita el paso 4 para las siguientes directivas de seguridad:

    * Worker Data: Work Email (Datos de trabajadores: Correo electrónico de trabajo)

    ![Directivas de seguridad de dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Directivas de seguridad de dominio")  
    
### <a name="activate-security-policy-changes"></a>Activación de cambios en directivas de seguridad

**Para activar cambios en directiva de seguridad:**

1. Escriba activate en el cuadro de búsqueda y, a continuación, haga clic en el vínculo **Activate Pending Security Policy Changes** (Activar cambios en la directiva de seguridad pendientes). 
   
    ![Activar](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Activar") 
2. Inicie la tarea Activate Pending Security Policy Changes (Activar cambios en la directiva de seguridad pendientes), para ello tiene que escribir un comentario para fines de auditoría y luego hacer clic en el botón **Aceptar**. 
   
    ![Activar seguridad pendiente](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Activar seguridad pendiente")   
3. Complete la tarea que aparece en la pantalla siguiente; para ello, active la casilla etiquetada como **Confirm** (Confirmar) y luego haga clic en **OK** (Aceptar). 
   
    ![Activar seguridad pendiente](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Activar seguridad pendiente")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Configuración del aprovisionamiento de usuarios de Workday a Active Directory
Siga estas instrucciones para configurar el aprovisionamiento de cuentas de usuario de Workday a cada bosque de Active Directory en el que deba efectuar un aprovisionamiento.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: agregación de la aplicación del conector de aprovisionamiento y creación de la conexión con Workday

**Para configurar Workday en el aprovisionamiento de Active Directory:**

1.  Vaya a <https://portal.azure.com>

2.  En la barra de navegación de la izquierda, seleccione **Azure Active Directory**.

3.  Seleccione **Enterprise Applications** (Aplicaciones empresariales) y **All Applications** (Todas las aplicaciones).

4.  Seleccione **Add an application** (Agregar una aplicación) y seleccione la categoría **All** (Todos).

5.  Busque **Workday Provisioning to Active Directory** (Aprovisionamiento de Workday en Active Directory) y agregue esa aplicación desde la galería.

6.  Una vez que se haya agregado la aplicación y se muestre la pantalla de detalles de la aplicación, seleccione **Aprovisionamiento**.

7.  Cambie el **modo** **de aprovisionamiento** a **Automático**.

8.  Cumplimente la sección **Credenciales de administrador** del siguiente modo:

   * **Nombre de usuario de administrador**: escriba el nombre de usuario de la cuenta del sistema de integración de Workday, anexando el nombre de dominio del inquilino. **Debe tener un aspecto similar al siguiente: username@contoso4**

   * **Contraseña de administrador**: escriba la contraseña de la cuenta del sistema de integración de Workday.

   * **URL de inquilino**: escriba la dirección URL al punto de conexión de servicios web de Workday de su inquilino. Debería ser similar a https://wd3-impl-services1.workday.com/ccx/service/contoso4, donde contoso4 se reemplaza por el nombre correcto del inquilino y wd3-impl se reemplaza por la cadena de entorno correcta.

   * **Bosque de Active Directory**: "nombre" del bosque de Active Directory, tal como lo devuelve el commandlet de PowerShell Get-ADForest. Suele ser una cadena tipo *contoso.com*.

   * **Contenedor de Active Directory**: escriba la cadena de contenedor que contiene todos los usuarios del bosque de AD. Ejemplo: *OU=Standard Users,OU=Users,DC=contoso,DC=test*

   * **Correo electrónico de notificación**: escriba su dirección de correo electrónico y marque la casilla "Enviar una notificación por correo electrónico cuando se produzca un error".

   * Haga clic en el botón **Probar conexión**. Si la prueba de conexión se lleva a cabo correctamente, haga clic en el botón **Guardar** situado en la parte superior. Si se produce un error, compruebe que las credenciales de Workday sean válidas en Workday. 

![Portal de Azure](./media/active-directory-saas-workday-inbound-tutorial/WD_1.PNG)

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: configuración de las asignaciones de atributos 

En esta sección configurará cómo fluyen los datos de los usuarios de Workday a Active Directory.

1.  En la pestaña Aprovisionamiento, en **Asignaciones**, haga clic en **Synchronize Workday Workers to OnPremises** (Sincronizar trabajadores de Workday con Local).

2.  En el campo **Ámbito de objeto de origen** puede seleccionar los conjuntos de usuarios de Workday que deben estar en el ámbito para el aprovisionamiento en AD; para ello debe definir un conjunto de filtros basados en atributos. El ámbito predeterminado es "Todos los usuarios de Workday". Filtros de ejemplo:

   * Ejemplo: ámbito de los usuarios que tengan los id. de trabajador comprendidos entre 1 000 000 y 2 000 000

      * Atributo: WorkerID

      * Operador: REGEX.COINCIDIR

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Ejemplo: solo los empleados, no los trabajadores temporales 

      * Atributo: EmployeeID

      * Operador: NO ES NULO

3.  En el campo **Acciones del objeto de destino**, puede filtrar de forma global las acciones permitidas en Active Directory. **Crear** y **Actualizar** son las más habituales.

4.  En la sección **Asignaciones de atributos** puede definir cómo se asignan los distintos atributos de Workday a los atributos de Active Directory.

5. Haga clic en una asignación de atributos existente para actualizarla o haga clic en **Agregar nueva asignación** en la parte inferior de la pantalla para agregar asignaciones nuevas. Las asignaciones de atributos admiten estas propiedades:

      * **Tipo de asignación**

         * **Directo**: escribe el valor del atributo de Workday en el atributo de AD sin cambios

         * **Constante**: escribe un valor de cadena estático y constante en el atributo de AD

         * **Expresión**: le permite escribir un valor personalizado en el atributo de AD, en función de uno o varios atributos de Workday. [Para obtener más información, consulte este artículo sobre las expresiones](active-directory-saas-writing-expressions-for-attribute-mappings.md).

      * **Atributo de origen**: el atributo de usuario de Workday.

      * **Valor predeterminado**: opcional. Si el atributo de origen tiene un valor vacío, la asignación escribirá este valor.
            La configuración más habitual consiste en dejarlo en blanco.

      * **Atributo de destino**: atributo de usuario de Active Directory.

      * **Hacer coincidir objetos con este atributo**: especifica si se debe usar o no esta asignación para identificar de forma unívoca a los usuarios entre Workday y Active Directory. Se suele establecer en el campo Id. del trabajador de Workday, que se suele asignar a uno de los atributos de id. de empleado de Active Directory.

      * **Precedencia de coincidencia**: se pueden establecer varios atributos coincidentes. Si hay varios, se evalúan en el orden definido por este campo. En el momento en que se encuentre una coincidencia, no se evaluarán más atributos coincidentes.

      * **Aplicar esta asignación**
       
         * **Siempre**: esta asignación se aplica a las acciones de creación y actualización de usuarios

         * **Solo durante la creación**: esta asignación se aplica solo a las acciones de creación de usuarios

6. Para guardar las asignaciones, haga clic en **Guardar** en la parte superior de la sección Asignación de atributos.

![Portal de Azure](./media/active-directory-saas-workday-inbound-tutorial/WD_2.PNG)

**A continuación se muestran algunos ejemplos de asignaciones de atributos entre Workday y Active Directory, con algunas expresiones comunes**

-   La expresión que se asigna al atributo parentDistinguishedName de AD se puede usar para aprovisionar un usuario en una UO específica en función de uno o varios atributos de origen de Workday. En este ejemplo se coloca a los usuarios en unidades organizativas diferentes en función de los datos de Workday relacionados con sus ciudades.

-   La expresión que se asigna al atributo userPrincipalName de AD crea un UPN de firstName.LastName@contoso.com. También reemplaza los caracteres especiales no válidos.

-   [Aquí encontrará documentación sobre cómo escribir expresiones.](active-directory-saas-writing-expressions-for-attribute-mappings.md)

  
| ATRIBUTO DE WORKDAY | ATRIBUTO DE ACTIVE DIRECTORY |  ¿IDENTIFICADOR COINCIDENTE? | CREAR / ACTUALIZAR |
| ---------- | ---------- | ---------- | ---------- |
|  **WorkerID**  |  EmployeeID | **Sí** | Escrito únicamente en Crear | 
|  **Municipality**   |   l   |     | Crear y Actualizar |
|  **Company**         | company   |     |  Crear y Actualizar |
|  **CountryReferenceTwoLetter**      |   co |     |   Crear y Actualizar |
| **CountryReferenceTwoLetter**    |  c  |     |         Crear y Actualizar |
| **SupervisoryOrganization**  | department  |     |  Crear y Actualizar |
|  **PreferredNameData**  |  DisplayName |     |   Crear y Actualizar |
| **EmployeeID**    |  cn    |   |   Escrito únicamente en Crear |
| **Fax**      | facsimileTelephoneNumber     |     |    Crear y Actualizar |
| **Nombre**   | givenName       |     |    Crear y Actualizar |
| **Switch(\[Active\], , "0", "True", "1",)** |  accountDisabled      |     | Crear y Actualizar |
| **Mobile**  |    mobile       |     |       Crear y Actualizar |
| **EmailAddress**    | mail    |     |     Crear y Actualizar |
| **ManagerReference**   | manager  |     |  Crear y Actualizar |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Crear y Actualizar |
| **PostalCode**  |   postalCode  |     | Crear y Actualizar |
| **LocalReference** |  preferredLanguage  |     |  Crear y Actualizar |
| **Replace(Mid(Replace(\[EmployeeID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         Escrito únicamente en Crear |
| **Apellidos**   |   sn   |     |  Crear y Actualizar |
| **CountryRegionReference** |  st     |     | Crear y Actualizar |
| **AddressLineData**    |  streetAddress  |     |   Crear y Actualizar |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Crear y Actualizar |
| **BusinessTitle**   |  título     |     |  Crear y Actualizar |
| **Join("@",Replace(Replace(Replace(Replace(Replace(Replace(Replace( Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace( Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Join(".", [FirstName], [LastName]), , "([Øø])", , "oe", , ), , "[Ææ]", , "ae", , ), , "([äãàâãåáąÄÃÀÂÃÅÁĄA])", , "a", , ), , "([B])", , "b", , ), , "([CçčćÇČĆ])", , "c", , ), , "([ďĎD])", , "d", , ), , "([ëèéêęěËÈÉÊĘĚE])", , "e", , ), , "([F])", , "f", , ), , "([G])", , "g", , ), , "([H])", , "h", , ), , "([ïîìíÏÎÌÍI])", , "i", , ), , "([J])", , "j", , ), , "([K])", , "k", , ), , "([ľłŁĽL])", , "l", , ), , "([M])", , "m", , ), , "([ñńňÑŃŇN])", , "n", , ), , "([öòőõôóÖÒŐÕÔÓO])", , "o", , ), , "([P])", , "p", , ), , "([Q])", , "q", , ), , "([řŘR])", , "r", , ), , "([ßšśŠŚS])", , "s", , ), , "([TŤť])", , "t", , ), , "([üùûúůűÜÙÛÚŮŰU])", , "u", , ), , "([V])", , "v", , ), , "([W])", , "w", , ), , "([ýÿýŸÝY])", , "y", , ), , "([źžżŹŽŻZ])", , "z", , ), " ", , , "", , ), "contoso.com")**   | userPrincipalName     |     | Crear y Actualizar                                                   
| **Switch(\[Municipality\], "OU=Standard Users,OU=Users,OU=Default,OU=Locations,DC=contoso,DC=com", "Dallas", "OU=Standard Users,OU=Users,OU=Dallas,OU=Locations,DC=contoso,DC=com", "Austin", "OU=Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", “London", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  Crear y Actualizar |
  
### <a name="part-3-configure-the-on-premises-synchronization-agent"></a>Parte 3: configuración del agente de sincronización local

Para poder efectuar un aprovisionamiento en Active Directory local, debe haber un agente instalado en un servidor unido a un dominio en el bosque de Active Directory deseado. Se necesitan credenciales de administrador de dominios (o de administrador empresarial) para poder llevar a cabo el procedimiento.

**[Aquí puede descargar el agente de sincronización local](https://go.microsoft.com/fwlink/?linkid=847801)**

Una vez instalado el agente, ejecute los siguientes comandos de PowerShell para configurar el agente para su entorno.

**Comando n.º 1**

> cd C:\\Archivos de programa\\Microsoft Azure Active Directory Synchronization Agent\\Modules\\AADSyncAgent

> import-module AADSyncAgent.psd1

**Comando n.º 2**

> Add-ADSyncAgentActiveDirectoryConfiguration

* Entrada: en "DirectoryName", escriba el nombre del bosque de AD igual que en la parte n.\# 2
* Entrada: nombre y contraseña del usuario administrador del bosque de Active Directory

**Comando n.º 3**

> Add-ADSyncAgentAzureActiveDirectoryConfiguration

* Entrada: nombre y contraseña del usuario administrador global de su inquilino de Azure AD

>[!IMPORTANT]
>Actualmente hay un problema conocido con las credenciales de administrador global y es que no funcionan si usan un dominio personalizado (por ejemplo, admin@contoso.com). Como solución alternativa, cree y use una cuenta de administrador global con un dominio onmicrosoft.com (por ejemplo, admin@contoso.onmicrosoft.com).


**Comando n.º 4**

> Get-AdSyncAgentProvisioningTasks

* Acción: se devuelven los datos de confirmación. Este comando detecta automáticamente las aplicaciones de aprovisionamiento de Workday en su inquilino de Azure AD. Salida de ejemplo:

> Nombre: Mi bosque de AD
>
> Habilitado: True
>
> DirectoryName : mydomain.contoso.com
>
> Credenciales: False
>
> Identificador: WDAYdnAppDelta.c2ef8d247a61499ba8af0a29208fb853.4725aa7b-1103-41e6-8929-75a5471a5203

**Comando n.º 5**

> Start-AdSyncAgentSynchronization -Automatic

**Comando n.º 6**

> net stop aadsyncagent

**Comando n.º 7**

> net start aadsyncagent

>[!TIP]
>Además de los comandos "net" de Powershell, el servicio del agente de sincronización también pueden iniciarse y detenerse mediante **Services.msc**. Si se producen errores al ejecutar los comandos de Powershell, asegúrese de que **Microsoft Azure AD Connect Provisioning Agent** se ejecuta en **Services.msc**.

![Services](./media/active-directory-saas-workday-inbound-tutorial/Services.png)  

**Configuración adicional para clientes de la Unión Europea**

Si su inquilino de Azure Active Directory se encuentra en uno de los centros de datos de la UE, siga los pasos adicionales que se describen a continuación.

1. Abra **Services.msc** y detenga el servicio **Microsoft Azure AD Connect Provisioning Agent**.
2. Vaya a la carpeta de instalación del agente (por ejemplo, C:\Program Files\Microsoft Azure AD Connect Provisioning Agent).
3. Abra **SyncAgnt.exe.config** en un editor de texto.
4. Reemplace https://manage.hub.syncfabric.windowsazure.com/Management por **https://eu.manage.hub.syncfabric.windowsazure.com/Management**.
5. Reemplace https://provision.hub.syncfabric.windowsazure.com/Provisioning por **https://eu.provision.hub.syncfabric.windowsazure.com/Provisioning**.
6. Guarde el archivo **SyncAgnt.exe.config**.
7. Abra **Services.msc** e inicie el servicio **Microsoft Azure AD Connect Provisioning Agent**.

**Solución de problemas del agente**

El [registro de eventos de Windows](https://technet.microsoft.com/en-us/library/cc722404(v=ws.11).aspx) en la máquina Windows Server que hospeda el agente contiene eventos de todas las operaciones realizadas por el agente. Para ver estos eventos:
    
1. Abra **Eventvwr.msc**.
2. Seleccione **Registros de Windows > Aplicación**.
3. Vea todos los eventos registrados en el origen **AADSyncAgent**. 
4. Compruebe los errores y las advertencias.

Si hay un problema de permisos con Active Directory o con las credenciales de Azure Active Directory proporcionadas en los comandos de Powershell, verá un error como este: 
    
![Registros de eventos](./media/active-directory-saas-workday-inbound-tutorial/Windows_Event_Logs.png) 


### <a name="part-4-start-the-service"></a>Parte 4: inicio del servicio
Una vez que las partes 1-3 se han completado, puede volver a iniciar el servicio de aprovisionamiento en Azure Portal.

1.  En la pestaña **Aprovisionamiento**, establezca **Estado de aprovisionamiento** en **Activado**.

2. Haga clic en **Guardar**.

3. Se iniciará la sincronización inicial, que puede tardar una cantidad de horas variable, según el número de usuarios que haya en Workday.

4. En cualquier momento, compruebe la pestaña **Registros de auditoría** en Azure Portal para ver las acciones que ha realizado el servicio de aprovisionamiento. Los registros de auditoría muestran todos los eventos de sincronización individuales realizados por el servicio de aprovisionamiento, por ejemplo, los usuarios que se leen fuera de Workday y que luego se agregan o actualizan en Active Directory. **[Consulte la guía de creación de informes de aprovisionamiento para obtener instrucciones detalladas sobre cómo leer los registros de auditoría](active-directory-saas-provisioning-reporting.md)**.

5.  Compruebe el [registro de eventos de Windows](https://technet.microsoft.com/en-us/library/cc722404(v=ws.11).aspx) en la máquina Windows Server que hospeda el agente para ver si hay nuevos errores o advertencias. Estos eventos se pueden ver si se inicia **Eventvwr.msc** en el servidor y se selecciona **Registros de Windows > Aplicación**. Todos los mensajes relacionados con el aprovisionamiento se registran en el origen **AADSyncAgent**. 
    

6. Una vez finalizada la operación, se escribe un informe resumido de auditoría en la pestaña **Aprovisionamiento**, tal y como se muestra a continuación.

![Azure Portal](./media/active-directory-saas-workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>Configuración del aprovisionamiento de usuarios en Azure Active Directory
El modo de configuración del aprovisionamiento en Azure Active Directory dependerá de sus requisitos de aprovisionamiento, tal como se detalla en la tabla siguiente.

| Escenario | Solución |
| -------- | -------- |
| **Los usuarios se deben aprovisionar en Active Directory y en Azure AD** | Use **[AAD Connect](connect/active-directory-aadconnect.md)** |
| **Los usuarios solo se deben aprovisionar en Active Directory** | Use **[AAD Connect](connect/active-directory-aadconnect.md)** |
| **Los usuarios solo se deben aprovisionar en Azure AD (solo los usuarios que están en la nube)** | Use la aplicación de **aprovisionamiento de Workday en Azure Active Directory** en la galería de aplicaciones |

Para obtener instrucciones sobre cómo configurar Azure AD Connect, consulte la [documentación de Azure AD Connect](connect/active-directory-aadconnect.md).

En las siguientes secciones se describe cómo configurar una conexión entre Workday y Azure AD para aprovisionar usuarios que solo están en la nube.

> [!IMPORTANT]
> Siga el siguiente procedimiento únicamente si tiene usuarios que solo están en la nube que se deban aprovisionar en Azure AD, y no en Active Directory local.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: agregación de la aplicación del conector de aprovisionamiento de Azure AD y creación de la conexión con Workday

**Para configurar el aprovisionamiento de Workday en Azure Active Directory para los usuarios que solo están en la nube:**

1.  Vaya a <https://portal.azure.com>.

2.  En la barra de navegación de la izquierda, seleccione **Azure Active Directory**.

3.  Seleccione **Enterprise Applications** (Aplicaciones empresariales) y **All Applications** (Todas las aplicaciones).

4.  Seleccione **Agregar una aplicación** y, luego, seleccione la categoría **Todos**.

5.  Busque **Workday to Azure AD provisioning** (Aprovisionamiento de Workday en Azure AD) y agregue esa aplicación desde la galería.

6.  Una vez que se haya agregado la aplicación y se muestre la pantalla de detalles de la aplicación, seleccione **Aprovisionamiento**.

7.  Cambie el **modo** **de aprovisionamiento** a **Automático**.

8.  Cumplimente la sección **Credenciales de administrador** del siguiente modo:

   * **Nombre de usuario de administrador**: escriba el nombre de usuario de la cuenta del sistema de integración de Workday, anexando el nombre de dominio del inquilino. Debe tener un aspecto similar al siguiente: username@contoso4

   * **Contraseña de administrador**: escriba la contraseña de la cuenta del sistema de integración de Workday.

   * **URL de inquilino**: escriba la dirección URL al punto de conexión de servicios web de Workday de su inquilino. Debería ser similar a https://wd3-impl-services1.workday.com/ccx/service/contoso4, donde contoso4 se reemplaza por el nombre correcto del inquilino y wd3-impl se reemplaza por la cadena de entorno correcta. Si no se conoce esta dirección URL, trabaje con su asociado de integración o representante de soporte técnico de Workday para determinar la dirección URL correcta que se debe usar.

   * **Correo electrónico de notificación**: escriba su dirección de correo electrónico y marque la casilla "Enviar una notificación por correo electrónico cuando se produzca un error".

   * Haga clic en el botón **Probar conexión**.

   * Si la prueba de conexión se lleva a cabo correctamente, haga clic en el botón **Guardar** situado en la parte superior. Si se produce un error, compruebe que la dirección URL y las credenciales de Workday sean válidas en Workday.


### <a name="part-2-configure-attribute-mappings"></a>Parte 2: configuración de las asignaciones de atributos 

En esta sección configurará cómo fluyen los datos de los usuarios de Workday a Azure Active Directory para los usuarios que solo están en la nube.

1.  En la pestaña Aprovisionamiento, en **Asignaciones**, haga clic en **Synchronize Workers to Azure AD** (Sincronizar trabajadores con Azure AD).

2.   En el campo **Ámbito de objeto de origen** puede seleccionar los conjuntos de usuarios de Workday que deben estar en el ámbito para el aprovisionamiento en Azure AD; para ello debe definir un conjunto de filtros basados en atributos. El ámbito predeterminado es "Todos los usuarios de Workday". Filtros de ejemplo:

   * Ejemplo: ámbito de los usuarios que tengan los id. de trabajador comprendidos entre 1 000 000 y 2 000 000

      * Atributo: WorkerID

      * Operador: REGEX.COINCIDIR

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Ejemplo: solo los trabajadores temporales, no los empleados habituales

      * Atributo: ContingentID

      * Operador: NO ES NULO

3.  En el campo **Acciones del objeto de destino**, puede filtrar de forma global las acciones permitidas en Azure AD. **Crear** y **Actualizar** son las más habituales.

4.  En la sección **Asignaciones de atributos** puede definir cómo se asignan los distintos atributos de Workday a los atributos de Active Directory.

5. Haga clic en una asignación de atributos existente para actualizarla o haga clic en **Agregar nueva asignación** en la parte inferior de la pantalla para agregar asignaciones nuevas. Las asignaciones de atributos admiten estas propiedades:

   * **Tipo de asignación**

      * **Directo**: escribe el valor del atributo de Workday en el atributo de AD sin cambios.

      * **Constante**: escribe un valor de cadena estático y constante en el atributo de AD.

      * **Expresión**: le permite escribir un valor personalizado en el atributo de AD, en función de uno o varios atributos de Workday. [Para obtener más información, consulte este artículo sobre las expresiones](active-directory-saas-writing-expressions-for-attribute-mappings.md).

   * **Atributo de origen**: el atributo de usuario de Workday.

   * **Valor predeterminado**: opcional. Si el atributo de origen tiene un valor vacío, la asignación escribirá este valor.
            La configuración más habitual consiste en dejarlo en blanco.

   * **Atributo de destino**: atributo de usuario de Azure AD.

   * **Hacer coincidir objetos con este atributo**: especifica si se debe usar o no esta asignación para identificar de forma unívoca a los usuarios entre Workday y Azure AD. Se suele establecer en el campo Id. del trabajador de Workday, que se suele asignar al atributo de id. de empleado (nuevo) o a un atributo de extensión de Azure AD.

   * **Precedencia de coincidencia**: se pueden establecer varios atributos coincidentes. Si hay varios, se evalúan en el orden definido por este campo. En el momento en que se encuentre una coincidencia, no se evaluarán más atributos coincidentes.

   * **Aplicar esta asignación**

     * **Siempre**: esta asignación se aplica a las acciones de creación y actualización de usuarios

     * **Solo durante la creación**: esta asignación se aplica solo a las acciones de creación de usuarios

6. Para guardar las asignaciones, haga clic en **Guardar** en la parte superior de la sección Asignación de atributos.

### <a name="part-3-start-the-service"></a>Parte 3: inicio del servicio
Después de las partes 1 y 2, puede iniciar el servicio de aprovisionamiento.

1.  En la pestaña **Aprovisionamiento**, establezca **Estado de aprovisionamiento** en **Activado**.

2. Haga clic en **Guardar**.

3. Se iniciará la sincronización inicial, que puede tardar una cantidad de horas variable, según el número de usuarios que haya en Workday.

4. Los eventos de sincronización se pueden ver en la pestaña **Registros de auditoría**. **[Consulte la guía de creación de informes de aprovisionamiento para obtener instrucciones detalladas sobre cómo leer los registros de auditoría](active-directory-saas-provisioning-reporting.md)**.

5. Una vez finalizada la operación, se escribe un informe resumido de auditoría en la pestaña **Aprovisionamiento**, tal y como se muestra a continuación.


## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Configuración de la reescritura de direcciones de correo electrónico en Workday
Siga estas instrucciones para configurar la reescritura de direcciones de correo electrónico de los usuarios de Azure Active Directory a Workday.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: agregación de la aplicación del conector de aprovisionamiento y creación de la conexión con Workday

**Para configurar Workday en el aprovisionamiento de Active Directory:**

1.  Vaya a <https://portal.azure.com>

2.  En la barra de navegación de la izquierda, seleccione **Azure Active Directory**.

3.  Seleccione **Enterprise Applications** (Aplicaciones empresariales) y **All Applications** (Todas las aplicaciones).

4.  Seleccione **Agregar una aplicación** y, luego, seleccione la categoría **Todos**.

5.  Busque **Workday Writeback** (Reescritura de Workday) y agregue esa aplicación desde la galería.

6.  Una vez que se haya agregado la aplicación y se muestre la pantalla de detalles de la aplicación, seleccione **Aprovisionamiento**.

7.  Cambie el **modo** **de aprovisionamiento** a **Automático**.

8.  Cumplimente la sección **Credenciales de administrador** del siguiente modo:

   * **Nombre de usuario de administrador**: escriba el nombre de usuario de la cuenta del sistema de integración de Workday, anexando el nombre de dominio del inquilino. Debe tener un aspecto similar al siguiente: username@contoso4

   * **Contraseña de administrador**: escriba la contraseña de la cuenta del sistema de integración de Workday.

   * **URL de inquilino**: escriba la dirección URL al punto de conexión de servicios web de Workday de su inquilino. Debería ser similar a https://wd3-impl-services1.workday.com/ccx/service/contoso4, donde contoso4 se reemplaza por el nombre correcto del inquilino y wd3-impl se reemplaza por la cadena de entorno correcta (si es necesario).

   * **Correo electrónico de notificación**: escriba su dirección de correo electrónico y marque la casilla "Enviar una notificación por correo electrónico cuando se produzca un error".

   * Haga clic en el botón **Probar conexión**. Si la prueba de conexión se lleva a cabo correctamente, haga clic en el botón **Guardar** situado en la parte superior. Si se produce un error, compruebe que la dirección URL y las credenciales de Workday sean válidas en Workday.


### <a name="part-2-configure-attribute-mappings"></a>Parte 2: configuración de las asignaciones de atributos 


En esta sección configurará cómo fluyen los datos de los usuarios de Workday a Active Directory.

1.  En la pestaña Aprovisionamiento, en **Asignaciones**, haga clic en **Synchronize Azure AD Users to Workday** (Sincronizar usuarios de Azure AD con Workday).

2.  En el campo **Ámbito de objeto de origen** tiene la opción de filtrar los conjuntos de usuarios de Azure Active Directory que deben reescribir sus direcciones de correo electrónico en Workday. El ámbito predeterminado es "Todos los usuarios de Azure AD". 

3.  En la sección **Asignaciones de atributos** puede definir cómo se asignan los distintos atributos de Workday a los atributos de Active Directory. De forma predeterminada, hay una asignación para la dirección de correo electrónico, aunque el identificador coincidente se debe actualizar para hacer coincidir los usuarios de Azure AD con sus entradas correspondientes de Workday. Un método de búsqueda de coincidencias conocido consiste en sincronizar el Id. de trabajador de Workday o el Id. de empleado con el atributo extensionAttribute1-15 en Azure AD y, luego, usar dicho atributo en Azure AD para hacer coincidir los usuarios en Workday.

4.  Para guardar las asignaciones, haga clic en **Guardar** en la parte superior de la sección Asignación de atributos.

### <a name="part-3-start-the-service"></a>Parte 3: inicio del servicio
Después de las partes 1 y 2, puede iniciar el servicio de aprovisionamiento.

1.  En la pestaña **Aprovisionamiento**, establezca **Estado de aprovisionamiento** en **Activado**.

2. Haga clic en **Guardar**.

3. Se iniciará la sincronización inicial, que puede tardar una cantidad de horas variable, según el número de usuarios que haya en Workday.

4. Los eventos de sincronización se pueden ver en la pestaña **Registros de auditoría**. **[Consulte la guía de creación de informes de aprovisionamiento para obtener instrucciones detalladas sobre cómo leer los registros de auditoría](active-directory-saas-provisioning-reporting.md)**.

5. Una vez finalizada la operación, se escribe un informe resumido de auditoría en la pestaña **Aprovisionamiento**, tal y como se muestra a continuación.

## <a name="known-issues"></a>Problemas conocidos

* Al ejecutar el comando **Add-ADSyncAgentAzureActiveDirectoryConfiguration** de Powershell, existe actualmente un problema conocido con las credenciales de administrador global, y es que no funcionan si usan un dominio personalizado (por ejemplo, admin@contoso.com). Como solución alternativa, cree y use una cuenta de administrador global en Azure AD con un dominio onmicrosoft.com (por ejemplo, admin@contoso.onmicrosoft.com).

* Se ha resuelto un problema anterior en el que los registros de auditoría no aparecían en los inquilinos de Azure AD ubicados en la Unión Europea. Sin embargo, se requiere la configuración adicional del agente para inquilinos de Azure AD en la Unión Europea. Para más información, consulte [Parte 3: configuración del agente de sincronización local](#Part 3: Configure the on-premises synchronization agent).

## <a name="additional-resources"></a>Recursos adicionales
* [Tutorial: configuración del inicio de sesión único entre Workday y Azure Active Directory](active-directory-saas-workday-tutorial.md)
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting)
