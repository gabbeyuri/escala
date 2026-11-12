# /escala — Escala

Skill pro [Claude Code](https://claude.ai/claude-code) que transforma operacao de low ticket digital num software interativo. Mineracao de ofertas escaladas, modelagem de funil, geracao de criativos e otimizacao de campanhas — tudo dentro do terminal.

**by YM MKT — Yuri MKT ([@yuri.mkt7](https://instagram.com/yuri.mkt7)) e Miguel MKT ([@miguelmkt8](https://instagram.com/miguelmkt8))**

## O que faz

| Modulo | Descricao | Status |
|--------|-----------|--------|
| [1] Setup | Diagnostico de dependencias | dia 21 |
| [2] Minerar | Reclame Aqui (via dev-browser) | **LIBERADO** |
| [2] Minerar | Biblioteca Meta | dia 21 |
| [3] Analisar | Analise completa de oferta + definir produto | dia 21 |
| [4] Entregavel | Gera PDF/pack/planilha/simulado completo | dia 21 |
| [5] Funil | Modela pagina de vendas a partir do concorrente | dia 21 |
| [6] Criativos | Scrapa ads reais, transcreve, gera 7 criativos modelados | dia 21 |
| [7] Meta Ads | Cria pixel, campanha e otimiza (metodo YM MKT) | dia 21 |
| [8] Config | Diagnostico, instalacao, metricas | dia 21 |

> Versao de lancamento: so a mineracao via Reclame Aqui esta liberada. O restante da skill sera revelado dia 21, e so quem estiver no grupo vai ter acesso. Entra agora: https://chat.whatsapp.com/LQYqP14qrwt6OX16JM0JLw

## Como instalar (passo a passo)

1. Tenha o Claude Code instalado no seu computador.

2. No terminal, clone a skill direto na pasta de skills do Claude Code:

```bash
git clone https://github.com/gabbeyuri/escala.git ~/.claude/skills/escala
```

3. Abra o Claude Code e rode o comando da skill:

```text
/escala
```

4. Escolha `[2] Minerar` > `[A] Reclame Aqui`. Na primeira vez a skill confere se voce tem Python, Edge e dev-browser e instala o que faltar.

## Minerador Reclame Aqui

O `minerador-reclameaqui.py` scrapa reclamacoes em gateways de pagamento pra encontrar ofertas escaladas.

```bash
# Todos os gateways suportados
python minerador-reclameaqui.py

# Gateways especificos
python minerador-reclameaqui.py cakto,kirvano 10 15
```

Requer Python, Edge e dev-browser (`npm install -g dev-browser`). Resultados salvos em `./mineracao/`.

## Estrutura de pastas (geradas automaticamente)

```
./mineracao/          resultados do Reclame Aqui
```

## Licenca

MIT
