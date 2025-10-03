# ⚙️ 001 - Workflows de Backup e Monitoramento

Esta pasta contém dois **Workflows** principais desenvolvidos no **n8n** projetados para automatizar a **segurança** e o **monitoramento** de seus sistemas e da própria plataforma n8n.

O foco é garantir a integridade dos seus workflows e a estabilidade de sua infraestrutura (VPS).

---

## ⚠️ Nota Importante sobre a Versão do n8n

Estes workflows foram desenvolvidos e testados na versão **1.113.3** do **n8n**.

**O que isso significa para você?**

Se você estiver utilizando uma versão do n8n significativamente **diferente** (mais antiga ou muito mais recente), pode haver a possibilidade de ocorrerem **conflitos** ou **incompatibilidades** na importação. Isso pode afetar a configuração de nós (nodes), a estrutura de expressões ou o funcionamento de novos recursos.

**Recomendação:** Tente utilizar a versão **1.113.3** para garantir a compatibilidade máxima.

---

## 🎯 Conteúdo dos Workflows

Esta pasta contém os seguintes arquivos JSON:

### 1. 💾 `Backup Workflows.json`

Este é um workflow de **Backup Centralizado**.

* **Função:** Automatiza a busca de todos os workflows ativos na sua instância do n8n, converte cada um em um arquivo JSON e salva esse arquivo em uma pasta específica do **Google Drive**.
* **Lógica Inteligente:** Ele verifica se o arquivo de backup já existe no Drive. Se **SIM**, ele **atualiza** o arquivo. Se **NÃO** (ou se a pasta diária é nova), ele **cria** um novo arquivo.
* **Uso Duplo:** Pode ser acionado por agendamento (`scheduleTrigger`) para backup diário de todos os workflows, **OU** pode ser chamado por outro workflow (através do node `Execute Workflow`) para realizar um backup imediato.

### 2. 🖥️ `Monitor VPS.json`

Este é um workflow de **Monitoramento de Servidor VPS**.

* **Função:** Executa comandos direto no Host do servidor (`Execute Command`) para monitorar métricas críticas de um Servidor Privado Virtual (VPS).
* **Métricas Monitoradas:**
    * **Uso da CPU**
    * **Uso da Memória (RAM)**
    * **Uso do Armazenamento (Disco)**
* **Sistema de Alerta:** Possui regras embutidas (`Code` e `Filter` nodes) que disparam alertas se as métricas ultrapassarem limites pré-definidos (ex: CPU > 80%, Storage > 80%).
* **Canais de Notificação:** Em caso de alerta, envia notificações via **E-mail (Gmail)** e **WhatsApp (API Evolution)**.

---

## 🛠️ Configurações Necessárias

Ambos os workflows requerem ajustes antes de serem ativados:

### 1. Configuração do Workflow de Backup (`Backup Workflows.json`)

| Node | Credencial | Campo a Alterar | Instrução |
| :--- | :--- | :--- | :--- |
| **`pega_filtra_workflows`** | `n8n API` | Credencial | Configurar uma chave API do n8n para que o workflow possa ler outros workflows. |
| **`getFolder`** | `Google Drive` | `Folder ID` no campo `Filter` | Cole o **ID da pasta principal** do seu Google Drive onde os backups diários serão salvos. |
| **`createFolder`** | `Google Drive` | `Parent Folder` | Cole o **ID da pasta principal** do seu Google Drive (o mesmo do item acima). |
| **`chama_backup`** | (N/A) | `Workflow ID` | **Importante:** Altere para o **ID deste próprio workflow de Backup**. Isso permite que ele seja chamado como um *hook* de backup imediato. |

### 2. Configuração do Workflow de Monitoramento (`Monitor VPS.json`)

| Node | Credencial | Campo a Alterar | Instrução |
| :--- | :--- | :--- | :--- |
| **`send_text`** | (N/A) | `URL` e `Body` | Preencha com sua **Instância, API Key e Número de Destino** da API Evolution (WhatsApp). |
| **`send_email`** | `Gmail` | Credencial e `Send To` | Configure suas credenciais do Gmail e o e-mail de destino. |
| **`chama_backup`** | (N/A) | `Workflow ID` | Preencha com o **ID do seu workflow de Backup** (o `Backup Workflows.json` que você configurou acima). |

---

## 💡 Recursos Extras (Auto-Backup em Save)

Os workflows contêm os nós **`escuta_atualizacao`** (n8n Trigger) e **`chama_backup`** (Execute Workflow).

* Você pode copiar e colar estes dois nós em **QUALQUER** outro workflow da sua instância.
* Ao fazer isso e manter o workflow **ATIVO**, um backup dele será automaticamente executado e salvo no seu Google Drive toda vez que você clicar em **Salvar**.

## 👨‍💻 Autor

<div align="center">
  <img src="https://avatars.githubusercontent.com/u/190038668?v=4" width="100" height="100" alt="Vinicius Sousa" style="border-radius: 50%;">
  
  **Vinicius Sousa**  
  *Desenvolvedor & Especialista em Automações*
  
  [![GitHub](https://img.shields.io/badge/GitHub-vinidevai-black?style=flat-square&logo=github)](https://github.com/vinidevai)