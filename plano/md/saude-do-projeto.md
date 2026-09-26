# Nota de saúde do projeto (0 a 10)

Ferramenta de diagnóstico. Não é etapa, não tem "pronto quando", e roda quando alguém quiser saber como o projeto está: antes de retomar depois de meses, antes de abrir pro primeiro usuário, ou quando bateu a sensação de que a casa desarrumou.

**Funciona em qualquer projeto**, tenha ele nascido deste molde ou não. Projeto que nunca viu o molde só vai tirar nota baixa em algumas dimensões, e é exatamente essa a informação.

---

## As três regras de quem aplica

1. **Não corrigir durante a análise.** Vale a mesma regra da Etapa 13: cada achado ganha destino decidido junto com o dono (corrigir já, pegar carona numa etapa, ou entrar na fila de erros). Quem corrige calado no meio da varredura perde a medida e não termina nenhuma das duas coisas.
2. **Nota sem evidência não vale.** Toda nota abaixo de 8 aponta arquivo e linha, ou o comando que provou. "Parece frágil" não é achado.
3. **Medir o que está lá, não o que se lembra.** Ler o código, rodar o que der pra rodar. Impressão de sessão antiga é a principal fonte de nota errada.

---

## As 8 dimensões

Cada uma vale de 0 a 10. O que cada nota significa está na régua do fim.

### 1. Segurança
Segredo versionado (senha, token, chave em arquivo do repo ou em migration). Autorização conferida rota a rota, com o padrão sendo negar. Senha com hash forte. Freio de força bruta no login. Upload validado por assinatura do arquivo, não pelo tipo declarado. Dependência com vulnerabilidade aberta.
**Prova rápida:** buscar por senha e chave no histórico do git; bater três papéis (anônimo, comum, admin) contra uma rota administrativa; conferir o alerta de dependência do repo.

### 2. Teste
Existe arnês rodando por um comando. Ele cobre o caminho crítico: autenticação, salvar com validação recusando, excluir. Roda contra banco de verdade. Bug já corrigido deixou teste que o reproduz.
**Prova rápida:** rodar a suíte. Se não roda por um comando, a nota já começa baixa, porque teste que ninguém consegue rodar não protege ninguém.

### 3. Legibilidade
Função com uma responsabilidade e tamanho que cabe na tela. Nome que dispensa comentário. HTML semântico. CSS e JS em arquivo por componente, não dentro do template. Pasta que um humano entende sem buscar.
**Prova rápida:** contar as funções maiores que ~60 linhas e as linhas de estilo e script inline dentro de template.

### 4. Duplicação
Clone do mesmo padrão (controller, modal, store, formulário, mapeador). Decisão repetida em N lugares: mudar o texto de um aviso comum exige lembrar de quantos arquivos?
**Prova rápida:** escolher um aviso ou uma regra que aparece em vários lugares e contar em quantos arquivos ela mora.

### 5. Banco e dados
Migrations versionadas e nunca editadas depois de aplicadas. Banco novo sobe do zero sem passo manual. Índice nas chaves estrangeiras e nas colunas de busca. Regra de exclusão pensada. Enum do código batendo com a restrição do banco.
**Prova rápida:** recriar o banco do zero. Se precisar de passo manual, a dimensão não passa de 5.

### 6. Deploy e operação
Procedimento de subir escrito e testado. Caminho de volta (rollback) que alguém já usou. Backup rodando, guardado fora da máquina, restaurado ao menos uma vez, com alerta quando falha. Monitoramento que avisa antes do usuário avisar.
**Prova rápida:** perguntar quando foi a última restauração de backup testada. "Nunca" é nota 3 ou menos, porque backup nunca testado não é backup.

### 7. Interface
Sem estouro horizontal nos 4 tamanhos. Acessibilidade: rótulo ligado ao campo, foco visível, alcançável por teclado. Estado vazio tratado. Imagem com dimensão declarada. Política de conteúdo (CSP) ligada.
**Prova rápida:** medir uma página pública e uma de painel num medidor de acessibilidade, e abrir as duas no tamanho de celular.

### 8. Registro
O plano (ou o README) reflete o que existe hoje. Decisões escritas com o porquê. Fila de erros viva. README ensina alguém de fora a subir o projeto do zero.
**Prova rápida:** seguir o README numa máquina limpa, ou ao menos ler se ele menciona os passos que a versão atual precisa.

---

## Como fecha a nota final

Média simples das oito, **com um freio**: se qualquer dimensão ficar em 3 ou menos, a nota final não passa de 5, por mais alto que esteja o resto. Projeto com segredo exposto em produção não é um "8 com uma ressalva", e média sozinha esconde exatamente esse tipo de buraco.

| Nota | O que significa |
|---|---|
| 9-10 | saudável. Dá pra crescer sem medo |
| 7-8 | bom, com dívida conhecida e escrita |
| 5-6 | funciona, mas cada mudança custa mais que devia |
| 3-4 | a casa desarrumou. Precisa de etapa dedicada antes de feature nova |
| 0-2 | risco real de perder dado, vazar dado ou não conseguir mudar |

---

## O que entregar

Uma tabela e três linhas de texto. Nada mais:

```markdown
| # | Dimensão | Nota | O que puxou pra baixo (arquivo:linha) |
|---|---|---|---|
| 1 | Segurança | 4 | senha no repo em V3__seed.sql:12 |
...

**Nota final: X/10** (freio aplicado: sim/não, por qual dimensão)

**As três coisas que mais sobem a nota:** ...
**O que NÃO vale mexer agora:** ...
```

A linha do que não vale mexer agora é tão importante quanto as outras. Varredura sem prioridade vira lista de 40 itens que ninguém ataca, e a casa segue desarrumada com um relatório em cima.

---

## Resultado

**Auditado em:** 26/09/2026

| # | Dimensão | Nota | O que puxou pra baixo (arquivo:linha) |
|---|---|---|---|
| 1 | Segurança | 9 | sem segredo no histórico (`git log -p --all` sem senha, token nem chave) e sem formulário; os links externos usam `rel="noopener"`. O que separa do 10: cada visita a uma página de balada liberal avisa Google Fonts e Wix (`index.html:12`, `:17`, `:243`, `:1060`). Os telefones do WhatsApp (`index.html:974` e mais 8 links) são o contato público do evento, num repositório público no GitHub (responde 200 sem login) |
| 2 | Teste | 0 | nada: o repo tem dois arquivos (`git ls-files`: `index.html` e `img/banner.png`) e nenhuma prova guardada. O que esta auditoria mediu (ver Interface) não fica no projeto |
| 3 | Legibilidade | 5 | a página inteira mora num arquivo: 935 linhas de CSS no `<style>` (`index.html:20` a `:956`), 16 de script inline (`:1242` a `:1259`) e 5 atributos `style=""` (`:1036`, `:1050`, `:1112`, `:1184`, `:1197`); a classe `.hero-actions` é usada fora do hero e corrigida na mão com `style` (`:1036`, `:1184`); o passo a passo pula de `h2` pra `h4` (`:1164`). A favor: CSS separado por seção com comentário, cores em variáveis (`:21` a `:33`) e `header`, `nav`, `section` e `footer` de verdade |
| 4 | Duplicação | 6 | o número principal do WhatsApp mora em 8 links (`index.html:974`, `:997`, `:1038`, `:1186`, `:1203`, `:1218`, `:1219`, `:1237`) e a mensagem padrão em 5; o SVG do WhatsApp é colado 3 vezes (`:1205`, `:1211`, `:1239`); "22h às 4h" aparece em 5 lugares (`:993`, `:1025`, `:1145`, `:1147`, `:1229`); a regra do `em` dourado dos títulos está 4 vezes (`:99`, `:467`, `:756`, `:815`) e o dourado translúcido `rgba(201, 161, 90, …)` 14 vezes fora das variáveis |
| 5 | Banco e dados | - | não se aplica: página estática, sem dado guardado |
| 6 | Deploy e operação | 4 | tem git com remoto no GitHub, `main` igual a `origin/main`, e o reclone de 23/08 (`.claude/repositorios-ativos.md:17`) provou que a cópia de fora restaura; mas não há procedimento de publicar nem endereço no ar escrito em lugar nenhum (o GitHub Pages do repo responde 404 e nenhum script do `c:\src` cita a pasta). As fotos vêm do Wix por link, e duas já caíram sem ninguém saber: o fundo do hero (`index.html:243`) e o favicon (`:12`) respondem 403 hoje |
| 7 | Interface | 5 | medido por `file://` em 360, 768, 1024 e 1440: sem estouro horizontal nos 4. Mas a primeira dobra abre só com o degradê, porque a foto do hero dá 403 (`index.html:243`); o bloco "Próximo Evento" anuncia "Sábado · 26/06" (`:1021`), três meses atrás; axe-core (WCAG A/AA) acusa 1 violação, o contraste do `.copy` do rodapé (`:912` a `:917`); as duas imagens não declaram `width` e `height` (`:1011`, `:1060`) e o flyer é um PNG de 782 KB pra 540x777 (`img/banner.png`); sem CSP; abaixo de 920px o menu some sem substituto (`:213` a `:224`). A favor: `prefers-reduced-motion` tratado (`:951`) e o botão flutuante com `aria-label` (`:1238`) |
| 8 | Registro | 2 | sem README nem plano; três commits de 09/06 com mensagem "primeiro commit", "INDEX" e "banner" (`git log`); fora do projeto, uma linha em `.claude/repositorios-ativos.md:17`. Nada diz pra quem é a página, se foi entregue, onde está no ar nem de onde vêm as fotos do Wix |

**Nota final: 4,4/10** (freio aplicado: sim, por Teste (0) e Registro (2); média das 7 dimensões que se aplicam, sem Banco, e já fica abaixo do teto de 5)

**As três coisas que mais sobem a nota:** um README que diga pra quem é a página, onde ela mora e como publica, que tira Registro e Deploy do chão; trazer as três fotos do Wix pra `img/` com `width` e `height` (conserta o hero e o favicon quebrados e corta a dependência de fora); e atualizar ou tirar o bloco "Próximo Evento" junto com o contraste do rodapé, que é o que o visitante vê primeiro.

**O que NÃO vale mexer agora:** partir o CSS em arquivos por componente numa página única que ninguém está mexendo (vai de carona na próxima mudança de verdade); montar arnês de teste antes de decidir se a página vai ao ar; e trocar os 8 links repetidos por JS que monte os endereços, que troca HTML simples por dependência de script.
