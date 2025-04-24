name: ChatGPT API for Python
use_when: When assisting with OpenAI API integration, ChatGPT implementation in Python applications, or advising on best practices for AI text generation
content: 

# ChatGPT API for Python Knowledge Entry

## Definition and Overview
The ChatGPT API (part of OpenAI's API offerings) provides programmatic access to OpenAI's powerful language models through Python. It enables developers to integrate AI-powered natural language processing capabilities into applications, allowing for text generation, conversation, content creation, and other language-based tasks. The Python SDK offers a streamlined way to interact with these models, making advanced AI accessible to developers of all skill levels.

## Core Components

### API Access and Authentication
1. **API Key**: A unique identifier required for authentication with OpenAI's services.
2. **Organization ID**: Optional identifier for users belonging to multiple organizations.
3. **Base URL**: The endpoint for API requests (https://api.openai.com/v1/).
4. **Authentication Headers**: HTTP headers containing the API key for request authorization.
5. **Rate Limits**: Constraints on the number of requests allowed within specific time periods.

### Available Models
1. **GPT-4**: The most advanced model with improved reasoning capabilities.
2. **GPT-3.5-Turbo**: Optimized for chat applications, balancing performance and cost.
3. **GPT-3.5-Turbo-Instruct**: Designed for instruction-following tasks.
4. **Fine-tuned Models**: Custom versions of base models trained on specific datasets.
5. **Legacy Models**: Older versions maintained for backward compatibility.

### Core Functionality
1. **Chat Completions**: Generate conversational responses based on message history.
2. **Text Completions**: Generate text continuations from prompts (legacy endpoint).
3. **Embeddings**: Convert text into numerical vector representations.
4. **Function Calling**: Define functions that the model can suggest calling with appropriate arguments.
5. **JSON Mode**: Force the model to return responses in valid JSON format.
6. **Streaming**: Receive partial responses as they're generated rather than waiting for complete responses.

## Python Implementation

### Installation and Setup
```python
# Install the OpenAI Python library
pip install openai

# Import and configure with API key
import openai
import os

# Method 1: Direct assignment (not recommended for production)
openai.api_key = "your-api-key"

# Method 2: Environment variable (recommended)
openai.api_key = os.getenv("OPENAI_API_KEY")

# Method 3: Using the client (newer approach)
from openai import OpenAI
client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))
```

### Basic Chat Completion
```python
# Using the client approach (recommended for newer applications)
from openai import OpenAI

client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Hello, can you explain what API stands for?"}
    ]
)

print(response.choices[0].message.content)
```

### Managing Conversations
```python
# Initialize conversation history
messages = [
    {"role": "system", "content": "You are a helpful assistant."}
]

# Function to get response and update conversation
def chat_with_gpt(user_input):
    # Add user message to conversation
    messages.append({"role": "user", "content": user_input})
    
    # Get response from API
    response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=messages
    )
    
    # Extract assistant's reply
    assistant_response = response.choices[0].message.content
    
    # Add assistant response to conversation history
    messages.append({"role": "assistant", "content": assistant_response})
    
    return assistant_response
```

### Advanced Features

#### Streaming Responses
```python
stream = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[{"role": "user", "content": "Write a poem about programming"}],
    stream=True
)

for chunk in stream:
    if chunk.choices[0].delta.content is not None:
        print(chunk.choices[0].delta.content, end="")
```

#### Function Calling
```python
functions = [
    {
        "name": "get_weather",
        "description": "Get the current weather in a given location",
        "parameters": {
            "type": "object",
            "properties": {
                "location": {
                    "type": "string",
                    "description": "The city and state, e.g. San Francisco, CA"
                },
                "unit": {
                    "type": "string",
                    "enum": ["celsius", "fahrenheit"]
                }
            },
            "required": ["location"]
        }
    }
]

response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[{"role": "user", "content": "What's the weather like in Boston?"}],
    functions=functions,
    function_call="auto"
)
```

## Best Practices

### API Key Security
1. **Environment Variables**: Store API keys in environment variables, not in code.
2. **Secret Management**: Use services like Azure Key Vault or AWS Secrets Manager for production.
3. **Backend Processing**: Make API calls from a backend server, not directly from client-side code.
4. **Restricted Access**: Limit API key permissions to only what's necessary.
5. **Regular Rotation**: Change API keys periodically, especially after team member departures.

### Cost Optimization
1. **Model Selection**: Choose the most cost-effective model for your needs.
2. **Token Management**: Limit input and output tokens to control costs.
3. **Caching**: Cache responses for identical or similar requests.
4. **Batching**: Combine multiple requests when possible.
5. **Request Monitoring**: Track API usage to identify optimization opportunities.

### Performance Optimization
1. **Prompt Engineering**: Craft efficient prompts to get better responses with fewer tokens.
2. **Parallel Processing**: Use async/await for concurrent requests.
3. **Streaming**: Implement streaming for better user experience with long responses.
4. **Request Timeouts**: Set appropriate timeouts to handle API delays.
5. **Retry Logic**: Implement exponential backoff for failed requests.

### Error Handling
1. **Rate Limit Handling**: Implement backoff strategies for rate limit errors.
2. **Graceful Degradation**: Provide fallback options when the API is unavailable.
3. **Logging**: Record errors for debugging and monitoring.
4. **User Feedback**: Communicate API issues to users appropriately.
5. **Validation**: Validate inputs before sending to the API to prevent unnecessary errors.

## Common Challenges and Solutions

### Challenge: Inconsistent Responses
**Solutions**:
- Use system messages to set clear context and constraints
- Implement temperature and top_p parameters to control randomness
- Use few-shot examples to guide the model's responses
- Consider fine-tuning for consistent domain-specific responses

### Challenge: Managing Long Conversations
**Solutions**:
- Implement conversation summarization to stay within token limits
- Use a sliding window approach to maintain recent context
- Store and retrieve relevant conversation parts based on current query
- Implement embeddings to retrieve semantically relevant past exchanges

### Challenge: Handling Sensitive Information
**Solutions**:
- Implement content filtering before and after API calls
- Use the OpenAI moderation API to detect problematic content
- Implement PII detection and redaction in both inputs and outputs
- Create clear usage policies and user guidelines

### Challenge: Deployment and Scaling
**Solutions**:
- Use asynchronous processing for high-volume applications
- Implement queue systems for managing request spikes
- Deploy with containerization for consistent environments
- Use load balancing and auto-scaling for production systems

## Advanced Implementation Patterns

### RAG (Retrieval-Augmented Generation)
```python
# Simplified RAG implementation
def retrieve_relevant_documents(query):
    # Use embeddings to find relevant documents
    query_embedding = client.embeddings.create(
        model="text-embedding-ada-002",
        input=query
    ).data[0].embedding
    
    # Retrieve documents with similar embeddings
    relevant_docs = vector_db.search(query_embedding, top_k=3)
    return relevant_docs

def answer_with_context(query):
    # Get relevant documents
    docs = retrieve_relevant_documents(query)
    context = "\n\n".join([doc.text for doc in docs])
    
    # Create prompt with context
    messages = [
        {"role": "system", "content": "Answer based on the following context only."},
        {"role": "user", "content": f"Context: {context}\n\nQuestion: {query}"}
    ]
    
    # Get response
    response = client.chat.completions.create(
        model="gpt-4",
        messages=messages
    )
    
    return response.choices[0].message.content
```

### Fine-tuning for Specialized Applications
```python
# Prepare training data
training_data = [
    {"messages": [
        {"role": "system", "content": "You are a customer service assistant."},
        {"role": "user", "content": "I haven't received my order yet."},
        {"role": "assistant", "content": "I'm sorry to hear that. Could you please provide your order number so I can check the status for you?"}
    ]},
    # More examples...
]

# Create fine-tuning job
job = client.fine_tuning.jobs.create(
    training_file="file-abc123",  # ID of uploaded JSONL file
    model="gpt-3.5-turbo"
)

# Check fine-tuning status
job_status = client.fine_tuning.jobs.retrieve(job.id)

# Use fine-tuned model
response = client.chat.completions.create(
    model=job_status.fine_tuned_model,
    messages=[{"role": "user", "content": "Where is my package?"}]
)
```

### Implementing Moderation
```python
def is_safe_content(text):
    moderation_response = client.moderations.create(input=text)
    return not moderation_response.results[0].flagged

def safe_chat_completion(user_input):
    # Check user input
    if not is_safe_content(user_input):
        return "I cannot process this request as it contains inappropriate content."
    
    # Get model response
    response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[{"role": "user", "content": user_input}]
    )
    model_output = response.choices[0].message.content
    
    # Check model output
    if not is_safe_content(model_output):
        return "I generated a response that may be inappropriate. Let me try a different approach."
    
    return model_output
```

## Integration with Other Technologies

### Web Applications (Flask)
```python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route('/chat', methods=['POST'])
def chat_endpoint():
    user_message = request.json.get('message', '')
    
    response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[{"role": "user", "content": user_message}]
    )
    
    return jsonify({
        "response": response.choices[0].message.content
    })

if __name__ == '__main__':
    app.run(debug=True)
```

### Async Processing (FastAPI)
```python
import asyncio
from fastapi import FastAPI
import httpx

app = FastAPI()

async def get_completion(message):
    async with httpx.AsyncClient() as http_client:
        response = await http_client.post(
            "https://api.openai.com/v1/chat/completions",
            headers={
                "Authorization": f"Bearer {os.getenv('OPENAI_API_KEY')}",
                "Content-Type": "application/json"
            },
            json={
                "model": "gpt-3.5-turbo",
                "messages": [{"role": "user", "content": message}]
            },
            timeout=30.0
        )
        return response.json()

@app.post("/chat")
async def chat_endpoint(request: dict):
    user_message = request.get('message', '')
    response = await get_completion(user_message)
    return {"response": response["choices"][0]["message"]["content"]}
```

### Database Integration
```python
import sqlite3
from datetime import datetime

# Initialize database
conn = sqlite3.connect('chat_history.db')
cursor = conn.cursor()
cursor.execute('''
CREATE TABLE IF NOT EXISTS conversations (
    id INTEGER PRIMARY KEY,
    user_id TEXT,
    message TEXT,
    response TEXT,
    timestamp TEXT
)
''')
conn.commit()

def save_conversation(user_id, message, response):
    timestamp = datetime.now().isoformat()
    cursor.execute(
        "INSERT INTO conversations (user_id, message, response, timestamp) VALUES (?, ?, ?, ?)",
        (user_id, message, response, timestamp)
    )
    conn.commit()

def get_user_history(user_id, limit=10):
    cursor.execute(
        "SELECT message, response FROM conversations WHERE user_id = ? ORDER BY timestamp DESC LIMIT ?",
        (user_id, limit)
    )
    return cursor.fetchall()
```

## Conclusion
The ChatGPT API for Python provides a powerful way to integrate advanced language models into applications. By following best practices for security, cost optimization, and error handling, developers can create robust AI-powered solutions. The flexibility of the API allows for implementation in various contexts, from simple chatbots to complex applications with specialized requirements. As the technology continues to evolve, staying updated with the latest features and techniques will help maximize the value derived from these powerful language models.
