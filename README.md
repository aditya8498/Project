# Project


import speech_recognition as sr
import webbrowser
import pyttsx3
import requests
#from openai import OpenAI
#from gtts import gTTS
#import pygame
import os
import time

# Your API Keys
OPENAI_API_KEY = "sk-XXXXX"          # Replace with your real key
NEWS_API_KEY = "XXXXXXXXXXXX"        # Replace with NewsAPI key

# Initialize API & Libraries
#client = OpenAI(api_key=OPENAI_API_KEY)
recognizer = sr.Recognizer()

def speak(text):
    """Convert text to speech using gTTS + pygame"""
  #  tts = gTTS(text)
   # filename = "temp.mp3"
   # tts.save(filename)

    #pygame.mixer.init()
    #pygame.mixer.music.load(filename)
    #pygame.mixer.music.play()

    #while pygame.mixer.music.get_busy():
       # time.sleep(0.1)

    #pygame.mixer.music.unload()
    #os.remove(filename)


def aiProcess(command):
    """Send command to OpenAI for response"""
    #response = client.chat.completions.create(
     #   model="gpt-4o-mini",
      #  messages=[
          #  {"role": "system", "content": "You are Jarvis, a concise and helpful AI assistant."},
          #  {"role": "user", "content": command}
    #    ]
   # )
   # return response.choices[0].message.content


def readNews():
    """Fetch and speak top news headlines from India"""
    url = f"https://newsapi.org/v2/top-headlines?country=in&apiKey={NEWS_API_KEY}"
    r = requests.get(url)

    if r.status_code == 200:
        articles = r.json().get("articles", [])
        for article in articles[:5]:
            speak(article["title"])
    else:
        speak("Sorry, I'm unable to fetch the news right now.")


def processCommand(command):
    """Process user commands"""
    command = command.lower()

    if "open google" in command:
        speak("Opening Google")
        webbrowser.open("https://google.com")

    elif "open youtube" in command:
        speak("Opening YouTube")
        webbrowser.open("https://youtube.com")

    elif "open facebook" in command:
        speak("Opening Facebook")
        webbrowser.open("https://facebook.com")

    elif "open linkedin" in command:
        speak("Opening LinkedIn")
        webbrowser.open("https://linkedin.com")

    elif "news" in command:
        speak("Fetching the latest headlines")
        readNews()

    else:
        output = aiProcess(command)
        speak(output)


def listenForWakeWord():
    """Listen for wake word 'Jarvis'"""
    with sr.Microphone() as source:
        recognizer.adjust_for_ambient_noise(source)
        print("Listening for wake word...")
        audio = recognizer.listen(source)

    try:
        word = recognizer.recognize_google(audio)
        return word.lower()
    except:
        return ""


def listenForCommand():
    """Listen for user's spoken command"""
    with sr.Microphone() as source:
        print("Jarvis Active... Listening")
        recognizer.adjust_for_ambient_noise(source)
        audio = recognizer.listen(source)

    try:
        command = recognizer.recognize_google(audio)
        return command
    except:
        speak("Sorry, I did not understand.")
        return ""


if __name__ == "__main__":
    speak("Initializing Jarvis. System Ready.")

    while True:
        wake = listenForWakeWord()
        if wake == "jarvis":
            speak("Yes?")
            command = listenForCommand()
            processCommand(command)
