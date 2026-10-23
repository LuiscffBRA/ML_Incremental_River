# Machine Learning Incremental com a Biblioteca River

Repositório desenvolvido para o seminário da disciplina de **Aprendizagem de Máquina** da Universidade Federal do Rio Grande do Norte (UFRN).

**Docente:** Prof. Josenalde Oliveira  
**Discente:** Luis Carlos Firmino Façanha 
**Tema:** Machine Learning Incremental (River)

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/)

---

## Objetivo do Projeto

Validar a tecnologia de **Aprendizado de Máquina Incremental (Online Learning)** utilizando a biblioteca **River**, comparando seu desempenho, consumo e adaptabilidade frente ao modelo tradicional em lote (**Batch Learning**) do **Scikit-Learn**.

O projeto utiliza o mesmo problema de regressão linear abordado em sala de aula (`y = 4 + 3*x1 + ε`) e expande o conceito para cenários de fluxo contínuo (*data streams*) e mudança de distribuição (*Concept Drift*).

---

## Metodologia e Experimentos

O experimento foi dividido em três etapas no Google Colab:

1. **Baseline em Lote (Scikit-Learn):**
   * Treinamento com `LinearRegression` exigindo todas as 1500 amostras carregadas na memória RAM (`.fit()`).
2. **Modelo Incremental (River):**
   * Criação de Pipeline de streaming: `StandardScaler | LinearRegression`.
   * Avaliação pré-sequencial (*test-then-train*): cada amostra é usada primeiro para teste (`predict_one`), computação da métrica dinâmica (`MAE.update`), e em seguida para atualização dos pesos (`learn_one`).
3. **Detecção de Concept Drift (ADWIN):**
   * Injeção de uma mudança abrupta na função geradora na amostra 1000  (`y = 10 - 5*x1 + ε`).
   * Monitoramento contínuo dos resíduos utilizando o algoritmo estatístico **ADWIN** (*Adaptive Windowing*).

---

## Resultados Obtidos

### 1. Comparativo de Métricas

| Métrica / Parâmetro | Scikit-Learn (Batch) | River (Incremental) |
| :--- | :--- | :--- |
| **Paradigma** | Em Lote (`.fit()`) | Fluxo Amostra a Amostra (`.learn_one()`) |
| **Uso de Memória** | Escala com `N` (`O(N)`) | Constante (`O(1)`) |
| **Formato de Entrada** | Matrizes 2D NumPy | Dicionários Python (`dict`) |
| **Intercepto (`w0`)** | 3.9723 (Teórico: 4.0) | Recuperado via SGD |
| **Coeficiente (`w1`)** | 3.0590 (Teórico: 3.0) | Recuperado via SGD |
| **MAE Final** | **0.7856** | **~0.95 (Convergência progressiva)** |
| **Reação a Concept Drift** | Cego (exige retreino do zero) | **Detecta em tempo real (ADWIN)** |

### 2. Convergência do Erro

O modelo incremental inicia com erro elevado e rapidamente converge para o mesmo patamar do modelo Batch a partir de ~300 amostras processadas.

### 3. Detecção de Concept Drift

Após a mudança estrutural dos dados na amostra **1000**, o algoritmo **ADWIN** detectou a anomalia estatística na amostra **1023** (latência de apenas 23 amostras), disparando o alerta de necessidade de adaptação.

---

## Tecnologias Utilizadas

* **Linguagem:** Python 3.10+
* **Online Machine Learning:** [River](https://riverml.xyz/)
* **Batch Machine Learning:** [Scikit-Learn](https://scikit-learn.org/)
* **Manipulação e Gráficos:** NumPy, Matplotlib

---

## Como Executar

1. Clone este repositório ou abra o notebook diretamente pelo botão do Google Colab no topo desta página.
2. Instale as dependências:

   ```bash
   pip install river scipy scikit-learn "numpy<2.3" -U -q
   ```

3. Execute as células do notebook em ordem.
