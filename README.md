# MVP — Análise de Dados e Boas Práticas: Risco de Diabetes

Análise exploratória e pré-processamento de dados do *Diabetes Risk Prediction Dataset (50K Patients)*, preparando bases limpas, particionadas e **sem vazamento de dados** para uma futura classificação ordinal do nível de risco de diabetes (`Low`, `Moderate`, `High`).

> **Escopo:** o trabalho termina na preparação dos dados. Não há treino nem comparação de modelos de aprendizado de máquina.

**Autor:** Renato Penna Magoulas Bacha — Pós-graduação em Ciência de Dados e Analytics, PUC-Rio
**Disciplina:** Análise de Dados e Boas Práticas (MVP)
**Versão:** novembro de 2026 (versão final)

[![Abrir no Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/renatobacha/MVP-AnalisedeDadosPUCRIO/blob/main/MVP_Analise_Dados_Diabetes.ipynb)

---

## Sumário

1. [Resumo do projeto](#1-resumo-do-projeto)
2. [Perguntas de pesquisa](#2-perguntas-de-pesquisa)
3. [Dados e licença](#3-dados-e-licença)
4. [Estrutura do repositório](#4-estrutura-do-repositório)
5. [Como executar](#5-como-executar)
6. [Estrutura do notebook](#6-estrutura-do-notebook)
7. [Metodologia e decisões](#7-metodologia-e-decisões)
8. [Principais resultados](#8-principais-resultados)
9. [Dados processados (saídas)](#9-dados-processados-saídas)
10. [Reprodutibilidade](#10-reprodutibilidade)
11. [Limitações](#11-limitações)
12. [Próximos passos](#12-próximos-passos)
13. [Solução de problemas](#13-solução-de-problemas)
14. [Declaração de uso de IA](#14-declaração-de-uso-de-ia)
15. [Referências](#15-referências)

---

## 1. Resumo do projeto

O diabetes mellitus é uma doença crônica de elevada prevalência mundial, e sua detecção tardia está associada a complicações cardiovasculares, renais, oculares e neurológicas. Identificar precocemente pessoas de maior risco, a partir de variáveis clínicas, comportamentais e demográficas, permite priorizar exames e intervenções preventivas.

- **Unidade de observação:** cada linha é um paciente (50.000 registros, 41 colunas).
- **Alvo:** `Diabetes_Risk`, categórico ordinal (`Low` < `Moderate` < `High`), codificado como 0, 1 e 2.
- **Objetivo analítico:** diagnosticar a qualidade dos dados, responder a quatro perguntas (H1 a H4) e entregar quatro visões processadas de treino e teste, com decisões documentadas e verificações antes e depois.

## 2. Perguntas de pesquisa

| Id | Pergunta |
|---|---|
| **H1** | O nível de risco cresce com os indicadores glicêmicos (`Blood_Glucose`, `Fasting_Blood_Sugar`, `HbA1c`)? |
| **H2** | O nível de risco cresce com o índice de massa corporal (`BMI`) e com a idade (`Age`)? |
| **H3** | O histórico familiar (`Family_History_Diabetes`) eleva a proporção de pacientes na classe `High`? |
| **H4** | Níveis mais elevados de atividade física (`Physical_Activity_Level`, `Exercise_Hours_Per_Week`) reduzem essa proporção? |

## 3. Dados e licença

| Item | Descrição |
|---|---|
| **Fonte original** | Kaggle — *Diabetes Risk Prediction Dataset (50K Patients)*, página do usuário `mobeenfatimah`: https://www.kaggle.com/datasets/mobeenfatimah/diabetes-risk-prediction-dataset-50k-patients |
| **Licença** | **CC BY-SA 4.0** (atribuição à fonte e compartilhamento de obras derivadas pela mesma licença) |
| **Cópia usada** | `diabetes_risk_prediction_dataset.csv`, neste repositório, lido pela URL *raw* |
| **Data de obtenção** | novembro de 2026 |
| **Período** | a base não possui dimensão temporal |
| **Recortes** | nenhuma linha foi removida; 4 colunas foram excluídas (ver D1) |
| **Dicionário** | seções 2.2 e 3.1 do notebook (a fonte não traz dicionário; significados e unidades foram inferidos dos nomes das colunas) |

**Atribuição e compartilhamento:** os arquivos derivados em `dados_processados/` herdam a licença CC BY-SA 4.0 e devem manter a atribuição ao autor da página no Kaggle.

URL de leitura usada pelo notebook:

```
https://raw.githubusercontent.com/renatobacha/MVP-AnalisedeDadosPUCRIO/main/diabetes_risk_prediction_dataset.csv
```

## 4. Estrutura do repositório

```
MVP-AnalisedeDadosPUCRIO/
├── MVP_Analise_Dados_Diabetes.ipynb          # notebook-relatório (código, resultados, interpretações, decisões)
├── diabetes_risk_prediction_dataset.csv       # dados originais lidos pelo notebook
├── dados_processados/                         # saídas do pré-processamento (8 arquivos .csv.gz)
│   ├── diabetes_onehot_train.csv.gz
│   ├── diabetes_onehot_test.csv.gz
│   ├── diabetes_padronizado_train.csv.gz
│   ├── diabetes_padronizado_test.csv.gz
│   ├── diabetes_normalizado_train.csv.gz
│   ├── diabetes_normalizado_test.csv.gz
│   ├── diabetes_discretizado_train.csv.gz
│   └── diabetes_discretizado_test.csv.gz
├── requirements.txt                           # dependências (versões mínimas)
└── README.md                                  # este arquivo
```

> O nome do notebook deve coincidir com o usado no link do Colab (cabeçalho do notebook e selo acima).

## 5. Como executar

### 5.1 Google Colab (recomendado)

1. Clique no selo **Abrir no Colab** no topo deste arquivo.
2. Em um ambiente novo, use **Ambiente de execução → Executar tudo**.
3. Não são necessárias contas pessoais, credenciais nem arquivos locais: o CSV é lido da URL pública do repositório.
4. Ao final, a pasta `dados_processados/` é criada no ambiente do Colab (arquivos temporários da sessão; as cópias permanentes estão neste repositório).

### 5.2 Execução local

```bash
git clone https://github.com/renatobacha/MVP-AnalisedeDadosPUCRIO.git
cd MVP-AnalisedeDadosPUCRIO
python -m venv .venv
source .venv/bin/activate          # no Windows: .venv\Scripts\activate
pip install -r requirements.txt
pip install notebook               # ou jupyterlab
jupyter notebook MVP_Analise_Dados_Diabetes.ipynb
```

É necessário acesso à internet na primeira célula de carga (leitura da URL *raw*). Para usar o arquivo local, troque `DATA_URL` pelo caminho do CSV.

### 5.3 Dependências

| Pacote | Versão mínima |
|---|---|
| pandas | 2.0 |
| numpy | 1.24 |
| matplotlib | 3.7 |
| seaborn | 0.12 |
| scikit-learn | 1.2 |
| scipy | 1.10 |

As versões efetivamente usadas na execução são impressas na primeira célula de código do notebook. O notebook usa `KBinsDiscretizer` com saída em *pandas* (`set_output`), disponível a partir do scikit-learn 1.2.

## 6. Estrutura do notebook

| Seção | Conteúdo |
|---|---|
| **1. Problema e perguntas** | Contexto, unidade de observação, objetivo analítico e hipóteses H1 a H4 |
| **2. Fonte e significado dos dados** | Proveniência, licença, recortes e dicionário resumido |
| **3. Ambiente e carregamento** | Imports, constantes, funções auxiliares, leitura dos dados e dicionário completo gerado por código |
| **4. Análise exploratória** | 4.1 dimensões, tipos e qualidade; 4.2 estatísticas descritivas; 4.3 distribuições e relações (Figuras 1 a 6) |
| **5. Pré-processamento e decisões** | 5.1 plano e prevenção de vazamento; 5.2 D1; 5.3 D2; 5.4 D3; 5.5 estado final dos dados |
| **6. Respostas e conclusão** | Respostas a H1–H4, limitações, próximos passos, referências e declaração de IA |
| **7. Checklist de entrega** | Localização de cada item exigido no enunciado |

As interpretações de figuras e decisões são geradas a partir das saídas do próprio código; por isso o notebook deve ser entregue **salvo com as saídas**.

## 7. Metodologia e decisões

### 7.1 Análise exploratória

- **Qualidade:** dimensões, tipos, ausências, duplicidades, inconsistências clínicas e valores extremos (critério de Tukey, 1,5·IQR).
- **Estatísticas:** mínimo, máximo, mediana, moda, média, desvio-padrão e ausências para as contínuas; frequências para as categóricas.
- **Relações:** medianas por classe, correlação de Spearman (adequada a alvo ordinal), qui-quadrado e V de Cramér (H3) e proporção da classe `High` por categoria (H3 e H4).
- **Figuras:** seis figuras selecionadas por relevância analítica, cada uma com *achado*, *evidência* e *relação com as hipóteses e limite*.

### 7.2 Decisões documentadas

| Decisão | Problema observado | Alternativas | Escolha |
|---|---|---|---|
| **D1** — colunas de vazamento e identificador | `Patient_ID` é identificador; `Diabetes_Risk_Score`, `AI_Health_Recommendation` e `Doctor_Consultation_Needed` derivam do próprio risco | manter × remover | **Remover** as quatro colunas, para evitar tautologia |
| **D2** — valores ausentes | 14 colunas com ausências (0,99% a 5,92%) | excluir linhas × imputar (mediana/moda do treino) | **Imputar**, preservando linhas e a classe `Low`; comparação efetiva de perda de linhas e de distorção de média e desvio-padrão |
| **D3** — extremos de BMI | 1.208 linhas (3,0% do treino) com BMI < 15 e 23 extremos altos acima do limite de Tukey | manter × remover linhas × anular e imputar | **Manter**: o BMI coincide com peso/altura² em todas as linhas avaliáveis; anular criaria incoerência interna e remover eliminaria casos `Low` |

### 7.3 Prevenção de vazamento

- A partição **80% treino / 20% teste** é **estratificada** pelo alvo, com `random_state=42`, e acontece **antes** de qualquer etapa que aprenda parâmetros.
- Imputação (mediana e moda), codificação, escalonamento e discretização usam `fit` **somente no treino**; o teste recebe apenas `transform`.
- A análise exploratória é descritiva e não alimenta nenhuma transformação; os limites de Tukey usados em D3 são calculados só no treino.
- Verificação: índices de treino e teste disjuntos (`assert`) e proporção de classes praticamente idêntica nos dois conjuntos.

### 7.4 Visões geradas

| Visão | Transformação adicional | Uso futuro previsto |
|---|---|---|
| `onehot` | nenhuma (apenas imputação e codificação) | árvores e ensembles |
| `padronizado` | média 0 e desvio 1 | regressão logística, SVM, k-NN |
| `normalizado` | intervalo [0, 1] | redes neurais |
| `discretizado` | 5 faixas por quantis (*one-hot*) | Naive Bayes, regras |

Em todas: imputação (mediana para contínuas; moda para categóricas e binárias), codificação *one-hot* das categóricas (`drop="if_binary"`, categorias com frequência < 1% agrupadas). Não foram aplicados PCA, balanceamento de classes nem criação de variáveis, por falta de necessidade demonstrada nesta etapa.

## 8. Principais resultados

### 8.1 Qualidade dos dados

- 50.000 linhas e 41 colunas; **0 duplicatas** (com e sem o ID).
- **14 colunas** com ausências, entre 0,99% e 5,92% (a maior é `Weight_kg`); nenhum código numérico especial de ausência.
- Inconsistências clínicas (mantidas e registradas como limitação):

| Regra | Violações | % das linhas avaliadas |
|---|---|---|
| Pressão diastólica > sistólica | 3.754 | 7,5% |
| LDL + HDL > colesterol total | 17.266 | 36,7% |
| Glicose < 100 com HbA1c ≥ 6,5% (heurística fraca) | 5.896 | 12,5% |
| IMC diferente de peso/altura² | 0 | 0,0% |

- **Alvo desbalanceado:** `High` 73,2%, `Moderate` 25,9% e `Low` 0,9% (470 casos).

### 8.2 Respostas às hipóteses

| Pergunta | Resposta | Evidência |
|---|---|---|
| **H1** | Parcialmente confirmada | Spearman com o risco: `Fasting_Blood_Sugar` ρ = 0,38 (moderada); `HbA1c` ρ = 0,27 (fraca); `Blood_Glucose` ρ = 0,005 (desprezível) |
| **H2** | Confirmada (associação fraca) | `BMI` ρ = 0,17 e `Age` ρ = 0,21; medianas crescentes de `Low` a `High` |
| **H3** | Confirmada | `High` em 82,9% com histórico familiar `Yes` contra 63,5% com `No`; χ² = 2.439,8, p < 0,001, V de Cramér = 0,221 (força fraca) |
| **H4** | Parcialmente confirmada | `High` em 80,7% (atividade Low), 69,7% (Moderate) e 69,0% (High); mediana de horas por semana praticamente igual entre as classes (5,1 → 5,1 → 5,0) |

Todas as associações são descritivas e não indicam causalidade. "Significativo" só é usado onde há teste estatístico (H3).

### 8.3 Verificações do pré-processamento

- Treino com 40.000 linhas e teste com 10.000 (80% / 20%); nenhuma linha perdida.
- **0 valores ausentes** em todas as visões após o pré-processamento.
- Proporção de classes idêntica em treino e teste: 0,9% / 25,9% / 73,2%.
- Padronizado: |média| máxima da ordem de 10⁻¹⁵ e desvio 1,000; normalizado: de 0 a 1 em treino e teste.
- Número de colunas de atributos: 80 (`onehot`, `padronizado`, `normalizado`) e 160 (`discretizado`).

## 9. Dados processados (saídas)

Cada arquivo `diabetes_<visão>_<partição>.csv.gz` contém os atributos processados e, na **última coluna**, o alvo `Diabetes_Risk` codificado (0 = `Low`, 1 = `Moderate`, 2 = `High`). Os arquivos são gravados com `float_format="%.6g"` e compactados em gzip.

| Visão | Colunas (atributos + alvo) |
|---|---|
| `onehot`, `padronizado`, `normalizado` | 80 + 1 |
| `discretizado` | 160 + 1 |

Exemplo de leitura (com a pasta `dados_processados/` no repositório):

```python
import pandas as pd

base = ("https://raw.githubusercontent.com/renatobacha/"
        "MVP-AnalisedeDadosPUCRIO/main/dados_processados/")

treino = pd.read_csv(base + "diabetes_padronizado_train.csv.gz")
teste = pd.read_csv(base + "diabetes_padronizado_test.csv.gz")

X_train, y_train = treino.drop(columns="Diabetes_Risk"), treino["Diabetes_Risk"]
X_test, y_test = teste.drop(columns="Diabetes_Risk"), teste["Diabetes_Risk"]
```

> Como `Low` tem poucos casos (cerca de 94 no teste), use validação estratificada e métricas robustas ao desbalanceamento (F1 macro, recall por classe, kappa ponderado) em qualquer modelagem futura.

## 10. Reprodutibilidade

| Elemento | Valor |
|---|---|
| Semente aleatória | `RANDOM_STATE = 42` |
| Proporção de teste | `TEST_SIZE = 0.20` (estratificada) |
| Parâmetros principais | `IQR_MULTIPLIER = 1.5`, `N_DISCRETIZATION_BINS = 5`, `MIN_CATEGORY_FREQUENCY = 0.01`, `BMI_TOLERANCE = 0.5`, `BMI_LIMIAR_BAIXO = 15.0` |
| Dados | lidos pela URL *raw*, sem credenciais |
| Ordem de execução | todas as células, na ordem, em ambiente novo |

Todos os parâmetros ficam em uma única célula de constantes (seção 3). Alterá-los atualiza as tabelas e os textos de interpretação, que são gerados por código.

## 11. Limitações

1. A base parece **sintética** (distribuições aproximadamente uniformes, incoerências clínicas entre variáveis e alta proporção de pacientes em alto risco), o que limita a validade externa. Os resultados descrevem a base analisada.
2. A classe `Low` tem apenas 470 casos, e comparações que a envolvem são imprecisas.
3. O **mecanismo de ausência é desconhecido**; a imputação por mediana é simples e reduz levemente a variância.
4. As regras clínicas de consistência são **heurísticas**; os registros incoerentes foram mantidos.
5. Valores extremos de `BMI` foram mantidos por serem consistentes com peso e altura, mas são clinicamente extremos e exigiriam auditoria em dados reais. O `BMI` é redundante com `Weight_kg` e `Height_cm`.
6. Todas as associações são **descritivas**, não causais.
7. Significados e unidades de várias colunas foram inferidos dos nomes, porque a fonte não traz dicionário.

## 12. Próximos passos

- Treinar modelos de classificação ordinal ou multiclasse (por exemplo, regressão logística ordinal e *gradient boosting*) sobre as quatro visões.
- Usar validação cruzada estratificada e métricas robustas ao desbalanceamento (F1 macro, recall por classe, kappa ponderado).
- Avaliar ponderação de classes **apenas no treino**.
- Se houver acesso, validar em dados reais e auditar clinicamente os extremos de `BMI`.

## 13. Solução de problemas

| Sintoma | Causa provável | Solução |
|---|---|---|
| `RuntimeError: Não foi possível ler o dataset da URL raw` | Sem internet ou nome/caminho do CSV diferente no repositório | Verifique a conexão e se `diabetes_risk_prediction_dataset.csv` está na raiz da branch `main`; ou use o caminho local em `DATA_URL` |
| `KeyError: Colunas esperadas ausentes no CSV` | Arquivo diferente do original do Kaggle | Use o CSV original ou ajuste os nomes das colunas nas constantes |
| Erro em `KBinsDiscretizer` ou `set_output` | scikit-learn antigo | Atualize: `pip install -U scikit-learn` (mínimo 1.2) |
| Link do Colab não abre o notebook | Nome do arquivo no GitHub diferente do usado no link | Renomeie o arquivo ou ajuste o link do selo e do cabeçalho do notebook |
| Textos de interpretação ausentes ao abrir o notebook | Notebook salvo sem saídas | Execute todas as células e salve novamente com as saídas |
| `FileNotFoundError` ao ler `dados_processados/` localmente | Pasta ainda não gerada | Execute o notebook até a seção 5.5 |

## 14. Declaração de uso de IA

Foram utilizadas ferramentas de IA generativa como apoio técnico e editorial: **Gemini**, para formatação de código Python, estruturação de blocos textuais e padronização do template da disciplina; e **Claude (Anthropic)**, para revisar o notebook frente ao enunciado, sugerir correções de código e reorganizar as seções e os textos de interpretação. O problema, a escolha da base, as hipóteses e as decisões foram definidos pelo autor, que revisou o conteúdo, executou o notebook completo no Google Colab e é responsável pelo resultado final.

## 15. Referências

- Kaggle. *Diabetes Risk Prediction Dataset (50K Patients)*. Autor da página: mobeenfatimah. https://www.kaggle.com/datasets/mobeenfatimah/diabetes-risk-prediction-dataset-50k-patients. Licença CC BY-SA 4.0. Acesso em: novembro de 2026.
- Pedregosa, F. et al. (2011). Scikit-learn: Machine Learning in Python. *Journal of Machine Learning Research*, 12, 2825–2830.
- McKinney, W. (2010). Data Structures for Statistical Computing in Python. *Proceedings of the 9th Python in Science Conference*, 51–56.
- Harris, C. R. et al. (2020). Array programming with NumPy. *Nature*, 585, 357–362.
- Virtanen, P. et al. (2020). SciPy 1.0: fundamental algorithms for scientific computing in Python. *Nature Methods*, 17, 261–272.
- Hunter, J. D. (2007). Matplotlib: A 2D Graphics Environment. *Computing in Science & Engineering*, 9(3), 90–95.
- Waskom, M. (2021). seaborn: statistical data visualization. *Journal of Open Source Software*, 6(60), 3021.
- Tukey, J. W. (1977). *Exploratory Data Analysis*. Addison-Wesley.
