---
title: 'Tutorial: Integração do Azure Active Directory com o vxMaintain | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o vxMaintain.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2016
ms.author: jeedes

---
# Tutorial: integração do Azure Active Directory ao vxMaintain
Neste tutorial, você aprenderá a integrar o vxMaintain ao Azure AD (Azure Active Directory).

A integração do vxMaintain ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao vxMaintain
* Você pode habilitar os usuários a entrar automaticamente no vxMaintain (Logon Único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos
Para configurar a integração do Azure AD ao vxMaintain, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do vxMaintain

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando vxMaintain da Galeria
2. configurar e testar o logon único do AD do Azure

## Adicionando vxMaintain da Galeria
Para configurar a integração do vxMaintain ao Azure AD, você precisará adicionar o vxMaintain da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o vxMaintain da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **vxMaintain**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_01.png)
7. No painel de resultados, selecione **vxMaintain** e clique em **Concluir** para adicionar o aplicativo.

## configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o vxMaintain, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do vxMaintain é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do vxMaintain.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de Usuário** no vxMaintain.

Para configurar e testar o logon único do Azure AD com o vxMaintain, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do vxMaintain](#creating-a-vxmaintain-test-user)** - para ter um equivalente de Brenda Fernandes no vxMaintain que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do Azure AD
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo do vxMaintain.

**Para configurar o logon único do Azure AD com o vxMaintain, execute as seguintes etapas:**

1. No portal clássico, na página de integração de aplicativos do **vxMaintain**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][6]
2. Na página **Como você deseja que os usuários façam logon no vxMaintain**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_03.png)
3. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_04.png)
   
    a. Na caixa de texto **URL de Identificador**, digite a URL no seguinte padrão: **https://\<nome da empresa>.verisae.com**
   
    b. Na caixa de texto **URL de resposta**, digite a URL usando o seguinte padrão: **https://\<nome da empresa>.verisae.com/DataNett/action/ssoConsume/mobile?\_log=true**
   
    c. Clique em **Avançar**
4. Na página **Configurar logon único no vxMaintain**, execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_05.png)
   
    a. Clique em **Baixar metadados** e salve o arquivo no computador.
   
    b. Clique em **Avançar**.
5. Para que o SSO seja configurado para seu aplicativo, entre em contato com o executivo de contas da Verisae para obter ajuda ao para configurar o SSO para sua organização. Forneça as seguintes informações:
   
    • Os **metadados** baixados
6. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon único do AD do Azure][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.
   
    ![Logon único do AD do Azure][11]

### Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_09.png)
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_03.png)
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_04.png)
5. Na página do diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas: ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_05.png)
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Avançar**.
6. Na página de diálogo **Perfil do Usuário**, execute as seguintes etapas: ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_06.png)
   
   a. Na caixa de texto **Nome**, digite **Brenda**.
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Avançar**.
7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_07.png)
8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_08.png)
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.

### Criando um usuário de teste vxMaintain
Nesta seção, você criará uma usuária chamada Brenda Fernandes no vxMaintain. Entre em contato com um executivo de contas na Verisae para obter ajuda para adicionar os usuários à plataforma vxMaintain.

### Atribuição do usuário de teste do AD do Azure
Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao vxMaintain.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao vxMaintain, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201]
2. Na lista de aplicativos, selecione **vxMaintain**.
   
    ![Configurar o logon único](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_50.png)
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### Teste do logon único
Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco vxMaintain no Painel de Acesso, deve ser conectado automaticamente ao seu aplicativo vxMaintain.

## Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0810_2016-->