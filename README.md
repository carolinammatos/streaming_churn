# Análise de Churn em Plataforma de Streaming por Assinatura

![image](https://github.com/danielesantiago/TemplatePED/assets/64613885/cc24a702-46b8-4263-a764-8c65c56f02ac)


## 📌 Visão Geral
Este projeto visa **entender por que os clientes cancelam a assinatura de uma plataforma de streaming e onde a empresa pode agir para reter mais**. Utilizamos **limpeza e padronização de dados, análise univariada e bivariada, análise de cohort, segmentação RFM, Pareto dos motivos de cancelamento e funil de onboarding** para **identificar os sinais honestos de risco de churn, os principais motivos de saída e os gargalos da jornada do cliente**.

O notebook foi desenvolvido em Python (pandas, NumPy, Matplotlib e Seaborn) no Google Colab, como parte do Módulo 3 da pós-graduação.

📄 [Veja o template da apresentação](https://github.com/profrenatabiaggi/template/blob/master/reports/figures/Template_Case.pptx)

📄 [Veja o template do notebook](https://github.com/profrenatabiaggi/template/blob/master/notebooks/Case_Fraude.ipynb)

## 💼 Entendimento do Negócio

Em negócios de assinatura, o churn corrói a receita recorrente e encarece o crescimento: adquirir um cliente novo custa mais do que manter um existente. Na base analisada (~5 mil clientes, extração em 31/01/2024), cerca de **30% dos clientes já cancelaram**. Entender quem cancela, quando e por quê permite priorizar ações de retenção, produto e marketing.

**Tipos de Análise Realizados:**
- Análise univariada e bivariada de variáveis categóricas e numéricas contra o churn
- Análise de cohort de retenção por safra de assinatura
- Segmentação de clientes ativos via RFM adaptado a streaming
- Pareto dos motivos de cancelamento
- Funil de conversão do onboarding

**Principais Indicadores Chave de Desempenho:**
- Taxa de churn (geral e por segmento: plano, ciclo, região e canal de aquisição)
- Retenção por safra e mês de vida
- NPS
- Conversão entre etapas do funil (visita → conta → 1ª aula → assinatura → renovação)

## 📊 Análise do Modelo Atual

A base apresenta churn relevante e sinais de que a **retenção piorou nas safras mais recentes**: as curvas de cohort de 2023 caem mais rápido que as de 2022, que por sua vez caem mais rápido que as de 2021. Isso sugere possível deterioração na qualidade da aquisição ou na experiência do produto.

O projeto propõe identificar quais variáveis realmente antecedem o cancelamento (e podem alimentar alertas precoces) e quais apenas descrevem um churn que já aconteceu.


## 🛠 Pré-processamento 
O pré-processamento foi feito em pandas, com validações (sanity checks) a cada etapa para garantir consistência e reprodutibilidade.

_Considerações Importantes:_
1. Os valores de valor_mensal dos planos anuais apareciam multiplicados por 100 (ex.: 1990 em vez de 19,90) e foram corrigidos, resultando em apenas três preços válidos (19,90 / 34,90 / 55,90).
2. A análise bivariada usa apenas clientes com **pelo menos 220 dias de assinatura** (recorte maduro, 3.678 clientes), para que todos tenham tido tempo real de cancelar e evitar viés de clientes recentes.
   
_Etapas do Pré-processamento no Pipeline:_
1. Padronização de texto em `plano` (8 grafias → 3 planos) e `canal_aquisicao` (9 grafias → 4 canais)
2. Tratamento de valores ausentes disfarçados: `"?"` em `regiao` convertido para nulo (170 registros)
3. Conversão de colunas de data para `datetime`
4. Criação de variáveis de diferença entre datas, com validação de consistência cronológica (1 registro com último acesso antes da assinatura foi removido)
5. Remoção de idades fora do intervalo [18, 90] (13 registros)
6. Correção da escala de `valor_mensal`
Criação de variáveis derivadas: `recencia`, `meses_assinatura` e `receita_acumulada`

## 🔎 Frameworks de Análise
| Framework | Objetivo | Base utilizada |
|---|---|---|
| **Cohort de retenção** | Medir a retenção mês a mês | Todos os clientes |
| **RFM adaptado** | Segmentar clientes ativos | Clientes ativos (churn = 0) |
| **Paretp** | Identificar os "poucos vitais" entre os motivos de cancelamento | Clientes que cancelaram
| **Funil de onboarding** | Encontrar o maior gargalo da jornada | 	Dataset de eventos próprio (`funil_eventos.csv`)
> **Cuidado com vazamento de target:** `dias_ativos_ult_mes`, `horas_assistidas_total`, `recencia` e qualquer diferença baseada em `data_ultimo_acesso` ou `data_cancelamento` parecem separar churners de não-churners de forma espetacular, mas são consequência do churn, e não causa. Elas foram usadas para descrever o estado atual dos clientes (RFM), e não como preditoras.

## 📈 Insights e Conclusões

- **NPS é o sinal mais forte e honesto de risco**: média de ~7,4 entre quem permaneceu vs. ~4,1 entre quem cancelou. É a melhor candidata a alerta precoce de cancelamento.
- **Idade e valor do plano não separam churners**: descartar essas hipóteses evita esforço de segmentação pouco útil.
- **Ciclo anual não segura o cliente**: a taxa de churn é praticamente igual entre planos anuais (24,1%) e mensais (24,3%).
- **Plano e canal têm efeito leve, mas com valor de negócio**: o plano básico tem churn maior (27,2%) que o premium (21,3%), e clientes vindos de indicação (22,7%) retêm mais do que os vindos do Google (27,0%).
- **Retenção piorando nas safras recentes**: sinal de alerta para aquisição e produto.
- **Motivos de cancelamento**: três motivos concentram ~75% das saídas, sendo **preço (33,6%)**, **pouco conteúdo novo (25,4%)** e **problema técnico (16,5%)**.
- **Maior gargalo do funil**: a passagem de "assistiu a 1ª aula" para "assinou plano" converte só **39,5%**. Dos 5.000 visitantes, apenas 13,9% chegam à renovação.
- **Segmentação RFM**: cerca de 23% dos clientes ativos estão no segmento "Em risco", um público prioritário para campanhas de reengajamento.

## 📜 Estrutura do Projeto

A estrutura de diretórios do projeto foi organizada da seguinte forma:
```
├── README.md
├── data
│   ├── processed
│   └── raw
├── notebooks
├── reports
│   └── figures
└── requirements.txt

```

## 🚧 Próximos Passos

- Construir um **modelo preditivo de churn** (ex.: regressão logística e gradient boosting) usando apenas variáveis sem vazamento, como NPS, plano, canal, região e tempo de casa
- Aprofundar a **análise de sobrevivência** (Kaplan-Meier) para medir a permanência de forma rigorosa, já que o tempo dos clientes ativos ainda está "correndo"
Investigar por que as safras recentes retêm menos (canal de aquisição, preço, catálogo)
Testar ações direcionadas: campanhas de reengajamento para o segmento "Em risco", revisão da oferta no ponto de assinatura e reforço de catálogo novo
Evoluir o notebook para scripts reutilizáveis em `src/`


