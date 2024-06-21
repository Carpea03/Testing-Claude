import streamlit as st
import pandas as pd
from datetime import datetime
import anthropic
import os

# Initialize Anthropic client
client = anthropic.Anthropic(api_key=os.environ.get("ANTHROPIC_API_KEY"))

# Initialize session state variables
if 'chat_history' not in st.session_state:
    st.session_state.chat_history = []

if 'current_question' not in st.session_state:
    st.session_state.current_question = 0

if 'conversation' not in st.session_state:
    st.session_state.conversation = []

# App title
st.title("AI Sales Professional Interview Chatbot")

# Initialize Claude with the context
SYSTEM_PROMPT = """You are an AI interviewer specializing in sales techniques. Your task is to interview sales professionals to gather insights about their successful sales experiences. Ask questions to understand their strategies, client interactions, and lessons learned. Be conversational, ask follow-up questions when appropriate, and try to extract valuable information that could benefit other sales professionals. Start by introducing yourself and asking about a recent successful sale."""

# Display chat history
for message in st.session_state.chat_history:
    st.text(message)

# Get user input
user_input = st.text_input("Your response:", key="user_input")

if st.button("Submit"):
    # Add user input to conversation and chat history
    st.session_state.conversation.append({"role": "human", "content": user_input})
    st.session_state.chat_history.append(f"You: {user_input}")

    # Get Claude's response
    response = client.messages.create(
        model="claude-3-opus-20240229",
        max_tokens=1000,
        temperature=0.7,
        system=SYSTEM_PROMPT,
        messages=st.session_state.conversation
    )

    claude_response = response.content[0].text

    # Add Claude's response to conversation and chat history
    st.session_state.conversation.append({"role": "assistant", "content": claude_response})
    st.session_state.chat_history.append(f"Claude: {claude_response}")

    # Increment question counter
    st.session_state.current_question += 1

    # Rerun the app to display the updated chat
    st.experimental_rerun()

# End interview and save transcript
if st.button("End Interview"):
    st.text("Interview ended. Saving transcript...")
    
    # Convert conversation to DataFrame
    df = pd.DataFrame(st.session_state.conversation)
    
    # Add timestamp
    df['Timestamp'] = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    
    # Save to CSV
    df.to_csv("sales_interviews_transcript.csv", mode='a', header=False, index=False)
    st.success("Transcript saved successfully!")
    
    # Display the full transcript
    st.subheader("Interview Transcript:")
    for message in st.session_state.conversation:
        st.text(f"{message['role'].capitalize()}: {message['content']}")
    
    # Reset for next interview
    st.session_state.chat_history = []
    st.session_state.conversation = []
    st.session_state.current_question = 0

# Display saved data
if st.checkbox("Show all saved transcripts"):
    try:
        saved_data = pd.read_csv("sales_interviews_transcript.csv", names=['Role', 'Content', 'Timestamp'])
        st.dataframe(saved_data)
    except FileNotFoundError:
        st.text("No saved transcripts found.")
