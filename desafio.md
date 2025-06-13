# Modelagem de Dados: Sistema de Gerenciamento de Buscas de Trabalho

Adaptando a estrutura didática do projeto de conferências científicas para criar um modelo de dados para seu sistema de gerenciamento de buscas de trabalho. Seguirei as mesmas etapas: identificação de entidades, definição de relacionamentos e especificação de atributos.

---

## 1. Mão na massa: sistema de gerenciamento de buscas de trabalho

Você está desenvolvendo um modelo conceitual para um sistema que gerencia suas buscas por oportunidades de trabalho. Este sistema deve ser capaz de acompanhar empresas, vagas, aplicações, contatos e processos seletivos.

### Levantamento de Requisitos

Para desenvolver um sistema eficiente, precisamos capturar os seguintes aspectos:

* Cada **oportunidade/vaga** tem um título, descrição, requisitos, nível de senioridade, tipo (CLT, PJ, estágio etc.), data de publicação e status (ativa, inativa)
* As **empresas** possuem nome, setor/área de atuação, porte, localização e informações de contato
* Você pode se **candidatar** a vagas, registrando data da aplicação, método (LinkedIn, site da empresa, etc.), status (enviada, em processo, rejeitada, etc.) e feedbacks
* Os **contatos** são pessoas específicas em empresas que podem ajudar no processo (recrutadores, gestores, etc.), com informações como nome, cargo, como conheceu e detalhes de contato
* Os **processos seletivos** podem ter múltiplas etapas (entrevistas, testes, etc.), cada uma com data, tipo, resultado e feedback
* Você pode armazenar **materiais** como currículos, portfólios e cartas de apresentação adaptadas para diferentes oportunidades.

---

## 2. Etapa 1: Identificação das Entidades

Baseado nos requisitos, identifiquei as seguintes entidades principais:

1. **Vaga** - As oportunidades de trabalho que você está acompanhando
2. **Empresa** - Organizações que publicam as vagas
3. **Aplicação** - Seus registros de candidatura a vagas
4. **Contato** - Pessoas relevantes nas empresas
5. **ProcessoSeletivo** - Etapas dos processos seletivos
6. **Material** - Documentos usados nas aplicações
7. **EtapaProcesso** - Componentes individuais de um processo seletivo

---

## 3. Etapa 2: Definição dos Relacionamentos

Agora vamos estabelecer como essas entidades se relacionam:

1. **Empresa - Vaga** (1:N): Uma empresa pode publicar várias vagas
2. **Vaga - Aplicação** (1:N): Uma vaga pode ter várias aplicações (se você se candidatar múltiplas vezes)
3. **Aplicação - ProcessoSeletivo** (1:1 ou 1:N): Cada aplicação pode ter um ou mais processos seletivos
4. **ProcessoSeletivo - EtapaProcesso** (1:N): Um processo tem múltiplas etapas
5. **Empresa - Contato** (1:N): Uma empresa pode ter vários contatos
6. **Contato - ProcessoSeletivo** (N:M): Um contato pode estar envolvido em vários processos e vice-versa
7. **Aplicação - Material** (N:M): Você pode usar os mesmos materiais em várias aplicações

---

## 4. Etapa 3: Especificação dos Atributos

Para cada entidade, definiremos os atributos necessários:

### Vaga
- id_vaga (PK)
- titulo
- descricao
- requisitos
- senioridade (Júnior, Pleno, Sênior)
- tipo_contrato (CLT, PJ, Estágio, etc.)
- data_publicacao
- status (Ativa, Inativa, Preenchida)
- id_empresa (FK)

### Empresa
- id_empresa (PK)
- nome
- setor
- porte
- localizacao
- site
- nota (sua avaliação da empresa)

### Aplicação
- id_aplicacao (PK)
- data_aplicacao
- metodo_aplicacao
- status (Enviada, Em processo, Rejeitada, etc.)
- feedback
- id_vaga (FK)
- id_material (FK)

### Contato
- id_contato (PK)
- nome
- cargo
- email
- telefone
- como_conheceu
- observacoes
- id_empresa (FK)

### ProcessoSeletivo
- id_processo (PK)
- status (Ativo, Concluído, etc.)
- resultado (Aprovado, Reprovado, etc.)
- feedback_geral
- id_aplicacao (FK)

### EtapaProcesso
- id_etapa (PK)
- tipo (Entrevista, Teste, etc.)
- data
- descricao
- resultado
- feedback
- id_processo (FK)

### Material
- id_material (PK)
- tipo (Currículo, Portfólio, Carta, etc.)
- nome_arquivo
- data_criacao
- data_ultima_atualizacao
- versao

---

## 5. Próximos Passos (Modelo Lógico e Físico)

Seguindo a estrutura didática do projeto original, os próximos passos seriam:

1. **Criar um dicionário de dados** detalhando cada entidade e atributo
2. **Transformar o modelo conceitual em lógico** com padronização de nomes
3. **Definir tipos de dados e tamanhos** para cada coluna
4. **Implementar as chaves primárias e estrangeiras**
5. **Representar graficamente os relacionamentos** com cardinalidades
6. **Converter para modelo físico** em um SGBD como MySQL

---

Quer que eu detalhe alguma parte específica deste modelo? Ou gostaria de ajustar alguma entidade ou relacionamento para melhor atender suas necessidades de busca por oportunidades?
