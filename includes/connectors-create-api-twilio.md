### <a name="prerequisites"></a>Requisitos previos
* Una cuenta de Twilio
* Un número de teléfono de Twilio comprobado que pueda recibir SMS
* Un número de teléfono de Twilio comprobado que pueda enviar SMS

> [!NOTE]
> Si usa una cuenta de prueba de Twilio, solamente podrá enviar SMS a números de teléfono **comprobados**.  
> 
> 

Para poder usar su cuenta de Twilio en una aplicación lógica, debe autorizar a la aplicación lógica para que pueda conectarse a dicha cuenta. Por suerte, esto es muy fácil de hacer desde la aplicación lógica en el Portal de Azure. 

Aquí se explica cómo autorizar a la aplicación lógica para conectarse a su cuenta de Twilio:

1. Para crear una conexión a Twilio, en el diseñador de aplicaciones lógicas, seleccione **Mostrar las API administradas por Microsoft** en la lista desplegable y, luego, escriba *Twilio* en el cuadro de búsqueda. Seleccione el desencadenador o la acción que quiera usar:   
   ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Si no ha creado ninguna conexión a Twilio antes, se le pedirá que indique sus credenciales de Twilio. Estas credenciales se usarán para autorizar a la aplicación lógica para conectarse y tener acceso a los datos de su cuenta de Twilio:  
   ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Necesitará el **identificador cuenta de Twilio** y el **token de acceso de Twilio** del panel de Twilio, así que inicie sesión en su cuenta de Twilio para obtener esos dos datos:  
   ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio y las aplicaciones lógicas usan nombres diferentes para identificar estos dos tipos de datos. Aquí se indica cómo hay que asignarlos al cuadro de diálogo de Logic Apps: ![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Seleccione el botón **Crear conexión**:  
   ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Observe que la conexión se ha creado y que puede continuar sin problemas con el resto de pasos en la aplicación lógica:   
   ![](./media/connectors-create-api-twilio/twilio-5.png)



<!--HONumber=Nov16_HO3-->


