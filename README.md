import os
import requests
import json

# Configurações do Azure Cognitive Search
AZURE_SEARCH_ENDPOINT = "https://<SEU-SERVICO>.search.windows.net"
AZURE_SEARCH_API_KEY = "<SUA-CHAVE-API>"
AZURE_SEARCH_INDEX_NAME = "meu-index"

# Cabeçalhos para autenticação
HEADERS = {
    "Content-Type": "application/json",
    "api-key": AZURE_SEARCH_API_KEY
}

def criar_index():
    """Cria um índice no Azure Cognitive Search."""
    index_config = {
        "name": AZURE_SEARCH_INDEX_NAME,
        "fields": [
            {"name": "id", "type": "Edm.String", "key": True},
            {"name": "titulo", "type": "Edm.String"},
            {"name": "conteudo", "type": "Edm.String"},
            {"name": "categoria", "type": "Edm.String"}
        ]
    }
    response = requests.put(f"{AZURE_SEARCH_ENDPOINT}/indexes/{AZURE_SEARCH_INDEX_NAME}?api-version=2023-07-01-preview", 
                            headers=HEADERS, 
                            json=index_config)
    print(response.json())

def adicionar_documentos(documentos):
    """Adiciona documentos ao índice."""
    data = {"value": documentos}
    response = requests.post(f"{AZURE_SEARCH_ENDPOINT}/indexes/{AZURE_SEARCH_INDEX_NAME}/docs/index?api-version=2023-07-01-preview", 
                             headers=HEADERS, 
                             json=data)
    print(response.json())

def pesquisar(query):
    """Realiza uma pesquisa no índice."""
    search_url = f"{AZURE_SEARCH_ENDPOINT}/indexes/{AZURE_SEARCH_INDEX_NAME}/docs/search?api-version=2023-07-01-preview"
    search_payload = {"search": query}
    response = requests.post(search_url, headers=HEADERS, json=search_payload)
    return response.json()

if __name__ == "__main__":
    criar_index()
    documentos = [
        {"id": "1", "titulo": "IA na Saúde", "conteudo": "A inteligência artificial está transformando a saúde.", "categoria": "Saúde"},
        {"id": "2", "titulo": "Machine Learning", "conteudo": "O aprendizado de máquina permite sistemas mais inteligentes.", "categoria": "Tecnologia"}
    ]
    adicionar_documentos(documentos)
    resultado = pesquisar("Inteligência Artificial")
    print(json.dumps(resultado, indent=4))
