### <a name="a-nameserver-authahow-to-authenticate-with-a-provider-server-flow"></a><a name="server-auth"></a>Como autenticar com um provedor (fluxo de servidor)
Para que os Aplicativos Móveis gerenciem o processo de autenticação em seu aplicativo, é necessário registrá-los no provedor de identidade. Em seguida, no Serviço de Aplicativo do Azure, você precisa configurar a ID e o segredo do aplicativo fornecidos por seu provedor.
Para obter mais informações, consulte o tutorial [Adicionar autenticação ao seu aplicativo](../articles/app-service-mobile/app-service-mobile-ios-get-started-users.md).

Depois de registrar seu provedor de identidade, basta chamar o método .login() com o nome de seu provedor. Por exemplo, para fazer logon com o Facebook, use o código a seguir.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
Nesse caso, o Serviço de Aplicativo do Azure gerencia o fluxo de autenticação OAuth 2.0 exibindo a página de logon do provedor selecionado e gerando um token de autenticação do Serviço de Aplicativo após um logon bem-sucedido com o provedor de identidade. A função de logon, quando concluída, retorna um objeto JSON (user) que expõe a ID do usuário e o token de autenticação do Serviço de Aplicativo nos campos userId e authenticationToken, respectivamente. Esse token pode ser armazenado em cache e reutilizado até que expire.

###<a name="a-nameclient-authahow-to-authenticate-with-a-provider-client-flow"></a><a name="client-auth"></a>Como autenticar com um provedor (fluxo de cliente)

Seu aplicativo também pode entrar em contato de forma independente com o provedor de identidade e fornecer o token retornado ao Serviço de Aplicativo para autenticação. Esse fluxo de cliente permite que você forneça uma experiência de logon único aos usuários ou recupere dados adicionais do usuário do provedor de identidade.

#### <a name="social-authentication-basic-example"></a>Exemplo básico de autenticação social

Este exemplo usa o SDK de cliente do Facebook para a autenticação:

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});

```
Esse exemplo pressupõe que o token fornecido pelo respectivo SDK do provedor é armazenado na variável 'token'.

#### <a name="microsoft-account-example"></a>Exemplo de conta da Microsoft

O exemplo a seguir usa o Live SDK, que oferece suporte a logon único para aplicativos da Windows Store, usando a Conta da Microsoft:

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});

```

Esse exemplo obtém um token do Live Connect, que é fornecido ao seu Serviço de Aplicativo chamando a função de logon.

###<a name="a-nameauth-getinfoahow-to-obtain-information-about-the-authenticated-user"></a><a name="auth-getinfo"></a>Como obter informações sobre o usuário autenticado

As informações de autenticação do usuário atual podem ser obtidas no ponto de extremidade `/.auth/me` usando qualquer método do AJAX.  Certifique-se de definir o cabeçalho `X-ZUMO-AUTH` ao token de autenticação.  O token de autenticação está armazenado em `client.currentUser.mobileServiceAuthenticationToken`.  Por exemplo, para usar a API de busca:

```
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

O Fetch está disponível como um pacote npm ou para download do navegador do CDNJS. Você também pode usar jQuery ou outra API AJAX para buscar as informações.  Os dados serão recebidos como um objeto JSON.



<!--HONumber=Nov16_HO5-->


