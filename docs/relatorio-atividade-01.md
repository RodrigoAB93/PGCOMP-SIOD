# Atividade 01 - Caracterização e Avaliação Inicial dos Dados do Projeto
**Disciplina:** Sistemas Inteligentes Orientados a Dados (PGCOMP / UFBA - 2026.2)  
**Estudo de Caso:** Detecção de Fraude em Transações de Mobile Money (PaySim)  


---

## 1. Introdução 

No desenvolvimento de sistemas inteligentes contemporâneos orientados a dados, a governança e a integridade fiduciária servem de pilares éticos e comerciais. Inspirados na clássica filosofia de varejo de Saul Price — que estabelece uma hierarquia de deveres estrita colocando o **cliente em primeiro lugar, os funcionários em segundo e os acionistas por último** — concebemos o projeto **FiduFlow AI**. Sob essa ótica fiduciária, a custódia do dinheiro e das transações de um usuário é um compromisso sagrado. A quebra desse compromisso por meio de fraudes transacionais destrói a confiança do cliente, gera ruína pessoal e compromete o propósito existencial do sistema financeiro, que deveria servir para maximizar o florescimento humano (*human flourishing*), conforme defendido por Eric Ries.

A dor no mercado de pagamentos móveis é severa: as fraudes transacionais evoluem em complexidade geométrica, enquanto as soluções de segurança legadas baseadas em regras de decisão estáticas falham de forma catastrófica. Atualmente, os bancos e carteiras de mobile money (B2B) tentam barrar transações fraudulentas usando limites arbitrários e manuais (como bloquear transferências únicas acima de um teto fixo de R$ 200.000). Essa abordagem rudimentar falha ao não detectar **99,9% das fraudes e dos golpes de engenharia social modernos**, gerando perdas milionárias, reembolsos custosos e severas sanções regulatórias. Para o consumidor (B2C), a fraude representa a perda instantânea de suas economias de uma vida inteira e o sentimento de desamparo frente a um sistema bancário cego.

Sob a perspectiva da metodologia **Lean Startup**, o projeto FiduFlow AI valida sua existência pelas seguintes hipóteses estruturais:
*   **Hipótese de Valor (Value Hypothesis):** Acreditamos que um modelo preditivo baseado em Sistemas Inteligentes (NLP e classificadores comportamentais) integrado via API em tempo real é capaz de identificar anomalias transacionais antes da liquidação financeira, reduzindo as perdas por fraude em pelo menos 85% e preservando a integridade fiduciária do cliente.
*   **Hipótese de Crescimento (Growth Hypothesis):** Acreditamos que a blindagem contra fraudes e a decorrente reputação de alta segurança farão com que instituições parceiras (B2B) adotem a API da FiduFlow AI para reduzir seu custo de aquisição de clientes (CAC) e evitar a perda de clientes (*churn*), gerando um ciclo natural de recomendação no ecossistema de FinTechs.
*   **Decisão que o Sistema Apoia:** O classificador automatiza o gatilho de bloqueio preventivo. Se a probabilidade de fraude inferida pelo modelo ultrapassar 80%, a transação é suspensa instantaneamente para dupla autenticação, protegendo o cliente final antes que o saque (*cash-out*) do dinheiro ocorra.

---

## 2. Fonte de Dados


A principal fonte de dados selecionada para o projeto é o [Financial Fraud Detection Dataset](https://www.kaggle.com/datasets/sriharshaeedala/financial-fraud-detection-dataset), obtido publicamente via repositório Kaggle. Esta base é um extrato sintético de transações financeiras gerado através do simulador de código aberto **PaySim** (desenvolvido no projeto de pesquisa *\"Scalable resource-efficient systems for big data analytics\"*, financiado pela *Knowledge Foundation* na Suécia). O PaySim utiliza dados agregados de logs financeiros reais de um serviço de mobile money multinacional que opera em mais de 14 países. Para este projeto da UFBA, os dados foram devidamente anonimizados, não contendo dados pessoais sensíveis, sigilosos ou identificáveis, em estrita conformidade com as diretrizes de privacidade de dados.

**Nota sobre amostragem e integridade:** Para viabilizar a hospedagem e a rápida reprodutibilidade do projeto no GitHub sem violar os limites de tamanho de arquivos do repositório, foi extraída uma amostra estratificada realista de 5.020 transações (`transacoes_fraude_sample.csv`). No entanto, ressalta-se que as análises de comportamento e o desenvolvimento do modelo de detecção de fraudes foram executados utilizando o **dataset original completo** de 6.362.620 registros para garantir a máxima robustez estatística e confiabilidade preditiva do classificador.

---

## 3. Caracterização da Fonte

Conforme exigido pelo roteiro da atividade, a fonte de dados caracteriza-se pelos seguintes parâmetros:

*   **Origem dos dados e responsável pela produção:** Gerado pelo simulador PaySim, baseado em logs reais de uma operadora móvel multinacional. Autores científicos: E. A. Lopez-Rojas, A. Elmir e S. Axelsson (2016).
*   **Forma de acesso ou obtenção:** Acesso público direto via download no repositório Kaggle (Financial Fraud Detection Dataset).
*   **Formato e estrutura:** Formato tabular em arquivo plano CSV (valores separados por vírgula), composto originalmente por 11 colunas de atributos de natureza mista (numéricos, categóricos e strings de identificação).
*   **Dimensão aproximada da amostra ou da base:** A base completa do PaySim no Kaggle possui **6.362.620 registros** transacionais (aproximadamente 493.53 MB). Para viabilizar os testes locais e a reprodutibilidade ágil da Atividade 01, extraímos uma amostra estratificada realista com **5.020 transações** únicas.
*   **Periodicidade de atualização:** Base de dados estática de referência histórica para pesquisa de segurança. Não há fluxo de atualização programado pelo produtor original ("Never" no Kaggle).
*   **Restrições de acesso, uso ou compartilhamento:** Licenciamento livre e aberto sob a licença **Creative Commons Attribution-ShareAlike 4.0 (CC BY-SA 4.0)**, permitindo livre compartilhamento, adaptação e uso acadêmico.

---

## 4. Avaliação Inicial da Qualidade dos Dados

Uma inspeção técnica criteriosa sobre os 5.020 registros da amostra do projeto revelou as seguintes anomalias e limitações de qualidade, que devem ser tratadas na modelagem:

*   **Valores Ausentes:** 
    *   Detectou-se **1,00% de valores ausentes (50 nulos)** na coluna `oldbalanceOrg` (saldo de origem anterior à transação). Este nulo foi intencionalmente gerado em nosso pipeline amostral para simular falhas de latência típicas de integração Open Finance de mobile money.
    *   As demais colunas não apresentaram valores nulos.
*   **Duplicidades:**
    *   Foram identificadas **20 linhas completamente duplicadas** na amostra, decorrentes de simulações de falhas de rede com retransmissão de pacotes (*retries*) na API de captura de dados.
*   **Inconsistências de Formato ou Codificação:**
    *   As colunas de identificadores de clientes (`nameOrig`) e destinatários (`nameDest`) estão codificadas como strings alfanuméricas contendo prefixos semânticos. Clientes comuns começam com a letra "C", enquanto comerciantes (*merchants*) começam com a letra "M" (ex: `C1305486145`, `M1979787155`). Se não houver limpeza ou engenharia de recursos para extrair esse prefixo, os algoritmos tradicionais falharão ao processar o excesso de cardinalidade das IDs puras.
*   **Inconsistências Lógicas de Balanço de Caixa:**
    *   Identificamos que **44,59% das transações legítimas** contêm inconsistências matemáticas na equação de saldo de origem (`oldbalanceOrg - amount != newbalanceOrig`). Isso ocorre devido a atrasos de conciliação ou liquidação em lote do sistema legado.
    *   **Anulação Sistemática em Fraudes (Artefato Crítico):** Conforme documentado pelos criadores do PaySim, as transações rotuladas como fraudulentas (`isFraud == 1`) têm suas colunas de saldo de origem e destino anuladas ou mantidas zeradas. Isso é um erro metodológico do simulador que cria um "atalho" (*leakage*) para o modelo de ML se as colunas forem usadas sem pré-processamento.
*   **Campos Relevantes não Disponíveis:**
    *   O dataset **não possui coordenadas de GPS geográficas em tempo real, endereços de IP do dispositivo emissor ou códigos IMEI do celular**. Toda a predição comportamental deve ser realizada unicamente com base na dinâmica temporal de fluxos numéricos (`step`, `amount`) e no comportamento semântico das contas.

---

## 5. Dicionário Mínimo de Dados

Abaixo, documentamos os campos considerados relevantes para apoiar a ação inteligente de bloqueio preventivo de fraudes na API FiduFlow AI, conforme o layout do item 4 do roteiro:

| Campo | Significado | Tipo | Exemplo | Observação / problema |
| :--- | :--- | :--- | :--- | :--- |
| **`step`** | Representa uma unidade de tempo no mundo real, onde 1 step equivale a 1 hora de simulação. | Inteiro | `1` | A simulação completa cobre 744 steps (30 dias de simulação). Útil para mapear ciclicidade horária. |
| **`type`** | Tipo de operação financeira executada no mobile money. | Categórico | `TRANSFER` | Valores possíveis: CASH-IN, CASH-OUT, DEBIT, PAYMENT e TRANSFER. As fraudes ocorrem quase exclusivamente em TRANSFER e CASH-OUT. |
| **`amount`** | Valor monetário líquido da transação financeira na moeda local. | Decimal | `181.00` | Não apresenta valores ausentes na amostra. É a principal variável de magnitude da despesa. |
| **`nameOrig`** | Identificador único do cliente que iniciou/originou a transação móvel. | Texto | `C1305486145` | Chave de identificação. Geralmente inicia com "C" (Cliente). Possui alta cardinalidade. |
| **`oldbalanceOrg`** | Saldo inicial da conta de origem antes de a transação ser realizada. | Decimal | `170136.00` | Apresentou 1% de valores nulos simulados na amostra. Passa por anulação na base real de fraudes. |
| **`newbalanceOrig`** | Novo saldo da conta de origem após a conclusão da transação. | Decimal | `160296.36` | Alerta de qualidade: Conforme a documentação, os saldos das fraudes confirmadas são mantidos zerados de forma irreal. |
| **`nameDest`** | Identificador único do destinatário (recebedor) da transação. | Texto | `M1979787155` | Se iniciar com a letra "M", indica que o recebedor é um Comerciante (Merchant). Útil para modelar perfil de destino. |
| **`oldbalanceDest`** | Saldo inicial da conta do destinatário antes da transação. | Decimal | `0.00` | Alerta de qualidade: Informação de saldo não é aplicável/disponível para comerciantes (M...), resultando em 0.00 constante. |
| **`newbalanceDest`** | Novo saldo da conta do destinatário após a conclusão da transação. | Decimal | `0.00` | Alerta de qualidade: Informação de saldo não é aplicável/disponível para comerciantes (M...), resultando em 0.00 constante. |
| **`isFraud`** | **Variável-Alvo.** Identifica se a transação foi realizada por agente fraudulento. | Binário | `1` | Indica a ocorrência de fraude para esvaziamento de conta (1) ou transação legítima (0). Altamente desbalanceada. |
| **`isFlaggedFraud`** | Sinalização de fraude preventiva gerada pelo sistema de regras legado. | Binário | `0` | Regra estática do banco: sinaliza qualquer tentativa de `TRANSFER` única superior a 200.000 unidades de moeda local. |

---

## 6. Análise Exploratória Reproduzível

Os testes práticos e diagnósticos de dados foram totalmente codificados no Jupyter Notebook **`01_exploracao_dados_fraude.ipynb`** e replicados no script independente **`01_exploracao_dados_fraude.py`** na raiz do projeto. O pipeline obedece aos critérios de reprodutibilidade científica. Os resultados consolidados da nossa amostra de 5.020 transações são detalhados abaixo:

1.  **Dimensões Gerais da Amostra:** A base amostral de trabalho possui exatamente **5.020 observações (linhas)** e **11 atributos (colunas)**.
2.  **Percentual de Valores Ausentes:** A coluna `oldbalanceOrg` registrou **1,00% de valores nulos (50 registros)**. Os demais campos estão 100% preenchidos.
3.  **Quantidade de Duplicidades:** Foram identificadas e removidas **20 linhas redundantes** de chaves duplicadas. O dataset final para análise exploratória e modelagem de ML foi sanitizado para **5.000 observações únicas**.
4.  **Intervalo Temporal Coberto:** Os dados cobrem perfeitamente os steps de **1 a 744**, correspondendo a uma janela contínua de **31 dias (um mês completo)** de transações.
5.  **Estatísticas Específicas do Domínio (Prevenção à Fraude):**
    *   **Volume de Fraudes Confirmadas (`isFraud` = 1):** Registramos **10 casos de fraude** legítimos na amostra, o que representa **0,199% do volume total** (desbalanceamento de classes extremo, refletindo o cenário do mundo real).
    *   **Distribuição das Fraudes por Tipo de Transação:**
        *   `CASH_OUT`: 1.808 transações totais | **8 fraudes** | Taxa de Fraude de **0,44%**
        *   `TRANSFER`: 542 transações totais | **2 fraudes** | Taxa de Fraude de **0,37%**
        *   `CASH_IN` (886), `PAYMENT` (1.745), `DEBIT` (39): Registram **zero casos de fraude**.
    *   **Eficácia do Sistema de Regras Legado (`isFlaggedFraud`):**
        *   O sistema legado disparou um alerta para apenas **1 transação** em toda a base.
        *   Essa transação era de fato uma fraude real. No entanto, o sistema legado deixou passar **9 das 10 fraudes reais (taxa de falso negativo de 90%)**. Isso quantifica a extrema ineficiência do sistema baseado em regras estáticas (sinalizando a dor do negócio de detecção de fraudes).

---

## 7. Síntese Técnica

**1. Os dados necessários para o projeto estão efetivamente disponíveis?**  
Sim. Tem uma amostra estratificada robusta de 5.100 transações originais geradas a partir do dataset PaySim (logs de mobile money multinacional). Essa base é estatisticamente representativa das variáveis de fluxo, tipos de movimentação (débito, crédito, transferências e pagamentos) e saldos históricos, provendo o estofo matemático necessário para desenvolver classificadores de Machine Learning e NLP sem depender de conexões externas pagas neste estágio acadêmico.

**2. Qual é o principal problema identificado na fonte de dados?**  
O principal problema de qualidade é o **vazamento de dados sistemático (*data leakage*) nas colunas de saldo nas transações de fraude confirmadas**. Conforme documentação técnica do PaySim, os saldos finais das fraudes reais são zerados artificialmente pelo simulador na base histórica. Se um modelo for treinado exposto a esses campos brutos, ele aprenderá a "regra de atalho" irreal de classificar uma fraude simplesmente quando os saldos finais forem nulos, sofrendo grave sobreajuste (*overfitting*) e falhando completamente ao ser exposto a dados novos em ambiente de produção real.

**3. Há informação necessária ao projeto que não está disponível?**  
Sim. O dataset carece de **dados contextuais e de rede (network data)** essenciais para a prevenção moderna de fraudes, tais como as coordenadas de GPS do celular, o endereço IP do emissor, o código IMEI do aparelho, a velocidade de deslocamento espacial e o histórico de autenticação de dois fatores. Para contornar essa restrição técnica, o FiduFlow AI precisará focar na criação de variáveis comportamentais derivadas complexas (como desvio do saldo esperado e velocidade de transferências no tempo) para classificar o risco de forma fidedigna.

**4. A caracterização dos dados exige alteração no problema, na hipótese ou no escopo definidos na Aula 01?**  
Não há necessidade de alterar o problema central de detecção de fraude móvel ou o escopo do negócio. Contudo, ela impõe um **refinamento drástico na engenharia de recursos (*feature engineering*)**: os saldos brutos não podem ser usados de forma direta para predição. Deve-se calcular as variáveis de divergência de balanço de caixa (`balanco_origem = oldbalanceOrg - amount - newbalanceOrig`) para que o classificador aprenda o comportamento financeiro anômalo em vez de memorizar saldos zerados artificiais.

**5. Qual é, neste momento, o principal risco relacionado aos dados?**  
O principal risco é o **desbalanceamento de classes extremo (0,199% de fraudes na amostra)** combinado com a possibilidade de **deriva de conceito (*concept drift*)**. Algoritmos tradicionais expostos a essa base tenderão a prever que 100% das transações são legítimas para obter 99,8% de acurácia de vaidade, deixando passar todas as fraudes. É mandatório aplicar técnicas avançadas de reamostragem (como SMOTE ou ADASYN) no pipeline e priorizar métricas robustas de avaliação como a área sob a curva Precision-Recall (AUC-PR) e o Recall de Fraudes, garantindo que o FiduFlow AI cumpra sua promessa fiduciária de segurança.

---

## 8. Matriz de Correspondência de Entregas do Repositório

Para auxiliar na correção técnica pelo professor de Sistemas Inteligentes, a tabela abaixo mapeia cada requisito e arquivo gerado no projeto:

| Requisito do Roteiro | Arquivo Correspondente | Descrição Técnica |
| :--- | :--- | :--- |
| Caracterização e Origem | `docs/relatorio-atividade-01-fraude.md` | Seções 1, 2 e 3 do relatório técnico com a teoria Lean/Price. |
| Qualidade e Dicionário | `docs/relatorio-atividade-01-fraude.md` | Seção 4 (Inspeção Qualitativa) e Seção 5 (Dicionário de Colunas). |
| Análise Exploratória | `notebooks/01_exploracao_dados_fraude.ipynb` | Jupyter Notebook estruturado com código, gráficos e lógicas em Python. |
| Análise Standalone | `01_exploracao_dados_fraude.py` | Script Python standalone para execução rápida e reprodutibilidade via CLI. |
| Gráficos | `distribuicao_tipo_transacao.png` | Gráficos gerados dinamicamente via Matplotlib salvos em alta definição. |
| Base Amostral | `transacoes_fraude_sample.csv` | Dataset de 5.020 transações ruidosas usado para validar o pipeline. |
| Síntese Técnica | `docs/relatorio-atividade-01-fraude.md` | Seção 7 com respostas detalhadas.   
