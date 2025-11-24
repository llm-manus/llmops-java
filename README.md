### Introduction to the llmops Project


#### I. Project Overview
`llmops` is an LLM Operations (LLMOps) platform based on LangChain and the OpenAI API specification. It mainly provides capabilities related to large language model application development, knowledge base management, and conversational interaction. It supports integration with large models such as DeepSeek and Kimi, and features core functionalities like persistent conversation memory and Retrieval-Augmented Generation (RAG).


#### II. Tech Stack
- **Development Language**: Java 17
- **Core Frameworks**: Spring Boot 4, MyBatis, LangChain4j (LLM toolchain)
- **Database**: MySQL 7 (stores business data)
- **Other Tools**:
  - Jsoup (web content parsing)
  - Redis (distributed locks, caching, etc.)
  - Gson (JSON serialization/deserialization)
  - JieBa (Chinese keyword extraction)


#### III. Project Structure (Multi-module Design)
```
llmops-java/
├── .gitignore          # Git ignore configuration
├── README.md           # Project description
├── pom.xml             # Parent project dependencies
├── llmops-dao/         # Data access layer (database interaction)
│   ├── src/            # Entity classes, Mapper interfaces and XML
│   └── pom.xml
├── llmops-web/         # Web layer (controllers, API interfaces)
│   ├── src/            # Controllers, resource files (e.g., API docs)
│   └── pom.xml
├── llmops-common/      # Common components (utilities, VOs, constants, etc.)
│   ├── src/            # GsonUtil, general VOs, exception handling, etc.
│   └── pom.xml
├── llmops-service/     # Business logic layer
│   ├── src/            # Service implementations (RAG, document processing, tool services, etc.)
│   └── pom.xml
├── llmops-client/      # Client module (specific functions to be clarified)
│   ├── src/
│   └── pom.xml
├── .github/            # GitHub Actions workflow configuration
└── data/               # Data files (e.g., IP pool configuration ip_pool.json)
```


#### IV. Core Function Modules

##### 1. Chat and Conversation Management
- **Basic Chat**: Integrates with large models such as DeepSeek and Kimi, supporting basic interaction between users and models (using `ChatVO` to pass conversation parameters like tenant ID, conversation ID, prompt, etc.).
- **Memory Module**: Supports paginated queries of conversation history through `ChatHistoryVO`, enabling persistent conversation states (solving the stateless issue of LLMs).


##### 2. Knowledge Base and RAG (Retrieval-Augmented Generation)
- **Document Processing**:
  - Supports document loading, parsing, and splitting into segments (`LLMOpsDocumentTask` handles the full lifecycle of documents: loading → splitting → indexing → storage).
  - The document segment entity (`LlmOpsSegmentDO`) records segment content, keywords, vector node IDs, etc., for subsequent retrieval.
- **Retrieval Capabilities**:
  - Implements vector-based semantic retrieval through `RAGSample`: embeds documents into a vector database, and matches relevant segments via vector similarity during queries.
  - Keyword Extraction: Uses the JieBa tool to extract keywords from document segments and maintains a knowledge base keyword table (for fast retrieval).


##### 3. Tool and API Integration
- **Built-in Tools**: `LLMOpsBuiltinToolController` and `BuiltinToolService` provide query capabilities for built-in tools, supporting retrieval of tool information by provider and tool name.
- **API Tool Management**: `LLMOpsApiToolController` offers CRUD interfaces for API tools, supporting validation of OpenAPI schemas, creation/updating/deletion of tools, etc.


##### 4. Application Management
- `LLMOpsAppController` implements lifecycle management of applications, including creating, querying, updating, and deleting applications, as well as managing application configurations (e.g., knowledge base association, retrieval strategies, long-term memory switches).


##### 5. Internet Connectivity
- `WebContentSample` implements web content extraction: crawls web HTML via RestTemplate, cleans irrelevant tags (script, style, etc.) using Jsoup, extracts main text, and supports internet query functions.


##### 6. Authorization and Authentication
- `LLMOpsOauthController` provides third-party authorization functions, supporting retrieval of authorization redirect URLs for platforms like GitHub and Google, and handling authorization callbacks to obtain access tokens.


#### V. Key Code Explanations
1. **RAG Core Process (`RAGSample`)**:
   - Loads documents into memory and stores document embeddings in a vector database via `EmbeddingStoreIngestor`.
   - Constructs a `ContentRetriever` to retrieve relevant segments from the vector database and generates responses in conjunction with large models.

2. **Document Processing Flow (`LLMOpsDocumentTask`)**:
   - Parses documents → splits into segments → extracts keywords → generates vectors → stores in a vector database, updating the status of documents/segments throughout (parsing, indexing, completed, etc.).

3. **Distributed Lock (`RedisLockGateway`)**:
   - Implements distributed locks based on Redis for resource control in concurrent scenarios (e.g., concurrent control during document processing).

4. **JSON Tool (`GsonUtil`)**:
   - Encapsulates Gson for object-JSON serialization/deserialization, supporting special handling of date types (`Date`).


#### VI. Summary
The `llmops` platform integrates capabilities such as large language model interaction, knowledge base management, and tool integration. Through modular design, it achieves functional decoupling, making it suitable as an enterprise-level LLM application foundation platform. It supports rapid construction of large model applications with memory, retrieval augmentation, and internet connectivity capabilities.
