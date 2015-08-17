<properties 
	pageTitle="Personalizar: Administración de contraseñas de Azure AD | Microsoft Azure" 
	description="Cómo personalizar el aspecto, el comportamiento y las notificaciones de la Administración de contraseñas en Azure AD para satisfacer sus necesidades." 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="asteen"/>

# Personalización de la administración de contraseñas para ajustarse a las necesidades de su organización
A fin de ofrecer la mejor experiencia posible a los usuarios, se recomienda explorar y jugar con todas las opciones de configuración de administración de contraseñas que están a su disposición. De hecho, puede empezar a explorarlas de inmediato en la pestaña de configuración de la **extensión de Active Directory** en el [Portal de administración de Azure](https://manage.windowsazure.com). Este tema le guiará a través de todas las diferentes personalizaciones de la Administración de contraseñas que puede realizar como administrador desde la pestaña **Configurar** del directorio dentro del [Portal de administración de Azure](https://manage.windowsazure.com), entre las que se incluyen las siguientes:

- [**Personalización del aspecto de la Administración de contraseñas**](#password-managment-look-and-feel)
- [**Personalización del comportamiento de la Administración de contraseñas del usuario**](#password-management-behavior)
- [**Personalización de las notificaciones de Administración de contraseñas**](#password-management-notifications)

## Aspecto de la Administración de contraseñas
En la siguiente tabla se describe la forma en la que cada control afecta a la experiencia de los usuarios que se registran para el restablecimiento de contraseña y que restablecen sus contraseñas. Puede configurar estas opciones en la sección **Propiedades de directorio** de la pestaña **Configurar** del [Portal de administración de Azure](https://manage.windowsazure.com).

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Control de directivas</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Descripción</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>¿Afecta?</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Nombre de directorio</p>
              </td>
              <td>
                <p>Determina lo que ven los usuarios o administradores de la organización en las comunicaciones por correo electrónico sobre restablecimiento de contraseñas.</p>
              </td>
              <td>
                <p>
                  <strong>Mensajes de correo electrónico de tipo "Póngase en contacto con el administrador":</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina el nombre descriptivo del remitente; por ejemplo, "Microsoft en nombre de <strong>Wingtip Toys</strong>".<br><br></li>
                  <li class="unordered">
												Determina el nombre de asunto del correo electrónico; por ejemplo, "Código de comprobación de correo electrónico de la cuenta <strong>Wingtip Toys</strong>".<br><br></li>
                </ul>
                <p>
                  <strong>Mensajes de correo electrónico de restablecimiento de contraseña:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina el nombre descriptivo del remitente; por ejemplo, "Microsoft en nombre de <strong>Wingtip Toys</strong>".<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Aspecto de la página de inicio de sesión y del panel de acceso</p>
              </td>
              <td>
                <p>Determina si los usuarios que visitan la página de restablecimiento de contraseña ven el logotipo de Microsoft o su propio logotipo personalizado. Este elemento de configuración agrega también su marca al panel de acceso y a la página de inicio de sesión.</p>
                <p>
                  
                </p>
                <p>Puede obtener más información acerca de la característica de personalización y uso de marca en inquilinos en <a href="https://technet.microsoft.com/library/dn532270.aspx">Añadir marcas de empresa a sus páginas de inicio de sesión y del panel de acceso</a>.</p>
              </td>
              <td>
                <p>
                  <strong>Portal de restablecimiento de contraseña:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina si se muestra o no el logotipo en la parte superior del portal de restablecimiento de contraseña en lugar del logotipo de Microsoft predeterminado.<br><br></li>
                  <li class="unordered">
                    <strong>Nota:</strong> puede que no vea su logotipo en la primera página del portal de restablecimiento de contraseña si llega directamente a la página de restablecimiento de contraseña. Una vez que el usuario escriba su identificador de usuario y haga clic en Siguiente, aparecerá su logotipo. Puede forzar que su logotipo aparezca al cargar la página si pasa el parámetro whr a la página de restablecimiento de contraseña, como se muestra aquí: <a href="https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com">https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com</a>.<br><br></li>
                </ul>
                <p>
                  <strong>Mensajes de correo electrónico de tipo "Póngase en contacto con el administrador":</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina si el logotipo se muestra en la parte inferior de los correos electrónicos enviados a los administradores cuando los usuarios eligen ponerse en contacto con usted haciendo clic en el vínculo "Póngase en contacto con el administrador" en la interfaz de usuario de restablecimiento de contraseña.<br><br></li>
                </ul>
                <p>
                  <strong>Mensajes de correo electrónico de restablecimiento de contraseña:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina si el logotipo se muestra en la parte inferior de los correos electrónicos enviados a los usuarios cuando restablecen sus contraseñas.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## Comportamiento de la Administración de contraseñas
En la siguiente tabla se describe la forma en la que cada control afecta a la experiencia de los usuarios que se registran para el restablecimiento de contraseña y que restablecen sus contraseñas. Puede configurar estas opciones en la sección **Directiva de restablecimiento de contraseña del usuario** en la pestaña **Configurar** del [Portal de administración de Azure](https://manage.windowsazure.com).

> [AZURE.NOTE]La cuenta de administrador que utilice debe tener asignada una licencia Premium de AAD para ver estos controles de directiva.<br><br>Estos controles de directiva solo se aplican a usuarios finales que restablecen sus contraseñas, no a administradores. **Los administradores tienen una directiva predeterminada de correo electrónico alternativo o teléfono móvil que se especifica para ellos por parte de Microsoft y que no se puede cambiar.**

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Control de directivas</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Descripción</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>¿Afecta?</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Usuarios habilitados para restablecer la contraseña</p>
              </td>
              <td>
                <p>Determina si está habilitado el restablecimiento de contraseña para los usuarios en este directorio. </p>
              </td>
              <td>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si está establecido en No, ningún usuario puede registrar sus propios datos de desafíos.<br><br></li>
                  <li class="unordered">
												Si está establecido en Sí, cualquier usuario del directorio puede registrar datos de desafíos en el portal de registro en <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>.<br><br></li>
                  <li class="unordered">
                    <strong>Nota:</strong> los usuarios deben tener asignada una licencia de Azure AD de nivel Premium o Básico para registrarse para restablecer la contraseña.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de restablecimiento de contraseña:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si está establecido en No, los usuarios ven un mensaje que indica que deben ponerse en contacto con su administrador para restablecer la contraseña.<br><br></li>
                  <li class="unordered">
												Si está establecido en Sí, los usuarios pueden restablecer sus contraseñas automáticamente en <a href="http://passwordreset.microsoftonline.com">http://passwordreset.microsoftonline.com</a> o al hacer clic en el vínculo <strong>¿No puede tener acceso a su cuenta?</strong> en cualquier página de inicio de sesión de identificador organizativo.<br><br></li>
                  <li class="unordered">
                    <strong>Nota:</strong> los usuarios deben tener asignada una licencia de Azure AD de nivel Premium o Básico para poder restablecer la contraseña.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Restricción de acceso para restablecer la contraseña</p>
              </td>
              <td>
                <p>Determina si el restablecimiento de contraseña se permite solo a un determinado grupo de usuarios. (Solo visible si <strong>Usuarios habilitados para restablecer la contraseña</strong> está establecido en <strong>Sí</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si se establece en No, todos los usuarios finales de su directorio pueden registrarse para restablecer la contraseña en <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>.<br><br></li>
                  <li class="unordered">
												Si se establece en Sí, solo los usuarios finales especificados en el control del <strong>grupo que puede realizar el restablecimiento de contraseña</strong> pueden registrarse para restablecer la contraseña en <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de restablecimiento de contraseña:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si se establece en No, todos los usuarios finales del directorio pueden restablecer sus contraseñas.<br><br></li>
                  <li class="unordered">
												Si se establece en Sí, solo los usuarios finales especificados en el control del <strong>grupo que puede realizar el restablecimiento de contraseña</strong> pueden restablecer sus contraseñas.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Grupo que puede realizar el restablecimiento de contraseña</p>
              </td>
              <td>
                <p>Determina qué grupo de usuarios finales puede utilizar el restablecimiento de contraseña. </p>
                <p>
                  
                </p>
                <p>(Solo visible si <strong>Restringir acceso para restablecer la contraseña</strong> está establecido en <strong>Sí</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si no se especifica ningún grupo y hace clic en <strong>Guardar</strong>, se crea un grupo vacío denominado <strong>SSPRSecurityGroupUsers</strong>.<br><br></li>
                  <li class="unordered">
												Si desea especificar su propio grupo, puede proporcionar su propio nombre para mostrar.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si <strong>Restringir acceso para restablecer la contraseña</strong> está establecido en <strong>Sí</strong>, solo los usuarios finales de este grupo podrán registrarse para restablecer la contraseña. <br><br></li>
                </ul>
                <p>
                  <strong>Portal de restablecimiento de contraseña:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si <strong>Restringir acceso para restablecer la contraseña</strong> está establecido en <strong>Sí</strong>, solo los usuarios finales de este grupo podrán restablecer la contraseña.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Métodos de autenticación disponibles para los usuarios</p>
              </td>
              <td>
                <p>Determina qué desafíos puede utilizar un usuario para restablecer su contraseña.</p>
                <p>
                  
                </p>
                <p>(Solo visible si <strong>Usuarios habilitados para restablecer la contraseña</strong> está establecido en <strong>Sí</strong>).</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Debe seleccionarse al menos una opción.<br><br></li>
                  <li class="unordered">
												Se recomienda habilitar al menos dos opciones para ofrecer la máxima flexibilidad a los usuarios al restablecer sus contraseñas.<br><br></li>
                  <li class="unordered">
												Si está utilizando preguntas de seguridad, recomendamos encarecidamente que las use junto con otro método de autenticación, ya que las preguntas de seguridad pueden resultar menos seguras que los métodos de restablecimiento de contraseña por teléfono o correo electrónico.<br><br></li>
                </ul>
                <p>
                  <strong>¿Qué campos del directorio se usan?</strong>
                </p>
                <ul>
                  <li class="unordered">
												Teléfono de la oficina corresponde al atributo <strong>Teléfono de la oficina</strong> en un objeto de usuario en el directorio.<br><br></li>
                  <li class="unordered">
												Teléfono móvil corresponde al atributo <strong>Móvil de autenticación</strong> (que no está visible públicamente) o al atributo <strong>Teléfono móvil</strong> (que está visible públicamente) en un objeto de usuario en el directorio. El servicio comprueba primero si hay datos en <strong>Teléfono de autenticación</strong> y, si no hay ninguno, vuelve al atributo <strong>Teléfono móvil</strong>.<br><br></li>
                  <li class="unordered">
												Dirección de correo electrónico alternativa corresponde al atributo <strong>Correo electrónico de autenticación</strong> (que no está visible públicamente) o al atributo <strong>Correo electrónico alternativo</strong> de un objeto de usuario en el directorio. El servicio comprueba primero si hay datos en <strong>Correo electrónico de autenticación</strong> y, si no hay ninguno, vuelve al atributo <strong>Correo electrónico alternativo</strong>.<br><br></li>
                  <li class="unordered">
												Las preguntas de seguridad se almacenan de forma privada y segura en un objeto de usuario en el directorio y solo las pueden responder los usuarios durante el registro. Por motivos de seguridad, no hay actualmente ninguna manera de que un administrador edite o vea estas respuestas.<br><br></li>
                  <li class="unordered">
                    <strong>Nota: </strong>de forma predeterminada, solo los atributos de la nube Teléfono de la oficina y Teléfono móvil se sincronizan en su directorio en la nube desde el directorio local. Para obtener más información acerca de qué atributos locales se sincronizan en la nube, consulte <a href="https://msdn.microsoft.com/library/azure/dn764938.aspx">Atributos sincronizados con Azure AD.</a><br><br></li>
                </ul>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Afecta a los métodos de autenticación que se muestran cuando los usuarios se van a registrar. Si no habilita un método de autenticación determinado, los usuarios no pueden registrarse por sí mismos para ese método de autenticación.<br><br></li>
                  <li class="unordered">
                    <strong>Nota: </strong>actualmente, los usuarios no pueden registrar sus propios números de teléfono de la oficina; ese método de autenticación debe definirlo el administrador.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de restablecimiento de contraseña:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina qué métodos de autenticación puede utilizar un usuario como desafíos para un determinado paso de verificación. Por ejemplo, si un usuario tiene datos en los campos <strong>Teléfono de la oficina</strong> y <strong>Teléfono de autenticación</strong> de Azure Active Directory, puede usar cualquiera de estos métodos de autenticación para recuperar su contraseña.<br><br></li>
                  <li class="unordered">
                    <strong>Nota: </strong>los usuarios podrán restablecer su contraseña solo si tienen datos en los métodos de autenticación que haya habilitado como administrador.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Número de métodos de autenticación requeridos</p>
              </td>
              <td>
                <p>Determina el número mínimo de métodos de autenticación disponibles que un usuario debe superar para restablecer su contraseña.</p>
                <p>
                  
                </p>
                <p>(Solo visible si <strong>Usuarios habilitados para restablecer la contraseña</strong> está establecido en <strong>Sí</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Puede establecerse en 1 o 2 métodos de autenticación necesarios.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina el número mínimo de métodos de autenticación que un usuario debe registrar para poder finalizar la experiencia de registro.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de restablecimiento de contraseña:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Afecta a la cantidad de pasos de verificación que debe superar un usuario antes de poder restablecer una contraseña. Un paso de verificación se define como el uso por parte de un usuario de un elemento de información de autenticación (como una llamada a su teléfono de la oficina o un correo electrónico a su correo electrónico alternativo) para comprobar su cuenta.<br><br></li>
                  <li class="unordered">
                    <strong>Nota:</strong> si un usuario no tiene la cantidad necesaria de información de autenticación definida en su cuenta para poder restablecer correctamente la contraseña de acuerdo con la directiva que ha establecido, aparece una página de error que le indica al usuario que debe solicitarle a un administrador que restablezca su contraseña.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Número de preguntas necesarias para registrarse</p>
              </td>
              <td>
                <p>Determina el número mínimo de preguntas que un usuario debe responder al registrarse para la opción de preguntas de seguridad.</p>
                <p>(Solo visible si está activada la casilla <strong>Preguntas de seguridad</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Se pueden establecer de 3 a 5 preguntas necesarias para registrarse.<br><br></li>
                  <li class="unordered">
												El número de preguntas necesarias para registrarse debe ser mayor o igual que el número de preguntas necesarias para el restablecimiento.<br><br></li>
                  <li class="unordered">
												Se recomienda establecer un número de preguntas necesarias para registrarse que sea mayor que el número necesario para el restablecimiento, de modo que los usuarios tengan más flexibilidad para restablecer sus contraseñas. También es una configuración más segura porque las preguntas se seleccionan de forma aleatoria para que el usuario las responda entre todas las preguntas que registren.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina el número mínimo de preguntas que debe responder el usuario para que se le considere totalmente registrado para restablecer la contraseña.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Número de preguntas necesarias para el restablecimiento </p>
              </td>
              <td>
                <p>Determina el número mínimo de preguntas que debe responder el usuario cuando restablece una contraseña.</p>
                <p>
                  
                </p>
                <p>(Solo visible si está activada la casilla <strong>Preguntas de seguridad</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Se pueden establecer de 3 a 5 preguntas necesarias para el restablecimiento.<br><br></li>
                  <li class="unordered">
												El número de preguntas necesarias para el restablecimiento debe ser menor o igual que el número de preguntas necesarias para registrarse.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de restablecimiento de contraseña:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina el número mínimo de preguntas que debe responder el usuario para que se pueda restablecer su contraseña.<br><br></li>
                  <li class="unordered">
												En el momento de restablecer la contraseña, este número de preguntas se selecciona de forma aleatoria en la lista total de preguntas registradas del usuario. Por ejemplo, si el usuario tiene 5 preguntas registradas, se seleccionan 3 de esas 5 preguntas de forma aleatoria cuando el usuario trata de restablecer una contraseña. El usuario debe responder todas las preguntas correctamente para que se pueda restablecer la contraseña.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Pregunta de seguridad</p>
              </td>
              <td>
                <p>Define las preguntas de seguridad entre las que los usuarios pueden elegir al registrarse para el restablecimiento de contraseña y al restablecer sus contraseñas.</p>
                <p>
                  
                </p>
                <p>(Solo visible si está activada la casilla <strong>Preguntas de seguridad</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Se pueden definir hasta 20 preguntas.<br><br></li>
                  <li class="unordered">
												El límite máximo de caracteres para las preguntas es de 200 caracteres.<br><br></li>
                  <li class="unordered">
												El límite mínimo de caracteres para las respuestas es de 3 caracteres.<br><br></li>
                  <li class="unordered">
												El límite máximo de caracteres para las respuestas es de 40 caracteres.<br><br></li>
                  <li class="unordered">
												Los usuarios no pueden responder a la misma pregunta dos veces.<br><br></li>
                  <li class="unordered">
												Los usuarios no pueden proporcionar dos veces la misma respuesta a dos preguntas diferentes.<br><br></li>
                  <li class="unordered">
												Puede utilizarse cualquier conjunto de caracteres para definir las preguntas y respuestas (incluidos los caracteres Unicode).<br><br></li>
                  <li class="unordered">
												El número de preguntas definidas debe ser mayor o igual que el número de preguntas necesarias para registrarse.<br><br></li>
                  <li class="unordered">
												La definición de preguntas diferentes para distintas configuraciones regionales no se admite aún, pero se admitirá en el futuro.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina qué preguntas puede responder un usuario cuando se registra para el restablecimiento de contraseña.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de restablecimiento de contraseña:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Determina qué preguntas puede utilizar un usuario para restablecer una contraseña.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>¿Es necesario que los usuarios se registren cuando inician sesión en el panel de acceso?</p>
                <p>
                  
                </p>
              </td>
              <td>
                <p>Determina si un usuario debe registrar datos de contacto para el restablecimiento de la contraseña la próxima vez que inicie sesión en el panel de acceso.</p>
                <p>
                  
                </p>
                <p>(Solo visible si <strong>Usuarios habilitados para restablecer la contraseña</strong> está establecido en <strong>Sí</strong>).</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si deshabilita esta característica, puede enviar manualmente a los usuarios a <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> para que registren sus datos de contacto.  <br><br></li>
                  <li class="unordered">
												Los usuarios también pueden obtener acceso al portal de registro si hacen clic en el vínculo <strong>Registrarme para restablecer la contraseña</strong> debajo de la pestaña de perfil en el panel de acceso.<br><br></li>
                  <li class="unordered">
												El registro con este método se puede descartar haciendo clic en el botón Cancelar o cerrando la ventana, pero puede ser molesto para los usuarios cada vez que inicien sesión si no se registran.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Esta configuración no afecta al comportamiento del portal de registro en sí, sino que determina si se les muestra o no el portal de registro a los usuarios cuando inician sesión en el panel de acceso.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Cantidad de días que los usuarios disponen para confirmar sus datos de contacto</p>
              </td>
              <td>
                <p>Cuando está activada la opción para <strong>obligar a los usuarios a registrarse</strong>, esta configuración determina el período de tiempo que puede transcurrir hasta que un usuario deba confirmar nuevamente sus datos. </p>
                <p>
                  
                </p>
                <p>(Solo visible si la opción <strong>¿Desea obligar a los usuarios a registrarse cuando inicien sesión en el panel de acceso?</strong> está establecida en <strong>Sí</strong>).</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Nota: </strong>
                </p>
                <ul>
                  <li class="unordered">
												Se aceptan valores entre 0 y 730 días. El valor 0 días significa que nunca se les solicitará a los usuarios que vuelvan a confirmar sus datos de contacto.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Esta configuración no afecta al comportamiento del portal de registro en sí, sino que determina si se les muestra o no el portal de registro a los usuarios cuando deban confirmar nuevamente sus datos de contacto. <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>¿Desea personalizar el vínculo para ponerse en contacto con el administrador?</p>
              </td>
              <td>
                <p>Controla si el vínculo para ponerse en contacto con el administrador (ubicado a la izquierda) que aparece en el portal de restablecimiento de contraseña cuando se produce un error o el usuario tarda demasiado tiempo en una operación apunta a una dirección de correo electrónico o una dirección URL personalizada.</p>
                <p>
                  
                </p>
                <p>(Solo visible si <strong>Usuarios habilitados para restablecer la contraseña</strong> está establecido en <strong>Sí</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Nota: </strong>
                </p>
                <ul>
                  <li class="unordered">
												Si habilita esta configuración, debe elegir una dirección de correo electrónico o una dirección URL personalizada rellenando el campo el campo <strong>Dirección de correo electrónico o URL personalizada</strong> que se encuentra debajo de esta opción.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de restablecimiento de contraseña:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si esta opción está establecida en No, cuando los usuarios hagan clic en el vínculo resaltado se enviará un correo electrónico a la dirección de correo electrónico principal de todos los administradores inquilinos para solicitar el restablecimiento de la contraseña. El correo electrónico se envía siguiendo la lógica que se indica a continuación:<br><br></li>
                  <li class="unordered">
                    <ul>
                      <li class="unordered">
														Si hay administradores de contraseñas, se envía el correo electrónico a todos los administradores de contraseñas, hasta un máximo de 100 destinatarios en total.<br><br></li>
                      <li class="unordered">
														Si no hay administradores de contraseñas, se envía el correo electrónico a todos los administradores de usuarios, hasta un máximo de 100 destinatarios en total.<br><br></li>
                      <li class="unordered">
														Si no hay administradores de usuarios, se envía el correo electrónico a todos los administradores globales, hasta un máximo de 100 destinatarios en total.<br><br></li>
                    </ul>
                  </li>
                  <li class="unordered">
												Si esta opción está establecida en Sí, la configuración personalizará el comportamiento del vínculo resaltado para que apunte a una dirección de correo electrónico o una dirección URL personalizada a la que los usuarios pueden ir para obtener ayuda con el restablecimiento de la contraseña.<br><br></li>
                  <li class="unordered">
												Si especifica una dirección URL, esta se abrirá en una pestaña nueva.<br><br></li>
                  <li class="unordered">
												Si especifica una dirección de correo electrónico, crearemos un vínculo mailto a dicha dirección de correo electrónico.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Dirección de correo electrónico o dirección URL personalizada</p>
              </td>
              <td>
                <p>Controla la dirección URL o de correo electrónico a la que apunta el vínculo <strong>Póngase en contacto con el administrador</strong>. </p>
                <p>
                  
                </p>
                <p>(Solo visible si la opción <strong>¿Desea personalizar el vínculo Póngase en contacto con el administrador?</strong> está establecida en <strong>Sí</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Debe ser una dirección de correo electrónico o dirección URL de intranet o extranet válida.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de restablecimiento de contraseña:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Cambia la ubicación a la que apunta el vínculo <strong>Póngase en contacto con el administrador</strong>.<br><br></li>
                  <li class="unordered">
												Si proporciona una dirección de correo electrónico, el vínculo se convierte en un vínculo "mailto" a esa dirección de correo electrónico.<br><br></li>
                  <li class="unordered">
												Si proporciona una dirección URL, el vínculo se convierte en un href estándar que señala a esa dirección URL, que se abrirá en una nueva pestaña.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Escritura diferida de contraseñas en el directorio local</p>
              </td>
              <td>
                <p>Controla si la escritura diferida de contraseñas está habilitada para este directorio y, si lo está, indica el estado del servicio de escritura diferida local.</p>
                <p>
                  
                </p>
                <p>Esto resulta útil si desea deshabilitar temporalmente el servicio debido a una interrupción.</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  <strong>Nota:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Este control solo aparece si ha instalado la escritura diferida de contraseñas tras descargar la versión más reciente de Azure AD Connect y habilitar la opción <strong>Escritura diferida de contraseñas</strong> en la pantalla de selección <strong>Características opcionales</strong>.<br><br></li>
                  <li class="unordered">
												Si ha habilitado la escritura diferida de contraseñas y cree que existe un problema de configuración con el servicio, vaya a esta pestaña y compruebe la etiqueta <strong>Estado del servicio de escritura diferida de contraseñas</strong> para ver si algo va mal.<br><br></li>
                  <li class="unordered">
												Los estados que se pueden mostrar son:<br><br><ul><li class="unordered"><strong>Configurado</strong>: todo funciona con normalidad.<br><br></li><li class="unordered"><strong>Sin configurar</strong>: tiene instalada la función de escritura diferida, pero no podemos acceder al servicio. Compruebe que no están bloqueadas las conexiones salientes a 443 y vuelva a instalar el servicio si sigue teniendo problemas.<br><br></li></ul></li>
                </ul>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si se ha implementado y configurado la escritura diferida y este modificador se establece en <strong>No</strong>, se deshabilita la escritura diferida y los usuarios federados y sincronizados por hash de contraseña no pueden registrarse para restablecer sus contraseñas.<br><br></li>
                  <li class="unordered">
												Si el modificador se establece en <strong>Sí</strong>, se habilita la escritura diferida y los usuarios federados y sincronizados por hash de contraseña pueden restablecer sus contraseñas.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de restablecimiento de contraseña:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si se ha implementado y configurado la escritura diferida y este modificador se establece en <strong>No</strong>, se deshabilita la escritura diferida y los usuarios federados y sincronizados por hash de contraseña no pueden restablecer sus contraseñas.<br><br></li>
                  <li class="unordered">
												Si el modificador se establece en <strong>Sí</strong>, se habilita la escritura diferida y los usuarios federados y sincronizados por hash de contraseña pueden restablecer sus contraseñas.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## Notificaciones de Administración de contraseñas
En la tabla siguiente se describe cómo afecta cada control a la experiencia de los usuarios y administradores que reciben notificaciones de restablecimiento de contraseña. Puede configurar estas opciones en la sección **Notificaciones** de la pestaña **Configurar** de su directorio en el [Portal de administración de Azure](https://manage.windowsazure.com).

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Control de directivas</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Descripción</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>¿Afecta?</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Notificar a los administradores cuando otros administradores restablezcan sus contraseñas</p>
              </td>
              <td>
                <p>Determina si a todos los administradores globales se les notificará mediante un mensaje a su dirección de correo electrónico principal cuando otro administrador de cualquier tipo restablezca su contraseña.</p>
              </td>
              <td>
                <p>
                  <strong>Portal de restablecimiento de contraseña:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si se establece en No, no se envían notificaciones.<br><br></li>
                  <li class="unordered">
												Si se establece en Sí, se notifica a todos los administradores globales cuando cualquier administrador restablezca su propia contraseña.<br><br></li>
                  <li class="unordered">
												Esta notificación se envía por correo electrónico a las direcciones de correo electrónico principales de todos los demás administradores globales de la organización.<br><br></li>
                </ul>
                <p>
                  <strong>Ejemplo:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si esta opción está habilitada cuando el administrador A restablece su contraseña, y hay otros 3 administradores en el inquilino, B, C y D, los administradores B, C y D reciben un correo electrónico que indica que el administrador A ha restablecido su contraseña.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Notificar a los usuarios y administradores cuando se haya restablecido su contraseña</p>
              </td>
              <td>
                <p>Determina si los usuarios finales y los administradores que restablecen sus contraseñas reciben o no una notificación por correo electrónico de que se ha restablecido su contraseña.</p>
              </td>
              <td>
                <p>
                  <strong>Portal de restablecimiento de contraseña:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si se establece en No, no se envían notificaciones.<br><br></li>
                  <li class="unordered">
												Si se establece en Sí, cada vez que un usuario o un administrador restablece su propia contraseña, recibe una notificación que indica que se ha restablecido su contraseña.<br><br></li>
                  <li class="unordered">
												Esta notificación se envía por correo electrónico a las direcciones principal y alternativa (o de autenticación) del usuario que restablezca su contraseña.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>


<br/> <br/> <br/>

**Recursos adicionales**


* [Qué es la administración de contraseñas](active-directory-passwords.md)
* [Funcionamiento de la administración de contraseñas](active-directory-passwords-how-it-works.md)
* [Introducción a la Administración de contraseñas](active-directory-passwords-getting-started.md)
* [Prácticas recomendadas de la Administración de contraseñas](active-directory-passwords-best-practices.md)
* [Visión operativa con los informes de administración de contraseñas](active-directory-passwords-get-insights.md)
* [Preguntas más frecuentes sobre la administración de contraseñas](active-directory-passwords-faq.md)
* [Solución de problemas de administración de contraseñas](active-directory-passwords-troubleshoot.md)
* [Más información](active-directory-passwords-learn-more.md)
* [Administración de contraseñas en MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx) 

<!---HONumber=August15_HO6-->