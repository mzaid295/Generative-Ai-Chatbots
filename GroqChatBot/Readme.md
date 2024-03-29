# Groq Chat App

This project is a chat application built using Groq API and Streamlit.

## Overview

The application allows users to interact with a chatbot powered by the Groq API. Users can ask questions and receive responses from the chatbot. The chatbot utilizes conversational memory to provide contextually relevant responses.

## Dependencies

- Streamlit
- Groq
- Langchain
- dotenv

## Installation

1. Clone the repository.
2. Install the required dependencies using pip:
   
   ```bash
   pip install streamlit groq langchain python-dotenv

Set up your environment variables by creating a .env file and adding your Groq API key:
GROQ_API_KEY=YOUR_API_KEY_HERE

## App Screenshot
![Chatbot](https://github.com/mzaid295/Generative-Ai-Chatbots/blob/main/GroqChatBot/Groq%20Chat%20App.png))


## Code
Run the application using the following command:
   ```bash
   streamlit run groq_chat_app.py

Open your browser and navigate to the provided URL to access the chat application.

Ask questions and interact with the chatbot.
   
import streamlit as st
import os
from groq import Groq
import random

from langchain.chains import ConversationChain
from langchain.chains.conversation.memory import ConversationBufferWindowMemory
from langchain_groq import ChatGroq
from dotenv import load_dotenv

load_dotenv()

groq_api_key = os.environ['GROQ_API_KEY']

def main():

    st.title("Groq Chat App")

    # Add customization options to the sidebar
    st.sidebar.title('Select an LLM')
    model = st.sidebar.selectbox(
        'Choose a model',
        ['mixtral-8x7b-32768', 'llama2-70b-4096']
    )
    conversational_memory_length = st.sidebar.slider('Conversational memory length:', 1, 10, value = 5)

    memory=ConversationBufferWindowMemory(k=conversational_memory_length)

    user_question = st.text_area("Ask a question:")

    # session state variable
    if 'chat_history' not in st.session_state:
        st.session_state.chat_history=[]
    else:
        for message in st.session_state.chat_history:
            memory.save_context({'input':message['human']},{'output':message['AI']})


    # Initialize Groq Langchain chat object and conversation
    groq_chat = ChatGroq(
            groq_api_key=groq_api_key,
            model_name=model
    )

    conversation = ConversationChain(
            llm=groq_chat,
            memory=memory
    )

    if user_question:
        response = conversation(user_question)
        message = {'human':user_question,'AI':response['response']}
        st.session_state.chat_history.append(message)
        st.write("Chatbot:", response['response'])

if __name__ == "__main__":
    main()
