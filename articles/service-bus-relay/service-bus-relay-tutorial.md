---
title: "Tutorial de retransmisión de WCF de Service Bus | Microsoft Docs"
description: "Cree un servicio y una aplicación cliente de Service Bus con la retransmisión de WCF de Service Bus."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: tysonn
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 57aec98a681e1cb5d75f910427975c6c3a1728c3
ms.openlocfilehash: 0977dc3fc1ebe4bbdb298ff8d72c7294052e7392


---
# <a name="service-bus-wcf-relay-tutorial"></a>Tutorial de retransmisión de WCF de Service Bus
En este tutorial se describe cómo crear un servicio y una aplicación cliente de Bus de servicio sencillos mediante las funciones de retransmisión del Bus de servicio. Para ver un tutorial correspondiente que usa la [mensajería asincrónica](../service-bus-messaging/service-bus-messaging-overview.md#brokered-messaging) de Service Bus, vea el [Tutorial de .NET de mensajería asincrónica de Service Bus](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md).

Al trabajar con este tutorial adquirirá unos conocimientos sobre los pasos necesarios para crear una aplicación cliente y de servicio de Bus de servicio. Como sus homólogos WCF, un servicio es una construcción que expone uno o varios extremos, cada uno de los cuales expone una o varias operaciones de servicio. El extremo de un servicio especifica una dirección donde se puede encontrar el servicio, un enlace que contiene la información que un cliente debe comunicar al servicio y un contrato que define la funcionalidad que ofrece el servicio a sus clientes. La diferencia principal entre un servicio WCF y uno de Bus de servicio es que el extremo se expone en la nube en lugar de localmente en su equipo.

Cuando complete la secuencia de temas de este tutorial, tendrá un servicio en ejecución y un cliente que puede invocar las operaciones de este servicio. En el primer tema se describe cómo configurar una cuenta. Los pasos siguientes explican cómo definir un servicio que usa un contrato, cómo implementar dicho servicio y cómo configurarlo en el código. También se describe cómo se hospeda y ejecuta el servicio. El servicio que se crea se hospeda a sí mismo y el cliente y el servicio se ejecutan en el mismo equipo. Puede configurar el servicio mediante código o con un archivo de configuración.

En los últimos tres pasos se describe cómo crear una aplicación cliente, cómo configurarla y cómo crear y usar un cliente que pueda tener acceso a la funcionalidad del host.

En todos los temas de esta sección se presupone que usa Visual Studio como entorno de desarrollo.

## <a name="sign-up-for-an-account"></a>Registro para obtener una cuenta
El primer paso consiste en crear un espacio de nombres y obtener una clave de firma de acceso compartido (SAS). Un espacio de nombres proporciona un límite de aplicación para cada aplicación que se expone a través del Bus de servicio. El sistema genera una clave SAS automáticamente cuando se crea un espacio de nombres de servicio. La combinación del espacio de nombres de servicio y la clave SAS proporciona las credenciales de Bus de servicio para autenticar el acceso a una aplicación.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract-to-use-with-service-bus"></a>Definir un contrato de servicio de WCF basado en REST para usarlo con el Bus de servicio
El contrato de servicio especifica las operaciones (la terminología del servicio web para funciones o métodos).que admite el servicio Los contratos se crean mediante la definición de una interfaz de C++, C# o Visual Basic. Cada método de la interfaz corresponde a una operación de servicio específica. Todas las interfaces deben tener el atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) aplicado, y todas las operaciones deben tener el atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) aplicado. Si un método en una interfaz que tiene el atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) no tiene el atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), no se expone dicho método. El código para estas tareas se ofrece en el ejemplo a continuación del procedimiento. Para leer una descripción completa de los contratos y servicios, vea [Diseño e implementación de servicios](https://msdn.microsoft.com/library/ms729746.aspx), en la documentación de WCF.

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>Para crear un contrato de Bus de servicio con una interfaz
1. Abra Visual Studio como administrador, para lo que debe hacer clic con el botón derecho en el programa del menú **Inicio** y, después, haga clic en **Ejecutar como administrador**.
2. Cree un nuevo proyecto de aplicación de consola. Haga clic en el menú **Archivo** y seleccione **Nuevo**; a continuación, haga clic en **Proyecto**. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **Visual C#** (si **Visual C#** no aparece, mire en **Otros lenguajes**). Haga clic en la plantilla **Aplicación de consola** y asígnele el nombre **EchoService**. Haga clic en **Aceptar** para crear el proyecto.

    ![][2]
3. Instale el paquete NuGet del Bus de servicio. Este paquete agrega automáticamente referencias a las bibliotecas de Service Bus, así como a **System.ServiceModel** de WCF. [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) es el espacio de nombres que permite el acceso mediante programación a las características básicas de WCF. Bus de servicio utiliza muchos de los objetos y atributos de WCF para definir contratos de servicio.

    En el Explorador de soluciones, haga clic con el botón derecho en la solución y luego haga clic en **Administrar paquetes NuGet para la solución**. Haga clic en la pestaña **Examinar** y luego busque `Microsoft Azure Service Bus`. Asegúrese de que el nombre del proyecto está seleccionado en el cuadro **Versiones**. Haga clic en **Instalar**y acepte las condiciones de uso.

    ![][3]
4. En el Explorador de soluciones, haga doble clic en el archivo Program.cs para abrirlo en el editor en caso de que no esté ya abierto.
5. Agregue las siguientes instrucciones using en la parte superior del archivo:

    ```
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```
6. Cambie el nombre de espacio de nombres de su nombre predeterminado **EchoService** a **Microsoft.ServiceBus.Samples**.

   > [!IMPORTANT]
   > En este tutorial se usa el espacio de nombres de C# **Microsoft.ServiceBus.Samples**, que es el espacio de nombres del tipo administrado por contrato que se utiliza en el archivo de configuración del paso [Configurar el cliente de WCF](#configure-the-wcf-client). Puede especificar el espacio de nombres que quiera al compilar este ejemplo; no obstante, el tutorial no funcionará a menos que también modifique los espacios de nombres del contrato y el servicio de manera acorde, en el archivo de configuración de la aplicación. El espacio de nombres especificado en el archivo App.config debe ser el mismo que el que se especifique en los archivos de C#.
   >
   >
7. Inmediatamente después de la declaración del espacio de nombres `Microsoft.ServiceBus.Samples`, pero dentro del espacio de nombres, defina una nueva interfaz denominada `IEchoContract` y aplique el atributo `ServiceContractAttribute` a la interfaz con un valor de espacio de nombres de **http://samples.microsoft.com/ServiceModel/Relay/**. El valor del espacio de nombres difiere del espacio de nombres que utiliza en todo el ámbito de su código. En su lugar, el valor del espacio de nombres se usa como identificador único para este contrato. La especificación del espacio de nombres impide explícitamente que el valor del espacio de nombres predeterminado se agregue al nombre del contrato.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

   > [!NOTE]
   > Normalmente, el espacio de nombres del contrato de servicio contiene un esquema de nomenclatura que incluye información de versión. Al incluirse la información de versión en el espacio de nombres del contrato de servicio, los servicios pueden aislar los cambios más importantes mediante la definición de un nuevo contrato de servicio con un nuevo espacio de nombres y su exposición en un nuevo extremo. De esta manera, los clientes pueden seguir usando el contrato de servicio anterior sin tener que actualizarse. La información de versión puede constar de una fecha o un número de compilación. Para obtener más información, vea [Control de versiones del servicio](http://go.microsoft.com/fwlink/?LinkID=180498). Para los fines de este tutorial, el esquema de nomenclatura del espacio de nombres del contrato de servicio no contiene información de versión.
   >
   >
8. Dentro de la interfaz `IEchoContract`, declare un método para la operación sencilla que el contrato `IEchoContract` expone en la interfaz y aplique el atributo `OperationContractAttribute` al método que desea exponer como parte del contrato público del Bus de servicio.

    ```
    [OperationContract]
    string Echo(string text);
    ```
9. Justo después de la definición de interfaz `IEchoContract`, declare un canal que herede de `IEchoContract` y también de la interfaz de `IClientChannel`, como se muestra a continuación:

    ```
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Un canal es el objeto de WCF a través del cual el host y el cliente se pasan información entre sí. Más tarde, escribirá código para el canal a fin de enviar información entre las dos aplicaciones.
10. En el menú **Compilar**, haga clic en **Compilar solución** o presione **Ctrl+Mayús+B** para confirmar la precisión del trabajo realizado.

### <a name="example"></a>Ejemplo
En el ejemplo de código siguiente se muestra una interfaz básica que define un contrato de Bus de servicio.

```
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Ahora que la interfaz se ha creado, puede implementarla.

## <a name="implement-the-wcf-contract-to-use-service-bus"></a>Implementar el contrato de WCF para usar el Bus de servicio
La creación de una retransmisión de bus de servicio requiere que primero cree el contrato, que se define con una interfaz. Para más información sobre cómo crear la interfaz, consulte el paso anterior. El siguiente paso es implementar la interfaz. Esto implica la creación de una clase denominada `EchoService` que implemente la interfaz `IEchoContract` definida por el usuario. Después de implementar la interfaz, se debe configurar con un archivo App.config. El archivo de configuración contiene la información necesaria para la aplicación, como el nombre del servicio, el nombre del contrato y el tipo de protocolo que se utiliza para comunicarse con el Bus de servicio. El código utilizado para estas tareas se proporciona en el ejemplo que sigue al procedimiento. Para obtener información más general sobre cómo implementar un contrato de servicio, vea [Implementación de contratos de servicio](https://msdn.microsoft.com/library/ms733764.aspx) en la documentación de WCF.

1. Cree una nueva clase denominada `EchoService` directamente después de la definición de la interfaz `IEchoContract`. La clase `EchoService` implementa la interfaz `IEchoContract`.

    ```
    class EchoService : IEchoContract
    {
    }
    ```

    Al igual que otras implementaciones de interfaz, puede implementar la definición en un archivo diferente. Sin embargo, para este tutorial, la implementación se ubica en el mismo archivo que la definición de la interfaz y el método `Main`.
2. Aplique el atributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) en la interfaz de `IEchoContract`. El atributo especifica el nombre del servicio y el espacio de nombres. Después de hacerlo, aparecerá la clase `EchoService` de la siguiente manera:

    ```
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```
3. Implemente el método `Echo` definido en la interfaz `IEchoContract` de la clase `EchoService`.

    ```
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```
4. Haga clic en **Compilar** y luego en **Compilar solución** para confirmar la precisión del trabajo realizado.

### <a name="to-define-the-configuration-for-the-service-host"></a>Para definir la configuración del host de servicio
1. El archivo de configuración es muy similar a un archivo de configuración de WCF. Incluye el nombre del servicio, el extremo (es decir, la ubicación que el Bus de servicio expone para que clientes y hosts se comuniquen entre sí) y el enlace (el tipo de protocolo que se usa para la comunicación). La principal diferencia es que el punto de conexión de servicio configurado hace referencia a un enlace [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) que no forma parte de .NET Framework. [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) es uno de los enlaces que define Service Bus.
2. En el **Explorador de soluciones**, haga doble clic en el archivo App.config para abrirlo en el editor de Visual Studio.
3. En el elemento `<appSettings>`, reemplace los marcadores de posición con el nombre de su espacio de nombres de servicio y la clave SAS que copió en el paso anterior.
4. Dentro de las etiquetas `<system.serviceModel>`, agregue un elemento `<services>`. Puede definir varias aplicaciones del Bus de servicio en un solo archivo de configuración. Sin embargo, este tutorial solo define una.

    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```
5. Dentro del elemento `<services>`, agregue un elemento `<service>` para definir el nombre del servicio.

    ```
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```
6. Dentro del elemento `<service>`, defina la ubicación del contrato del punto de conexión y también el tipo de enlace del extremo.

    ```
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    El extremo define dónde buscará el cliente la aplicación host. Más adelante en el tutorial, se usa este paso para crear un identificador URI que expone completamente el host a través del Bus de servicio. El enlace declara que se está usando TCP como protocolo para comunicarse con el Bus de servicio.
7. En el menú **Compilar**, haga clic en **Compilar solución** para confirmar la precisión del trabajo realizado.

### <a name="example"></a>Ejemplo
En el código siguiente se muestra la implementación del contrato de servicio.

```
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

En el código siguiente se muestra el formato básico del archivo App.config asociado al servicio.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-a-basic-web-service-to-register-with-service-bus"></a>Hospedar y ejecutar un servicio web básico para registrarse con el Bus de servicio
En este paso se detalla cómo ejecutar un servicio de Bus de servicio básico.

### <a name="to-create-the-service-bus-credentials"></a>Para crear las credenciales del Bus de servicio
1. En `Main()`, cree dos variables para almacenar el espacio de nombres y la clave de SAS que se leen desde la ventana de la consola.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    La clave de SAS se usará más adelante para obtener acceso al proyecto de Bus de servicio. El espacio de nombres se pasa como parámetro a `CreateServiceUri` para crear un URI de servicio.
2. Con un objeto [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx), declare que usará una clave de SAS como tipo de credencial. Agregue el código siguiente directamente después del código de que se agregó en el último paso.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="to-create-a-base-address-for-the-service"></a>Para crear una dirección base para el servicio
1. Siguiendo el código que agregó en el último paso, cree una instancia de `Uri` para la dirección base del servicio. Este identificador URI especifica el esquema del Bus de servicio, el espacio de nombres y la ruta de acceso de la interfaz del servicio.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

    "sb" es una abreviatura para el esquema de Bus de servicio e indica que se usa TCP como protocolo. Esto también se indicó anteriormente en el archivo de configuración, cuando se especificó [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) como el enlace.

    Para este tutorial, el identificador URI es `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="to-create-and-configure-the-service-host"></a>Para crear y configurar el host del servicio
1. Establezca el modo de conectividad a `AutoDetect`.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    El modo de conectividad describe el protocolo que usa el servicio para comunicarse con el Bus de servicio; HTTP o TCP. Con la configuración predeterminada `AutoDetect`, el servicio intenta conectarse al Bus de servicio a través de TCP si está disponible, y HTTP si no lo está. Tenga en cuenta que esto difiere del protocolo que especifica el servicio para la comunicación del cliente. Dicho protocolo viene determinado por el enlace utilizado. Por ejemplo, un servicio puede usar el enlace [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx), que especifica que su punto de conexión (expuesto en Service Bus) se comunica con los clientes a través de HTTP. Ese mismo servicio podría especificar **ConnectivityMode.AutoDetect** para que el servicio se comunique con Service Bus mediante TCP.
2. Cree el host del servicio, con el identificador URI creado anteriormente en esta sección.

    ```
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    El host del servicio es el objeto de WCF que crea una instancia del servicio. En este caso, se le pasa el tipo de servicio que quiere crear (un tipo `EchoService`) y también a la dirección en la que quiere exponer el servicio.
3. En la parte superior del archivo Program.cs, agregue referencias a [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) y [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx).

    ```
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```
4. De nuevo en `Main()`, configure el punto de conexión para permitir el acceso público.

    ```
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    En este paso se informa al Bus de servicio de que la aplicación se puede encontrar públicamente al examinar la fuente Atom del Bus de servicio del proyecto. Si establece **DiscoveryType** en **privada**, un cliente podría seguir teniendo acceso al servicio. Sin embargo, el servicio no aparecería cuando buscase el espacio de nombres del Bus de servicio. En su lugar, el cliente tendría que conocer de antemano la ruta de acceso del extremo.
5. Aplique las credenciales de servicio a los extremos de servicio definidos en el archivo App.config:

    ```
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Como se indicó en el paso anterior, podría haber declarado varios servicios y extremos en el archivo de configuración. Si lo hubiera hecho, este código recorrería el archivo de configuración y buscaría todos los extremos a los que se deban aplicar las credenciales. Sin embargo, para este tutorial, el archivo de configuración tiene un solo extremo.

### <a name="to-open-the-service-host"></a>Para abrir el host del servicio.
1. Abra el servicio.

    ```
    host.Open();
    ```
2. Informe al usuario de que se está ejecutando el servicio y explíquele cómo cerrarlo.

    ```
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. Cuando termine, cierre el host del servicio.

    ```
    host.Close();
    ```
4. Presione **Ctrl+Mayús+B** para compilar el proyecto.

### <a name="example"></a>Ejemplo
En el ejemplo siguiente se incluye el contrato de servicio y la implementación de los pasos anteriores del tutorial y se hospeda el servicio en una aplicación de consola. Compile lo siguiente en un archivo ejecutable denominado EchoService.exe.

```
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Service Bus credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Crear un cliente WCF para el contrato de servicio
El siguiente paso consiste en crear una aplicación cliente básica de Bus de servicio y definir el contrato de servicio que se implementará en pasos posteriores. Tenga en cuenta que muchos de estos pasos son similares a los que se siguieron para crear un servicio: definir un contrato, editar un archivo App.config, usar credenciales para conectarse al Bus de servicio, etc. El código utilizado para estas tareas se proporciona en el ejemplo que sigue al procedimiento.

1. Cree un nuevo proyecto en la solución actual de Visual Studio para el cliente mediante los pasos siguientes:

   1. En el Explorador de soluciones, en la misma solución que contenga el servicio, haga clic con el botón derecho en la solución actual (no el proyecto) y haga clic en **Agregar**. Después, haga clic en **Nuevo proyecto**.
   2. En el cuadro de diálogo **Agregar nuevo proyecto**, haga clic en **Visual C#** (si **Visual C#** no aparece, mire en **Otros lenguajes**), seleccione la plantilla **Aplicación de consola**y asígnele el nombre **EchoClient**.
   3. Haga clic en **Aceptar**.
      <br />
2. En el Explorador de soluciones, haga doble clic en el archivo Program.cs del proyecto **EchoClient** para abrirlo en el editor en caso de que no esté ya abierto.
3. Cambie el nombre del espacio de nombres de su nombre predeterminado de `EchoClient` a `Microsoft.ServiceBus.Samples`.
4. Instale el [paquete NuGet de Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus). En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **EchoClient** y después haga clic en **Administrar paquetes NuGet**. Haga clic en la pestaña **Examinar** y luego busque `Microsoft Azure Service Bus`. Haga clic en **Instalar**y acepte las condiciones de uso.

    ![][3]
5. Agregue una instrucción `using` para el espacio de nombres [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) en el archivo Program.cs.

    ```
    using System.ServiceModel;
    ```
6. Agregue la definición del contrato de servicio al espacio de nombres, como se muestra en el ejemplo siguiente. Tenga en cuenta que esta definición es idéntica a la definición que se usa en el proyecto **Service**. Debe agregar este código en la parte superior del espacio de nombres `Microsoft.ServiceBus.Samples`.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```
7. Presione **Ctrl+Shift+B** para compilar el cliente.

### <a name="example"></a>Ejemplo
En el código siguiente se muestra el estado actual del archivo Program.cs en el proyecto EchoClient.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Configurar el cliente de WCF
En este paso, creará un archivo App.config para una aplicación cliente básica que accede al servicio creado anteriormente en este tutorial. Este archivo App.config define el contrato, el enlace y el nombre del extremo. El código utilizado para estas tareas se proporciona en el ejemplo que sigue al procedimiento.

1. En el Explorador de soluciones, en el proyecto **EchoClient**, haga doble clic en **App.config** para abrir el archivo en el editor de Visual Studio.
2. En el elemento `<appSettings>`, reemplace los marcadores de posición con el nombre de su espacio de nombres de servicio y la clave SAS que copió en el paso anterior.
3. Dentro del elemento system.serviceModel, agregue un elemento `<client>`.

    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    En este paso se declara que está definiendo una aplicación cliente de estilo WCF.
4. Dentro del elemento `client`, defina el nombre, el contrato y el tipo de enlace para el punto de conexión.

    ```
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    En este paso se define el nombre del extremo, el contrato definido en el servicio y el hecho de que la aplicación cliente use TCP para comunicarse con el Bus de servicio. El nombre del extremo se usa en el paso siguiente para vincular la configuración de este extremo con el identificador URI del servicio.
5. Haga clic en **Archivo** y en **Guardar todo**.

## <a name="example"></a>Ejemplo
En el código siguiente se muestra el archivo App.config del cliente de eco.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client-to-call-service-bus"></a>Implementar el cliente de WCF para llamar al Bus de servicio
En este paso, implementará una aplicación cliente básica que accede al servicio que creó anteriormente en este tutorial. De forma similar al servicio, el cliente realiza muchas de las mismas operaciones para tener acceso al Bus de servicio:

1. Establece el modo de conectividad.
2. Crea el identificador URI que localiza el servicio de host.
3. Define las credenciales de seguridad.
4. Aplica a las credenciales para la conexión.
5. Abre la conexión.
6. Realiza las tareas específicas de la aplicación.
7. Cierra la conexión.

Sin embargo, una de las principales diferencias es que la aplicación cliente usa un canal para conectarse a Service Bus, mientras que el servicio utiliza una llamada a **ServiceHost**. El código utilizado para estas tareas se proporciona en el ejemplo que sigue al procedimiento.

### <a name="to-implement-a-client-application"></a>Para implementar una aplicación cliente
1. Establezca el modo de conectividad en **AutoDetect**. Agregue el código siguiente dentro del método `Main()` de la aplicación **EchoClient**.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```
2. Defina variables para contener los valores del espacio de nombres del servicio y la clave SAS que se leen desde la consola.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```
3. Cree el identificador URI que define la ubicación del host en el proyecto de Bus de servicio.

    ```
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```
4. Cree el objeto de credenciales para el extremo del espacio de nombres de servicio.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```
5. Cree el generador de canales que carga la configuración que se describe en el archivo App.config.

    ```
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Un generador de canales es un objeto de WCF que crea un canal a través del cual se comunican las aplicaciones cliente y de servicio.
6. Aplique las credenciales del Bus de servicio.

    ```
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```
7. Cree y abra el canal al servicio.

    ```
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```
8. Escriba la interfaz de usuario básica y la funcionalidad del eco.

    ```
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Tenga en cuenta que el código usa la instancia del objeto de canal como un proxy para el servicio.
9. Cierre el canal y el generador.

    ```
    channel.Close();
    channelFactory.Close();
    ```

## <a name="to-run-the-applications"></a>Ejecución de las aplicaciones
1. Presione **Ctrl+Mayús+B** para compilar la solución. Esto compila el proyecto de cliente y el proyecto de servicio que creó en los pasos anteriores.
2. Antes de ejecutar la aplicación cliente, debe asegurarse de que se está ejecutando la aplicación del servicio. En el Explorador de soluciones en Visual Studio, haga clic con el botón derecho en la solución **EchoService** y después haga clic en **Propiedades**.
3. En el cuadro de diálogo de propiedades de la solución haga clic en **Proyecto de inicio** y después haga clic en el botón **Proyectos de inicio múltiples**. Asegúrese de que **EchoService** aparece en primer lugar en la lista.
4. Defina el cuadro **Acción** para los proyectos **EchoService** y **EchoClient** en **Iniciar**.

    ![][5]
5. Haga clic en **Dependencias del proyecto**. En el cuadro **Proyectos**, seleccione **EchoClient**. En el cuadro **Depende de:**, asegúrese de que **EchoService** está seleccionado.

    ![][6]
6. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Propiedades**.
7. Presione **F5** para ejecutar ambos proyectos.
8. Ambas ventanas de consola se abrirán y le solicitarán el espacio de nombres. Primero debe ejecutarse el servicio, por ello, en la ventana de la consola de **EchoService** escriba el espacio de nombres y después presione **Entrar**.
9. A continuación, se le solicitará su clave de SAS. Escríbala y presione ENTRAR.

    Este un ejemplo del resultado de la ventana de consola. Tenga en cuenta que los valores que se proporcionan aquí se ofrecen solamente como un ejemplo.

    `Your Service Namespace: myNamespace`
    `Your SAS Key: <SAS key value>`

    La aplicación del servicio imprime en la ventana de la consola la dirección en la que está escuchando, tal y como se muestra en el ejemplo siguiente.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`
    `Press [Enter] to exit`
10. En la ventana de la consola de **EchoClient** escriba la misma información que especificó anteriormente para la aplicación de servicio. Siga los pasos anteriores para especificar los mismos valores de espacio de nombres de servicio y clave SAS para la aplicación cliente.
11. Tras especificar estos valores, el cliente abrirá un canal al servicio y se le solicitará que escriba algo de texto, como se muestra en el siguiente ejemplo de salida de consola.

    `Enter text to echo (or [Enter] to exit):`

    Escriba algo de texto para enviarlo a la aplicación de servicio y presione ENTRAR. Este texto se enviará al servicio a través de la operación de servicio de eco y aparecerá en la ventana de la consola del servicio, como se muestra en el siguiente ejemplo de salida.

    `Echoing: My sample text`

    La aplicación cliente recibe el valor devuelto de la operación `Echo`, que es el texto original, y lo imprime en la ventana de consola. A continuación se muestra un ejemplo del resultado de la ventana de consola del cliente.

    `Server echoed: My sample text`
12. Puede continuar enviando mensajes de texto desde el cliente al servicio de esta manera. Cuando haya terminado, presione ENTRAR en las ventanas de las consolas del cliente y el servicio para finalizar ambas aplicaciones.

## <a name="example"></a>Ejemplo
En el ejemplo siguiente se muestra cómo crear una aplicación cliente, cómo llamar a las operaciones del servicio y cómo cerrar el cliente cuando finaliza la llamada a la operación.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
En este tutorial se ha mostrado cómo crear un servicio y una aplicación cliente del Bus de servicio sencillos mediante las funciones de retransmisión del Bus de servicio. Para ver un tutorial similar que usa la [mensajería](../service-bus-messaging/service-bus-messaging-overview.md#brokered-messaging) de Service Bus, vea el [Tutorial de .NET de mensajería asincrónica de Service Bus](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md).

Para más información sobre Service Bus, consulte los temas siguientes.

* [Introducción a la mensajería del Bus de servicio](../service-bus-messaging/service-bus-messaging-overview.md)
* [Elementos fundamentales de Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* [Arquitectura de Service Bus](../service-bus-messaging/service-bus-architecture.md)

[Portal de Azure clásico]: http://manage.windowsazure.com

[1]: ./media/service-bus-relay-tutorial/service-bus-policies.png
[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[4]: ./media/service-bus-relay-tutorial/create-ns.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png



<!--HONumber=Nov16_HO3-->


