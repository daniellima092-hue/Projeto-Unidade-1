---
title: "Da senha à chave de acesso: uma análise comparativa dos métodos de autenticação da Google quanto à resistência a phishing"
author: "Daniel Santos Lima"
date: "Instituto Federal de Sergipe (IFS), Campus Itabaiana. Segurança e Auditoria em TI, 2026.2"
lang: pt-BR
---

## Resumo

A senha continua sendo o mecanismo de autenticação mais difundido, embora seja vulnerável a phishing, vazamentos e reutilização. Este artigo analisa os métodos de autenticação oferecidos pela Google (senha, verificação em duas etapas por SMS, aplicativo autenticador, aviso no dispositivo e chave de segurança, além de chaves de acesso e dos protocolos federados OAuth 2.0, OpenID Connect e SAML 2.0) com o objetivo de identificar quais oferecem proteção efetiva contra phishing e roubo de credenciais e em que cenários cada um é adequado. Trata-se de pesquisa qualitativa, de natureza bibliográfica e documental, que confronta a documentação oficial da Google com padrões abertos, normas de referência e estudos empíricos publicados. Os resultados indicam que qualquer segundo fator neutraliza ataques automatizados, mas que apenas os métodos baseados em FIDO2 resistem a phishing em tempo real, por vincularem a autenticação à origem do site. Conclui-se que a segurança efetiva de uma conta é limitada pelo método mais fraco ainda habilitado e pelo processo de recuperação, de modo que a adoção de fatores fortes precisa ser acompanhada da retirada dos fracos, de um plano de continuidade e de educação dos usuários.

**Palavras-chave:** autenticação; verificação em duas etapas; chaves de acesso; FIDO2; phishing; identidade federada.

## Abstract

Passwords remain the most widespread authentication mechanism despite being vulnerable to phishing, breaches and reuse. This paper analyses the authentication methods offered by Google (passwords, 2-Step Verification, passkeys and the federated protocols OAuth 2.0, OpenID Connect and SAML 2.0) in order to identify which of them effectively protect against phishing and credential theft, and in which scenarios each is appropriate. It is a qualitative, bibliographic and documentary study that compares Google's official documentation with open standards, reference guidelines and published empirical research. Results indicate that any second factor defeats automated attacks, but only FIDO2-based methods withstand real-time phishing, because they bind authentication to the website's origin. We conclude that an account is only as secure as its weakest enabled method and its recovery process.

**Keywords:** authentication; 2-Step Verification; passkeys; FIDO2; phishing; federated identity.

## 1 Introdução

A autenticação é o primeiro controle de segurança de praticamente todo sistema de informação. Stallings (2015) a define como a garantia de que a entidade que se comunica é aquela que afirma ser, e a coloca como pré-requisito dos demais serviços de segurança, pois não há controle de acesso nem responsabilização sem identidade confiável.

Apesar de décadas de críticas, a senha permanece dominante. Florêncio e Herley (2007), em estudo com meio milhão de usuários, mostraram que cada pessoa mantinha em média cerca de 25 contas protegidas por poucas senhas distintas, reutilizadas entre vários sites. Bonneau et al. (2012) compararam 35 alternativas à senha e concluíram que nenhuma reunia, ao mesmo tempo, todos os benefícios de usabilidade, implantação e segurança, o que ajuda a explicar a permanência do modelo.

Esse cenário mudou nos últimos anos com a padronização do FIDO2 e a adoção de chaves de acesso (*passkeys*) pelos grandes provedores. A Google é um caso de estudo relevante por três razões: opera um dos maiores sistemas de identidade do mundo, oferece simultaneamente todos os métodos, dos mais antigos aos mais recentes, e funciona como provedor de identidade para serviços de terceiros.

A pergunta que orienta este trabalho é: **quais métodos de autenticação da Google oferecem proteção efetiva contra phishing e roubo de credenciais, e em que cenários cada um é adequado?**

O objetivo geral é analisar comparativamente esses métodos. Os objetivos específicos são: (a) descrever o funcionamento de cada mecanismo e os padrões em que se apoia; (b) compará-los quanto a segurança, usabilidade e custo; (c) relacioná-los às ameaças atuais, em especial phishing e engenharia social; e (d) propor diretrizes para usuários, administradores e desenvolvedores.

## 2 Fundamentação teórica

### 2.1 Fatores de autenticação e níveis de garantia

A literatura classifica os fatores em conhecimento, posse e inerência (STALLINGS, 2015). A NIST SP 800-63B-4 organiza os autenticadores em três níveis de garantia e introduz como requisito central a **resistência a phishing**, definida como a capacidade do protocolo de impedir que segredos ou resultados válidos sejam entregues a um verificador impostor sem depender da vigilância do usuário (TEMOSHOK et al., 2025). A mesma publicação abandona regras de composição e a troca periódica de senhas, privilegia o comprimento e classifica o envio de códigos pela rede telefônica como de uso restrito.

No contexto brasileiro, a ABNT NBR ISO/IEC 27002:2022 trata do tema nos controles de gestão de identidade, informações de autenticação e autenticação segura, e a Lei Geral de Proteção de Dados exige medidas técnicas aptas a proteger dados pessoais contra acessos não autorizados (BRASIL, 2018).

### 2.2 Senhas e segundos fatores

O risco da senha decorre de ela ser um segredo compartilhado e reutilizável. Thomas et al. (2017) analisaram, ao longo de um ano, credenciais expostas por vazamentos, kits de phishing e *keyloggers*, e verificaram que as vítimas de phishing apresentavam probabilidade de tomada de conta muito superior à das vítimas de vazamentos, porque os kits capturam também informações usadas nos desafios de verificação.

Os segundos fatores baseados em códigos de uso único seguem os padrões HOTP e TOTP (M'RAIHI et al., 2005; M'RAIHI et al., 2011). Embora elevem o custo do ataque, o código continua sendo um segredo que o usuário digita e, portanto, pode ser capturado por uma página intermediária.

### 2.3 FIDO2 e chaves de acesso

O FIDO2 combina a API WebAuthn, do W3C, com o protocolo CTAP, da FIDO Alliance. A autenticação se dá por assinatura digital de um desafio com chave privada que nunca deixa o autenticador, e a assinatura inclui a origem informada pelo navegador (W3C, 2021). Lang et al. (2016) descrevem a implantação de chaves de segurança na Google e relatam ganhos de segurança e de usabilidade em relação a códigos de uso único. Chaves de acesso são credenciais FIDO2 detectáveis, que podem ser sincronizadas entre dispositivos e dispensam a senha.

### 2.4 Identidade federada

O OAuth 2.0 é um arcabouço de autorização delegada (HARDT, 2012). O OpenID Connect acrescenta a ele uma camada de identidade por meio do ID token (SAKIMURA et al., 2014). O SAML 2.0, baseado em XML, é o padrão predominante para *single sign-on* corporativo (OASIS, 2005). Fett, Küsters e Schmitz (2016) realizaram uma análise formal do OAuth 2.0 e identificaram ataques que motivaram correções hoje consolidadas na RFC 9700, a qual torna o PKCE recomendado para todos os clientes e proíbe o fluxo implícito (LODDERSTEDT et al., 2025).

## 3 Metodologia

A pesquisa é qualitativa, de caráter exploratório e descritivo, e utiliza procedimentos bibliográficos e documentais. Não foram realizados experimentos nem testes contra contas reais.

O levantamento abrangeu três conjuntos de fontes: (i) documentação oficial da Google para usuários, administradores do Workspace e desenvolvedores; (ii) especificações e normas, incluindo RFCs do IETF, documentos da OpenID Foundation, OASIS, W3C e FIDO Alliance, a NIST SP 800-63B-4 e a ABNT NBR ISO/IEC 27002:2022; e (iii) estudos acadêmicos revisados por pares sobre senhas, segundos fatores e protocolos federados.

A comparação adotou seis critérios, adaptados do quadro de Bonneau et al. (2012): resistência a phishing, resistência a roubo remoto, usabilidade, custo, disponibilidade e facilidade de recuperação. Cada método foi classificado em três níveis para cada critério, com base nas propriedades descritas nas especificações e, quando disponíveis, em dados empíricos. Em seguida, os métodos foram confrontados com uma lista de ameaças e associados a cenários de uso.

Como limitações, reconhece-se que a classificação em três níveis envolve julgamento do autor, que os dados empíricos disponíveis provêm em boa parte de estudos conduzidos pela própria Google e que as interfaces e políticas da empresa mudam com frequência.

## 4 Resultados e discussão

### 4.1 Comparação dos métodos

O Quadro 1 sintetiza a classificação obtida.

**Quadro 1 – Comparação dos métodos de autenticação da Google**

| Método | Resistência a phishing | Resistência a roubo remoto | Usabilidade | Custo | Recuperação |
|---|---|---|---|---|---|
| Senha isolada | Baixa | Baixa | Média | Nenhum | Fácil |
| Senha + SMS ou voz | Baixa | Baixa | Média | Nenhum | Média |
| Senha + aplicativo autenticador | Baixa | Média | Média | Nenhum | Difícil |
| Senha + aviso do Google | Média | Média | Alta | Nenhum | Média |
| Senha + chave de segurança | Alta | Alta | Média | Compra das chaves | Difícil |
| Chave de acesso | Alta | Alta | Alta | Nenhum | Média |

Fonte: elaborado pelo autor.

A chave de acesso é o único método que reúne classificação alta em segurança e em usabilidade sem custo de aquisição. Trata-se de um resultado relevante à luz de Bonneau et al. (2012), para quem nenhuma alternativa conhecida à época superava a senha no conjunto dos critérios.

### 4.2 Evidência empírica

Doerfler et al. (2019) mediram a eficácia dos desafios de login da Google contra tentativas reais de tomada de conta (Quadro 2).

**Quadro 2 – Percentual de ataques bloqueados por tipo de desafio**

| Desafio | Robôs automatizados | Phishing em massa | Ataques direcionados |
|---|---|---|---|
| Código por SMS | 100% | 96% | 76% |
| Aviso no dispositivo | 100% | 99% | 90% |
| Chave de segurança | 100% | 100% | 100% |

Fonte: Doerfler et al. (2019).

Os dados sustentam duas conclusões. A primeira é que ativar qualquer segundo fator já elimina a maior parte do risco, o que justifica tornar a verificação em duas etapas obrigatória mesmo quando só o SMS é viável. A segunda é que a diferença entre os métodos se manifesta nos ataques direcionados, justamente os que atingem administradores, executivos e pessoas expostas. Nesse grupo, somente o fator vinculado à origem manteve bloqueio total. A experiência interna da Google aponta no mesmo sentido: a empresa informou não ter registrado contas de funcionários comprometidas por phishing depois de adotar chaves de segurança (KREBS, 2018).

### 4.3 Por que o vínculo com a origem é decisivo

Nos métodos baseados em código, a decisão de confiar no site cabe ao usuário. Em um ataque de adversário no meio, a página falsa repassa ao Google a senha e o código em tempo real e captura a sessão. No FIDO2, a origem é informada pelo navegador e entra no dado assinado. Em um domínio falso não existe credencial correspondente, e nada aproveitável é produzido. A proteção deixa de depender de atenção humana, que é exatamente o requisito da NIST SP 800-63B-4 para resistência a phishing.

### 4.4 O elo mais fraco

A análise mostrou que a Google mantém, por compatibilidade, métodos antigos ao lado dos novos. Uma conta com chave de acesso costuma conservar a senha e, muitas vezes, o SMS. Como o atacante escolhe o caminho, a segurança real é a do método mais fraco ainda habilitado e a do processo de recuperação. Esse ponto é pouco percebido pelos usuários e explica por que o Programa Proteção Avançada e a opção "somente chave de segurança" do Workspace atuam restringindo alternativas, e não apenas acrescentando fatores.

A sincronização das chaves de acesso merece a mesma leitura crítica. Ela resolve o problema da perda do dispositivo, que historicamente limitou a adoção de chaves físicas, mas desloca a confiança para a conta que realiza a sincronização.

### 4.5 Engenharia social e fator humano

Os métodos fortes deslocam o ataque do protocolo para as pessoas e os processos. Aparecem então a fadiga de MFA, em que avisos repetidos induzem a aprovação, o phishing de consentimento, que explora a tela legítima do OAuth para obter acesso sem roubar credenciais, e a fraude contra a central de suporte, em que o atacante se passa por funcionário para redefinir o segundo fator. Nenhuma dessas técnicas é contida apenas por tecnologia. Elas exigem controles administrativos: restrição de aplicativos de terceiros, verificação de identidade no atendimento e treinamento recorrente.

### 4.6 Protocolos federados

O login federado reduz a superfície de ataque do serviço de terceiros, que deixa de armazenar senhas, e estende a ele a proteção da Conta Google. Em contrapartida, concentra o risco: a conta do provedor de identidade torna-se ponto único de falha. A segurança depende também da implementação. Os erros mais comuns identificados na documentação e na literatura são a ausência de PKCE e de validação do parâmetro `state` no OAuth 2.0, a aceitação do ID token sem conferir assinatura e destinatário no OpenID Connect, e a validação incompleta da assinatura XML no SAML. Para aplicações novas, o OpenID Connect com código de autorização e PKCE mostrou-se a opção mais simples de implementar corretamente. O SAML permanece indicado quando o sistema corporativo já o suporta.

### 4.7 Cenários de uso

Da comparação resultam as seguintes recomendações: chave de acesso como padrão para usuários domésticos; verificação em duas etapas obrigatória, sem SMS, para pequenas organizações; chave de segurança física em duplicidade para administradores e áreas sensíveis; Programa Proteção Avançada para pessoas sob risco de ataque direcionado; e SMS apenas quando não houver alternativa. Em todos os casos, ao menos dois fatores e códigos de backup devem ser mantidos para garantir a continuidade do acesso.

## 5 Conclusão

O estudo respondeu à pergunta de pesquisa ao mostrar que, entre os métodos oferecidos pela Google, somente as chaves de segurança e as chaves de acesso, ambas baseadas em FIDO2, oferecem resistência a phishing por construção. Os demais segundos fatores são eficazes contra ataques automatizados e de massa, mas falham diante de phishing em tempo real e de engenharia social.

A principal contribuição é evidenciar que a escolha do método forte é condição necessária, porém não suficiente. A segurança efetiva depende de três elementos adicionais: a retirada ou restrição dos métodos fracos, um processo de recuperação de força equivalente e a preparação das pessoas e do suporte contra engenharia social. Para desenvolvedores, a recomendação é adotar OpenID Connect com as práticas da RFC 9700 em vez de manter sistemas próprios de senha.

Como trabalhos futuros, sugere-se medir a adoção e a percepção de usabilidade de chaves de acesso entre estudantes e servidores da instituição, avaliar experimentalmente a implantação de verificação em duas etapas obrigatória em um domínio de testes do Google Workspace e analisar mecanismos de proteção da sessão após o login, como o vínculo de credenciais de sessão ao dispositivo.

## Referências

ASSOCIAÇÃO BRASILEIRA DE NORMAS TÉCNICAS. **ABNT NBR ISO/IEC 27002:2022**: segurança da informação, segurança cibernética e proteção à privacidade: controles de segurança da informação. Rio de Janeiro: ABNT, 2022.

BONNEAU, J.; HERLEY, C.; VAN OORSCHOT, P. C.; STAJANO, F. The quest to replace passwords: a framework for comparative evaluation of web authentication schemes. *In*: IEEE SYMPOSIUM ON SECURITY AND PRIVACY, 2012, San Francisco. **Proceedings** [...]. Piscataway: IEEE, 2012. p. 553-567.

BRASIL. **Lei nº 13.709, de 14 de agosto de 2018**. Lei Geral de Proteção de Dados Pessoais (LGPD). Brasília, DF: Presidência da República, 2018. Disponível em: https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm. Acesso em: 15 nov. 2026.

DOERFLER, P. *et al*. Evaluating login challenges as a defense against account takeover. *In*: THE WORLD WIDE WEB CONFERENCE, 2019, San Francisco. **Proceedings** [...]. New York: ACM, 2019. p. 372-382.

FETT, D.; KÜSTERS, R.; SCHMITZ, G. A comprehensive formal security analysis of OAuth 2.0. *In*: ACM SIGSAC CONFERENCE ON COMPUTER AND COMMUNICATIONS SECURITY, 2016, Vienna. **Proceedings** [...]. New York: ACM, 2016. p. 1204-1215.

FLORÊNCIO, D.; HERLEY, C. A large-scale study of web password habits. *In*: INTERNATIONAL CONFERENCE ON WORLD WIDE WEB, 16., 2007, Banff. **Proceedings** [...]. New York: ACM, 2007. p. 657-666.

HARDT, D. (ed.). **The OAuth 2.0 authorization framework**. RFC 6749. [*S. l.*]: IETF, 2012. Disponível em: https://www.rfc-editor.org/rfc/rfc6749. Acesso em: 15 nov. 2026.

KREBS, B. Google: security keys neutralized employee phishing. **Krebs on Security**, 23 jul. 2018. Disponível em: https://krebsonsecurity.com/2018/07/google-security-keys-neutralized-employee-phishing/. Acesso em: 15 nov. 2026.

LANG, J. *et al*. Security keys: practical cryptographic second factors for the modern web. *In*: INTERNATIONAL CONFERENCE ON FINANCIAL CRYPTOGRAPHY AND DATA SECURITY, 20., 2016, Christ Church. **Proceedings** [...]. Berlin: Springer, 2017. p. 422-440.

LODDERSTEDT, T. *et al*. **Best current practice for OAuth 2.0 security**. RFC 9700. [*S. l.*]: IETF, 2025. Disponível em: https://www.rfc-editor.org/rfc/rfc9700. Acesso em: 15 nov. 2026.

M'RAIHI, D. *et al*. **HOTP**: an HMAC-based one-time password algorithm. RFC 4226. [*S. l.*]: IETF, 2005. Disponível em: https://www.rfc-editor.org/rfc/rfc4226. Acesso em: 15 nov. 2026.

M'RAIHI, D. *et al*. **TOTP**: time-based one-time password algorithm. RFC 6238. [*S. l.*]: IETF, 2011. Disponível em: https://www.rfc-editor.org/rfc/rfc6238. Acesso em: 15 nov. 2026.

OASIS. **Assertions and protocols for the OASIS Security Assertion Markup Language (SAML) V2.0**. [*S. l.*]: OASIS, 2005. Disponível em: https://docs.oasis-open.org/security/saml/v2.0/saml-core-2.0-os.pdf. Acesso em: 15 nov. 2026.

SAKIMURA, N. *et al*. **OpenID Connect Core 1.0**. [*S. l.*]: OpenID Foundation, 2014. Disponível em: https://openid.net/specs/openid-connect-core-1_0.html. Acesso em: 15 nov. 2026.

STALLINGS, W. **Criptografia e segurança de redes**: princípios e práticas. 6. ed. São Paulo: Pearson, 2015.

TEMOSHOK, D. *et al*. **Digital identity guidelines**: authentication and authenticator management. NIST SP 800-63B-4. Gaithersburg: NIST, 2025. Disponível em: https://doi.org/10.6028/NIST.SP.800-63B-4. Acesso em: 15 nov. 2026.

THOMAS, K. *et al*. Data breaches, phishing, or malware? Understanding the risks of stolen credentials. *In*: ACM SIGSAC CONFERENCE ON COMPUTER AND COMMUNICATIONS SECURITY, 2017, Dallas. **Proceedings** [...]. New York: ACM, 2017. p. 1421-1434.

W3C. **Web Authentication**: an API for accessing public key credentials: level 2. [*S. l.*]: W3C, 2021. Disponível em: https://www.w3.org/TR/webauthn-2/. Acesso em: 15 nov. 2026.
