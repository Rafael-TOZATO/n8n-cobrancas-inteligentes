n8n-cobrancas-inteligentes

"Branch Protection" (https://img.shields.io/badge/branch%20protection-active-success)

Automação financeira inteligente com n8n, Google Sheets e Gmail.

---

📋 Objetivo do Projeto

Projetar e estruturar um fluxo de trabalho automatizado para o envio de faturas e cobranças vencidas, garantindo confiabilidade, tratamento adequado de exceções e eficiência operacional para a equipe financeira.

O workflow realiza a leitura das faturas, aplica regras de validação e elegibilidade, gera uma mensagem de cobrança em HTML, envia o e-mail ao cliente e registra o envio na planilha.

---

🛠️ Ferramentas e Escopo

Público responsável: Equipe financeira e de atendimento ao cliente.

Ferramentas envolvidas:

- Google Sheets
- n8n
- Gmail API

---

🔄 Fluxo de Automação

O processo é executado automaticamente todos os dias às 08:00.

Schedule Trigger
        ↓
Google Sheets - Ler Faturas
        ↓
Filter - Email valido
        ↓
Filter - Sem pagamento automatico
        ↓
Filter - Faturas vencidas
        ↓
Set - Variaveis da mensagem
        ↓
Code - Template HTML
        ↓
Gmail - Enviar cobranca
        ↓
Google Sheets - Marcar enviado

Etapas do processo

1. Schedule Trigger
   Inicia automaticamente o workflow diariamente às 08:00.

2. Google Sheets - Ler Faturas
   Consulta a aba "Faturas" da planilha configurada.

3. Filter - Email valido
   Verifica se o registro possui um endereço de e-mail válido. Registros sem e-mail válido são descartados.

4. Filter - Sem pagamento automatico
   Exclui registros cuja forma de pagamento seja "cartao_automatico".

5. Filter - Faturas vencidas
   Seleciona somente faturas com data de vencimento anterior à data atual e com status de pagamento "inadimplente".

6. Set - Variaveis da mensagem
   Prepara os dados utilizados na comunicação, incluindo nome do cliente, quantidade de dias de atraso e valor formatado da fatura.

7. Code - Template HTML
   Gera o conteúdo HTML utilizado na mensagem de cobrança.

8. Gmail - Enviar cobranca
   Envia automaticamente o e-mail para o endereço do cliente elegível.

9. Google Sheets - Marcar enviado
   Atualiza o registro na planilha, utilizando "status_cobranca = enviado".

---

📊 Estrutura Esperada da Planilha

A aba utilizada pelo workflow é:

Faturas

Os campos utilizados pelo processo são:

Campo| Finalidade
"nome"| Nome do cliente
"email"| Endereço para envio da cobrança
"forma_pagamento"| Identificação da forma de pagamento
"data_vencimento"| Data de vencimento da fatura
"status_pagamento"| Situação do pagamento
"valor_fatura"| Valor da fatura
"status_cobranca"| Registro do processamento da cobrança

---

⚠️ Regras e Restrições

Validação de e-mail

Registros sem endereço de e-mail válido são filtrados antes do envio.

Pagamento automático

Registros com:

forma_pagamento = cartao_automatico

não são encaminhados para cobrança.

Inadimplência

O envio é direcionado somente para registros que atendam simultaneamente às condições:

data_vencimento < data atual
status_pagamento = inadimplente

Registro do envio

Após o processamento da cobrança, o workflow atualiza:

status_cobranca = enviado

---

📂 Arquivos do Projeto

- "workflow-cobrancas-inteligentes.json" — workflow do n8n para importação e configuração.
- "n8n-cobrancas-inteligentes.pdf" — documentação técnica do projeto, incluindo o prompt estruturado utilizado para orientar a arquitetura do workflow n8n.
- "README.md" — documentação e instruções gerais do projeto.

---

⚙️ Configuração

Antes da execução, é necessário configurar as credenciais e os parâmetros utilizados pelo workflow.

Google Sheets

Configurar:

- credencial de acesso ao Google Sheets;
- ID da planilha;
- aba "Faturas".

O workflow contém um placeholder para o ID da planilha:

SUBSTITUIR_PELO_ID_DA_PLANILHA

Esse valor deve ser substituído pelo ID correspondente à planilha utilizada.

Gmail

Configurar a credencial necessária para o envio das mensagens por meio do Gmail.

Segurança

O arquivo JSON do workflow não deve conter tokens, senhas ou chaves privadas. As credenciais devem ser configuradas diretamente no ambiente n8n.

---

📥 Importação do Workflow

Para utilizar o fluxo:

1. Abra o n8n.
2. Crie ou abra um projeto.
3. Utilize a opção de importação de workflow.
4. Selecione o arquivo:

workflow-cobrancas-inteligentes.json

5. Configure as credenciais do Google Sheets e Gmail.
6. Substitua o placeholder pelo ID da planilha.
7. Confirme a existência da aba "Faturas".
8. Valide os campos utilizados pelo workflow.
9. Execute testes antes de habilitar a execução automática.

---

🧪 Validação

Antes de colocar o processo em produção, recomenda-se testar individualmente:

- leitura da planilha;
- validação dos endereços de e-mail;
- exclusão dos pagamentos automáticos;
- identificação das faturas inadimplentes;
- cálculo dos dias de atraso;
- formatação do valor da fatura;
- geração do HTML;
- envio pelo Gmail;
- atualização do "status_cobranca".

---

🎯 Resultado Esperado

O workflow automatiza o processo de identificação e comunicação de cobranças vencidas, reduzindo atividades manuais e mantendo o registro do processamento diretamente na base de dados utilizada pelo processo.

A automação foi estruturada para atuar somente sobre os registros que atendem às regras definidas no fluxo.

---

Contexto do Projeto

Desafio Criativo desenvolvido na plataforma DIO (Digital Innovation One).

---

📞 Contato

- E-mail: "ornelas.tozato@gmail.com" (mailto:ornelas.tozato@gmail.com)
- LinkedIn: "Rafael Ornelas Tozato" (https://www.linkedin.com/in/rafaeltozato81)
- Medium: "Rafael Ornelas Tozato" (https://medium.com/@ornelas.tozato)
- GitHub: "Rafael-TOZATO" (https://github.com/Rafael-TOZATO)
