# MVP — Análise de Dados e Boas Práticas: Risco de Diabetes

Análise exploratória e pré-processamento do *Diabetes Risk Prediction Dataset (50K Patients)* (Kaggle), para uma futura classificação ordinal do nível de risco (`Low`, `Moderate`, `High`). O trabalho **termina na preparação dos dados**; não há treino de modelos.

**Autor:** Renato Penna Magoulas Bacha (PUC-Rio — Pós-graduação em Ciência de Dados e Analytics)

## Conteúdo do repositório

| Arquivo | Descrição |
|---|---|
| `MVP_Analise_Dados_Diabetes.ipynb` | Notebook-relatório (código, resultados, interpretações, decisões e limitações) |
| `diabetes_risk_prediction_dataset.csv` | Dados lidos pelo notebook via URL *raw* |

## Como executar

1. Abra o notebook no Google Colab: https://colab.research.google.com/github/renatobacha/MVP-AnalisedeDadosPUCRIO/blob/main/MVP_Analise_Dados_Diabetes.ipynb
2. Em um ambiente novo, use *Ambiente de execução → Executar tudo*.
3. Não são necessárias contas pessoais, credenciais nem arquivos locais: o CSV é lido da URL pública do repositório.

## Estrutura do notebook

1. Problema e perguntas (H1 a H4)
2. Fonte e significado dos dados
3. Ambiente e carregamento (e dicionário completo) ·
4. Análise exploratória
   4.1 qualidade
   4.2 estatísticas
   4.3 figuras
5. Pré-processamento e decisões (5.1 a 5.5, com D1 a D3) ·
6. Respostas e conclusão (com referências e declaração de IA)
7. Checklist de entrega.

## Decisões principais

- **D1:** remoção do identificador e de três colunas derivadas do risco (vazamento).
- **D2:** imputação (mediana/moda, ajustada no treino) em vez de exclusão de linhas, com comparação das duas alternativas.
- **D3:** BMI < 15 e demais extremos mantidos, após verificar que o BMI é consistente com peso e altura; comparação entre manter, remover linhas e anular/imputar.
- Partição 80/20 estratificada, `random_state=42`, antes de qualquer ajuste de parâmetros.

## Fonte e licença

Dados: *Diabetes Risk Prediction Dataset (50K Patients)*, Kaggle (autor da página: `mobeenfatimah`): https://www.kaggle.com/datasets/mobeenfatimah/diabetes-risk-prediction-dataset-50k-patients — licença **CC BY-SA 4.0** (atribuição e compartilhamento pela mesma licença;

## Observações

A base parece sintética (distribuições planas, incoerências clínicas); os resultados descrevem a base e não devem ser extrapolados para populações reais.
