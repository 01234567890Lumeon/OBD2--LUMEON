## 🔧 Instalación
### **1️⃣ Requisitos**
Antes de ejecutar la aplicación, instala las siguientes dependencias:

```bash
pip install obd PyQt6 pyttsx3 SpeechRecognition
```

Para **Windows**, instala PyAudio:  
```bash
pip install pyaudio
```
Para **Linux**, usa:  
```bash
sudo apt-get install portaudio19-dev && pip install pyaudio
```

### **2️⃣ Clonar el repositorio**
```bash
git clone https://github.com/01234567890Lumeon/OBD2--LUMEON.git
cd OBD2--LUMEON
```

### **3️⃣ Ejecutar la aplicación**
```bash
python obd2_lumeon.py
```

## 🎮 Uso
1️⃣ Conecta tu adaptador OBD-II vía **USB, Bluetooth o WiFi**.  
2️⃣ Abre la aplicación y selecciona el puerto OBD-II.  
3️⃣ Presiona **"Escanear"** para obtener códigos de error.  
4️⃣ Usa el **modo de voz** para describir síntomas.  
5️⃣ Puedes **borrar fallos** si es compatible con tu coche.  

## 📝 Contribuir  
¡Este proyecto es **open-source**! Puedes mejorarlo enviando PRs o reportando problemas.  

🔹 **Forkea el repositorio**.  
🔹 **Crea una nueva rama**: `git checkout -b mi-mejora`  
🔹 **Sube los cambios**: `git commit -m "Mejoras en la UI"`  
🔹 **Haz un pull request** en GitHub.  

## 📜 Licencia  
Este proyecto usa la **GPL v3**, lo que significa que cualquiera puede usarlo y mejorarlo.  

📌 **Autor:** [Lumeon](https://github.com/01234567890Lumeon)
## 🔧 Instalación
### **1️⃣ Requisitos**
Antes de ejecutar la aplicación, instala las siguientes dependencias:

```bash
pip install obd PyQt6 pyttsx3 SpeechRecognition
```

Para **Windows**, instala PyAudio:  
```bash
pip install pyaudio
```
Para **Linux**, usa:  
```bash
sudo apt-get install portaudio19-dev && pip install pyaudio
```

### **2️⃣ Clonar el repositorio**
```bash
git clone https://github.com/01234567890Lumeon/OBD2--LUMEON.git
cd OBD2--LUMEON
```

### **3️⃣ Ejecutar la aplicación**
```bash
python obd2_lumeon.py
```

## 🎮 Uso
1️⃣ Conecta tu adaptador OBD-II vía **USB, Bluetooth o WiFi**.  
2️⃣ Abre la aplicación y selecciona el puerto OBD-II.  
3️⃣ Presiona **"Escanear"** para obtener códigos de error.  
4️⃣ Usa el **modo de voz** para describir síntomas.  
5️⃣ Puedes **borrar fallos** si es compatible con tu coche.  

## 📝 Contribuir  
¡Este proyecto es **open-source**! Puedes mejorarlo enviando PRs o reportando problemas.  

🔹 **Forkea el repositorio**.  
🔹 **Crea una nueva rama**: `git checkout -b mi-mejora`  
🔹 **Sube los cambios**: `git commit -m "Mejoras en la UI"`  
🔹 **Haz un pull request** en GitHub.  

## 📜 Licencia  
Este proyecto usa la **GPL v3**, lo que significa que cualquiera puede usarlo y mejorarlo.  

📌 **Autor:** [Lumeon](https://github.com/01234567890Lumeon)

import sys
import obd
import pyttsx3
import speech_recognition as sr
from PyQt6.QtWidgets import QApplication, QMainWindow, QPushButton, QTextEdit, QLabel, QVBoxLayout, QWidget

class OBD2Lumeon(QMainWindow):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("OBD2 LUMEON - Diagnóstico Inteligente")
        self.setGeometry(100, 100, 600, 400)

        self.layout = QVBoxLayout()

        self.label = QLabel("🔍 Presiona 'Escanear' para obtener los códigos de error")
        self.layout.addWidget(self.label)

        self.result_area = QTextEdit()
        self.result_area.setReadOnly(True)
        self.layout.addWidget(self.result_area)

        self.scan_button = QPushButton("Escanear")
        self.scan_button.clicked.connect(self.scan_obd)
        self.layout.addWidget(self.scan_button)

        self.clear_button = QPushButton("Borrar Fallos")
        self.clear_button.clicked.connect(self.clear_errors)
        self.layout.addWidget(self.clear_button)

        self.voice_button = QPushButton("Modo Voz")
        self.voice_button.clicked.connect(self.voice_input)
        self.layout.addWidget(self.voice_button)

        container = QWidget()
        container.setLayout(self.layout)
        self.setCentralWidget(container)

        self.engine = pyttsx3.init()

    def speak(self, text):
        self.engine.say(text)
        self.engine.runAndWait()

    def scan_obd(self):
        self.result_area.append("🔄 Escaneando...")
        try:
            connection = obd.OBD()  # Conecta con el adaptador OBD-II
            response = connection.query(obd.commands.GET_DTC)
            if response.value:
                self.result_area.append("🚗 Códigos encontrados:")
                for code in response.value:
                    self.result_area.append(f"❌ {code[0]} - {code[1]}")
                self.speak("Códigos de error encontrados")
            else:
                self.result_area.append("✅ No se encontraron errores.")
                self.speak("No se encontraron fallos en el sistema.")
        except Exception as e:
            self.result_area.append(f"⚠️ Error: {e}")

    def clear_errors(self):
        try:
            connection = obd.OBD()
            connection.query(obd.commands.CLEAR_DTC)
            self.result_area.append("✅ Fallos borrados con éxito.")
            self.speak("Fallos borrados correctamente.")
        except Exception as e:
            self.result_area.append(f"⚠️ Error: {e}")

    def voice_input(self):
        recognizer = sr.Recognizer()
        with sr.Microphone() as source:
            self.result_area.append("🎤 Escuchando...")
            self.speak("Dime el problema del coche.")
            try:
                audio = recognizer.listen(source, timeout=5)
                text = recognizer.recognize_google(audio, language="es-ES")
                self.result_area.append(f"🗣️ Detección de voz: {text}")
                self.speak(f"Has dicho: {text}")
            except sr.UnknownValueError:
                self.result_area.append("⚠️ No se entendió el audio.")
                self.speak("No entendí, intenta de nuevo.")
            except sr.RequestError:
                self.result_area.append("⚠️ Error con el reconocimiento de voz.")
                self.speak("Hubo un error con el reconocimiento de voz.")

if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = OBD2Lumeon()
    window.show()
    sys.exit(app.exec())
# Instalar dependencias necesarias
pip install obd pyttsx3 SpeechRecognition PyQt6

# Inicializar Git si aún no lo has hecho
git init

# Agregar archivos al repositorio
git add .

# Hacer un commit con el mensaje "Subiendo OBD2 LUMEON"
git commit -m "Subiendo OBD2 LUMEON"

# Configurar la rama principal
git branch -M main

# Agregar el repositorio remoto (cambia "TuUsuario" por tu nombre de usuario en GitHub)
git remote add origin https://github.com/TuUsuario/OBD2-LUMEON.git

# Subir los archivos al repositorio
git push -u origin main

# Ejecutar la aplicación OBD2 LUMEON
python obd2_lumeon.py

chmod +x setup_obd2_lumeon.sh
./setup_obd2_lumeon.sh
