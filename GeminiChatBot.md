
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

### 3. Building Your Chatbot in Python (METHOD 01)
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

### 6. Building Custom Gemini Chatbot in Python (METHOD 02)
Create a file named gemini-bot.py and add the following code to it.

```javascript
import streamlit as st
import os
import google.generativeai as genai

st.title("Gemini Bot")

os.environ['GOOGLE_API_KEY'] = "AIzaSyAjsDpD-XXXXXXXXXXXXX"
genai.configure(api_key = os.environ['GOOGLE_API_KEY'])

# Select the model
model = genai.GenerativeModel('gemini-pro')

# Initialize chat history
if "messages" not in st.session_state:
    st.session_state.messages = [
        {
            "role":"assistant",
            "content":"Ask me Anything"
        }
    ]

# Display chat messages from history on app rerun
for message in st.session_state.messages:
    with st.chat_message(message["role"]):
        st.markdown(message["content"])

# Process and store Query and Response
def llm_function(query):
    response = model.generate_content(query)

    # Displaying the Assistant Message
    with st.chat_message("assistant"):
        st.markdown(response.text)

    # Storing the User Message
    st.session_state.messages.append(
        {
            "role":"user",
            "content": query
        }
    )

    # Storing the User Message
    st.session_state.messages.append(
        {
            "role":"assistant",
            "content": response.text
        }
    )

# Accept user input
query = st.chat_input("What's up?")

# Calling the Function when Input is Provided
if query:
    # Displaying the User Message
    with st.chat_message("user"):
        st.markdown(query)

    llm_function(query)
```

### 7. Run the app by executing the following command: 
```javascript
streamlit run gemini-bot.py
```

### 8. Open the link which is displayed on the terminal to access the application

### 9. Building Custom Gemini Chatbot in Python with NiceGUI and Jinja2 (METHOD 03)
Create a file named main.py and add the following code to it.

```javascript
import vertexai
from google.oauth2 import service_account
from jinja2 import Environment, PackageLoader, select_autoescape
from nicegui import ui, app
from vertexai.generative_models import GenerativeModel

credentials = service_account.Credentials.from_service_account_file(
    "../gcp-vojay-gemini.json"
)
vertexai.init(project="vojay-329716", location="us-central1", credentials=credentials)

env = Environment(
    loader=PackageLoader("my_gemini_chatbot"),
    autoescape=select_autoescape()
)

model = GenerativeModel("gemini-pro")


def get_chat_response(chat, prompt):
    text_response = []
    responses = chat.send_message(prompt, stream=True)
    for chunk in responses:
        text_response.append(chunk.text)
    return ''.join(text_response)


def get_personality_file(value):
    match value:
        case "Default":
            return "default.jinja"
        case "Santa Claus":
            return "santaclaus.jinja"
        case "Dad Jokes":
            return "dadjokes.jinja"
        case _:
            return "default.jinja"


def send():
    user_prompt = app.storage.client.get("prompt")
    personality = app.storage.client.get("personality")

    personality_template = env.get_template(get_personality_file(personality))
    prompt_template = env.get_template("prompt.jinja")

    prompt = prompt_template.render(
        prompt=user_prompt,
        personality=personality_template.render()
    )

    ui.notify("Sending to Gemini...", type="info")
    chat = model.start_chat()
    response = get_chat_response(chat, prompt)
    ui.notify("Received response...", type="info")

    app.storage.client["response"] = response


@ui.page('/')
def index():
    with ui.grid(columns=16).classes("w-3/4 place-self-center gap-4"):
        ui.markdown("# 🚀 My Gemini Chatbot").classes("col-span-full")
        ui.input(label="Prompt").bind_value(app.storage.client, "prompt").classes("col-span-10")
        ui.select(
            options=["Default", "Santa Claus", "Dad Jokes"],
            value="Default",
            label="Personality"
        ).bind_value(app.storage.client, "personality").classes("col-span-6")

        ui.button("Send to Gemini", on_click=send).classes("col-span-8")

        dark = ui.dark_mode()
        ui.button("Light UI", on_click=dark.disable).classes("col-span-4")
        ui.button("Dark UI", on_click=dark.enable).classes("col-span-4")

        with ui.card().classes("col-span-full"):
            ui.markdown("## Gemini Response")
            ui.separator()
            ui.label().bind_text(app.storage.client, "response")


ui.run()
```
