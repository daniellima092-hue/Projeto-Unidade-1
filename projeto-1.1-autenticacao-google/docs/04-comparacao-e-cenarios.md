# 4. Comparação dos métodos e cenários de uso

## 4.1 Critérios

Os métodos foram comparados por seis critérios, inspirados no quadro de avaliação de Bonneau et al. (2012), que divide os benefícios de um esquema de autenticação em segurança, usabilidade e facilidade de implantação.

| Critério | Pergunta |
|---|---|
| Resistência a phishing | Se o usuário for enganado por um site falso, o atacante consegue entrar? |
| Resistência a roubo remoto | O fator pode ser obtido à distância (vazamento, interceptação, *SIM swap*)? |
| Usabilidade | Quanto esforço exige em cada login? |
| Custo | Há gasto com equipamento ou serviço? |
| Disponibilidade | Funciona sem internet, sem sinal, sem bateria? |
| Recuperação | É fácil voltar a ter acesso depois de perder o fator? |

## 4.2 Quadro comparativo

Legenda: ●●● alto, ●●○ médio, ●○○ baixo.

| Método | Resistência a phishing | Resistência a roubo remoto | Usabilidade | Custo | Disponibilidade | Recuperação |
|---|---|---|---|---|---|---|
| Senha isolada | ●○○ | ●○○ | ●●○ | Nenhum | ●●● | ●●● |
| Senha + SMS ou voz | ●○○ | ●○○ | ●●○ | Nenhum | ●●○ | ●●○ |
| Senha + aplicativo autenticador | ●○○ | ●●○ | ●●○ | Nenhum | ●●● | ●○○ |
| Senha + aviso do Google | ●●○ | ●●○ | ●●● | Nenhum | ●●○ | ●●○ |
| Senha + chave de segurança | ●●● | ●●● | ●●○ | Compra das chaves | ●●● | ●○○ |
| Chave de acesso | ●●● | ●●● | ●●● | Nenhum | ●●● | ●●○ |
| Código de backup | ●○○ | ●●○ | ●○○ | Nenhum | ●●● | Não se aplica |

## 4.3 Evidência empírica

Um estudo da própria Google com a Universidade de Nova York e a Universidade da Califórnia em San Diego mediu a eficácia de cada desafio contra tomadas de conta reais (DOERFLER et al., 2019).

| Desafio | Bloqueio de robôs automatizados | Bloqueio de phishing em massa | Bloqueio de ataques direcionados |
|---|---|---|---|
| Código por SMS | 100% | 96% | 76% |
| Aviso no dispositivo | 100% | 99% | 90% |
| Chave de segurança | 100% | 100% | 100% |

Os números mostram duas coisas. Primeiro, qualquer segundo fator elimina os ataques automatizados, que são a grande maioria. Segundo, a diferença entre os métodos aparece nos ataques direcionados, em que o SMS deixa passar cerca de um quarto das tentativas e a chave de segurança não deixa passar nenhuma.

## 4.4 Cenários e justificativas

| Cenário | Método recomendado | Método alternativo | Justificativa |
|---|---|---|---|
| Usuário doméstico comum | Chave de acesso no celular | Aviso do Google + códigos de backup impressos | Sem custo, simples e resistente a phishing, que é a principal ameaça a esse perfil |
| Pessoa sem smartphone ou com aparelho antigo | Senha longa + SMS ou voz | Códigos de backup | O SMS é fraco, mas é muito melhor do que só a senha |
| Estudante que usa computadores de laboratório | Chave de acesso no celular com login por QR | Aviso do Google | Nada fica guardado na máquina compartilhada; encerrar a sessão ao sair |
| Pequena empresa com Workspace | 2SV obrigatória com aviso do Google ou chave de acesso | Aplicativo autenticador | Proteção forte sem compra de equipamento, adequada a equipes sem setor de TI |
| Administradores do Workspace | Somente chave de segurança, com duas chaves por pessoa | Nenhum método mais fraco | A conta de administrador controla todas as demais; o custo das chaves é pequeno perante o risco |
| Financeiro, diretoria e RH | Chave de segurança ou chave de acesso | Aviso do Google com tolerância curta | São os alvos preferenciais de phishing direcionado e de fraude de pagamento |
| Jornalistas, ativistas, autoridades | Programa Proteção Avançada | Nenhum | Perfil sujeito a ataques direcionados e persistentes |
| Colaborador em campo, sem sinal | Aplicativo autenticador ou chave de segurança | Códigos de backup | Ambos funcionam sem internet e sem operadora |
| Sistema corporativo interno | SSO por SAML 2.0 com o Workspace como IdP | OpenID Connect | Centraliza a política de autenticação e facilita o desligamento de colaboradores |
| Site ou aplicativo público novo | "Fazer login com o Google" via OpenID Connect | Chave de acesso própria | O site não guarda senhas e herda a proteção da Conta Google |
| Aplicativo que acessa dados do usuário na Google | OAuth 2.0 com código de autorização e PKCE | Fluxo de dispositivo para TVs | É o fluxo recomendado pela RFC 9700 |

## 4.5 Ordem de preferência

Como síntese, a ordem de preferência para proteger uma Conta Google é:

1. Chave de acesso ou chave de segurança física.
2. Aviso do Google.
3. Aplicativo autenticador.
4. SMS ou chamada de voz, apenas quando não houver outra opção.

Os códigos de backup acompanham qualquer uma das escolhas, como reserva. A senha, quando mantida, deve ser longa, única e guardada em gerenciador.

## 4.6 Análise crítica

Nenhum método é perfeito. A chave de segurança tem a melhor proteção, mas o custo e o risco de perda dificultam a adoção em massa. A chave de acesso sincronizada resolve esses dois problemas, porém transfere a confiança para o provedor da sincronização: quem tomar a conta do gerenciador de senhas passa a ter as chaves. Além disso, a segurança real de uma conta é a do seu **método mais fraco ainda habilitado**. Uma conta com chave de acesso que mantém recuperação por SMS continua exposta ao *SIM swap*. Por isso a recomendação vai além de adotar o método forte e inclui retirar ou restringir os métodos fracos sempre que possível.
