# AI-Powered-Investigation-Bot
Automatically investigate Microsoft Defender alerts using Azure OpenAI and send detailed summaries to Microsoft Teams — all without writing code. Powered by Power Automate, GPT-4, and Microsoft security too


# 🔍 AI-Powered Investigation Bot (No-Code - Azure OpenAI + Power Automate + Teams)

This project connects Microsoft Defender for Endpoint with Azure OpenAI using Power Automate, enabling automated analysis and response for security alerts — all without writing a single line of code.

---

## 🚀 Overview

When a new security alert is triggered in Microsoft Defender for Endpoint, this bot:
1. Collects alert details.
2. Sends them to an Azure OpenAI model for analysis.
3. Parses the AI-generated investigation report.
4. Posts the results in a Microsoft Teams channel.

---

## 🧠 Technology Stack

| Component              | Purpose                              |
|------------------------|--------------------------------------|
| Azure OpenAI Foundry   | Generates the investigation report.  |
| Power Automate         | Orchestrates alert flow and API calls. |
| Microsoft Defender     | Triggers the workflow with new alerts. |
| Microsoft Teams        | Receives the final investigation message. |

---

## ⚙️ Power Automate Flow

### 🟢 Trigger: **When a new WDATP alert occurs**
- **Service**: Microsoft Defender for Endpoint
- **Auth**: App ID + Secret  
- **Trigger**: Fires when a new security alert is created.

---

### 🔁 Actions

#### 1. **Get Alert by ID**
- **Connector**: Microsoft Defender
- **Input**: `Alert ID` from the trigger
- **Output**: Full alert details

---

#### 2. **HTTP – Call Azure OpenAI**
- **Method**: `POST`
- **URI**: Your Azure OpenAI Foundry deployment endpoint
- **Headers**:
    "Content-Type": "application/json",
    "api-key": "<your-api-key>"
- {
  "messages": [
    {
      "role": "system",
      "content": "You are a security analyst investigating alerts from Microsoft Defender."
    },
  ],
  "temperature": 0.2
}

### 3.Parse JSON 
  Content: Use Body from HTTP step
  Schema example : {
  "type": "object",
  "properties": {
    "choices": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "message": {
            "type": "object",
            "properties": {
              "content": {
                "type": "string"
              }
            }
          }
        }
      }
    }
  }
}

### 4.Compose 
	inputs : body('Parse_JSON')?['choices']?[0]?['message']?['content']

### 5.post message in chat or channel
