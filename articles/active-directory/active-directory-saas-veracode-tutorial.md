---
title: "Tutorial: Integração do Azure Active Directory com o Veracode | Microsoft Docs"
description: "Saiba como usar o Veracode com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 731a0fd05b6ed0c49c0c06836e33cd99f7e3bce2


---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Tutorial: Integração do Active Directory do Azure com o Veracode
O objetivo deste tutorial é mostrar a integração do Azure com o Veracode. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura do Veracode habilitada para logon único

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Veracode poderão fazer logon único no aplicativo usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Veracode
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-veracode-tutorial/IC802903.png "Scenario")

## <a name="enabling-the-application-integration-for-veracode"></a>Habilitando a integração de aplicativos para o Veracode
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Veracode.

### <a name="to-enable-the-application-integration-for-veracode-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o Veracode, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-veracode-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-veracode-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-veracode-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-veracode-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **Veracode**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-veracode-tutorial/IC802904.png "Application Gallery")
7. No painel de resultados, selecione **Veracode** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Veracode](./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

## <a name="configuring-single-sign-on"></a>Configurando o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Veracode com sua conta do AD do Azure usando a federação baseada no protocolo SAML.  
Seu aplicativo Veracode espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de **atributos de token SAML** .  
A captura de tela a seguir mostra um exemplo disso.

![Atributos](./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributes")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No Portal Clássico do Azure, na página de integração de aplicativo do **Veracode**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-veracode-tutorial/IC802907.png "Configure Single Sign-On")
2. Na página **Como você deseja que os usuários façam logon no Veracode**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-veracode-tutorial/IC802908.png "Configure Single Sign-On")
3. Na página **Definir Configurações de Aplicativo**, clique em **Próximo**.
   
   ![Definir configurações de aplicativo](./media/active-directory-saas-veracode-tutorial/IC802909.png "Configure App Settings")
4. Na página **Configurar logon único no Veracode**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.
   
   ![Configurar o logon único](./media/active-directory-saas-veracode-tutorial/IC802910.png "Configure Single Sign-On")
5. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Veracode como administrador.
6. No menu na parte superior, clique em **Configurações** e em **Administrador**.
   
   ![Administração](./media/active-directory-saas-veracode-tutorial/IC802911.png "Administration")
7. Clique na guia **SAML** .
8. Na seção **Configurações do SAML da Organização** , realize as seguintes etapas:
   
   ![Administração](./media/active-directory-saas-veracode-tutorial/IC802912.png "Administration")
   
   1. No Portal Clássico do Azure, na página da caixa de diálogo **Configurar logon único no Veracode**, copie o valor de **URL do Emissor** e cole-o na caixa de texto **Emissor**
   2. Para carregar seu certificado baixado, clique em **Escolher Arquivo**.
   3. Selecione **Habilitar Autorregistro**.
9. Na seção **Configurações de Autorregistro**, realize as seguintes etapas e clique em **Salvar**:
   
   ![Administração](./media/active-directory-saas-veracode-tutorial/IC802913.png "Administration")
   
   1. Para **Ativação de Novo Usuário**, selecione **Sem Ativação Necessária**.
   2. Para **Atualizações de Dados do Usuário**, selecione **Dados do Usuário de Preferência do Veracode**.
   3. Para **Detalhes de Atributos do SAML**, selecione o seguinte:
      * **Funções de usuário**
      * **Administrador de políticas**
      * **Revisor**
      * **Orientações de Segurança**
      * **Executivo**
      * **Emissor**
      * **Criador**
      * **Todos os Tipos de Verificação**
      * **Associações de Equipe**
      * **Equipe Padrão**
10. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar Logon Único](./media/active-directory-saas-veracode-tutorial/IC802914.png "Configure Single Sign-On")
11. Na parte superior do menu, clique em **Atributos** to open the **SAML Token Atributos** .
    
    ![Atributos](./media/active-directory-saas-veracode-tutorial/IC795920.png "Attributes")
12. Para adicionar os mapeamentos de atributo necessários, execute as seguintes etapas:
    
    ![Atributos](./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributes")
    
    | Nome do atributo | Valor do atributo |
    |:--- |:--- |
    | nome |User.givenname |
    | sobrenome |User.surname |
    | email |User.mail |
    
    1. Para cada linha de dados na tabela acima, clique em **adicionar atributo do usuário**.
    2. Na caixa de texto **Nome do Atributo** , digite o nome do atributo mostrado para essa linha.
    3. Na caixa de texto **Valor do Atributo** , selecione o valor do atributo mostrado para essa linha.
    4. Clique em **Concluído**.
13. Clique em **Aplicar alterações**.

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários
Para permitir que os usuários do AD do Azure façam logon no Veracode, eles devem ser provisionados no Veracode.  
No caso do Veracode, o provisionamento é uma tarefa automatizada.  
Não há nenhum item de ação para você.

Os usuários são criados automaticamente, se necessário, durante a primeira tentativa de logon único.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Veracode ou APIs fornecidas pelo Veracode para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-veracode-perform-the-following-steps"></a>Para atribuir usuários ao Veracode, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **Veracode**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-veracode-tutorial/IC802915.png "Assign Users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-veracode-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


