# 5. Políticas de acesso e melhores práticas

As recomendações abaixo têm como base a NIST SP 800-63B-4, a ABNT NBR ISO/IEC 27002:2022 (controles 5.15 a 5.18 e 8.5), a RFC 9700 e a documentação da Google. Cada item indica a justificativa.

## 5.1 Política de senhas

| Item | Diretriz | Justificativa |
|---|---|---|
| Comprimento mínimo | 12 caracteres para contas com 2SV obrigatória; 15 quando a senha for o único fator | NIST SP 800-63B-4. O comprimento é o principal fator de resistência a adivinhação |
| Comprimento máximo | Aceitar pelo menos 64 caracteres | Permite frases-senha e senhas geradas por gerenciador |
| Composição | Não impor regras de maiúscula, número e símbolo | Regras de composição produzem padrões previsíveis |
| Força | Ativar "Aplicar senha forte" no Workspace | Bloqueia senhas comuns e fracas |
| Reutilização | Proibir a reutilização de senhas anteriores e de senhas de outros serviços | Reduz o impacto de vazamentos externos |
| Gerenciador | Recomendar o uso de gerenciador de senhas | Viabiliza senhas únicas e longas |
| Compartilhamento | Proibir o compartilhamento de senhas; usar delegação ou caixas colaborativas | Garante rastreabilidade individual (ISO/IEC 27002, controle 5.16) |
| Armazenamento | Nunca em texto puro, planilhas ou mensagens | Controle 5.17, informações de autenticação |

### Rotação de senhas

A troca obrigatória a cada 60 ou 90 dias, ainda comum, não é mais recomendada. A NIST SP 800-63B-4 determina que a troca só seja exigida quando houver evidência de comprometimento, pois a rotação por calendário leva os usuários a criar variações mínimas e previsíveis (`Verao2026!`, `Outono2026!`).

Política proposta:

1. Sem expiração periódica para contas com 2SV ativa.
2. Troca imediata e obrigatória nos seguintes eventos: senha encontrada em vazamento, alerta de login suspeito, infecção por *malware* no dispositivo, perda de aparelho sem bloqueio de tela, desligamento de quem conhecia uma credencial compartilhada.
3. Monitoramento contínuo com o Check-up de senhas e com os alertas da central de segurança do Workspace.
4. Se uma norma externa ou contrato exigir expiração, adotar o maior intervalo permitido e compensar com 2SV obrigatória.

## 5.2 Implantação da verificação em duas etapas

### Plano em fases para uma organização com Google Workspace

| Fase | Duração sugerida | Ações |
|---|---|---|
| 1. Preparação | 2 semanas | Levantar quem já usa 2SV (relatório do Admin Console). Definir métodos aceitos por grupo. Comprar chaves de segurança para os grupos críticos. Preparar o material de comunicação |
| 2. Piloto | 2 semanas | Ativar para a equipe de TI e voluntários. Ajustar as instruções com base nas dúvidas |
| 3. Adesão voluntária | 3 a 4 semanas | Permitir a inscrição de todos. Divulgar tutoriais e oferecer plantão de dúvidas. Acompanhar o percentual de adesão |
| 4. Obrigatoriedade | A partir de data anunciada | Ativar a aplicação obrigatória por unidade organizacional. Configurar o período de adesão para novos usuários (por exemplo, 1 semana) |
| 5. Endurecimento | Contínuo | Restringir administradores e grupos sensíveis a chave de segurança. Desativar SMS e voz onde for viável |
| 6. Manutenção | Contínuo | Revisar relatórios mensais, tratar exceções, repor chaves e reciclar o treinamento |

### Configurações recomendadas no Admin Console

Caminho: **Segurança > Autenticação > Verificação em duas etapas**.

| Configuração | Valor recomendado | Justificativa |
|---|---|---|
| Permitir que os usuários ativem a 2SV | Ativado | Pré-requisito para a adesão |
| Aplicação obrigatória | Ativada, com data definida | Sem obrigatoriedade a adesão tende a ser baixa |
| Período de adesão de novos usuários | 1 semana | Tempo para o novo colaborador configurar sem ficar bloqueado |
| Métodos permitidos, usuários em geral | Qualquer um, exceto códigos por SMS ou voz | Elimina o fator mais fraco |
| Métodos permitidos, administradores e grupos sensíveis | Somente chave de segurança | Resistência a phishing para as contas de maior impacto |
| Dispositivo confiável | Desativado para administradores | Evita que um computador comprometido dispense a segunda etapa |
| Login sem senha com chave de acesso | Permitido | Melhora a usabilidade sem reduzir a segurança |

### Regras para o usuário

1. Cadastrar pelo menos dois segundos fatores diferentes.
2. Gerar e imprimir os códigos de backup no dia da ativação.
3. Recusar qualquer aviso de login que não tenha iniciado e comunicar o ocorrido à TI.
4. Nunca informar códigos por telefone, e-mail ou aplicativo de mensagens. Nem a Google nem a equipe de TI pedem códigos.
5. Manter o bloqueio de tela ativo no celular.

## 5.3 Uso seguro dos protocolos federados

### Para desenvolvedores (OAuth 2.0 e OpenID Connect)

- [ ] Usar o fluxo de código de autorização com PKCE (`S256`).
- [ ] Gerar `state` e `nonce` aleatórios a cada pedido e conferi-los no retorno.
- [ ] Cadastrar `redirect_uri` exatas e com HTTPS.
- [ ] Solicitar apenas os escopos necessários, de forma incremental.
- [ ] Validar o ID token: assinatura, `iss`, `aud`, `exp` e `nonce`.
- [ ] Identificar o usuário pelo campo `sub`.
- [ ] Guardar `client_secret` e tokens de atualização em cofre de segredos, nunca no repositório de código.
- [ ] Revogar os tokens quando o usuário sair ou desconectar a conta.
- [ ] Usar bibliotecas oficiais ou certificadas em vez de implementar o protocolo manualmente.
- [ ] Não registrar tokens em arquivos de log.

### Para administradores (OAuth no Workspace)

- Em **Segurança > Controles de API**, restringir o acesso de aplicativos de terceiros e liberar apenas os aprovados.
- Bloquear escopos de alto risco (Gmail e Drive completos) para aplicativos não verificados.
- Revisar periodicamente o relatório de tokens concedidos e revogar os que não são mais usados.
- Orientar os usuários sobre o phishing de consentimento.

### Para administradores (SAML)

- Exigir asserções assinadas e HTTPS em todos os pontos.
- Registrar a data de validade do certificado do IdP e agendar a rotação com antecedência.
- Atribuir cada aplicativo SAML apenas às unidades organizacionais que precisam dele (menor privilégio).
- Manter ao menos uma conta de superadministrador fora do SSO, protegida por chave de segurança, para emergências.
- Remover o acesso no IdP no mesmo dia do desligamento do colaborador.

## 5.4 Controles complementares

| Controle | Descrição |
|---|---|
| Acesso baseado em contexto | Regras por localização, tipo de dispositivo e estado de segurança do aparelho (edições do Workspace que oferecem o recurso) |
| Duração da sessão | Reautenticação periódica; sessões mais curtas para administradores |
| Menor privilégio | Papéis administrativos específicos em vez de superadministrador para todos |
| Contas administrativas separadas | O administrador usa uma conta comum no dia a dia e outra para tarefas administrativas |
| Auditoria | Revisão dos registros de login, alertas de atividade suspeita e exportação para ferramenta de monitoramento |
| Conscientização | Treinamento periódico sobre phishing e engenharia social, com simulações |

## 5.5 Relação com normas

| Norma | Pontos atendidos |
|---|---|
| ABNT NBR ISO/IEC 27001:2022 | Base para o sistema de gestão no qual a política de acesso se insere |
| ABNT NBR ISO/IEC 27002:2022 | 5.15 Controle de acesso; 5.16 Gestão de identidade; 5.17 Informações de autenticação; 5.18 Direitos de acesso; 8.5 Autenticação segura |
| NIST SP 800-63B-4 | Regras de senha, classificação de autenticadores e exigência de opção resistente a phishing |
| LGPD (Lei nº 13.709/2018), art. 46 | Adoção de medidas técnicas e administrativas para proteger dados pessoais contra acesso não autorizado |
| RFC 9700 | Boas práticas de segurança para OAuth 2.0 |
| OWASP Authentication Cheat Sheet e ASVS | Requisitos de autenticação para aplicações web |
