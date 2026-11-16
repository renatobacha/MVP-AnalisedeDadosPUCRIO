# MVP — Análise de Dados e Boas Práticas: Risco de Diabetes

Análise exploratória e pré-processamento do *Diabetes Risk Prediction Dataset (50K Patients)* (Kaggle), para uma futura classificação ordinal do nível de risco (`Low`, `Moderate`, `High`). O trabalho **termina na preparação dos dados**; não há treino de modelos.

**Autor:** Renato Penna Magoulas Bacha (PUC-Rio — Pós-graduação em Ciência de Dados e Analytics)

## Conteúdo do repositório

| Arquivo | Descrição |
|---|---|
| `MVP_Analise_Dados_Diabetes.ipynb` | Notebook-relatório (código, resultados, interpretações, decisões e limitações) |
| `diabetes_risk_prediction_dataset.csv` | Dados lidos pelo notebook via URL *raw* |
| `dados_processados/` | Oito arquivos `.csv.gz` (4 visões × treino/teste) gerados pelo notebook |
| `requirements.txt` | Dependências |

## Como executar

1. Abra o notebook no Google Colab: https://colab.research.google.com/github/renatobacha/MVP-AnalisedeDadosPUCRIO/blob/main/MVP_Analise_Dados_Diabetes.ipynb
2. Em um ambiente novo, use *Ambiente de execução → Executar tudo*.
3. Não são necessárias contas pessoais, credenciais nem arquivos locais: o CSV é lido da URL pública do repositório.
4. Localmente: `pip install -r requirements.txt` e abra o notebook com Jupyter.

## Estrutura do notebook

1. Problema e perguntas (H1 a H4) · 2. Fonte, ambiente e dicionário · 3. Análise exploratória · 4. Pré-processamento e decisões (D1 a D3) · 5. Respostas e conclusão · Referências · Declaração de uso de IA · 6. Checklist de entrega.

## Decisões principais

- **D1:** remoção do identificador e de três colunas derivadas do risco (vazamento).
- **D2:** imputação (mediana/moda, ajustada no treino) em vez de exclusão de linhas, com comparação das duas alternativas.
- **D3:** BMI < 15 tratado como erro de registro (vira ausente e é imputado); extremos altos plausíveis mantidos.
- Partição 80/20 estratificada, `random_state=42`, antes de qualquer ajuste de parâmetros.

## Dependências

Versões mínimas em `requirements.txt`; as versões efetivamente usadas são impressas na primeira célula de código do notebook.

## Observações

A base parece sintética (distribuições planas, incoerências clínicas); os resultados descrevem a base e não devem ser extrapolados para populações reais.
