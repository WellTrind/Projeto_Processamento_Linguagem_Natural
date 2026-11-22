# Análise de Reviews da Steam com Processamento de Linguagem Natural

Projeto acadêmico de **Processamento de Linguagem Natural (PLN)** voltado à análise e normalização de avaliações de jogos publicadas na plataforma Steam. A proposta é utilizar **Python**, **LangChain** e um modelo de linguagem **Gemini** para transformar comentários escritos livremente em textos mais padronizados, preservando o sentido original, e apoiar análises exploratórias.

> **Nota de escopo:** esta documentação foi elaborada a partir da descrição do projeto e de trechos de código compartilhados pelo autor. Não foi possível inspecionar a branch `feature` do repositório; portanto, nomes de notebooks, caminhos de arquivos e versões específicas devem ser conferidos no código antes de considerar a instalação validada.

## Objetivos

- Coletar ou carregar avaliações de jogos da Steam.
- Organizar os registros em um DataFrame e tratar comentários ausentes ou vazios.
- Normalizar textos com auxílio de um LLM, preservando seu significado.
- Comparar avaliações originais e normalizadas.
- Explorar os dados e produzir visualizações para apoiar a interpretação dos resultados.

## Funcionamento

```text
Reviews da Steam (API ou dados exportados)
                  |
                  v
     Carregamento com pandas
                  |
                  v
  Limpeza e seleção dos comentários
                  |
                  v
  LangChain + modelo Gemini (LLM)
                  |
                  v
   Normalização e padronização
                  |
                  v
 DataFrame com texto original e normalizado
                  |
                  v
       Análises e gráficos
```

### 1. Coleta e preparação

As avaliações podem ser obtidas da API da Steam ou carregadas de um arquivo de dados previamente exportado. Entre os campos utilizados no projeto estão `recommendationid`, `review`, `language`, `voted_up`, `timestamp_created` e `votes_up`. O conjunto de dados é organizado com **pandas**, removendo registros sem texto útil antes de enviar os comentários ao modelo.

### 2. Normalização com LLM

O projeto utiliza **LangChain** para integrar o fluxo Python a um modelo **Gemini**. Para cada comentário, uma instrução solicita a normalização do texto, preservando o significado original. A saída pode ser armazenada em uma coluna como `comentario_normalizado`, mantendo o texto original para comparação.

A normalização busca reduzir variações de escrita e facilitar a análise; **não deve alterar o posicionamento positivo ou negativo da avaliação**. Como modelos generativos podem produzir respostas inesperadas, recomenda-se revisar amostras e validar a saída antes de realizar análises quantitativas.

### 3. Análise exploratória

Com os comentários organizados, o notebook pode gerar tabelas e gráficos para investigar a distribuição das avaliações e observar os resultados do tratamento textual. As visualizações efetivamente disponíveis dependem da versão do notebook.

## Tecnologias

- **Python** — linguagem principal.
- **pandas** — manipulação e limpeza dos dados.
- **LangChain** — integração e orquestração do fluxo com LLM.
- **Google Gemini** — modelo de linguagem para normalização.
- **PyPDF / PyPDFLoader** — leitura de material de referência em PDF, quando utilizado no notebook.
- **Matplotlib** — visualização de dados, quando utilizada.
- **Jupyter Notebook / Google Colab** — ambiente de execução interativa.

## Instalação local

### Pré-requisitos

- Python 3.10 ou superior (a compatibilidade exata depende das versões instaladas do LangChain).
- Git.
- Acesso à internet para instalar pacotes, consultar a Steam ou chamar a API Gemini.
- Uma chave de API Gemini válida para as etapas que usam LLM.

### 1. Clonar a branch do projeto

```bash
git clone --branch feature https://github.com/WellTrind/Projeto_Processamento_Linguagem_Natural.git
cd Projeto_Processamento_Linguagem_Natural
```

### 2. Criar e ativar um ambiente virtual

**Windows (PowerShell):**

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

**Linux / macOS:**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Instalar as dependências

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

> O arquivo `requirements.txt` fornecido é uma lista inicial baseada nas bibliotecas conhecidas do projeto. Como o código da branch não pôde ser inspecionado, pode ser necessário ajustar dependências ou versões.

### 4. Configurar a API Gemini

Crie uma chave de API no [Google AI Studio](https://aistudio.google.com/apikey). No terminal, configure-a como variável de ambiente antes de executar o notebook:

**Windows (PowerShell):**

```powershell
$env:GOOGLE_API_KEY="SUA_CHAVE_AQUI"
```

**Linux / macOS:**

```bash
export GOOGLE_API_KEY="SUA_CHAVE_AQUI"
```

No Python, é possível recuperá-la com:

```python
import os

api_key = os.environ.get("GOOGLE_API_KEY")
if not api_key:
    raise RuntimeError("Configure GOOGLE_API_KEY antes de executar o modelo.")
```

Se o notebook original utiliza `google.colab.userdata`, configure o segredo correspondente no painel **Secrets** do Colab e mantenha o nome da variável consistente com o código. Nunca inclua chaves reais no notebook ou no repositório.

### 5. Executar o notebook

```bash
jupyter lab
```

Abra o arquivo `.ipynb` do projeto e execute as células na sequência. Antes, confira os caminhos de entrada (CSV, PDF e outros dados), o nome do modelo Gemini e as instruções de autenticação usadas no código.

## Execução no Google Colab

1. Abra o notebook do repositório no Google Colab.
2. Instale as dependências, se necessário, usando `!pip install -r requirements.txt` após disponibilizar o arquivo no ambiente.
3. Configure a chave Gemini em **Secrets**.
4. Faça upload ou monte o Google Drive com os dados necessários.
5. Ajuste os caminhos dos arquivos e execute as células na ordem.

## Estrutura dos dados

Um conjunto de avaliações da Steam pode incluir os campos abaixo:

| Campo | Descrição |
| --- | --- |
| `recommendationid` | Identificador da avaliação. |
| `review` | Comentário textual original. |
| `language` | Idioma informado para a avaliação. |
| `voted_up` | Indicador de recomendação positiva ou negativa. |
| `timestamp_created` | Data/hora de criação, em timestamp. |
| `votes_up` | Quantidade de votos úteis recebidos. |
| `comentario_normalizado` | Texto processado pelo LLM, quando gerado pelo pipeline. |

Os nomes e a disponibilidade das colunas podem variar conforme a etapa de transformação dos dados.

## Cuidados e limitações

- A chamada ao Gemini pode ter **limites de uso e custos**, conforme a conta e o modelo selecionado.
- Comentários em grande volume podem demandar tempo significativo de processamento.
- Não envie credenciais, dados pessoais ou informações sensíveis desnecessariamente ao modelo.
- A normalização por LLM não é determinística; verifique amostras e preserve os textos originais.
- Dependências da API Gemini e do LangChain podem sofrer mudanças; ajuste imports e versões quando necessário.
- Os caminhos usados no Colab, como `/content/...`, precisam ser adaptados para execução local.

## Repositório

[Projeto_Processamento_Linguagem_Natural — branch feature](https://github.com/WellTrind/Projeto_Processamento_Linguagem_Natural/tree/feature)

## Contexto acadêmico

Projeto desenvolvido para aplicação prática de conceitos de **Processamento de Linguagem Natural**, integrando tratamento de dados textuais, modelos de linguagem e análise exploratória de avaliações de jogos.
