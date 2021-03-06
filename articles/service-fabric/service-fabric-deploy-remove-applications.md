---
title: "Implantação de aplicativos do Service Fabric | Microsoft Docs"
description: "Como implantar e remover aplicativos na Malha do Serviço"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/14/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 4dea75987df0cf05126564d2c7e20d59b842dc1c
ms.openlocfilehash: a9aa385d8c18600c50797a76e5a67754c695fdf8


---
# <a name="deploy-and-remove-applications-using-powershell"></a>Implantar e remover aplicativos usando o PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> 
> 

<br/>

Quando um [tipo de aplicativo tiver sido empacotado][10], ele estará pronto para implantação em um cluster do Azure Service Fabric. A implantação envolve as três etapas a seguir:

1. Carregar o pacote de aplicativo
2. Registrar o tipo de aplicativo
3. Criar a instância do aplicativo

> [!NOTE]
> Se você usar o Visual Studio para implantar e depurar aplicativos no cluster de desenvolvimento local, todas as etapas a seguir serão tratadas automaticamente por meio de um script do PowerShell encontrado na pasta Scripts do projeto de aplicativo. Este artigo fornece informações sobre o que esses scripts fazem para que você possa executar as mesmas operações fora do Visual Studio.
> 
> 

## <a name="upload-the-application-package"></a>Carregar o pacote de aplicativo
O carregamento do pacote de aplicativos coloca-o em um local acessível por componentes internos do Service Fabric. Você pode usar o PowerShell para executar o carregamento. Antes de executar qualquer comando do PowerShell neste artigo, sempre comece usando [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/servicefabric/vlatest/connect-servicefabriccluster) para se conectar ao cluster do Service Fabric.

Suponhamos que você tenha uma pasta chamada *MyApplicationType* que contém o manifesto de aplicativo necessário, os manifestos de serviço e os pacotes de código/configuração/dados. O comando [Copy-ServiceFabricApplicationPackage](https://docs.microsoft.com/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) carrega o pacote para o Repositório de Imagens do cluster. O cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , que faz parte do módulo do PowerShell do SDK do Service Fabric, é usado para obter a cadeia de conexão do repositório de imagens.  Para importar o módulo do SDK, execute:

```
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Você pode copiar um pacote de aplicativos de *C:\users\ryanwi\Documents\Visual Studio 2015\Projects\MyApplication\myapplication\pkg\debug* to *c:\temp\MyApplicationType* (renomeie o diretório "debug" como "MyApplicationType"). O exemplo a seguir carrega o pacote:

~~~
PS C:\temp> dir

    Directory: c:\temp


Mode                LastWriteTime         Length Name                                                                                   
----                -------------         ------ ----                                                                                   
d-----        8/12/2016  10:23 AM                MyApplicationType                                                                          

PS C:\temp> tree /f .\Stateless1Pkg

C:\TEMP\MyApplicationType
│   ApplicationManifest.xml
|
└───Stateless1Pkg
    |   ServiceManifest.xml
    │
    └───Code
    │   │  Microsoft.ServiceFabric.Data.dll
    │   │  Microsoft.ServiceFabric.Data.Interfaces.dll
    │   │  Microsoft.ServiceFabric.Internal.dll
    │   │  Microsoft.ServiceFabric.Internal.Strings.dll
    │   │  Microsoft.ServiceFabric.Services.dll
    │   │  ServiceFabricServiceModel.dll
    │   │  MyService.exe
    │   │  MyService.exe.config
    │   │  MyService.pdb
    │   │  System.Fabric.dll
    │   │  System.Fabric.Strings.dll
    │   │
    │   └───en-us
    |         Microsoft.ServiceFabric.Internal.Strings.resources.dll
    |         System.Fabric.Strings.resources.dll
    |
    ├───Config
    │     Settings.xml
    │
    └───Data
          init.dat

PS C:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath MyApplicationType -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
Copy application package succeeded

PS D:\temp>
~~~

## <a name="register-the-application-package"></a>Registrar o pacote de aplicativo
O registro do pacote de aplicativos disponibiliza para uso o tipo de aplicativo e a versão declarada no manifesto do aplicativo. O sistema lê o pacote carregado na etapa anterior, verifica o pacote (equivalente a executar [Test-ServiceFabricApplicationPackage](https://docs.microsoft.com/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) localmente), processa o conteúdo do pacote e copia o pacote processado em uma localização interna do sistema.

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

O comando [Register-ServiceFabricApplicationType](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) retornará somente depois que o sistema tiver copiado com êxito o pacote de aplicativos. O tempo que se leva para isso depende do conteúdo do pacote de aplicativo. Se necessário, o parâmetro **-TimeoutSec** poderá ser usado para fornecer um tempo limite maior. (O tempo limite padrão é de 60 segundos.)

O comando [Get-ServiceFabricApplicationType](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) lista todas as versões do tipo de aplicativo registradas com êxito.

## <a name="create-the-application"></a>Criar o aplicativo
É possível instanciar um aplicativo usando qualquer versão do tipo de aplicativo registrada com êxito por meio do comando [New-ServiceFabricApplication](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricapplication). O nome de cada aplicativo deve começar com o esquema *fabric:* e ser exclusivo para cada instância do aplicativo. Quaisquer serviços padrão definidos no manifesto do aplicativo do tipo de aplicativo de destino são criados nesse momento.

~~~
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
~~~

O comando [Get-ServiceFabricApplication](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplication) lista todas as instâncias de aplicativos criadas com êxito juntamente com seu status geral.

O comando [Get-ServiceFabricService](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricservice) lista todas as instâncias de serviço criadas com êxito em uma determinada instância do aplicativo. Os serviços padrão (caso haja) são listados aqui.

Várias instâncias do aplicativo podem ser criadas para qualquer determinada versão de um tipo de aplicativo registrado. Cada instância do aplicativo é executada isoladamente, com seu próprio processo e diretório de trabalho.

## <a name="remove-an-application"></a>Remover um aplicativo
Quando uma instância do aplicativo não é mais necessária, você pode removê-la permanentemente usando o comando [Remove-ServiceFabricApplication](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricapplication). Esse comando também remove automaticamente todos os serviços que pertencem ao aplicativo, removendo permanentemente todos os estados de serviço. Essa operação não pode ser revertida e o estado do aplicativo não pode ser recuperado.

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

Quando uma versão específica de um tipo de aplicativo não é mais necessária, você deve cancelar o registro dela usando o comando [Unregister-ServiceFabricApplicationType](https://docs.microsoft.com/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype). O cancelamento de registro de tipos não utilizados libera o espaço de armazenamento usado pelo conteúdo do pacote de aplicativos desse tipo no repositório de imagens. Um tipo de aplicativo pode ter seu registro cancelado, desde que não existam aplicativos instanciados nele e nenhuma atualização de aplicativo pendente que faça referência a ele.

~~~
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
~~~

## <a name="troubleshooting"></a>Solucionar problemas
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage solicita um ImageStoreConnectionString
O ambiente do SDK da Malha do Serviço já deve ter os padrões corretos configurados. Mas, se necessário, o ImageStoreConnectionString para todos os comandos deve corresponder ao valor que o cluster de Malha do Serviço está usando. Você pode encontrá-lo no manifesto do cluster recuperado por meio do comando [Get-ServiceFabricClusterManifest](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricclustermanifest):

~~~
PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType

cmdlet Copy-ServiceFabricApplicationPackage at command pipeline position 1
Supply values for the following parameters:
ImageStoreConnectionString:

PS D:\temp> Get-ServiceFabricClusterManifest
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]

PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType -ImageStoreConnectionString file:D:\ServiceFabric\Data\ImageStore
Copy application package succeeded

PS D:\temp>
~~~

## <a name="next-steps"></a>Próximas etapas
[Atualização de aplicativos do Service Fabric](service-fabric-application-upgrade.md)

[Introdução à integridade da Malha do Serviço](service-fabric-health-introduction.md)

[Diagnosticar e solucionar problemas de um serviço da Malha do Serviço](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelar um aplicativo na Malha do Serviço](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md



<!--HONumber=Nov16_HO3-->


