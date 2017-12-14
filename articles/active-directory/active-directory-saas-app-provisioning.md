---
title: Aprovisionamiento automatizado de usuarios para aplicaciones SaaS en Azure AD | Microsoft Docs
description: "Una introducción sobre cómo puede usar Azure AD para el aprovisionamiento, el desaprovisionamiento y la actualización continua de cuentas de usuario de manera automática en varias aplicaciones SaaS de terceros."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: mtillman
editor: 
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2017
ms.author: asmalser
ms.openlocfilehash: cd82ef109abbc5707db4c02c2f14f9d55dfb74e3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>¿Qué es el aprovisionamiento automático de usuarios para aplicaciones SaaS?
Azure Active Directory (Azure AD) permite automatizar la creación, el mantenimiento y la eliminación de identidades de usuario en aplicaciones de nube ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) como Dropbox, Salesforce, ServiceNow y muchas más.

**A continuación se muestran algunos ejemplos de lo que esta característica permite hacer:**

* Crear cuentas automáticamente en los sistemas adecuados para los usuarios nuevos cuando se unen a su equipo u organización.
* Desactivar las cuentas automáticamente en los sistemas adecuados cuando los usuarios dejan el equipo o la organización.
* Asegurarse de que las identidades de las aplicaciones y sistemas se mantienen actualizadas con los cambios del directorio o el sistema de recursos humanos.
* Aprovisionar los objetos que no sean de los usuarios, como los grupos, para las aplicaciones que los admitan.

**El aprovisionamiento automático de usuarios también incluye la funcionalidad siguiente:**

* La capacidad de hacer coincidir las identidades existentes entre los sistemas de origen y de destino.
* Opciones de personalización para ayudar a Azure AD a ajustar las configuraciones actuales de las aplicaciones y sistemas que su organización utiliza actualmente.
* Alertas de correo electrónico opcionales para errores de aprovisionamiento.
* Informes y registros de actividades para facilitar la supervisión y solución de problemas.

## <a name="why-use-automated-provisioning"></a>¿Por qué usar el aprovisionamiento automático?
Las razones habituales por las que se debe usar esta característica son:

* Evitar los costes, las ineficiencias y los errores humanos asociados con los procesos de aprovisionamiento manuales.
* Evitar los costos asociados al hospedaje y mantenimiento de los scripts y las soluciones de aprovisionamiento desarrollados de forma personalizada.
* Proteger su organización mediante la eliminación instantánea de las identidades de los usuarios de aplicaciones SaaS claves cuando estos abandonan la organización.
* Importar fácilmente una gran cantidad de usuarios a una aplicación o sistema SaaS concretos.
* Disfrutar de la comodidad de que la solución de aprovisionamiento se ejecute fuera de las mismas directivas de acceso a las aplicaciones definidas para el Inicio de sesión único de Azure AD.


## <a name="how-does-automatic-provisioning-work"></a>¿Cómo funciona el aprovisionamiento automático?
    
El **servicio de aprovisionamiento de Azure AD** aprovisiona usuarios para las aplicaciones SaaS y otros sistemas mediante la conexión de los puntos de conexión de la API de administración de usuarios proporcionados por el proveedor de cada aplicación. Estos puntos de conexión de la API de administración de usuarios permiten a Azure AD crear, actualizar y quitar usuarios mediante programación. Para las aplicaciones seleccionadas, el servicio de aprovisionamiento también puede crear, actualizar y quitar objetos adicionales relacionados con la identidad, como los grupos y los roles. 

![Aprovisionamiento](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*Figura 1: Servicio de aprovisionamiento de Azure AD*

![Aprovisionamiento de salida](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*Figura 2: Flujo de trabajo "saliente" del aprovisionamiento de usuarios desde Azure AD a aplicaciones SaaS populares*

![Aprovisionamiento de entrada](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*Figura 3: Flujo de trabajo "entrante" del aprovisionamiento de usuarios desde aplicaciones populares de administración del capital humano (HCM) a Azure Active Directory y Windows Server Active Directory*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>¿Qué aplicaciones y sistemas puedo usar con el aprovisionamiento automático de usuarios de Azure AD?

Las características preintegradas de Azure AD admiten una gran variedad de aplicaciones SaaS populares y sistemas de recursos humanos, así como compatibilidad genérica con las aplicaciones que implementan determinadas partes del [estándar SCIM 2.0](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning).

Todas las aplicaciones destacadas de la galería de aplicaciones de Azure AD admiten el aprovisionamiento automático de usuarios. [Puede ver la lista de aplicaciones destacadas aquí.](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)

Para que una aplicación admita el aprovisionamiento automático de usuarios, primero debe proporcionar los puntos de conexión de administración de usuarios necesarios que permitan a los programas externos automatizar la creación, el mantenimiento y la eliminación de usuarios. Por lo tanto, no todas las aplicaciones SaaS son compatibles con esta característica. En el caso de las aplicaciones que sí son compatibles con las API de administración de usuarios, el equipo de ingeniería de Azure AD podrá crear un conector de aprovisionamiento para esas aplicaciones, y las prioridades para realizar este trabajo se basan en las necesidades de los clientes actuales y potenciales. 

Si desea ponerse en contacto con el equipo de ingeniería de Azure AD para solicitar soporte técnico para el aprovisionamiento de aplicaciones adicionales, envíe un mensaje a través del [foro de comentarios de Azure Active Directory](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/category/172035-user-provisioning). 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>¿Cómo configuro el aprovisionamiento automático para una aplicación?

La configuración del servicio de aprovisionamiento de Azure AD para una aplicación seleccionada se inicia en  **[Azure Portal](https://potal.azure.com)**. En la sección **Azure Active Directory > Aplicaciones empresariales**, seleccione **Agregar**, **Todos** y, a continuación, agregue lo que necesite entre los elementos siguientes en función de su escenario:

* Todas las aplicaciones de la sección **Aplicaciones destacadas** admiten el aprovisionamiento automático

* Use la opción "Aplicación situada fuera de la galería" para las integraciones de SCIM desarrolladas de forma personalizada

![Galería](./media/active-directory-saas-app-provisioning/gallery.png)

En la pantalla de administración de aplicaciones, el aprovisionamiento se configura en la pestaña **Aprovisionamiento**.

![Settings](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* Se deben proporcionar las **credenciales de administrador** al servicio de aprovisionamiento de Azure AD que le permitirá conectarse a la API de administración de usuarios proporcionada por la aplicación.

* Se pueden configurar **asignaciones de atributo** que especifiquen qué campos del sistema de origen (ejemplo: Azure AD) tendrán su contenido sincronizado con los campos del sistema de destino (ejemplo: ServiceNow). Si la aplicación de destino lo admite, esta sección le permitirá configurar, si lo desea, el aprovisionamiento de grupos, además de las cuentas de usuario. La "búsqueda de coincidencia de propiedades" le permite seleccionar los campos que se usan para buscar cuentas entre los sistemas. Las "[expresiones](active-directory-saas-writing-expressions-for-attribute-mappings.md)" le permiten modificar y transformar los valores recuperados del sistema de origen antes de que se escriban en el sistema de destino. Para obtener más información, vea [Personalización de la asignación de atributos](active-directory-saas-customizing-attribute-mappings.md).

![Settings](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* Los **filtros de ámbito** le indican al servicio de aprovisionamiento qué usuarios y grupo del sistema de origen se deben aprovisionar o desaprovisionar para el sistema de destino. Hay dos aspectos en los filtros de ámbito que se evalúan conjuntamente y que determinan quién está en el ámbito para el aprovisionamiento:

* **Filter on attribute values** (Filtrar por valores de atributo): el menú "Ámbito de objeto de origen" en las asignaciones de atributos permite el filtrado según valores de atributo determinados. Por ejemplo, puede especificar que solo los usuarios con el atributo "Department" (Departamento) de "Sales" (Ventas) deben estar en el ámbito del aprovisionamiento.

* **Filter on assignments** (Filtrar por asignaciones): el menú "Ámbito" en la sección Aprovisionamiento &gt; Configuración del portal le permite especificar si solo los usuarios y los grupos "asignados" deben estar en el ámbito de aprovisionamiento o si se deben aprovisionar todos los usuarios del directorio de Azure AD. Para obtener más información sobre la "asignación" de usuarios y grupos, consulte [Asignación de un usuario o un grupo a una aplicación empresarial en Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md).
    
* La **configuración** controla el funcionamiento del servicio de aprovisionamiento de una aplicación, tanto si se está ejecutando como si no.

* Los **registros de auditoría** proporcionan registros de todas las operaciones realizadas por el servicio de aprovisionamiento de Azure AD. Para obtener más detalles, consulte la [guía de informes de aprovisionamiento](active-directory-saas-provisioning-reporting.md).

![Settings](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

## <a name="what-happens-during-provisioning"></a>¿Qué ocurre durante el aprovisionamiento?

1. Al habilitar el aprovisionamiento de una aplicación por primera vez, se realizan las siguientes acciones:
   * Azure AD intentará hacer coincidir todos los usuarios existentes en la aplicación SaaS con sus identidades correspondientes en el directorio. Cuando se asocia un usuario, *no* se habilitan automáticamente para el inicio de sesión único. Para que un usuario disponga de acceso a la aplicación, deben asignarse explícitamente a la aplicación en Azure AD, ya sea directamente o a través de la pertenencia a grupos.
   * Si ya ha especificado qué usuarios deben asignarse a la aplicación, y si Azure AD no logra encontrar cuentas existentes para tales usuarios, Azure AD aprovisionará cuentas nuevas para ellos en la aplicación.
2. Una vez que se haya completado la sincronización inicial como se describió anteriormente, Azure AD comprobará cada 20 minutos si se han producido los cambios siguientes:
   * Si los nuevos usuarios se han asignado a la aplicación (ya sea directamente o a través de la pertenencia a grupos), se aprovisionan con una nueva cuenta en la aplicación SaaS.
   * Si se ha quitado el acceso de un usuario, su cuenta en la aplicación SaaS se marca como deshabilitada (los usuarios nunca se eliminan totalmente, lo que le protege contra la pérdida de datos, si se produce un error de configuración).
   * Si un usuario se asignó recientemente a la aplicación y ya tenía una cuenta en la aplicación SaaS, esa cuenta se marca como habilitada y algunas propiedades de usuario pueden actualizarse si están obsoletas en comparación con el directorio.
   * Si ha cambiado la información del usuario (por ejemplo, el número de teléfono o la ubicación de la oficina) en el directorio, esta información también se actualizará en la aplicación SaaS.


## <a name="frequently-asked-questions"></a>Preguntas más frecuentes
**¿Con qué frecuencia Azure AD escribe los cambios de directorio en las aplicaciones SaaS?**

Una vez se haya realizado una sincronización completa inicial, el servicio de aprovisionamiento de Azure AD suele comprobar cada 20 minutos si hay cambios. 

Si la aplicación SaaS devuelve varios errores (como en el caso de las credenciales de administrador no válidas), Azure AD disminuye gradualmente su frecuencia hasta una vez al día hasta que se solucionan los errores. Cuando esto sucede, el trabajo de aprovisionamiento de Azure AD entra en un estado de "cuarentena" y lo indica en el [informe de resumen del aprovisionamiento](active-directory-saas-provisioning-reporting.md).

**¿Cuánto tiempo se tarda en aprovisionar usuarios?**

Después de que se haya realizado una sincronización completa inicial, los cambios incrementales suelen realizarse entre 20 y 40 minutos después. Si intenta aprovisionar a la mayor parte de su directorio, depende del número de usuarios y grupos que tenga. El rendimiento depende del rendimiento de las API de administración de usuario que los servicios de aprovisionamiento usan para leer los datos del sistema de origen y para escribirlos en el sistema de destino. 

El rendimiento es también menor si hay muchos errores (registrados en los [registros de auditoría](active-directory-saas-provisioning-reporting.md)) y el servicio de aprovisionamiento ha quedado en un estado de "cuarentena".

**¿Qué es una sincronización completa inicial y por qué tarda más que las sincronizaciones posteriores?**

La primera vez que se ejecuta el servicio de aprovisionamiento de Azure AD para una aplicación determinada, toma una "instantánea" de los usuarios (y, opcionalmente, de los grupos) en el directorio de origen. Esta instantánea permite que el servicio de aprovisionamiento reduzca el número de viajes de ida y vuelta de las API de origen y de destino, y permite que las sincronizaciones "de diferencias" posteriores se comporten de forma más eficaz. 

La sincronización completa inicial de los usuarios a menudo se puede completar en minutos para los directorios muy pequeños, pero puede tardar varias horas en los directorios mayores. Los directorios de empresa con cientos de miles de usuarios pueden tardar muchas horas en realizar la sincronización inicial. Sin embargo, después de la sincronización inicial, las sincronizaciones posteriores "de diferencias" tienen lugar mucho más rápidamente.

> [!NOTE]
> En el caso de las aplicaciones que admiten el aprovisionamiento de grupos y de las pertenencias a grupos, habilitar esta opción aumenta en gran medida el tiempo necesario para que una sincronización completa se lleve a cabo. Si no desea aprovisionar los nombres de grupos y la pertenencia a grupos para una aplicación, puede deshabilitar esta opción en las [asignaciones de atributo](active-directory-saas-customizing-attribute-mappings.md) de la configuración del aprovisionamiento.

**¿Cómo se puede seguir el progreso del trabajo de aprovisionamiento actual?**

Consulte la [guía de informes de aprovisionamiento](active-directory-saas-provisioning-reporting.md).

**¿Cómo puedo saber si se produce algún error en el aprovisionamiento de un usuario?**

Todos los errores se copian en los registros de auditoría de Azure AD. Para obtener más información, consulte la [guía de informes de aprovisionamiento](active-directory-saas-provisioning-reporting.md).

**¿Cómo puedo enviar comentarios al equipo de ingeniería?**

Póngase en contacto con nosotros a través del [foro de comentarios de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).


## <a name="related-articles"></a>Artículos relacionados
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [Personalización de asignaciones de atributos para el aprovisionamiento de usuarios](active-directory-saas-customizing-attribute-mappings.md)
* [Escritura de expresiones para asignaciones de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de ámbito para el aprovisionamiento de usuario](active-directory-saas-scoping-filters.md)
* [Uso de SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory a aplicaciones](active-directory-scim-provisioning.md)
* [Notificaciones de aprovisionamiento de cuentas](active-directory-saas-account-provisioning-notifications.md)
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS](active-directory-saas-tutorial-list.md)

