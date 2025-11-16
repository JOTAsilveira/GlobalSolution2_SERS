# Global Solution – Eficiência Energética de Edifícios e Futuro do Trabalho (SERS)

Este repositório contém a solução desenvolvida para a Global Solution da FIAP, no contexto do **Future Skills Lab**, com foco em **eficiência energética de edifícios** e sua relação com o **futuro do trabalho**.

O projeto utiliza dados reais de desempenho energético de construções para entender como características de projeto (como compacidade, área de vidro e altura) impactam a **carga de resfriamento** de um prédio — diretamente ligada ao consumo de energia com climatização em ambientes de trabalho.

---

## 1. Contexto

Ambientes de trabalho modernos (escritórios, coworkings, centros corporativos) dependem fortemente de climatização para manter conforto térmico. Em países e regiões de clima mais quente, o gasto com **ar-condicionado** e sistemas de resfriamento representa uma parcela significativa da conta de energia.

Ao mesmo tempo, empresas buscam:

- Reduzir custos operacionais;
- Atender a metas de sustentabilidade e ESG;
- Criar ambientes de trabalho mais saudáveis, confortáveis e inteligentes.

Nesse cenário, projetar edifícios energeticamente eficientes e entender **quais fatores de projeto aumentam ou reduzem a carga de resfriamento** é essencial. Este projeto usa dados abertos para mostrar como técnicas de **análise de dados e aprendizado de máquina** podem ajudar nessa tomada de decisão.

---

## 2. Objetivo do Projeto

O objetivo principal deste projeto é:

- Analisar um conjunto de dados de eficiência energética de edifícios (Energy Efficiency Data Set);
- Investigar relações entre características construtivas (área, altura, compacidade, área de vidro, etc.) e a **carga de resfriamento**;
- Construir um modelo de **Regressão Linear** para prever a carga de resfriamento a partir dessas características;
- Interpretar os coeficientes do modelo para identificar quais fatores tornam um edifício **mais ou menos eficiente energeticamente**;
- Conectar esses resultados ao **futuro do trabalho**, mostrando como decisões de projeto impactam o consumo de energia em ambientes produtivos.

---

## 3. Dados Utilizados

- **Dataset:** Energy Efficiency Data Set  
- **Origem:** Disponível em repositórios públicos (ex.: UCI / Kaggle).  
  - Link sugerido (Kaggle): `https://www.kaggle.com/datasets/ujjwalchowdhury/energy-efficiency-data-set?utm_source=chatgpt.com`  
- **Arquivo utilizado no projeto:** `energy_efficiency_data.csv`

### 3.1. Principais colunas

O dataset contém, entre outras, as seguintes variáveis:

**Features (entradas do modelo):**

- `Relative_Compactness` – Compacidade relativa do edifício;
- `Surface_Area` – Área de superfície;
- `Wall_Area` – Área das paredes;
- `Roof_Area` – Área do telhado;
- `Overall_Height` – Altura total;
- `Orientation` – Orientação do edifício;
- `Glazing_Area` – Área de vidros;
- `Glazing_Area_Distribution` – Distribuição da área de vidros.

**Targets (variáveis-alvo do dataset):**

- `Heating_Load` – Carga de aquecimento (não utilizada como alvo neste projeto);
- `Cooling_Load` – Carga de resfriamento (variável que queremos prever).

Neste projeto, focamos na previsão da **Cooling_Load**, que está diretamente relacionada ao consumo de energia com ar-condicionado e refrigeração.

---

## 4. Metodologia

Toda a implementação está concentrada no arquivo:

- `GlobalSolution_SERS.ipynb` (notebook Jupyter, originalmente desenvolvido em Google Colab).

As principais etapas do código são:

### 4.1. Carregamento dos dados

- Leitura do arquivo `energy_efficiency_data.csv` com `pandas`;
- Exibição do número de linhas e colunas;
- Renomeação opcional de colunas com um dicionário (`rename_dict`), caso o dataset venha em outro formato.

### 4.2. Análise Exploratória

- Impressão de `info()` e `describe().T` para entender tipos de dados, faixas de valores e estatísticas básicas;
- Verificação de valores ausentes (`df.isna().sum()`).

### 4.3. Visualização das Distribuições

- Seleção das colunas numéricas;
- Plotagem de histogramas com `seaborn.histplot` + `kde=True` para cada variável numérica;
- Objetivo: entender a distribuição das variáveis de entrada e das cargas de aquecimento/resfriamento.

### 4.4. Matriz de Correlação

- Cálculo da matriz de correlação numérica com `df.corr(numeric_only=True)`;
- Visualização via `sns.heatmap` (anotações, mapa de calor colorido);
- Identificação de quais variáveis têm maior correlação com a **Cooling_Load**.

### 4.5. Preparação dos Dados para Regressão Linear

- Definição da variável alvo: `target_col = 'Cooling_Load'`;
- Definição da matriz de features `X` removendo `Heating_Load` e `Cooling_Load` do dataframe;
- Definição do vetor alvo `y = df['Cooling_Load']`;
- Divisão em treino e teste usando `train_test_split` com `test_size=0.2` e `random_state=42`.

### 4.6. Treinamento do Modelo

- Uso de `LinearRegression` do `scikit-learn`;
- Treinamento do modelo com `model.fit(X_train, y_train)`.

### 4.7. Avaliação do Modelo

- Predição para treino e teste (`y_pred_train`, `y_pred_test`);
- Cálculo das métricas:
  - **MAE** (Mean Absolute Error);
  - **MSE** e **RMSE** (root mean squared error);
  - **R²** (coeficiente de determinação).
- Função `avaliar_modelo()` encapsula esse cálculo e imprime os resultados para treino e teste.

### 4.8. Visualização Real vs. Previsto

- Gráfico de dispersão (`plt.scatter`) comparando valores reais de `Cooling_Load` no conjunto de teste com as previsões do modelo;
- Linha diagonal pontilhada representando o cenário ideal (previsão perfeita).

### 4.9. Interpretação dos Coeficientes

- Criação de um `DataFrame` com:
  - `feature`: nome de cada variável de entrada;
  - `coeficiente`: valor do coeficiente da regressão para aquela variável.
- Ordenação dos coeficientes para ver quais variáveis mais impactam a carga de resfriamento;
- Interpretação:
  - Coeficientes **positivos** → aumentam a carga de resfriamento quando a variável cresce;
  - Coeficientes **negativos** → reduzem a carga de resfriamento quando a variável cresce.

---

## 5. Principais Resultados

### 5.1. Desempenho do Modelo

**Conjunto de Treino:**

- **MAE:** 2.253  
- **RMSE:** 3.198  
- **R²:** 0.886  

**Conjunto de Teste:**

- **MAE:** 2.195  
- **RMSE:** 3.145  
- **R²:** 0.893  

Esses resultados indicam que:

- O modelo apresenta **boa capacidade de explicação** da variabilidade da carga de resfriamento (R² ≈ 0,89);
- Não há overfitting severo, pois o desempenho em treino e teste é muito semelhante;
- O erro médio absoluto em torno de ~2 unidades de carga é relativamente baixo para o contexto do dataset.

### 5.2. Importância das Variáveis (Coeficientes da Regressão)

Tabela de coeficientes estimados pelo modelo:

| feature                     | coeficiente   | Interpretação básica                                     |
|----------------------------|--------------:|----------------------------------------------------------|
| `Glazing_Area`            |  14.787587    | Mais área de vidro ↑ → maior carga de resfriamento      |
| `Overall_Height`          |   4.046160    | Edifícios mais altos tendem a ter maior carga           |
| `Orientation`             |   0.055210    | A orientação tem impacto positivo, mas pequeno           |
| `Glazing_Area_Distribution` | 0.033740   | Distribuição da área de vidro afeta levemente a carga   |
| `Wall_Area`               |   0.025423    | Maior área de parede aumenta um pouco a carga           |
| `Roof_Area`               |  -0.047322    | Maior área de telhado reduz levemente a carga           |
| `Surface_Area`            |  -0.069220    | Maior área de superfície está associada a menor carga   |
| `Relative_Compactness`    | -71.084528    | Edifícios mais compactos exigem bem menos resfriamento  |

**Principais insights:**

- **Mais área de vidro (`Glazing_Area`)** → aumenta bastante a carga de resfriamento, o que faz sentido porque o ganho de calor por radiação solar é maior;
- **Maior compacidade (`Relative_Compactness`)** → está fortemente associada a menor carga de resfriamento (coeficiente negativo grande). Edifícios mais compactos perdem menos energia para o ambiente;
- Características como **altura total**, **área de paredes** e **orientação** também influenciam, mas com menor intensidade que compacidade e área de vidro.

---

## 6. Conexão com o Futuro do Trabalho

Os resultados deste projeto podem ser diretamente conectados ao futuro do trabalho em diversos aspectos:

- **Projetos de edifícios corporativos mais eficientes:** Ao compreender quais parâmetros de projeto impactam mais a carga de resfriamento, arquitetos e engenheiros podem tomar decisões mais inteligentes ao planejar escritórios e ambientes de trabalho.
- **Redução de custos operacionais:** Menor carga de resfriamento implica menor consumo de energia com ar-condicionado, reduzindo custos recorrentes para empresas.
- **Sustentabilidade e ESG:** Edifícios energeticamente eficientes contribuem para redução de emissões de CO₂ e metas de sustentabilidade, alinhando o ambiente de trabalho a práticas responsáveis.
- **Ambientes de trabalho confortáveis e inteligentes:** Aliar design eficiente, automação e monitoramento contínuo do consumo de energia leva a espaços mais confortáveis, saudáveis e produtivos para as pessoas.
- **Uso de dados e IA na tomada de decisão:** O projeto demonstra como técnicas simples de machine learning podem apoiar decisões de infraestrutura, algo cada vez mais comum no contexto de **cidades inteligentes** e **smart buildings**.

---

## Como Executar o Projeto (Google Colab)

Este projeto pode ser executado diretamente no **Google Colab**, sem necessidade de instalação local.

### 1. Abrir o notebook
Clique no link abaixo para abrir o notebook no Colab:

->`https://colab.research.google.com/drive/1s_GNOdAPkC-MzzlTrFUDArAqG7ebQxa9?authuser=1#scrollTo=fg4ngEPcNmnD`

Se preferir transformar o link do GitHub para Colab manualmente, basta substituir o início da URL por:
