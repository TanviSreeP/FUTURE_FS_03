import random
import time
import requests
import wolframalpha
import pyttsx3
import wikipedia
import re

class AIAssistant:
    def __init__(self):
        # Initialize speech engine
        self.engine = pyttsx3.init()

        # Wolfram Alpha Client (You need to replace this with your actual app ID)
        self.client = wolframalpha.Client("YOUR_WOLFRAM_ALPHA_APP_ID")  # Replace with your Wolfram Alpha API key

        self.responses = {
            "greeting": ["Hello!", "Hi there!", "Hey! How can I help?"],
            "how_are_you": ["I'm just a program, but I'm doing fine!", "I'm good, thanks for asking!"],
            "bye": ["Goodbye!", "See you later!", "Bye! Take care!"],
            "what_is_your_name": ["I am a simple AI assistant.", "You can call me your assistant!"],
            "what_can_you_do": ["I can help you with simple questions, calculations, and some tasks.", "I can provide answers to general questions, perform math calculations, and chat with you."]
        }

    def speak(self, text):
        """Use text-to-speech to respond to the user."""
        self.engine.say(text)
        self.engine.runAndWait()

    def calculate(self, query):
        """Perform basic math calculations."""
        try:
            result = eval(query)
            return f"The result is: {result}"
        except Exception as e:
            return "I couldn't compute that. Can you rephrase your question?"

    def fetch_from_wikipedia(self, query):
        """Fetch answer from Wikipedia."""
        try:
            summary = wikipedia.summary(query, sentences=2)
            return summary
        except wikipedia.exceptions.DisambiguationError as e:
            return f"Could you be more specific? There were several results for '{query}'."
        except wikipedia.exceptions.HTTPTimeoutError:
            return "I had trouble reaching Wikipedia. Please try again later."
        except wikipedia.exceptions.PageError:
            return f"I couldn't find information on '{query}'."

    def fetch_from_wolframalpha(self, query):
        """Fetch answer from Wolfram Alpha (for scientific queries)."""
        try:
            res = self.client.query(query)
            answer = next(res.results).text
            return answer
        except StopIteration:
            return "I couldn't find an answer for that in Wolfram Alpha."
        except Exception as e:
            return f"Error: {str(e)}"

    def respond(self, user_input):
        """Generate a response based on the user input."""
        user_input = user_input.lower()

        # Check for greetings
        if re.search(r"(hello|hi|hey|morning|good evening)", user_input):
            return random.choice(self.responses["greeting"])

        # Check if the user asks how the assistant is doing
        elif re.search(r"(how are you|how's it going|what's up|how do you do)", user_input):
            return random.choice(self.responses["how_are_you"])

        # Check for "bye" or "goodbye"
        elif re.search(r"(bye|goodbye|see you later)", user_input):
            return random.choice(self.responses["bye"])

        # Check for "name" or "what is your name?"
        elif re.search(r"(what is your name|what's your name|who are you)", user_input):
            return random.choice(self.responses["what_is_your_name"])

        # Check for "what can you do?"
        elif re.search(r"(what can you do|what are your abilities|what do you do)", user_input):
            return random.choice(self.responses["what_can_you_do"])

        # Handle mathematical queries
        elif re.search(r"(calculate|math|solve)", user_input):
            return self.calculate(user_input)

        # Handle fact-based questions (via Wikipedia)
        elif re.search(r"(who is|what is|tell me about)", user_input):
            return self.fetch_from_wikipedia(user_input)

        # Use Wolfram Alpha for scientific or detailed queries
        else:
            return self.fetch_from_wolframalpha(user_input)

    def start_conversation(self):
        """Start a simple conversation loop with the user."""
        self.speak("Hello! I am your AI assistant. Type 'bye' to exit.")
        print("AI Assistant: Hello! I am your AI assistant. Type 'bye' to exit.")
        
        while True:
            user_input = input("You: ")

            # Check if the user wants to exit
            if re.search(r"(bye|goodbye|see you later)", user_input.lower()):
                self.speak("Goodbye! Take care.")
                print("AI Assistant: Goodbye!")
                break

            # Respond to the user
            response = self.respond(user_input)
            self.speak(response)
            print(f"AI Assistant: {response}")
            time.sleep(1)  # Simulate thinking time

if __name__ == "__main__":
    assistant = AIAssistant()
    assistant.start_conversation()
