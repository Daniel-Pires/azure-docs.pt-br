---
title: "Tutorial: Integração do Azure Active Directory ao SumoLogic | Microsoft Docs"
description: "Saiba como usar o SumoLogic com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 084676f695165f6015b35ddc4bc8c4a6bc3d189e


---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Tutorial: Integração do Active Directory do Azure com o SumoLogic
O objetivo deste tutorial é mostrar a integração do Azure com o SumoLogic.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do SumoLogic

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao SumoLogic poderão fazer logon único no aplicativo em seu site de empresa do SumoLogic (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o SumoLogic
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-sumologic-tutorial/IC778549.png "Scenario")

## <a name="enabling-the-application-integration-for-sumologic"></a>Habilitando a integração de aplicativos para o SumoLogic
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o SumoLogic.

### <a name="to-enable-the-application-integration-for-sumologic-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o SumoLogic, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-sumologic-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-sumologic-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-sumologic-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-sumologic-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **sumologic**.
   
   ![Galeria de aplicativos](./media/active-directory-saas-sumologic-tutorial/IC778550.png "Application gallery")
7. No painel de resultados, selecione **SumoLogic** e clique em **Concluir** para adicionar o aplicativo.
   
   ![SumoLogic](./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

## <a name="configuring-single-sign-on"></a>Configurando o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no SumoLogic com sua conta do AD do Azure usando federação baseada em protocolo SAML.  
Como parte desse procedimento, é necessário carregar um certificado codificado em base 64 no locatário do SumoLogic.  
Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No Portal clássico do Azure, na página de integração do aplicativo **SumoLogic**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-sumologic-tutorial/IC778552.png "Configure single sign-on")
2. Na página **Como você deseja que os usuários façam logon no SumoLogic**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-sumologic-tutorial/IC778553.png "Configure single sign-on")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do SumoLogic**, digite a URL usando o padrão "*https://\<tenant-name\>.SumoLogic.com*" e clique em **Avançar**.
   
   ![Configurar URL aoo](./media/active-directory-saas-sumologic-tutorial/IC778554.png "Configure aoo URL")
4. Na página **Configurar logon único no SumoLogic**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar o logon único](./media/active-directory-saas-sumologic-tutorial/IC778555.png "Configure single sign-on")
5. Em outra janela do navegador da Web, faça logon em seu site de empresa SumoLogic como um administrador.
6. Vá para **Gerenciar \> Segurança**.
   
   ![Gerenciar](./media/active-directory-saas-sumologic-tutorial/IC778556.png "Manage")
7. Clique em **SAML**.
   
   ![Configurações de globais de segurança](./media/active-directory-saas-sumologic-tutorial/IC778557.png "Global security settings")
8. Na lista **Selecionar uma configuração ou criar uma nova**, selecione **Azure AD** e clique em **Configurar**.
   
   ![Configurar SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778558.png "Configure SAML 2.0")
9. No diálogo **Configurar SAML 2.0** , realize as seguintes etapas:
   
   ![Configurar SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778559.png "Configure SAML 2.0")
   
   1. Na caixa de texto **Nome da Configuração**, digite **Azure AD**.
   2. Selecione **Modo de Depuração**.
   3. No Portal clássico do Azure, na página de diálogo **Configurar logon único no SumoLogic**, copie o valor da **URL do Emissor** e cole-o na caixa de texto **Emissor**.
   4. No Portal clássico do Azure, na página de diálogo **Configurar logon único no SumoLogic**, copie o valor da **URL da Solicitação de Autenticação** e cole-o na caixa de texto **URL da Solicitação de Autenticação**.
   5. Crie um arquivo **codificado em base 64** usando o certificado baixado.  
      
      > [!TIP]
      > Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   6. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole todo o Certificado na caixa de texto **Certificado X.509** .
   7. Para **Atributo de Email**, selecione **Usar entidade do SAML**.
   8. Selecione **Configuração de Logon iniciada pelo SP**.
   9. Na caixa de texto **Caminho de Logon**, digite **Azure**.
   10. Clique em **Salvar**.
10. No Portal clássico do Azure, na página **Configurar logon único no SumoLogic**, selecione a confirmação de configuração de logon único e clique em **Concluir**.
    
    ![Configurar o logon único](./media/active-directory-saas-sumologic-tutorial/IC778560.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários
Para permitir que os usuários do AD do Azure façam logon no SumoLogic, eles deverão ser provisionados no SumoLogic.  
No caso do SumoLogic, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon em seu locatário do **SumoLogic** .
2. Vá para **Gerenciar \> Usuários**.
   
   ![Usuários](./media/active-directory-saas-sumologic-tutorial/IC778561.png "Users")
3. Clique em **Adicionar**.
   
   ![Usuários](./media/active-directory-saas-sumologic-tutorial/IC778562.png "Users")
4. No diálogo **Novo Usuário** , realize as seguintes etapas:
   
   ![Novo Usuário](./media/active-directory-saas-sumologic-tutorial/IC778563.png "New User")
   
   1. Digite as informações relacionadas da conta do Azure AD que você deseja provisionar nas caixas de texto **Nome**, **Sobrenome** e **Email**.
   2. Selecione uma função.
   3. Para **Status**, selecione **Ativo**.
   4. Clique em **Salvar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do SumoLogic ou as APIs fornecidas pelo SumoLogic para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-sumologic-perform-the-following-steps"></a>Para atribuir usuários ao SumoLogic, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **SumoLogic**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-sumologic-tutorial/IC778564.png "Assign users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-sumologic-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


