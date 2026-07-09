# Estudo 4 — Alugar ou Financiar?

Simulação comparativa entre **comprar um imóvel financiado (SAC)** e **alugar + investir a diferença no CDI**, em 7 capitais brasileiras, ao longo de um horizonte de 20 anos.

## Pergunta central

Dado o patamar atual de juros no Brasil, compensa financiar um imóvel ou é melhor alugar e investir a diferença mensal entre o custo de financiar e o custo de alugar?

## Cidades analisadas

| Cidade | Região |
|---|---|
| São Paulo | Sudeste |
| Rio de Janeiro | Sudeste |
| Porto Alegre | Sul |
| Fortaleza | Nordeste |
| Salvador | Nordeste |
| Goiânia | Centro-Oeste |
| Belém | Norte |

## Fontes de dados

- **Preço de venda, aluguel, yield e valorização (12 meses)**: [Índice FipeZAP](https://www.fipezap.com.br/) — Informe Residencial de Dezembro/2025 (venda e locação).
- **CDI**: API de séries temporais do Banco Central (SGS, série 12), com fallback fixo de 14,75% a.a. quando a API está indisponível no ambiente de execução.
- **Taxa de financiamento**: 11,5% a.a. — estimativa baseada nas taxas médias praticadas no SBPE pelos principais bancos (Caixa, Itaú, Bradesco, Santander, BB).
- **IPTU e condomínio**: estimativas de mercado por cidade (percentual do valor venal e R$/m²/mês, respectivamente) — não há fonte consolidada nacional para esses dois itens, já que variam por prefeitura/bairro/prédio.

## Metodologia

- **Financiamento**: SAC, 30 anos, entrada de 20%, 80% financiado.
- **Custo de transação do comprador**: 3,5% do valor do imóvel (ITBI + registro).
- **Valorização projetada**: 40% da valorização FipeZAP de 12 meses — um *haircut* conservador para não extrapolar picos recentes de curto prazo como se fossem sustentáveis no longo prazo.
- **Cenário do inquilino**: investe a diferença mensal entre o custo total de financiar (parcela + IPTU + condomínio) e o custo de alugar, rendendo ao CDI.
- **Métrica de comparação**: patrimônio acumulado em 20 anos (valor do imóvel líquido de saldo devedor vs. carteira de investimentos), e o *break-even* (ano em que o patrimônio do comprador supera o do inquilino, se houver).

## Estrutura do notebook

1. Setup e parâmetros de entrada por cidade
2. Busca do CDI (API do BCB, com fallback)
3. Simulação de amortização SAC
4. Simulação de aluguel + investimento no CDI
5. Consolidação dos resultados (`df_resumo`)
6. Gráficos (evolução patrimonial, break-even, painel resumo)
7. Conclusão automática (texto gerado a partir dos resultados)

## Como rodar

```bash
pip install pandas numpy matplotlib requests
jupyter notebook estudo4_alugar_ou_financiar.ipynb
```

Ou abra diretamente no [Google Colab](https://colab.research.google.com/).

## Limitações conhecidas

- Yield de algumas cidades (Belém, Salvador) foi recalculado (aluguel × 12 ÷ preço) por não constar explicitamente no trecho do informe consultado — as demais usam o yield publicado diretamente.
- IPTU e condomínio de Salvador e Rio de Janeiro são estimativas próprias, não validadas contra fonte oficial.
- Os dados de venda e locação usados são de dezembro/2025 — revisar contra o informe FipeZAP mais recente antes de qualquer nova publicação.
- CDI validado contra a taxa real de julho/2026 (14,75% a.a.), praticamente idêntica ao fallback usado.

## Resultado (resumo)

Com CDI em ~14,75% a.a., **nenhuma das 7 capitais atinge break-even em 20 anos** — alugar e investir a diferença supera financiar em todos os cenários testados, inclusive nas cidades com valorização recente mais forte. Salvador é a cidade que mais se aproxima do break-even, seguida por Belém.

## Aviso

Este estudo é uma simulação educativa baseada em premissas e dados públicos, não constitui recomendação de investimento ou aconselhamento financeiro/imobiliário individualizado.
