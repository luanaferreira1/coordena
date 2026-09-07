# Product Requirements Document (PRD)

# Coordena

## Plataforma Web & Mobile (PWA) de Gestão e Acompanhamento Pedagógico e Físico Infantil

---

### 1. Visão Geral e Contexto de Negócio

#### 1.1 Objetivo do Projeto

Desenvolver uma aplicação web responsiva com abordagem estritamente **Mobile-First e preparada para PWA (Progressive Web App)**. O foco central de uso é o smartphone do educador físico diretamente no ambiente de treino (quadra, campo ou parque), permitindo a instalação de um atalho na tela inicial ("Adicionar à Tela Inicial") para funcionar com visual e agilidade de aplicativo nativo, sem necessidade de publicação em lojas de apps.

#### 1.2 Problema a Resolver

Profissionais de educação física e preparação infantil frequentemente utilizam pranchetas de papel, anotações descentralizadas no WhatsApp e planilhas desconexas. Em ambiente de treino prático (beira de quadra), um notebook é inviável, tornando indispensável que toda a operação ocorra com velocidade através do celular:

1. Registrar chamadas e ocorrências com apenas um toque na tela enquanto monitora as crianças.
2. Consultar fichas médicas e contatos de emergência/WhatsApp com 1 clique pelo celular.
3. Visualizar gráficos de desempenho e enviar relatórios em PDF diretamente via WhatsApp pelo smartphone.

#### 1.3 Impacto Social e Extensão Universitária

A plataforma organiza a rotina do educador, otimiza o planejamento pedagógico e gera dados palpáveis para que as crianças recebam estímulos direcionados às suas reais necessidades. Além disso, fortalece a comunicação com as famílias através de boletins de desenvolvimento exportáveis.

- **Carga Horária Estimada:** 160 horas.

---

### 2. Personas e Níveis de Acesso (RBAC)

1. **Administrador / Preparador Físico Responsável:**
   - Acesso irrestrito a todas as turmas, alunos, relatórios, configurações e cadastro de outros instrutores.
2. **Instrutor / Assistente:**
   - Acesso às turmas sob sua responsabilidade para realizar chamada, lançar avaliações motoras e visualizar fichas cadastrais.
3. **Responsável do Aluno (_Escopo Futuro / Fase 2_):**
   - Visualização restrita apenas ao boletim e histórico de frequência do seu dependente (via link público assinado ou login futuro).

---

### 3. Stack Tecnológica e Arquitetura

```
+-------------------------------------------------------------+
|                 Frontend: React.js (Vite)                   |
|  Tailwind CSS + shadcn/ui + Lucide Icons + Recharts + jsPDF |
+------------------------------+------------------------------+
                               | REST API / JSON
+------------------------------v------------------------------+
|                 Backend: Node.js (TypeScript)               |
|                 Express.js / Fastify + Zod                  |
|                 Autenticação JWT + Bcrypt                   |
+------------------------------+------------------------------+
                               | Prisma ORM
+------------------------------v------------------------------+
|             Banco de Dados: PostgreSQL                      |
|             (Hospedado no Supabase - Free Tier)             |
+-------------------------------------------------------------+
```

- **Frontend:** React 18+ (Vite, TypeScript), Tailwind CSS, componentes acessíveis via `shadcn/ui`, ícones via `lucide-react`, gráficos via `recharts`, geração de PDF client-side via `jspdf` / `html2canvas`.
- **Backend:** Node.js (TypeScript) com Express ou Fastify, validação de payload com `Zod`, autenticação com `jsonwebtoken` e hash de senhas com `bcryptjs`.
- **Banco de Dados & ORM:** PostgreSQL hospedado no **Supabase** com **Prisma ORM** (utilizando connection pooler/Supavisor para conexões eficientes).

---

### 4. Requisitos Funcionais Detalhados

#### RF01: Autenticação & Gestão de Acessos

- Login com e-mail e senha.
- Criação de conta do Administrador via seed/setup inicial.
- Administrador pode convidar/cadastrar novos instrutores (RBAC).

#### RF02: Gestão Flexível de Turmas & Locais de Atuação

- **Suporte a Múltiplos Locais/Escolas:** Como o educador atua como prestador em diferentes instituições (ex: escolas, clubes ou academias), o sistema permite cadastrar ou associar a turma a uma **Escola/Local**.
- **Organização Dinâmica e Customizável:** O critério de organização da turma **não é fixo**, podendo ser configurado pelo próprio professor:
  - _Por Nível de Habilidade (Ex: Nível 1 - Introdução técnica, Nível 2 - Desenvolvimento técnico, Nível 3 - Aperfeiçoamento, Nível 4 - Formação esportiva)._
  - _Por Faixa Etária (Ex: Sub-7, Sub-9, 4 a 6 anos)._
  - _Por Categoria Livre/Horário (Ex: Iniciantes Manhã, Turma Avançada)._
- Filtro rápido na tela inicial por Escola/Instituição para facilitar o acesso do professor quando chegar em cada local de trabalho.

#### RF03: Gestão de Alunos

- Cadastro de Aluno: Nome completo, data de nascimento (com cálculo automático da idade atual), nome e telefone/WhatsApp do responsável, data de matrícula e turma associada.
- **Ficha de Saúde:** Campo de texto enriquecido para anotação de restrições médicas, alergias e observações de saúde.
- Busca rápida e filtros por turma e status (ativo/inativo).

#### RF04: Diário de Classe & Chamada Rápida (Mobile-First)

- Criação instantânea de aula para a data atual com 1 clique.
- Lista de chamada em formato de cartões ou linhas touch-friendly:
  - Botões diretos: `[ Presente ]` (Verde), `[ Ausente ]` (Vermelho), `[ Justificada ]` (Amarelo).
  - Modal/Gaveta para inserir observação rápida individual por aluno (ex: "Sentiu cansaço excessivo").
- Botão de "Marcar Todos como Presentes" para acelerar o processo em quadra.

#### RF05: Avaliação Contínua e Painel de Desempenho

- Registro de notas de 1 a 5 (ou 1 a 10) para os 5 pilares do desenvolvimento infantil:
  1. Coordenação Motora
  2. Agilidade & Velocidade
  3. Equilíbrio
  4. Flexibilidade
  5. Cooperação & Disciplina
- Campo para parecer pedagógico/físico do educador.
- **Gráficos Visuais (Recharts):**
  - **Gráfico de Radar:** Mapa de competências atuais da criança (identificando pontos fortes e pontos a desenvolver).
  - **Gráfico de Linhas/Tendência:** Evolução histórica das notas ao longo das semanas/meses.

#### RF06: Geração e Exportação de Relatórios PDF (com Envio Mobile)

- **Boletim Individual do Aluno:**
  - Cabeçalho profissional com nome da escola/treinador, dados do aluno e período.
  - Resumo de assiduidade (% de presença nas aulas).
  - Gráfico de radar ou tabela comparativa de evolução motora.
  - Parecer descritivo final do educador físico.
  - Layout limpo pronto para envio no WhatsApp dos pais ou impressão em folha A4.
  - **Integração Mobile (Web Share API):** Botão "Compartilhar no WhatsApp" que abre diretamente o app do WhatsApp no celular com o PDF/link pronto para o contato do responsável.
- **Relatório Geral da Turma:**
  - Lista de todos os alunos da turma com percentual de faltas e presenças no mês.

---

### 5. Requisitos Não Funcionais & Diretrizes de UX Mobile

- **RNF01 (Mobile-First & PWA):**
  - Layout otimizado primariamente para telas de 360px a 430px (smartphones comuns).
  - Navegação inferior estilo aplicativo (**Bottom Navigation Bar**) com ícones para: _Início_, _Turmas_, _Chamada Rápida_ e _Relatórios_.
  - Suporte a **PWA (Manifest.json + Service Worker)** permitindo "Adicionar à Tela de Início" para tela cheia sem barras do navegador.
- **RNF02 (Touch Friendly para Beira de Quadra):**
  - Áreas de toque generosas (mínimo de 48x48px) para botões de presença (`Presente`, `Falta`, `Justificada`), permitindo marcação rápida com uma mão só.
  - Alto contraste de cores para facilitar a leitura sob sol forte.
- **RNF03 (Feedback Otimista e Resiliência de Rede):**
  - Chamada com salvamento em tempo real (autosave) para garantir que oscilações de 4G/Wi-Fi em áreas abertas não façam o professor perder os dados da aula.
- **RNF04 (Segurança & LGPD):**
  - Dados de menores (saúde, nomes de responsáveis e números de WhatsApp) protegidos por autenticação com tokens JWT.
