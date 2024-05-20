# Análise de Palavras em PDF

Este repositório contém um script para análise de palavras em arquivos PDF, incluindo a contagem das palavras mais comuns e a análise de co-ocorrências.

## Requisitos

- Python 3.x
- Bibliotecas: `PyPDF2`, `nltk`, `collections`, `google.colab`

## Instalação

Certifique-se de ter as bibliotecas necessárias instaladas. Você pode instalá-las usando `pip`:

```bash
!pip install PyPDF2 nltk

## Uso
Passo a Passo
Upload do Arquivo PDF: Faça o upload do arquivo PDF que deseja analisar.
Extração de Texto: O texto será extraído do PDF.
Contagem das Palavras Mais Comuns: As 10 palavras mais frequentes serão identificadas.
Análise de Co-ocorrências: Para cada uma das 10 palavras mais frequentes, serão identificadas as 5 palavras que mais frequentemente ocorrem próximas a elas.
Exibição dos Resultados: Os resultados serão exibidos no console.
Script
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
Explicação do Código
Importações e Downloads
PyPDF2: Biblioteca usada para ler e extrair texto de arquivos PDF.
nltk: Natural Language Toolkit, uma biblioteca usada para processamento de linguagem natural.
stopwords: Lista de palavras comuns (como "e", "o", "a") que geralmente são removidas na análise de texto.
Counter: Classe do módulo collections que conta a frequência dos elementos em uma lista.
files: Módulo do Google Colab para upload de arquivos.
Os comandos nltk.download('stopwords') e nltk.download('punkt') garantem que os recursos necessários do NLTK sejam baixados se ainda não estiverem disponíveis.

Função extract_text_from_pdf
Abertura do PDF: Abre o arquivo PDF no caminho especificado em modo de leitura binária.
Leitura do PDF: Cria um objeto PdfReader para ler o PDF.
Extração de Texto: Inicializa uma string vazia e concatena o texto extraído de cada página do PDF.
Retorno do Texto: Retorna o texto completo extraído do PDF.
Função count_words
Stopwords: Define um conjunto de palavras a serem ignoradas, incluindo as stopwords em português e as palavras "sobre", "todos" e "todas".
Tokenização: Divide o texto em palavras individuais, convertendo tudo para minúsculas.
Filtragem de Palavras: Remove palavras que não são alfanuméricas e as que estão na lista de stopwords.
Contagem de Palavras: Conta a frequência de cada palavra filtrada.
Top 10 Palavras: Retorna as 10 palavras mais comuns e suas frequências.
Função find_cooccurring_words
Stopwords: Define o mesmo conjunto de stopwords que a função count_words.
Tokenização e Filtragem: Realiza a tokenização e filtragem das palavras, como na função count_words.
Inicialização de Coocorrências: Cria um dicionário para armazenar contadores de palavras co-ocorrentes para cada uma das palavras principais (top_words).
Atualização de Coocorrências: Para cada palavra na lista filtrada, se a palavra estiver entre as top_words, um contexto de 5 palavras antes e depois é considerado. As palavras nesse contexto são contadas como co-ocorrências.
Top 5 Coocorrências: Para cada palavra principal, as 5 palavras que mais co-ocorrem são retornadas.
Execução Principal
Upload do Arquivo: Solicita ao usuário que carregue um arquivo PDF.
Extração de Texto: Extrai o texto do PDF carregado.
Contagem das Palavras Mais Comuns: Obtém as 10 palavras mais frequentes no texto.
Co-ocorrências das Palavras: Encontra as 5 palavras que mais frequentemente ocorrem próximas de cada uma das top 10 palavras.
Impressão dos Resultados: Exibe as top 10 palavras e as 5 palavras que mais frequentemente co-ocorrem com cada uma delas.
Diretrizes para Escolha das Palavras
Stopwords: Palavras comuns que não agregam valor na análise de conteúdo são removidas. Adicionalmente, palavras específicas como "sobre", "todos" e "todas" foram excluídas devido à sua alta frequência e baixa relevância semântica.
Tokenização: Separação do texto em palavras individuais para análise.
Filtragem: Remoção de palavras não alfanuméricas e stopwords para focar em palavras significativas.
Co-ocorrências: Análise do contexto das palavras principais para identificar associações frequentes, fornecendo uma visão sobre como as palavras são usadas em conjunto no texto.

Este README.md fornece uma explicação detalhada do script, instruções de uso, requisitos e a lógica por trás da escolha das palavras e da análise de co-ocorrências.
