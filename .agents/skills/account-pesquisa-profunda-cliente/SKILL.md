---
name: account-pesquisa-profunda-cliente
description: Pesquisa profunda de cliente para KB acionavel. Orienta coleta de dados internos com o cliente; entrega 4 prompts sequenciais para rodar no Deep Research do Gemini (cliente+digital+regiao; produto+setor; consumidor; concorrencia). Opcional Perplexity com foco social. Nao usa Gem nem deep research no agente. Use antes de copy, conteudo, campanha ou LP.
area: account
author: guilhermelippert
version: 1.2.0
---

# /account-pesquisa-profunda-cliente

Conduza o usuario a montar **Knowledge Base do cliente** para gerar resultado em copy, conteudo, campanha, LP/funil e decisoes.

**O agente normal nao faz deep research.** **Nao use Gem** nem orquestrador externo. O fluxo e:

1. Orientar o usuario a **pegar do cliente** (ou da pasta) tudo que for possivel.
2. Entregar **quatro prompts prontos** para o usuario colar no **Deep Research do Gemini** — **uma rodada por vez**, na ordem. Entre rodadas, o usuario anexa/cola o relatorio anterior no Gemini junto com o proximo prompt.
3. **Opcional:** um prompt para **Perplexity** com foco em social/reviews (no fim).

## Principios

- Nao invente fatos. Separe evidencias, hipoteses e lacunas.
- Material interno (brief, CRM, transcricao, kickoff) tem **prioridade** sobre inferencia da web.
- Nao crie pasta de cliente aqui. Cliente inexistente → `/novo-cliente`.
- **Gemini Deep Research:** quatro execucoes sequenciais conforme templates abaixo.
- **Perplexity:** complemento social com links (opcional).
- Ad libraries e scraping manual: `references/fontes-pesquisa-social.md`.

## Passo 1 — Identificar cliente e pasta

1. Nome/slug do cliente; se ambiguo, liste `clientes/` (ignore `_template`) e pergunte.
2. Se nao existir pasta → mensagem fixa para `/novo-cliente`.
3. Trabalhe em `clientes/<cliente>/docs/pesquisa-profunda/`.

Estrutura a criar ou completar:

```text
clientes/<cliente>/docs/pesquisa-profunda/
├── 00-briefing-pesquisa.md
├── 01-cliente-posicionamento-digital.md   # sintese pos DR-1
├── 02-mercado-setor-modelo-negocio.md     # sintese pos DR-2 (parte setor)
├── 03-produto-servico.md                  # sintese pos DR-2 (parte produto)
├── 04-concorrentes.md                     # sintese pos DR-4
├── 05-panorama-ads.md                     # checklist manual / pos DRs
├── 06-consumidor.md                       # sintese pos DR-3
├── 07-fontes-e-evidencias.md
├── prompts/
│   ├── dr-1-cliente-posicionamento-regiao.md
│   ├── dr-2-produto-setor.md
│   ├── dr-3-consumidor.md
│   └── dr-4-concorrencia.md
├── prompt-perplexity-social-opcional.md
└── bruto/
    ├── gemini/
    │   ├── dr-01-output.md
    │   ├── dr-02-output.md
    │   ├── dr-03-output.md
    │   └── dr-04-output.md
    └── perplexity/
```

Se `docs/` nao existir, crie.

## Passo 2 — O que o usuario precisa ter antes dos 4 Deep Researches

**Instrua o usuario** a reunir com o cliente (ou colar do CRM/pasta) o maximo possivel:

| Area | O que pedir |
|------|-------------|
| Identidade | Nome fantasia, razao se util, **cidade/UF ou abrangencia** (critico para regiao) |
| Digital | Site, Instagram, TikTok, YouTube, LinkedIn, Google Meu Negocio, marketplaces (links) |
| Cadastro | CNPJ/contato **se autorizado** (dado sensivel; pasta local gitignored) |
| Oferta | O que vendem, pacotes, B2B/B2C, ticket ou faixa se souberem |
| Mercado | Concorrentes que o cliente cita, restricoes (verba, compliance, canais) |
| Interno | Notas ou transcricao de **kickoff**, **vendas**, **comercial**; **formulario de brief**; proposta; export CRM |
| Objetivo | O que a KB precisa destravar (copy, campanha, LP, diagnostico) |

Se vier pouco: **prossiga mesmo assim** — preencha `00-briefing-pesquisa.md` com o que ha e marque **LACUNA**; os prompts abaixo ja mandam o modelo declarar lacunas.

**Nao dependa de Gem.** Entregue os quatro arquivos em `prompts/` (ou o texto no chat) **ja preenchidos** com resumo `[DADOS INTERNOS CONSOLIDADOS]` extraido do briefing/pasta.

## Passo 3 — Instrucao fixa ao usuario (copiar para o time)

Envie algo neste spirit:

> Rode **quatro Deep Researches no Gemini**, **um de cada vez**. Em cada rodada depois da primeira, **anexe ou cole o relatório anterior** no contexto do Deep Research junto com o novo prompt. Salve cada saida em `bruto/gemini/dr-0N-output.md`. Opcional: no fim, rode o `prompt-perplexity-social-opcional.md` no Perplexity.

## Templates dos 4 prompts (obrigatorio entregar preenchidos)

O agente deve **substituir** o placeholder `<<<DADOS INTERNOS CONSOLIDADOS>>>` pelo texto real (e em DR-2+, pode adicionar `<<<RESUMO DR-ANTERIOR>>>` com 10-20 linhas do que o usuario colou do relatorio anterior, se ele ja tiver).

### DR-1 — Cliente, posicionamento digital e regiao

Salvar como `prompts/dr-1-cliente-posicionamento-regiao.md`. Objetivo: entender o cliente; mapear **tudo** que der para achar e inferir do **posicionamento digital** (fato vs inferencia); **regiao de atuacao** (Brasil, estadual, local, multi-regiao) e **pormenores uteis** do territorio para marketing (com fonte ou como inferencia explicita).

```text
Deep Research — Rodada 1 de 4: Cliente, presenca digital e regiao de atuacao.

DADOS INTERNOS (nao contradizer sem apontar inconsistencia):
<<<DADOS INTERNOS CONSOLIDADOS>>>

TAREFA:
1) Quem e o cliente e o que parece vender (cruzar digital + dados internos).
2) Posicionamento digital: site, redes,SEO aparente, tom, promessas, provas sociais, gaps.
3) Separar rigorosamente: FATO (fonte publica ou dado interno) / INFERENCIA / HIPOTESE / LACUNA.
4) Regiao de atuacao: escala (Brasil vs regional vs local); evidencias; nuances do territorio **uteis para marketing** (economia, consumo, concorrencia local quando houver fonte — evitar clichês sem fonte).
5) Perguntas obrigatorias para validar com o cliente.
6) Riscos: homonimia de marca, dados desatualizados.

REGRAS: Portugues BR; cite links; nao invente faturamento, precos internos nem performance de anuncios.
```

### DR-2 — Produto, oferta e como funciona o setor

Salvar como `prompts/dr-2-produto-setor.md`. Incluir contexto da DR-1 (usuario cola output ou resumo).

```text
Deep Research — Rodada 2 de 4: Produtos/servicos e dinamica do setor.

CONTEXTO DA RODADA 1 (cole aqui o relatorio completo ou resumo fiel):
<<<OUTPUT DR-1 OU RESUMO>>>

DADOS INTERNOS (repetir o essencial):
<<<DADOS INTERNOS CONSOLIDADOS>>>

TAREFA:
1) Detalhar produtos/servicos que o cliente oferece.
2) Como o SETOR funciona: modelo de negocio tipico, fontes de receita, recorrencia, sazonalidade.
3) O que **normalmente** da margem vs volume no setor (separar padrao de mercado vs especulacao sobre ESTE cliente).
4) Como empresas desse tipo **costumam vender** (canais, bundles, argumentos, servicos anexos).
5) Indicadores e linguagem do ramo.
6) Lacunas so o dono fecha.

REGRAS: PT-BR; separar "padrao do setor" vs "esta marca"; FATO/INFERENCIA/LACUNA; links.
```

### DR-3 — Consumidor (foco total na demanda)

```text
Deep Research — Rodada 3 de 4: Consumidor — panorama completo.

CONTEXTO DAS RODADAS 1 E 2 (cole resumos ou relatorios):
<<<OUTPUT DR-1 E DR-2 OU RESUMOS>>>

DADOS INTERNOS:
<<<DADOS INTERNOS CONSOLIDADOS>>>

TAREFA (prioridade maxima = quem COMPRA):
1) Segmentos, gatilhos de compra, contexto.
2) O que querem; como falam (aspas so com citacao real; senao SINTETIZADO).
3) Reclamacoes, medos, friccoes (com fontes quando possivel).
4) Criterios de escolha e alternativas.
5) Confianca vs rejeicao na categoria.
6) Mapa acionavel de dores/desejos para copy e criativo.
7) Lacunas de voz do consumidor online.

REGRAS: PT-BR; nao confundir persona inventada com evidencia.
```

### DR-4 — Concorrencia

```text
Deep Research — Rodada 4 de 4: Panorama competitivo geral.

CONTEXTO DAS RODADAS ANTERIORES (cole resumos ou relatorios 1–3):
<<<OUTPUT DR-1 A DR-3 OU RESUMOS>>>

DADOS INTERNOS (incluir concorrentes citados pelo cliente):
<<<DADOS INTERNOS CONSOLIDADOS>>>

TAREFA:
1) Concorrentes diretos, indiretos, substitutos relevantes (regiao do cliente; declarar limite se regiao incerta).
2) Promessas, posicionamento, precos publicos quando houver fonte.
3) Presenca digital comparativa (sem afirmar ROAS/CPA).
4) Gaps e oportunidades.
5) O que validar com o cliente sobre concorrencia.

REGRAS: PT-BR; links; FATO/INFERENCIA/LACUNA; homonimia.
```

## Opcional — Perplexity (busca social)

Salvar `prompt-perplexity-social-opcional.md` e dizer ao usuario que e **recomendado depois da DR-3 ou DR-4** para cruzar reviews/comentarios com links.

```text
Pesquisa com FONTES e LINKS — foco em voz social do consumidor.

Marca/categoria: [NOME / CATEGORIA]
Regiao: [REGIAO ou Brasil]
Concorrentes: [LISTA ou LACUNA]

Quero: reviews, RA, comentarios, foruns em PT-BR; padroes de elogio/raiva; citacoes reais entre aspas ou FRASE SINTETIZADA; hesitacoes ("quase comprei"); concorrentes citados por usuarios; LACUNAS.

Separe FATO COM LINK vs INFERENCIA. Nao invente citacoes.
```

## Apos os quatro DRs — sintese nos markdowns

Quando o usuario voltar com outputs, ajude a distribuir:

| Output | Arquivo de sintese sugerido |
|--------|-----------------------------|
| DR-1 | `01-cliente-posicionamento-digital.md` (+ trechos de regiao se quiser no briefing) |
| DR-2 | `02-mercado-setor-modelo-negocio.md` e `03-produto-servico.md` |
| DR-3 | `06-consumidor.md` |
| DR-4 | `04-concorrentes.md` |
| Perplexity | `06-consumidor.md` (complemento) e `07-fontes-e-evidencias.md` |

Preencha `07-fontes-e-evidencias.md` com links, data, ferramenta, fato vs inferencia vs validar com cliente.

**Panorama de ads (05):** nao exige novo DR; checklist em `references/fontes-pesquisa-social.md` (Meta Ad Library, Google Transparency, TikTok Creative Center) — observacao vs inferencia; sem performance sem dado.

## Consumidor sintetico

> Isso nao e pesquisa real final; e hipotese para testar copy. Basear sempre em evidencias dos DRs.

## Fechamento

Entregue ao usuario:

1. `00-briefing-pesquisa.md` atualizado.
2. Os quatro arquivos em `prompts/` **preenchidos** (ou os quatro blocos no chat).
3. `prompt-perplexity-social-opcional.md` preenchido.
4. Onde salvar cada output bruto.
5. Lacunas criticas para o cliente.

Nao entregue plano de campanha final aqui — isso vem depois da KB consolidada.
