\# Analyze Text - Azure AI Language in Microsoft Foundry



\## Objetivo



Neste laboratório foi desenvolvida uma aplicação Python utilizando o Azure AI Language através do Microsoft Foundry Tools.



O objetivo foi criar uma aplicação capaz de analisar textos de avaliações de hotéis, utilizando recursos de Inteligência Artificial para:



\* Detectar o idioma do texto;

\* Identificar entidades mencionadas;

\* Reconhecer informações de identificação pessoal (PII);

\* Realizar a anonimização de dados sensíveis.



A aplicação utiliza o SDK oficial do Azure Language para Python.



\---



\# Pré-requisitos



Para realizar este laboratório foram utilizados:



\* Assinatura ativa do Microsoft Azure;

\* Visual Studio Code;

\* Python 3.13.x;

\* Azure CLI;

\* Extensão Python para Visual Studio Code.



\---



\# 1. Criando o projeto no Microsoft Foundry



O primeiro passo foi acessar o portal:



```

https://ai.azure.com

```



Após autenticação com a conta Azure, foi criado um novo projeto no Microsoft Foundry.



Configurações utilizadas:



\* \*\*Foundry Resource:\*\* nome padrão gerado pelo projeto;

\* \*\*Subscription:\*\* assinatura Azure disponível;

\* \*\*Resource Group:\*\* grupo de recursos criado/selecionado;

\* \*\*Region:\*\* região recomendada pelo Microsoft Foundry.



Após a criação do projeto, foram obtidas as informações necessárias:



\* API Key;

\* Project Endpoint;

\* OpenAI Endpoint.



Neste laboratório foi utilizado o \*\*Project Endpoint\*\* para configurar a aplicação.



\---



\# 2. Obtendo os arquivos da aplicação



Os arquivos iniciais da aplicação foram disponibilizados pelo Microsoft Learn.



A estrutura utilizada no laboratório foi:



```

text-analysis

│

├── reviews

│   └── arquivos de avaliações

│

├── .env

│

├── requirements.txt

│

└── text-analysis.py

```



O arquivo principal da aplicação foi:



```

text-analysis.py

```



\---



\# 3. Configurando o ambiente Python



\## Criando o ambiente virtual



Dentro da pasta da aplicação foi criado um ambiente virtual Python:



```bash

python -m venv labenv

```



\---



\## Ativando o ambiente virtual



O ambiente foi ativado utilizando:



```bash

labenv\\Scripts\\activate

```



Após ativação, o terminal passou a indicar o ambiente Python ativo.



Exemplo:



```

(labenv)

```



\---



\# 4. Instalando as dependências



Com o ambiente virtual ativo, foram instaladas as bibliotecas necessárias:



```bash

pip install -r requirements.txt

```



Entre as dependências instaladas está o SDK do Azure Language Text Analytics.



\---



\# 5. Configurando o endpoint do Microsoft Foundry



A aplicação utiliza um arquivo `.env` para armazenar as configurações do ambiente Azure.



Arquivo:



```

.env

```



Foi necessário preencher a variável:



```env

FOUNDRY\_ENDPOINT=

```



\---



\## Problema encontrado no endpoint



O endpoint copiado diretamente do Microsoft Foundry apresentava o seguinte formato:



```

https://<resource-name>.services.ai.azure.com/api/projects/<project-name>

```



Ao utilizar esse valor diretamente na aplicação, ocorreu erro de conexão.



O motivo era que o endpoint copiado continha o caminho específico do projeto:



```

/api/projects/{project\_name}

```



Esse trecho precisava ser removido.



\---



\## Configuração correta



O valor utilizado no arquivo `.env` ficou:



```env

FOUNDRY\_ENDPOINT=https://<resource-name>.services.ai.azure.com

```



O endpoint utilizado pela aplicação deve conter somente o endereço do recurso Azure AI.



Formato esperado:



```

https://{your-foundry-resource-name}.services.ai.azure.com

```



\---



\# 6. Criando o cliente do Azure AI Language



No arquivo:



```

text-analysis.py

```



foram adicionados os namespaces necessários:



```python

from azure.identity import DefaultAzureCredential

from azure.ai.textanalytics import TextAnalyticsClient

```



\---



Na função principal, foi criado o cliente responsável pela comunicação com o Azure AI Language:



```python

credential = DefaultAzureCredential()



ai\_client = TextAnalyticsClient(

&#x20;   endpoint=foundry\_endpoint,

&#x20;   credential=credential

)

```



\---



\# 7. Autenticação no Azure



Antes de executar a aplicação foi realizado o login utilizando Azure CLI:



```bash

az login

```



Após autenticação, a aplicação passou a utilizar as credenciais da conta Azure para acessar o recurso criado no Microsoft Foundry.



\---



\# 8. Executando a aplicação inicial



A aplicação foi executada utilizando:



```bash

python text-analysis.py

```



Inicialmente, o programa apenas carregava os arquivos de avaliações existentes na pasta:



```

reviews

```



e criava o cliente do Azure Language.



\---



\# 9. Detectando o idioma dos textos



Foi implementada a funcionalidade de identificação do idioma de cada avaliação.



Código adicionado:



```python

detectedLanguage = ai\_client.detect\_language(

&#x20;   documents=\[text]

)\[0]



print('\\nLanguage: {}'.format(

&#x20;   detectedLanguage.primary\_language.name

))

```



Resultado:



A aplicação passou a identificar automaticamente o idioma de cada documento analisado.



\---



\# 10. Extraindo entidades dos textos



Foi adicionada a funcionalidade de reconhecimento de entidades.



Exemplos de entidades identificadas:



\* Pessoas;

\* Locais;

\* Organizações;

\* Datas;

\* Outros elementos relevantes.



Código utilizado:



```python

entities = ai\_client.recognize\_entities(

&#x20;   documents=\[text]

)\[0].entities



if len(entities) > 0:

&#x20;   print("\\nEntities")



&#x20;   for entity in entities:

&#x20;       print('\\t{} ({})'.format(

&#x20;           entity.text,

&#x20;           entity.category

&#x20;       ))

```



\---



\# 11. Identificando informações pessoais (PII)



A última funcionalidade implementada foi a identificação de informações pessoais.



O Azure Language foi utilizado para encontrar dados como:



\* Nomes;

\* Telefones;

\* Endereços;

\* Outras informações sensíveis.



Código utilizado:



```python

pii\_result = ai\_client.recognize\_pii\_entities(

&#x20;   documents=\[text]

)\[0]



pii\_entities = pii\_result.entities



if len(pii\_entities) > 0:

&#x20;   print("\\nPII Entities")



&#x20;   for pii\_entity in pii\_entities:

&#x20;       print('\\t{} ({})'.format(

&#x20;           pii\_entity.text,

&#x20;           pii\_entity.category

&#x20;       ))



&#x20;   print(

&#x20;       "Redacted Text:\\n {}".format(

&#x20;           pii\_result.redacted\_text

&#x20;       )

&#x20;   )

```



Resultado:



Além de identificar informações pessoais, a aplicação gera uma versão anonimizada do texto.



\---



\# Conceitos aprendidos



Durante este laboratório foram aplicados os seguintes conceitos:



\* Criação de projetos no Microsoft Foundry;

\* Uso do Azure AI Language;

\* Configuração de aplicações utilizando variáveis de ambiente;

\* Uso do Azure SDK para Python;

\* Autenticação utilizando Azure CLI;

\* Criação de clientes para serviços Azure AI;

\* Detecção automática de idioma;

\* Reconhecimento de entidades;

\* Identificação e anonimização de informações pessoais (PII).



\---



\# Limpeza dos recursos



Após finalizar os testes, os recursos criados no Azure devem ser removidos para evitar custos desnecessários.



Procedimento:



1\. Acessar o portal Azure;

2\. Localizar o Resource Group utilizado;

3\. Selecionar \*\*Delete Resource Group\*\*;

4\. Confirmar a exclusão.



