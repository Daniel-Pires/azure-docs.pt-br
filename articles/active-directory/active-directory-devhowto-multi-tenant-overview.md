---
title: Como criar um aplicativo que pode conectar qualquer usuário do Azure Active Directory| Microsoft Docs
description: Instruções passo a passo para a criação de um aplicativo que pode conectar um usuário de qualquer locatário do Azure Active Directory, também conhecido como aplicativo multilocatário.
services: active-directory
documentationcenter: ''
author: skwan
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2016
ms.author: skwan;bryanla

---
# Como entrar em qualquer usuário do Azure AD (Active Directory) usando o padrão de aplicativo multilocatário
Se você oferecer um aplicativo Software como Serviço para muitas organizações, poderá configurar seu aplicativo para aceitar entradas de qualquer locatário do Azure AD. No Azure AD, isso é chamado de tornar seu aplicativo multilocatário. Os usuários em qualquer locatário do Azure AD poderão entrar em seu aplicativo após o consentimento para usar sua conta com o aplicativo.

Caso você tenha um aplicativo existente que tem seu próprio sistema de conta ou dê suporte a outros tipos de entrada de outros provedores de nuvem, adicionar a entrada do Azure AD de qualquer locatário é tão simples quanto registrar seu aplicativo, adicionar o código de entrada por meio de OAuth2, OpenID Connect ou SAML e colocar um botão Entrar com a Microsoft no aplicativo. Clique no botão abaixo para saber mais sobre a identidade visual do seu aplicativo.

[![Botão Entrar][AAD-Sign-In]][AAD-App-Branding]

Este artigo pressupõe que você já está familiarizado com a criação de um aplicativo de locatário único do Azure AD. Se não estiver, retorne à [homepage do guia do desenvolvedor][AAD-Dev-Guide] e experimente um de nossos inícios rápidos!

Há quatro etapas simples para converter seu aplicativo em um aplicativo multilocatário do Azure AD:

1. Atualizar seu registro de aplicativo para ser multilocatário
2. Atualizar seu código para enviar solicitações para o ponto de extremidade /common
3. Atualizar seu código para lidar com vários valores de emissor
4. Entender o consentimento do usuário e administrador e fazer as alterações de código apropriadas

Vamos examinar cada etapa detalhadamente. Você também pode ir diretamente para [esta lista de exemplos de multilocatário][AAD-Samples-MT].

## Atualizar o registro para ser multilocatário
Por padrão, os registros de API/aplicativo Web no Azure AD são de locatário único. Você pode tornar o registro multilocatário localizando a opção “O aplicativo é multilocatário” na página de configuração do registro do aplicativo no [portal clássico do Azure][AZURE-classic-portal] e configurando-a como “Sim”.

Observação: antes de um aplicativo poder ser definido como multilocatário, o Azure AD requer que o URI da ID do Aplicativo seja globalmente exclusivo. O URI da ID do Aplicativo é uma das maneiras que um aplicativo é identificado em mensagens de protocolo. Para um aplicativo de locatário único, é suficiente para o URI da ID do Aplicativo ser exclusivo dentro desse locatário. Para um aplicativo multilocatário, ele deve ser globalmente exclusivo para que o Azure AD possa localizar os aplicativos em todos os locatários. A exclusividade global é imposta exigindo o URI da ID do Aplicativo com um nome de host que corresponda a um domínio verificado do locatário do Azure AD. Por exemplo, se o nome do seu locatário fosse contoso.onmicrosoft.com, um URI da ID do Aplicativo válido seria `https://contoso.onmicrosoft.com/myapp`. Se seu locatário tivesse um domínio verificado de `contoso.com`, então um URI da ID do Aplicativo também seria `https://contoso.com/myapp`. A configuração de um aplicativo como multilocatário falhará se o URI da ID do Aplicativo não seguir esse padrão.

Registros de cliente nativos são multilocatários por padrão. Você não precisa realizar nenhuma ação para tornar um registro de aplicativo cliente nativo multilocatário.

## Atualizar seu código para enviar solicitações para /common
Em um aplicativo de locatário único, as solicitações de entrada são enviadas para o ponto de extremidade de entrada do locatário. Por exemplo, para contoso.onmicrosoft.com o ponto de extremidade seria:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

Solicitações enviadas para o ponto de extremidade de um locatário podem realizar a entrada de usuários (ou convidados) naquele locatário para aplicativos nele. Com um aplicativo multilocatário, o aplicativo não sabe com antecedência de qual locatário o usuário é, portanto, você não pode enviar solicitações para o ponto de extremidade de um locatário. Em vez disso, as solicitações são enviadas para um ponto de extremidade que multiplexa entre todos os locatários do Azure AD:

    https://login.microsoftonline.com/common

Quando o Azure AD recebe uma solicitação no ponto de extremidade /common, ele realiza a entrada do usuário e como consequência descobre de qual locatário o usuário é. O ponto de extremidade /common funciona com todos os protocolos de autenticação com suporte pelo Azure AD: OpenID Connect, OAuth 2.0, SAML 2.0 e Web Services Federation.

A resposta de entrada no aplicativo contém um token representando o usuário. O valor do emissor no token diz a um aplicativo de qual locatário o usuário é. Quando uma resposta retorna do ponto de extremidade /common, o valor do emissor no token corresponderá ao locatário do usuário. É importante observar que o ponto de extremidade /common não é um locatário e não é um emissor, ele é apenas um multiplexador. Ao usar /common, a lógica em seu aplicativo para validar tokens precisa ser atualizada para considerar isso.

Como mencionado anteriormente, os aplicativos multilocatário também devem fornecer uma experiência de entrada consistente para usuários, seguindo as diretrizes de identidade visual do aplicativo do Azure AD. Clique no botão abaixo para saber mais sobre a identidade visual do seu aplicativo.

[![Botão Entrar][AAD-Sign-In]][AAD-App-Branding]

Vamos examinar o uso do ponto de extremidade /common e a sua implementação de código em mais detalhes.

## Atualizar seu código para lidar com vários valores de emissor
Os aplicativos Web e as APIs Web recebem e validam os tokens do Azure AD.

> [!NOTE]
> Embora os aplicativos cliente nativos solicitem e recebam tokens do Azure AD, eles fazem isso para enviá-los para APIs, nas quais eles são validados. Os aplicativos nativos não validam os tokens e devem tratá-los como opacos.
> 
> 

Vamos examinar como um aplicativo valida os tokens que ele recebe do Azure AD. Um aplicativo de locatário único normalmente terá um valor de ponto de extremidade como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

e o usará para criar uma URL de metadados (nesse caso, OpenID Connect) como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

para baixar duas partes críticas de informações que são usadas para validar tokens: as chaves de assinatura do locatário e o valor de emissor. Cada locatário do Azure AD tem um valor de emissor exclusivo do formato:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

em que o valor de GUID é a versão à prova de renomeação da ID de locatário. Se você clicar no link de metadados acima para `contoso.onmicrosoft.com`, poderá ver esse valor de emissor no documento.

Quando um aplicativo de locatário único valida um token, ele verifica a assinatura do token em relação às chaves de assinatura do documento de metadados e garante que o valor do emissor no token corresponde ao que foi encontrado no documento de metadados.

Como o ponto de extremidade /common não corresponde a um locatário e não é um emissor, ao examinar o valor do emissor nos metadados para /common, ele tem uma URL de modelo em vez de um valor real:

    https://sts.windows.net/{tenantid}/

Portanto, um aplicativo multilocatário não pode validar tokens apenas combinando o valor do emissor nos metadados com o valor `issuer` no token. Um aplicativo multilocatário precisa de lógica para decidir quais valores de emissor são válidos e quais não são com base na parte da ID do locatário do valor do emissor.

Por exemplo, se um aplicativo multilocatário permite apenas a entrada de locatários específicos que se inscreveram para seu serviço, ele deve verificar o valor do emissor ou o valor da declaração `tid` no token para ter certeza de que o locatário está na sua lista de assinantes. Se um aplicativo multilocatário lida apenas com pessoas e não toma nenhuma decisão de acesso com base em locatários, ele pode ignorar o valor de emissor completamente.

Nos exemplos de multilocatários que você encontrará na seção [Conteúdo relacionado](#related-content) no final desse artigo, a validação do emissor está desabilitada para habilitar qualquer locatário do Azure AD a entrar.

Agora vamos examinar a experiência do usuário para usuários que estão entrando em aplicativos multilocatários.

## Entendendo o consentimento do usuário e do administrador
Para um usuário entrar em um aplicativo no Azure AD, o aplicativo deve estar representado no locatário do usuário. Isso permite que a organização realize ações como aplicar políticas exclusivas quando usuários de seu locatário entrarem no aplicativo. Para um aplicativo de locatário único esse registro é simples, é o que acontece quando você registra o aplicativo no [portal clássico do Azure][AZURE-classic-portal].

Para um aplicativo multilocatário, o registro inicial para o aplicativo reside no locatário do Azure AD usado pelo desenvolvedor. Quando um usuário de um locatário diferente entra no aplicativo pela primeira vez, o Azure AD solicita que ele consinta com as permissões solicitadas pelo aplicativo. Se ele consentir, uma representação do aplicativo chamada de uma *entidade de serviço* será criada no locatário do usuário e o processo de entrada poderá continuar. Uma delegação também é criada no diretório que registra o consentimento do usuário para o aplicativo. Consulte [Objetos de aplicativo e de entidade de serviço][AAD-App-SP-Objects] para obter detalhes sobre objetos Application e ServicePrincipal do aplicativo e como eles se relacionam entre si.

![Consentimento ao aplicativo de camada única][Consent-Single-Tier]

Essa experiência de consentimento é afetada pelas permissões solicitadas pelo aplicativo. O Azure AD dá suporte a dois tipos de permissões, somente do aplicativo e delegadas:

* Uma permissão delegada concede a um aplicativo a capacidade de atuar como um usuário conectado para um subconjunto das ações que o usuário pode realizar. Por exemplo, você pode conceder a um aplicativo a permissão delegada para ler o calendário do usuário conectado.
* Uma permissão somente do aplicativo é concedida diretamente à identidade do aplicativo. Por exemplo, você pode conceder a permissão somente do aplicativo a um aplicativo para ler a lista de usuários em um locatário e ele poderá fazer isso independentemente de quem estiver conectado ao aplicativo.

Algumas permissões podem ser consentidas por um usuário normal, enquanto outras exigem o consentimento de um administrador de locatários.

### Consentimento do administrador
As permissões somente do aplicativo sempre exigem o consentimento do administrador de locatários. Se seu aplicativo solicitar uma permissão somente do aplicativo e um usuário normal tentar entrar no aplicativo, o aplicativo receberá uma mensagem de erro informando que o usuário não pode consentir.

Algumas permissões delegadas também exigem o consentimento do administrador de locatários. Por exemplo, a capacidade de gravar no Azure AD como o usuário conectado requer o consentimento de um administrador de locatários. Semelhante às permissões somente do aplicativo, se um usuário comum tentar entrar em um aplicativo que solicita uma permissão delegada que exige o consentimento do administrador, seu aplicativo receberá um erro. Se uma permissão exige ou não o consentimento do administrador é determinado pelo desenvolvedor que publicou o recurso e pode ser encontrado na documentação do recurso. Os links para tópicos que descrevem as permissões disponíveis para a API do Azure AD Graph e a API do Microsoft Graph estão na seção [Conteúdo relacionado](#related-content) desse artigo.

Se seu aplicativo usa permissões que exigem o consentimento do administrador, você precisa ter um gesto em seu aplicativo, como um botão ou link, em que o administrador possa iniciar a ação. A solicitação que seu aplicativo envia para essa ação é uma solicitação de autorização do OAuth2/OpenID Connect normal, mas ela também inclui o parâmetro de cadeia de caracteres de consulta `prompt=admin_consent`. Após o administrador ter consentido e a entidade de serviço ter sido criada no locatário do cliente, as solicitações de entrada subsequentes não precisam do parâmetro `prompt=admin_consent`. Uma vez que o administrador tenha decidido que as permissões solicitadas são aceitáveis, nenhum outro usuário no locatário será solicitado quanto ao consentimento daquele ponto em diante.

O parâmetro `prompt=admin_consent` também pode ser usado por aplicativos que solicitam permissões que não exigem o consentimento do administrador, mas desejam proporcionar uma experiência em que o administrador do locatário “se inscreve” no aplicativo uma vez e nenhum outro usuário é solicitado quanto ao consentimento daí em diante.

Se um aplicativo exigir o consentimento do administrador e o administrador entrar no aplicativo, mas o parâmetro `prompt=admin_consent` não for enviado, o administrador poderá consentir o aplicativo com êxito, mas ele consentirá apenas sua conta de usuário. Os usuários normais ainda não poderão fazer entrar e consentir o aplicativo. Isso será útil se você quiser conceder ao administrador de locatários a capacidade de explorar seu aplicativo antes de permitir o acesso de outros usuários.

Um administrador de locatários pode desabilitar a capacidade dos usuários regulares consentirem aplicativos. Se essa funcionalidade estiver desabilitada, o consentimento do administrador sempre será necessário para o aplicativo a ser configurado no locatário. Se você quiser testar seu aplicativo com o consentimento do usuário normal desabilitado, poderá encontrar a opção de configuração na seção de configuração do locatário do Azure AD do [portal clássico do Azure][AZURE-classic-portal].

> [!NOTE]
> Alguns aplicativos desejam uma experiência em que os usuários normais podem consentir inicialmente e posteriormente o aplicativo pode envolver o administrador e solicitar permissões que exigem o consentimento do administrador. Não é possível fazer isso com um registro de aplicativo único no Azure AD atualmente. O próximo ponto de extremidade do Azure AD v2 permitirá que os aplicativos solicitem permissões no tempo de execução em vez de no momento do registro, o que permitirá esse cenário. Para saber mais, consulte o [Guia do desenvolvedor do modelo de aplicativo do Azure AD v2][AAD-V2-Dev-Guide].
> 
> 

### Aplicativos de várias camadas e consentimento
Seu aplicativo pode ter várias camadas, cada uma representada por seu próprio registro no Azure AD. Por exemplo, um aplicativo nativo que chama uma API Web ou um aplicativo Web que chama uma API Web. Em ambos os casos, o cliente (aplicativo nativo ou aplicativo Web) solicita permissões para chamar o recurso (API Web). Para o cliente ter o consentimento com êxito em um locatário do cliente, todos os recursos aos quais ele solicita permissões já devem existir no locatário do cliente. Se essa condição não for atendida, o Azure AD retornará um erro de que o recurso deve ser adicionado primeiro.

Isso poderá ser um problema se seu aplicativo lógico consistir em dois ou mais registros de aplicativo, por exemplo, um cliente e um recurso separados. Como você obtém o recurso no locatário do cliente primeiro? O Azure AD aborda este caso permitindo que o cliente e o recurso sejam consentidos em uma única etapa, na qual o usuário vê a soma total das permissões solicitadas pelo cliente e pelo recurso na página de consentimento. Para permitir esse comportamento, o registro do aplicativo do recurso deve incluir a ID do aplicativo do cliente como um `knownClientApplications` no manifesto do aplicativo. Por exemplo:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Essa propriedade pode ser atualizada por meio do [manifesto do aplicativo][AAD-App-Manifest] do recurso e é demonstrada em um cliente nativo de várias camadas chamando o exemplo de API Web na seção [Conteúdo relacionado](#related-content) no final desse artigo. O diagrama a seguir fornece uma visão geral de consentimento para um aplicativo de várias camadas:

![Consentimento ao aplicativo cliente conhecido de várias camadas][Consent-Multi-Tier-Known-Client]

Um caso semelhante acontecerá se as diferentes camadas de um aplicativo forem registradas em locatários diferentes. Por exemplo, considere o caso da criação de um aplicativo cliente nativo que chama a API do Office 365 Exchange Online. Para desenvolver o aplicativo e posteriormente para o aplicativo nativo ser executado no locatário de um cliente, a entidade de serviço do Exchange Online deve estar presente. Nesse caso o cliente deve adquirir o Exchange Online para a entidade de serviço ser criada em seu locatário. No caso de uma API criada por uma organização diferente da Microsoft, o desenvolvedor da API precisa fornecer uma maneira para seus clientes consentirem seu aplicativo no locatário do cliente, por exemplo, uma página da Web que conduz o consentimento usando os mecanismos descritos nesse artigo. Depois que a entidade de serviço é criada no locatário, o aplicativo nativo pode obter tokens para a API.

O diagrama a seguir fornece uma visão geral de consentimento para um aplicativo de várias camadas registrado em locatários diferentes:

![Consentimento ao aplicativo de vários fornecedores de várias camadas][Consent-Multi-Tier-Multi-Party]

### Revogando o consentimento
Os usuários e administradores podem revogar o consentimento para seu aplicativo a qualquer momento:

* Os usuários revogam o acesso a aplicativos individuais removendo-os da lista de [Aplicativos do Painel de Acesso][AAD-Access-Panel].
* Os administradores revogam o acesso a aplicativos removendo-os do Azure AD usando a seção de gerenciamento do Azure AD do [portal clássico do Azure][AZURE-classic-portal].

Se um administrador der o consentimento a um aplicativo para todos os usuários em um locatário, os usuários não poderão revogar o acesso individualmente. Somente o administrador pode revogar o acesso e somente para o aplicativo inteiro.

### Suporte a protocolo e consentimento
O consentimento tem suporte no Azure AD por meio dos protocolos OAuth, OpenID Connect, Web Services Federation e SAML. Os protocolos SAML e WS-Federation não dão suporte ao parâmetro `prompt=admin_consent`, portanto, o consentimento do administrador é possível apenas por meio do OAuth e do OpenID Connect.

## Aplicativos multilocatários e caching de tokens de acesso
Os aplicativos multilocatários também podem obter tokens de acesso para chamar APIs que são protegidas pelo Azure AD. Um erro comum ao usar a ADAL (Biblioteca de Autenticação do Active Directory) com um aplicativo multilocatário é solicitar inicialmente um token para um usuário usando /common, receber uma resposta e solicitar um token subsequente para o mesmo usuário também usando /common. Uma vez que a resposta do Azure AD vem de um locatário, não de /common, a ADAL armazena em cache o token como sendo do locatário. A chamada subsequente para /common para obter um token de acesso para o usuário perde a entrada de cache e o usuário é solicitado a entrar novamente. Para evitar a perda de cache, certifique-se de que as chamadas subsequentes para um usuário já conectado sejam feitas para o ponto de extremidade do locatário.

## Conteúdo relacionado
* [Exemplos de aplicativos multilocatários][AAD-Samples-MT]
* [Diretrizes de identidade visual para aplicativos][AAD-App-Branding]
* [Guia do Desenvolvedor do Azure AD][AAD-Dev-Guide]
* [Objetos de Aplicativo e de Entidade de Serviço][AAD-App-SP-Objects]
* [Integrando aplicativos ao Active Directory do Azure][AAD-Integrating-Apps]
* [Visão geral da estrutura de consentimento][AAD-Consent-Overview]
* [Escopos de permissão da API do Microsoft Graph][MSFT-Graph-AAD]
* [Escopos de permissão da API do Azure AD Graph][AAD-Graph-Perm-Scopes]

Use a seção de comentários do Disqus abaixo para fornecer seus comentários e ajudar a refinar e a moldar o nosso conteúdo.

<!--Reference style links IN USE -->
[AAD-Access-Panel]: https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/pt-BR/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[MSFT-Graph-AAD]: https://graph.microsoft.io/pt-BR/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ./active-directory-appmodel-v2-overview.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/pt-BR/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken

<!---HONumber=AcomDC_0727_2016-->