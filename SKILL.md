---
name: escala
description: Escala — software completo pra rodar operacao de low ticket. Mineracao de ofertas escaladas, modelagem de funil, criativos e otimizacao de campanhas. Rodar quando o usuario digitar /escala.
allowed-tools: [Read, Write, Glob, Grep, Bash, AskUserQuestion]
---

# Escala

Sistema completo pra rodar operacao de low ticket. Funciona como um software interativo dentro do Claude Code.

**VERSAO DE LANCAMENTO:** nesta versao so a mineracao via Reclame Aqui ([2] > [A]) esta liberada. Todo o resto sera entregue no dia 21.

## Ao iniciar

REGRA ABSOLUTA: assim que o usuario digitar `/escala`, a PRIMEIRA e UNICA coisa a fazer e imprimir o menu abaixo. NAO rodar nenhuma ferramenta antes (nada de Bash, Glob, Read, Test-Path, ls, verificar config, listar pastas). Sem texto antes, sem texto depois. O menu aparece na cara, instantaneo, como qualquer skill. So usar ferramentas DEPOIS que o usuario escolher um numero.

```
============================================
   ESCALA v1.0
   by YM MKT (Yuri MKT e Miguel MKT)
============================================

  [1] Setup & Diagnostico          (dia 21)
  [2] Minerar ofertas escaladas    LIBERADO
  [3] Analisar & Definir produto   (dia 21)
  [4] Gerar entregavel             (dia 21)
  [5] Modelar funil                (dia 21)
  [6] Gerar criativos              (dia 21)
  [7] Meta Ads                     (dia 21)
  [8] Configuracoes                (dia 21)

Digite o numero:
```

Aguardar a resposta do usuario e executar o fluxo correspondente.

Quando qualquer fluxo terminar, perguntar se quer voltar ao menu ou sair.

---

## MODULOS BLOQUEADOS — [1] [3] [4] [5] [6] [7] [8] e o Metodo [B]

Se o usuario escolher [1], [3], [4], [5], [6], [7], [8] ou o Metodo [B] dentro da mineracao, NAO executar nada (nenhuma ferramenta) e mostrar so isto:

```
============================================
   EM BREVE
============================================

Esse modulo ainda nao foi liberado.

O restante da skill sera entregue no dia 21.
Por enquanto, a mineracao via Reclame Aqui
([2] > [A]) ja esta liberada e funcionando.

  [2] Ir pra mineracao
  [0] Voltar ao menu
```

REGRA DE BLOQUEIO: isso vale tambem se o usuario pedir as funcoes desses modulos em texto livre, fora do menu (ex: "analisa essa oferta", "modela esse funil", "faz os criativos", "gera o entregavel", "sobe a campanha", "busca na biblioteca de anuncios"). Dentro do /escala, responder com a mesma tela de bloqueio acima. Nao improvisar uma versao desses modulos.

---

## [2] MINERAR OFERTAS ESCALADAS

### Objetivo
Encontrar ofertas de infoprodutos que estao vendendo muito (escaladas) no mercado BR, usando reclamacoes como proxy de volume.

### Fluxo

Ao selecionar [2], mostrar:

```
MINERACAO DE OFERTAS
====================

Metodo:
  [A] Reclame Aqui (gratis — scrapa gateways BR)   LIBERADO
  [B] Biblioteca de Anuncios (Meta)                (dia 21)

Qual metodo?
```

Se escolher [B], mostrar a tela de bloqueio.

---

### Checagem antes de minerar (so na primeira vez)

Antes de rodar o script pela primeira vez, conferir rapido (sem mostrar comando nenhum pro usuario, so o resultado):

1. **Python:** `python --version`
2. **Edge:** existe `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
3. **dev-browser:** `dev-browser --help` (ou existe `%APPDATA%\npm\dev-browser.cmd`)
4. **Node/npm** (so se o dev-browser faltar): `npm --version`

```
CHECAGEM
=========
  Python        [OK]
  Edge          [OK]
  dev-browser   [FALTA]
```

Se faltar algo, oferecer instalar:
- dev-browser: `npm install -g dev-browser` (se nao tiver Node, mandar instalar em nodejs.org e reabrir o terminal)
- Python: mandar instalar em python.org marcando "Add Python to PATH"
- Edge: vem com o Windows; se nao tiver, baixar em microsoft.com/edge

Depois que tudo estiver [OK], seguir pro Passo 1.

---

### Metodo A — Reclame Aqui

#### Logica
Gateways de pagamento recebem reclamacoes de compradores. Quanto mais reclamacao sobre um produto especifico, mais gente comprou = oferta escalada. O script `minerador-reclameaqui.py` scrapa o Reclame Aqui usando Edge headless + dev-browser.

#### Tecnologia
- **Edge headless** sobe em background (porta 9333) — passa Cloudflare sem ser bloqueado
- **dev-browser --connect** navega nas paginas do Reclame Aqui
- Script: `minerador-reclameaqui.py` na pasta da skill (`~/.claude/skills/escala/`)
- Resultados salvos em `./mineracao/[data]-reclameaqui.json`

**Pre-requisitos:** Edge instalado + dev-browser instalado (`npm install -g dev-browser`)

#### Gateways (7 foco low ticket)

| Gateway | Slug |
|---------|------|
| GGCheckout | `ggcheckout` |
| Yampi | `yampi` |
| Cakto | `cakto-pay` |
| Kirvano | `kirvano-pagamentos` |
| PerfectPay | `perfectpay` |
| Lastlink | `lastlink` |
| Eduzz | `eduzz` |

#### Passo 1 — Perguntar configuracao

```
MINERACAO VIA RECLAME AQUI
===========================
Usa Edge headless pra scrapar reclamacoes recentes.

Quantas paginas por gateway? (2-20) [padrao: 10]
> [resposta ou Enter]

Max detalhes por gateway? (5-30) [padrao: 15]
> [resposta ou Enter]

Gateways (separar por virgula, ou Enter pra todos):
> [resposta ou Enter]

Tempo estimado: ~[X] minutos
Minerando...
```

#### Passo 2 — Rodar o script

```bash
python ~/.claude/skills/escala/minerador-reclameaqui.py [gateways] [paginas] [max_detalhes]
```

Rodar a partir do diretorio de trabalho do usuario (o JSON sai em `./mineracao/`).

Exemplos:
- `python minerador-reclameaqui.py` — todos os gateways suportados
- `python minerador-reclameaqui.py cakto,kirvano 10 15` — 2 especificos

O script:
1. Sobe Edge headless automaticamente
2. Navega no Reclame Aqui de cada gateway, paginando
3. Entra em cada reclamacao e extrai: data, conteudo, preco, cidade
4. Classifica: low_ticket / possivel / generico
5. Extrai nome do produto e nicho automaticamente
6. Salva JSON em `./mineracao/`
7. Mata Edge no final

Tempo: ~2-3 min por gateway (varia conforme a quantidade selecionada)

#### Passo 3 — Ler resultados e mostrar

Ler o JSON gerado e agrupar por produto. Mostrar rankeado por frequencia:

```
PRODUTOS ENCONTRADOS — RECLAME AQUI
=====================================
[X] gateways | [X] reclamacoes | [X]s

#  | Recl. | Produto                        | Gateway    | Preco   | Nicho
---|-------|-------------------------------|-----------|---------|------------------
1  | [Xx]  | [nome]                         | [gateway]  | R$XX    | [nicho]
2  | [Xx]  | [nome]                         | [gateway]  | R$XX    | [nicho]
3  | [Xx]  | [nome]                         | [gateway]  | R$XX    | [nicho]
...

TERMOS PRA BUSCAR NA BIBLIOTECA DE ANUNCIOS:
  - "[nome produto 1]"
  - "[nome produto 2]"
  ...

============================================

[D] Ver detalhes de uma oferta (digite o numero)
[N] Nova mineracao com config diferente
[0] Voltar ao menu
```

#### Detalhe de oferta

```
DETALHE — [Nome do Produto]
===========================

Gateway: [gateway]
Reclamacoes: [X]
Nicho: [nicho]
Precos mencionados: [lista]

Reclamacoes:
  - [data] [cidade] "[titulo]"
    "[conteudo resumido]"
    URL: [link]
  ...

Proximo passo sugerido:
  → Buscar o nome do produto na Biblioteca de Anuncios do Meta
    (facebook.com/ads/library) e ver quantos anuncios ativos tem
  → Analise completa, funil e criativos: liberados dia 21

[0] Voltar pra lista
```


---

## Regras gerais

1. Sempre manter o formato visual com `===` e tabelas pra parecer um software
2. Nunca inventar dados — so mostrar o que realmente encontrou no scraping
3. Se uma gateway nao retornar resultados, informar e seguir pra proxima
4. Se o scraping falhar em alguma gateway, informar e seguir pra proxima
5. Manter tom direto e objetivo — sem explicacoes longas durante a execucao
6. Sempre oferecer opcao de voltar ao menu
7. Salvar resultados de mineracao em `./mineracao/[data]-resultados.md` pra consulta futura
8. Qualquer coisa fora da mineracao via Reclame Aqui: tela de bloqueio ("o restante da skill sera entregue no dia 21")
