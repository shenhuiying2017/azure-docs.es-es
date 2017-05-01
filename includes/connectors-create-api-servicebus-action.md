Ahora que ha agregado un desencadenador, es hora de hacer algo interesante con los datos que genera. Siga estos pasos para agregar una acción **Service Bus - Send message** (Bus de servicio: enviar mensaje). Esta acción enviará un mensaje al Bus de servicio.  

Siga estos pasos para crear la acción envío del mensaje:  

1. Seleccione **+ New step** (+ Nuevo paso) para agregar la acción.  
2. Seleccione **Add an action**(Agregar una acción). Se abrirá el cuadro de búsqueda en el que podrá buscar cualquier acción que quiera realizar. En este ejemplo, nos interesan las acciones del Bus de servicio.    
   ![Imagen 1 de acción del Bus de servicio](./media/connectors-create-api-servicebus/action-1.png)   
3. Escriba *Bus de servicio*.  
4. Seleccione **Service Bus - Send message** (Service Bus: enviar mensaje). Esta es la acción que se realizará.  
   ![Imagen 2 de acción del Bus de servicio](./media/connectors-create-api-servicebus/action-2.png)    
5. Escriba el contenido del mensaje. Este es un paso necesario.  
6. Escriba el nombre de la cola o del tema a los que se enviará el mensaje. Este paso también es necesario.   
7. Opcionalmente, puede proporcionar otros detalles sobre el mensaje. Esto es opcional.     
   ![Imagen 3 de acción del Bus de servicio](./media/connectors-create-api-servicebus/action-3.png)    
8. Guarde los cambios en el flujo de trabajo.   
   ![Imagen 4 de acción del Bus de servicio](./media/connectors-create-api-servicebus/action-4.png)     

