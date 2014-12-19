

Las instrucciones siguientes se aplican a la actualización de una aplicación cliente de la Tienda Windows, pero puede probar esto en cualquiera de las otras plataformas compatibles con Servicios móviles de Azure. 


1. En Visual Studio, abra MainPage.xaml.cs y agregue la siguiente instrucción "using" en la parte superior del archivo.
 
        using System.Net.Http;

2. En MainPage.xaml.cs, agregue la siguiente definición de clase al espacio de nombres para ayudar a serializar la información del usuario.

        public class UserInfo
        {
            public String displayName { get; set; }
            public String streetAddress { get; set; }
            public String city { get; set; }
            public String state { get; set; }
            public String postalCode { get; set; }
        }


3. En MainPage.xaml.cs, actualice el método  "AuthenticateAsync" para llamar a la API personalizada a fin de devolver información adicional acerca del usuario desde AAD. 

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);
                    UserInfo userInfo = await App.MobileService.InvokeApiAsync<UserInfo>("getuserinfo", 
                        HttpMethod.Get, null);
                    message = string.Format("{0}, you are now logged in.\n\nYour address is...\n\n{1}\n{2}, {3} {4}", 
                        userInfo.displayName, userInfo.streetAddress, userInfo.city, userInfo.state, 
                        userInfo.postalCode);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


4. Guarde los cambios y compile el servicio para comprobar que no existen errores de sintaxis.  
