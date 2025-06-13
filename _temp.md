## **Processo de Modelagem de Dados: Identificação de Entidades, Atributos e Relacionamentos**  

#### **Objetivo**:  
Estruturar um modelo conceitual de banco de dados, identificando entidades, atributos e relacionamentos com base nos requisitos do sistema.  

---

### **Etapas do Processo**:  

#### **1. Definição do Minimundo**  
- **Objetivo**: Delimitar o escopo do sistema, focando nos elementos relevantes do mundo real.  
- **Ações**:  
  - Listar os principais conceitos/objetos envolvidos (ex.: "Conferência", "Participante", "Artigo").  
  - Descartar elementos irrelevantes ao sistema.  

#### **2. Levantamento de Requisitos**  
- **Objetivo**: Coletar necessidades dos usuários e regras de negócio.  
- **Técnicas**:  
  - **Entrevistas** com stakeholders.  
  - **Questionários** para escala ampla.  
  - **Observação direta** de processos existentes.  
- **Saída**: Documento com funcionalidades, restrições e dados necessários (ex.: "Todo artigo deve ter um revisor").  

#### **3. Identificação das Entidades**  
- **Objetivo**: Listar objetos principais que armazenarão dados.  
- **Classificação**:  
  - **Entidades Fortes**: Existem independentemente (ex.: "Conferência").  
  - **Entidades Fracas**: Dependem de outras (ex.: "Apresentação" depende de "Sessão").  
- **Exemplo**:  
  - Conferência, Participante, Sessão, Artigo, Revisor.  

#### **4. Definição dos Relacionamentos**  
- **Objetivo**: Estabelecer conexões lógicas entre entidades.  
- **Passos**:  
  - Identificar pares de entidades inter-relacionadas.  
  - Definir **cardinalidade** (1:1, 1:N, N:M).  
  - Usar **entidades associativas** para relações N:M (ex.: "Submissão" liga "Artigo" e "Revisor").  
- **Exemplo**:  
  - "Participante" **apresenta** "Artigo" (1:N).  
  - "Artigo" **é avaliado por** "Revisor" (N:M, via entidade "Avaliação").  

#### **5. Especificação dos Atributos**  
- **Objetivo**: Detalhar características das entidades.  
- **Tipos de Atributos**:  
  - **Simples**: Indivisíveis (ex.: "Título da Conferência").  
  - **Compostos**: Divisíveis (ex.: "Endereço" em rua, cidade).  
  - **Multivalorados**: Múltiplos valores (ex.: "Telefones do Participante").  
  - **Chave**: Identificadores únicos (ex.: "ID_Conferência").  
- **Exemplo**:  
  - Entidade "Artigo": Título, Resumo, DataSubmissão, Status.  

#### **6. Validação com Regras de Negócio**  
- **Objetivo**: Garantir aderência às necessidades do sistema.  
- **Verificar**:  
  - Restrições (ex.: "Nenhum artigo sem revisor").  
  - Integridade dos relacionamentos.  

#### **7. Documentação e Ferramentas**  
- **Saída**:  
  - **Diagrama Entidade-Relacionamento (DER)** visualizando entidades, atributos e relacionamentos.  
  - **Ferramentas Sugeridas**:  
    - **ERwin**: Para modelos conceituais e físicos.  
    - **Lucidchart**: Colaboração em nuvem.  
    - **Microsoft Visio**: Diagramas intuitivos.  

---

### **Resumo das Etapas**:  
1. Delimitar o **Minimundo**.  
2. Coletar **Requisitos**.  
3. Identificar **Entidades** (fortes/fracas).  
4. Definir **Relacionamentos** e cardinalidades.  
5. Especificar **Atributos** (tipos e chaves).  
6. Validar com **Regras de Negócio**.  
7. Documentar em **DER** usando ferramentas adequadas.  

**Resultado**: Modelo conceitual pronto para implementação em banco de dados físico.


## **Processo de Modelagem de Dados: Desenvolvimento do Modelo Lógico**  

#### **Objetivo**:  
Transformar o modelo conceitual em uma estrutura lógica detalhada, definindo entidades, atributos, relacionamentos, chaves e regras de integridade, preparando-o para implementação física em um SGBD.  

---

### **Etapas do Processo**:  

#### **1. Mapeamento do Modelo Conceitual para o Modelo Lógico**  
- **Objetivo**: Converter entidades, atributos e relacionamentos do DER conceitual em tabelas, colunas e chaves.  
- **Ações**:  
  - **Entidades → Tabelas**: Cada entidade vira uma tabela (ex.: "Cliente" → Tabela `CLIENTE`).  
  - **Atributos → Colunas**: Atributos viram colunas (ex.: "Nome do Cliente" → Coluna `NOME_CLIENTE`).  
  - **Relacionamentos → Chaves Estrangeiras**: Relações viram FK (ex.: "Cliente possui Conta" → `ID_CLIENTE` em `CONTA`).  

#### **2. Definição de Chaves Primárias (PK)**  
- **Objetivo**: Identificar atributos que garantam unicidade em cada tabela.  
- **Critérios**:  
  - **Unicidade**: Valores únicos (ex.: `ID_CLIENTE` como PK autoincrementável).  
  - **Não Nulo**: PK não pode ser nula.  
- **Exemplo**:  
  ```sql
  CREATE TABLE CLIENTE (
      ID_CLIENTE INT PRIMARY KEY,
      NOME_CLIENTE VARCHAR(100),
      CPF_CLIENTE VARCHAR(11) UNIQUE
  );
  ```

#### **3. Estabelecimento de Chaves Estrangeiras (FK)**  
- **Objetivo**: Implementar relacionamentos via referências entre tabelas.  
- **Regras**:  
  - **Integridade Referencial**: FK deve referenciar uma PK existente.  
  - **Cardinalidade**: Refletir 1:1, 1:N ou N:M (com tabela associativa).  
- **Exemplo**:  
  ```sql
  CREATE TABLE CONTA (
      ID_CONTA INT PRIMARY KEY,
      NUMERO_CONTA VARCHAR(20),
      ID_CLIENTE INT,
      FOREIGN KEY (ID_CLIENTE) REFERENCES CLIENTE(ID_CLIENTE)
  );
  ```

#### **4. Normalização (Opcional, mas Recomendada)**  
- **Objetivo**: Eliminar redundâncias e anomalias.  
- **Formas Normais**:  
  - **1FN**: Atributos atômicos (sem multivalorados).  
  - **2FN**: Dependência total da PK.  
  - **3FN**: Sem dependências transitivas.  

#### **5. Documentação no Dicionário de Dados**  
- **Objetivo**: Registrar metadados para clareza e manutenção.  
- **Conteúdo**:  
  - **Tabelas**: Nome, descrição.  
  - **Colunas**: Nome, tipo, tamanho, obrigatoriedade, FK/PK.  
  - **Exemplo**:  
    | Tabela   | Coluna        | Tipo    | Descrição               |  
    |----------|---------------|---------|-------------------------|  
    | CLIENTE  | ID_CLIENTE    | INT     | Chave primária.         |  

#### **6. Validação com Regras de Negócio**  
- **Objetivo**: Garantir aderência aos requisitos.  
- **Verificações**:  
  - Cardinalidades (ex.: "Um cliente pode ter N contas").  
  - Restrições (ex.: "Saldo da conta não pode ser negativo").  

#### **7. Adaptação para Ferramentas (PowerBI, Planilhas, NoSQL)**  
- **Objetivo**: Preparar o modelo para uso em diferentes ambientes.  
- **Ações**:  
  - **PowerBI**: Mapear tabelas como "Entidades" no modelo de dados.  
  - **Planilhas**: Simular PK/FK com colunas de referência.  
  - **NoSQL**: Converter tabelas em documentos (ex.: JSON para MongoDB).  

---

### **Resumo das Etapas**:  
1. **Mapear** entidades/atributos para tabelas/colunas.  
2. **Definir PKs** para unicidade.  
3. **Estabelecer FKs** para relacionamentos.  
4. **Normalizar** (opcional).  
5. **Documentar** no dicionário de dados.  
6. **Validar** com regras de negócio.  
7. **Adaptar** para ferramentas específicas (SQL, PowerBI, etc.).  

**Saída**: Modelo lógico pronto para implementação física em um SGBD ou integração com ferramentas de análise.  

---

### **Ferramentas Recomendadas**:  
- **Diagramação**: ERwin, Lucidchart, MySQL Workbench.  
- **Documentação**: Excel (Dicionário de Dados), Confluence.  
- **Implementação**: SQL (MySQL, PostgreSQL), NoSQL (MongoDB).  

**Observação**: O modelo lógico deve ser revisado iterativamente com stakeholders para garantir alinhamento com as necessidades do negócio.

## **Processo de Modelagem de Dados: Construção do Modelo Físico**  

#### **Objetivo**:  
Transformar o modelo lógico em uma estrutura implementável em um SGBD, otimizando desempenho, integridade e segurança dos dados.  

---

### **Etapas do Processo**:  

#### **1. Preparação do Modelo Físico**  
- **Objetivo**: Adaptar o modelo lógico ao SGBD escolhido (ex.: MySQL, PostgreSQL).  
- **Ações**:  
  - **Revisão do Modelo Lógico**: Verificar entidades, atributos, relacionamentos e regras de negócio.  
  - **Escolha de Tipos de Dados**: Definir tipos de colunas conforme características do SGBD (ex.: `INT` para números, `VARCHAR(255)` para textos).  
  - **Normalização**: Aplicar 3FN (Forma Normal) para eliminar redundâncias.  

#### **2. Definição de Estruturas de Armazenamento**  
- **Objetivo**: Criar tabelas, chaves e índices.  
- **Ações**:  
  - **Criação de Tabelas**: Converter entidades em tabelas (ex.: `CLIENTE(id_cliente, nome)`).  
  - **Chaves Primárias (PK)**: Definir identificadores únicos (ex.: `id_cliente INT PRIMARY KEY`).  
  - **Chaves Estrangeiras (FK)**: Implementar relacionamentos (ex.: `id_cliente INT REFERENCES CLIENTE(id_cliente)`).  
  - **Índices**: Criar índices para colunas frequentemente consultadas (ex.: `CREATE INDEX idx_nome ON CLIENTE(nome)`).  

#### **3. Implementação de Restrições**  
- **Objetivo**: Garantir integridade e segurança.  
- **Ações**:  
  - **Restrições de Domínio**: `NOT NULL`, `UNIQUE`, `CHECK` (ex.: `saldo DECIMAL(10,2) CHECK (saldo >= 0)`).  
  - **Integridade Referencial**: `ON DELETE CASCADE` para manter consistência em exclusões.  
  - **Segurança**: Definir permissões de acesso a tabelas.  

#### **4. Otimização de Desempenho**  
- **Objetivo**: Melhorar eficiência de consultas e operações.  
- **Ações**:  
  - **Escolha de Storage Engine**: Ex.: InnoDB (transações ACID) vs. MyISAM (leitura rápida).  
  - **Particionamento**: Dividir tabelas grandes (ex.: por datas).  
  - **Caching**: Configurar buffers e caches no SGBD.  

#### **5. Documentação**  
- **Objetivo**: Registrar metadados para manutenção futura.  
- **Ações**:  
  - **Dicionário de Dados**: Descrever tabelas, colunas, tipos, restrições.  
  - **Diagrama Físico**: Gerar diagrama com ferramentas como MySQL Workbench ou ERwin.  

#### **6. Migração para o SGBD**  
- **Objetivo**: Implementar o modelo no ambiente de produção.  
- **Ações**:  
  - **Scripts SQL**: Gerar e executar scripts de criação (`CREATE TABLE`, `ALTER TABLE`).  
  - **Validação**: Testar estrutura com dados reais.  

#### **7. Testes e Ajustes**  
- **Objetivo**: Garantir funcionalidade e desempenho.  
- **Ações**:  
  - **Testes de Carga**: Simular alto volume de operações.  
  - **Monitoramento**: Identificar gargalos com ferramentas como `EXPLAIN` (MySQL).  
  - **Ajustes**: Refinar índices, tipos de dados ou esquema conforme resultados.  

#### **8. Manutenção Contínua**  
- **Objetivo**: Adaptar o modelo a novas necessidades.  
- **Ações**:  
  - **Backups**: Implementar rotinas de backup.  
  - **Atualizações**: Revisar modelo periodicamente para escalabilidade.  

---

### **Resumo das Etapas**:  
1. **Preparar** o modelo físico com base no SGBD.  
2. **Definir** tabelas, PKs, FKs e índices.  
3. **Implementar** restrições de integridade e segurança.  
4. **Otimizar** desempenho com storage engines e particionamento.  
5. **Documentar** estrutura e regras.  
6. **Migrar** para o SGBD via scripts SQL.  
7. **Testar** e ajustar com dados reais.  
8. **Manter** o modelo atualizado.  

---

### **Ferramentas Recomendadas**:  
- **Modelagem**: MySQL Workbench, ERwin, Lucidchart.  
- **SGBDs**: MySQL, PostgreSQL, Oracle.  
- **Monitoramento**: Prometheus, Grafana.  

**Saída**: Banco de dados físico funcional, otimizado e alinhado aos requisitos de negócio.  

---

### **Exemplo Prático (MySQL)**:  
```sql
-- Criação da tabela CLIENTE
CREATE TABLE CLIENTE (
    id_cliente INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    cpf VARCHAR(11) UNIQUE
);

-- Criação da tabela CONTA com FK
CREATE TABLE CONTA (
    id_conta INT PRIMARY KEY,
    numero_conta VARCHAR(20) UNIQUE,
    id_cliente INT,
    saldo DECIMAL(10,2) DEFAULT 0.00,
    FOREIGN KEY (id_cliente) REFERENCES CLIENTE(id_cliente)
);

-- Criação de índice para busca rápida
CREATE INDEX idx_cliente_nome ON CLIENTE(nome);
```  

**Observação**: O modelo físico deve ser revisado iterativamente com stakeholders para garantir aderência às necessidades do negócio.