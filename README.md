<p align="center">
  <img src="banner-n8n.jpg" alt="Banner do projeto n8n Cobranças Inteligentes" width="100%">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/n8n-workflow-FF6584?style=for-the-badge&logo=n8n&logoColor=white" alt="n8n">
  <img src="https://img.shields.io/badge/Google%20Sheets-integração-34A853?style=for-the-badge&logo=googlesheets&logoColor=white" alt="Google Sheets">
  <img src="https://img.shields.io/badge/Gmail-integração-EA4335?style=for-the-badge&logo=gmail&logoColor=white" alt="Gmail">
  <img src="https://img.shields.io/badge/Documentação-SKILL.md-2563EB?style=for-the-badge&logo=markdown&logoColor=white" alt="SKILL.md">
</p>

# Cobranças Inteligentes

**Automação de cobranças financeiras com n8n, Google Sheets e Gmail.**

Projeto de automação que organiza a identificação de faturas vencidas, aplica regras de elegibilidade, prepara mensagens de cobrança em HTML, realiza o envio por e-mail e registra o processamento na planilha de origem.

> **Escopo:** o repositório contém um workflow n8n e documentação para configuração, execução, validação e evolução. A skill `SKILL.md` orienta agentes de IA na análise e no trabalho técnico sobre esse processo. Ela não substitui as credenciais, a infraestrutura ou a ativação do workflow no ambiente do usuário.

## 1. Visão geral

O objetivo é reduzir tarefas manuais e padronizar uma rotina de comunicação de contas a receber. O workflow consulta os registros de faturas no Google Sheets e seleciona os registros que atendem às condições configuradas antes de preparar e enviar a mensagem pelo Gmail.

### Objetivos operacionais

- Centralizar a leitura dos dados de clientes e faturas.
- Identificar registros com e-mail sintaticamente válido.
- Excluir registros configurados com pagamento automático por cartão.
- Selecionar faturas com vencimento anterior à data de execução e status de pagamento `inadimplente`.
- Calcular os dias de atraso e formatar o valor em reais.
- Gerar o corpo da mensagem em HTML.
- Enviar a comunicação pelo Gmail.
- Atualizar o campo de status de cobrança na planilha após o envio.

## 2. Arquitetura e tecnologias

| Componente | Responsabilidade |
|---|---|
| n8n | Orquestrar a execução e as integrações do workflow |
| Schedule Trigger | Iniciar a rotina diariamente às 08:00, conforme fuso horário configurado na instância |
| Google Sheets | Fornecer os registros de faturas e receber a atualização de status |
| Gmail | Enviar a mensagem de cobrança ao endereço do registro elegível |
| SKILL.md | Orientar agentes de IA na análise, configuração, validação, homologação e governança do processo |

### Sequência do workflow

1. **Schedule Trigger:** inicia a execução diária às 08:00.
2. **Google Sheets - Ler Faturas:** consulta a aba `Faturas`.
3. **Filter - Email valido:** aplica uma expressão regular para filtrar endereços com formato de e-mail válido.
4. **Filter - Sem pagamento automatico:** exclui registros cujo campo `forma_pagamento` seja `cartao_automatico`.
5. **Filter - Faturas vencidas:** mantém registros cuja data de vencimento seja anterior ao momento da execução e cujo `status_pagamento` seja `inadimplente`.
6. **Set - Variaveis da mensagem:** prepara nome do cliente, dias de atraso e valor formatado em BRL.
7. **Code - Template HTML:** constrói o corpo HTML da mensagem.
8. **Gmail - Enviar cobranca:** envia o e-mail ao cliente elegível.
9. **Google Sheets - Marcar enviado:** atualiza `status_cobranca` para `enviado`, utilizando o e-mail como campo de correspondência.

## 3. Estrutura esperada da planilha

O workflow utiliza uma planilha Google Sheets com uma aba denominada **Faturas**. Os nomes de campos abaixo correspondem às propriedades referenciadas no JSON do workflow.

| Campo | Utilização | Observação |
|---|---|---|
| `nome` | Personalização da mensagem | Nome do cliente |
| `email` | Validação, destinatário e correspondência na atualização | Deve conter endereço de e-mail válido |
| `forma_pagamento` | Regra de exclusão | O valor `cartao_automatico` é excluído |
| `data_vencimento` | Identificação de vencimento e cálculo do atraso | Deve ser reconhecido como data pelo n8n |
| `status_pagamento` | Elegibilidade da cobrança | O valor esperado para seleção é `inadimplente` |
| `valor_fatura` | Exibição do valor na mensagem | Deve ser numérico para a formatação monetária |
| `status_cobranca` | Registro do processamento | Atualizado para `enviado` após o envio |

**Atenção à chave de atualização:** o nó final utiliza `email` como campo de correspondência. Se houver mais de uma fatura para o mesmo endereço, a atualização poderá não identificar exclusivamente o registro pretendido. Para operação com múltiplas faturas por cliente, recomenda-se adotar um identificador único de fatura e utilizá-lo na atualização.

## 4. Regras de negócio implementadas

- O e-mail passa por filtro de formato antes do envio.
- Registros com `forma_pagamento` igual a `cartao_automatico` são excluídos.
- A data de vencimento deve ser anterior ao momento da execução.
- O status de pagamento deve ser exatamente `inadimplente`.
- O número de dias em atraso é calculado pela diferença entre a data atual e a data de vencimento, em dias inteiros.
- O valor da fatura é formatado como moeda brasileira.
- A mensagem informa o nome do cliente, o valor e os dias de atraso.
- Após o envio, o workflow atualiza `status_cobranca` para `enviado`.

### Limitações conhecidas e controles recomendados

O workflow atual **não consulta `status_cobranca` antes do envio**. Portanto, a atualização para `enviado` não constitui, por si só, uma trava contra reenvios em execuções futuras. Antes de produção, implementar uma condição que exclua registros já processados e avaliar uma estratégia de idempotência baseada em identificador único da fatura.

Também devem ser avaliados: tratamento de falhas entre o envio e a atualização da planilha, política de retentativas, limites de envio do Gmail, tratamento de datas inválidas, proteção contra duplicidade e registro de evidências de execução.

## 5. Estrutura do repositório

```text
n8n-cobrancas-inteligentes/
├── README.md
├── SKILL.md
├── banner-n8n.jpg
├── workflow-cobrancas-inteligentes.json
├── n8n-cobrancas-inteligentes.pdf
└── assets/
    ├── 01-arquitetura-fluxo.png
    ├── 02-painel-kpis.png
    └── 03-governanca-homologacao.png
```

Os arquivos de imagem da pasta `assets/` são materiais ilustrativos e de apresentação. Indicadores, valores e percentuais exibidos em mockups não representam resultados operacionais medidos do workflow.

## 6. Pré-requisitos

- Instância n8n disponível e compatível com os nós utilizados no workflow.
- Conta Google com acesso à planilha de faturas.
- Credencial Google Sheets configurada no n8n.
- Credencial Gmail configurada e autorizada para envio.
- Planilha com aba `Faturas` e os campos definidos na seção 3.
- Dados de teste controlados para homologação.

## 7. Instalação e configuração

### 7.1 Obter o projeto

Clone o repositório:

```bash
git clone https://github.com/Rafael-TOZATO/n8n-cobrancas-inteligentes.git
cd n8n-cobrancas-inteligentes
```

### 7.2 Importar o workflow

1. Acesse sua instância n8n.
2. Utilize a opção de importar workflow a partir de arquivo.
3. Selecione `workflow-cobrancas-inteligentes.json`.
4. Abra o workflow importado e revise os nós e as conexões.

### 7.3 Configurar o Google Sheets

1. Crie ou selecione a planilha de faturas.
2. Garanta que a aba se chame `Faturas`.
3. Confira os nomes das colunas e os tipos de dados.
4. Substitua o valor de exemplo `SUBSTITUIR_PELO_ID_DA_PLANILHA` pelo ID real da planilha nos nós de leitura e atualização.
5. Associe a credencial Google Sheets correspondente nos nós.

### 7.4 Configurar o Gmail

1. Configure a credencial Gmail no n8n.
2. Associe a credencial ao nó `Gmail - Enviar cobranca`.
3. Confira destinatário, assunto e corpo da mensagem.
4. Utilize destinatários controlados durante a homologação.

### 7.5 Revisar o agendamento

O Schedule Trigger está definido para execução diária às 08:00. Confirme o fuso horário da instância n8n e ajuste o agendamento conforme a política operacional da organização.

### 7.6 Ativar com segurança

Mantenha o workflow desativado durante a configuração e os testes. A ativação deve ocorrer somente após a homologação, a revisão das regras de elegibilidade, a validação das credenciais e a implantação dos controles contra reenvio.

## 8. Homologação e plano de testes

Executar os testes em ambiente controlado, com cópia da planilha e destinatários de teste. Não utilizar dados pessoais ou financeiros reais sem autorização e controles adequados.

| ID | Cenário | Resultado esperado |
|---|---|---|
| H01 | E-mail em formato inválido | Registro não segue para envio |
| H02 | Pagamento automático por cartão | Registro é excluído |
| H03 | Fatura com vencimento futuro | Registro não segue para envio |
| H04 | Fatura vencida com status diferente de `inadimplente` | Registro não segue para envio |
| H05 | Fatura vencida e inadimplente, com e-mail válido | Registro segue para preparação e envio de teste |
| H06 | Valor numérico da fatura | Valor aparece formatado em BRL |
| H07 | Data de vencimento válida e anterior à execução | Dias de atraso são calculados |
| H08 | Envio de e-mail concluído | Campo `status_cobranca` é atualizado para `enviado` |
| H09 | Dois registros com o mesmo e-mail | Verificar risco de atualização não exclusiva |
| H10 | Reexecução do workflow com registro já enviado | Confirmar comportamento atual e validar a trava de idempotência após sua implementação |

### Critérios mínimos de aprovação

- Todos os filtros devem apresentar o comportamento esperado.
- Nenhum registro inelegível deve chegar ao nó de envio.
- A mensagem deve apresentar os dados correspondentes à fatura testada.
- A atualização deve atingir o registro correto.
- Falhas devem ser identificáveis nos registros de execução.
- A proteção contra reenvio deve ser implementada e testada antes da produção.

## 9. Segurança, privacidade e governança

- Armazene credenciais exclusivamente no gerenciador de credenciais do n8n.
- Não inclua tokens, senhas, chaves ou dados financeiros reais em arquivos versionados.
- Restrinja o acesso à planilha e à instância de automação por necessidade operacional.
- Utilize dados fictícios ou anonimizados nos testes sempre que possível.
- Avalie finalidade, base legal, retenção, acesso e segurança dos dados pessoais conforme a LGPD.
- Registre alterações no workflow e mantenha versões identificáveis dos artefatos.
- Separe desenvolvimento, homologação e produção.
- Revise logs para evitar exposição desnecessária de dados pessoais.
- Defina responsáveis pela aprovação de mudanças e pela ativação em produção.

## 10. Indicadores para acompanhamento

Os indicadores abaixo são sugestões de monitoramento. O workflow fornecido não implementa um painel analítico nem calcula automaticamente todos esses indicadores.

- Total de faturas avaliadas.
- Quantidade de registros elegíveis.
- Quantidade de e-mails enviados.
- Taxa de falhas de envio.
- Registros excluídos por regra de negócio.
- Registros com dados inválidos.
- Tempo médio de execução.
- Quantidade de possíveis duplicidades.
- Valor nominal das faturas notificadas.

Não interpretar envio de e-mail como confirmação de recebimento, pagamento ou recuperação financeira. Esses resultados exigem dados e integrações adicionais.

## 11. Uso da skill por agentes de IA

O arquivo `SKILL.md` contém instruções para que um agente de IA atue como assistente técnico na análise e evolução do processo de cobranças. Ele organiza o trabalho em etapas, define limites do escopo, estabelece critérios de validação e orienta a documentação de homologação e governança.

Exemplos de solicitações ao agente:

- Analise o workflow e identifique riscos de reenvio.
- Revise as regras de elegibilidade das faturas.
- Elabore um plano de homologação para uma alteração no fluxo.
- Verifique a consistência entre os campos da planilha e os nós do workflow.
- Documente uma mudança técnica e seus critérios de aceite.
- Proponha controles de governança antes da ativação em produção.

A skill fornece orientação e método de trabalho. A execução efetiva depende das ferramentas, permissões, credenciais e ambiente disponibilizados ao agente e ao operador.

## 12. Arquivos e documentação

- `SKILL.md` — instruções operacionais da skill para agentes de IA.
- `workflow-cobrancas-inteligentes.json` — workflow n8n para importação.
- `n8n-cobrancas-inteligentes.pdf` — documentação técnica de referência do projeto.
- `banner-n8n.jpg` — identidade visual original do projeto.
- `assets/` — imagens complementares de arquitetura, indicadores e governança.

## 13. Histórico de versão

| Versão | Descrição |
|---|---|
| 1.0.0 | Estrutura inicial do workflow, documentação técnica e skill operacional. |

## 14. Autor e projeto

**Rafael Ornelas Tozato**  
Especialista em Qualidade e P&D | Automação, Governança e Processos

- GitHub: [Rafael-TOZATO](https://github.com/Rafael-TOZATO)
- Repositório: [n8n-cobrancas-inteligentes](https://github.com/Rafael-TOZATO/n8n-cobrancas-inteligentes)
- LinkedIn: [rafaeltozato81](https://www.linkedin.com/in/rafaeltozato81)
- Medium: [ornelas.tozato](https://medium.com/@ornelas.tozato)
- E-mail: [ornelas.tozato@gmail.com](mailto:ornelas.tozato@gmail.com)

---

<p align="center"><strong>Cobranças Inteligentes</strong><br>Automação financeira com qualidade, rastreabilidade e governança.</p>
