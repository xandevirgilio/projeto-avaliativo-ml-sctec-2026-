# Pipeline Preditivo de Machine Learning: Análise Estatística e Decisão de Negócios

> 💡 **Nota de Visualização:** O notebook completo com todos os outputs, tabelas e gráficos está renderizado diretamente no arquivo `projeto_ml.ipynb`. Uma versão em HTML de backup também está disponível no repositório como `projeto_ml.html`.

Este repositório contém o projeto prático avaliativo desenvolvido para o módulo de **Machine Learning e Visão Computacional**. O objetivo deste trabalho é construir um pipeline preditivo de ponta a ponta, aplicando o rigor científico na preparação de dados, engenharia de recursos (*Feature Engineering*), sintonia de hiperparâmetros, combate ao *overfitting* e, fundamentalmente, traduzindo métricas estatísticas em decisões financeiras estratégicas para a diretoria executiva.

---

## 1. Contexto e Problema de Negócio

O cenário deste projeto envolve a atuação como um Cientista de Dados Corporativo para uma instituição financeira de grande porte. O desafio consiste em mitigar o risco de crédito prevendo a inadimplência antes que o capital seja concedido.

*   **Variável Alvo (`loan_status`):** 
    *   `0`: Cliente adimplente (pagará o empréstimo em dia).
    *   `1`: Cliente inadimplente (risco de calote).
*   **A Dor do Negócio (Trade-off de Erros):**
    *   **Falso Positivo (FP):** Classificar um bom pagador como "Risco de Calote". O banco nega o crédito injustamente. **Impacto:** Perda do cliente para a concorrência e perda de receita esperada com juros (*Custo de Oportunidade*).
    *   **Falso Negativo (FN):** Classificar um mau pagador como "Seguro". O banco concede o crédito. **Impacto:** Perda severa e direta do capital principal emprestado (*Prejuízo Financeiro Líquido*).

---

## 2. Dicionário de Dados

Abaixo está mapeada a estrutura das variáveis utilizadas no pipeline, incluindo os tipos de dados originais e a nova variável estratégica criada na Fase de *Feature Engineering*.

| Nome da Variável | Tipo de Dado | Descrição Completa | Tipo de Recurso |
| :--- | :--- | :--- | :--- |
| `person_age` | Numérico (Int) | Idade do solicitante do crédito | Preditora (Original) |
| `person_income` | Numérico (Float) | Renda anual informada pelo cliente | Preditora (Original) |
| `person_home_ownership` | Categórico (String)| Tipo de propriedade da residência (Alugada, Própria, etc.) | Preditora (Original) |
| `person_emp_length` | Numérico (Float) | Tempo de emprego atual em anos | Preditora (Original) |
| `loan_intent` | Categórico (String)| Intenção/motivo do empréstimo (Pessoal, Educação, Venture, etc.)| Preditora (Original) |
| `loan_grade` | Categórico (String)| Nota de crédito interna atribuída ao perfil do cliente | Preditora (Original) |
| `loan_amnt` | Numérico (Float) | Valor total total do empréstimo solicitado | Preditora (Original) |
| `loan_int_rate` | Numérico (Float) | Taxa de juros aplicada ao contrato | Preditora (Original) |
| `loan_percent_income` | Numérico (Float) | Percentual da renda que o empréstimo representa na origem | Preditora (Original) |
| `cb_person_default_on_file`| Categórico (String)| Histórico de inadimplência prévia registrado (Sim / Não) | Preditora (Original) |
| `cb_person_cred_hist_length`| Numérico (Int) | Tempo em anos do primeiro registro de crédito ativo | Preditora (Original) |
| **`comprometimento_renda`** | **Numérico (Float)** | **Métrica percentual calculada: `(loan_amnt / person_income) * 100`** | **Preditora (Calculada)** |
| `loan_status` | Numérico (Binary) | Indicador final de inadimplência (0 = Não, 1 = Sim) | **Variável Alvo (Target)** |

---

## 3. Fluxo de Desenvolvimento e Governança (Git/GitHub)

O desenvolvimento deste ecossistema de machine learning seguiu as boas práticas de engenharia de software e versionamento corporativo, estruturado por meio de ramificações (*branches*) específicas por entrega técnica para garantir a rastreabilidade do código.

### Estrutura de Branches Utilizada
*   `main`: Código em estado estável de produção, documentado e revisado.
*   `fase/eda`: Isolamento para análise exploratória de dados e plotagem gráfica inicial.
*   `fase/data-prep`: Tratamento de nulos, duplicados, outliers e criação da coluna calculada.
*   `fase/modelagem`: Ajuste de hiperparâmetros, validação paralela (Treino/Teste) e avaliação de negócio.

### Padrão de Commits Semânticos Implementado
*   `feat:` Adição de novas funcionalidades (ex: `feat: aplica SMOTE nos dados de treino exclusivamente`).
*   `fix:` Correções de bugs ou erros lógicos (ex: `fix: corrige divisao por zero na feature engineering`).
*   `docs:` Alterações puramente documentais (ex: `docs: atualiza dicionario de dados no readme`).
*   `refactor:` Otimizações de código que não alteram o comportamento final da aplicação.

---

## 4. Resumo Executivo: Insights da Análise Exploratória (EDA)

*   **Volumetria de Dados:** A base de dados original apresenta um volume massivo de **32.581 linhas** e **12 colunas** operacionais.
*   **Desbalanceamento Crítico:** A classe alvo (`loan_status`) demonstrou uma distribuição altamente assimétrica, contendo **78,18%** de clientes adimplentes (25.473 registros na Classe 0) e apenas **21,82%** de inadimplentes (7.108 registros na Classe 1). Esse fator justifica a aplicação obrigatória da técnica de reamostragem sintética (*SMOTE*) na fase de modelagem para evitar o viés do algoritmo.
*   **Descoberta de Anomalias e Nulos:** O sumário descritivo revelou dados ausentes em `loan_int_rate` (taxa de juros) e `person_emp_length` (tempo de emprego). Além disso, detectamos outliers severos de digitação/registro, como idade máxima de **144 anos** e tempo de emprego de **123 anos**, os quais receberão tratamento de limpeza na próxima fase do pipeline.
*   **Correlações Relevantes:** O mapa de calor de correlação de Pearson revelou uma forte associação linear entre as variáveis `loan_amnt` e `loan_percent_income` (0.57), além de uma correlação natural esperada entre a idade do cliente e o tempo do seu histórico de crédito ativo (0.86).
---

## 5. Resultados dos Modelos e Métricas de Desempenho

O pipeline de Machine Learning avaliou dois algoritmos concorrentes utilizando a base de teste original (não balanceada) para garantir a fidelidade dos resultados em produção.

### Algoritmo 1: K-Nearest Neighbors (KNN)
*   **Acurácia:** 78%
*   **Métricas da Classe Crítica (Inadimplente - 1):**
    *   *Precision:* 0.49 (Alto índice de falsos alarmes, classificando adimplentes como de risco).
    *   *Recall:* 0.68 (Capturou apenas 68% dos clientes em situação de inadimplência).
    *   *F1-Score:* 0.57

### Algoritmo 2: Árvore de Decisão (Decision Tree) - CAMPEÃO
*   **Acurácia:** 91%
*   **Métricas da Classe Crítica (Inadimplente - 1):**
    *   *Precision:* 0.83 (Excelente assertividade ao apontar um perfil de risco).
    *   *Recall:* 0.73 (Identificou com eficácia 73% da inadimplência real da base de teste).
    *   *F1-Score:* 0.78

### Justificativa de Escolha do Modelo
A **Árvore de Decisão** foi selecionada como o modelo de produção para este projeto. Ela superou o KNN em todas as métricas essenciais, demonstrando robustez contra a alta dimensionalidade gerada pelas variáveis categóricas encodadas. O Recall de 73% atrelado a uma precisão de 83% garante um equilíbrio financeiro saudável para a instituição, mitigando o risco de crédito sem bloquear clientes legítimos.

---

## 6. Monitoramento de Overfitting e Otimização

Abaixo estão consolidadas as matrizes de experimentos para diagnóstico de sobreajuste de ambos os algoritmos clássicos.

### Otimização do KNN (Hiperparâmetro K)
| Cenário | Valor de `n_neighbors` | Acurácia/F1 (Treino) | Acurácia/F1 (Teste) | Diagnóstico Clínico |
| :---: | :---: | :---: | :---: | :--- |
| 1 | K = 3 | *0.XX* | *0.XX* | Alta variância (Overfitting leve) |
| 2 | K = 5 | *0.XX* | *0.XX* | Ponto Ideal de Generalização |
| 3 | K = 7 | *0.XX* | *0.XX* | Estabilização de Desempenho |
| 4 | K = 9 | *0.XX* | *0.XX* | Viés Crescente (Underfitting leve)|

### Otimização da Árvore de Decisão (Hiperparâmetro `max_depth`)
| Cenário | Valor de `max_depth` | Acurácia/F1 (Treino) | Acurácia/F1 (Teste) | Diagnóstico Clínico |
| :---: | :---: | :---: | :---: | :--- |
| 1 | `depth = 3` | *0.XX* | *0.XX* | Alta restrição (Underfitting) |
| 2 | `depth = 5` | *0.XX* | *0.XX* | Ponto Ótimo de Equilíbrio |
| 3 | `depth = 7` | *0.XX* | *0.XX* | Início de memorização de ruído |
| 4 | `depth = None` | *1.00* | *0.XX* | Overfitting Crítico (Decorou o treino) |

---

## 7. Veredito de Negócios (Recomendação Executiva)

* **Análise de Impacto Financeiro:** O modelo de **Árvore de Decisão** obteve o melhor desempenho corporativo. Embora a acurácia global possa parecer similar à do concorrente, ele apresentou uma métrica de **Recall (Sensibilidade)** drasticamente superior para a Classe 1 (Inadimplentes). No contexto bancário, capturar o caloteiro (reduzir Falsos Negativos) poupa o patrimônio líquido direto do banco, compensando amplamente o pequeno aumento de Falsos Positivos.
* **Veredito Final:** Recomenda-se a homologação e implantação em produção do modelo de **Árvore de Decisão** configurado com o parâmetro **`max_depth=8`**, alinhando de forma perfeita a acurácia matemática à saúde financeira do negócio.
---

## Como Executar este Projeto

1. Clone o repositório:
   ```bash
   git clone https://github.com/xandevirgilio/projeto-avaliativo-ml-sctec-2026-