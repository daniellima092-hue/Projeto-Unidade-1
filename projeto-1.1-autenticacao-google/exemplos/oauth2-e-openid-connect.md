# Exemplos: OAuth 2.0 e OpenID Connect na Google

Os endereços são os reais da plataforma de identidade da Google. Identificadores, códigos e tokens são fictícios.

## Parte 1: OAuth 2.0 com código de autorização e PKCE

Os endereços são os reais da plataforma de identidade da Google. Os valores de `client_id`, `client_secret`, códigos e tokens são fictícios e servem apenas de ilustração.

Diagrama do fluxo: [04-oauth2-authorization-code-pkce.png](../diagramas/04-oauth2-authorization-code-pkce.png).

### Pré-requisito: registrar o aplicativo

1. Acessar o Google Cloud Console e criar um projeto.
2. Configurar a tela de consentimento OAuth (nome do aplicativo, e-mail de suporte, escopos).
3. Em **Credenciais**, criar um **ID do cliente OAuth** do tipo "Aplicativo da Web".
4. Cadastrar a URI de redirecionamento exata: `https://app.exemplo.com.br/oauth2/callback`.
5. Guardar o `client_secret` em cofre de segredos.

### Passo 1: gerar os valores de segurança

```
code_verifier  = dBjftJeZ4CVP-mB92K27uhbUJU1p1r_wW1gFWFOEjXk
code_challenge = E9Melhoa2OwvFrEMTJguCHaoeK1t8URWbuGJSstw-cM      (BASE64URL(SHA-256(code_verifier)))
state          = 8f3b1c7e5a9d4e20b6c1f0a2d7e4c9b3
nonce          = n-0S6_WzA2Mj
```

O par `code_verifier` e `code_challenge` acima é o exemplo do Apêndice B da RFC 7636, o que permite conferir o cálculo.

### Passo 2: pedido de autorização (redirecionamento do navegador)

```http
GET https://accounts.google.com/o/oauth2/v2/auth
    ?client_id=1234567890-abc123def456.apps.googleusercontent.com
    &redirect_uri=https%3A%2F%2Fapp.exemplo.com.br%2Foauth2%2Fcallback
    &response_type=code
    &scope=openid%20email%20profile%20https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fcalendar.readonly
    &state=8f3b1c7e5a9d4e20b6c1f0a2d7e4c9b3
    &nonce=n-0S6_WzA2Mj
    &code_challenge=E9Melhoa2OwvFrEMTJguCHaoeK1t8URWbuGJSstw-cM
    &code_challenge_method=S256
    &access_type=offline
    &prompt=consent
```

| Parâmetro | Função |
|---|---|
| `response_type=code` | Solicita o fluxo de código de autorização |
| `scope` | Permissões pedidas. `openid email profile` ativam o OpenID Connect; o último escopo dá leitura da Agenda |
| `state` | Protege contra CSRF |
| `nonce` | Liga o ID token a este pedido |
| `code_challenge` | Metade pública do PKCE |
| `access_type=offline` | Pede também um token de atualização |

O Google autentica o usuário (senha, 2SV ou chave de acesso) e mostra a tela de consentimento.

### Passo 3: retorno para o aplicativo

```http
HTTP/1.1 302 Found
Location: https://app.exemplo.com.br/oauth2/callback
    ?code=4/0AeanS0Zx-EXEMPLO-FICTICIO
    &state=8f3b1c7e5a9d4e20b6c1f0a2d7e4c9b3
```

O aplicativo confere se o `state` recebido é igual ao que enviou. Se for diferente, o processo é interrompido.

### Passo 4: troca do código por tokens (servidor para servidor)

```http
POST /token HTTP/1.1
Host: oauth2.googleapis.com
Content-Type: application/x-www-form-urlencoded

code=4/0AeanS0Zx-EXEMPLO-FICTICIO
&client_id=1234567890-abc123def456.apps.googleusercontent.com
&client_secret=VALOR_GUARDADO_NO_COFRE
&redirect_uri=https%3A%2F%2Fapp.exemplo.com.br%2Foauth2%2Fcallback
&grant_type=authorization_code
&code_verifier=dBjftJeZ4CVP-mB92K27uhbUJU1p1r_wW1gFWFOEjXk
```

Resposta:

```json
{
  "access_token": "ya29.a0EXEMPLO-FICTICIO",
  "expires_in": 3599,
  "refresh_token": "1//0gEXEMPLO-FICTICIO",
  "scope": "openid https://www.googleapis.com/auth/userinfo.email https://www.googleapis.com/auth/userinfo.profile https://www.googleapis.com/auth/calendar.readonly",
  "token_type": "Bearer",
  "id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IkVYRU1QTE8ifQ.EXEMPLO.FICTICIO"
}
```

### Passo 5: chamada à API

```http
GET /calendar/v3/users/me/calendarList HTTP/1.1
Host: www.googleapis.com
Authorization: Bearer ya29.a0EXEMPLO-FICTICIO
```

### Passo 6: renovação do token de acesso

```http
POST /token HTTP/1.1
Host: oauth2.googleapis.com
Content-Type: application/x-www-form-urlencoded

client_id=1234567890-abc123def456.apps.googleusercontent.com
&client_secret=VALOR_GUARDADO_NO_COFRE
&refresh_token=1//0gEXEMPLO-FICTICIO
&grant_type=refresh_token
```

### Passo 7: revogação ao desconectar

```http
POST /revoke HTTP/1.1
Host: oauth2.googleapis.com
Content-Type: application/x-www-form-urlencoded

token=1//0gEXEMPLO-FICTICIO
```

### Variante: fluxo de dispositivo (TVs e aparelhos sem teclado)

```http
POST /device/code HTTP/1.1
Host: oauth2.googleapis.com
Content-Type: application/x-www-form-urlencoded

client_id=1234567890-abc123def456.apps.googleusercontent.com&scope=openid%20email
```

O aparelho recebe um `user_code` curto e o endereço `https://www.google.com/device`. O usuário abre esse endereço no celular, digita o código e autoriza. Enquanto isso, o aparelho consulta o endpoint de token até a autorização ser concluída.

### Erros que este exemplo evita

| Erro | Como o exemplo evita |
|---|---|
| Fluxo implícito (`response_type=token`) | Usa `response_type=code` |
| Ausência de PKCE | Envia `code_challenge` com `S256` |
| `state` fixo ou ausente | Gera valor aleatório por pedido |
| Escopo excessivo | Pede apenas leitura da Agenda |
| `client_secret` no navegador | A troca ocorre no servidor |

---

## Parte 2: OpenID Connect e validação do ID token

Valores fictícios, com a estrutura real usada pela Google. Diagrama: [05-openid-connect-login-google.png](../diagramas/05-openid-connect-login-google.png).

### Documento de descoberta

O aplicativo não deve fixar endereços no código. Ele consulta o documento de descoberta:

```http
GET https://accounts.google.com/.well-known/openid-configuration
```

Trecho da resposta:

```json
{
  "issuer": "https://accounts.google.com",
  "authorization_endpoint": "https://accounts.google.com/o/oauth2/v2/auth",
  "token_endpoint": "https://oauth2.googleapis.com/token",
  "userinfo_endpoint": "https://openidconnect.googleapis.com/v1/userinfo",
  "revocation_endpoint": "https://oauth2.googleapis.com/revoke",
  "jwks_uri": "https://www.googleapis.com/oauth2/v3/certs",
  "response_types_supported": ["code", "token", "id_token", "..."],
  "id_token_signing_alg_values_supported": ["RS256"],
  "code_challenge_methods_supported": ["plain", "S256"]
}
```

### Estrutura do ID token

O ID token é um JWT com três partes separadas por ponto: cabeçalho, corpo e assinatura.

Cabeçalho:

```json
{
  "alg": "RS256",
  "kid": "a1b2c3d4e5f6EXEMPLO",
  "typ": "JWT"
}
```

Corpo:

```json
{
  "iss": "https://accounts.google.com",
  "azp": "1234567890-abc123def456.apps.googleusercontent.com",
  "aud": "1234567890-abc123def456.apps.googleusercontent.com",
  "sub": "110169484474386276334",
  "hd": "empresa.com.br",
  "email": "maria.silva@empresa.com.br",
  "email_verified": true,
  "nonce": "n-0S6_WzA2Mj",
  "name": "Maria Silva",
  "picture": "https://lh3.googleusercontent.com/a/EXEMPLO",
  "given_name": "Maria",
  "family_name": "Silva",
  "iat": 1794000000,
  "exp": 1794003600
}
```

### Lista de validação

O aplicativo só cria a sessão se todas as verificações passarem.

| Nº | Verificação | Motivo |
|---|---|---|
| 1 | A assinatura confere com a chave pública do `jwks_uri` identificada pelo `kid` | Garante que o token foi emitido pela Google e não foi alterado |
| 2 | O algoritmo é `RS256`, e não `none` ou outro inesperado | Evita ataque de troca de algoritmo |
| 3 | `iss` é `https://accounts.google.com` | Confirma o emissor |
| 4 | `aud` é o `client_id` do aplicativo | Impede o uso de token emitido para outro aplicativo |
| 5 | `exp` está no futuro | Rejeita token vencido |
| 6 | `nonce` é igual ao enviado no pedido | Impede a repetição de um token antigo |
| 7 | `hd` é o domínio esperado, se o acesso for restrito à organização | O parâmetro `hd` do pedido é só uma sugestão de interface e pode ser alterado |
| 8 | `email_verified` é verdadeiro, se o e-mail for usado | Evita confiar em endereço não confirmado |

### Pseudocódigo

```python
# Ilustrativo. Em produção, usar a biblioteca oficial google-auth.
from google.oauth2 import id_token
from google.auth.transport import requests

CLIENT_ID = "1234567890-abc123def456.apps.googleusercontent.com"

def autenticar(token_recebido, nonce_da_sessao):
    # Valida assinatura, iss, aud e exp
    dados = id_token.verify_oauth2_token(token_recebido, requests.Request(), CLIENT_ID)

    if dados.get("nonce") != nonce_da_sessao:
        raise ValueError("nonce não confere")
    if dados.get("hd") != "empresa.com.br":
        raise ValueError("conta fora da organização")

    # O identificador do usuário é o sub, não o e-mail
    return buscar_ou_criar_usuario(google_sub=dados["sub"], email=dados["email"])
```

### Botão "Fazer login com o Google" (Google Identity Services)

```html
<script src="https://accounts.google.com/gsi/client" async></script>

<div id="g_id_onload"
     data-client_id="1234567890-abc123def456.apps.googleusercontent.com"
     data-login_uri="https://app.exemplo.com.br/login/google"
     data-nonce="n-0S6_WzA2Mj"
     data-auto_prompt="false">
</div>
<div class="g_id_signin" data-type="standard" data-text="signin_with"></div>
```

O Google envia o ID token por `POST` para `data-login_uri`, e o servidor aplica a lista de validação acima.
