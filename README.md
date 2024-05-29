#Pyflowchart
import tkinter as tk
from tkinter import filedialog
import pyaudio
import wave

class Song:
    def _init_(self, title, artist, category, filepath):
        self.title = title
        self.artist = artist
        self.category = category
        self.filepath = filepath

class MusicPlayer:
    def _init_(self):
        self.library = []
        self.chunk = 1024

    def add_song(self, title, artist, category, filepath):
        song = Song(title, artist, category, filepath)
        self.library.append(song)

    def play_song(self, filepath):
        wf = wave.open(filepath, 'rb')
        p = pyaudio.PyAudio()

        stream = p.open(format=p.get_format_from_width(wf.getsampwidth()),
                        channels=wf.getnchannels(),
                        rate=wf.getframerate(),
                        output=True)

        data = wf.readframes(self.chunk)

        while data:
            stream.write(data)
            data = wf.readframes(self.chunk)

        stream.stop_stream()
        stream.close()

        p.terminate()

# Initialize Tkinter
root = tk.Tk()
root.title("Music Player")

# Create music player instance
player = MusicPlayer()

# Function to handle play button click
def play_songs():
    category = category_var.get()
    for song in player.library:
        if song.category.lower() == category.lower():
            player.play_song(song.filepath)

# Function to handle upload button click
def upload_song():
    song_filepath = filedialog.askopenfilename(filetypes=(("MP3 files", ".mp3"), ("All files", ".*")))
    if song_filepath:
        player.add_song("Custom Song", "Unknown Artist", category_var.get(), song_filepath)

# GUI elements
tk.Label(root, text="Select Category:").pack()
category_var = tk.StringVar(root)
category_var.set("Pop")  # Default category
categories = ["Pop", "Love", "Rock", "Custom"]  # Add more categories if needed
category_dropdown = tk.OptionMenu(root, category_var, *categories)
category_dropdown.pack()

play_button = tk.Button(root, text="Play", command=play_songs)
play_button.pack()

upload_button = tk.Button(root, text="Upload Song", command=upload_song)
upload_button.pack()

# Adding songs to the library
player.add_song("Shape of You", "Ed Sheeran", "Pop", "path/to/shape_of_you.mp3")
player.add_song("Love Story", "Taylor Swift", "Pop", "path/to/love_story.mp3")
player.add_song("Perfect", "Ed Sheeran", "Love", "path/to/perfect.mp3")
player.add_song("All of Me", "John Legend", "Love", "path/to/all_of_me.mp3")
player.add_song("Hotel California", "Eagles", "Rock", "path/to/hotel_california.mp3")

root.mainloop()
