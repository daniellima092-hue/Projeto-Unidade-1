# Guia para usuários domésticos e pequenas empresas

Adaptação das recomendações do projeto para quem não tem equipe de TI, com material de educação do usuário.

## Parte 1: proteção da conta em 15 minutos

| Passo | Ação | Tempo |
|---|---|---|
| 1 | Abrir `myaccount.google.com/security-checkup` e seguir as sugestões | 3 min |
| 2 | Criar uma chave de acesso no celular | 2 min |
| 3 | Ativar a verificação em duas etapas | 3 min |
| 4 | Gerar os códigos de backup, imprimir e guardar com os documentos | 3 min |
| 5 | Conferir o telefone e o e-mail de recuperação | 2 min |
| 6 | Fazer o Check-up de senhas e trocar as que aparecerem como vazadas | 2 min |

O passo a passo ilustrado está em [exemplos/ativacao-2sv.md](../exemplos/ativacao-2sv.md).

## Parte 2: cinco regras para o dia a dia

1. **Ninguém legítimo pede código.** O Google, o banco e a operadora nunca ligam ou mandam mensagem pedindo o código recebido.
2. **Não pediu, não aprove.** Diante de um aviso de login não iniciado, tocar em "Não" e trocar a senha.
3. **Desconfie da pressa.** Mensagens que ameaçam bloquear a conta em poucas horas são o padrão dos golpes.
4. **Entre pelo endereço, não pelo link.** Digitar `google.com` ou usar o aplicativo, em vez de clicar no link da mensagem.
5. **Uma senha para cada lugar.** Deixar o gerenciador de senhas criar e lembrar.

## Parte 3: como reconhecer uma tentativa de phishing

| Sinal | Exemplo |
|---|---|
| Endereço estranho | `accounts-google.seguranca-login.com` em vez de `accounts.google.com` |
| Urgência ou ameaça | "Sua conta será excluída em 24 horas" |
| Pedido de código ou senha | "Informe o código para cancelar a compra" |
| Remetente parecido, mas diferente | `suporte@g00gle-ajuda.com` |
| Anexo ou código QR inesperado | "Escaneie para regularizar seu acesso" |
| O gerenciador de senhas não preenche | Sinal de que o domínio não é o verdadeiro |

Com chave de acesso, o golpe da página falsa deixa de funcionar, porque a chave só responde ao site verdadeiro.

## Parte 4: o que fazer quando algo dá errado

| Situação | Ação |
|---|---|
| Celular perdido | Entrar por outro aparelho ou com código de backup, encerrar a sessão do celular perdido e bloquear o chip na operadora |
| Aviso aprovado por engano | Trocar a senha na hora e encerrar as outras sessões |
| Código informado a um golpista | Trocar a senha e revisar os fatores cadastrados e os dados de recuperação |
| Sem acesso à conta | Usar `accounts.google.com/signin/recovery` a partir de um aparelho e de uma rede de uso habitual |

Os procedimentos completos estão em [docs/06-continuidade-e-redundancia.md](../docs/06-continuidade-e-redundancia.md).

## Parte 5: pequenas empresas (até 20 pessoas)

| Item | Recomendação de baixo custo |
|---|---|
| Contas | Uma conta por pessoa. Em vez de conta compartilhada do tipo `contato@` com senha conhecida por todos, usar grupo ou caixa colaborativa |
| 2SV | Obrigatória para todos, com aviso do Google ou chave de acesso, que não têm custo |
| Administrador | Duas pessoas com acesso de administrador, cada uma com duas chaves de segurança |
| Senhas | Mínimo de 12 caracteres, sem troca periódica, com gerenciador de senhas |
| Saída de funcionário | Suspender a conta no mesmo dia e transferir os arquivos |
| Dispositivos | Bloqueio de tela obrigatório em celulares e notebooks |
| Treinamento | Uma conversa de 20 minutos por semestre com as cinco regras da Parte 2 |
| Emergência | Códigos de backup do administrador guardados em envelope lacrado, em cofre ou com o proprietário |

## Parte 6: material de conscientização

Cartaz para o mural ou para o grupo de mensagens da empresa:

> **Antes de aprovar, pergunte: fui eu que pedi?**
>
> - Código é como chave de casa: não se empresta.
> - Mensagem com pressa demais é golpe.
> - Na dúvida, pare e pergunte a quem cuida da TI.

Exercício rápido para a equipe: mostrar três telas de login, uma verdadeira e duas falsas, e pedir que as pessoas apontem as diferenças no endereço.
