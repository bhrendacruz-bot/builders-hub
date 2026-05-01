---
name: novo-cliente
description: Cria uma nova pasta de cliente dentro de um squad com estrutura padrao e CLAUDE.md inicial. Pergunta squad, nome e NotebookLM. Use quando o usuario rodar /novo-cliente ou disser que quer adicionar um cliente novo.
---

Voce vai criar a pasta de um novo cliente DENTRO de um squad. Todo cliente vive em `clientes/{squad}/clientes/{cliente}/`.

## Processo

### Passo 1 — Escolher o squad

Liste os squads existentes em `clientes/` (ignore qualquer pasta `_template-*`):

```bash
ls clientes/ | grep -v '^_template-'
```

- **Se nao existir nenhum squad**: pare e diga:
  > "Voce ainda nao tem squad. Roda `/novo-squad` antes pra criar o squad e depois rode `/novo-cliente` de novo."
  
  NAO crie cliente fora de squad.

- **Se existirem squads**: liste numerada e pergunte:
  > "Em qual squad esse cliente entra? (digite o numero ou o nome)"
  
  Aceite numero ou nome. Guarde o nome formatado (com hifens) como `[squad]`.

### Passo 2 — Nome do cliente

Pergunte:
> "Qual o nome do cliente?"

Use o nome para criar a pasta. Converta para lowercase-com-hifens (ex: "Academia Estação Saúde" → "academia-estacao-saude"). Guarde como `[cliente]`.

### Passo 3 — Criar a estrutura

```bash
cp -r clientes/_template-cliente "clientes/[squad]/clientes/[cliente]"
# Copia o .env.example pra .env (inicial vazio, o usuario preenche conforme for usando)
cp "clientes/[squad]/clientes/[cliente]/.env.example" "clientes/[squad]/clientes/[cliente]/.env"
```

O `.env` e gitignored por padrao (clientes/ inteiro e — so `_template-*/` sobe pro repo). Credenciais ficam locais.

### Passo 4 — NotebookLM

Pergunte:
> "Esse cliente tem um NotebookLM? Se sim, cola o link aqui. Se nao, so aperta Enter."

**Se tiver link** (formato `https://notebooklm.google.com/notebook/XXXXX`):
- Extraia o notebook ID da URL

Crie `clientes/[squad]/clientes/[cliente]/CLAUDE.md`:
```markdown
# [Nome do Cliente]

## NotebookLM
- **Link:** [URL]
- **Notebook ID:** [ID]

Use `notebooklm` CLI com o notebook ID acima para consultar a base de conhecimento desse cliente, gerar podcasts ou resumos.

## Contexto
Rode `/contexto` apos adicionar dados nesta pasta para gerar o contexto completo.
```

**Se NAO tiver link:**

Crie `clientes/[squad]/clientes/[cliente]/CLAUDE.md`:
```markdown
# [Nome do Cliente]

## Contexto
Rode `/contexto` apos adicionar dados nesta pasta para gerar o contexto completo.
```

### Passo 5 — Confirmar

Mostre a estrutura criada:
```
clientes/[squad]/clientes/[cliente]/
├── CLAUDE.md
├── .env            # suas credenciais (gitignored)
├── .env.example    # template das credenciais
├── calls/
├── docs/
└── campanhas/
```

Diga:
> "Cliente criado dentro do squad [squad]. Jogue os dados dele nas pastas (calls, docs, campanhas) e rode `/contexto` quando tiver pronto. O `.env` ta vazio — preenche as credenciais V4mos conforme for precisar (skills tipo `/trafego-meta-diagnostico` vao pedir o que falta)."
