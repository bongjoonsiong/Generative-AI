
#page 
## Generative AI - Building a Chatbot with Gemini API

**Project description:** 
Generative AI taking the world by storm and almost every organization is rushing to see how they can leverage this revolutional technology in their operation. One area is the customized Chatbot built from advanced LLM model. We will delve into the detail on how to use Google AI Studio Gemini API to build such customized Chatbot. 

### 1. What is Google Gemini API

Gemini is a collection of large language models (LLMs) hosted on Google Cloud’s Vertex AI platform. These models excel at tasks like:

Conversational AI: Holding natural, open-ended conversations.
Text Summarization: Distilling key points from large bodies of text.
Question Answering: Providing accurate, relevant answers to questions drawing on a knowledge base or a text passage.
Text Generation: Crafting different creative text formats (poems, scripts, email, etc.)

### 2. Why Use the Gemini API?
Gemini API gives you access to powerful, versatile language models within the robust Google Cloud ecosystem, making it a top choice for building intelligent chatbots. Gemini offers several compelling reasons for choosing it as your AI language model solution:

Multimodal Capabilities: Gemini excels at handling both text and image inputs. Your chatbot can understand images alongside user inquiries, enriching the conversational experience and enabling visual question-answering scenarios.
Advanced Language Understanding: Gemini’s language models are built on Google’s cutting-edge AI research, providing exceptional accuracy and fluency in text generation, summarization, and answering complex questions.
Flexibility: Gemini is adaptable to diverse tasks. You can fine-tune it for specific use cases, enhancing its performance within your conversational AI system.
Ease of Integration with Google Cloud: As part of Google Cloud Platform, Gemini integrates seamlessly with other GCP services like Cloud Storage, Cloud Functions, and Cloud Run, simplifying your development workflow and data management.
Scalability: Vertex AI, the platform hosting Gemini, provides the scalability needed to handle large volumes of chatbot interactions, ensuring your application performs well under load.

Setup
API Key
Before you can use the Gemini API, you must first obtain an API key. If you don’t already have one, create a key with one click in Google AI Studio.

Python SDK
The Python SDK for the Gemini API, is contained in the google-generativeai package. Install the dependency using pip:

```javascript
pip install -q -U google-generativeai #run on command prompt
```

### 3. Building Your Chatbot in Python
We’ll explore how to construct your chatbot: a command-line interface (CLI) for quick prototyping

```javascript
import google.generativeai as genai

GOOGLE_API_KEY='...'
genai.configure(api_key=GOOGLE_API_KEY)

# List available models.
print('Available models:')
for m in genai.list_models():
  if 'generateContent' in m.supported_generation_methods:
      print(f'- {m.name}')
model = genai.GenerativeModel('gemini-pro')

print('\nReady to chat...')
while True:
  prompt= input("You: ")
  response = model.generate_content(prompt)
  result = ''.join([p.text for p in response.candidates[0].content.parts])
  print("Gemini: ", result)
```

### 4. Run this python script and you will be abel to chat with this Chabtbot just like on ChatGPT 4.

```javascript
$ python chatbot.py 

Available models:
- models/gemini-pro
- models/gemini-pro-vision

Ready to chat...

You: hello
Gemini:  Greetings! How may I assist you today?

You: how old is Elon Musk?
Gemini:  As of January 2023, Elon Musk is 51 years old. He was born on June 28, 1971 in Pretoria, South Africa.
```

<img src="images/dummy_thumbnail.jpg?raw=true"/>

### 5. Conclusion

This Python code shows how we can use Generative AI framework and Gemini API (get the API Key from Google AI Studio) to create our own Chatbot, leverage the underlyding multimodel Gemini LLM.  


