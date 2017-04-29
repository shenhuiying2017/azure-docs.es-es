## <a name="connect-to-outlookcom"></a>Conectarse a Outlook.com
### <a name="prerequisites"></a>Requisitos previos
* Una cuenta de Outlook.com

Para poder usar su cuenta de Outlook.com en una aplicación lógica, debe autorizar a la aplicación lógica para que pueda conectarse a dicha cuenta. Por suerte, esto es muy fácil de hacer desde la aplicación lógica en el Portal de Azure. 

Aquí se explica cómo autorizar a la aplicación lógica para conectarse a su cuenta de Outlook.com:

1. Todas las aplicaciones lógicas se inician a través de un desencadenador; por eso, después de crear la aplicación lógica se abrirá el diseñador con una lista de los desencadenadores que puede usar para iniciarla:
   
   ![](./media/connectors-create-api-outlook/office365-outlook-0.png)
2. Escriba "outlook" en el cuadro de búsqueda. Vea que la lista se filtra para mostrar todos los desencadenadores que tengan "Outlook" en el nombre: ![](./media/connectors-create-api-outlook/office365-outlook-0-5.png)
3. Seleccione **Office 365 Outlook - On new email** (Outlook de Office 365: al recibir correo nuevo).   
   Si no ha creado ninguna conexión a Outlook antes, se le pedirá que indique sus credenciales de Outlook.com. Estas credenciales se usarán para autorizar a la aplicación lógica a conectarse y acceder a los datos de su cuenta de Outlook.com: ![](./media/connectors-create-api-outlook/office365-outlook-1.png)
4. Indique sus credenciales de Outlook e inicie sesión: ![](./media/connectors-create-api-outlook/office365-outlook-2.png)  
   Eso es todo. Acaba de crear una conexión a Outlook. Esta conexión estará disponible para su uso en cualquier otra aplicación lógica que cree.

