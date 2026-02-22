import tkinter as tk
import customtkinter as ctk
import numpy as np
import sounddevice as sd
import matplotlib.pyplot as plt
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg

# 1. DTMF Frekans Tablosu Tanımlama
DTMF_TABLE = {
    '1': (697, 1209), '2': (697, 1336), '3': (697, 1477), 'A': (697, 1633),
    '4': (770, 1209), '5': (770, 1336), '6': (770, 1477), 'B': (770, 1633),
    '7': (852, 1209), '8': (852, 1336), '9': (852, 1477), 'C': (852, 1633),
    '*': (941, 1209), '0': (941, 1336), '#': (941, 1477), 'D': (941, 1633),
}

class DTMFApp(ctk.CTk):
    def __init__(self):
        super().__init__()

        self.title("DTMF Sinyal Sentezleyici - Grup Ödevi")
        self.geometry("900x500")
        ctk.set_appearance_mode("dark")

        # Parametreler
        self.fs = 44100  # Örnekleme Frekansı (Hz)
        self.duration = 0.3  # Saniye

        self.setup_ui()

    def setup_ui(self):
        # Ana Frame
        self.grid_columnconfigure(1, weight=1)
        self.grid_rowconfigure(0, weight=1)

        # --- Sol Panel: Tuş Takımı ---
        self.keypad_frame = ctk.CTkFrame(self)
        self.keypad_frame.grid(row=0, column=0, padx=20, pady=20, sticky="nsew")
        
        keys = [
            ['1', '2', '3', 'A'],
            ['4', '5', '6', 'B'],
            ['7', '8', '9', 'C'],
            ['*', '0', '#', 'D']
        ]

        for r, row in enumerate(keys):
            for c, key in enumerate(row):
                btn = ctk.CTkButton(self.keypad_frame, text=key, width=60, height=60,
                                   font=("Arial", 20, "bold"),
                                   command=lambda k=key: self.play_dtmf(k))
                btn.grid(row=r, column=c, padx=5, pady=5)

        # --- Sağ Panel: Grafik ---
        self.plot_frame = ctk.CTkFrame(self)
        self.plot_frame.grid(row=0, column=1, padx=20, pady=20, sticky="nsew")
        
        self.fig, self.ax = plt.subplots(figsize=(5, 4), dpi=100)
        self.fig.patch.set_facecolor('#2b2b2b') # Dark mode uyumu
        self.ax.set_facecolor('#2b2b2b')
        self.ax.tick_params(colors='white')
        self.ax.set_title("Sinyal Grafiği (Zaman Domaini)", color="white")
        
        self.canvas = FigureCanvasTkAgg(self.fig, master=self.plot_frame)
        self.canvas.get_tk_widget().pack(fill="both", expand=True)

    def play_dtmf(self, key):
        f_low, f_high = DTMF_TABLE[key]
        
        # Zaman dizisi oluşturma
        t = np.linspace(0, self.duration, int(self.fs * self.duration), endpoint=False)
        
        # Matematiksel Model: x(t) = sin(2*pi*f1*t) + sin(2*pi*f2*t)
        signal = np.sin(2 * np.pi * f_low * t) + np.sin(2 * np.pi * f_high * t)
        
        # Normalizasyon (Clipping önlemek için 0.5 ile çarpım)
        signal = signal * 0.5 

        # Sesi Çal
        sd.play(signal, self.fs)
        
        # Grafiği Güncelle (Sadece ilk 10ms'yi göstererek dalga formunu netleştiriyoruz)
        self.update_plot(t[:441], signal[:441], key, f_low, f_high)

    def update_plot(self, t, signal, key, fl, fh):
        self.ax.clear()
        self.ax.plot(t * 1000, signal, color="#1f538d", linewidth=2) # ms cinsinden
        self.ax.set_title(f"Tuş: {key} (Düşük: {fl}Hz, Yüksek: {fh}Hz)", color="white")
        self.ax.set_xlabel("Zaman (ms)", color="white")
        self.ax.set_ylabel("Genlik", color="white")
        self.ax.set_ylim(-1.1, 1.1)
        self.ax.grid(True, linestyle='--', alpha=0.3)
        self.canvas.draw()

if __name__ == "__main__":
    app = DTMFApp()
    app.mainloop()
