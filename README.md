# n8n-cobrancas-inteligentes

![Branch Protection](https://img.shields.io/badge/branch%20protection-active-success)

Automação financeira inteligente com n8n, Google Sheets e Gmail.

---

## 📋 Objetivo do Projeto

Projetar e estruturar um fluxo de trabalho automatizado para o envio de faturas e cobranças vencidas, garantindo alta confiabilidade, tratamento adequado de exceções e eficiência operacional para a equipe financeira.

---

## 🛠️ Ferramentas e Escopo

**Público responsável:** Equipe financeira e de atendimento ao cliente.

**Ferramentas envolvidas:**
- Google Sheets
- n8n
- Gmail API

**Fluxo operacional:**
- Leitura centralizada de dados de clientes e faturas armazenados no Google Sheets.
- Consolidação e filtragem inteligente das faturas com base na data de vencimento e status de inadimplência.
- Formatação dinâmica de mensagens em HTML e disparo automatizado de e-mails de cobrança via Gmail para os clientes elegíveis.

---

## ⚠️ Regras e Restrições

- **Exclusão de faturamento automático:** pagamentos automáticos via cartão de crédito são isentos do envio preventivo, notificando estritamente faturas em atraso real.
- **Validação de dados:** registros sem e-mail válido são ignorados e filtrados antes de qualquer tentativa de disparo.

---

## 📂 Arquivos do projeto

- `n8n-cobrancas-inteligentes.pdf` — documentação técnica completa do projeto, incluindo o prompt estruturado utilizado para orientar a arquitetura do workflow n8n.

---

## Contexto do projeto

Desafio Criativo desenvolvido na plataforma DIO (Digital Innovation One).

---

## 📞 Contato

- **E-mail:** [ornelas.tozato@gmail.com](mailto:ornelas.tozato@gmail.com)
- **LinkedIn:** [Rafael Ornelas Tozato](https://www.linkedin.com/in/rafaeltozato81)
- **Medium:** [Rafael Ornelas Tozato](https://medium.com/@ornelas.tozato)
- **GitHub:** [Rafael-TOZATO](https://github.com/Rafael-TOZATO)
