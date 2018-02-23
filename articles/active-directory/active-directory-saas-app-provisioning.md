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
ms.openlocfilehash: e14ba62ce2d6c48e47a6b75387bcede68bb1a5b0
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2018
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
* Asignaciones de atributos personalizables que definen qué datos del usuario deben fluir del sistema de origen al sistema de destino.
* Alertas de correo electrónico opcionales para errores de aprovisionamiento.
* Informes y registros de actividades para facilitar la supervisión y solución de problemas.

## <a name="why-use-automated-provisioning"></a>¿Por qué usar el aprovisionamiento automático?
Las razones habituales por las que se debe usar esta característica son:

* Evitar los costos, las ineficiencias y los errores humanos asociados con los procesos de aprovisionamiento manuales.
* Evitar los costos asociados al hospedaje y el mantenimiento de scripts y soluciones de aprovisionamiento desarrollados de forma personalizada.
* Proteger su organización mediante la eliminación instantánea de las identidades de los usuarios de aplicaciones SaaS claves cuando estos abandonan la organización.
* Importar fácilmente una gran cantidad de usuarios a una aplicación o sistema SaaS concretos.
* Disfrutar de tener un único conjunto de directivas para determinar quién se aprovisiona y quién puede iniciar sesión en una aplicación.


## <a name="how-does-automatic-provisioning-work"></a>¿Cómo funciona el aprovisionamiento automático?
    
El **servicio de aprovisionamiento de Azure AD** aprovisiona usuarios para las aplicaciones SaaS y otros sistemas mediante la conexión de los puntos de conexión de la API de administración de usuarios proporcionados por el proveedor de cada aplicación. Estos puntos de conexión de la API de administración de usuarios permiten a Azure AD crear, actualizar y quitar usuarios mediante programación. Para las aplicaciones seleccionadas, el servicio de aprovisionamiento también puede crear, actualizar y quitar objetos adicionales relacionados con la identidad, como los grupos y los roles. 

![Aprovisionamiento](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*Figura 1: Servicio de aprovisionamiento de Azure AD*

![Aprovisionamiento de salida](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*Figura 2: Flujo de trabajo "saliente" del aprovisionamiento de usuarios desde Azure AD a aplicaciones SaaS populares*

![Aprovisionamiento de entrada](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*Figura 3: Flujo de trabajo "entrante" del aprovisionamiento de usuarios desde aplicaciones populares de administración del capital humano (HCM) a Azure Active Directory y Windows Server Active Directory*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>¿Qué aplicaciones y sistemas puedo usar con el aprovisionamiento automático de usuarios de Azure AD?

Las características preintegradas de Azure AD admiten una gran variedad de aplicaciones SaaS populares y sistemas de recursos humanos, así como compatibilidad genérica con aplicaciones que implementan determinadas partes del estándar SCIM 2.0.

Para ver una lista de todas las aplicaciones en las que Azure AD admite un conector de aprovisionamiento previamente integrado, consulte la [lista de tutoriales de aplicaciones de aprovisionamiento de usuarios](active-directory-saas-tutorial-list.md).

Para información sobre cómo agregar compatibilidad con el aprovisionamiento de usuarios de Azure AD a una aplicación, consulte [Uso de System for Cross-Domain Identity Management para aprovisionar automáticamente a los usuarios y grupos de Azure Active Directory para aplicaciones](active-directory-scim-provisioning.md).

Si desea ponerse en contacto con el equipo de ingeniería de Azure AD para solicitar soporte técnico para el aprovisionamiento de aplicaciones adicionales, envíe un mensaje a través del [foro de comentarios de Azure Active Directory](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).    

> [!NOTE]
> Para que una aplicación admita el aprovisionamiento automático de usuarios, primero debe proporcionar las API de administración de usuarios necesarias que permitan a los programas externos automatizar la creación, el mantenimiento y la eliminación de usuarios. Por lo tanto, no todas las aplicaciones SaaS son compatibles con esta característica. En el caso de las aplicaciones que sí son compatibles con las API de administración de usuarios, el equipo de ingeniería de Azure AD podrá crear un conector de aprovisionamiento para esas aplicaciones, y las prioridades para realizar este trabajo se basan en las necesidades de los clientes actuales y potenciales. 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>¿Cómo configuro el aprovisionamiento automático para una aplicación?

La configuración del servicio de aprovisionamiento de Azure AD para una aplicación seleccionada se inicia en  **[Azure Portal](https://portal.azure.com)**. En la sección **Azure Active Directory > Aplicaciones empresariales**, seleccione **Agregar**, **Todos** y, a continuación, agregue lo que necesite entre los elementos siguientes en función de su escenario:

* Todas las aplicaciones de la sección **Aplicaciones destacadas** admiten el aprovisionamiento automático. Consulte la [lista de tutoriales de aplicaciones para el aprovisionamiento de usuarios]active-directory-saas-tutorial-list.md) para ver más.

* Use la opción "Aplicación situada fuera de la galería" para las integraciones de SCIM desarrolladas de forma personalizada

![Galería](./media/active-directory-saas-app-provisioning/gallery.png)

En la pantalla de administración de aplicaciones, el aprovisionamiento se configura en la pestaña **Aprovisionamiento**.

![Settings](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* Se deben proporcionar las **credenciales de administrador** al servicio de aprovisionamiento de Azure AD que le permitirá conectarse a la API de administración de usuarios proporcionada por la aplicación. Esta sección también le permite habilitar notificaciones por correo electrónico si las credenciales producen error o el trabajo de aprovisionamiento entra en [cuarentena](#quarantine).

* Se pueden configurar **asignaciones de atributo** que especifiquen qué campos del sistema de origen (ejemplo: Azure AD) tendrán su contenido sincronizado con los campos del sistema de destino (ejemplo: ServiceNow). Si la aplicación de destino lo admite, esta sección le permitirá configurar, si lo desea, el aprovisionamiento de grupos, además de las cuentas de usuario. La "búsqueda de coincidencia de propiedades" le permite seleccionar los campos que se usan para buscar cuentas entre los sistemas. Las "[expresiones](active-directory-saas-writing-expressions-for-attribute-mappings.md)" le permiten modificar y transformar los valores recuperados del sistema de origen antes de que se escriban en el sistema de destino. Para obtener más información, vea [Personalización de la asignación de atributos](active-directory-saas-customizing-attribute-mappings.md).

![Settings](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* Los **filtros de ámbito** le indican al servicio de aprovisionamiento qué usuarios y grupo del sistema de origen se deben aprovisionar o desaprovisionar para el sistema de destino. Hay dos aspectos en los filtros de ámbito que se evalúan conjuntamente y que determinan quién está en el ámbito para el aprovisionamiento:

    * **Filter on attribute values** (Filtrar por valores de atributo): el menú "Ámbito de objeto de origen" en las asignaciones de atributos permite el filtrado según valores de atributo determinados. Por ejemplo, puede especificar que solo los usuarios con el atributo "Department" (Departamento) de "Sales" (Ventas) deben estar en el ámbito del aprovisionamiento. Para más información, consulte [Uso de filtros de ámbito](active-directory-saas-scoping-filters.md).

    * **Filter on assignments** (Filtrar por asignaciones): el menú "Ámbito" en la sección Aprovisionamiento &gt; Configuración del portal le permite especificar si solo los usuarios y los grupos "asignados" deben estar en el ámbito de aprovisionamiento o si se deben aprovisionar todos los usuarios del directorio de Azure AD. Para obtener más información sobre la "asignación" de usuarios y grupos, consulte [Asignación de un usuario o un grupo a una aplicación empresarial en Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md).
    
* La **configuración** controla el funcionamiento del servicio de aprovisionamiento de una aplicación, tanto si se está ejecutando como si no.

* Los **registros de auditoría** proporcionan registros de todas las operaciones realizadas por el servicio de aprovisionamiento de Azure AD. Para obtener más detalles, consulte la [guía de informes de aprovisionamiento](active-directory-saas-provisioning-reporting.md).

![Settings](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

> [!NOTE]
> El servicio de aprovisionamiento de usuarios de Azure AD también se puede configurar y administrar mediante [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).


## <a name="what-happens-during-provisioning"></a>¿Qué ocurre durante el aprovisionamiento?

Si Azure AD es el sistema de origen, el servicio de aprovisionamiento usa la [característica de consulta diferencial de Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) para supervisar usuarios y grupos. El servicio de aprovisionamiento ejecuta una sincronización con el sistema de origen y el sistema de destino, seguida de sincronizaciones incrementales periódicas. 

### <a name="initial-sync"></a>Sincronización inicial
Cuando se inicia el servicio de aprovisionamiento, la primera sincronización realizada será así:

1. Se consultan todos los usuarios y grupos del sistema de origen y se recuperan todos los atributos definidos en la [asignación de atributos](active-directory-saas-customizing-attribute-mappings.md).
2. Se filtran los usuarios y grupos devueltos mediante cualquier [asignación](active-directory-coreapps-assign-user-azure-portal.md) o [filtro de ámbito basado en atributos](active-directory-saas-scoping-filters.md) configurados.
3. Cuando se detecta que un usuario se ha asignado o está en ámbito para el aprovisionamiento, el servicio consulta el sistema de destino en busca de un usuario coincidente mediante los [atributos coincidentes](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties) designados. Ejemplo: si el nombre de userPrincipal en el sistema de origen es el atributo coincidente y se asigna a userName en el sistema de destino, el servicio de aprovisionamiento consulta el sistema de destino en busca de valores userName que coincidan con los valores de nombre del sistema de origen.
4. Si no se encuentra un usuario coincidente en el sistema de destino, se crea mediante los atributos devueltos por el sistema de origen.
5. Si se encuentra un usuario coincidente, se actualiza mediante los atributos proporcionados por el sistema de origen.
6. Si las asignaciones de atributos contienen atributos de "referencia", el servicio realiza actualizaciones adicionales en el sistema de destino para crear y vincular los objetos a los que se hace referencia. Por ejemplo, un usuario puede tener un atributo "Administrador" en el sistema de destino, que está vinculado a otro usuario creado en el sistema de destino.
7. Se conserva una marca de agua al final de la sincronización inicial, que proporciona el punto de partida para las sucesivas sincronizaciones incrementales.

Algunas aplicaciones, como ServiceNow, Google Apps y Box no solo admiten el aprovisionamiento de usuarios, sino también el de los grupos y sus miembros. En esos casos, si el aprovisionamiento de grupos está habilitado en las [asignaciones](active-directory-saas-customizing-attribute-mappings.md), el servicio de aprovisionamiento sincroniza los usuarios y los grupos y luego las pertenencias del grupo. 

### <a name="incremental-syncs"></a>Sincronizaciones incrementales
Después de la sincronización inicial, todas las sincronizaciones posteriores serán así:

1. Se consulta el sistema de origen de los usuarios y grupos que se actualizaron desde la última marca de agua almacenada.
2. Se filtran los usuarios y grupos devueltos mediante cualquier [asignación](active-directory-coreapps-assign-user-azure-portal.md) o [filtro de ámbito basado en atributos](active-directory-saas-scoping-filters.md) configurados.
3. Cuando se detecta que un usuario se ha asignado o está en ámbito para el aprovisionamiento, el servicio consulta el sistema de destino en busca de un usuario coincidente mediante los [atributos coincidentes](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties) designados.
4. Si no se encuentra un usuario coincidente en el sistema de destino, se crea mediante los atributos devueltos por el sistema de origen.
5. Si se encuentra un usuario coincidente, se actualiza mediante los atributos proporcionados por el sistema de origen.
6. Si las asignaciones de atributos contienen atributos de "referencia", el servicio realiza actualizaciones adicionales en el sistema de destino para crear y vincular los objetos a los que se hace referencia. Por ejemplo, un usuario puede tener un atributo "Administrador" en el sistema de destino, que está vinculado a otro usuario creado en el sistema de destino.
7. Si un usuario que estaba anteriormente en ámbito del aprovisionamiento se quita del ámbito (lo que incluye estar sin asignar), el servicio deshabilita al usuario en el sistema de destino mediante una actualización.
8. Si un usuario que estaba anteriormente en ámbito del aprovisionamiento se deshabilita o se elimina temporalmente en el sistema de origen, el servicio deshabilita al usuario en el sistema de destino mediante una actualización.
9. Si un usuario que estaba anteriormente en ámbito del aprovisionamiento se elimina permanentemente en el sistema de origen, el servicio elimina al usuario en el sistema de destino. En Azure AD, los usuarios se eliminan permanentemente 30 días después de que se han eliminado temporalmente.
10. Se conserva una nueva marca de agua al final de la sincronización incremental, que proporciona el punto de partida para las sucesivas sincronizaciones incrementales.

>[!NOTE]
> Opcionalmente, puede deshabilitar las operaciones de creación, actualización o eliminación mediante las casillas **Acciones del objeto de destino** de la sección [Asignaciones de atributos](active-directory-saas-customizing-attribute-mappings.md). La lógica para deshabilitar un usuario durante una actualización también se controla mediante una asignación de atributos desde un campo como "accountEnabled".

El servicio de aprovisionamiento sigue ejecutando sincronizaciones incrementales opuestas de manera indefinida, según intervalos definidos en el [tutorial específico de cada aplicación](active-directory-saas-tutorial-list.md), hasta que se produce uno de los siguientes eventos:

* El servicio se detiene manualmente mediante Azure Portal o por medio del comando de Graph API adecuado. 
* Se desencadena una nueva sincronización inicial mediante la opción **Clear state and restart** (Borrar estado y reiniciar) de Azure Portal o por medio del comando de Graph API adecuado. Esta sincronización borra cualquier marca de agua almacenada y hace que todos los objetos de origen se evalúen de nuevo.
* Se desencadena una nueva sincronización inicial debido a un cambio en las asignaciones de atributos o los filtros de ámbito. Esta sincronización también borra cualquier marca de agua almacenada y hace que todos los objetos de origen se evalúen de nuevo.
* El proceso de aprovisionamiento entra en cuarentena (ver a continuación) debido a una alta tasa de errores y permanece en cuarentena durante más de cuatro semanas. En este caso, el servicio se deshabilita automáticamente.

### <a name="errors-and-retries"></a>Errores y reintentos 
Si un usuario individual no se puede agregar, actualizar o eliminar en el sistema de destino debido a un error en dicho sistema, la operación se reintenta en el siguiente ciclo de sincronización. Si el error continúa, los reintentos comienzan a producirse según una frecuencia reducida y disminuyen gradualmente hasta un solo intento al día. Para resolver el error, los administradores deben comprobar los [registros de auditoría](active-directory-saas-provisioning-reporting.md) en busca de eventos de "custodia de proceso" para determinar la causa y realizar la acción apropiada. Algunos errores comunes son:

* Usuarios que no tienen relleno un atributo en el sistema de origen que es necesario en el sistema de destino
* Usuarios que tienen un valor de atributo en el sistema de origen para el que existe una restricción única en el sistema de destino, y el mismo valor está presente en otro registro de usuario

Estos errores pueden resolverse mediante el ajuste de los valores de atributo del usuario afectado en el sistema de origen, o por medio del ajuste de las asignaciones de atributos para no provocar conflictos.   

### <a name="quarantine"></a>Cuarentena
Si todas o la mayoría de las llamadas realizadas al sistema de destino no tienen éxito sistemáticamente debido a un error (como en el caso de credenciales de administrador no válidas), el trabajo de aprovisionamiento entra en estado de "cuarentena". Esto se indica en el [informe de resumen de aprovisionamiento](active-directory-saas-provisioning-reporting.md) y por correo electrónico si se han configurado las notificaciones por correo electrónico en Azure Portal. 

En cuarentena, la frecuencia de las sincronizaciones incrementales se reduce gradualmente a una vez al día. 

El trabajo de aprovisionamiento se quita de la cuarentena después de que se han resuelto todos los errores causantes y se inicia el siguiente ciclo de sincronización. Si el trabajo de aprovisionamiento permanece en cuarentena durante más de cuatro semanas, se deshabilita.


## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**¿Cuánto tiempo se tarda en aprovisionar usuarios?**

El rendimiento será diferente en función de si el trabajo de aprovisionamiento realiza una sincronización inicial o una sincronización incremental.

En el caso de las sincronizaciones iniciales, el tiempo que tardan en completarse dependerá directamente de cuántos usuarios, grupos y miembros de los grupos estén presentes en el sistema de origen. Los sistemas de origen muy pequeños con cientos de objetos pueden completar las sincronizaciones iniciales en cuestión de minutos. Sin embargo, los sistemas de origen con cientos de miles o millones de objetos combinados pueden tardar mucho tiempo.

En las sincronizaciones incrementales, el tiempo que tardan depende del número de cambios detectados en ese ciclo de sincronización. Si se detectan menos de 5000 cambios de usuarios o de pertenencia a grupos, con frecuencia se pueden sincronizar en un ciclo de 40 minutos. 

Tenga en cuenta que el rendimiento general depende de los sistemas de origen y destino. Algunos sistemas de destino implementan límites de tasa de solicitudes que pueden afectar al rendimiento durante operaciones de sincronización de gran tamaño, y los conectores de aprovisionamiento de Azure AD predefinidos para esos sistemas tienen esto en cuenta.

El rendimiento es también menor si hay muchos errores (registrados en los [registros de auditoría](active-directory-saas-provisioning-reporting.md)) y el servicio de aprovisionamiento ha quedado en un estado de "cuarentena".

**¿Cómo se puede mejorar el rendimiento de la sincronización?**

La mayoría de los problemas de rendimiento se producen durante las sincronizaciones iniciales de sistemas que tienen un gran número de grupos y miembros de grupo.

Si no se requiere la sincronización de grupos o pertenencias a grupos, el rendimiento de la sincronización se puede mejorar enormemente mediante estos pasos:

1. Establezca el menú **Aprovisionamiento > Configuración > Ámbito** en **Sincronizar todo**, en lugar de sincronizar los usuarios y grupos asignados.
2. Use [filtros de ámbito](active-directory-saas-scoping-filters.md) en lugar de asignaciones para filtrar la lista de usuarios aprovisionados.

> [!NOTE]
> En aplicaciones que admiten el aprovisionamiento de nombres de grupos y propiedades de grupos (por ejemplo, ServiceNow y Google Apps), al deshabilitar esta característica también se reduce el tiempo que tarda en completarse una sincronización inicial. Si no desea aprovisionar los nombres de grupos y la pertenencia a grupos para una aplicación, puede deshabilitar esta opción en las [asignaciones de atributo](active-directory-saas-customizing-attribute-mappings.md) de la configuración del aprovisionamiento.

**¿Cómo se puede seguir el progreso del trabajo de aprovisionamiento actual?**

Consulte la [guía de informes de aprovisionamiento](active-directory-saas-provisioning-reporting.md).

**¿Cómo puedo saber si se produce algún error en el aprovisionamiento de un usuario?**

Todos los errores se copian en los registros de auditoría de Azure AD. Para obtener más información, consulte la [guía de informes de aprovisionamiento](active-directory-saas-provisioning-reporting.md).

**¿Cómo se compila una aplicación que funciona con el servicio de aprovisionamiento?**

Consulte [Uso de System for Cross-Domain Identity Management para aprovisionar automáticamente a los usuarios y grupos de Azure Active Directory para aplicaciones](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning).

**¿Cómo puedo enviar comentarios al equipo de ingeniería?**

Póngase en contacto con nosotros a través del [foro de comentarios de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).


## <a name="related-articles"></a>Artículos relacionados
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS](active-directory-saas-tutorial-list.md)
* [Personalización de asignaciones de atributos para el aprovisionamiento de usuarios](active-directory-saas-customizing-attribute-mappings.md)
* [Escritura de expresiones para asignaciones de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de ámbito para el aprovisionamiento de usuario](active-directory-saas-scoping-filters.md)
* [Uso de SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory a aplicaciones](active-directory-scim-provisioning.md)
* [Introducción a la API de sincronización de Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

