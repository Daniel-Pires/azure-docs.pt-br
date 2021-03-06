---
title: Implante seu primeiro aplicativo Web PHP no Azure em cinco minutos | Microsoft Docs
description: "Saiba como é fácil executar aplicativos Web no Serviço de Aplicativo implantando um aplicativo de exemplo. Inicie o desenvolvimento real rapidamente e veja os resultados imediatamente."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/13/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 4fc33ba185122496661f7bc49d14f7522d6ee522
ms.openlocfilehash: 2c31f12bdef63405245f43a48537a02b040248a1


---
# <a name="deploy-your-first-php-web-app-to-azure-in-five-minutes"></a>Implantar seu primeiro aplicativo Web PHP no Azure em cinco minutos
Este tutorial o ajuda a implantar seu primeiro aplicativo Web PHP para o [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md).
Você pode usar o Serviço de Aplicativo para criar aplicativos Web, [back-ends de aplicativos móveis](/documentation/learning-paths/appservice-mobileapps/) e [aplicativos de API](../app-service-api/app-service-api-apps-why-best-platform.md).

Você irá: 

* Criar um aplicativo Web no Serviço de Aplicativo do Azure.
* Implantar o exemplo de código PHP.
* Ver seu código em execução na produção.
* Atualize o aplicativo Web da mesma maneira como faria com [confirmações do Git por push](https://git-scm.com/docs/git-push).

## <a name="prerequisites"></a>Pré-requisitos
* [Git](http://www.git-scm.com/downloads).
* [CLI do Azure](../xplat-cli-install.md).
* Uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Você pode [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751) sem uma conta do Azure. Crie um aplicativo inicial e brinque com ele por até uma hora: não é necessário cartão de crédito ou compromissos.
> 
> 

## <a name="deploy-a-php-web-app"></a>Implantar um aplicativo Web PHP
1. Abra um novo prompt de comando do Windows, janela do PowerShell, shell do Linux ou terminal do OS X. Execute `git --version` e `azure --version` para verificar se o Git e a CLI do Azure estão instalados em seu computador.
   
    ![Testar a instalação das ferramentas da CLI para seu primeiro aplicativo Web no Azure](./media/app-service-web-get-started/1-test-tools.png)
   
    Se ainda não tiver instalado as ferramentas, confira [Pré-requisitos](#Prerequisites) para obter links de download.
2. Faça logon no Azure da seguinte forma:
   
        azure login
   
    Siga a mensagem de ajuda para continuar o processo de logon.
   
    ![Fazer logon no Azure para criar seu primeiro aplicativo Web](./media/app-service-web-get-started/3-azure-login.png)
3. Altere a CLI do Azure para o modo ASM e defina o usuário de implantação para o Serviço de Aplicativo. Mais tarde, você implantará o código usando as credenciais.
   
        azure config mode asm
        azure site deployment user set --username <username> --pass <password>
4. Altere para um diretório de trabalho (`CD`) e clone o aplicativo de exemplo desta forma:
   
        git clone https://github.com/Azure-Samples/app-service-web-php-get-started.git
5. Altere para o repositório do aplicativo de exemplo. Por exemplo:
   
        cd app-service-web-php-get-started
6. Crie o recurso de aplicativo do Serviço de Aplicativo no Azure com um nome exclusivo de aplicativo e o usuário de implantação que você configurou anteriormente. Quando solicitado, especifique o número da região desejada.
   
        azure site create <app_name> --git --gitusername <username>
   
    ![Criar o recurso do Azure para seu primeiro aplicativo Web no Azure](./media/app-service-web-get-started-languages/php-site-create.png)
   
    Seu aplicativo está criado no Azure. Além disso, seu diretório atual é inicializado no Git e conectado ao novo aplicativo do Serviço de Aplicativo como um remoto do Git.
    Você pode navegar até a URL do aplicativo (http://&lt;nome_aplicativo>.azurewebsites.net) para ver a bela página HTML padrão, mas agora vamos incluir seu código lá.
7. Implante o código de exemplo no aplicativo do Azure como você faria com qualquer código do Git. Quando solicitado, use a senha configurada anteriormente.
   
        git push azure master
   
    ![Enviar código por push para seu primeiro aplicativo Web no Azure](./media/app-service-web-get-started-languages/php-git-push.png)
   
    O `git push` não apenas coloca o código no Azure, mas também dispara as tarefas de implantação no mecanismo de implantação. Você também pode  [habilitar a extensão do Composer](web-sites-php-mysql-deploy-use-git.md#composer) para processar automaticamente arquivos composer.json no aplicativo PHP.

Parabéns, você implantou seu aplicativo no Serviço de Aplicativo do Azure.

## <a name="see-your-app-running-live"></a>Ver o aplicativo em execução
Para ver seu aplicativo em execução no Azure, execute este comando em qualquer pasta no repositório:

    azure site browse

## <a name="make-updates-to-your-app"></a>Fazer atualizações no aplicativo
Agora você pode usar o Git para enviar da raiz do projeto (repositório) a qualquer momento e fazer uma atualização no site ativo. Você faz isso da mesma forma que foi feito ao implantar o aplicativo no Azure pela primeira vez. Por exemplo, sempre que você desejar enviar novas alterações que testou localmente, bastará executar os seguintes comandos da raiz do projeto (repositório):

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>Próximas etapas
[Criar, configurar e implantar um aplicativo Web Laravel no Azure](app-service-web-php-get-started.md). Seguindo este tutorial, você aprenderá as habilidades básicas necessárias para executar qualquer aplicativo Web PHP no Azure, tais como:

* Criar e configurar aplicativos no Azure do PowerShell/Bash.
* Definir a versão do PHP.
* Usar um arquivo de inicialização que não está no diretório raiz do aplicativo.
* Habilite a automação do Composer.
* Acessar variáveis de ambiente específicas.
* Solucionar erros comuns.

Ou faça mais com seu primeiro aplicativo Web. Por exemplo:

* Experimente [outras maneiras de implantar seu código no Azure](web-sites-deploy.md). Por exemplo, para implantar de um dos repositórios GitHub, basta selecionar **GitHub** em vez de **Repositório Git Local** nas **Opções de implantação**.
* Leve o aplicativo do Azure para o próximo patamar. Autenticar os usuários. Dimensione-o com base na demanda. Configure alguns alertas de desempenho. Tudo isso com apenas alguns cliques. Confira [Adicionar funcionalidade a seu primeiro aplicativo Web](app-service-web-get-started-2.md).




<!--HONumber=Dec16_HO1-->


