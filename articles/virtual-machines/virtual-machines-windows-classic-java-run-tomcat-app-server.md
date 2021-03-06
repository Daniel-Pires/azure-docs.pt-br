---
title: "Tomcat em uma máquina virtual | Microsoft Docs"
description: "Este tutorial usa os recursos criados com o modelo de implantação clássico e mostra como criar uma Máquina virtual do Windows e configurá-la para executar o servidor de aplicativos do Apache Tomcat."
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: d627aa09-f7d6-4239-8110-f8fc5111b939
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e6fd09bb3a750d8d537abb12a386f46be0c60794


---
# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>Como executar um servidor de aplicativos do Java em uma máquina virtual criada com o modelo de implantação clássico
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Para um modelo do Resource Manager para implantar um aplicativo Web com o Java 8 e o Tomcat, veja [aqui](https://azure.microsoft.com/documentation/templates/201-web-app-java-tomcat/).

Com o Azure, você pode usar uma máquina virtual para fornecer recursos de servidor. Como um exemplo, uma máquina virtual em execução no Azure pode ser configurada para hospedar um servidor de aplicativos Java, como o Apache Tomcat. Depois de concluir este guia, você saberá como criar uma máquina virtual em execução no Azure e configurá-la para executar um servidor de aplicativos Java.

Você aprenderá:

* Como criar uma máquina virtual que já tenha um JDK (Java Development Kit) instalado.
* Entre remotamente na máquina virtual.
* Instalar um servidor de aplicativos Java na máquina virtual.
* Criar um ponto de extremidade para a máquina virtual.
* Abrir uma porta no firewall para o servidor de aplicativos.

Neste tutorial, um servidor de aplicativos Apache Tomcat será instalado em uma máquina virtual. A instalação concluída resultará em uma instalação do Tomcat, como a seguinte.

![Máquina virtual executando o Apache Tomcat][virtual_machine_tomcat]

[!INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Para criar uma máquina virtual
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Clique em **Nova**, clique em **Computação**, clique em **Máquina virtual** e, em seguida, clique em **Da Galeria**.
3. Na caixa de diálogo **Seleção de imagem da máquina virtual**, selecione **JDK 7 Windows Server 2012**.
   É importante lembrar que o **JDK 6 Windows Server 2012** está disponível caso você tenha aplicativos herdados que ainda não estejam prontos para serem executados no JDK 7.
4. Clique em **Próximo**.
5. Na caixa de diálogo **Configuração da máquina virtual** :
   1. Especifique um nome para a máquina virtual.
   2. Especifique o tamanho a ser usado para a máquina virtual.
   3. Digite um nome para o administrador no campo **Nome do Usuário** . Lembre-se do nome e da senha inseridos a seguir, você irá usá-los ao entrar remotamente na máquina virtual.
   4. Digite uma senha no campo **Nova senha** e insira-a novamente no campo **Confirmar**. Esta é a senha da conta do Administrador.
   5. Clique em **Próximo**.
6. Na próxima caixa de diálogo **Configuração da máquina virtual** :
   1. Para **Serviço de Nuvem**, use o padrão **Criar um novo serviço de nuvem**.
   2. O valor de **Nome DNS do Serviço de Nuvem** deve ser exclusivo no cloudapp.net. Se necessário, modifique esse valor para que o Azure indique que ele é exclusivo.
   3. Especifique uma região, um grupo de afinidade ou uma rede virtual. Neste tutorial, especifique uma região, como **Oeste dos Estados Unidos**.
   4. Para **Conta de Armazenamento**, selecione **Usar uma conta de armazenamento gerada automaticamente**.
   5. Para **Conjunto de Disponibilidade**, selecione **(Nenhum)**.
   6. Clique em **Próximo**.
7. Na caixa de diálogo **Configuração da máquina virtual** final:
   1. Aceite as entradas de ponto de extremidade padrão.
   2. Clique em **Concluído**.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>Para entrar remotamente na máquina virtual
1. Faça logon no [portal clássico do Azure](https://manage.windowsazure.com).
2. Clique em **Máquinas Virtuais**.
3. Clique no nome da Máquina Virtual na qual você deseja entrar.
4. Depois que a máquina virtual for iniciada, um menu pop-up aparecerá na parte inferior da página para permitir as conexões.
5. Clique em **Conectar**.
6. Responda às solicitações conforme necessário para se conectar à máquina virtual. Isso deve incluir salvar ou abrir o arquivo .rdp que contém os detalhes de conexão. Pode ser necessário copiar url:port como último parte da primeira linha do arquivo .rdp e colar essa informações em um aplicativo de entrada remoto.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>Para instalar um servidor de aplicativos Java em sua máquina virtual
Você pode copiar um servidor de aplicativos Java em sua máquina virtual ou instalar um servidor de aplicativos Java por meio de um instalador.

Para o objetivo deste tutorial, o Tomcat será instalado.

1. Depois de se conectar à máquina virtual, abra uma sessão do navegador para [Apache Tomcat](http://tomcat.apache.org/download-70.cgi).
2. Clique duas vezes no link **Instalador de serviço do Windows de 32 bits/64 bits**. Usando essa técnica, o Tomcat será instalado como um serviço do Windows.
3. Quando solicitado, opte por executar o instalador.
4. No assistente de **Configuração do Apache Tomcat** , siga os prompts para instalar o Tomcat. Para o objetivo deste tutorial, aceitar os padrões será o suficiente. Quando você chegar à caixa de diálogo **Concluindo o Assistente de Instalação do Apache Tomcat**, poderá marcar opcionalmente **Executar o Apache Tomcat** para que o Tomcat inicie agora. Clique em **Concluir** para concluir o processo de configuração do Tomcat.

## <a name="to-start-tomcat"></a>Para iniciar o Tomcat
Se você não optou por executar o Tomcat na caixa de diálogo **Concluindo o Assistente de Instalação do Apache Tomcat**, inicie-o abrindo um prompt de comando em sua máquina virtual e executando **net start Tomcat7**.

Agora você deverá ver o Tomcat em execução se executar o navegador da máquina virtual e abrir <http://localhost:8080>.

Para ver o Tomcat em execução em máquinas externas, você precisará criar um ponto de extremidade e abrir uma porta.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>Para criar um ponto de extremidade para sua máquina virtual
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Clique em **Máquinas Virtuais**.
3. Clique no nome da máquina virtual que está executando o servidor de aplicativos Java.
4. Clique em **Pontos de Extremidade**.
5. Clique em **Adicionar**.
6. Na caixa de diálogo **Adicionar ponto de extremidade**, verifique se a opção **Adicionar ponto de extremidade autônomo** está marcada e clique em **Próximo**.
7. Na caixa de diálogo **Detalhes do novo ponto de extremidade** :
   1. Especifique um nome para o ponto de extremidade. Por exemplo, **HttpIn**.
   2. Especifique **TCP** para o protocolo.
   3. Especifique **80** para a porta pública.
   4. Especifique **8080** para a porta privada.
   5. Clique no botão **Concluído** para fechar a caixa de diálogo. O ponto de extremidade será criado.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>Para abrir uma porta no firewall para sua máquina virtual
1. Entre na máquina virtual.
2. Clique em **Iniciar do Windows**.
3. Clique em **Painel de Controle**.
4. Clique em **Sistema e Segurança**, clique em **Firewall do Windows** e, em seguida, clique em **Configurações Avançadas**.
5. Clique em **Regras de Entrada** e, em seguida, clique em **Nova Regra**.
   ![Nova regra de entrada][NewIBRule]
6. Para o **Tipo de Regra**, selecione **Porta** e clique em **Próximo**.
   ![Nova porta de regra de entrada][NewRulePort]
7. Na tela **Protocolo e Portas**, selecione **TCP**, especifique **8080** como a **Porta local específica** e clique em **Próximo**.
   ![Nova regra de entrada][NewRuleProtocol]
8. Na tela **Ação**, selecione **Permitir a conexão** e clique em **Próximo**.
   ![Nova ação de regra de entrada][NewRuleAction]
9. Na tela **Perfil**, verifique se **Domínio**, **Privado** e **Público** estão marcados e clique em **Próximo**.
   ![Novo perfil de regra de entrada][NewRuleProfile]
10. Na tela **Nome**, especifique um nome para a regra, como **HttpIn** (no entanto, o nome da regra não precisa corresponder ao nome do ponto de extremidade) e clique em **Concluir**.  
    ![Nome da nova regra de entrada][NewRuleName]

Neste ponto, o site do Tomcat deve ser visível de um navegador externo usando uma URL no formato **http://*your\_DNS\_name*.cloudapp.net**, em que ***your\_DNS\_name*** é o nome DNS que você especificou ao criar a máquina virtual.

## <a name="application-lifecycle-considerations"></a>Considerações sobre o ciclo de vida do aplicativo
* Você pode criar o próprio arquivo web do aplicativo (WAR) e adicioná-lo à pasta **webapps** . Por exemplo, crie um projeto Web dinâmico JSP (página de serviço Java) básico e o exporte como um arquivo WAR, copie o WAR para a pasta **webapps** do Apache Tomcat na máquina virtual e o execute em um navegador.
* Por padrão, quando o serviço Tomcat for instalado, ele será definido para iniciar manualmente. Você pode mudá-lo para iniciar automaticamente, usando o snap-in Serviços. Inicie o snap-in Serviços clicando em **Iniciar do Windows**, **Ferramentas Administrativas** e em **Serviços**. Clique duas vezes no serviço **Apache Tomcat** e defina o **Tipo de inicialização** como **Automático**.
  
    ![Configurando um serviço para iniciar automaticamente][service_automatic_startup]
  
    O benefício de fazer o Tomcat ser iniciado automaticamente é que ele será iniciado se a máquina virtual for reinicializada (por exemplo, depois que atualizações de software que exijam uma reinicialização forem instaladas).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre outros serviços (como o Armazenamento do Azure, o barramento de serviço, o Banco de Dados SQL) que você pode desejar incluir com seus aplicativos do Java, conferindo as informações disponíveis no [Centro de Desenvolvedores do Java](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProfile.png



<!--HONumber=Nov16_HO3-->


