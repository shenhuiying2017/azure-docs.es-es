---
title: Conector de Lotus Domino | Microsoft Docs
description: "En este artículo, se describe cómo configurar el conector de Lotus Domino para Microsoft."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: e07fd469-d862-470f-a3c6-3ed2a8d745bf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/119/2017
ms.author: barclayn
ms.openlocfilehash: 6c412be1c54e0378166791c61469c951bca3a583
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="lotus-domino-connector-technical-reference"></a>Referencia técnica del conector de Lotus Domino
En este artículo, se describe el conector de Lotus Domino. El artículo se aplica a los siguientes productos:

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * Debe usar la revisión 4.1.3671.0 o posterior ( [KB3092178](https://support.microsoft.com/kb/3092178)).

Para MIM2016 y FIM2010R2, el conector está disponible como descarga desde el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-lotus-domino-connector"></a>Información general sobre el conector de Lotus Domino
El conector de Lotus Domino le permite integrar el servicio de sincronización con el servidor de Lotus Domino de IBM.

Desde una perspectiva de alto nivel, las siguientes características son compatibles con la versión actual del conector:

| Característica | Soporte técnico |
| --- | --- |
| Origen de datos conectado |Servidor:  <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9.x</li>Cliente:<li>Lotus Domino 8.5.x</li><li>Lotus Notes 9.x</li> |
| Escenarios |<li>Administración del ciclo de vida de objetos</li><li>Administración de grupos</li><li>Administración de contraseñas</li> |
| Operaciones |<li>Importación completa y diferencial</li><li>Exportación</li><li>Establecer y cambiar la contraseña en Contraseña de HTTP</li> |
| Esquema |<li>Persona (usuario móvil, contacto (personas sin certificado))</li><li>Grupo</li><li>Recurso (recurso, sala, reunión en línea)</li><li>Base de datos de correo de entrada</li><li>Detección dinámica de atributos para los objetos admitidos</li><li>Admite hasta 250 certificadores personalizados con una organización y con unidades organizativas (OU)</li> |

El conector de Lotus Domino usa el cliente de Lotus Notes para comunicarse con el servidor de Lotus Domino. Como consecuencia de esta dependencia, debe instalarse un cliente de Lotus Notes compatible en el servidor de sincronización. La comunicación entre el cliente y el servidor se implementa a través de la interfaz de interoperabilidad .NET de Lotus Notes (Interop.domino.dll). Esta interfaz facilita la comunicación entre el cliente de Lotus Notes y la plataforma de Microsoft .NET, y admite el acceso a documentos y vistas de Lotus Domino. Para la importación diferencial, es también posible que se use la interfaz nativa de C++ (según el método de importación diferencial seleccionado).

### <a name="prerequisites"></a>requisitos previos
Antes de usar el conector, asegúrese de que cumple los siguientes requisitos previos en el servidor de sincronización:

* Microsoft .NET 4.5.2 Framework o posterior
* El cliente de Lotus Notes debe estar instalado en el servidor de sincronización.
* El conector de Lotus Domino requiere que la base de datos predeterminada del esquema LDAP de Lotus Domino (schema.nsf) esté presente en el servidor del Directorio de Domino. Si no lo está, para instalarla, ejecute o reinicie el servicio LDAP en el servidor de Domino.

### <a name="connected-data-source-permissions"></a>Permisos del origen de datos conectado
Para realizar cualquiera de las tareas admitidas en el conector de Lotus Domino, debe ser miembro de los grupos siguientes:

* Administradores de acceso total
* Administradores
* Administradores de bases de datos

En la tabla siguiente, se muestran los permisos necesarios para cada operación:

| Operación | Derechos de acceso |
| --- | --- |
| Importar |<li>Leer documentos públicos</li><li> Administrador de acceso total (cuando se es miembro del grupo Administradores de acceso total, se dispone automáticamente de acceso efectivo a la ACL).</li> |
| Exportación y establecimiento de contraseña |Acceso efectivo:  <li>Creación de documentos</li><li>Eliminar documentos</li><li>Leer documentos públicos</li><li>Escribir documentos públicos</li><li>Replicar o copiar documentos</li>Para las operaciones de exportación, también necesitará los roles siguientes: <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li> |

### <a name="direct-operations-and-adminp"></a>Operaciones directas y AdminP
Las operaciones van directamente al directorio de Domino o a través del proceso AdminP. En las siguientes tablas, se incluyen todos los objetos admitidos, las operaciones y, si procede, el método de implementación relacionado:

**Libreta de direcciones principal**

| Objeto | Crear | Actualizar | Eliminar |
| --- | --- | --- | --- |
| Persona |AdminP |Directo |AdminP |
| Grupo |AdminP |Directo |AdminP |
| MailInDB |Directo |Directo |Directo |
| Recurso |AdminP |Directo |AdminP |

**Libreta de direcciones secundaria**

| Objeto | Crear | Actualizar | Eliminar |
| --- | --- | --- | --- |
| Persona |N/D |Directo |Directo |
| Grupo |Directo |Directo |Directo |
| MailInDB |Directo |Directo |Directo |
| Recurso |N/D |N/D |N/D |

Cuando se cree un recurso, se crea un documento de Notes. De forma similar, cuando se elimine un recurso, se elimina el documento de Notes.

### <a name="ports-and-protocols"></a>Puertos y protocolos
El cliente de IBM Lotus Notes y los servidores de Domino se comunican mediante una llamada a procedimiento remoto de Notes (NRPC) en la que NRPC debe usar TCP/IP. El número de puerto predeterminado es 1352, pero el administrador de Domino puede cambiarlo.

### <a name="not-supported"></a>No compatible
La versión actual del conector de Lotus Domino no admite las siguientes operaciones:

* Mover un buzón entre servidores.

## <a name="create-a-new-connector"></a>Creación de un nuevo conector
### <a name="client-software-installation-and-configuration"></a>Instalación y configuración del software de cliente
Se debe instalar Lotus Notes en el servidor **antes de** instalar el conector.

Durante la instalación, asegúrese de realizar una **instalación para un solo usuario (Single User Install)**. El valor predeterminado **Multi-User Install** (Instalación para varios usuarios) no funciona.  
![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

En la página de características, instale solo las características necesarias de Lotus Notes y **Client Single Logon**(Inicio de sesión único para el cliente). Esta última característica se necesita para que el conector pueda iniciar sesión en el servidor de Domino.  
![Notes2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**Nota:** Inicie Lotus Notes una vez con un usuario que se encuentre en el mismo servidor que la cuenta que usa como cuenta de servicio del conector. Además, asegúrese de cerrar el cliente Lotus Notes en el servidor. No se puede ejecutar al mismo tiempo que el conector intenta conectarse al servidor Domino.

### <a name="create-connector"></a>Creación del conector
Para crear un conector de Lotus Domino, en **Servicio de sincronización**, seleccione **Agente de administración** y **Crear**. Seleccione el conector **Lotus Domino (Microsoft)** .  
![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

Si la versión del servicio de sincronización ofrece la capacidad de establecer un valor para **Arquitectura**, asegúrese de que el conector esté establecido en su valor predeterminado para ejecutarse en **Proceso**.

### <a name="connectivity"></a>Conectividad
En la página Conectividad, debe especificar el nombre del servidor de Lotus Domino y escribir las credenciales de inicio de sesión.  
![Conectividad](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

La propiedad Servidor de Domino admite dos formatos para el nombre del servidor:

* nombreDeServidor
* nombreDeServidor/nombreDeDirectorio

El formato **nombreDeServidor/nombreDeDirectorio** es el preferido para este atributo, porque proporciona una respuesta más rápida cuando el conector se pone en contacto con el servidor de Domino.

El archivo UserID proporcionado se almacena en la base de datos de configuración del servicio de sincronización.

Para **Importación diferencial** , tiene estas opciones:

* **No**. El conector no realiza ninguna importación diferencial.
* **Adición y actualización**. El conector realiza una importación diferencial de las operaciones de adición y actualización. Para las de eliminación, se necesita una operación **Importación completa** . Esta operación emplea la interoperabilidad .NET.
* **Adición, actualización y eliminación**. El conector realiza una importación diferencial de las operaciones de adición, actualización y eliminación. Esta operación usa las interfaces de C++ nativo.

En **Opción de esquema** , dispone de las siguientes opciones:

* **Esquema predeterminado**. El conector detecta el esquema en el servidor de Domino. Esta selección es la opción predeterminada.
* **Esquema DSML**. Solo se usa si el servidor de Domino no expone el esquema. Después, puede crear un archivo DSML con el esquema e importarlo en su lugar. Para más información sobre DSML, consulte [OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml).

Cuando hace clic en Siguiente, se comprueban los parámetros de configuración de UserID y contraseña.

### <a name="global-parameters"></a>Parámetros globales
En la página Parámetros globales, configure la zona horaria y la opción para la operación de importación y exportación.  
![Parámetros globales](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

El parámetro **Zona horaria del servidor de Domino** define la ubicación de su servidor de Domino.

Esta opción de configuración es necesaria para admitir las operaciones de **importación diferencial** porque permite que el servicio de sincronización determine los cambios entre las dos últimas importaciones.

>[!Note]
A partir de la actualización de marzo de 2017, la pantalla de parámetros globales incluye una opción para eliminar la base de datos de correo del usuario durante la eliminación del usuario.

![Eliminación del buzón del usuario](./media/active-directory-aadconnectsync-connector-domino/AdminP.png)

#### <a name="import-settings-method"></a>Configuración de importación: método
En **Realizar importación completa mediante** , aparecen estas opciones:

* Search
* Vista (recomendada)

**Búsqueda** usa la indexación en Domino, pero es habitual que los índices no se actualicen en tiempo real y los datos devueltos desde el servidor no son siempre correctos. Para un sistema con muchos cambios, esta opción normalmente no funciona bien y proporciona eliminaciones falsas en algunas situaciones. Sin embargo, la **búsqueda** es más rápida que la **vista**.

**Vista** es la opción recomendada, ya que proporciona el estado correcto de los datos. Es ligeramente más lenta que la búsqueda.

#### <a name="creation-of-virtual-contact-objects"></a>Creación de objetos _Contact virtuales
En el cuadro desplegable **Habilitar la creación del objeto \_Contact**, se ofrecen estas opciones:

* None
* Valores sin referencia
* Valores de referencia y sin referencia

En Domino, los atributos de referencia pueden contener muchos formatos diferentes para hacer referencia a otros objetos. Para poder representar distintas variaciones, el conector implementa objetos \_Contact, también conocido como **contactos virtuales** (VC). Estos objetos se crean para que puedan unirse a los objetos existentes de MV o proyectarse como nuevos objetos. De esta forma, se conservan las referencias de atributo.

Si habilita esta configuración y si el contenido de un atributo de referencia no es un formato DN, se crea un objeto \_Contact. Por ejemplo, un atributo member de un grupo puede contener direcciones SMTP. También es posible que shortName y otros atributos estén presentes en los atributos de referencia. En este escenario, seleccione **Valores sin referencia**. Esta configuración es la más común para las implementaciones de Domino.

Cuando se configura Lotus Domino de forma que tenga libretas de direcciones independientes con diferentes nombres completos que representan al mismo objeto, es posible crear también objetos \_Contact para todos los valores de referencia que se encuentran en una libreta de direcciones. En este escenario, seleccione la opción **Valores de referencia y sin referencia** .

Si tiene varios valores en el atributo **FullName** en Domino, también conviene que habilite la creación de contactos virtuales para que se puedan resolver las referencias. Por ejemplo, este atributo puede poseer varios valores después de un cambio de nombre. Active la casilla **Habilitar la creación del objeto _Contact cuando "FullName" tenga varios valores para una persona** para este escenario.

Mediante la combinación por los atributos correctos, los objetos \_Contact se combinarían con el objeto MV.

Para estos objetos, se agrega VC=\_Contact a su DN.

#### <a name="import-settings-conflict-object"></a>Configuración de importación: objeto de conflicto
**Excluir objeto de conflicto**

En una implementación de Domino de gran envergadura, es posible que haya varios objetos con el mismo DN debido a problemas de replicación. En estos casos, el conector vería dos objetos con atributos UniversalID diferentes pero el mismo DN. Este conflicto hace que se cree un objeto temporal en el espacio del conector. El conector puede pasar por alto los objetos que se han seleccionado en Domino como elementos afectados de replicación. La recomendación es mantener activada esta casilla.

#### <a name="export-settings"></a>Exportar configuración
Si la opción **Usar AdminP para actualizar referencias** no está seleccionada, la exportación de atributos de referencia, como member, es una llamada directa y no se usa el proceso AdminP. Solo use esta opción si no se ha configurado AdminP para mantener la integridad referencial.

#### <a name="routing-information"></a>Información de enrutamiento
En Domino, es posible que un atributo de referencia tenga información de enrutamiento insertada como sufijo del DN. Por ejemplo, el atributo member de un grupo podría contener **CN=example/organization@ABC**. El sufijo @ABC es la información de enrutamiento. Domino usa esta información de enrutamiento para enviar correos electrónicos al sistema de Domino correcto, que puede estar en una organización diferente. En el campo Información de enrutamiento, puede especificar los sufijos de enrutamiento usados dentro de la organización en el ámbito del conector. Si uno de estos valores se encuentra como sufijo en un atributo de referencia, la información de enrutamiento se quita de la referencia. Si el sufijo de enrutamiento de un valor de referencia no coincide con uno de los valores especificados, se crea un objeto \_Contact. Estos objetos \_Contact se crean con **RO=@<RoutingSuffix>** insertado en el DN. Para estos objetos \_Contact, también se agregan los atributos siguientes para permitir combinarlos con un objeto real, si es necesario: \_routingName, \_contactName, \_displayName y UniversalID.

#### <a name="additional-address-books"></a>Libretas de direcciones adicionales
Si no tiene instalada la característica **Directory Assistance** , que proporciona el nombre de las libretas de direcciones secundarias, puede escribirlos manualmente.

#### <a name="multivalued-transformation"></a>Transformación multivalor
Muchos de los atributos de Lotus Domino son multivalor. Los atributos de metaverso correspondientes son normalmente de un solo valor. Al configurar la opción para la operación de importación y exportación, permite que el conector ayude con la traducción necesaria de los atributos afectados.

**Exportarar**  
La opción para la operación de exportación admite dos modos:

* Anexar elemento
* Reemplazar elemento

**Reemplazar elemento** : cuando se selecciona esta opción, el conector siempre quita los valores actuales del atributo en Domino y los reemplazará por los valores proporcionados. El valor proporcionado puede ser un solo valor o multivalor.

Por ejemplo, el atributo Assistant (Asistente) de un objeto de persona tiene los siguientes valores:

* CN=Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN=John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Si se asigna un nuevo asistente llamado **David Alexander** a este objeto de persona, el resultado es:

* CN=David Alexander/OU=Contoso/O=Americas,NAB=names.nsf

**Anexar elemento** : cuando se selecciona esta opción, el conector conserva los valores existentes en el atributo de Domino e insertará otros nuevos al principio de la lista de datos.

Por ejemplo, el atributo Assistant (Asistente) de un objeto de persona tiene los siguientes valores:

* CN=Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN=John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Si se asigna un nuevo asistente llamado **David Alexander** a este objeto de persona, el resultado es:

* CN=David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
* CN=Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN=John Smith/OU=Contoso/O=Americas,NAB=names.nsf

**Importaciónación**  
La opción para la operación de importación admite dos modos:

* Valor predeterminado
* Multivalor en un solo valor

**Predeterminada** : cuando se selecciona la opción Predeterminada, se importan todos los valores de todos los atributos.

**Multivalor en un solo valor** : cuando se selecciona esta opción, un atributo multivalor se convierte en uno de un solo valor. Si existe más de un valor, se usa el del principio (este valor suele ser también el más reciente).

Por ejemplo, el atributo Assistant (Asistente) de un objeto de persona tiene los siguientes valores:

* CN=David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
* CN=Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN=John Smith/OU=Contoso/O=Americas,NAB=names.nsf

La actualización más reciente de este atributo es **David Alexander**. Como la opción para la operación de importación está establecida en Multivalor en un solo valor, el conector solamente importa **David Alexander** al espacio del conector.

La lógica para convertir los atributos multivalor en otros de un solo valor no se aplica al atributo member de un grupo ni al atributo fullname de persona.

También es posible configurar las reglas de transformación para la importación y la exportación para los atributos multivalor en función del atributo, como una excepción a la regla global. Para configurar esta opción, escriba [tipoDeObjeto].[nombreDeAtributo] en los cuadros de texto **Lista de atributos de exclusión de importación** y **Lista de atributos de exclusión de exportación**. Por ejemplo, si escribe Person.Assistant y se establece la marca global para importar todos los valores, solo se importa el primer valor para el asistente.

#### <a name="certifiers"></a>Certificadores
Se muestran todas las organizaciones y las unidades organizativas por el conector. Para poder exportar objetos de persona a la libreta de direcciones principal, es necesario un certificador con su contraseña.

Si todos los certificadores comparten la misma contraseña, se puede usar el campo **Password for all Certifers** (Contraseña para todos los certificadores). Entonces, puede escribir la contraseña aquí y solo especificar el archivo de certificador.

Si solo va a importar, no es necesario especificar ningún certificador.

### <a name="configure-provisioning-hierarchy"></a>Configurar la jerarquía de aprovisionamiento
Al configurar el conector de Lotus Domino, omita esta página del cuadro de diálogo. El conector de Lotus Domino no admite el aprovisionamiento de jerarquía.  
![Jerarquía de aprovisionamiento](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurar particiones y jerarquías
Cuando configure particiones y jerarquías, debe seleccionar la libreta de direcciones principal denominada NAB=names.nsf. Además de la libreta de direcciones principal, puede seleccionar otras secundarias, si existen.  
![Particiones](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### <a name="select-attributes"></a>Seleccionar atributos
Cuando configure los atributos, debe seleccionar todos aquellos con el prefijo **\_MMS\_**. Estos atributos son obligatorios cuando se aprovisionan nuevos objetos en Lotus Domino.

![Atributos](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## <a name="object-lifecycle-management"></a>Administración del ciclo de vida de objetos
En esta sección, se proporciona información general sobre los diferentes objetos en Domino.

### <a name="person-objects"></a>Objetos de persona
El objeto de persona representa a los usuarios de la organización y las unidades organizativas. Además de los atributos predeterminados, el administrador de Domino puede agregar atributos personalizados a un objeto de persona. Como mínimo, un objeto de persona debe incluir todos los atributos obligatorios. Para obtener una lista completa de los atributos obligatorios, consulte [Propiedades de Lotus Notes](#lotus-notes-properties). Para registrar un objeto de persona, deben cumplirse los siguientes requisitos previos:

* Se debe haber definido la libreta de direcciones (names.nsf), que debe ser la libreta de direcciones principal.
* Debería tener la identificación y la contraseña del certificador de organización o unidad organizativa (O/OU) para registrar un usuario determinado en la organización o unidad organizativa.
* Debe establecer un conjunto específico de propiedades de Lotus Notes para un objeto de persona. Estas propiedades se usan para aprovisionar el objeto de persona. Para obtener más detalles, consulte la sección llamada [Propiedades de Lotus Notes](#lotus-notes-properties) más adelante en este documento.
* La contraseña HTTP inicial de una persona es un atributo y se establece durante el aprovisionamiento.
* El objeto de persona debe ser de uno de los tres tipos admitidos siguientes:
  1. Usuario normal que tiene un archivo de correo y un archivo de identificación de usuario
  2. Usuario móvil (un usuario normal que incluye todos los archivos de base de datos móvil)
  3. Contactos (usuarios sin archivo de identificación)

Las personas (excepto los contactos) se pueden agrupar además en usuarios de EE. UU. y usuarios de fuera de EE. UU. de acuerdo con el valor de la propiedad \_MMS\_IDRegType. Estas personas usan al cliente de Notes para acceder a los servidores de Lotus Domino, que tienen una identificación de Notes y un documento de persona. Si usan el correo de Notes, también tienen un archivo de correo. El usuario debe estar registrado para activarse. Para obtener más información, consulte 

* [Setting up Notes users](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
* [User Registration](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
* [Administración de usuarios](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
* [Renaming users](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

Todas estas operaciones se realizan en Lotus Domino y después se importan al servicio de sincronización.

### <a name="resources-and-rooms"></a>Recursos y salas
Un recurso es otro tipo de base de datos de Lotus Domino. Los recursos pueden ser las salas de conferencia con varios tipos de equipos como proyectores. Existen subtipos de recursos admitidos por el conector de Lotus Domino que se definen por el atributo de tipo de recurso:

| Tipo de recurso | Atributo de tipo de recurso |
| --- | --- |
| Sala |1 |
| Recurso (otros) |2 |
| Reunión en línea |3 |

Para que el tipo de objeto de recurso funcione, es necesario lo siguiente:

* La base de datos de reserva de recursos ya debería existir en el servidor de Domino conectado.
* El sitio ya está definido para el recurso.

La base de datos de reserva de recursos contiene tres tipos de documentos:

* Perfil de sitio
* Recurso
* Reserva

Para obtener más detalles sobre la configuración de la base de datos de reserva de recursos, consulte [Setting up the Resource Reservations database](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html) (Configuración de la base de datos de reserva de recursos).

**Crear, actualizar y eliminar recursos**  
Las operaciones de creación, actualización y eliminación se realizan mediante el conector de Lotus Domino en la base de datos de reserva de recursos. Los recursos se crean como documentos en Names.nsf (es decir, la libreta de direcciones principal). Para obtener más información sobre cómo modificar y eliminar recursos, consulte [Editing and deleting Resource documents](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html) (Edición y eliminación de documentos de recursos).

**Operación de importación y exportación para recursos**  
Los recursos se pueden importar al servicio de sincronización y exportarlos desde él igual que cualquier otro tipo de objeto. Seleccione el tipo de objeto como recurso durante la configuración. Para que la operación de exportación se realice correctamente, debería tener información sobre el tipo de recurso, la base de datos de conferencias y el nombre del sitio.

### <a name="mail-in-databases"></a>Bases de datos de correo de entrada
Una base de datos de correo de entrada está diseñada para recibir mensajes de correo. Es un buzón de Lotus Domino que no está asociado a ninguna cuenta de usuario de Lotus Domino específica (es decir, no tiene archivo de identificación ni contraseña propios). Una base de datos de correo de entrada tiene un valor UserId único ("nombre corto") asociado y su propia dirección de correo electrónico.

Si se necesita un buzón independiente con su propia dirección de correo electrónico que se pueda compartir entre distintos usuarios (por ejemplo: group@contoso.com), se crea una base de datos de correo de entrada. El acceso a este buzón se controla a través de su lista de control de acceso (ACL), que contiene los nombres de los usuarios de Notes que tienen permiso para abrir el buzón.

Para ver una lista de los atributos necesarios, consulte la sección llamada [Atributos obligatorios](#mandatory-attributes) más adelante en este artículo.

Cuando se ha diseñado una base de datos para recibir un mensaje de correo, se crea un documento de base de datos de correo de entrada en Lotus Domino. Este documento debe existir en el Directorio de Domino de cada servidor que almacene una copia de la base de datos. Para ver una descripción detallada de cómo crear un documento de base de datos de correo de entrada, consulte [Creating a Mail-In Database document](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html)(Creación de un documento de base de datos de correo de entrada).

Antes de crear una base de datos de correo de entrada, ya debería existir la base de datos (debería haberla creado el administrador de Lotus) en el servidor de Domino.

### <a name="group-management"></a>Administración de grupos
Puede obtener una descripción detallada de la administración de grupos en Lotus Domino de los siguientes recursos:

* [Using groups](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
* [Creating a group](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
* [Creating and modifying groups](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
* [Managing groups](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
* [Renaming a group](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### <a name="password-management"></a>Administración de contraseñas
Para un usuario registrado de Lotus Domino, existen dos tipos de contraseñas:

1. Contraseña de usuario (se almacena en el archivo User.id)
2. Contraseña HTTP/Internet

El conector de Lotus Domino solo admite operaciones con la contraseña HTTP.

Para realizar la administración de contraseñas, debe habilitarla para el conector en el Diseñador de agente de administración. Para habilitar la administración de contraseñas, active **Habilitar la administración de contraseñas** en la página **Configure Extensions** (Configurar extensiones) del cuadro de diálogo.  
![Configurar extensiones](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

El conector de Lotus Domino admite las siguientes operaciones para la contraseña de Internet:

* Establecer contraseña: esta opción establece una nueva contraseña HTTP/Internet en el usuario de Domino. Además, de forma predeterminada, la cuenta también se desbloquea. La marca de desbloqueo se expone en la interfaz de WMI del motor de sincronización.
* Cambiar contraseña: en este escenario, con la opción Solo cambiar, un usuario puede cambiar la contraseña o se le pide que la cambie tras un tiempo especificado. Para que esta operación se lleve a cabo, son obligatorias ambas contraseñas (la antigua y la nueva). Una vez cambiada, la nueva contraseña se actualiza en Lotus Domino.

Para obtener más información, consulte 

* [Using the Internet lockout feature](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
* [Managing Internet passwords](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## <a name="reference-information"></a>Información de referencia
En esta sección, se muestran las descripciones de atributos y los requisitos de los atributos para el conector de Lotus Domino.

### <a name="lotus-notes-properties"></a>Propiedades de Lotus Notes
Cuando se aprovisionan objetos de persona en el directorio de Lotus Domino, los objetos deben poseer un conjunto específico de propiedades con valores específicos rellenados. Estos valores solo son necesarios para las operaciones de creación.

En la tabla siguiente, se incluyen estas propiedades y se proporciona su descripción.

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| \_MMS_AltFullName |El nombre completo alternativo del usuario. |
| \_MMS_AltFullNameLanguage |El idioma que se usa para especificar el nombre completo alternativo del usuario. |
| \_MMS_CertDaysToExpire |El número de días en que expira el certificado a partir de la fecha actual. Si no se especifica, la fecha predeterminada es dos años desde la fecha actual. |
| \_MMS_Certifier |Propiedad que contiene el nombre de la jerarquía organizativa del certificador. Por ejemplo: OU=OrganizationUnit,O=Org,C=Country. |
| \_MMS_IDPath |Si la propiedad está vacía, no se crea ningún archivo de identificación de usuario localmente en el servidor de sincronización. Si la propiedad contiene un nombre de archivo, se crea un archivo de identificación de usuario en la carpeta madata. La propiedad también puede contener una ruta de acceso completa. |
| \_MMS_IDRegType |Las personas se pueden clasificar como contactos, usuarios de Estados Unidos y usuarios de fuera de Estados Unidos. En la tabla siguiente, se ofrecen los valores posibles: <li>0 - Contacto</li><li>1 - Usuario Estados Unidos</li><li>2 - Usuario internacional</li> |
| \_MMS_IDStoreType |Propiedad obligatoria para los usuarios de Estados Unidos y de fuera de Estados Unidos. La propiedad contiene un valor entero que especifica si la identificación de usuario se almacena como datos adjuntos en la libreta de direcciones de Notes o en el archivo de correo de la persona. Si el archivo de identificación de usuario está adjunto a la libreta de direcciones, existe la opción de crearlo como archivo con \_MMS_IDPath. <li>Almacenar el archivo de identificación en el almacén de identificaciones, sin archivo de identificación (se usa para contactos).</li><li> 1: adjunto a la libreta de direcciones de Notes. La propiedad \_MMS_Password debe establecerse para los archivos de identificación de usuario que son datos adjuntos.</li><li>2: almacenar la identificación en el archivo de correo de la persona. \_MMS_UseAdminP debe establecerse en false para permitir que el archivo de correo se cree durante el registro de la persona. La propiedad \_MMS_Password debe establecerse para los archivos de identificación de usuario.</li> |
| \_MMS_MailQuotaSizeLimit |El número de megabytes que se permiten para la base de datos de archivos de correo electrónico. |
| \_MMS_MailQuotaWarningThreshold |El número de megabytes que se permiten para la base de datos de archivos de correo electrónico antes de que se emita una advertencia. |
| \_MMS_MailTemplateName |El archivo de plantilla de correo electrónico que se usa para crear el archivo de correo electrónico del usuario. Si se especifica una plantilla, se crea el archivo de correo con ella. Si no se especifica ninguna, se emplea el archivo de plantilla predeterminado para crear el archivo. |
| \_MMS_OU |Propiedad opcional que es el nombre de la unidad organizativa en el certificador. Esta propiedad debe estar vacía para los contactos. |
| \_MMS_Password |Propiedad obligatoria para los usuarios. La propiedad contiene la contraseña para el archivo de identificación del objeto. |
| \_MMS_UseAdminP |La propiedad debe establecerse en true si el proceso AdminP debe crear el archivo de correo en el servidor de Domino (asincrónico respecto al proceso de exportación). Si la propiedad se establece en false, se crea el archivo de correo con el usuario de Domino (sincrónico en el proceso de exportación). |

Para un usuario con un archivo de identificación asociado, la propiedad \_MMS_Password debe contener un valor. Para el acceso a correo electrónico a través del cliente de Lotus Notes, las propiedades MailServer y MailFile de un usuario deben contener un valor.

Para acceder al correo electrónico mediante un explorador web, las siguientes propiedades deben contener valores:

* MailFile: propiedad obligatoria que contiene la ruta de acceso en el servidor de Lotus Domino donde se almacena el archivo de correo.
* MailServer: propiedad obligatoria que contiene el nombre del servidor de Lotus Domino. Este valor es el nombre que se usa al crear el archivo de correo de Lotus en el servidor de Domino.
* HTTPPassword: propiedad opcional que contiene la contraseña de acceso web para el objeto.

Para acceder al servidor Domino sin la funcionalidad de correo, la propiedad HTTPPassword debe contener un valor. La propiedad MailFile y la propiedad MailServer pueden estar vacías.

Con \_MMS_IDStoreType = 2 (almacenar la identificación en el archivo de correo), se establece la propiedad MailSystem de NotesRegistrationclass en REG_MAILSYSTEM_INOTES (3).

### <a name="mandatory-attributes"></a>Atributos obligatorios
El conector de Lotus Domino admite principalmente estos tipos de objetos (tipos de documento):

* Grupo
* Base de datos de correo de entrada
* Persona
* Contacto (persona sin certificador)
* Recurso

En esta sección, se incluyen los atributos que son obligatorios para cada tipo de objeto admitido con el fin de exportar el objeto admitido a un servidor de Domino.

| Tipo de objeto | Atributos obligatorios |
| --- | --- |
| Grupo |<li>ListName</li> |
| Base de datos de correo de entrada |<li>FullName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li> |
| Persona |<li>Apellidos</li><li>MailFile</li><li>ShortName</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\_MMS_IDRegType</li><li>\_MMS_UseAdminP</li> |
| Contacto (persona sin certificador) |<li>\_MMS_IDRegType</li> |
| Recurso |<li>FullName</li><li>ResourceType</li><li>ConfDB</li><li>ResourceCapacity</li><li>Sitio</li><li>DisplayName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li> |

## <a name="common-issues-and-questions"></a>Problemas y preguntas comunes
### <a name="schema-detection-does-not-work"></a>La detección de esquema no funciona
Para poder detectar el esquema, es necesario que el archivo schema.nsf esté presente en el servidor de Domino. Este archivo solo aparece si LDAP está instalado en el servidor. Si no se detecta el esquema, compruebe lo siguiente:

* El archivo schema.nsf está presente en la carpeta raíz del servidor de Domino.
* El usuario tiene permisos para ver el archivo schema.nsf.
* Fuerce el reinicio del servidor LDAP. Abra **Lotus Domino Console** (Consola de Lotus Domino) y use el comando **Tell LDAP ReloadSchema** (Indicar a LDAP ReloadSchema) para volver a cargar el esquema.

### <a name="not-all-secondary-address-books-are-visible"></a>No todas las libretas de direcciones secundarias son visibles
El conector de Domino se basa en la característica **Directory Assistance** para encontrar las libretas de direcciones secundarias. Si faltan las libretas de direcciones secundarias, compruebe si [Directory Assistance](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) se ha habilitado y configurado en el servidor de Domino.

### <a name="custom-attributes-in-domino"></a>Atributos personalizados de Domino
En Domino, existen varias maneras de extender el esquema para que aparezca como atributo personalizado que el conector puede consumir.

**Enfoque 1: Extender el esquema de Lotus Domino**

1. Cree una copia de la plantilla del directorio de Domino {PUBNAMES.NTF} siguiendo [estos pasos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (no debe personalizar la plantilla del Directorio de IBM Lotus Domino predeterminada):
2. Abra la copia de la plantilla del directorio de Domino {CONTOSO.NTF} que se ha creado en Domino Designer y siga estos pasos:
   * Haga clic en Shared Elements (Elementos compartidos) y expanda Subforms (Subformularios).
   * Haga doble clic en el subformulario ${nombreDeObjeto}InheritableSchema (donde {nombreDeObjeto} es el nombre de la clase de objeto estructural predeterminada, como, por ejemplo, Person).
   * Nombre del atributo que desea agregar en el esquema {MyPersonAtrribute} y el correspondiente a ese atributo. Cree un campo, seleccione el menú **Crear** y, después seleccione un **campo** en el menú.
   * En el campo agregado, establezca sus propiedades seleccionando el tipo, el estilo, el tamaño, la fuente y otros parámetros relacionados en la ventana de propiedades del campo.
   * Mantenga el mismo valor predeterminado de atributo que el nombre asignado a ese atributo (por ejemplo, si el nombre del atributo es miAtributoDePersona, mantenga el valor predeterminado con el mismo nombre).
   * Guarde el subformulario ${nombreDeObjeto}InheritableSchema con valores actualizados.
3. Reemplace la plantilla del Directorio de Domino {PUBNAMES.NTF} por la nueva plantilla personalizada {CONTOSO.NTF} siguiendo [estos pasos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
4. Cierre el administrador de Domino y abra la consola de Domino para reiniciar el servicio LDAP y volver a cargar el esquema LDAP:
   * En la consola de Domino, inserte el comando en el campo de texto **Domino Command** (Comando de Domino) para reiniciar el servicio LDAP: [Restart Task LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html) (Reiniciar tarea LDAP).
   * Para volver a cargar el esquema LDAP, use el comando Tell LDAP: Tell LDAP ReloadSchema (Indicar a LDAP ReloadSchema).
5. Abra el administrador de Domino y seleccione la pestaña People & Groups (Personas y grupos) para ver que el atributo agregado se refleja en Add Person (Agregar persona) en Domino.
6. Abra Schema.nsf en la pestaña **Files** (Archivos) y compruebe que el atributo agregado se refleja en la clase de objeto de LDAP dominoPerson.

**Enfoque 2: Crear una clase auxiliar con un atributo personalizado y asociarla con la clase de objeto**

1. Cree una copia de la plantilla del Directorio de Domino {PUBNAMES.NTF} siguiendo [estos pasos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (no personalice nunca la plantilla del Directorio de IBM Lotus Domino predeterminada):
2. Abra en Domino Designer la copia de la plantilla del Directorio de Domino {CONTOSO.NTF} que se ha creado.
3. En el panel izquierdo, seleccione Shared Code (Código compartido) y después Subforms (Subformularios).
4. Haga clic en New Subform (Nuevo subformulario).
5. Siga este procedimiento para especificar las propiedades del nuevo subformulario:
   * Con el nuevo subformulario abierto, seleccione Design - Subform Properties (Diseñar - Propiedades del subformulario).
   * Junto a la propiedad Name (Nombre), escriba un nombre para la clase auxiliar de objeto, por ejemplo, subformularioPrueba.
   * Mantenga la propiedad "Include in Insert Subform... dialog" (Incluir en el cuadro de diálogo Insertar subformulario) de Options (Opciones) seleccionada.
   * Anule la selección de la propiedad "Render pass through HTML in Notes" (Representar texto HTML en Notes) de Options (Opciones).
   * Deje las demás propiedades como están y cierre el cuadro Subform Properties (Propiedades del subformulario).
   * Guarde y cierre el subformulario nuevo.
6. Siga este procedimiento para agregar un campo para definir la clase auxiliar de objeto:
   * Abra el subformulario que ha creado.
   * Elija Create - Field (Crear - Campo).
   * Junto a Name (Nombre) en la pestaña Basics (Básico) del cuadro de diálogo Field (Campo), especifique un nombre, como {miAtributoDePruebaDePersona}.
   * En el campo agregado, establezca sus propiedades seleccionando el tipo, el estilo, el tamaño, la fuente y otras propiedades relacionadas.
   * Mantenga el mismo valor predeterminado de atributo que el nombre asignado a ese atributo (por ejemplo, si el nombre del atributo es miAtributoDePruebaDePersona, mantenga el valor predeterminado con el mismo nombre).
   * Guarde el subformulario con valores actualizados y haga lo siguiente:
     * En el panel izquierdo, seleccione Shared Code (Código compartido) y después Subforms (Subformularios).
     * Seleccione el nuevo subformulario y elija Design - Design Properties (Diseñar - Propiedades del diseño).
     * Haga clic en la tercera pestaña desde la izquierda y seleccione **Propagate this prohibition of design change**(Propagar esta prohibición de cambios en el diseño).
7. Abra el subformulario ${nombreDeObjeto}ExtensibleSchema (donde {nombreDeObjeto} es el nombre de la clase de objeto estructural predeterminada, como, por ejemplo, Person).
8. Inserte el recurso y seleccione el subformulario (que ha creado, por ejemplo: subformularioDePrueba) y guarde el subformulario ${nombreDeObjeto}ExtensibleSchema.
9. Reemplace la plantilla del Directorio de Domino {PUBNAMES.NTF} por la nueva plantilla personalizada {CONTOSO.NTF} siguiendo [estos pasos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
10. Cierre el administrador de Domino y abra la consola de Domino para reiniciar el servicio LDAP y volver a cargar el esquema LDAP:
    * En la consola de Domino, inserte el comando en el campo de texto **Domino Command** (Comando de Domino) para reiniciar el servicio LDAP: [Restart Task LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html)(Reiniciar tarea LDAP).
    * Para volver a cargar el esquema LDAP, use el comando Tell LDAP **Tell LDAP ReloadSchema**(Indicar a LDAP ReloadSchema).
11. Abra el administrador de Domino y seleccione la pestaña People & Groups (Personas y grupos) para ver que el atributo agregado se refleja en Add Person (Agregar persona) en Domino (en la pestaña Others [Otros]).
12. Abra Schema.nsf en la pestaña **Files** (Archivos) y compruebe que el atributo agregado se refleja en la clase auxiliar de objeto de LDAP subformularioDePrueba.

**Enfoque 3: Agregar el atributo personalizado a la clase ExtensibleObject**

1. Abra el archivo {Schema.nsf} ubicado en el directorio raíz.
2. Seleccione LDAP Object Classes (Clases de objeto LDAP) en el menú izquierdo bajo **All Schema Documents** (Todos los documentos del esquema) y haga clic en el botón **Add Object class** (Agregar clase de objeto):
3. Proporcione el nombre LDAP con el formato {zzzExtensibleSchema} (donde zzz es el nombre de la clase de objeto estructural predeterminada, por ejemplo, Person). Por ejemplo, para extender el esquema de la clase de objeto Person, proporcione el nombre LDAP {PersonExtensibleSchema}.
4. Proporcione el nombre de la clase de objeto superior, para la que desea extender el esquema. Por ejemplo, para extender el esquema para la clase de objeto Person, proporcione el nombre de clase de objeto superior {dominoPerson}:
5. Proporcione un OID válido correspondiente a la clase de objeto.
6. Seleccione los atributos extendidos o personalizados en los campos de tipos de atributos opcionales u obligatorios según los requisitos:
7. Después de agregar los atributos requeridos para ExtensibleObjectClass, haga clic en **Save & Close** (Guardar y cerrar).
8. Se crea un elemento ExtensibleObjectClass para la clase de objeto predeterminada respectiva con atributos extendidos.

## <a name="troubleshooting"></a>solución de problemas
* Para más información acerca de cómo habilitar el registro para solucionar problemas del conector, consulte [How to Enable ETW Tracing for FIM 2010 R2 Connectors](http://go.microsoft.com/fwlink/?LinkId=335731).
