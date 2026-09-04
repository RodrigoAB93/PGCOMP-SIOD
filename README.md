# 🎓 Sistemas Inteligentes Orientados a Dados (PGCOMP / UFBA - 2026.2)

Este repositório é de caráter **estritamente acadêmico** e destina-se à entrega das atividades práticas da disciplina **Sistemas Inteligentes Orientados a Dados** do Programa de Pós-Graduação em Ciência da Computação (PGCOMP) da Universidade Federal da Bahia (UFBA).

---

## 📝 Estudo de Caso: Classificação de Transações Financeiras Mistas  

O projeto aborda cientificamente a sobreposição de finanças pessoais e profissionais de trabalhadores autônomos. A proposta investiga como técnicas de Inteligência Artificial e Processamento de Linguagem Natural (NLP) podem automatizar a separação dessas contas de forma ética e fiduciária, inspirada na literatura de engenharia de software e gestão de incertezas.

*   **Problema:** Classificar descrições textuais curtas, cruas, poluídas e truncadas de extratos bancários (*bank feeds*) em perfis de gastos: **Pessoal**, **Profissional** ou **Misto**.

---

## 📂 Entregas (Atividade 01)

Seguindo rigorosamente o roteiro e os critérios de avaliação estabelecidos para a **Atividade 01 - Caracterização e avaliação inicial dos dados do projeto**:

1.  **Fonte de Dados:** [`transacoes_financeiras_anonimizadas.csv`](transacoes_financeiras_anonimizadas.csv)  
    Amostra com **5.100 transações financeiras reais cruas** anonimizadas obtidas via Open Finance, contendo as anomalias, ruídos tipográficos de faturas de adquirentes e erros sistemáticos exigidos no diagnóstico de qualidade.
2.  **Relatório Técnico de Dados:** [`atividade-01-dados-v2.md`](docs/atividade-01-dados-v2.md)   
    Contém o enquadramento de negócio fiduciário, caracterização detalhada da fonte, mapeamento de dores, dicionário de dados focado na decisão e a análise exploratória formal.
3.  **Script de Análise Exploratória Standalone:** [`01_exploracao_dados.py`](01_exploracao_dados.py)  
    Código em Python capaz de carregar a base de dados, executar testes de qualidade de forma reprodutível, aplicar um pipeline de limpeza básica via Regex e plotar estatísticas de distribuição.

---

## ⚙️ Roteiro de Execução e Reprodutibilidade

Para executar a análise exploratória básica e verificar de forma automatizada o volume e a qualidade das transações, execute os Notebooks em alguma plataforma (Jupyter / Google Colab).



## 📊 Caracterização Estatística Básica do Dataset

Ao rodar o script de caracterização automatizado, a base apresentará as seguintes métricas descritivas e qualitativas:

*   **Número de registros (observações):** 5.100 transações originais.
*   **Número de atributos (campos):** 6 atributos estruturados (`id_transacao`, `data_transacao`, `descricao_original`, `valor`, `categoria_sugerida`, `tipo_conta_estimado`).
*   **Percentual de valores ausentes:**
    *   `data_transacao`: 2,18% (111 nulos) - falha de sincronização.
    *   `categoria_sugerida`: 7,88% (402 nulos) - limitação da heurística legada do banco.
*   **Quantidade de duplicidades:** 100 linhas e chaves de ID perfeitamente duplicadas (decorrentes de *retries* no envio da API).
*   **Distribuição da Variável-Alvo (`tipo_conta_estimado`):**
    *   **Pessoal:** 3.485 transações 
    *   **Misto:** 770 transações 
    *   **Profissional:** 745 transações 
*   **Intervalo temporal coberto pelos dados:** Janeiro de 2026 a Setembro de 2026 (9 meses de cobertura do ano fiscal corrente).
*   **Anomalias e Outliers Numéricos:** 12 transações com outliers bizarros causados por erros internos do sistema legado bancário (valores numéricos fixados em `R$ -99.999,00` e `R$ 999.999,00`).

---

## 📝  Síntese Técnica

1.  **Os dados necessários para o projeto estão efetivamente disponíveis?**  
    Sim. É uma amostra estruturada em CSV com 5.100 registros reais anonimizados de transações financeiras via Open Finance. Essa base provê uma representação robusta e diversificada para treinar os algoritmos iniciais de processamento de texto (NLP).
2.  **Qual é o principal problema identificado na fonte de dados?**  
    O principal problema de qualidade reside nos **outliers numéricos de sistema** (os registros contendo `R$ -99.999,00` e `R$ 999.999,00` que violam o sentido financeiro) e nas **inconsistências de strings de data** (ex: `'Jun 25, 2026'`, `'13/04/2026'`). Estes formatos exigem regras estritas de normalização e filtros antes de alimentar algoritmos de aprendizado supervisionado.
3.  **Há informação necessária ao projeto que não está disponível?**  
    Sim. Para um enquadramento contábil perfeito de negócios, seria necessário dispor do **detalhamento físico da Nota Fiscal (NCM ou cupons de compras)**. Como adquirentes e bancos provêm apenas a string curta e crua do estabelecimento no extrato bancário, o modelo inteligente precisará residir estritamente na capacidade de inferência linguística a partir do texto truncado.
4.  **A caracterização dos dados exige alteração no problema, na hipótese ou no escopo definidos na Aula 01?**  
    Não há necessidade de alterar o escopo ou problema principal. Entretanto, exige um **refinamento do pipeline de pré-processamento de NLP**. Descrições vazias ou transações com categorias nulas precisarão passar por um processo de vetorização semântica (embeddings) ou fallback geográfico para garantir previsões confiáveis.
5.  **Qual é, neste momento, o principal risco relacionado aos dados?**  
    O principal risco reside na **deriva de conceito linguístico (*concept drift*)**. Os formatos de faturas de adquirentes e bandeiras mudam dinamicamente ao longo do tempo conforme novas tecnologias de transação (como Pix ou carteiras de micro-pagamento fechadas) entram no mercado brasileiro. Um modelo de classificação de texto treinado com dados de 2026 apresentará inevitável perda de acurácia em anos subsequentes se as adquirentes alterarem as strings padrão enviadas nos extratos, necessitando de pipelines periódicos de recalibração algorítmica.

---
*Declaração de Integridade: Os dados utilizados nesta atividade são simulados de forma realista e anonimizados, não contendo dados pessoais, sigilosos ou sensíveis, em conformidade com as diretrizes de privacidade da UFBA.*
