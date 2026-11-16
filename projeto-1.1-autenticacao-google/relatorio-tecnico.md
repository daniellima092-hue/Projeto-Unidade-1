# Relatório técnico

## Métodos de autenticação da Google frente a phishing e roubo de credenciais

**Disciplina:** Segurança e Auditoria em TI (2026.2), IFS Campus Itabaiana
**Projeto:** 1.1, Um Estudo sobre os Métodos de Autenticação da Google
**Autor:** Daniel Santos Lima

---

## Sumário

1. [Introdução](#1-introdução)
2. [Panorama das ameaças](#2-panorama-das-ameaças)
3. [Métodos de autenticação em detalhe](#3-métodos-de-autenticação-em-detalhe)
4. [Como as soluções da Google mitigam cada ameaça](#4-como-as-soluções-da-google-mitigam-cada-ameaça)
5. [Riscos residuais](#5-riscos-residuais)
6. [Recomendações](#6-recomendações)
7. [Conclusão](#7-conclusão)

---

## 1. Introdução

A Conta Google concentra e-mail, arquivos, fotos, agenda, histórico de localização e, pelo recurso "Fazer login com o Google", o acesso a serviços de terceiros. Comprometer essa conta costuma dar ao atacante o controle da vida digital da vítima, inclusive a possibilidade de redefinir senhas de outros serviços pelo e-mail.

Este relatório descreve como os métodos de autenticação da Google funcionam, quais ataques cada um sofre e de que forma as soluções mais recentes reduzem os dois riscos centrais: o phishing e o roubo de senhas. A documentação de apoio está na pasta [docs](docs/) e os exemplos, na pasta [exemplos](exemplos/).

## 2. Panorama das ameaças

### 2.1 Phishing

Phishing é a obtenção de credenciais por engano. A vítima recebe uma mensagem convincente e é levada a uma página que imita a tela de login. As variantes relevantes são:

| Variante | Descrição |
|---|---|
| Phishing em massa | Mensagens genéricas enviadas a milhares de pessoas |
| *Spear phishing* | Mensagem personalizada para um alvo específico, com dados reais da vítima |
| Phishing em tempo real (adversário no meio, AiTM) | A página falsa funciona como intermediária: repassa ao Google o que a vítima digita, inclusive o código de segundo fator, e captura o *cookie* de sessão resultante. Ferramentas prontas, como o Evilginx, automatizam o processo |
| Phishing de consentimento | Não rouba a senha. Um aplicativo malicioso pede acesso aos dados pela tela legítima de consentimento OAuth |
| *Smishing* e *vishing* | Phishing por SMS e por ligação telefônica |
| *Quishing* | O link malicioso vem em um código QR, para fugir dos filtros de e-mail |

### 2.2 Roubo de senhas

| Vetor | Descrição |
|---|---|
| Vazamento de bases de dados | Senhas de um serviço invadido são publicadas ou vendidas |
| *Credential stuffing* | Pares de e-mail e senha vazados são testados automaticamente em outros serviços |
| *Password spraying* | Poucas senhas muito comuns são testadas contra muitas contas |
| *Infostealers* | *Malware* que copia senhas salvas no navegador e *cookies* de sessão |
| *Keyloggers* | Registram o que é digitado |

Thomas et al. (2017), em pesquisa conduzida com a Google, analisaram credenciais expostas por vazamentos, kits de phishing e *keyloggers*, e concluíram que as vítimas de phishing têm a maior probabilidade de sofrer tomada de conta, pois os kits capturam também dados usados na verificação, como telefone e localização.

### 2.3 Engenharia social

A engenharia social explora pessoas em vez de sistemas. Exemplos atuais:

- **Fadiga de MFA.** O atacante, já com a senha, dispara dezenas de avisos de login até a vítima aprovar. Foi a técnica usada na invasão da Uber em 2022.
- **Falso suporte.** O atacante liga para a vítima dizendo ser do Google ou do banco e pede o código recebido por SMS.
- **Ataque ao suporte da empresa.** O atacante liga para a central de atendimento fingindo ser um funcionário e pede a redefinição do segundo fator. Foi o ponto de partida do ataque à MGM Resorts em 2023.
- ***SIM swap*.** O atacante convence a operadora a transferir o número da vítima para outro chip e passa a receber os códigos por SMS.

## 3. Métodos de autenticação em detalhe

A descrição completa está em [docs/02-mecanismos-de-autenticacao.md](docs/02-mecanismos-de-autenticacao.md). O quadro abaixo resume o que cada método protege.

| Método | Como funciona | O que o atacante precisa obter |
|---|---|---|
| Senha | Segredo compartilhado, conferido contra um *hash* no servidor | A senha |
| SMS ou voz | Código de uso único enviado pela rede de telefonia | A senha e o código, ou o controle do número |
| Aplicativo autenticador | Código TOTP calculado a partir de um segredo e do horário | A senha e um código válido por até 30 segundos |
| Aviso do Google | Confirmação por notificação em aparelho já logado | A senha e a aprovação da vítima |
| Chave de segurança | Assinatura criptográfica vinculada ao domínio | A senha e a chave física |
| Chave de acesso | Assinatura criptográfica vinculada ao domínio, liberada por biometria ou PIN | O dispositivo desbloqueado ou a conta que sincroniza as chaves |
| OAuth 2.0 e OpenID Connect | Tokens emitidos pelo Google para aplicativos de terceiros | O consentimento da vítima ou um token válido |
| SAML 2.0 | Asserção XML assinada pelo provedor de identidade | A sessão no provedor de identidade ou a chave de assinatura |

## 4. Como as soluções da Google mitigam cada ameaça

### 4.1 Matriz de ameaças e métodos

Legenda: ✔ protege, ◐ protege em parte, ✘ não protege.

| Ameaça | Senha | SMS | TOTP | Aviso | Chave de segurança | Chave de acesso |
|---|---|---|---|---|---|---|
| Phishing em massa | ✘ | ◐ | ◐ | ◐ | ✔ | ✔ |
| Phishing em tempo real (AiTM) | ✘ | ✘ | ✘ | ✘ | ✔ | ✔ |
| *Credential stuffing* | ✘ | ✔ | ✔ | ✔ | ✔ | ✔ |
| Vazamento do banco de dados do serviço | ✘ | ✔ | ◐ | ✔ | ✔ | ✔ |
| *SIM swap* | Não se aplica | ✘ | ✔ | ✔ | ✔ | ✔ |
| Fadiga de MFA | Não se aplica | Não se aplica | Não se aplica | ◐ | ✔ | ✔ |
| *Keylogger* | ✘ | ◐ | ◐ | ✔ | ✔ | ✔ |
| Roubo de *cookie* de sessão por *malware* | ✘ | ✘ | ✘ | ✘ | ✘ | ✘ |

Duas observações. O TOTP protege apenas em parte contra vazamento do servidor porque o segredo que gera os códigos também fica guardado no servidor. E nenhum método de login protege contra o roubo do *cookie* de sessão depois que o login já foi feito, o que é tratado no item 4.4.

### 4.2 Mitigação do phishing

1. **Vínculo com a origem (FIDO2/WebAuthn).** É a única defesa que não depende da atenção do usuário. Chaves de segurança e chaves de acesso só assinam para o domínio em que foram cadastradas. Uma página falsa em outro domínio não recebe nada aproveitável. Lang et al. (2016) descrevem a implantação de chaves de segurança na Google e relatam redução de incidentes e de chamados de suporte em comparação com códigos de uso único.
2. **Avisos com contexto e seleção de número.** O aviso mostra aparelho, local e horário. Em situações de risco, o usuário precisa escolher no celular o número mostrado na tela do computador, o que dificulta a aprovação automática e a fadiga de MFA.
3. **Navegação segura e filtros do Gmail.** O Google Safe Browsing bloqueia páginas de phishing conhecidas no Chrome, e o Gmail filtra mensagens suspeitas antes que cheguem à caixa de entrada.
4. **Redução do SMS.** A Google declarou em 2025 a intenção de substituir códigos por SMS pela leitura de código QR, eliminando um código que pode ser repassado por engano.
5. **Programa Proteção Avançada.** Para pessoas em risco elevado, exige chave de segurança ou chave de acesso, restringe aplicativos de terceiros e torna a recuperação de conta mais rigorosa.
6. **Verificação de aplicativos OAuth.** Aplicativos que pedem escopos sensíveis passam por verificação, e a tela de consentimento alerta sobre aplicativos não verificados. No Workspace, o administrador pode liberar apenas aplicativos aprovados, o que reduz o phishing de consentimento.

### 4.3 Mitigação do roubo de senhas

1. **Chaves de acesso.** Não existe senha para roubar. O servidor guarda apenas a chave pública, que não serve para autenticar.
2. **Verificação em duas etapas.** Torna a senha roubada insuficiente. Doerfler et al. (2019) mediram que qualquer segundo fator bloqueou 100% dos ataques automatizados.
3. **Check-up de senhas.** Compara as senhas salvas com bases de vazamentos conhecidos e alerta o usuário, sem revelar a senha ao Google em texto claro.
4. **Gerenciador de senhas.** Gera senhas longas e únicas, e só preenche a senha no domínio correto, o que também serve de alerta contra páginas falsas.
5. **Análise de risco no login.** Tentativas vindas de aparelho, rede ou país incomuns recebem desafios adicionais, mesmo quando a senha está correta.
6. **Federação.** Com "Fazer login com o Google", o site de terceiros não armazena senha. Um vazamento nesse site não expõe credenciais.

### 4.4 Proteção depois do login

O roubo de *cookies* por *infostealers* contorna qualquer método de login, porque o atacante reutiliza uma sessão já autenticada. As respostas a esse risco são:

- Sessões mais curtas e reautenticação para ações sensíveis.
- Encerramento remoto de sessões em `myaccount.google.com/device-activity`.
- Vínculo da sessão ao dispositivo. A Google vem implantando no Chrome o recurso *Device Bound Session Credentials*, que associa a sessão a uma chave guardada no aparelho.
- Higiene do dispositivo: sistema atualizado, antivírus e nenhuma instalação de programas de origem duvidosa.

## 5. Riscos residuais

| Risco | Explicação | Tratamento |
|---|---|---|
| Método fraco ainda habilitado | O atacante escolhe "tentar de outro jeito" e usa o SMS | Remover ou restringir métodos fracos |
| Recuperação de conta | O processo de recuperação pode ser alvo de engenharia social | Manter dados de recuperação atualizados e protegidos |
| Conta que sincroniza as chaves de acesso | Quem a domina obtém as chaves | Proteger essa conta com fator forte |
| Ponto único de falha | A Conta Google dá acesso aos serviços federados | Proteção máxima na conta e revisão dos aplicativos conectados |
| Dispositivo comprometido | *Malware* age com a sessão do usuário | Gestão de dispositivos e sessões curtas |
| Suporte interno | Atendente redefine fator sem verificar identidade | Procedimento formal de verificação |
| Fator humano | Usuário aprova aviso indevido ou cede código | Treinamento contínuo |

## 6. Recomendações

### Para usuários

1. Criar uma chave de acesso e usá-la como forma principal de login.
2. Ativar a verificação em duas etapas e cadastrar ao menos dois fatores.
3. Imprimir os códigos de backup.
4. Usar gerenciador de senhas e fazer o Check-up de senhas.
5. Recusar avisos de login não solicitados e nunca informar códigos a terceiros.
6. Revisar a cada seis meses os dispositivos e aplicativos conectados.

### Para administradores

1. Tornar a 2SV obrigatória e proibir SMS e voz.
2. Exigir chave de segurança de administradores e de áreas sensíveis.
3. Restringir aplicativos OAuth de terceiros.
4. Manter contas de emergência e procedimento de verificação de identidade no suporte.
5. Monitorar registros de login e alertas.
6. Treinar os usuários com simulações periódicas de phishing.

### Para desenvolvedores

1. Preferir OpenID Connect a um sistema próprio de senhas.
2. Usar código de autorização com PKCE e validar `state`, `nonce` e o ID token.
3. Pedir escopos mínimos e proteger os tokens de atualização.
4. Oferecer chaves de acesso por WebAuthn nos sistemas próprios.

O detalhamento está em [docs/05-politicas-e-boas-praticas.md](docs/05-politicas-e-boas-praticas.md).

## 7. Conclusão

A senha isolada não oferece proteção adequada contra as ameaças atuais. Os segundos fatores baseados em código (SMS e TOTP) eliminam os ataques automatizados, mas cedem ao phishing em tempo real. Apenas os métodos baseados em FIDO2, isto é, chaves de segurança e chaves de acesso, resistem ao phishing por construção, porque vinculam a autenticação ao domínio verdadeiro e não dependem de um segredo que o usuário possa entregar.

A estratégia da Google caminha nessa direção: chaves de acesso como padrão, redução do SMS e exigência de fatores fortes para perfis de risco. Ainda assim, a segurança de uma conta é limitada pelo método mais fraco que continua habilitado, pelo processo de recuperação e pelo comportamento das pessoas. Por isso a adoção de métodos fortes deve vir acompanhada da retirada dos fracos, de um plano de continuidade e de educação dos usuários.

As fontes citadas estão em [referencias.md](referencias.md).
