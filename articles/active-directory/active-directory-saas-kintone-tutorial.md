---
title: "Tutorial: Integração do Azure Active Directory com o Kintone | Microsoft Docs"
description: "Saiba como usar o Kintone com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7a25e030e8ade95db2ac9cd58fe4d94bbb7775ba


---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Tutorial: Integração do Active Directory do Azure com o Kintone
O objetivo deste tutorial é mostrar a integração do Azure com o Kintone.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura do Kintone com logon único habilitado

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Kintone poderão fazer logon único no aplicativo em seu site de empresa do Kintone (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Kintone
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-kintone-tutorial/IC785859.png "Scenario")

## <a name="enabling-the-application-integration-for-kintone"></a>Habilitando a integração de aplicativos para o Kintone
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Kintone.

### <a name="to-enable-the-application-integration-for-kintone-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o Kintone, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-kintone-tutorial/IC700994.png "Applications")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-kintone-tutorial/IC749321.png "Add application")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-kintone-tutorial/IC749322.png "Add an application from gallerry")

6. Na **caixa de pesquisa**, digite **Kintone**.
   
    ![Galeria de Aplicativos](./media/active-directory-saas-kintone-tutorial/IC785867.png "Application Gallery")

7. No painel de resultados, selecione **Kintone** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Kintone](./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
   
## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Kintone com a própria conta no AD do Azure usando federação baseada no protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração de aplicativos do **Kintone**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-kintone-tutorial/IC785872.png "Configure Single Sign-On")

2. Na página **Como você deseja que os usuários façam logon no Kintone**, selecione **Logon único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-kintone-tutorial/IC785873.png "Configure Single Sign-On")

3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon no Kintone**, digite a URL usando o padrão "*https://company.kintone.com*" e clique em **Avançar**.
   
    ![Configurar a URL do Aplicativo](./media/active-directory-saas-kintone-tutorial/IC785875.png "Configure App URL")

4. Na página **Configurar logon único no Kintone**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
    ![Configurar Logon Único](./media/active-directory-saas-kintone-tutorial/IC785878.png "Configure Single Sign-On")

5. Em outra janela do navegador da Web, faça logon no site da sua empresa do **Kintone** como administrador.

6. Clique em **Configurações**.
   
    ![Configurações](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

7. Clique em **Usuários e administração do sistema**.
   
    ![Usuários e administração do sistema](./media/active-directory-saas-kintone-tutorial/IC785880.png "Users & System Administration")

8. Em **Administração do Sistema \> Segurança**, clique em **Logon**.
   
    ![Logon](./media/active-directory-saas-kintone-tutorial/IC785881.png "Login")

9. Selecione **Habilitar a autenticação do SAML**.
   
    ![Autenticação do SAML](./media/active-directory-saas-kintone-tutorial/IC785882.png "SAML Authentication")

10. Na seção Autenticação do SAML, execute as seguintes etapas:
    
    ![Autenticação SAML](./media/active-directory-saas-kintone-tutorial/IC785883.png "SAML Authentication")
    
    1. No portal clássico do Azure, na página do diálogo **Configurar logon único no Kintone**, copie o valor de **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon**.
   
    2. No portal clássico do Azure, na página do diálogo **Configurar logon único no Kintone**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff**.
    
    3. Clique em **Pesquisar** para carregar o certificado que você baixou.
    
    4. Clique em **Salvar**.

11. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar Logon Único](./media/active-directory-saas-kintone-tutorial/IC785884.png "Configure Single Sign-On")
    
## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Kintone, eles devem ser provisionados no Kintone.  
No caso do Kintone, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **Kintone** como administrador.

2. Clique em **Configuração**.
   
    ![Configurações](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

3. Clique em **Usuários e administração do sistema**.
   
    ![Usuário e administração do sistema](./media/active-directory-saas-kintone-tutorial/IC785880.png "User & System Administration")

4. Em **Administração de Usuários**, clique em **Departamentos e Usuários**.
   
    ![Departamento e usuários](./media/active-directory-saas-kintone-tutorial/IC785888.png "Department & Users")

5. Clique em **Novo Usuário**.
   
    ![Novos usuários](./media/active-directory-saas-kintone-tutorial/IC785889.png "New Users")

6. Na seção **Novo Usuário** , realize as seguintes etapas:
   
    ![Novos usuários](./media/active-directory-saas-kintone-tutorial/IC785890.png "New Users")
   
    1. Digite um **Nome de Exibição**, **Nome de Logon**, **Nova Senha**, **Confirmar Senha**, **Endereço de Email** e outros detalhes de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas.
 
    2. Clique em **Salvar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Kintone ou APIs fornecidas pelo Kintone para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-kintone-perform-the-following-steps"></a>Para atribuir usuários ao Kintone, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.

2. Na página de integração de aplicativos do **Kintone**, clique em **Atribuir usuários**.
   
    ![Atribuir usuários](./media/active-directory-saas-kintone-tutorial/IC785891.png "Assign Users")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-kintone-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


