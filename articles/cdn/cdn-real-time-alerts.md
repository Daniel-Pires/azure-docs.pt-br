---
title: Alertas em tempo real do Azure CDN | Microsoft Docs
description: "Alertas em tempo real na CDN do Microsoft Azure. Alertas em tempo real fornecem notificações sobre o desempenho dos pontos de extremidade em seu perfil da CDN."
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2a6bd8982bb89c1829a6a5945f6ff9d8ed7009f0


---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Alertas em tempo real na CDN do Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Visão geral
Este documento explica os alertas em tempo real na CDN do Microsoft Azure. Essa funcionalidade fornece notificações em tempo real sobre o desempenho dos pontos de extremidade em seu perfil da CDN.  Você pode configurar alertas por email ou HTTP com base em:

* Largura de banda
* Códigos de status
* Status do Cache
* Conexões

## <a name="creating-a-real-time-alert"></a>Criar um alerta em tempo real
1. No [Portal do Azure](https://portal.azure.com), navegue para seu perfil CDN.
   
    ![Folha Perfil CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)
2. Na folha do perfil CDN, clique no botão **Gerenciar** .
   
    ![botão gerenciar da folha Perfil CDN](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    O portal de gerenciamento da CDN é aberto.
3. Focalize a guia **Análise** e, em seguida, o submenu **Estatísticas em Tempo Real**.  Clique em **Alertas em Tempo Real**.
   
    ![Portal de gerenciamento da CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    A lista de configurações de alerta existentes (se houver) é exibida.
4. Clique no botão **Adicionar Alerta** .
   
    ![Botão Adicionar Alerta](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Um formulário para criar um novo alerta é exibido.
   
    ![Formulário Novo Alerta](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Se você quiser que esse alerta esteja ativo quando clicar em **Salvar**, marque a caixa de seleção **Alerta Habilitado**.
6. Insira um nome descritivo para o alerta no campo **Nome** .
7. Na lista suspensa **Tipo de Mídia**, selecione **Objeto Grande HTTP**.
   
    ![Tipo de Mídia com Objeto Grande HTTP selecionado](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Você deve selecionar o **Objeto Grande HTTP** como o **Tipo de Mídia**.  As outras opções não são usadas pela **CDN do Azure da Verizon**.  Se **Objeto Grande HTTP** não for selecionado, isso fará com que o alerta nunca seja disparado.
   > 
   > 
8. Crie uma **Expressão** para monitorar, selecionando uma **Métrica**, um **Operador** e um **Valor de gatilho**.
   
   * Para **Métrica**, selecione o tipo de condição que você deseja monitorar.  **Mbps de largura de banda** é a quantidade de uso de largura de banda em megabits por segundo.  **Total de Conexões** é o número de conexões HTTP simultâneas para os servidores de borda.  Para obter definições dos vários códigos de status e dos status de cache, confira [Azure CDN Cache Status Codes](https://msdn.microsoft.com/library/mt759237.aspx) (Códigos de Status de Cache da CDN do Azure) e [Azure CDN HTTP Status Codes](https://msdn.microsoft.com/library/mt759238.aspx) (Códigos de Status de HTTP da CDN do Azure)
   * **Operador** é o operador matemático que estabelece a relação entre a métrica e o valor do gatilho.
   * **Valor de Disparador** é o valor de limite que deve ser atingido antes que uma notificação seja enviada.
     
     No exemplo abaixo, a expressão que criei indica que desejo ser notificado quando o número de códigos de status 404 for maior que 25.
     
     ![Expressão de exemplo de alerta em tempo real](./media/cdn-real-time-alerts/cdn-expression.png)
9. Para **Intervalo**, digite a frequência com que você quer que a expressão seja avaliada.
10. Na lista suspensa **Notificar sobre** , selecione quando deseja ser notificado quando a expressão for verdadeira.
    
    * **Início da Condição** indica que uma notificação será enviada quando a condição especificada for detectada pela primeira vez.
    * **Condição Final** indica que uma notificação será enviada quando a condição especificada não for mais detectada. Essa notificação só pode ser disparada após nosso sistema de monitoramento de rede detectar que ocorreu a condição especificada.
    * **Contínuo** indica que uma notificação será enviada sempre que o sistema de monitoramento de rede detectar a condição especificada. Lembre-se de que o sistema de monitoramento de rede verifica a condição especificada somente uma vez por intervalo .
    * **Início e Fim de Condição** indica que uma notificação será enviada na primeira vez em que a condição especificada for detectada e novamente quando a condição não for mais detectada.
11. Se você desejar receber notificações por email, marque a caixa de seleção **Notificar por Email** .  
    
    ![Formulário Notificar por Email](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    No campo **Para** , insira o endereço de email para o qual deseja que as notificações sejam enviadas. Para **Assunto** e **Corpo**, você pode manter o padrão ou personalizar a mensagem usando a lista **Palavras-chave disponíveis** para inserir dados de alerta dinamicamente quando a mensagem for enviada.
    
    > [!NOTE]
    > Você pode testar a notificação por email clicando no botão **Notificação de Teste** , mas somente depois que a configuração de alertas for salva.
    > 
    > 
12. Se você quiser que as notificações sejam postadas em um servidor Web, marque a caixa de seleção **Notificar por HTTP Post** .
    
    ![Formulário Notificar por HTTP Post](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    No campo **Url** , digite a URL em que você deseja que a mensagem HTTP seja postada. Na caixa de texto **Cabeçalhos** , insira os cabeçalhos HTTP a serem enviados na solicitação.  Para **Corpo**, você pode personalizar a mensagem usando a lista de **Palavras-chave disponíveis** para inserir dados de alerta dinamicamente quando a mensagem for enviada.  **Cabeçalhos** e **Corpo** usam como padrão um conteúdo de XML semelhante ao exemplo abaixo.
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Você pode testar a notificação de HTTP Post clicando no botão **Notificação de Teste** , mas somente depois que a configuração de alertas for salva.
    > 
    > 
13. Clique no botão **Salvar** para salvar sua configuração de alerta.  Se você tiver selecionado **Alerta Habilitado** na etapa 5, o alerta estará ativo agora.

## <a name="next-steps"></a>Próximas etapas
* Analisar [estatísticas em tempo real na CDN do Azure](cdn-real-time-stats.md)
* Saiba mais com os [relatórios HTTP avançados](cdn-advanced-http-reports.md)
* Analisar os [padrões de uso](cdn-analyze-usage-patterns.md)




<!--HONumber=Nov16_HO3-->


