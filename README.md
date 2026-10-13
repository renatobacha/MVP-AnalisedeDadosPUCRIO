# MVP – Análise de Dados e Boas Práticas
## Predição de Risco de Diabetes: análise exploratória e pré-processamento

**Autor:** Renato Bacha  
**Disciplina:** Análise de Dados e Boas Práticas  
**Dataset:** [Diabetes Risk Prediction Dataset (50K patients) – Kaggle](https://www.kaggle.com/datasets/mobeenfatimah/diabetes-risk-prediction-dataset-50k-patients)

---

## 1. Objetivo

Preparar um dataset de **50.000 pacientes** para uma futura modelagem de **classificação multiclasse**, cujo alvo é o **nível de risco de diabetes** (`Diabetes_Risk`): `Low` < `Moderate` < `High`.

O trabalho cobre as etapas de **definição do problema**, **análise exploratória** e **pré-processamento de dados**. A modelagem não faz parte deste MVP.

## 2. Conteúdo do repositório

| Arquivo / pasta | Descrição |
|---|---|
| `MVP_Diabetes_Analise_Exploratoria_ULTRA_FINAL.ipynb` | Notebook (Google Colab) com o relatório completo: texto, código, gráficos e análises |
| `diabetes_risk_prediction_dataset.csv` | Dataset original do Kaggle, sem alterações (50.000 linhas e 41 colunas) |
| `dados_processados/` | Quatro versões do dataset já pré-processadas, em treino e teste (`.csv.gz`) |
| `README.md` | Este resumo |

## 3. Como executar

1. Abra o notebook no [Google Colab](https://colab.research.google.com/) (*Arquivo → Abrir notebook → GitHub*, ou faça o upload do arquivo `.ipynb`).
2. Execute todas as células (*Ambiente de execução → Executar tudo*).

O dataset é lido direto deste repositório pela URL *raw*, então não é necessário baixar nenhum arquivo. A URL fica na célula "Parâmetros do projeto":

```python
DATA_URL = ("https://raw.githubusercontent.com/renatobacha/"
            "MVP-AnalisedeDadosPUCRIO/main/"
            "diabetes_risk_prediction_dataset.csv")
```

Os parâmetros e constantes do projeto (alvo, ordem das classes, número de faixas etc.) ficam concentrados na célula "Parâmetros do projeto".

Bibliotecas usadas: `pandas`, `numpy`, `matplotlib`, `seaborn` e `scikit-learn` (todas já disponíveis no Colab).

## 4. Estrutura do notebook

1. **Definição do problema:** descrição, tipo de aprendizado (supervisionado, classificação multiclasse), hipóteses, restrições e dicionário dos 41 atributos.
2. **Configuração e carga dos dados.**
3. **Análise de dados:** estatísticas descritivas, valores faltantes, duplicados, consistência clínica, outliers, 6 grupos de gráficos e a verificação das hipóteses, cada um seguido de uma análise.
4. **Pré-processamento:** limpeza, divisão treino/teste, quatro visões do dataset, verificação das transformações e retorno à análise exploratória.
5. **Conclusões:** resposta às hipóteses, pontos de atenção e próximos passos.

## 5. Principais resultados

- **Os dados são sintéticos:** quase todos os atributos são uniformes e independentes entre si, e há contradições clínicas (pressão diastólica maior que a sistólica em 7,5% dos registros; LDL + HDL acima do colesterol total em 36,7%).
- **Classes muito desbalanceadas:** `High` 73,2%, `Moderate` 25,9% e `Low` 0,9%.
- **Vazamento de dados identificado:** `Diabetes_Risk_Score` (o alvo é essa pontuação dividida em faixas), `AI_Health_Recommendation` e `Doctor_Consultation_Needed` entregam a resposta e foram removidas, assim como `Patient_ID`.
- **Atributos mais associados ao risco:**

| Atributo | Correlação com o risco |
|---|---|
| `Fasting_Blood_Sugar` (glicemia de jejum) | +0,38 |
| `HbA1c` | +0,28 |
| `Family_History_Diabetes` = Yes | +0,22 |
| `Age` | +0,21 |
| `BMI` | +0,16 |

- **Histórico familiar:** 83% dos pacientes com histórico familiar são de risco `High`, contra 64% dos demais.
- **`Blood_Glucose` não discrimina o risco** (média de 159 a 160 nas três classes), ao contrário de `Fasting_Blood_Sugar` e `HbA1c`.
- **Redundância:** `BMI` é calculado a partir de `Weight_kg` e `Height_cm` (correlação de 0,84 com o peso).

### Hipóteses

| Hipótese | Resultado |
|---|---|
| H1: glicose e HbA1c mais altas em risco maior | Parcialmente confirmada |
| H2: IMC e idade maiores em risco maior | Confirmada (efeito fraco) |
| H3: histórico familiar aumenta o risco | Confirmada |
| H4: mais atividade física, menor risco | Parcialmente confirmada |

## 6. Pré-processamento realizado

| Etapa | O que foi feito |
|---|---|
| Colunas removidas | `Patient_ID` e as três colunas que vazam o alvo |
| Duplicados | Nenhum encontrado |
| Valores faltantes | 14 colunas com 1% a 6%; mediana (numéricos) e moda (categóricos), aprendidas só no treino |
| Outliers | Só o `BMI` tem (0,06%); mantidos |
| Divisão | 80% treino (40.000) e 20% teste (10.000), **estratificada**, feita **antes** de qualquer transformação (evita vazamento do teste) |
| Codificação | *One-hot encoding* nas categóricas |

### Quatro visões do dataset (`dados_processados/`)

| Visão | Transformação | Colunas |
|---|---|---|
| `onehot` | Imputação + one-hot, escala original | 80 |
| `padronizado` | + `StandardScaler` (média 0, desvio 1) | 80 |
| `normalizado` | + `MinMaxScaler` (0 a 1) | 80 |
| `discretizado` | + 5 faixas por quantis | 160 |

Arquivos no padrão `diabetes_<visão>_train.csv.gz` e `diabetes_<visão>_test.csv.gz`, já com a coluna alvo (`Diabetes_Risk`: Low = 0, Moderate = 1, High = 2). Podem ser lidos com `pd.read_csv`.

## 7. Limitações

- Por ser um dataset sintético, os padrões refletem a geração dos dados e não a realidade clínica.
- O desbalanceamento exige métricas por classe, validação estratificada e `class_weight` ou *oversampling* (somente no treino) na modelagem.
- Os registros clinicamente inconsistentes foram mantidos, por não ser possível saber qual valor está errado.

## 8. Próximos passos

Treinar modelos de classificação (regressão logística, árvores, *random forest*, *gradient boosting*) com as visões salvas, avaliar com F1 macro, *recall* por classe e matriz de confusão, e validar com validação cruzada estratificada.
