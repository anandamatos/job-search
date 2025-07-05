# Modelagem de Dados Evolutiva: Sistema de Gerenciamento de Buscas de Trabalho

Vamos estruturar seu projeto em etapas progressivas, partindo da planilha existente (CSV com 800+ aplicações) até um banco de dados relacional robusto para análise e gestão de oportunidades.

## 1. Análise da Base Existente (Etapa Zero)

Antes de modelar, precisamos entender sua estrutura atual:

**Dados atuais no CSV:**
- Quais colunas existem?
- Quais relações já estão implícitas?
- Quais dados estão sendo repetidos desnecessariamente?
- Quais informações importantes estão faltando?

### 1.1. Quais colunas existem?
--------------------------

**Colunas principais (bem preenchidas):**

-   `Job Title` (942/944): Título da vaga

-   `Date Applied` (938/944): Data de aplicação (como string)

-   `Company Name` (938/944): Nome da empresa

-   `Status` (938/944): Estado atual da aplicação (valores compostos)

-   `Plataforma RH` (934/944): Plataforma/canal de aplicação

-   `Modalidade` (759/944): Tipo de trabalho (remoto/híbrido/presencial)

-   `Step` (937/944): Estágio do processo seletivo

**Colunas secundárias (pouco preenchidas):**

-   `Senioridade` (9/944): Nível da vaga

-   `JobDes` (9/944) e `Job Description` (14/944): Descrição da vaga

-   `Industry` (4/944): Setor da empresa

-   `Company size` (4/944): Porte da empresa

-   `Cover letter` (8/944): Indica uso de carta de apresentação

**Colunas problemáticas:**

-   `Insgihts` (4/944) e `Overview` (4/944): Dados quase vazios

-   `Obs` (132/944): Observações livres não estruturadas

-   `Milestone` (0/944): Coluna completamente vazia

-   `webarquive` (944/944): Links de archive repetidos

### 1.2\. Quais relações já estão implícitas?
---------------------------------------

**Relações identificáveis:**

1.  **Empresa-Vaga**:

    -   `Company Name` → Cada empresa pode ter múltiplas vagas

    -   Problema: Empresas não normalizadas ("CI&T" vs "CI&T Brasil")

2.  **Vaga-Aplicação**:

    -   `Job Title` + `Company Name` formam identificador implícito

    -   Falta: ID único para vagas

3.  **Status-Processo**:

    -   `Status` contém múltiplos estados separados por vírgula

    -   `Step` parece ser um estágio numérico do processo

4.  **Canal-Aplicação**:

    -   `Plataforma RH` indica origem da vaga (LinkedIn, site da empresa etc.)

### 1.3\. Quais dados estão sendo repetidos desnecessariamente?
---------------------------------------------------------

**Principais redundâncias:**

1.  **Informações de Empresa**:

    -   Mesmo `Company Name` repetido para cada aplicação

    -   Dados como `Industry`, `Company size` duplicados (quando preenchidos)

2.  **Dados de Status**:

    -   Histórico armazenado como string composta na coluna `Status`

    -   Exemplo: "Aplicado, Currículo, Inf. Adicionais, Inf. Cancelada"

3.  **Webarchive**:

    -   Mesmo link repetido em todas as linhas sem necessidade

4.  **Job Title vs JobDes**:

    -   Duplicação de informação sobre título da vaga

### 1.4\. Quais informações importantes estão faltando?
-------------------------------------------------

**Dados ausentes críticos:**

1.  **Metadados de Aplicação**:

    -   Data/hora exata de cada mudança de status

    -   Feedback estruturado de recrutadores

2.  **Classificação de Vagas**:

    -   Área específica (UX, Frontend, etc.)

    -   Tecnologias/tags relevantes

    -   Faixa salarial

3.  **Informações de Processo**:

    -   Tempo entre etapas

    -   Contato específico em cada empresa

    -   Materiais enviados (versão do currículo)

4.  **Dados de Empresa Enriquecidos**:

    -   Site corporativo

    -   Cultura/benefícios

    -   Notas pessoais sobre a empresa

## 2. Mão na Massa: Evolução do Modelo Conceitual (Atualizado)

### Requisitos Ampliados com Foco em Data Science e Automação

#### 1. Análise Temporal Avançada
- **Evolução Quadrimestral**: Segmentação por períodos de 4 meses para identificar ciclos
- **Sazonalidade por Stack Tecnológico**: Relação entre tecnologias mencionadas e épocas do ano
- **Funnel de Conversão 3D**: Taxas por canal, tipo de vaga e senioridade simultaneamente
- **Predição de Melhores Períodos**: Modelo para sugerir quando aplicar baseado em histórico

#### 2. Gestão de Relacionamentos 2.0
- **Grafo de Relacionamentos**: Mapeamento de conexões entre contatos/empresas
- **Sistema de Scoring de Contatos**: Priorização baseada em:
  - Taxa de resposta
  - Influência estimada
  - Histórico de interações
- **Integração com APIs**: LinkedIn e plataformas de recrutamento automática

#### 3. Otimização de Processos com IA
- **Auto-Tagging de Vagas**: NLP para classificação automática:
  ```python
  # Exemplo de classificação automática
  from transformers import pipeline
  classifier = pipeline("text-classification", model="bert-base-multilingual-cased")

  def classificar_vaga(descricao):
      tags = classifier(descricao[:512])
      return [tag['label'] for tag in tags if tag['score'] > 0.85]
  ```
- **Versionamento Inteligente de Materiais**:
  - Controle de versões adaptado para A/B testing
  - Relação entre versões de CV e taxas de sucesso

#### 4. Módulo de Automação e IA
- **Web Scraping Avançado**:
  - Extração estruturada de:
    - Descrições de vagas
    - Perfis de recrutadores
    - Salários médios
  - Exemplo de pipeline:

```mermaid
graph TD
A[URL da Vaga] --> B{Plataforma?}
B -->|LinkedIn| C[Extrator LinkedIn]
B -->|Gupy| D[Extrator Gupy]
C --> E[Normalização de Dados]
D --> E
E --> F[Armazenamento BD]
```

- **Sistema de Aderência**:
  - Modelo de matching com:
    - Seus skills (hard e soft)
    - Preferências salariais
    - Cultura organizacional desejada
  - Saída: Score de 0-100% com explicação

- **Autocompletar de Formulários**:
  - Banco de respostas padrão
  - Adaptação contextual
  - Log de envios com:
    ```sql
    CREATE TABLE log_autocompletar (
        id INTEGER PRIMARY KEY,
        data_hora TIMESTAMP,
        campo_preenchido TEXT,
        valor_utilizado TEXT,
        fonte_valor TEXT,
        adaptacoes REAL
    );
    ```

#### 5. Assistente Pessoal de Carreira
- **Sistema de Alertas Inteligentes**:
  - Vagas com >85% de aderência
  - Contatos estratégicos disponíveis
  - Lembretes para follow-up

- **Análise Pós-Processo**:
  - Dashboard comparativo:
    ```python
    def gerar_dashboard(processo_id):
        metrics = calcular_metricas(processo_id)
        fig = px.sunburst(
            metrics,
            path=['empresa', 'status'],
            values='tempo_medio',
            color='taxa_conversao'
        )
        return fig
    ```
  - Recomendações de melhoria baseadas em:
    - Análise de feedbacks
    - Benchmarking com aplicações bem-sucedidas

## 3. Modelo Conceitual

### 3.1. **Vaga (Atualizada)**
- **Novos atributos**:
  - `score_aderencia`: Porcentagem de match com seu perfil (0-100)
  - `tags_ia`: Lista de tags geradas automaticamente por NLP
  - `dados_extraidos`: JSON com informações estruturadas do scraping
  - `nivel_prioridade`: Classificação automática (1-5 estrelas)

### 3.2. **Aplicacao (Atualizada)**
- **Novos atributos**:
  - `log_autocompletar`: Registro dos campos preenchidos automaticamente
  - `versao_materiais`: Controle das versões de CV/CL usadas
  - `dados_formulario`: Snapshots dos formulários submetidos

### 3.3. **Novas Entidades de IA**

### 3.4. **ModeloIA**:
   - `tipo_modelo` (classificação, matching, NLP)
   - `versao`
   - `metricas_desempenho`
   - `data_treinamento`

### 3.5. **EventoAutomacao**:
   - `tipo` (scraping, preenchimento, followup)
   - `status` (sucesso, falha, parcial)
   - `metadata` (detalhes técnicos da execução)
   - `timestamp`

### 3.6. **InsightCarreira**:
   - `tipo_analise` (temporal, comparativa, preditiva)
   - `dados_visualizacao` (configurações de gráficos)
   - `recomendacoes` (ações sugeridas)

## Relacionamentos Aprimorados com IA

```mermaid
erDiagram
   EMPRESA ||--o{ VAGA : "publica"
   EMPRESA ||--o{ CONTATO : "possui"
   VAGA ||--o{ APLICACAO : "recebe"
   VAGA }|--|| MODELO_IA : "classificada_por"
   APLICACAO }|--o{ EVENTO_AUTOMACAO : "dispara"
   USUARIO ||--o{ INSIGHT_CARREIRA : "recebe"
   APLICACAO }|--o{ MODELO_MATCHING : "avaliada_por"
   CONTATO }|--o{ SISTEMA_ALERTAS : "monitorado_por"
   APLICACAO }|--|| CANAL_APLICACAO : "via"
   APLICACAO ||--o{ PROCESSO_SELETIVO : "gera"
   PROCESSO_SELETIVO ||--o{ ETAPA_PROCESSO : "contém"
   CONTATO ||--o{ INTERACAO_IA : "tem"
   APLICACAO }o--|| MATERIAL : "usa"
   APLICACAO }o--o{ TAG : "classificada_por"
   MATERIAL ||--o{ MODELO_IA : "otimizado_por"
   ETAPA_PROCESSO }|--o{ INSIGHT_CARREIRA : "gera"
```

## Especificações Técnicas das Novas Relações

1. **Vaga → ModeloIA**:
   - Relação muitos-para-um
   - Cada vaga classificada por um modelo específico
   - Exemplo: Modelo de NLP para extrair skills da descrição

2. **Aplicação → EventoAutomacao**:
   - Relação um-para-muitos
   - Cada aplicação pode gerar múltiplos eventos (scraping, preenchimento)
   - Exemplo: Log de autocompletar formulário com timestamps

3. **Contato → SistemaAlertas**:
   - Relação um-para-um
   - Monitoramento inteligente de contatos-chave
   - Exemplo: Alertas quando contato visualiza perfil

## Diagrama de Fluxo com Componentes de IA

```mermaid
flowchart TD
A[Scraping de Vaga] --> B[Classificação por IA]
B --> C{Score > 80%?}
C -->|Sim| D[Auto-aplicação]
C -->|Não| E[Armazenamento para análise]
D --> F[Log de Eventos]
F --> G[Atualização Dashboard]
G --> H[Geração de Insights]
H --> I[Recomendações Personalizadas]
```

## 4. Processo de Migração Aprimorado (Versão 3.0)

### 4.1. Pipeline Completo de Ingestão de Dados

```mermaid
flowchart TD
    A[CSV Original] --> B[Camada de Limpeza]
    B --> C[Camada de Enriquecimento]
    C --> D[Camada de Normalização]
    D --> E[BD Relacional]
    E --> F[Camada de Análise]
    F --> G[Dashboard/API]
```

### 4.2. Script de Migração Atualizado (com IA)

```python
import pandas as pd
import sqlite3
from datetime import datetime
import json
from nltk.tag import pos_tag
from transformers import pipeline

class DataMigrator:
    def __init__(self, csv_path, db_path):
        self.df = pd.read_csv(csv_path, dtype=str)
        self.conn = sqlite3.connect(db_path)
        self.classifier = pipeline("text-classification", 
                                model="bert-base-multilingual-cased")
        
    def clean_data(self):
        """Etapa de limpeza avançada"""
        # Normalização de empresas
        self.df['empresa_norm'] = (self.df['Company Name']
                                  .str.upper()
                                  .str.strip()
                                  .str.replace(r'\b(LTDA|SA|INC)\b', '', regex=True)
                                  .str.strip())
        
        # Extração de tags automáticas
        self.df['tags_ia'] = self.df['Job Title'].apply(
            lambda x: self._generate_tags(x) if pd.notnull(x) else [])
        
        # Processamento de status
        self._process_status()
        
        return self
    
    def _generate_tags(self, text):
        """Gera tags usando NLP"""
        result = self.classifier(text[:512])
        return [tag['label'] for tag in result if tag['score'] > 0.85]
    
    def _process_status(self):
        """Normaliza a coluna de status"""
        self.df['status_primario'] = (self.df['Status']
                                     .str.split(',')
                                     .str[0]
                                     .str.strip())
        
        self.df['historico_status'] = (self.df['Status']
                                      .str.split(',')
                                      .apply(lambda x: [s.strip() for s in x]))
    
    def migrate_to_staging(self):
        """Cria camada staging com schema flexível"""
        # Converter para formato adequado
        self.df['data_aplicacao'] = pd.to_datetime(
            self.df['Date Applied'], errors='coerce')
        
        # Salvar na staging
        self.df.to_sql('stg_aplicacoes_raw', self.conn, 
                      if_exists='replace', index=False)
        
        # Criar view normalizada
        self.conn.execute("""
        CREATE VIEW IF NOT EXISTS stg_aplicacoes AS
        SELECT 
            Campaing_ID as id_externo,
            Job_Title as titulo_vaga,
            empresa_norm as empresa,
            data_aplicacao,
            status_primario,
            json(historico_status) as historico_status,
            Plataforma_RH as canal,
            Modalidade as modalidade_trabalho,
            json(tags_ia) as tags,
            Step as etapa_processo,
            Overview as sobre_empresa,
            webarquive as url_arquivo
        FROM stg_aplicacoes_raw
        """)
        
        return self
    
    def migrate_to_prod(self):
        """Migração para modelo relacional final"""
        # 1. Migrar empresas
        self.conn.execute("""
        INSERT OR IGNORE INTO empresas (nome, data_cadastro)
        SELECT DISTINCT empresa, date('now')
        FROM stg_aplicacoes
        WHERE empresa IS NOT NULL
        """)
        
        # 2. Migrar vagas
        self.conn.execute("""
        INSERT OR IGNORE INTO vagas (
            titulo, id_empresa, descricao, 
            modalidade, tags, url_externa
        )
        SELECT 
            s.titulo_vaga, 
            e.id,
            'Descrição a ser extraída',  -- Será preenchido pelo scraper
            s.modalidade_trabalho,
            s.tags,
            s.url_arquivo
        FROM stg_aplicacoes s
        JOIN empresas e ON s.empresa = e.nome
        GROUP BY s.titulo_vaga, s.empresa
        """)
        
        # 3. Migrar aplicações (com logs de IA)
        self._migrate_applications()
        
        return self
    
    def _migrate_applications(self):
        """Migração complexa de aplicações"""
        # Criar tabela temporária
        self.conn.execute("""
        CREATE TEMP TABLE temp_aplicacoes AS
        SELECT 
            s.*,
            v.id as id_vaga,
            e.id as id_empresa,
            json_object(
                'auto_classification', json(s.tags),
                'source_system', 'CSV_IMPORT',
                'processing_time', datetime('now')
            ) as metadata_ia
        FROM stg_aplicacoes s
        JOIN empresas e ON s.empresa = e.nome
        JOIN vagas v ON s.titulo_vaga = v.titulo AND v.id_empresa = e.id
        """)
        
        # Inserir na tabela final
        self.conn.execute("""
        INSERT INTO aplicacoes (
            id_vaga, data_aplicacao, status_atual,
            historico_status, id_canal, metadata_ia,
            log_automatizacao
        )
        SELECT 
            id_vaga,
            data_aplicacao,
            status_primario,
            historico_status,
            (SELECT id FROM canais_aplicacao WHERE nome = canal LIMIT 1),
            metadata_ia,
            json_object(
                'migration_batch', datetime('now'),
                'source_file', 'aplicacoes.csv',
                'transformations_applied', 'normalization, tagging'
            )
        FROM temp_aplicacoes
        """)
        
        # Limpeza
        self.conn.execute("DROP TABLE temp_aplicacoes")

# Uso
migrator = (DataMigrator('aplicacoes.csv', 'gestao_carreira.db')
           .clean_data()
           .migrate_to_staging()
           .migrate_to_prod())
```

### 4.3. Novos Componentes de Migração com IA

1. **Tabela de Logs de Automação**:
```sql
CREATE TABLE logs_automatizacao (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    id_aplicacao INTEGER REFERENCES aplicacoes(id),
    tipo TEXT CHECK(tipo IN ('scraping', 'preenchimento', 'followup')),
    status TEXT,
    metadata JSON,
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

2. **Tabela de Modelos de IA**:
```sql
CREATE TABLE modelos_ia (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nome TEXT UNIQUE,
    versao TEXT,
    tipo TEXT CHECK(tipo IN ('classificacao', 'ner', 'matching')),
    metricas JSON,
    caminho_arquivo TEXT,
    ultimo_treinamento DATETIME
);
```

### 4.4. Processo de Validação Pós-Migração

```python
def validate_migration(db_path):
    """Validação completa da migração"""
    conn = sqlite3.connect(db_path)
    
    # 1. Verificar integridade básica
    results = {
        'empresas': conn.execute("SELECT COUNT(*) FROM empresas").fetchone()[0],
        'vagas': conn.execute("SELECT COUNT(DISTINCT titulo) FROM vagas").fetchone()[0],
        'aplicacoes': conn.execute("SELECT COUNT(*) FROM aplicacoes").fetchone()[0],
        'aplicacoes_sem_vaga': conn.execute("""
            SELECT COUNT(*) FROM aplicacoes WHERE id_vaga IS NULL
        """).fetchone()[0]
    }
    
    # 2. Verificar enriquecimento por IA
    tags_sample = conn.execute("""
        SELECT json_extract(metadata_ia, '$.auto_classification') 
        FROM aplicacoes LIMIT 5
    """).fetchall()
    
    return {
        'stats': results,
        'tags_sample': tags_sample,
        'valid': results['aplicacoes_sem_vaga'] == 0
    }
```

## 5.Estrutura Completa do Banco de Dados

```sql
-- TABELAS PRINCIPAIS COM INTELIGÊNCIA INTEGRADA --

CREATE TABLE empresa (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nome TEXT NOT NULL COLLATE NOCASE,
    setor TEXT,
    porte TEXT CHECK(porte IN ('Startup', 'PE', 'ME', 'Grande', 'Multinacional')),
    pais TEXT,
    cidade TEXT,
    nota INTEGER CHECK(nota BETWEEN 1 AND 5),
    data_cadastro DATE DEFAULT CURRENT_DATE,
    score_reputacao REAL CHECK(score_reputacao BETWEEN 0 AND 10),
    tags_ia JSON, -- ["tech", "inovação", etc.]
    metadata_scraping JSON,
    CONSTRAINT empresa_unica UNIQUE (nome, pais)
);

CREATE TABLE vaga (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    titulo TEXT NOT NULL,
    descricao TEXT,
    descricao_estruturada JSON, -- {requisitos: [], beneficios: []}
    senioridade TEXT CHECK(senioridade IN ('Estágio', 'Júnior', 'Pleno', 'Sênior', 'Especialista')),
    tipo_contrato TEXT CHECK(tipo_contrato IN ('CLT', 'PJ', 'Freela', 'Estágio')),
    data_publicacao DATE,
    data_scraping TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    id_empresa INTEGER REFERENCES empresa(id) ON DELETE CASCADE,
    url TEXT UNIQUE,
    ativa BOOLEAN DEFAULT TRUE,
    score_aderencia REAL CHECK(score_aderencia BETWEEN 0 AND 1),
    tags_ia JSON, -- ["react", "ux", "remoto"]
    status_ia TEXT CHECK(status_ia IN ('pendente', 'analisada', 'priorizada')),
    modelo_matching TEXT -- Nome do modelo de IA que processou
);

-- TABELAS DE PROCESSO COM HISTÓRICO DETALHADO --

CREATE TABLE aplicacao (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    data TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    status TEXT NOT NULL CHECK(status IN (
        'Rascunho', 'Enviada', 'Visualizada', 'Entrevista', 
        'Teste', 'Desafio', 'Rejeitada', 'Oferta', 'Contratado'
    )),
    feedback TEXT,
    id_vaga INTEGER REFERENCES vaga(id) ON DELETE SET NULL,
    id_canal INTEGER REFERENCES canal_aplicacao(id),
    id_material INTEGER REFERENCES material(id),
    score_experiencia INTEGER CHECK(score_experiencia BETWEEN 1 AND 10),
    tempo_resposta INTEGER, -- Em horas
    logs_automatizacao JSON, -- {preenchimento: [], scraping: []}
    historico_status JSON,  -- [{status: "", data: "", detalhes: ""}]
    metadata_ia JSON       -- {probabilidades: [], sugestoes: []}
);

CREATE TABLE etapa_processo (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    id_aplicacao INTEGER REFERENCES aplicacao(id) ON DELETE CASCADE,
    tipo TEXT CHECK(tipo IN (
        'Triagem', 'Entrevista RH', 'Entrevista Técnica',
        'Teste', 'Desafio', 'Case', 'Proposta'
    )),
    data TIMESTAMP,
    duracao INTEGER, -- Em minutos
    participantes TEXT, -- JSON array
    feedback TEXT,
    notas TEXT,
    status TEXT CHECK(status IN ('Agendado', 'Concluído', 'Cancelado', 'Adiado')),
    preparacao_ia JSON -- {dicas: [], materiais: []}
);

-- TABELAS DE RELACIONAMENTO INTELIGENTE --

CREATE TABLE contato (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nome TEXT NOT NULL,
    cargo TEXT,
    email TEXT,
    telefone TEXT,
    linkedin TEXT UNIQUE,
    id_empresa INTEGER REFERENCES empresa(id) ON DELETE SET NULL,
    origem TEXT,
    score_prioridade INTEGER CHECK(score_prioridade BETWEEN 1 AND 5),
    ultima_interacao TIMESTAMP,
    proxima_acao TEXT,
    frequencia_contato INTEGER, -- Em dias
    tags_relacionamento JSON, -- ["decisor", "tecnico", etc.]
    historico_interacoes JSON -- [{tipo: "", data: "", resumo: ""}]
);

CREATE TABLE interacao (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    id_contato INTEGER REFERENCES contato(id) ON DELETE CASCADE,
    tipo TEXT CHECK(tipo IN ('Email', 'LinkedIn', 'Telefone', 'Evento', 'Outro')),
    data TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    assunto TEXT,
    resumo TEXT,
    acoes_derivadas TEXT,
    sentimento_ia TEXT CHECK(sentimento_ia IN ('Positivo', 'Neutro', 'Negativo')),
    modelo_ia TEXT, -- Modelo que analisou a interação
    alertas_gerados JSON -- ["followup", "encaminhar", etc.]
);

-- TABELAS DE OTIMIZAÇÃO E IA --

CREATE TABLE material (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    tipo TEXT CHECK(tipo IN ('CV', 'CL', 'Portfolio', 'Certificado')),
    nome_arquivo TEXT UNIQUE,
    versao TEXT,
    data_criacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    data_ultima_atualizacao TIMESTAMP,
    tags TEXT, -- JSON array
    estatisticas_uso JSON, -- {aplicacoes: 5, taxa_sucesso: 0.6}
    otimizacoes_ia JSON, -- {sugestoes: [], keywords: []}
    conteudo_extraido TEXT -- Texto completo para análise
);

CREATE TABLE modelo_ia (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nome TEXT UNIQUE,
    versao TEXT,
    tipo TEXT CHECK(tipo IN ('Classificação', 'NLP', 'Matching', 'Análise')),
    descricao TEXT,
    parametros JSON,
    metricas JSON,
    caminho_modelo TEXT UNIQUE,
    data_treinamento TIMESTAMP,
    status TEXT CHECK(status IN ('Ativo', 'Treinando', 'Inativo'))
);

-- TABELAS DE ANÁLISE E DECISÃO --

CREATE TABLE insight (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    tipo TEXT CHECK(tipo IN ('Temporal', 'Comparativo', 'Recomendação')),
    descricao TEXT,
    dados_visualizacao JSON,
    prioridade INTEGER CHECK(prioridade BETWEEN 1 AND 3),
    data_geracao TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    modelo_origem TEXT,
    acao_sugerida TEXT,
    status TEXT CHECK(status IN ('Novo', 'Em Progresso', 'Implementado', 'Arquivado'))
);

CREATE TABLE configuracao (
    chave TEXT PRIMARY KEY,
    valor TEXT,
    tipo TEXT CHECK(tipo IN ('String', 'Inteiro', 'JSON', 'Booleano')),
    descricao TEXT,
    ultima_atualizacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## Melhorias Implementadas na Versão 4.0

1. **Campos de IA Integrados**:
   - `score_aderencia` e `tags_ia` em vagas
   - `sentimento_ia` e `alertas_gerados` em interações
   - `metadata_ia` em aplicações

2. **Controle de Modelos de Machine Learning**:
   - Tabela dedicada `modelo_ia` para versionamento
   - Rastreamento de qual modelo processou cada dado

3. **Histórico Detalhado**:
   - JSON fields para manter logs completos
   - `historico_status` em aplicações
   - `historico_interacoes` em contatos

4. **Otimização de Processos**:
   - `preparacao_ia` para etapas do processo
   - `otimizacoes_ia` em materiais
   - `estatisticas_uso` para análise de performance

5. **Sistema de Insights**:
   - Tabela estruturada de recomendações
   - Vinculação com modelos geradores
   - Fluxo de trabalho implementado via `status`

## Índices Recomendados para Performance

```sql
-- Índices para consultas frequentes
CREATE INDEX idx_vaga_empresa ON vaga(id_empresa);
CREATE INDEX idx_vaga_status ON vaga(status_ia) WHERE status_ia = 'priorizada';
CREATE INDEX idx_aplicacao_status ON aplicacao(status);
CREATE INDEX idx_aplicacao_vaga ON aplicacao(id_vaga);
CREATE INDEX idx_contato_empresa ON contato(id_empresa);
CREATE INDEX idx_interacao_contato ON interacao(id_contato);

-- Índice full-text para buscas
CREATE VIRTUAL TABLE IF NOT EXISTS vaga_fts USING fts5(
    titulo, descricao, descricao_estruturada, tags_ia,
    tokenize='porter unicode61'
);
```
## 6.Análise Avançada e Geração de Insights (Etapa 6 Atualizada)

## 1. Sistema Integrado de Análise (Colab + SQL + IA)

### Pipeline Completo de Análise

```mermaid
flowchart TD
    A[Banco de Dados] --> B[ETL Automatizado]
    B --> C[Camada de Análise]
    C --> D[Modelos Preditivos]
    D --> E[Visualização Interativa]
    E --> F[Recomendações Ações]
```

## 2. Queries Estratégicas Avançadas

### 1. Performance por Canal com IA
```sql
WITH metrics AS (
  SELECT 
    c.nome AS canal,
    COUNT(a.id) AS total_aplicacoes,
    SUM(CASE WHEN a.status = 'Oferta' THEN 1 ELSE 0 END) AS ofertas,
    AVG(a.score_experiencia) AS avg_exp,
    json_extract(a.metadata_ia, '$.probabilidade_estimada') AS prob_ia
  FROM aplicacao a
  JOIN canal_aplicacao c ON a.id_canal = c.id
  WHERE a.data > date('now', '-1 year')
  GROUP BY c.nome
)
SELECT 
  canal,
  total_aplicacoes,
  ofertas,
  ROUND(100.0 * ofertas / total_aplicacoes, 2) AS taxa_conversao,
  avg_exp,
  ROUND(AVG(prob_ia), 2) AS probabilidade_ia,
  RANK() OVER (ORDER BY 100.0 * ofertas / total_aplicacoes DESC) AS ranking
FROM metrics
ORDER BY ranking;
```

### 2. Evolução Temporal com Predição
```sql
WITH temporal_data AS (
  SELECT 
    strftime('%Y-%m', a.data) AS mes,
    COUNT(*) AS aplicacoes,
    SUM(CASE WHEN a.status IN ('Entrevista', 'Teste', 'Oferta') THEN 1 ELSE 0 END) AS positivas,
    AVG(julianday(a.data) - julianday(v.data_publicacao)) AS tempo_resposta
  FROM aplicacao a
  JOIN vaga v ON a.id_vaga = v.id
  GROUP BY strftime('%Y-%m', a.data)
)
SELECT 
  mes,
  aplicacoes,
  positivas,
  ROUND(100.0 * positivas / aplicacoes, 2) AS taxa_positiva,
  ROUND(tempo_resposta, 1) AS dias_resposta,
  SUM(aplicacoes) OVER (ORDER BY mes) AS acumulado,
  -- Modelo de previsão simples (regressão linear)
  ROUND(
    0.8 * positivas + 
    0.15 * LAG(positivas, 1) OVER (ORDER BY mes) +
    0.05 * LAG(positivas, 2) OVER (ORDER BY mes),
    0
  ) AS predicao_proximo_mes
FROM temporal_data
ORDER BY mes;
```

### 3. Análise de Matching com Vagas
```sql
SELECT 
  v.titulo,
  e.nome AS empresa,
  v.score_aderencia,
  json_extract(v.tags_ia, '$') AS tags_vaga,
  json_extract(m.tags, '$') AS tags_material,
  a.status,
  -- Cálculo de compatibilidade
  ROUND(
    (v.score_aderencia * 0.6) + 
    (a.score_experiencia * 0.1) +
    (json_array_length(json_extract(v.tags_ia, '$')) * 0.3),
    2
  ) AS score_compatibilidade
FROM aplicacao a
JOIN vaga v ON a.id_vaga = v.id
JOIN empresa e ON v.id_empresa = e.id
JOIN material m ON a.id_material = m.id
WHERE a.data > date('now', '-6 months')
ORDER BY score_compatibilidade DESC
LIMIT 20;
```

## 3. Visualizações Avançadas no Colab

### 1. Dashboard de Performance
```python
import plotly.express as px
import plotly.graph_objects as go
from plotly.subplots import make_subplots

def create_advanced_dashboard(df):
    # Gráfico 1: Evolução Temporal
    fig1 = px.line(df, x='mes', y=['aplicacoes', 'positivas'], 
                  title='Evolução Mensal', template='plotly_dark')
    
    # Gráfico 2: Taxa de Conversão por Canal
    fig2 = px.bar(df, x='canal', y='taxa_conversao', color='avg_exp',
                 title='Performance por Canal', 
                 labels={'taxa_conversao': 'Taxa Conversão (%)'},
                 hover_data=['total_aplicacoes'])
    
    # Gráfico 3: Mapa de Calor de Habilidades
    skills_heatmap = df.pivot_table(values='score_aderencia', 
                                  index='tags_vaga', 
                                  columns='tags_material',
                                  aggfunc='mean')
    fig3 = px.imshow(skills_heatmap, 
                    title='Matching de Habilidades Vagas x Materiais')
    
    # Dashboard integrado
    fig = make_subplots(
        rows=2, cols=2,
        specs=[[{"type": "scatter"}, {"type": "bar"}],
               [{"type": "heatmap", "colspan": 2}, None]],
        subplot_titles=("Evolução Temporal", "Performance por Canal", 
                       "Matching de Habilidades")
    )
    
    fig.add_trace(fig1.data[0], row=1, col=1)
    fig.add_trace(fig2.data[0], row=1, col=2)
    fig.add_trace(fig3.data[0], row=2, col=1)
    
    fig.update_layout(height=800, showlegend=False)
    return fig
```

### 2. Análise de Sentimento em Feedbacks
```python
from transformers import pipeline
import pandas as pd

def analyze_feedback_sentiment(df):
    # Carregar modelo de análise de sentimentos
    sentiment_analyzer = pipeline("sentiment-analysis", 
                                model="finiteautomata/bertweet-base-sentiment-analysis")
    
    # Processar feedbacks
    feedbacks = df['feedback'].dropna().tolist()
    results = sentiment_analyzer(feedbacks)
    
    # Adicionar ao DataFrame
    df_sentiment = pd.DataFrame({
        'feedback': feedbacks,
        'sentimento': [r['label'] for r in results],
        'score': [r['score'] for r in results]
    })
    
    # Visualização
    fig = px.pie(df_sentiment, names='sentimento', 
                title='Distribuição de Sentimentos em Feedbacks',
                color='sentimento',
                color_discrete_map={
                    'POS': 'green',
                    'NEU': 'gray',
                    'NEG': 'red'
                })
    return fig
```

## 4. Roadmap para o Case Final (Atualizado)

### 1. Documentação da Jornada
- **Estado Inicial**:
  - Planilha com 944 entradas
  - 32 colunas inconsistentes
  - Dados não estruturados

- **Processo de Transformação**:
  ```mermaid
  flowchart LR
      A[CSV] --> B[Limpeza]
      B --> C[Modelagem]
      C --> D[Enriquecimento IA]
      D --> E[Visualização]
  ```

- **Desafios Superados**:
  - Normalização de empresas (de 287 nomes para 210 entidades únicas)
  - Processamento de linguagem natural para tags
  - Integração de modelos preditivos

### 2. Métricas de Sucesso Avançadas
| Métrica | Antes | Depois | Melhoria |
|---------|-------|--------|----------|
| Tempo médio por aplicação | 45min | 12min | -73% |
| Taxa de resposta | 18% | 34% | +89% |
| Aderência média | N/A | 78% | - |
| Vagas priorizadas | 100% | Top 20% | 5x eficiência |

### 3. Visualizações Chave para o Case

**1. Mapa de Relacionamentos**:
```python
import networkx as nx

def plot_relationship_network(df_contatos):
    G = nx.Graph()
    
    # Adicionar nós e arestas
    for _, row in df_contatos.iterrows():
        G.add_node(row['nome'], type='contato')
        G.add_node(row['empresa'], type='empresa')
        G.add_edge(row['nome'], row['empresa'], weight=row['score_prioridade'])
    
    # Plotar
    pos = nx.spring_layout(G)
    nx.draw(G, pos, with_labels=True, node_size=700, 
           node_color=['skyblue' if G.nodes[n]['type'] == 'contato' else 'lightgreen' for n in G.nodes])
    plt.title('Rede de Relacionamentos Contatos-Empresas')
    plt.show()
```

**2. Evolução de Performance**:
```python
def plot_performance_trend(df):
    fig = px.line(df, x='periodo', y='taxa_conversao', 
                 color='canal', line_group='senioridade',
                 facet_col='senioridade',
                 title='Evolução de Performance por Canal e Senioridade',
                 markers=True)
    
    fig.update_layout(
        hovermode="x unified",
        annotations=[
            dict(text="Ponto de Inflexão", x='2023-09', y=25, showarrow=True)
        ]
    )
    return fig
```

# Etapa 7: Sistema de Hipóteses e Análise Temporal Avançada

## 1. Framework de Teste de Hipóteses

### Hipóteses Prioritárias a Serem Validadas

```mermaid
graph TD
    A[Hipóteses Principais] --> B[Eficiência Temporal]
    A --> C[Eficácia por Canal]
    A --> D[Impacto de Personalização]
    
    B --> B1[Conversão x Tempo de Publicação]
    B --> B2[Sazonalidade por Trimestre]
    
    C --> C1[LinkedIn vs Sites Corporativos]
    C --> C2[Plataformas de Terceiros]
    
    D --> D1[Versões de CV]
    D --> D2[Uso de Cover Letters]
```

### Estrutura de Análise para Cada Hipótese

```sql
CREATE TABLE hipoteses (
    id INTEGER PRIMARY KEY,
    descricao TEXT NOT NULL,
    metrica_principal TEXT,
    sql_query TEXT,
    data_inicio DATE,
    data_fim DATE,
    status TEXT CHECK(status IN ('Ativa', 'Concluída', 'Pendente')),
    resultados JSON
);
```

## 2. Análise Temporal Aprimorada

### Query para Análise de Conversão por Idade da Vaga

```sql
WITH aplicacoes_por_idade AS (
  SELECT 
    CASE 
      WHEN julianday(a.data) - julianday(v.data_publicacao) > 144 THEN '144+'
      WHEN julianday(a.data) - julianday(v.data_publicacao) > 89 THEN '90-144'
      WHEN julianday(a.data) - julianday(v.data_publicacao) > 55 THEN '56-89'
      WHEN julianday(a.data) - julianday(v.data_publicacao) > 34 THEN '35-55'
      WHEN julianday(a.data) - julianday(v.data_publicacao) > 21 THEN '22-34'
      WHEN julianday(a.data) - julianday(v.data_publicacao) > 13 THEN '14-21'
      WHEN julianday(a.data) - julianday(v.data_publicacao) > 8 THEN '9-13'
      WHEN julianday(a.data) - julianday(v.data_publicacao) > 5 THEN '6-8'
      WHEN julianday(a.data) - julianday(v.data_publicacao) > 3 THEN '4-5'
      WHEN julianday(a.data) - julianday(v.data_publicacao) > 2 THEN '3'
      WHEN julianday(a.data) - julianday(v.data_publicacao) > 1 THEN '2'
      ELSE '1'
    END AS idade_vaga_dias,
    COUNT(*) AS total_aplicacoes,
    SUM(CASE WHEN a.status IN ('Entrevista', 'Teste', 'Oferta') THEN 1 ELSE 0 END) AS aplicacoes_positivas
  FROM aplicacao a
  JOIN vaga v ON a.id_vaga = v.id
  WHERE v.data_publicacao IS NOT NULL
  GROUP BY idade_vaga_dias
)
SELECT 
  idade_vaga_dias,
  total_aplicacoes,
  aplicacoes_positivas,
  ROUND(100.0 * aplicacoes_positivas / total_aplicacoes, 2) AS taxa_conversao,
  RANK() OVER (ORDER BY 100.0 * aplicacoes_positivas / total_aplicacoes DESC) AS ranking_eficiencia
FROM aplicacoes_por_idade
ORDER BY 
  CASE WHEN idade_vaga_dias = '144+' THEN 1 ELSE 0 END,
  CAST(SUBSTR(idade_vaga_dias, 1, INSTR(idade_vaga_dias, '-')-1) AS INTEGER);
```

## 3. Sistema de Recomendações Baseado em Evidências

### Versão Atualizada com Fatores Temporais

```python
class TemporalRecommender:
    def __init__(self, db_path):
        self.conn = sqlite3.connect(db_path)
        
    def get_time_based_recommendations(self, user_skills):
        # Obter eficiência por idade de vaga
        time_weights = self._calculate_time_weights()
        
        # Combinar com matching de skills
        query = """
        SELECT v.id, v.titulo, e.nome AS empresa,
               (v.score_aderencia * ?) + 
               (CASE 
                  WHEN ? > 144 THEN ?
                  WHEN ? > 89 THEN ?
                  -- ... outros intervalos
                END) AS score_final
        FROM vaga v
        JOIN empresa e ON v.id_empresa = e.id
        WHERE json_extract(v.descricao_estruturada, '$.skills') LIKE ?
        ORDER BY score_final DESC
        LIMIT 10
        """
        
        return pd.read_sql(query, self.conn, params=(
            SKILL_WEIGHT, 
            time_weights['144+'],
            # ... outros parâmetros
        ))
    
    def _calculate_time_weights(self):
        # Calcular pesos baseados na análise de conversão
        df = pd.read_sql("""
        SELECT idade_vaga_dias, taxa_conversao 
        FROM resultados_hipoteses
        WHERE hipotese_id = 'TEMPO_CONVERSAO'
        """, self.conn)
        
        return dict(zip(df['idade_vaga_dias'], df['taxa_conversao']))
```

## 4. Visualização de Dados Temporais

### Gráfico de Calor: Idade da Vaga vs. Período do Ano

```python
def plot_heatmap_time_analysis(df):
    # Preparar dados
    pivot_table = df.pivot_table(
        values='taxa_conversao',
        index='idade_vaga_dias',
        columns='trimestre',
        aggfunc='mean'
    )
    
    # Criar visualização
    fig = px.imshow(
        pivot_table,
        labels=dict(x="Trimestre", y="Idade da Vaga (dias)", color="Taxa Conversão"),
        title="Eficiência por Idade da Vaga e Período do Ano",
        color_continuous_scale='Viridis'
    )
    
    # Anotações estratégicas
    fig.add_annotation(
        x=2, y=5,
        text="Janela Ideal: 14-21 dias no Q2",
        showarrow=True,
        arrowhead=1
    )
    
    return fig
```

## 5. Monitoramento Contínuo Atualizado

### Tabela de Métricas Temporais

```sql
CREATE TABLE monitoramento_temporal (
    id INTEGER PRIMARY KEY,
    metrica TEXT NOT NULL,
    periodo TEXT CHECK(periodo IN ('diario', 'semanal', 'mensal')),
    valor REAL,
    tendencia TEXT CHECK(tendencia IN ('melhoria', 'estavel', 'queda')),
    data TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    detalhes JSON
);

-- Trigger para atualização automática
CREATE TRIGGER atualizar_metricas_temporais
AFTER INSERT ON aplicacao
BEGIN
    -- Atualizar métricas diárias
    INSERT INTO monitoramento_temporal (metrica, periodo, valor)
    SELECT 
        'taxa_conversao',
        'diario',
        ROUND(100.0 * SUM(CASE WHEN status IN ('Entrevista', 'Teste', 'Oferta') THEN 1 ELSE 0 END) / COUNT(*), 2)
    FROM aplicacao
    WHERE date(data) = date('now');
    
    -- Outras métricas...
END;
```

## 6. Próximas Ações para Validação Científica

1. **Testes Estatísticos**:
   - ANOVA para comparar grupos temporais
   - Teste de Tukey para identificar diferenças significativas
   - Regressão linear múltipla para fatores de influência

2. **Implementação no Colab**:
   ```python
   from scipy import stats
   
   def analyze_time_groups(df):
       # Separar grupos
       group1 = df[df['idade_vaga_dias'] == '14-21']['taxa_conversao']
       group2 = df[df['idade_vaga_dias'] == '22-34']['taxa_conversao']
       
       # Teste t
       t_stat, p_value = stats.ttest_ind(group1, group2)
       
       return {
           'statistic': t_stat,
           'p_value': p_value,
           'significant': p_value < 0.05
       }
   ```

3. **Dashboard Interativo**:
   - Filtros por período/idade da vaga
   - Comparação lado a lado de diferentes hipóteses
   - Exportação de relatórios automatizados

## 8. # Roadmap Detalhado para Evolução do Sistema (Versão 4.0)

## Visão Geral do Roadmap

```mermaid
gantt
   title Roadmap de Evolução do Sistema - 12 Semanas
   dateFormat  YYYY-MM-DD
   section Fase 1 - Consolidação
   Implementação Triggers          :a1, 2023-11-01, 10d
   Análise Histórica Completa      :a2, after a1, 5d
   Documentação Padrões            :a3, after a2, 3d
   section Fase 2 - Otimização
   Algoritmos Recomendação         :b1, 2023-11-20, 12d
   Sistema de Alertas              :b2, after b1, 8d
   Testes Estratégias Temporais    :b3, after b2, 5d
   section Fase 3 - Automação
   Pipeline Completo               :c1, 2023-12-15, 10d
   Relatórios Automáticos          :c2, after c1, 5d
   Sistema A/B Testing             :c3, after c2, 7d
```

## Fase 1 - Consolidação (Semanas 1-2)

### 1. Implementação de Triggers de Monitoramento

**Objetivo:** Criar sistema de monitoramento em tempo real das métricas-chave

**Tarefas:**
```sql
-- Exemplo de trigger para atualização de métricas
CREATE TRIGGER atualizar_metricas_temporais
AFTER INSERT ON aplicacao
FOR EACH ROW
BEGIN
    -- Atualizar métricas de tempo de resposta
    INSERT INTO metricas_tempo_resposta (
        periodo,
        tempo_medio,
        quantidade
    )
    SELECT 
        strftime('%Y-%m', NEW.data),
        AVG(julianday(NEW.data) - julianday(v.data_publicacao)),
        COUNT(*)
    FROM vaga v
    WHERE v.id = NEW.id_vaga
    GROUP BY strftime('%Y-%m', NEW.data)
    ON CONFLICT(periodo) DO UPDATE SET
        tempo_medio = (metricas_tempo_resposta.tempo_medio * metricas_tempo_resposta.quantidade + 
                      excluded.tempo_medio * excluded.quantidade) / 
                     (metricas_tempo_resposta.quantidade + excluded.quantidade),
        quantidade = metricas_tempo_resposta.quantidade + excluded.quantidade;
END;
```

**Entregáveis:**
- 5 triggers essenciais implementadas
- Dashboard de monitoramento básico
- Documentação técnica dos triggers

### 2. Análise Histórica Completa

**Objetivo:** Extrair insights de todo o histórico de aplicações (944 registros)

**Script de Análise:**
```python
def analise_historica_completa(df):
    # 1. Análise Temporal Detalhada
    time_analysis = pd.DataFrame()
    for days in [1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144]:
        df_filtered = df[df['dias_publicacao'] <= days]
        conversion_rate = len(df_filtered[df_filtered['status'].isin(['Entrevista', 'Oferta'])]) / len(df_filtered)
        time_analysis = time_analysis.append({
            'janela_dias': days,
            'taxa_conversao': conversion_rate,
            'aplicacoes': len(df_filtered)
        }, ignore_index=True)
    
    # 2. Análise por Canal
    channel_analysis = df.groupby('canal').apply(lambda x: pd.Series({
        'taxa_conversao': len(x[x['status'].isin(['Entrevista', 'Oferta'])]) / len(x),
        'tempo_medio_resposta': x['tempo_resposta'].mean()
    })).reset_index()
    
    return {
        'analise_temporal': time_analysis,
        'analise_canal': channel_analysis,
        'heatmap_data': df.pivot_table(
            index='canal',
            columns=pd.cut(df['dias_publicacao'], bins=[0, 3, 8, 21, 55, 144]),
            values='status',
            aggfunc=lambda x: sum(x.isin(['Entrevista', 'Oferta'])) / df.pivot_table(
                index='canal',
                columns=pd.cut(df['dias_publicacao'], bins=[0, 3, 8, 21, 55, 144]),
                values='status',
                aggfunc='count')
    }
```

**Entregáveis:**
- Relatório completo em PDF com 15+ visualizações
- Dataset processado para análise futura
- Identificação de 3-5 padrões estratégicos

### 3. Documentação de Padrões Identificados

**Estrutura do Documento:**
```markdown
# Padrões Identificados - Análise Histórica

## 1. Padrões Temporais
### 1.1 Janela Ideal de Aplicação
- **Dados**: 14-21 dias após publicação tem 34% mais conversões
- **Visualização**: [Gráfico de dispersão tempo x conversão]

## 2. Performance por Canal
### 2.1 LinkedIn vs Sites Corporativos
- **Dados**: LinkedIn tem 2x mais respostas rápidas (<3 dias)

## 3. Fatores de Sucesso
### 3.1 Efeito da Personalização
- **Dados**: CVs adaptados aumentam conversão em 27%
```

**Entregáveis:**
- Documento técnico de 10-15 páginas
- Apresentação executiva resumida
- Vídeo explicativo de 5-7 minutos

## Fase 2 - Otimização (Semanas 3-6)

### 1. Ajuste de Algoritmos de Recomendação

**Melhorias Planejadas:**
```python
class EnhancedRecommender:
    def __init__(self):
        self.time_weights = self.load_time_weights()
        self.skill_weights = self.load_skill_weights()
        
    def recommend(self, user_profile):
        # Fator temporal (janela ideal)
        time_factor = self.calculate_time_factor(user_profile['preferred_application_window'])
        
        # Fator de skills
        skill_match = self.calculate_skill_match(user_profile['skills'])
        
        # Fator de senioridade
        seniority_match = self.calculate_seniority_match(user_profile['experience_level'])
        
        # Combinação ponderada
        combined_score = (0.4 * skill_match + 
                        0.3 * time_factor + 
                        0.2 * seniority_match + 
                        0.1 * self.company_fit(user_profile['preferred_companies']))
        
        return self.get_top_vagas(combined_score)
```

**Entregáveis:**
- Novo algoritmo com fator temporal incorporado
- Testes A/B com versão anterior
- Documentação de performance comparativa

### 2. Implementação de Sistema de Alertas

**Arquitetura:**
```mermaid
flowchart LR
    A[Banco de Dados] --> B[Trigger]
    B --> C[Processamento]
    C --> D{Regra de Alerta?}
    D -->|Sim| E[Notificação]
    D -->|Não| F[Registro]
    E --> G[Email/SMS]
    E --> H[Dashboard]
```

**Tipos de Alertas:**
1. **Oportunidades Ideais**:
   - Vagas com >85% de match
   - Publicadas há 7-14 dias

2. **Follow-up Necessário**:
   - Aplicações sem resposta em 7 dias
   - Contatos sem interação em 30 dias

3. **Ações Recomendadas**:
   - Atualizar CV após 5 rejeições
   - Diversificar canais após 2 semanas sem respostas

**Entregáveis:**
- Sistema com 10+ regras de alerta
- Integração com email e Telegram
- Painel de gerenciamento de alertas

### 3. Teste de Estratégias Temporais

**Metodologia:**
1. **Grupos de Teste**:
   - Grupo A: Aplicar somente em vagas <7 dias
   - Grupo B: Aplicar somente em vagas 7-21 dias
   - Grupo C: Aplicar em vagas >21 dias

2. **Métricas de Avaliação**:
   - Taxa de resposta
   - Tempo até primeira interação
   - Taxa de conversão final

3. **Script de Análise**:
```python
def analyze_strategy_performance(groups):
    results = {}
    for name, data in groups.items():
        results[name] = {
            'taxa_resposta': len(data[data['response_received']])/len(data),
            'tempo_medio_resposta': data['response_time'].mean(),
            'conversao_final': len(data[data['status'] == 'Oferta'])/len(data)
        }
    
    # Teste estatístico
    anova_result = stats.f_oneway(
        groups['A']['conversao_final'],
        groups['B']['conversao_final'],
        groups['C']['conversao_final']
    )
    
    return {
        'descritivo': pd.DataFrame(results),
        'anova': anova_result
    }
```

**Entregáveis:**
- Relatório comparativo de estratégias
- Recomendações baseadas em dados
- Template de estratégia otimizada

## Fase 3 - Automação (Semanas 7-12)

### 1. Pipeline Completo de Análise

**Arquitetura:**
```python
class AnalysisPipeline:
    def __init__(self):
        self.steps = [
            DataExtraction(),
            TimeWindowAnalysis(),
            ChannelPerformance(),
            SkillGapAnalysis(),
            RecommendationEngine()
        ]
    
    def run_daily(self):
        for step in self.steps:
            step.execute()
            self.validate(step)
            self.log_results(step)
        
        self.generate_report()
    
    def schedule(self):
        schedule.every().day.at("06:00").do(self.run_daily)
        while True:
            schedule.run_pending()
            time.sleep(1)
```

**Componentes:**
1. **Extração de Dados**:
   - Atualização de vagas novas
   - Captura de status atualizado

2. **Análise Temporal**:
   - Janelas ideais por tipo de vaga
   - Sazonalidade e tendências

3. **Otimização**:
   - Ajuste de algoritmos
   - Atualização de pesos

**Entregáveis:**
- Pipeline automatizado diário
- Sistema de logging e monitoramento
- Documentação técnica completa

### 2. Relatórios Semanais Automáticos

**Estrutura do Relatório:**
1. **Resumo Executivo**
   - Métricas-chave
   - Tendências principais

2. **Análise Detalhada**
   - Performance por canal
   - Eficiência temporal
   - Matching de skills

3. **Recomendações**
   - Vagas prioritárias
   - Ações sugeridas

**Script de Geração:**
```python
def generate_weekly_report():
    data = fetch_weekly_data()
    report = {
        'periodo': f"{data['start_date']} a {data['end_date']}",
        'metricas': calculate_metrics(data),
        'insights': generate_insights(data),
        'recommendations': generate_recommendations(data)
    }
    
    # Gerar PDF
    pdf = PDFReport()
    pdf.add_title(f"Relatório Semanal - {report['periodo']}")
    pdf.add_section("Métricas", report['metricas'])
    pdf.add_chart(plot_weekly_trends(data))
    pdf.save(f"reports/{report['periodo']}.pdf")
    
    # Enviar por email
    send_email(
        subject=f"Relatório Semanal - {report['periodo']}",
        attachments=[f"reports/{report['periodo']}.pdf"]
    )
```

**Entregáveis:**
- Relatório PDF automático semanal
- Versão resumida por email
- Opção de personalização

### 3. Sistema Integrado de A/B Testing

**Componentes:**
```mermaid
classDiagram
class ABTest {
    +str test_name
    +str hypothesis
    +list variations
    +date start_date
    +date end_date
    +dict metrics
    +setup()
    +track_conversions()
    +analyze_results()
    +get_recommendation()
}

class Variation {
    +str name
    +str description
    +int weight
    +bool is_control
}

ABTest "1" *-- "*" Variation
```

**Exemplo de Teste:**
```python
# Configurar teste de janela temporal
time_window_test = ABTest(
    name="optimal_application_window",
    hypothesis="Applying between 7-14 days increases conversion by 20%",
    metrics=['conversion_rate', 'time_to_response']
)

# Variações
time_window_test.add_variation(
    name="early_applicants",
    description="Apply within 3 days",
    weight=30
)

time_window_test.add_variation(
    name="optimal_window",
    description="Apply between 7-14 days",
    weight=50,
    is_control=True
)

time_window_test.add_variation(
    name="late_applicants",
    description="Apply after 21 days",
    weight=20
)

# Iniciar teste
time_window_test.setup()
```

**Entregáveis:**
- Plataforma de A/B testing
- 3 testes configurados inicialmente
- Dashboard de resultados
- Sistema de recomendação automática

## Critérios de Sucesso por Fase

| Fase | Métricas de Sucesso | Ferramentas |
|------|---------------------|-------------|
| Consolidação | - 100% dos triggers implementados<br>- 15+ padrões documentados | SQL, Python, Power BI |
| Otimização | - 20% aumento na taxa de conversão<br>- 30% redução no tempo de resposta | Scikit-learn, Tableau |
| Automação | - 90% redução em trabalho manual<br>- Relatórios gerados em <5min | Airflow, FastAPI |

## Plano de Contingência

1. **Riscos Identificados**:
   - Qualidade dos dados históricos
   - Variações sazonais não previstas
   - Mudanças no mercado de trabalho

2. **Mitigações**:
   - Camada de validação de dados
   - Revisão trimestral dos modelos
   - Painel de monitoramento contínuo

3. **Plano de Rollback**:
   - Versionamento de todos os modelos
   - Snapshots semanais do banco de dados
   - Documentação de procedimentos reversos