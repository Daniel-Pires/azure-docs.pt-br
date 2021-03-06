---
title: "Introdução ao fornecimento de conteúdo sob demanda usando o .NET | Microsoft Docs"
description: "Este tutorial orienta você pelas etapas de implementação de um aplicativo de fornecimento de conteúdo sob demanda com os Serviços de Mídia do Azure usando .NET."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/15/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: e048e70714c260fcb13ec5ca53434173026eb8d8
ms.openlocfilehash: 623841606367a319eadf268c8938066d98aa491d


---

# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Introdução ao fornecimento de conteúdo sob demanda usando o SDK do .NET
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). 
> 
> 

## <a name="overview"></a>Visão geral
Este tutorial orienta você pelas etapas de implementação de um aplicativo de entrega de conteúdo de vídeo sob demanda (VoD) com os Serviços de Mídia do Azure (AMS) para .NET.

O tutorial apresenta o fluxo de trabalho básico dos Serviços de Mídia e os objetos e as tarefas de programação mais comuns necessárias para o desenvolvimento dos Serviços de Mídia do Microsoft Azure. No final do tutorial, você poderá transmitir ou baixar progressivamente um arquivo de mídia de exemplo que você carregou, codificou e baixou.

### <a name="ams-model"></a>Modelo do AMS

A imagem a seguir mostra alguns dos objetos mais usados ao desenvolver aplicativos VoD em relação ao modelo de OData de Serviços de Mídia. 

Clique na imagem para exibi-la em tamanho normal.  

<a href="./media/media-services-dotnet-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-dotnet-get-started/media-services-overview-object-model-small.png"></a> 

Você pode exibir todo o modelo [aqui](https://media.windows.net/API/$metadata?api-version=2.14).  

## <a name="what-youll-learn"></a>O que você aprenderá

O tutorial mostra como concluir as seguintes tarefas:

1. Criar uma conta de Serviços de Mídia (usando o portal do Azure).
2. Configurar o ponto de extremidade de streaming (usando o portal do Azure).
3. Criar e configurar um projeto do Visual Studio.
4. Conectar-se à conta dos Serviços de Mídia.
5. Criar um novo ativo e carregar um arquivo de vídeo.
6. Codificar o arquivo de origem em um conjunto de arquivos MP4 com taxa de bits adaptável.
7. Publicar o ativo e obter URLs para streaming e download progressivo.
8. Testar ao reproduzir o conteúdo.

## <a name="prerequisites"></a>Pré-requisitos
Os itens a seguir são necessários para concluir o tutorial.

* Para concluir este tutorial, você precisa de uma conta do Azure. 
  
    Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Obtenha créditos que possam ser usados para experimentar os serviços pagos do Azure. Mesmo depois que os créditos são usados, você pode manter a conta e usar os serviços e recursos do Azure gratuitos, como o recurso de aplicativos Web do Serviço de Aplicativo do Azure.
* Sistemas operacionais: Windows 8 ou posterior, Windows 2008 R2, Windows 7.
* .NET Framework 4.0 ou posterior
* Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express) ou versões posteriores.

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Criar uma conta dos Serviços de Mídia do Azure usando o portal do Azure
As etapas nesta seção mostram como criar uma conta do AMS.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Clique em **+Novo** > **Mídia + CDN** > **Serviços de Mídia**.
   
    ![Criar Serviços de Mídia](./media/media-services-portal-vod-get-started/media-services-new1.png)
3. Em **CRIAR CONTA DOS SERVIÇOS DE MÍDIA** , insira os valores necessários.
   
    ![Criar Serviços de Mídia](./media/media-services-portal-vod-get-started/media-services-new3.png)
   
   1. Em **Nome da Conta**, insira o nome da nova conta AMS. Um nome de conta de Serviços de Mídia deve ser composto de letras minúsculas ou números, sem espaços, e deve ter de 3 a 24 caracteres de comprimento.
   2. Em Assinatura, selecione uma das diferentes assinaturas do Azure às quais você tem acesso.
   3. Em **Grupo de Recursos**, selecione o recurso novo ou existente.  Um grupo de recursos é uma coleção de recursos que compartilham o ciclo de vida, as permissões e as políticas. Saiba mais [aqui](../azure-resource-manager/resource-group-overview.md#resource-groups).
   4. Em **Localização**, selecione a região geográfica que é usada para armazenar os registros de mídia e metadados para sua conta de Serviços de Mídia. Essa região é usada para processar e transmitir a mídia. Somente as regiões de Serviços de Mídia disponíveis são exibidas na caixa da lista suspensa. 
   5. Em **Conta de Armazenamento**, selecione uma conta de armazenamento para fornecer armazenamento de blobs do conteúdo de mídia de sua conta de Serviços de Mídia. Você pode selecionar uma conta de armazenamento existente na mesma região geográfica que sua conta de Serviços de Mídia ou criar uma conta de armazenamento. É criada uma nova conta de armazenamento na mesma região. As regras para nomes de contas de armazenamento são as mesmas que para contas de Serviços de Mídia.
      
       Saiba mais sobre o armazenamento [aqui](../storage/storage-introduction.md).
   6. Selecione **Fixar no painel** para ver o progresso da implantação da conta.
4. Clique em **Criar** na parte inferior do formulário.
   
    Quando a conta for criada com êxito, o status mudará para **Executando**. 
   
    ![Configurações dos Serviços de Mídia](./media/media-services-portal-vod-get-started/media-services-settings.png)
   
    Para gerenciar sua conta AMS (por exemplo, carregar vídeos, codificar ativos, monitorar o andamento do trabalho), use a janela **Configurações** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Configurar pontos de extremidade de streaming usando o portal do Azure
Ao trabalhar com os Serviços de Mídia do Azure, um dos cenários mais comuns é fornecer o vídeo via streaming de taxa de bits adaptável para seus clientes. Os Serviços de Mídia do Azure permitem as seguintes tecnologias de streaming de taxa de bits adaptável: HLS (HTTP Live Streaming), Smooth Streaming e MPEG DASH.

Os Serviços de Mídia fornecem um empacotamento dinâmico que permite a você enviar o conteúdo codificado para MP4 da taxa de bits adaptável nos formatos de transmissão suportados pelos Serviços de Mídia (MPEG DASH, HLS, Smooth Streaming) just-in-time, sem ter que armazenar as versões recolocadas de cada um dos formatos de transmissão.

Para aproveitar os benefícios do empacotamento dinâmico, você precisa fazer o seguinte:

* Codifique seu arquivo mezanino (fonte) em um conjunto de arquivos MP4 da taxa de bits adaptável (as etapas de codificação serão demonstradas mais tarde neste tutorial).  
* Crie pelo menos uma unidade de transmissão para o *ponto de extremidade de streaming* a partir da qual você planeja fornecer seu conteúdo. As etapas a seguir mostram como alterar o número de unidades da transmissão.

Com o empacotamento dinâmico, você só precisa armazenar e pagar pelos arquivos em um único formato de armazenamento, e os Serviços de Mídia compilam e fornecem a resposta adequada com base nas solicitações de um cliente.

Para criar e alterar o número de unidades reservadas de transmissão, faça o seguinte:

1. Na janela **Configurações**, clique em **Pontos de extremidade de streaming**. 
2. Clique no ponto de extremidade de streaming padrão. 
   
    A janela **DETALHES DO PONTO DE EXTREMIDADE DE STREAMING PADRÃO** é exibida.
3. Para especificar o número de unidades de transmissão, deslize o controle **Unidades de transmissão** .
   
    ![Unidades de transmissão](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)
4. Clique no botão **Salvar** para salvar as alterações.
   
   > [!NOTE]
   > A alocação de quaisquer novas unidades leva cerca de 20 minutos para ser concluída.
   > 
   > 

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto do Visual Studio

1. Crie um novo Aplicativo de Console C# no Visual Studio 2013, no Visual Studio 2012 ou no Visual Studio 2010 SP1. Digite o **Nome**, o **Local** e o **Nome da solução** e clique em **OK**.
2. Use o pacote NuGet [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) para instalar **extensões do SDK .NET dos Serviços de Mídia do Azure**.  As Extensões do SDK do .NET dos Serviços de Mídia do Azure são um conjunto de métodos de extensão e funções auxiliares que simplificarão seu código e tornarão mais fácil desenvolver com os Serviços de Mídia. Instalar esse pacote também instala o **SDK do .NET dos Serviços de Mídia** e adiciona todas as outras dependências necessárias.

    Para adicionar referências usando o NuGet, faça o seguinte: no Gerenciador de Soluções, clique no botão direito do mouse no nome do projeto, selecione **Gerenciar pacotes NuGet**. Em seguida, pesquise por **windowsazure.mediaservices.extensions** e clique em **Instalar**.

3. Adicione uma referência ao assembly do System.Configuration. Esse assembly contém a classe **System.Configuration.ConfigurationManager** que é utilizada para acessar arquivos de configuração, como App.config.

    Para adicionar uma referência, faça o seguinte: no Gerenciador de Soluções, clique com o botão direito do mouse no nome do projeto, selecione **Adicionar** > **Referência...** e digite a configurações na caixa de pesquisa. 

4. Abra o arquivo App.config (adicione o arquivo ao seu projeto se ele não foi adicionado por padrão) e adicione uma seção *appSettings* ao arquivo. Defina os valores para o nome e chave de conta de seus Serviços de Mídia do Azure, conforme mostrado no exemplo a seguir. Para obter as informações de chave e nome da conta, vá para o [Portal do Azure](https://portal.azure.com/) e selecione sua conta do AMS. Em seguida, selecione **Configurações** > **Chaves**. A janela Gerenciar chaves mostra o nome da conta e as chaves primária e secundária são exibidas. Copie os valores do nome da conta e a chave primária.
   
        <configuration>
        ...
          <appSettings>
            <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
            <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
          </appSettings>
   
        </configuration>
5. Substitua as instruções **using** existentes no início do arquivo Program.cs pelo código a seguir.
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
6. Crie uma nova pasta (a pasta pode estar em qualquer lugar na unidade local) e copie um arquivo .mp4 que você deseja codificar e transmitir ou baixar progressivamente. Este exemplo usa o caminho "C:\VideoFiles".

## <a name="connect-to-the-media-services-account"></a>Conectar-se à conta dos Serviços de Mídia

Ao usar os serviços de mídia com o .NET, você deve usar a classe **CloudMediaContext** para a maioria das tarefas de programação dos Serviços de Mídia: conectar-se à conta de Serviços de Mídia; criar, atualizar, acessar e excluir os seguintes objetos: ativos, arquivos de ativos, trabalhos, políticas de acesso, localizadores, etc.

Substitua a classe Program padrão pelo código a seguir. O código demonstra como ler os valores de conexão por meio do arquivo App.config e como criar o objeto **CloudMediaContext** para poder se conectar aos Serviços de Mídia. Para saber mais sobre como conectar-se aos Serviços de Mídia, veja [Conectando-se aos Serviços de Mídia com o SDK dos Serviços de Mídia para .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

Atualize o nome do arquivo e o caminho onde está o arquivo de mídia.

A função **Main** chama métodos que serão definidos posteriormente nesta seção.

> [!NOTE]
> Você receberá erros de compilação até que adicione definições a todas as funções.

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.
        // Make sure to update the file name and path to where you have your media file.
                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }
    }

## <a name="create-a-new-asset-and-upload-a-video-file"></a>Criar um novo ativo e carregar um arquivo de vídeo

No Serviços de Mídia, você carrega (ou ingere) seus arquivos digitais em um ativo. A entidade **Asset** pode conter vídeo, áudio, imagens, coleções de miniaturas, sequências de texto e arquivos de legendas (e os metadados sobre esses arquivos).  Depois que os arquivos são carregados, o conteúdo é armazenado com segurança na nuvem para processamento adicional e transmissão. Os arquivos no ativo são chamados **Arquivos de Ativo**.

O método **UploadFile** definido abaixo chama **CreateFromFile** (definido em extensões do SDK .NET). **CreateFromFile** cria um novo ativo no qual o arquivo de origem especificado é carregado.

O método **CreateFromFile** contém **AssetCreationOptions**, que permite especificar uma das seguintes opções de criação de ativos:

* **None** - nenhuma criptografia é usada. Esse é o valor padrão. Observe que, ao usar essa opção, seu conteúdo não será protegido quando estiver em trânsito ou em repouso no armazenamento.
  Se você pretende enviar um MP4 usando o download progressivo, use essa opção.
* **StorageEncrypted** – use essa opção para criptografar seu conteúdo limpo localmente usando a criptografia AES de 256 bits e, em seguida, carregá-lo para o armazenamento do Azure, onde ele é armazenado, criptografado em repouso. Ativos protegidos pela criptografia de armazenamento são descriptografados automaticamente e posicionados em um sistema de arquivos criptografado antes da codificação, então opcionalmente criptografados novamente antes do carregamento como um novo ativo de saída. O caso de uso primário para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada de alta qualidade com criptografia forte em repouso no disco.
* **CommonEncryptionProtected** — use esta opção se você estiver carregando conteúdo que já foi criptografado e protegido com criptografia comum ou DRM PlayReady (por exemplo, Smooth Streaming protegido com DRM PlayReady).
* **EnvelopeEncryptionProtected** – use esta opção se você estiver carregando HLS criptografado com AES. Observe que os arquivos devem ter sido codificados e criptografados pelo Gerenciador de Transformação.

O método **CreateFromFile** também permite especificar um retorno de chamada para relatar o progresso do upload do arquivo.

No exemplo a seguir, podemos especificar **Nenhum** para as opções de ativo.

Adicionar o método a seguir à classe do programa.

    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }


## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Codificar o arquivo de origem em um conjunto de arquivos MP4 com taxa de bits adaptável
Após a inserção de Ativos nos Serviços de Mídia, a mídia poderá ser codificada, transmultiplexada, marcada com marca d'água e assim por diante antes que seja entregue aos clientes. Essas atividades são agendadas e executadas em contraste com várias instâncias de função de plano de fundo para garantir a disponibilidade e desempenho elevados. Essas atividades são chamadas de Trabalhos, e cada Trabalho é composto por Tarefas atômicas, que fazem o trabalho real no arquivo do Ativo.

Como mencionado anteriormente, ao trabalhar com os Serviços de Mídia do Azure, um dos cenários mais comuns é fornecer streaming com uma taxa de bits adaptável aos clientes. Os serviços de mídia podem empacotar dinamicamente um conjunto de arquivos MP4 com taxas de bit adaptável: HTTP Live Streaming (HLS), Smooth Streaming e MPEG DASH.

Para aproveitar os benefícios do empacotamento dinâmico, você precisa fazer o seguinte:

* Codificar ou transcodificar seu arquivo mezanino (fonte) em um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos Smooth Streaming de taxa de bits adaptável.  
* Obter pelo menos uma unidade de streaming para o ponto de extremidade de streaming do qual você planeja fornecer seu conteúdo.

O código a seguir mostra como enviar um trabalho de codificação. O trabalho contém uma tarefa que determina a transcodificação do arquivo de mezanino em um conjunto de MP4s de taxa de bits adaptável usando o **Codificador de Mídia Standard**. O código envia o trabalho e aguarda até que ele seja concluído.

Depois que o trabalho de codificação for concluído, você poderá publicar seus ativos e transmitir ou baixar progressivamente os arquivos MP4.
 
Adicionar o método a seguir à classe do programa.

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "H264 Multiple Bitrate 720p",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Publicar o ativo e obter URLs para streaming e download progressivo

Para transmitir ou baixar um ativo, primeiro você precisa "publicá-lo" criando um localizador. Os localizadores fornecem acesso aos arquivos contidos no ativo. Os Serviços de Mídia oferecem suporte a dois tipos de localizador: OnDemandOrigin, usados para transmitir mídia por streaming (por exemplo, MPEG DASH, HLS ou Smooth Streaming) e SAS (Assinatura de Acesso), usados para baixar arquivos de mídia (Para saber mais sobre localizadores SAS, confira [este](http://southworks.com/blog/2015/05/27/reusing-azure-media-services-locators-to-avoid-facing-the-5-shared-access-policy-limitation/) blog).

### <a name="some-details-about-url-formats"></a>Alguns detalhes sobre os formatos de URL

Depois de criar os localizadores, você pode criar as URLs que seriam usadas para transmitir ou baixar os arquivos. O exemplo neste tutorial produzirá URLs que podem ser coladas em navegadores apropriados. Esta seção fornece apenas breves exemplos da aparência de formatos diferentes. 

#### <a name="a-streaming-url-for-mpeg-dash-has-the-following-format"></a>Uma URL de streaming para MPEG DASH tem o seguinte formato:

{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest**(format=mpd-time-csf)**

#### <a name="a-streaming-url-for-hls-has-the-following-format"></a>Uma URL de streaming para HLS tem o seguinte formato:

{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest**(format=m3u8-aapl)**

#### <a name="a-streaming-url-for-smooth-streaming-has-the-following-format"></a>Uma URL de streaming para Smooth Streaming tem o seguinte formato:

{nome do ponto de extremidade de streaming - nome de conta do dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arqui}.ism/Manifest


Uma URL SAS usada para baixar arquivos tem o seguinte formato:

{nome do contêiner de blob}/{nome do ativo}/{nome do arquivo}/{assinatura SAS}

Extensões do SDK do .NET dos Serviços de Mídia fornecem métodos auxiliares práticos, que retornam URLs formatadas para o ativo publicado.

O código a seguir usa extensões do SDK .NET para criar os localizadores e obter streaming e URLs de download progressivo. O código também mostra como baixar os arquivos em uma pasta local.

Adicionar o método a seguir à classe do programa.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

## <a name="test-by-playing-your-content"></a>Testar ao reproduzir o conteúdo

Depois que você executar o programa definido na seção anterior, as URLs semelhantes à seguinte serão exibidas na janela do console.

URLs de streaming adaptáveis:

Smooth Streaming

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

URLs de download progressivo (áudio e vídeo).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Para transmitir o vídeo, cole a URL na caixa de texto de URL no [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Para testar o download progressivo, cole uma URL em um navegador (por exemplo, Internet Explorer, Chrome ou Safari).

Para saber mais, consulte os tópicos a seguir:

- [Reprodução de seu conteúdo com players existentes](media-services-playback-content-with-existing-players.md)
- [Desenvolver aplicativos de player de vídeo](media-services-develop-video-players.md)
- [Inserindo um vídeo de streaming adaptável MPEG-DASH em um aplicativo HTML5 com DASH.js](media-services-embed-mpeg-dash-in-html5.md)

## <a name="download-sample"></a>Baixar exemplo
O exemplo de código a seguir contém o código que você criou neste tutorial: [exemplo](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="next-steps-media-services-learning-paths"></a>Próximas etapas: roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="looking-for-something-else"></a>Procurando outra coisa?
Se este tópico não contiver o que você esperava, se estiver faltando alguma informação ou se não tiver atendido de alguma outra forma às suas necessidades, envie seus comentários usando o thread Disqus abaixo.

<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
[Portal]: http://manage.windowsazure.com/



<!--HONumber=Dec16_HO3-->


