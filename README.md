# Portfólio - Bot Whatsapp Integrado com o GPT

### 1. Introdução

O Assistente Virtual para WhatsApp Integrado com OpenAI é uma solução que automatiza interações via WhatsApp, oferecendo suporte dinâmico por meio de uma base de conhecimento e inteligência artificial.

### 2. Escopo

O sistema permitirá:
- Responder automaticamente a mensagens recebidas no WhatsApp com base em uma base de respostas pré-definidas e, caso necessário, recorrendo à OpenAI;
- Armazenar as interações em um banco de dados SQLite, garantindo rastreabilidade e futuras análises;
- Minimizar custos e latência, utilizando uma versão mais leve do GPT (exemplo: GPT-3.5 Turbo) e um cache local para respostas já processadas;
- Manter uma arquitetura bem estruturada, seguindo o modelo MVC (Model-View-Controller) para modularidade e organização.

### 3. Requisitos

##### 3.1 Requisitos Funcionais
- RF-01	O sistema deve receber mensagens via WhatsApp API (Meta Cloud API).
- RF-02	O sistema deve verificar se a mensagem possui uma resposta na base de conhecimento local antes de consultar a OpenAI.
- RF-03	O sistema deve utilizar um cache local para armazenar respostas já geradas, evitando requisições desnecessárias à OpenAI.
- RF-04	O sistema deve recorrer ao modelo GPT-3.5 Turbo (ou outra versão mais barata) caso a resposta não esteja no cache ou na base local.
- RF-05	O sistema deve armazenar todas as interações em um banco de dados SQLite.
- RF-06	O sistema deve permitir adicionar, editar e remover respostas da base local através de um painel administrativo ou API.
- RF-07	O sistema deve enviar mensagens de resposta no WhatsApp em um formato compatível com a API do WhatsApp.
- RF-08	O sistema deve garantir que as mensagens sejam processadas e respondidas em tempo real ou com latência mínima.
- RF-09	O sistema deve permitir configurar limites de requisição para a OpenAI para evitar custos elevados.
- RF-10	O sistema deve ser implementado seguindo o modelo MVC, separando a lógica de negócios, comunicação com APIs e armazenamento de dados.

2.2 Requisitos Não Funcionais
- RNF-01	O sistema deve ser desenvolvido em Python.
- RNF-02	A persistência de dados deve ser feita utilizando SQLite para simplicidade e portabilidade.
- RNF-03	O tempo de resposta para consultas ao cache ou base local deve ser inferior a 1 segundo.
- RNF-04	O tempo de resposta para consultas à OpenAI deve ser inferior a 3 segundos.
- RNF-05	O sistema deve permitir a fácil escalabilidade para bancos de dados mais robustos (ex: PostgreSQL) no futuro.
- RNF-06	O sistema deve utilizar boas práticas de arquitetura de software, garantindo modularidade e reutilização de código.
- RNF-07	O sistema deve ser compatível com ambiente Linux e Windows.
- RNF-08	O código deve ser documentado e seguir PEP8 (Python Enhancement Proposal 8).
- RNF-09	O sistema deve permitir fácil configuração do modelo de IA e dos parâmetros de inferência.

### 3. Modelagem da Arquitetura (Diagrama C4)

Agora, seguem os diagramas no modelo C4 para descrever a arquitetura do sistema.

##### Diagrama C4 - Nível 1 (Contexto)

Este nível descreve a visão geral do sistema e como ele interage com usuários e serviços externos.

Elementos
- Usuário → Envia mensagens para o bot no WhatsApp.
- WhatsApp Cloud API → Recebe as mensagens do usuário e as repassa para o assistente virtual.
- Assistente Virtual → O núcleo do sistema, responsável por processar as mensagens e gerar respostas.
- Base de Respostas → Contém um conjunto de respostas pré-definidas para perguntas frequentes.
- Cache de Respostas → Guarda respostas já geradas para evitar consultas repetidas à OpenAI.
- OpenAI API → É consultada quando a resposta não está na base local nem no cache.
- Banco de Dados SQLite → Armazena as interações do usuário para registros e futuras análises.

Fluxo de Interação
- O Usuário envia uma mensagem via WhatsApp.
- A WhatsApp Cloud API recebe a mensagem e a encaminha para o Assistente Virtual.
- O Assistente Virtual verifica se a resposta já está na Base de Respostas ou no Cache de Respostas.
- Se não encontrar uma resposta, consulta a OpenAI API (usando GPT-3.5 Turbo).
- O Assistente Virtual envia a resposta de volta ao usuário via WhatsApp Cloud API.
- A interação é armazenada no Banco de Dados SQLite.

##### Diagrama C4 - Nível 2 (Container)

Este nível detalha a estrutura interna do sistema, que segue o modelo MVC (Model-View-Controller).

Componentes Internos:

Controller
- Recebe mensagens da API do WhatsApp.
- Processa as requisições e define qual ação tomar.
- Faz chamadas ao Model para buscar respostas ou registrar interações.
- Envia respostas formatadas para a View.

Model
- Gerencia o acesso à Base de Respostas, Cache de Respostas e Banco de Dados SQLite.
- Se não encontrar uma resposta na base local ou cache, aciona a OpenAI API.
- Atualiza o Banco de Dados SQLite com novas interações.

View
- Responsável pelo envio das respostas formatadas ao usuário via WhatsApp Cloud API.

Fluxo de Dados
- WhatsApp Cloud API → Envia mensagem para o Controller.
- Controller → Verifica no Model se há resposta na Base de Respostas ou no Cache.
- Se não houver resposta, o Model faz requisição à OpenAI API.
- Model → Retorna a resposta ao Controller.
- Controller → Passa a resposta para a View.
- View → Envia a mensagem para o usuário via WhatsApp Cloud API.

##### Diagrama C4 - Nível 3 (Componentes)

Neste nível, detalhamos os componentes internos de cada parte do MVC.

Controller
- MessageController: Processa mensagens recebidas, chama o Model e envia resposta à View.

Model
- ResponseManager: Verifica a base local, cache e, se necessário, consulta a OpenAI.
- CacheManager: Armazena respostas recentes para evitar chamadas repetidas.
- DatabaseManager: Gerencia interações no SQLite.

View
- WhatsAppResponder: Formata e envia as mensagens para a API do WhatsApp.

# Estrutura de pastas
```plaintext
assistente_virtual/
│── main.py                      # Arquivo principal que inicia a aplicação
│── config.py                     # Configurações do sistema
│── requirements.txt               # Dependências do projeto
│
├── core/                          # Componentes centrais e abstrações
│   ├── database.py                 # Gerenciador do banco de dados (SQLite + SQLAlchemy)
│   ├── cache.py                    # Gerenciador de cache
│   ├── interfaces.py               # Interfaces para APIs externas (WhatsApp, OpenAI)
│
├── models/                        # Modelos de dados (ORM)
│   ├── base.py                     # Definição da base do banco de dados
│   ├── interaction.py              # Modelo para armazenar interações
│   ├── predefined_responses.py     # Modelo para base de respostas pré-definidas
│
├── services/                      # Serviços (OpenAI, WhatsApp, etc.)
│   ├── whatsapp_service.py         # Implementação da API do WhatsApp
│   ├── llm_service.py              # Implementação da API OpenAI
│
├── controllers/                   # Controladores (MVC)
│   ├── message_controller.py       # Processa mensagens recebidas
│
├── views/                         # Views (MVC)
│   ├── whatsapp_view.py            # Responsável por formatar e enviar mensagens
│
├── tests/                         # Testes unitários e de integração
│   ├── test_whatsapp_service.py    # Testes para WhatsApp Service
│   ├── test_llm_service.py         # Testes para OpenAI Service
│   ├── test_message_controller.py  # Testes para o controlador
```

