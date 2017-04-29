Ahora que ha agregado una condición, es hora de hacer algo interesante con los datos que genera el desencadenador. Para agregar la acción **Salesforce - Get object** (Salesforce: obtener objeto), siga estos pasos. Con esta acción, cada vez que se crea un cliente potencial se obtienen los datos. También agregará una segunda acción que usará los datos de la acción Salesforce - Get object (Salesforce: obtener objeto) para enviar un correo electrónico mediante el conector de Office 365.  

Para configurar esta acción, deberá proporcionar la siguiente información. Observará que resulta sencillo usar los datos generados por el desencadenador como entrada para algunas de las propiedades del nuevo archivo:

| Propiedad Crear archivo | Description |
| --- | --- |
| Tipo de objeto |Este es el tipo de objeto de Salesforce que le interesa. Algunos ejemplos son clientes potenciales, cuentas, etc. |
| Id. de objeto |Representa un identificador para el objeto. |

1. Seleccione el vínculo **Add an action** (Agregar una acción). Se abrirá el cuadro de búsqueda en el que podrá buscar cualquier acción que quiera realizar. En este ejemplo, nos interesan las acciones de Salesforce.      
   ![Imagen 1 de acción de Salesforce](./media/connectors-create-api-salesforce/action-1.png)  
2. Escriba *salesforce* para buscar acciones relacionadas con salesforce.
3. Seleccione **Salesforce - Get object** (Salesforce: obtener objeto) como la acción que se realizará.   **Nota**: Si no lo ha hecho anteriormente, se le pedirá que autorice a la aplicación lógica el acceso a la cuenta de Salesforce.    
   ![Imagen 2 de acción de Salesforce](./media/connectors-create-api-salesforce/action-2.png)    
4. Se abre el control **Get object** (Obtener objeto).  
5. Seleccione *lead* (cliente potencial) como el tipo de objeto.
6. Seleccione el control **Object ID** (Id. de objeto).
7. Seleccione **...** para expandir la lista de tokens que se pueden usar como entrada para las acciones.       
   ![Imagen 3 de acción de Salesforce](./media/connectors-create-api-salesforce/action-3.png)    
8. Seleccione **Lead ID** (Id. de cliente potencial); el control se abre.   
   ![Imagen 4 de acción de Salesforce](./media/connectors-create-api-salesforce/action-4.png)     
9. Observe que el token Lead ID (Id. de cliente potencial) está ahora en el control Object ID (Id. de objeto), lo que indica que la acción Get object (Obtener objeto) buscará un cliente potencial cuyo identificador sea igual que el identificador del cliente potencial que desencadenó esta aplicación lógica.  
   ![Imagen 5 de acción de Salesforce](./media/connectors-create-api-salesforce/action-5.png)  
10. Guarde el trabajo. Y eso es todo, ha agregado la acción Get object (Obtener objeto) a la aplicación lógica. El control Get object (Obtener objeto) debe tener este aspecto:     
    ![Imagen 6 de acción de Salesforce](./media/connectors-create-api-salesforce/action-6.png)  

Ahora que ha agregado una acción para obtener un cliente potencial, puede hacer algo interesante con el cliente potencial recién creado. En una empresa, puede que quiera enviar un correo electrónico para notificar a una lista de distribución que se ha creado un nuevo cliente potencial. Vamos a usar el conector de Office 365 para enviar un correo electrónico con información relacionada del nuevo objeto de cliente potencial de Salesforce.  

1. Seleccione **Add an action** (Agregar una acción) y escriba *correo electrónico* en el control de búsqueda. De esta manera se filtran las acciones relacionadas con el envío y la recepción de correo electrónico.  
2. Seleccione el elemento de la lista **Office 365 Outlook - Send an email** (Office 365 Outlook: enviar un correo electrónico). Si no ha creado todavía una *conexión* a su cuenta de Office 365, se le pedirá que escriba las credenciales de Office 365 para crearla ahora. Cuando haya terminado, se abrirá el control **Send an email** (Enviar un correo electrónico).        
   ![Imagen 7 de acción de Salesforce](./media/connectors-create-api-salesforce/action-7.png)  
3. Escriba la dirección de correo electrónico a la que le gustaría enviar correo electrónico en el control **To** (Para).
4. En el control **Subject** (Asunto), escriba *New Lead created* (Nuevo cliente potencial creado) y seleccione el token *Company* (Compañía). Se mostrará el campo *company* (compañía) del nuevo cliente potencial creado en Salesforce.  
5. En el control **body** (cuerpo), puede seleccionar cualquiera de los tokens del nuevo objeto de cliente potencial y también puede escribir un texto que quiera que se muestre en el cuerpo del correo electrónico. Veamos un ejemplo:   
   ![Imagen 8 de acción de Salesforce](./media/connectors-create-api-salesforce/action-8.png)   
6. Guarde el flujo de trabajo.  

Eso es todo. Ahora la aplicación lógica está completa.  

A continuación, puede probarla: en Salesforce, cree un nuevo cliente potencial que cumpla la condición que creó.  Si ha seguido completamente este tutorial, solo tiene que crear un cliente potencial con una dirección de correo electrónico que contenga *amazon.com*. Al cabo de unos segundos, se deberá desencadenar la aplicación lógica y los resultados pueden ser parecidos a estos:  
![Imagen 9 de acción de Salesforce](./media/connectors-create-api-salesforce/action-9.png)  

