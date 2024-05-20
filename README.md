# Análise de Palavras no documento "Agenda 2030"

Esta seção contém um script para análise de palavras em arquivos PDF, incluindo a contagem das palavras mais comuns e a análise de co-ocorrências.

## Requisitos

- Python 3.x
- Bibliotecas: `PyPDF2`, `nltk`, `collections`, `google.colab`

## Instalação

Certifique-se de ter as bibliotecas necessárias instaladas. Você pode instalá-las usando `pip`:

## Uso
- Upload do Arquivo PDF: Faça o upload do arquivo PDF que deseja analisar.
- Extração de Texto: O texto será extraído do PDF.
- Contagem das Palavras Mais Comuns: As 10 palavras mais frequentes serão identificadas.
- Análise de Co-ocorrências: Para cada uma das 10 palavras mais frequentes, serão identificadas as 5 palavras que mais frequentemente ocorrem próximas a elas.
- Exibição dos Resultados: Os resultados serão exibidos no console.

```bash
!pip install PyPDF2 nltk

import PyPDF2
import nltk
from nltk.corpus import stopwords
from collections import Counter
from google.colab import files

# Baixe os recursos do NLTK, se ainda não os tiver
nltk.download('stopwords')
nltk.download('punkt')

def extract_text_from_pdf(pdf_path):
    with open(pdf_path, 'rb') as file:
        pdf_reader = PyPDF2.PdfReader(file)
        text = ""
        for page in pdf_reader.pages:
            text += page.extract_text()
    return text

def count_words(text):
    stop_words = set(stopwords.words('portuguese')) | {"sobre", "todos", "todas"}
    words = nltk.word_tokenize(text.lower())
    filtered_words = [word for word in words if word.isalnum() and word not in stop_words]
    word_counts = Counter(filtered_words)
    return word_counts.most_common(10)

def find_cooccurring_words(text, top_words):
    stop_words = set(stopwords.words('portuguese')) | {"sobre", "todos", "todas"}
    words = nltk.word_tokenize(text.lower())
    filtered_words = [word for word in words if word.isalnum() and word not in stop_words]
    
    cooccurrences = {word: Counter() for word in top_words}
    for i, word in enumerate(filtered_words):
        if word in top_words:
            context_window = filtered_words[max(0, i-5):min(len(filtered_words), i+6)]
            context_words = [w for w in context_window if w != word]
            cooccurrences[word].update(context_words)
    
    top_cooccurrences = {word: cooccurrences[word].most_common(5) for word in top_words}
    return top_cooccurrences

if __name__ == "__main__":
    print("Por favor, carregue o arquivo PDF.")
    uploaded = files.upload()
    pdf_path = next(iter(uploaded))  # Obtém o caminho do primeiro arquivo carregado

    if pdf_path:
        text = extract_text_from_pdf(pdf_path)
        most_common_words = count_words(text)
        top_10_words = [word for word, _ in most_common_words]
        
        top_cooccurrences = find_cooccurring_words(text, top_10_words)

        for word, count in most_common_words:
            print(f"Palavra: {word} - Ocorrências: {count}")
            print("Top 5 palavras que acompanham:")
            for co_word, co_count in top_cooccurrences[word]:
                print(f" - {co_word}: {co_count}")
    else:
        print("Nenhum arquivo carregado.")

```

# Explicação do Código
## Importações e Downloads
- PyPDF2: Biblioteca usada para ler e extrair texto de arquivos PDF.
- nltk: Natural Language Toolkit, uma biblioteca usada para processamento de linguagem natural.
- stopwords: Lista de palavras comuns (como "e", "o", "a") que geralmente são removidas na análise de texto.
- Counter: Classe do módulo collections que conta a frequência dos elementos em uma lista.
- files: Módulo do Google Colab para upload de arquivos.
  
Os comandos nltk.download('stopwords') e nltk.download('punkt') garantem que os recursos necessários do NLTK sejam baixados se ainda não estiverem disponíveis.

## Função "extract_text_from_pdf"
- Abertura do PDF: Abre o arquivo PDF no caminho especificado em modo de leitura binária.
- Leitura do PDF: Cria um objeto PdfReader para ler o PDF.
- Extração de Texto: Inicializa uma string vazia e concatena o texto extraído de cada página do PDF.
- Retorno do Texto: Retorna o texto completo extraído do PDF.
  
## Função "count_words"
- Stopwords: Define um conjunto de palavras a serem ignoradas, incluindo as stopwords em português e as palavras "sobre", "todos" e "todas".
- Tokenização: Divide o texto em palavras individuais, convertendo tudo para minúsculas.
- Filtragem de Palavras: Remove palavras que não são alfanuméricas e as que estão na lista de stopwords.
- Contagem de Palavras: Conta a frequência de cada palavra filtrada.
- Top 10 Palavras: Retorna as 10 palavras mais comuns e suas frequências.

## Função "find_cooccurring_words"
- Stopwords: Define o mesmo conjunto de stopwords que a função count_words.
- Tokenização e Filtragem: Realiza a tokenização e filtragem das palavras, como na função count_words.
- Inicialização de Coocorrências: Cria um dicionário para armazenar contadores de palavras co-ocorrentes para cada uma das palavras principais (top_words).
- Atualização de Coocorrências: Para cada palavra na lista filtrada, se a palavra estiver entre as top_words, um contexto de 5 palavras antes e depois é considerado. As palavras nesse contexto são contadas como co-ocorrências.
- Top 5 Coocorrências: Para cada palavra principal, as 5 palavras que mais co-ocorrem são retornadas.

## Execução Principal
- Upload do Arquivo: Solicita ao usuário que carregue um arquivo PDF.
- Extração de Texto: Extrai o texto do PDF carregado.
- Contagem das Palavras Mais Comuns: Obtém as 10 palavras mais frequentes no texto.
- Co-ocorrências das Palavras: Encontra as 5 palavras que mais frequentemente ocorrem próximas de cada uma das top 10 palavras.
- Impressão dos Resultados: Exibe as top 10 palavras e as 5 palavras que mais frequentemente co-ocorrem com cada uma delas.

## Diretrizes para Escolha das Palavras
- Stopwords: Palavras comuns que não agregam valor na análise de conteúdo são removidas. Adicionalmente, palavras específicas como "sobre", "todos" e "todas" foram excluídas devido à sua alta frequência e baixa relevância semântica.
- Tokenização: Separação do texto em palavras individuais para análise.
- Filtragem: Remoção de palavras não alfanuméricas e stopwords para focar em palavras significativas.
- Co-ocorrências: Análise do contexto das palavras principais para identificar associações frequentes, fornecendo uma visão sobre como as palavras são usadas em conjunto no texto.

## Resultados
- Palavra: desenvolvimento - Ocorrências: 273
Top 5 palavras que acompanham:
 - países: 146
 - sustentável: 117
 - pequenos: 42
 - desenvolvidos: 39
 - estados: 37
   
Palavra: países - Ocorrências: 185
Top 5 palavras que acompanham:
 - desenvolvimento: 146
 - desenvolvidos: 93
 - menos: 75
 - pequenos: 33
 - estados: 32
   
Palavra: sustentável - Ocorrências: 138
Top 5 palavras que acompanham:
 - desenvolvimento: 117
 - promover: 21
 - objetivos: 20
 - países: 18
 - recursos: 17
   
Palavra: agenda - Ocorrências: 70
Top 5 palavras que acompanham:
 - desenvolvimento: 23
 - ação: 18
 - implementação: 17
 - nova: 16
 - objetivos: 15
   
Palavra: incluindo - Ocorrências: 69
Top 5 palavras que acompanham:
 - desenvolvimento: 21
 - países: 18
 - sustentável: 7
 - pobreza: 6
 - formas: 6
   
Palavra: 2030 - Ocorrências: 64
Top 5 palavras que acompanham:
 - acesso: 13
 - reduzir: 11
 - garantir: 11
 - sustentável: 10
 - desenvolvimento: 9
   
Palavra: global - Ocorrências: 60
Top 5 palavras que acompanham:
 - desenvolvimento: 16
 - sustentável: 14
 - parceria: 13
 - nível: 11
 - implementação: 11
   
Palavra: desenvolvidos - Ocorrências: 55
Top 5 palavras que acompanham:
 - países: 93
 - menos: 43
 - desenvolvimento: 39
 - estados: 21
 - pequenos: 19
   
Palavra: menos - Ocorrências: 53
Top 5 palavras que acompanham:
 - países: 75
 - desenvolvidos: 43
 - desenvolvimento: 36
 - pequenos: 20
 - estados: 12
Palavra: nacionais - Ocorrências: 52
Top 5 palavras que acompanham:
 - desenvolvimento: 20
 - políticas: 14
 - prioridades: 10
 - níveis: 9
 - acordo: 9


# Geração do gráfico

Esta seção contém um script para a criação de um gráfico de barras para visualizar as palavras mais comuns e suas co-ocorrências.

## Requisitos

- Python 3.x
- Bibliotecas: `matplotlib`, `seaborn`, `pandas`

## Instalação

Certifique-se de ter as bibliotecas necessárias instaladas. Você pode instalá-las usando `pip`:

## Uso
- Criação de Dados: Inicialize os dados das palavras e suas frequências.
- Criação do DataFrame: Converta os dados em um DataFrame do pandas.
- Configuração do Gráfico: Configure o gráfico de barras usando seaborn e matplotlib.
- Ajustes de Estilo e Títulos: Personalize o estilo e os títulos do gráfico.
- Exibição do Gráfico: Exiba o gráfico final.

```bash
!pip install matplotlib seaborn pandas

import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd

# Dados fornecidos
data = {
    'Palavra': ['desenvolvimento', 'desenvolvimento', 'desenvolvimento', 'desenvolvimento', 'desenvolvimento',
                'países', 'países', 'países', 'países', 'países',
                'sustentável', 'sustentável', 'sustentável', 'sustentável', 'sustentável',
                'agenda', 'agenda', 'agenda', 'agenda', 'agenda',
                'incluindo', 'incluindo', 'incluindo', 'incluindo', 'incluindo',
                '2030', '2030', '2030', '2030', '2030',
                'global', 'global', 'global', 'global', 'global',
                'desenvolvidos', 'desenvolvidos', 'desenvolvidos', 'desenvolvidos', 'desenvolvidos',
                'menos', 'menos', 'menos', 'menos', 'menos',
                'nacionais', 'nacionais', 'nacionais', 'nacionais', 'nacionais'],
    'Acompanha': ['países', 'sustentável', 'pequenos', 'desenvolvidos', 'estados',
                  'desenvolvimento', 'desenvolvidos', 'menos', 'pequenos', 'estados',
                  'desenvolvimento', 'promover', 'objetivos', 'países', 'recursos',
                  'desenvolvimento', 'ação', 'implementação', 'nova', 'objetivos',
                  'desenvolvimento', 'países', 'sustentável', 'pobreza', 'formas',
                  'acesso', 'reduzir', 'garantir', 'sustentável', 'desenvolvimento',
                  'desenvolvimento', 'sustentável', 'parceria', 'nível', 'implementação',
                  'países', 'menos', 'desenvolvimento', 'estados', 'pequenos',
                  'países', 'desenvolvidos', 'desenvolvimento', 'pequenos', 'estados',
                  'desenvolvimento', 'políticas', 'prioridades', 'níveis', 'acordo'],
    'Frequência': [146, 117, 42, 39, 37,
                   146, 93, 75, 33, 32,
                   117, 21, 20, 18, 17,
                   23, 18, 17, 16, 15,
                   21, 18, 7, 6, 6,
                   13, 11, 11, 10, 9,
                   16, 14, 13, 11, 11,
                   93, 43, 39, 21, 19,
                   75, 43, 36, 20, 12,
                   20, 14, 10, 9, 9]
}

# Criação do DataFrame
df = pd.DataFrame(data)

# Configuração do gráfico
plt.figure(figsize=(10, 15))
sns.set(style="whitegrid")
sns.barplot(x="Frequência", y="Acompanha", hue="Palavra", data=df, palette="tab10")

# Ajustes de estilo e títulos
plt.title("Frequência das Palavras que Acompanham as Top 10 Palavras Mais Comuns")
plt.xlabel("Frequência")
plt.ylabel("Palavras que Acompanham")
plt.legend(title="Top 10 Palavras", bbox_to_anchor=(1.05, 1), loc='upper left')

# Exibição do gráfico
plt.tight_layout()
plt.show()
```

# Explicação do Código
## Importações
- matplotlib.pyplot: Biblioteca para criação de gráficos e visualizações.
- seaborn: Biblioteca baseada em matplotlib para criar gráficos estatísticos atraentes e informativos.
- pandas: Biblioteca para manipulação e análise de dados, especialmente útil para estruturas de dados como DataFrames.

## Dados Fornecidos
Os dados fornecidos são um dicionário com três listas:

- 'Palavra': Lista das palavras principais que aparecem nas análises.
- 'Acompanha': Lista das palavras que acompanham as palavras principais.
- 'Frequência': Frequência com que as palavras de 'Acompanha' aparecem junto com as palavras de 'Palavra'.

## Criação do DataFrame
Os dados são convertidos em um DataFrame do pandas, que é uma estrutura de dados bidimensional com rótulos de eixo (linhas e colunas).
```bash
# Criação do DataFrame
df = pd.DataFrame(data)
```

## Configuração do Gráfico
- Figura: Define o tamanho da figura do gráfico.
- Estilo: Define o estilo do gráfico como 'whitegrid' para um fundo de grade branco.
- Gráfico de Barras: Cria um gráfico de barras com a frequência das palavras que acompanham as palavras principais, usando a biblioteca seaborn.
```bash
# Configuração do gráfico
plt.figure(figsize=(10, 15))
sns.set(style="whitegrid")
sns.barplot(x="Frequência", y="Acompanha", hue="Palavra", data=df, palette="tab10")
```

## Ajustes de Estilo e Títulos
- Título: Define o título do gráfico.
- Rótulos dos Eixos: Define os rótulos dos eixos X e Y.
- Legenda: Configura a posição e o título da legenda.
```bash
# Ajustes de estilo e títulos
plt.title("Frequência das Palavras que Acompanham as Top 10 Palavras Mais Comuns")
plt.xlabel("Frequência")
plt.ylabel("Palavras que Acompanham")
plt.legend(title="Top 10 Palavras", bbox_to_anchor=(1.05, 1), loc='upper left')
```

## Exibição do Gráfico
O método plt.tight_layout() ajusta automaticamente os parâmetros de subplots para que o subplot se ajuste na figura da área de exibição. plt.show() exibe o gráfico.
```bash
# Exibição do gráfico
plt.tight_layout()
plt.show()
```


## Resultado

![Gráfico de Frequência das Palavras](https://github.com/BrunoChiconato/TCC/blob/main/grafico-tcc-emanuelle-flemming.png)




