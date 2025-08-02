# ResearchGPT

An intelligent research assistant designed to streamline academic and scientific research. Built and deployed on IBM watsonx.ai, this agent automates literature reviews and knowledge synthesis to help researchers, students, and professionals stay updated with ease.

## Overview

This repository documents an AI Research Agent built and deployed on **IBM watsonx.ai**. The agent is powered by the **llama-3-3-70b-instruct** foundation model and uses a sophisticated LangGraph with a ReAct reasoning architecture. It is designed to provide highly accurate, structured, and comprehensive responses to complex research inquiries.

## Core Capabilities

The agent is equipped with a suite of features to streamline the research process:

* **Multilingual Support:** Capable of conducting research and providing responses across various languages.
* **Semantic Search:** Performs intelligent, contextual searches across academic papers, journals, and datasets.
* **AI-Assisted Drafting:** Can generate comprehensive reports, suggest hypotheses, and draft sections of research papers.
* **Research Gap Identification:** Analyzes existing literature to pinpoint unexplored areas and suggest novel topics.
* **Trend Analysis:** Provides insights into research trends over time for specific keywords or domains.
* **Real-Time Tooling:** Integrates external tools (e.g., Google Search, Wikipedia) to access up-to-the-minute information.

## Technology Stack

* **Platform:** IBM watsonx.ai
* **Foundation Model:** mistral-large
* **Architecture:** LangGraph with ReAct reasoning
* **Tools:**
    * Google Search
    * Wikipedia

## Getting Started

This section provides a simple Python script to demonstrate how to call your deployed agent's API endpoint.

### Prerequisites

* Python 3.x
* The `requests` library (`pip install requests`)

### API Call Example

Replace the placeholders `YOUR_API_KEY` and `YOUR_DEPLOYMENT_ID` with your own credentials.

```python
import requests
import json

# This is a placeholder for your API key. For security, do not commit
# your actual key to a public repository.
API_KEY = "YOUR_API_KEY"

# This is the endpoint for the IBM Cloud IAM token service
token_response = requests.post('[https://iam.cloud.ibm.com/identity/token](https://iam.cloud.ibm.com/identity/token)', data={"apikey": API_KEY, "grant_type": 'urn:ibm:params:oauth:grant-type:apikey'})
mltoken = token_response.json()["access_token"]

# Headers for the scoring request
header = {'Content-Type': 'application/json', 'Authorization': 'Bearer ' + mltoken}

# This is a placeholder for your deployment ID.
deployment_id = "YOUR_DEPLOYMENT_ID"
payload_scoring = {
    "messages": [
        {"content": "PREPARE A WELL RESEARCHED ARTICLE ABOUT WORLD WAR 2", "role": "user"}
    ]
}

# The endpoint for your deployed watsonx agent
endpoint_url = f"[https://eu-gb.ml.cloud.ibm.com/ml/v4/deployments/](https://eu-gb.ml.cloud.ibm.com/ml/v4/deployments/){deployment_id}/ai_service_stream?version=2021-05-01"

print("Sending request to watsonx Agent...")

# Make the streaming API call
response_scoring = requests.post(endpoint_url, json=payload_scoring, headers=header, stream=True)

full_article = ""

# Process the streaming response
for line in response_scoring.iter_lines():
    if line:
        try:
            line = line.decode('utf-8')
            if line.startswith('data:'):
                json_data = json.loads(line[5:].strip())
                if "choices" in json_data and json_data["choices"][0]["delta"] and "content" in json_data["choices"][0]["delta"]:
                    content = json_data["choices"][0]["delta"]["content"]
                    full_article += content
        except Exception as e:
            pass

print("\n--- Agent Response ---")
print(full_article)
