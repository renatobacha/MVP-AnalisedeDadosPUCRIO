MVP – Análise de Dados e Boas Práticas
Predição de Risco de Diabetes: análise exploratória e pré-processamento de dados

Autor: Renato Penna Magoulas Bacha
Disciplina: Análise de Dados e Boas Práticas
Dataset: Diabetes Risk Prediction Dataset (50K patients) – Kaggle

1. Objetivo

Este trabalho examina um conjunto de 50.000 registros de pacientes, com 41 atributos, e prepara os dados para a classificação multiclasse do nível de risco de diabetes (Diabetes_Risk: Low < Moderate < High).

O escopo compreende a definição do problema, a análise exploratória e o pré-processamento. A modelagem não integra este MVP. Como produto, obtêm-se bases limpas, sem atributos indevidos, particionadas em treino e teste e transformadas em quatro visões distintas, prontas para diferentes famílias de algoritmos.

2. Conteúdo do repositório
Arquivo / pasta	Descrição
MVP_Diabetes_Analise_Exploratoria_ULTRA_FINAL.ipynb	Notebook (Google Colab) com o relatório completo: texto, código, gráficos e análises
diabetes_risk_prediction_dataset.csv	Dataset original do Kaggle, sem alterações (50.000 linhas e 41 colunas)
dados_processados/	Quatro visões pré-processadas do dataset, em treino e teste (.csv.gz)
README.md	Este resumo
3. Reprodução
Abrir o notebook no Google Colab (Arquivo → Abrir notebook → GitHub, ou por upload do arquivo .ipynb).
Executar todas as células (Ambiente de execução → Executar tudo).

O dataset é lido diretamente deste repositório pela URL raw, sem necessidade de download manual. O endereço e os demais parâmetros do projeto (alvo, ordem das classes, constantes das análises) estão concentrados na célula "Parâmetros do projeto":

python
DATA_URL = ("https://raw.githubusercontent.com/renatobacha/"
            "MVP-AnalisedeDadosPUCRIO/main/"
            "diabetes_risk_prediction_dataset.csv")

Bibliotecas utilizadas: pandas, numpy, matplotlib, seaborn e scikit-learn, todas disponíveis no ambiente do Colab.

4. Estrutura do notebook

O notebook segue a estrutura do template da disciplina:

Descrição do Problema: hipóteses, tipo de problema (aprendizado supervisionado, classificação multiclasse), seleção de dados e dicionário dos 41 atributos.
Importação das Bibliotecas Necessárias e Carga de Dados.
Análise de Dados: instâncias e tipos, valores nulos, duplicidades, consistência clínica, estatísticas descritivas, outliers, histogramas, boxplots, matriz de correlação e tratamento de valores nulos. Cada visualização é seguida de uma análise.
Pré-Processamento de Dados: limpeza, partição treino/teste, normalização, padronização e demais transformações, com retorno à análise exploratória.
Respondendo nossas hipóteses: evidências associadas a cada hipótese.
Conclusão: síntese, pontos de atenção e próximos passos.
5. Principais resultados

Natureza dos dados. Os atributos apresentam distribuição aproximadamente uniforme e são praticamente independentes entre si, com inconsistências clínicas frequentes: a pressão diastólica excede a sistólica em 7,5% dos registros e LDL + HDL superam o colesterol total em 36,7% dos registros avaliáveis. Esses indícios, somados à descrição do Kaggle, caracterizam os dados como sintéticos.

Alvo desbalanceado. High representa 73,2% dos pacientes, Moderate 25,9% e Low 0,9%.

Vazamento de dados. Diabetes_Risk_Score (cujas faixas não se sobrepõem entre as classes, sendo o alvo uma discretização da pontuação), AI_Health_Recommendation e Doctor_Consultation_Needed foram removidas, assim como o identificador Patient_ID.

Atributos mais associados ao risco:

Atributo	Correlação com o alvo (r)
Fasting_Blood_Sugar	+0,38
HbA1c	+0,28
Family_History_Diabetes = Yes	+0,22
Age	+0,21
BMI	+0,16

Nenhum atributo, isoladamente, separa bem as classes. Blood_Glucose não acompanha o risco (r ≈ 0,005), ao contrário da glicemia de jejum e da HbA1c. BMI é derivado de peso e altura, o que gera multicolinearidade (r = 0,84 com Weight_kg).

Hipóteses
Hipótese	Resultado
H1: indicadores glicêmicos crescem com o risco	Parcialmente confirmada
H2: IMC e idade crescem com o risco	Confirmada (efeito fraco)
H3: histórico familiar eleva o risco (82,9% de High contra 63,5%)	Confirmada
H4: mais atividade física, menor risco	Parcialmente confirmada
6. Pré-processamento
Etapa	Procedimento
Colunas removidas	Patient_ID e as três colunas que vazam o alvo
Duplicidades	Nenhuma encontrada
Valores ausentes	14 atributos com 1% a 6%; imputação por mediana (numéricos) e moda (categóricos), ajustadas apenas no treino
Outliers	Apenas BMI (0,06%); mantidos
Partição	80% treino (40.000) e 20% teste (10.000), estratificada, realizada antes de qualquer transformação
Codificação	One-hot encoding das variáveis categóricas
Visões do dataset (dados_processados/)
Visão	Transformação	Preditores
onehot	Imputação + one-hot, escala original	80
padronizado	+ StandardScaler (média 0, desvio-padrão 1)	80
normalizado	+ MinMaxScaler ([0, 1])	80
discretizado	+ 5 faixas por quantis	160

Os arquivos seguem o padrão diabetes_<visão>_train.csv.gz e diabetes_<visão>_test.csv.gz, incluem o alvo Diabetes_Risk (Low = 0, Moderate = 1, High = 2) e são lidos diretamente com pd.read_csv.

7. Limitações
Por serem sintéticos, os padrões observados refletem o processo de geração dos dados e não a realidade clínica; as conclusões aplicam-se apenas a este dataset.
O desbalanceamento exige, na modelagem, métricas por classe, validação cruzada estratificada e class_weight ou oversampling restrito ao treino.
Os registros clinicamente inconsistentes foram mantidos, pois não é possível identificar qual valor é o incorreto.
8. Próximos passos

Treinar modelos de classificação multiclasse (regressão logística, árvores de decisão, random forest e gradient boosting) a partir das visões salvas, avaliá-los por F1 macro, recall por classe e matriz de confusão, e validá-los por validação cruzada estratificada.
