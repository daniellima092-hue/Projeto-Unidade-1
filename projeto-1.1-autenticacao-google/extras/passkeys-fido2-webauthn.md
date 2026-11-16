# Integração com FIDO2 e WebAuthn

Este documento mostra como um sistema próprio pode oferecer chaves de acesso com a mesma tecnologia usada pela Google. Diagrama do fluxo: [03-passkey-webauthn.png](../diagramas/03-passkey-webauthn.png).

## Componentes

| Componente | Papel |
|---|---|
| WebAuthn (W3C) | API do navegador (`navigator.credentials`) usada pelo site |
| CTAP (FIDO Alliance) | Protocolo entre o navegador ou sistema e o autenticador externo (USB, NFC, Bluetooth) |
| Autenticador | Celular, computador ou chave de segurança que guarda a chave privada |
| Relying Party (RP) | O site, identificado pelo domínio (`rpId`) |

FIDO2 é o nome do conjunto formado por WebAuthn e CTAP.

## Cadastro da chave de acesso

O servidor gera um desafio aleatório e o envia ao navegador.

```javascript
const credencial = await navigator.credentials.create({
  publicKey: {
    challenge: desafioDoServidor,                    // bytes aleatórios, uso único
    rp: { id: "exemplo.com.br", name: "Exemplo" },
    user: {
      id: idInternoDoUsuario,                        // bytes, sem dados pessoais
      name: "maria.silva@exemplo.com.br",
      displayName: "Maria Silva"
    },
    pubKeyCredParams: [
      { type: "public-key", alg: -7 },               // ES256
      { type: "public-key", alg: -257 }              // RS256
    ],
    authenticatorSelection: {
      residentKey: "required",                       // credencial detectável (passkey)
      userVerification: "required"                   // exige biometria ou PIN
    },
    timeout: 60000
  }
});
// O site envia ao servidor credencial.id e a chave pública para armazenamento.
```

## Login

```javascript
const assercao = await navigator.credentials.get({
  publicKey: {
    challenge: novoDesafioDoServidor,
    rpId: "exemplo.com.br",
    userVerification: "required"
  }
});
// O servidor recebe authenticatorData, clientDataJSON e signature.
```

## Verificações no servidor

| Nº | Verificação | Protege contra |
|---|---|---|
| 1 | O `challenge` em `clientDataJSON` é o que o servidor emitiu | Repetição de asserção antiga |
| 2 | O `origin` em `clientDataJSON` é `https://exemplo.com.br` | Phishing |
| 3 | O *hash* do `rpId` em `authenticatorData` corresponde ao domínio | Uso da credencial em outro site |
| 4 | O indicador de verificação do usuário (UV) está ativo | Uso por terceiro sem biometria ou PIN |
| 5 | A assinatura confere com a chave pública armazenada | Falsificação |

## Por que resiste a phishing

O navegador, e não o usuário, informa a origem ao autenticador. Em um domínio parecido, como `exemp1o.com.br`, não existe credencial cadastrada para aquele `rpId`, e nenhuma assinatura é produzida. Não há segredo que o usuário possa digitar por engano.

## Recomendações de implementação

- Usar biblioteca consolidada no servidor, como SimpleWebAuthn, py_webauthn ou java-webauthn-server.
- Permitir mais de uma chave de acesso por conta.
- Manter um método de recuperação de força compatível.
- Usar o preenchimento automático de chave de acesso (`mediation: "conditional"`) para facilitar a adoção.
