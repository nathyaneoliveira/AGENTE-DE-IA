# 🤖 Agentes de IA - Triagem e Atendimento Automático

## Descrição
Projeto desenvolvido durante a **IMERSÃO DEV AGENTES DE IA (ALURA E GOOGLE)**.  
Este notebook implementa um **workflow de agentes de IA** utilizando o modelo **Gemini** do Google, integrado com **LangChain** e bibliotecas auxiliares para leitura de documentos, embeddings e RAG (Retrieval-Augmented Generation).  

O objetivo principal é automatizar a triagem de solicitações de **Service Desk** da empresa **Carraro Desenvolvimento**, classificando as mensagens em:

- **AUTO_RESOLVER**: perguntas claras sobre políticas internas.
- **PEDIR_INFO**: mensagens vagas ou incompletas.
- **ABRIR_CHAMADO**: pedidos de exceção, liberações ou acesso especial.

O sistema também consulta documentos internos (PDFs) para fornecer respostas baseadas em contexto.

---

## Estrutura do Projeto

```

agentesdeIA.ipynb          # Notebook principal
requirements.txt           # Dependências do projeto (opcional)

````

---

## Requisitos

- Python 3.10+
- Colab ou ambiente local com Jupyter Notebook
- Bibliotecas:

```bash
pip install --upgrade langchain langchain-google-genai google-generativeai
pip install --upgrade langchain_community faiss-cpu langchain-text-splitters pymupdf
pip install --upgrade langgraph
pip install pydantic
````

* **Chave de API do Google Gemini** para acessar o modelo generativo.

---

## Funcionalidades

1. **Triagem de mensagens**:
   Classifica mensagens de usuários em `AUTO_RESOLVER`, `PEDIR_INFO` ou `ABRIR_CHAMADO`.

2. **RAG (Retrieval-Augmented Generation)**:
   Consulta PDFs internos e documentos carregados para fornecer respostas baseadas em contexto.

3. **Fluxo de atendimento automático (Workflow)**:

   * Nó de triagem → decide se auto-resolve, pede mais informações ou abre chamado.
   * Nó de auto-resolver → consulta RAG e decide se conclui ou escala.
   * Nó de pedir informação → solicita dados faltantes do usuário.
   * Nó de abrir chamado → gera descrição resumida para abertura de ticket.

4. **Gerenciamento de documentos**:

   * Carrega PDFs da pasta definida.
   * Divide documentos em chunks.
   * Calcula embeddings e armazena em FAISS para buscas de similaridade.

5. **Visualização do workflow**:
   Gera imagem do grafo de estados utilizando **Mermaid** via IPython.

---

## Uso

### Configuração da API Key

```python
from google.colab import userdata
GOOGLE_API_KEY = userdata.get('gemini')
```

### Inicializando o modelo Gemini

```python
from langchain_google_genai import ChatGoogleGenerativeAI

llm = ChatGoogleGenerativeAI(
    model='gemini-2.5-flash',
    temperature=0,
    api_key=GOOGLE_API_KEY
)
```

### Triagem de mensagens

```python
from langchain_core.messages import SystemMessage, HumanMessage

triagem_chain = llm.with_structured_output(TriagemOut)

resultado = triagem_chain.invoke([
    SystemMessage(content=TRIAGEM_PROMPT),
    HumanMessage(content="Posso reembolsar a internet?")
])
print(resultado.model_dump())
```

### Workflow de atendimento

```python
from langgraph.graph import StateGraph, START, END

workflow = StateGraph(AgentState)
workflow.add_node("triagem", node_triagem)
workflow.add_node("auto_resolver", node_auto_resolver)
workflow.add_node("pedir_info", node_pedir_info)
workflow.add_node("abrir_chamado", node_abrir_chamado)
# Definir edges e decisões condicionais...
grafo = workflow.compile()
```

### Teste do fluxo

```python
perguntas = ["Posso reembolsar a internet?", "Quero mais 5 dias de trabalho remoto."]
for p in perguntas:
    resposta_final = grafo.invoke({"pergunta": p})
    print(resposta_final)
```

---

## Contribuição

Este projeto é experimental.
Sugestões e melhorias podem ser feitas via **issues** ou **pull requests**.

---

## Licença

Uso experimental. Requer chave de API do Google Gemini.

---

## Status

*  Triagem automática implementada
*  Workflow de estados criado
*  Integração com documentos internos via RAG
*  Próximos passos: integração com sistemas de chamados e monitoramento de métricas
