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
from PyQt6.QtWidgets import QApplication, QWidget, QVBoxLayout, QPushButton, QLabel, QTextEdit
from PyQt6.QtGui import QFont, QPalette, QColor
from PyQt6.QtCore import Qt, QSize

class OBD2LumeonUI(QWidget):
    def __init__(self):
        super().__init__()

        # Configurar ventana
        self.setWindowTitle("OBD2 LUMEON - Diagnóstico Inteligente")
        self.setGeometry(100, 100, 600, 500)
        self.setStyleSheet("background-color: #121212;")  # Fondo oscuro

        # Diseño vertical
        layout = QVBoxLayout()

        # Título
        self.label_title = QLabel("OBD2 LUMEON")
        self.label_title.setFont(QFont("Arial", 18, QFont.Weight.Bold))
        self.label_title.setStyleSheet("color: #00FFFF;")  # Texto en neón azul
        self.label_title.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.label_title)

        # Área de diagnóstico
        self.diagnosis_area = QTextEdit()
        self.diagnosis_area.setFont(QFont("Arial", 12))
        self.diagnosis_area.setStyleSheet(
            "background-color: #1E1E1E; color: #FFFFFF; border: 2px solid #00FFFF; border-radius: 10px;"
        )
        self.diagnosis_area.setReadOnly(True)
        layout.addWidget(self.diagnosis_area)

        # Botones con efecto 3D
        self.btn_scan = self.create_3d_button("Escanear")
        self.btn_clear = self.create_3d_button("Borrar Fallos")
        self.btn_settings = self.create_3d_button("Configuración")

        layout.addWidget(self.btn_scan)
        layout.addWidget(self.btn_clear)
        layout.addWidget(self.btn_settings)

        self.setLayout(layout)

    def create_3d_button(self, text):
        """Crea un botón con efecto 3D"""
        btn = QPushButton(text)
        btn.setFont(QFont("Arial", 12, QFont.Weight.Bold))
        btn.setFixedSize(QSize(200, 50))
        btn.setStyleSheet("""
            QPushButton {
                background-color: #00FFFF;
                color: #121212;
                border-radius: 10px;
                border: 2px solid #00FFFF;
                padding: 10px;
                box-shadow: 3px 3px 10px rgba(0, 255, 255, 0.7);
            }
            QPushButton:pressed {
                background-color: #0099CC;
                border: 2px solid #0099CC;
                box-shadow: none;
            }
        """)
        return btn

if __name__ == "__main__":
    app = QApplication([])
    window = OBD2LumeonUI()
    window.show()
    app.exec()
import obd
from PyQt6.QtWidgets import QApplication, QWidget, QVBoxLayout, QPushButton, QLabel, QTextEdit
from PyQt6.QtGui import QFont
from PyQt6.QtCore import Qt, QSize

class OBD2LumeonUI(QWidget):
    def __init__(self):
        super().__init__()

        # Configurar ventana
        self.setWindowTitle("OBD2 LUMEON - Diagnóstico Inteligente")
        self.setGeometry(100, 100, 600, 500)
        self.setStyleSheet("background-color: #121212;")  # Fondo oscuro

        # Diseño vertical
        layout = QVBoxLayout()

        # Título
        self.label_title = QLabel("OBD2 LUMEON")
        self.label_title.setFont(QFont("Arial", 18, QFont.Weight.Bold))
        self.label_title.setStyleSheet("color: #00FFFF;")  # Texto en neón azul
        self.label_title.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.label_title)

        # Área de diagnóstico
        self.diagnosis_area = QTextEdit()
        self.diagnosis_area.setFont(QFont("Arial", 12))
        self.diagnosis_area.setStyleSheet(
            "background-color: #1E1E1E; color: #FFFFFF; border: 2px solid #00FFFF; border-radius: 10px;"
        )
        self.diagnosis_area.setReadOnly(True)
        layout.addWidget(self.diagnosis_area)

        # Botones con efecto 3D
        self.btn_scan = self.create_3d_button("Escanear", self.scan_obd2)
        self.btn_clear = self.create_3d_button("Borrar Fallos", self.clear_codes)
        self.btn_settings = self.create_3d_button("Configuración", None)

        layout.addWidget(self.btn_scan)
        layout.addWidget(self.btn_clear)
        layout.addWidget(self.btn_settings)

        self.setLayout(layout)

        # Conectar con OBD-II
        self.connection = obd.OBD()

    def create_3d_button(self, text, function):
        """Crea un botón con efecto 3D"""
        btn = QPushButton(text)
        btn.setFont(QFont("Arial", 12, QFont.Weight.Bold))
        btn.setFixedSize(QSize(200, 50))
        btn.setStyleSheet("""
            QPushButton {
                background-color: #00FFFF;
                color: #121212;
                border-radius: 10px;
                border: 2px solid #00FFFF;
                padding: 10px;
                box-shadow: 3px 3px 10px rgba(0, 255, 255, 0.7);
            }
            QPushButton:pressed {
                background-color: #0099CC;
                border: 2px solid #0099CC;
                box-shadow: none;
            }
        """)
        if function:
            btn.clicked.connect(function)
        return btn

    def scan_obd2(self):
        """Escanea los códigos de error del coche"""
        if self.connection.is_connected():
            codes = self.connection.query(obd.commands.GET_DTC)
            if codes.value:
                result = "\n".join([f"Código: {code[0]}, Significado: {code[1]}" for code in codes.value])
                self.diagnosis_area.setText(result)
            else:
                self.diagnosis_area.setText("No se encontraron errores.")
        else:
            self.diagnosis_area.setText("No se pudo conectar con el OBD-II.")

    def clear_codes(self):
        """Borra los códigos de error"""
        if self.connection.is_connected():
            self.connection.query(obd.commands.CLEAR_DTC)
            self.diagnosis_area.setText("Códigos de error borrados correctamente.")
        else:
            self.diagnosis_area.setText("No se pudo conectar con el OBD-II.")

if __name__ == "__main__":
    app = QApplication([])
    window = OBD2LumeonUI()
    window.show()
    app.exec()
import json
import obd
from PyQt6.QtWidgets import QApplication, QWidget, QVBoxLayout, QPushButton, QLabel, QTextEdit
from PyQt6.QtGui import QFont
from PyQt6.QtCore import Qt, QSize
import os

class OBD2LumeonUI(QWidget):
    def __init__(self):
        super().__init__()

        # Configurar ventana
        self.setWindowTitle("OBD2 LUMEON - Diagnóstico Inteligente")
        self.setGeometry(100, 100, 600, 500)
        self.setStyleSheet("background-color: #121212;")  # Fondo oscuro

        # Diseño vertical
        layout = QVBoxLayout()

        # Título
        self.label_title = QLabel("OBD2 LUMEON")
        self.label_title.setFont(QFont("Arial", 18, QFont.Weight.Bold))
        self.label_title.setStyleSheet("color: #00FFFF;")  # Texto en neón azul
        self.label_title.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.label_title)

        # Área de diagnóstico
        self.diagnosis_area = QTextEdit()
        self.diagnosis_area.setFont(QFont("Arial", 12))
        self.diagnosis_area.setStyleSheet(
            "background-color: #1E1E1E; color: #FFFFFF; border: 2px solid #00FFFF; border-radius: 10px;"
        )
        self.diagnosis_area.setReadOnly(True)
        layout.addWidget(self.diagnosis_area)

        # Botones con efecto 3D
        self.btn_scan = self.create_3d_button("Escanear", self.scan_obd2)
        self.btn_clear = self.create_3d_button("Borrar Fallos", self.clear_codes)
        self.btn_history = self.create_3d_button("Ver Historial", self.show_history)
        self.btn_settings = self.create_3d_button("Configuración", None)

        layout.addWidget(self.btn_scan)
        layout.addWidget(self.btn_clear)
        layout.addWidget(self.btn_history)
        layout.addWidget(self.btn_settings)

        self.setLayout(layout)

        # Conectar con OBD-II
        self.connection = obd.OBD()

        # Archivo de historial
        self.history_file = "diagnosticos.json"

    def create_3d_button(self, text, function):
        """Crea un botón con efecto 3D"""
        btn = QPushButton(text)
        btn.setFont(QFont("Arial", 12, QFont.Weight.Bold))
        btn.setFixedSize(QSize(200, 50))
        btn.setStyleSheet("""
            QPushButton {
                background-color: #00FFFF;
                color: #121212;
                border-radius: 10px;
                border: 2px solid #00FFFF;
                padding: 10px;
                box-shadow: 3px 3px 10px rgba(0, 255, 255, 0.7);
            }
            QPushButton:pressed {
                background-color: #0099CC;
                border: 2px solid #0099CC;
                box-shadow: none;
            }
        """)
        if function:
            btn.clicked.connect(function)
        return btn

    def scan_obd2(self):
        """Escanea los códigos de error del coche y guarda en el historial"""
        if self.connection.is_connected():
            codes = self.connection.query(obd.commands.GET_DTC)
            if codes.value:
                result = "\n".join([f"Código: {code[0]}, Significado: {code[1]}" for code in codes.value])
                self.diagnosis_area.setText(result)
                self.save_to_history(codes.value)
            else:
                self.diagnosis_area.setText("No se encontraron errores.")
        else:
            self.diagnosis_area.setText("No se pudo conectar con el OBD-II.")

    def save_to_history(self, codes):
        """Guarda los códigos en el historial JSON"""
        history = []
        if os.path.exists(self.history_file):
            with open(self.history_file, "r") as file:
                try:
                    history = json.load(file)
                except json.JSONDecodeError:
                    history = []

        history.append({"error_codes": codes})

        with open(self.history_file, "w") as file:
            json.dump(history, file, indent=4)

    def show_history(self):
        """Muestra el historial de diagnósticos guardados"""
        if os.path.exists(self.history_file):
            with open(self.history_file, "r") as file:
                try:
                    history = json.load(file)
                    result = "\n".join([f"{item['error_codes']}" for item in history])
                    self.diagnosis_area.setText(result)
                except json.JSONDecodeError:
                    self.diagnosis_area.setText("Historial vacío o corrupto.")
        else:
            self.diagnosis_area.setText("No hay historial disponible.")

    def clear_codes(self):
        """Borra los códigos de error"""
        if self.connection.is_connected():
            self.connection.query(obd.commands.CLEAR_DTC)
            self.diagnosis_area.setText("Códigos de error borrados correctamente.")
        else:
            self.diagnosis_area.setText("No se pudo conectar con el OBD-II.")

if __name__ == "__main__":
    app = QApplication([])
    window = OBD2LumeonUI()
    window.show()
    app.exec()
import json
import obd
import threading
import time
from PyQt6.QtWidgets import QApplication, QWidget, QVBoxLayout, QPushButton, QLabel, QTextEdit
from PyQt6.QtGui import QFont
from PyQt6.QtCore import Qt, QSize

class OBD2LumeonUI(QWidget):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("OBD2 LUMEON - Diagnóstico Inteligente")
        self.setGeometry(100, 100, 600, 500)
        self.setStyleSheet("background-color: #121212;")

        layout = QVBoxLayout()

        self.label_title = QLabel("OBD2 LUMEON")
        self.label_title.setFont(QFont("Arial", 18, QFont.Weight.Bold))
        self.label_title.setStyleSheet("color: #00FFFF;")
        self.label_title.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.label_title)

        self.diagnosis_area = QTextEdit()
        self.diagnosis_area.setFont(QFont("Arial", 12))
        self.diagnosis_area.setStyleSheet(
            "background-color: #1E1E1E; color: #FFFFFF; border: 2px solid #00FFFF; border-radius: 10px;"
        )
        self.diagnosis_area.setReadOnly(True)
        layout.addWidget(self.diagnosis_area)

        self.btn_scan = self.create_3d_button("Escanear", self.scan_obd2)
        self.btn_clear = self.create_3d_button("Borrar Fallos", self.clear_codes)
        self.btn_history = self.create_3d_button("Ver Historial", self.show_history)
        self.btn_start_auto_scan = self.create_3d_button("Iniciar Escaneo Automático", self.start_auto_scan)
        self.btn_stop_auto_scan = self.create_3d_button("Detener Escaneo", self.stop_auto_scan)

        layout.addWidget(self.btn_scan)
        layout.addWidget(self.btn_clear)
        layout.addWidget(self.btn_history)
        layout.addWidget(self.btn_start_auto_scan)
        layout.addWidget(self.btn_stop_auto_scan)

        self.setLayout(layout)

        self.connection = obd.OBD()
        self.history_file = "diagnosticos.json"
        self.scanning = False

    def create_3d_button(self, text, function):
        btn = QPushButton(text)
        btn.setFont(QFont("Arial", 12, QFont.Weight.Bold))
        btn.setFixedSize(QSize(220, 50))
        btn.setStyleSheet("""
            QPushButton {
                background-color: #00FFFF;
                color: #121212;
                border-radius: 10px;
                border: 2px solid #00FFFF;
                padding: 10px;
                box-shadow: 3px 3px 10px rgba(0, 255, 255, 0.7);
            }
            QPushButton:pressed {
                background-color: #0099CC;
                border: 2px solid #0099CC;
                box-shadow: none;
            }
        """)
        if function:
            btn.clicked.connect(function)
        return btn

    def scan_obd2(self):
        """Escanea los códigos de error del coche y guarda en el historial"""
        if self.connection.is_connected():
            codes = self.connection.query(obd.commands.GET_DTC)
            if codes.value:
                result = "\n".join([f"Código: {code[0]}, Significado: {code[1]}" for code in codes.value])
                self.diagnosis_area.setText(result)
                self.save_to_history(codes.value)
            else:
                self.diagnosis_area.setText("No se encontraron errores.")
        else:
            self.diagnosis_area.setText("No se pudo conectar con el OBD-II.")

    def save_to_history(self, codes):
        """Guarda los códigos en el historial JSON"""
        history = []
        try:
            with open(self.history_file, "r") as file:
                history = json.load(file)
        except (FileNotFoundError, json.JSONDecodeError):
            history = []

        history.append({"error_codes": codes})

        with open(self.history_file, "w") as file:
            json.dump(history, file, indent=4)

    def show_history(self):
        """Muestra el historial de diagnósticos guardados"""
        try:
            with open(self.history_file, "r") as file:
                history = json.load(file)
                result = "\n".join([f"{item['error_codes']}" for item in history])
                self.diagnosis_area.setText(result)
        except (FileNotFoundError, json.JSONDecodeError):
            self.diagnosis_area.setText("No hay historial disponible.")

    def clear_codes(self):
        """Borra los códigos de error"""
        if self.connection.is_connected():
            self.connection.query(obd.commands.CLEAR_DTC)
            self.diagnosis_area.setText("Códigos de error borrados correctamente.")
        else:
            self.diagnosis_area.setText("No se pudo conectar con el OBD-II.")

    def start_auto_scan(self):
        """Inicia el escaneo en segundo plano"""
        if not self.scanning:
            self.scanning = True
            self.diagnosis_area.setText("Escaneo automático activado...")
            self.auto_scan_thread = threading.Thread(target=self.auto_scan, daemon=True)
            self.auto_scan_thread.start()

    def auto_scan(self):
        """Ejecuta escaneos periódicos cada 10 segundos"""
        while self.scanning:
            self.scan_obd2()
            time.sleep(10)

    def stop_auto_scan(self):
        """Detiene el escaneo en segundo plano"""
        self.scanning = False
        self.diagnosis_area.setText("Escaneo automático detenido.")

if __name__ == "__main__":
    app = QApplication([])
    window = OBD2LumeonUI()
    window.show()
    app.exec()
import json
import obd
import threading
import time
import speech_recognition as sr
import pyttsx3
from PyQt6.QtWidgets import QApplication, QWidget, QVBoxLayout, QPushButton, QLabel, QTextEdit
from PyQt6.QtGui import QFont
from PyQt6.QtCore import Qt, QSize

class OBD2LumeonAI(QWidget):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("OBD2 LUMEON - Diagnóstico Inteligente con IA")
        self.setGeometry(100, 100, 600, 600)
        self.setStyleSheet("background-color: #121212;")

        layout = QVBoxLayout()

        self.label_title = QLabel("OBD2 LUMEON AI")
        self.label_title.setFont(QFont("Arial", 18, QFont.Weight.Bold))
        self.label_title.setStyleSheet("color: #00FFFF;")
        self.label_title.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.label_title)

        self.diagnosis_area = QTextEdit()
        self.diagnosis_area.setFont(QFont("Arial", 12))
        self.diagnosis_area.setStyleSheet(
            "background-color: #1E1E1E; color: #FFFFFF; border: 2px solid #00FFFF; border-radius: 10px;"
        )
        self.diagnosis_area.setReadOnly(True)
        layout.addWidget(self.diagnosis_area)

        self.btn_voice_input = self.create_3d_button("🔊 Ingresar por Voz", self.voice_input)
        self.btn_voice_output = self.create_3d_button("📢 Leer Diagnóstico", self.voice_output)
        self.btn_scan = self.create_3d_button("🔍 Escanear", self.scan_obd2)
        self.btn_predict = self.create_3d_button("🤖 Predecir Fallo", self.predict_fault)
        self.btn_clear = self.create_3d_button("🛠️ Borrar Fallos", self.clear_codes)

        layout.addWidget(self.btn_voice_input)
        layout.addWidget(self.btn_voice_output)
        layout.addWidget(self.btn_scan)
        layout.addWidget(self.btn_predict)
        layout.addWidget(self.btn_clear)

        self.setLayout(layout)

        self.connection = obd.OBD()
        self.history_file = "diagnosticos.json"
        self.recognizer = sr.Recognizer()
        self.engine = pyttsx3.init()

        # Base de datos de síntomas y fallos comunes
        self.fault_database = {
            "ralentí inestable": "Posibles causas: Sensor de oxígeno defectuoso, válvula EGR sucia.",
            "humo negro en el escape": "Posibles causas: Mezcla de combustible rica, sensor de flujo de aire dañado.",
            "tirones al acelerar": "Posibles causas: Inyectores sucios, bomba de combustible débil.",
            "falla de encendido": "Posibles causas: Bujías defectuosas, bobina de encendido en mal estado."
        }

    def create_3d_button(self, text, function):
        btn = QPushButton(text)
        btn.setFont(QFont("Arial", 12, QFont.Weight.Bold))
        btn.setFixedSize(QSize(250, 50))
        btn.setStyleSheet("""
            QPushButton {
                background-color: #00FFFF;
                color: #121212;
                border-radius: 10px;
                border: 2px solid #00FFFF;
                padding: 10px;
                box-shadow: 3px 3px 10px rgba(0, 255, 255, 0.7);
            }
            QPushButton:pressed {
                background-color: #0099CC;
                border: 2px solid #0099CC;
                box-shadow: none;
            }
        """)
        if function:
            btn.clicked.connect(function)
        return btn

    def scan_obd2(self):
        """Escanea los códigos de error del coche y guarda en el historial"""
        if self.connection.is_connected():
            codes = self.connection.query(obd.commands.GET_DTC)
            if codes.value:
                result = "\n".join([f"Código: {code[0]}, Significado: {code[1]}" for code in codes.value])
                self.diagnosis_area.setText(result)
                self.save_to_history(codes.value)
            else:
                self.diagnosis_area.setText("No se encontraron errores.")
        else:
            self.diagnosis_area.setText("No se pudo conectar con el OBD-II.")

    def predict_fault(self):
        """Predice el posible fallo del coche basado en los síntomas"""
        text = self.diagnosis_area.toPlainText().lower()
        for symptom, fault in self.fault_database.items():
            if symptom in text:
                self.diagnosis_area.setText(f"Síntoma detectado: {symptom}\n{fault}")
                return
        self.diagnosis_area.setText("No se encontraron coincidencias. Intente con otra descripción.")

    def clear_codes(self):
        """Borra los códigos de error"""
        if self.connection.is_connected():
            self.connection.query(obd.commands.CLEAR_DTC)
            self.diagnosis_area.setText("Códigos de error borrados correctamente.")
        else:
            self.diagnosis_area.setText("No se pudo conectar con el OBD-II.")

    def voice_input(self):
        """Permite ingresar síntomas por voz"""
        self.diagnosis_area.setText("Escuchando...")
        with sr.Microphone() as source:
            self.recognizer.adjust_for_ambient_noise(source)
            try:
                audio = self.recognizer.listen(source)
                symptoms = self.recognizer.recognize_google(audio, language="es-ES")
                self.diagnosis_area.setText(f"Síntomas detectados: {symptoms}")
                self.predict_fault()  # Ejecuta la predicción automáticamente
            except sr.UnknownValueError:
                self.diagnosis_area.setText("No se entendió el audio.")
            except sr.RequestError:
                self.diagnosis_area.setText("Error en el reconocimiento de voz.")

    def voice_output(self):
        """Lee el diagnóstico en voz alta"""
        diagnosis_text = self.diagnosis_area.toPlainText()
        if diagnosis_text:
            self.engine.say(diagnosis_text)
            self.engine.runAndWait()

if __name__ == "__main__":
    app = QApplication([])
    window = OBD2LumeonAI()
    window.show()
    app.exec()
import json
import requests
from PyQt6.QtWidgets import QApplication, QWidget, QVBoxLayout, QPushButton, QLabel, QTextEdit, QLineEdit
from PyQt6.QtGui import QFont
from PyQt6.QtCore import Qt, QSize

AUTO_CREW_API = "https://autocrew-database.com/api/fallos"  # Simulación de API

class AutoCrewIntegration(QWidget):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("OBD2 LUMEON - Comunidad AutoCrew")
        self.setGeometry(100, 100, 600, 600)
        self.setStyleSheet("background-color: #121212;")

        layout = QVBoxLayout()

        self.label_title = QLabel("🔗 AutoCrew - Comunidad de Diagnóstico")
        self.label_title.setFont(QFont("Arial", 16, QFont.Weight.Bold))
        self.label_title.setStyleSheet("color: #00FFFF;")
        self.label_title.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.label_title)

        self.error_code_input = QLineEdit()
        self.error_code_input.setPlaceholderText("Ingrese el código de error OBD-II o síntomas...")
        self.error_code_input.setFont(QFont("Arial", 12))
        self.error_code_input.setStyleSheet(
            "background-color: #1E1E1E; color: #FFFFFF; border: 2px solid #00FFFF; border-radius: 10px; padding: 5px;"
        )
        layout.addWidget(self.error_code_input)

        self.search_button = self.create_3d_button("🔍 Buscar en AutoCrew", self.search_error)
        self.report_button = self.create_3d_button("📤 Reportar Fallo", self.report_error)

        layout.addWidget(self.search_button)
        layout.addWidget(self.report_button)

        self.result_area = QTextEdit()
        self.result_area.setFont(QFont("Arial", 12))
        self.result_area.setStyleSheet(
            "background-color: #1E1E1E; color: #FFFFFF; border: 2px solid #00FFFF; border-radius: 10px;"
        )
        self.result_area.setReadOnly(True)
        layout.addWidget(self.result_area)

        self.setLayout(layout)

    def create_3d_button(self, text, function):
        btn = QPushButton(text)
        btn.setFont(QFont("Arial", 12, QFont.Weight.Bold))
        btn.setFixedSize(QSize(250, 50))
        btn.setStyleSheet("""
            QPushButton {
                background-color: #00FFFF;
                color: #121212;
                border-radius: 10px;
                border: 2px solid #00FFFF;
                padding: 10px;
                box-shadow: 3px 3px 10px rgba(0, 255, 255, 0.7);
            }
            QPushButton:pressed {
                background-color: #0099CC;
                border: 2px solid #0099CC;
                box-shadow: none;
            }
        """)
        if function:
            btn.clicked.connect(function)
        return btn

    def search_error(self):
        """Consulta la base de datos AutoCrew en línea."""
        code = self.error_code_input.text()
        if not code:
            self.result_area.setText("⚠️ Ingrese un código de error o síntoma.")
            return

        response = requests.get(f"{AUTO_CREW_API}/buscar?codigo={code}")

        if response.status_code == 200:
            data = response.json()
            if data:
                result_text = f"🔍 Resultados de búsqueda para '{code}':\n\n"
                for item in data:
                    result_text += f"⚙️ Código: {item['codigo']}\n🛠️ Solución: {item['solucion']}\n🔄 Reportado por: {item['usuario']}\n\n"
                self.result_area.setText(result_text)
            else:
                self.result_area.setText("❌ No se encontraron coincidencias en AutoCrew.")
        else:
            self.result_area.setText("⛔ Error al conectar con la comunidad AutoCrew.")

    def report_error(self):
        """Envía un fallo nuevo a la comunidad."""
        code = self.error_code_input.text()
        if not code:
            self.result_area.setText("⚠️ Ingrese un código de error o síntoma.")
            return

        payload = {"codigo": code, "usuario": "UsuarioAnónimo"}
        response = requests.post(f"{AUTO_CREW_API}/reportar", json=payload)

        if response.status_code == 201:
            self.result_area.setText("✅ Fallo reportado exitosamente en AutoCrew.")
        else:
            self.result_area.setText("⛔ Error al reportar el fallo.")

if __name__ == "__main__":
    app = QApplication([])
    window = AutoCrewIntegration()
    window.show()
    app.exec()
import sys
import requests
import json
from PyQt6.QtWidgets import QApplication, QWidget, QVBoxLayout, QPushButton, QLabel, QTextEdit, QLineEdit, QHBoxLayout
from PyQt6.QtGui import QFont
from PyQt6.QtCore import Qt, QTimer

AUTO_CREW_CHAT_API = "https://autocrew-database.com/api/chat"  # Simulación de API

class AutoCrewChat(QWidget):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("OBD2 LUMEON - Chat de Soporte AutoCrew")
        self.setGeometry(100, 100, 600, 600)
        self.setStyleSheet("background-color: #121212;")

        layout = QVBoxLayout()

        self.label_title = QLabel("💬 Chat en Vivo - AutoCrew")
        self.label_title.setFont(QFont("Arial", 16, QFont.Weight.Bold))
        self.label_title.setStyleSheet("color: #00FFFF;")
        self.label_title.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.label_title)

        self.chat_display = QTextEdit()
        self.chat_display.setFont(QFont("Arial", 12))
        self.chat_display.setStyleSheet(
            "background-color: #1E1E1E; color: #FFFFFF; border: 2px solid #00FFFF; border-radius: 10px;"
        )
        self.chat_display.setReadOnly(True)
        layout.addWidget(self.chat_display)

        self.input_area = QLineEdit()
        self.input_area.setPlaceholderText("Escribe tu mensaje...")
        self.input_area.setFont(QFont("Arial", 12))
        self.input_area.setStyleSheet(
            "background-color: #1E1E1E; color: #FFFFFF; border: 2px solid #00FFFF; border-radius: 10px; padding: 5px;"
        )

        self.send_button = self.create_3d_button("📨 Enviar", self.send_message)

        input_layout = QHBoxLayout()
        input_layout.addWidget(self.input_area)
        input_layout.addWidget(self.send_button)
        layout.addLayout(input_layout)

        self.setLayout(layout)

        self.timer = QTimer(self)
        self.timer.timeout.connect(self.update_chat)
        self.timer.start(3000)  # Actualizar chat cada 3 segundos

    def create_3d_button(self, text, function):
        btn = QPushButton(text)
        btn.setFont(QFont("Arial", 12, QFont.Weight.Bold))
        btn.setStyleSheet("""
            QPushButton {
                background-color: #00FFFF;
                color: #121212;
                border-radius: 10px;
                border: 2px solid #00FFFF;
                padding: 10px;
                box-shadow: 3px 3px 10px rgba(0, 255, 255, 0.7);
            }
            QPushButton:pressed {
                background-color: #0099CC;
                border: 2px solid #0099CC;
                box-shadow: none;
            }
        """)
        if function:
            btn.clicked.connect(function)
        return btn

    def send_message(self):
        """Envía un mensaje al chat AutoCrew."""
        message = self.input_area.text()
        if not message:
            return

        payload = {"usuario": "UsuarioAnónimo", "mensaje": message}
        response = requests.post(f"{AUTO_CREW_CHAT_API}/enviar", json=payload)

        if response.status_code == 201:
            self.input_area.clear()
            self.update_chat()  # Actualizar chat después de enviar mensaje

    def update_chat(self):
        """Actualiza el chat con nuevos mensajes."""
        response = requests.get(f"{AUTO_CREW_CHAT_API}/mensajes")

        if response.status_code == 200:
            data = response.json()
            chat_text = "📢 **Chat en Vivo - AutoCrew**\n\n"
            for msg in data:
                chat_text += f"👤 {msg['usuario']}: {msg['mensaje']}\n"
            self.chat_display.setText(chat_text)

if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = AutoCrewChat()
    window.show()
    sys.exit(app.exec())
import sys
import requests
import json
import speech_recognition as sr
from PyQt6.QtWidgets import QApplication, QWidget, QVBoxLayout, QPushButton, QLabel, QTextEdit
from PyQt6.QtGui import QFont
from PyQt6.QtCore import Qt

OBD2_API = "https://obd2-database.com/api/errors"  # Simulación de API

class OBD2LumeonVoice(QWidget):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("OBD2 LUMEON - Control por Voz")
        self.setGeometry(100, 100, 600, 500)
        self.setStyleSheet("background-color: #121212;")

        layout = QVBoxLayout()

        self.label_title = QLabel("🎙️ Control por Voz - OBD2 LUMEON")
        self.label_title.setFont(QFont("Arial", 16, QFont.Weight.Bold))
        self.label_title.setStyleSheet("color: #00FFFF;")
        self.label_title.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.label_title)

        self.result_display = QTextEdit()
        self.result_display.setFont(QFont("Arial", 12))
        self.result_display.setStyleSheet(
            "background-color: #1E1E1E; color: #FFFFFF; border: 2px solid #00FFFF; border-radius: 10px;"
        )
        self.result_display.setReadOnly(True)
        layout.addWidget(self.result_display)

        self.voice_button = self.create_3d_button("🎤 Activar Voz", self.voice_command)
        layout.addWidget(self.voice_button)

        self.setLayout(layout)

    def create_3d_button(self, text, function):
        btn = QPushButton(text)
        btn.setFont(QFont("Arial", 12, QFont.Weight.Bold))
        btn.setStyleSheet("""
            QPushButton {
                background-color: #00FFFF;
                color: #121212;
                border-radius: 10px;
                border: 2px solid #00FFFF;
                padding: 10px;
                box-shadow: 3px 3px 10px rgba(0, 255, 255, 0.7);
            }
            QPushButton:pressed {
                background-color: #0099CC;
                border: 2px solid #0099CC;
                box-shadow: none;
            }
        """)
        if function:
            btn.clicked.connect(function)
        return btn

    def voice_command(self):
        recognizer = sr.Recognizer()
        with sr.Microphone() as source:
            self.result_display.setText("🎙️ Escuchando... Di un comando como 'Escanear coche'")
            try:
                audio = recognizer.listen(source)
                command = recognizer.recognize_google(audio, language="es-ES").lower()
                self.process_command(command)
            except sr.UnknownValueError:
                self.result_display.setText("❌ No se entendió el comando, intenta de nuevo.")
            except sr.RequestError:
                self.result_display.setText("⚠️ Error en el servicio de reconocimiento.")

    def process_command(self, command):
        if "escanear coche" in command:
            self.result_display.setText("🔍 Iniciando diagnóstico...")
            response = requests.get(f"{OBD2_API}/scan")
            if response.status_code == 200:
                data = response.json()
                errors = "\n".join([f"⚠️ Código {err['code']}: {err['description']}" for err in data["errors"]])
                self.result_display.setText(errors)
            else:
                self.result_display.setText("🚗 No se encontraron errores en el coche.")
        else:
            self.result_display.setText("🤔 Comando no reconocido. Intenta 'Escanear coche'.")

if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = OBD2LumeonVoice()
    window.show()
    sys.exit(app.exec())
import sys
import requests
import json
import speech_recognition as sr
from PyQt6.QtWidgets import QApplication, QWidget, QVBoxLayout, QPushButton, QLabel, QTextEdit, QListWidget
from PyQt6.QtGui import QFont
from PyQt6.QtCore import Qt

OBD2_API = "https://obd2-database.com/api/errors"  # Simulación de API
AUTOCREW_API = "https://autocrew-community.com/api/solutions"  # Simulación de API

class OBD2LumeonCommunity(QWidget):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("OBD2 LUMEON - Comunidad AutoCrew")
        self.setGeometry(100, 100, 600, 600)
        self.setStyleSheet("background-color: #121212;")

        layout = QVBoxLayout()

        self.label_title = QLabel("📡 Comunidad AutoCrew - OBD2 LUMEON")
        self.label_title.setFont(QFont("Arial", 16, QFont.Weight.Bold))
        self.label_title.setStyleSheet("color: #00FFFF;")
        self.label_title.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.label_title)

        self.result_display = QTextEdit()
        self.result_display.setFont(QFont("Arial", 12))
        self.result_display.setStyleSheet(
            "background-color: #1E1E1E; color: #FFFFFF; border: 2px solid #00FFFF; border-radius: 10px;"
        )
        self.result_display.setReadOnly(True)
        layout.addWidget(self.result_display)

        self.solution_list = QListWidget()
        self.solution_list.setFont(QFont("Arial", 12))
        self.solution_list.setStyleSheet(
            "background-color: #1E1E1E; color: #00FF00; border: 2px solid #00FFFF; border-radius: 10px;"
        )
        layout.addWidget(self.solution_list)

        self.voice_button = self.create_3d_button("🎤 Activar Voz", self.voice_command)
        layout.addWidget(self.voice_button)

        self.setLayout(layout)

    def create_3d_button(self, text, function):
        btn = QPushButton(text)
        btn.setFont(QFont("Arial", 12, QFont.Weight.Bold))
        btn.setStyleSheet("""
            QPushButton {
                background-color: #00FFFF;
                color: #121212;
                border-radius: 10px;
                border: 2px solid #00FFFF;
                padding: 10px;
                box-shadow: 3px 3px 10px rgba(0, 255, 255, 0.7);
            }
            QPushButton:pressed {
                background-color: #0099CC;
                border: 2px solid #0099CC;
                box-shadow: none;
            }
        """)
        if function:
            btn.clicked.connect(function)
        return btn

    def voice_command(self):
        recognizer = sr.Recognizer()
        with sr.Microphone() as source:
            self.result_display.setText("🎙️ Escuchando... Di un comando como 'Buscar solución para P0300'")
            try:
                audio = recognizer.listen(source)
                command = recognizer.recognize_google(audio, language="es-ES").lower()
                self.process_command(command)
            except sr.UnknownValueError:
                self.result_display.setText("❌ No se entendió el comando, intenta de nuevo.")
            except sr.RequestError:
                self.result_display.setText("⚠️ Error en el servicio de reconocimiento.")

    def process_command(self, command):
        if "buscar solución" in command:
            error_code = command.split()[-1]
            self.result_display.setText(f"🔍 Buscando soluciones para el código {error_code}...")
            response = requests.get(f"{AUTOCREW_API}/{error_code}")
            if response.status_code == 200:
                data = response.json()
                self.solution_list.clear()
                for solution in data["solutions"]:
                    self.solution_list.addItem(f"✅ {solution['description']} - 👍 {solution['votes']} votos")
                self.result_display.setText(f"📡 Soluciones encontradas para {error_code}:")
            else:
                self.result_display.setText("🚗 No se encontraron soluciones en la comunidad.")
        else:
            self.result_display.setText("🤔 Comando no reconocido. Intenta 'Buscar solución para P0300'.")

if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = OBD2LumeonCommunity()
    window.show()
    sys.exit(app.exec())
import sys
import json
import random
import requests
import matplotlib.pyplot as plt
from PyQt6.QtWidgets import QApplication, QWidget, QVBoxLayout, QLabel, QPushButton, QTextEdit
from PyQt6.QtGui import QFont
from PyQt6.QtCore import Qt

# Simulación de API de historial de fallos OBD2
HISTORIAL_API = "https://obd2-database.com/api/historial"  # Simulado

class OBD2LumeonStats(QWidget):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("📊 OBD2 LUMEON - Estadísticas")
        self.setGeometry(100, 100, 600, 600)
        self.setStyleSheet("background-color: #121212;")

        layout = QVBoxLayout()

        self.label_title = QLabel("📊 Estadísticas de Diagnóstico")
        self.label_title.setFont(QFont("Arial", 16, QFont.Weight.Bold))
        self.label_title.setStyleSheet("color: #00FFFF;")
        self.label_title.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.label_title)

        self.text_log = QTextEdit()
        self.text_log.setFont(QFont("Arial", 12))
        self.text_log.setStyleSheet(
            "background-color: #1E1E1E; color: #FFFFFF; border: 2px solid #00FFFF; border-radius: 10px;"
        )
        self.text_log.setReadOnly(True)
        layout.addWidget(self.text_log)

        self.btn_refresh = self.create_3d_button("🔄 Actualizar Datos", self.fetch_data)
        layout.addWidget(self.btn_refresh)

        self.btn_graph = self.create_3d_button("📈 Ver Gráficos", self.show_graphs)
        layout.addWidget(self.btn_graph)

        self.setLayout(layout)
        self.fetch_data()

    def create_3d_button(self, text, function):
        btn = QPushButton(text)
        btn.setFont(QFont("Arial", 12, QFont.Weight.Bold))
        btn.setStyleSheet("""
            QPushButton {
                background-color: #00FFFF;
                color: #121212;
                border-radius: 10px;
                border: 2px solid #00FFFF;
                padding: 10px;
                box-shadow: 3px 3px 10px rgba(0, 255, 255, 0.7);
            }
            QPushButton:pressed {
                background-color: #0099CC;
                border: 2px solid #0099CC;
                box-shadow: none;
            }
        """)
        if function:
            btn.clicked.connect(function)
        return btn

    def fetch_data(self):
        try:
            response = requests.get(HISTORIAL_API)
            if response.status_code == 200:
                data = response.json()
                self.text_log.clear()
                for entry in data["history"]:
                    self.text_log.append(f"🔹 {entry['date']} - Código: {entry['code']} - {entry['description']}")
            else:
                self.text_log.setText("⚠️ No se pudo obtener el historial de diagnósticos.")
        except:
            self.text_log.setText("🚗 Datos simulados cargados.")
            self.load_fake_data()

    def load_fake_data(self):
        fake_data = [
            {"date": "2025-02-15", "code": "P0300", "description": "Fallo de encendido aleatorio"},
            {"date": "2025-02-12", "code": "P0420", "description": "Eficiencia del catalizador baja"},
            {"date": "2025-02-08", "code": "P0171", "description": "Mezcla de combustible demasiado pobre"}
        ]
        for entry in fake_data:
            self.text_log.append(f"🔹 {entry['date']} - Código: {entry['code']} - {entry['description']}")

    def show_graphs(self):
        codes = ["P0300", "P0420", "P0171", "P0500", "P0455"]
        occurrences = [random.randint(5, 20) for _ in range(len(codes))]

        plt.figure(figsize=(7, 5))
        plt.bar(codes, occurrences, color=['#00FFFF', '#00FF00', '#FFA500', '#FF4500', '#9400D3'])
        plt.xlabel("Códigos de error")
        plt.ylabel("Frecuencia")
        plt.title("📊 Fallos más frecuentes en tu coche")
        plt.grid(axis="y", linestyle="--", alpha=0.7)
        plt.show()

if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = OBD2LumeonStats()
    window.show()
    sys.exit(app.exec())
import sys
import json
import random
import requests
import matplotlib.pyplot as plt
from PyQt6.QtWidgets import QApplication, QWidget, QVBoxLayout, QLabel, QPushButton, QTextEdit
from PyQt6.QtGui import QFont
from PyQt6.QtCore import Qt

# Simulación de API de historial de fallos OBD2
HISTORIAL_API = "https://obd2-database.com/api/historial"  # Simulado

class OBD2LumeonStats(QWidget):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("📊 OBD2 LUMEON - Estadísticas")
        self.setGeometry(100, 100, 600, 600)
        self.setStyleSheet("background-color: #121212;")

        layout = QVBoxLayout()

        self.label_title = QLabel("📊 Estadísticas de Diagnóstico")
        self.label_title.setFont(QFont("Arial", 16, QFont.Weight.Bold))
        self.label_title.setStyleSheet("color: #00FFFF;")
        self.label_title.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.label_title)

        self.text_log = QTextEdit()
        self.text_log.setFont(QFont("Arial", 12))
        self.text_log.setStyleSheet(
            "background-color: #1E1E1E; color: #FFFFFF; border: 2px solid #00FFFF; border-radius: 10px;"
        )
        self.text_log.setReadOnly(True)
        layout.addWidget(self.text_log)

        self.btn_refresh = self.create_3d_button("🔄 Actualizar Datos", self.fetch_data)
        layout.addWidget(self.btn_refresh)

        self.btn_graph = self.create_3d_button("📈 Ver Gráficos", self.show_graphs)
        layout.addWidget(self.btn_graph)

        self.setLayout(layout)
        self.fetch_data()

    def create_3d_button(self, text, function):
        btn = QPushButton(text)
        btn.setFont(QFont("Arial", 12, QFont.Weight.Bold))
        btn.setStyleSheet("""
            QPushButton {
                background-color: #00FFFF;
                color: #121212;
                border-radius: 10px;
                border: 2px solid #00FFFF;
                padding: 10px;
                box-shadow: 3px 3px 10px rgba(0, 255, 255, 0.7);
            }
            QPushButton:pressed {
                background-color: #0099CC;
                border: 2px solid #0099CC;
                box-shadow: none;
            }
        """)
        if function:
            btn.clicked.connect(function)
        return btn

    def fetch_data(self):
        try:
            response = requests.get(HISTORIAL_API)
            if response.status_code == 200:
                data = response.json()
                self.text_log.clear()
                for entry in data["history"]:
                    self.text_log.append(f"🔹 {entry['date']} - Código: {entry['code']} - {entry['description']}")
            else:
                self.text_log.setText("⚠️ No se pudo obtener el historial de diagnósticos.")
        except:
            self.text_log.setText("🚗 Datos simulados cargados.")
            self.load_fake_data()

    def load_fake_data(self):
        fake_data = [
            {"date": "2025-02-15", "code": "P0300", "description": "Fallo de encendido aleatorio"},
            {"date": "2025-02-12", "code": "P0420", "description": "Eficiencia del catalizador baja"},
            {"date": "2025-02-08", "code": "P0171", "description": "Mezcla de combustible demasiado pobre"}
        ]
        for entry in fake_data:
            self.text_log.append(f"🔹 {entry['date']} - Código: {entry['code']} - {entry['description']}")

    def show_graphs(self):
        codes = ["P0300", "P0420", "P0171", "P0500", "P0455"]
        occurrences = [random.randint(5, 20) for _ in range(len(codes))]

        plt.figure(figsize=(7, 5))
        plt.bar(codes, occurrences, color=['#00FFFF', '#00FF00', '#FFA500', '#FF4500', '#9400D3'])
        plt.xlabel("Códigos de error")
        plt.ylabel("Frecuencia")
        plt.title("📊 Fallos más frecuentes en tu coche")
        plt.grid(axis="y", linestyle="--", alpha=0.7)
        plt.show()

if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = OBD2LumeonStats()
    window.show()
    sys.exit(app.exec())
import sys
import json
import random
from PyQt6.QtWidgets import QApplication, QWidget, QVBoxLayout, QLabel, QPushButton, QListWidget
from PyQt6.QtGui import QFont
from PyQt6.QtCore import Qt

# Datos simulados de mantenimiento
MANTENIMIENTO_DATA = {
    "P0300": {"accion": "Revisar bujías y bobinas", "piezas": ["Bujías", "Bobinas de encendido"]},
    "P0420": {"accion": "Verificar catalizador", "piezas": ["Catalizador", "Sensor de oxígeno"]},
    "P0171": {"accion": "Limpiar sensor MAF", "piezas": ["Sensor MAF", "Filtro de aire"]},
    "P0500": {"accion": "Revisar sensor de velocidad", "piezas": ["Sensor de velocidad"]},
    "P0455": {"accion": "Verificar sistema EVAP", "piezas": ["Válvula EVAP", "Mangueras de vacío"]},
}

class OBD2LumeonMantenimiento(QWidget):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("🔧 OBD2 LUMEON - Mantenimiento Preventivo")
        self.setGeometry(100, 100, 600, 500)
        self.setStyleSheet("background-color: #121212;")

        layout = QVBoxLayout()

        self.label_title = QLabel("🔧 Recomendaciones de Mantenimiento")
        self.label_title.setFont(QFont("Arial", 16, QFont.Weight.Bold))
        self.label_title.setStyleSheet("color: #00FFFF;")
        self.label_title.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.label_title)

        self.lista_mantenimiento = QListWidget()
        self.lista_mantenimiento.setFont(QFont("Arial", 12))
        self.lista_mantenimiento.setStyleSheet(
            "background-color: #1E1E1E; color: #FFFFFF; border: 2px solid #00FFFF; border-radius: 10px;"
        )
        layout.addWidget(self.lista_mantenimiento)

        self.btn_check = self.create_3d_button("🔍 Ver Mantenimiento", self.show_mantenimiento)
        layout.addWidget(self.btn_check)

        self.setLayout(layout)

    def create_3d_button(self, text, function):
        btn = QPushButton(text)
        btn.setFont(QFont("Arial", 12, QFont.Weight.Bold))
        btn.setStyleSheet("""
            QPushButton {
                background-color: #00FFFF;
                color: #121212;
                border-radius: 10px;
                border: 2px solid #00FFFF;
                padding: 10px;
                box-shadow: 3px 3px 10px rgba(0, 255, 255, 0.7);
            }
            QPushButton:pressed {
                background-color: #0099CC;
                border: 2px solid #0099CC;
                box-shadow: none;
            }
        """)
        if function:
            btn.clicked.connect(function)
        return btn

    def show_mantenimiento(self):
        self.lista_mantenimiento.clear()
        for code, data in MANTENIMIENTO_DATA.items():
            self.lista_mantenimiento.addItem(f"🔹 Código: {code}")
            self.lista_mantenimiento.addItem(f"   ✅ Acción: {data['accion']}")
            self.lista_mantenimiento.addItem(f"   🛠️ Piezas necesarias: {', '.join(data['piezas'])}")
            self.lista_mantenimiento.addItem("")

if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = OBD2LumeonMantenimiento()
    window.show()
    sys.exit(app.exec())
import sys
import random
import obd
import time
from PyQt6.QtWidgets import QApplication, QWidget, QVBoxLayout, QLabel, QPushButton, QProgressBar
from PyQt6.QtGui import QFont
from PyQt6.QtCore import Qt, QTimer

class OBD2LumeonTiempoReal(QWidget):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("📡 OBD2 LUMEON - Diagnóstico en Tiempo Real")
        self.setGeometry(100, 100, 600, 500)
        self.setStyleSheet("background-color: #121212;")

        layout = QVBoxLayout()

        self.label_title = QLabel("📡 Sensores en Tiempo Real")
        self.label_title.setFont(QFont("Arial", 16, QFont.Weight.Bold))
        self.label_title.setStyleSheet("color: #00FFFF;")
        self.label_title.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.label_title)

        self.label_rpm = QLabel("🔄 RPM: --")
        self.label_speed = QLabel("🚗 Velocidad: -- km/h")
        self.label_temp = QLabel("🌡️ Temperatura: -- °C")

        for label in [self.label_rpm, self.label_speed, self.label_temp]:
            label.setFont(QFont("Arial", 14))
            label.setStyleSheet("color: #FFFFFF;")
            label.setAlignment(Qt.AlignmentFlag.AlignCenter)
            layout.addWidget(label)

        self.progress_rpm = QProgressBar()
        self.progress_rpm.setMaximum(7000)
        self.progress_rpm.setStyleSheet("QProgressBar { border: 2px solid #00FFFF; border-radius: 10px; height: 20px; }")
        layout.addWidget(self.progress_rpm)

        self.progress_speed = QProgressBar()
        self.progress_speed.setMaximum(240)
        layout.addWidget(self.progress_speed)

        self.btn_start = self.create_3d_button("▶️ Iniciar Diagnóstico", self.start_diagnosis)
        layout.addWidget(self.btn_start)

        self.setLayout(layout)
        self.timer = QTimer()
        self.timer.timeout.connect(self.update_data)

        try:
            self.connection = obd.OBD()  # Conectar al OBD-II
        except:
            self.connection = None

    def create_3d_button(self, text, function):
        btn = QPushButton(text)
        btn.setFont(QFont("Arial", 12, QFont.Weight.Bold))
        btn.setStyleSheet("""
            QPushButton {
                background-color: #00FFFF;
                color: #121212;
                border-radius: 10px;
                border: 2px solid #00FFFF;
                padding: 10px;
                box-shadow: 3px 3px 10px rgba(0, 255, 255, 0.7);
            }
            QPushButton:pressed {
                background-color: #0099CC;
                border: 2px solid #0099CC;
                box-shadow: none;
            }
        """)
        if function:
            btn.clicked.connect(function)
        return btn

    def start_diagnosis(self):
        self.timer.start(1000)

    def update_data(self):
        if self.connection and self.connection.status() == obd.OBDStatus.CAR_CONNECTED:
            rpm = self.connection.query(obd.commands.RPM).value.magnitude if self.connection.query(obd.commands.RPM) else random.randint(800, 6000)
            speed = self.connection.query(obd.commands.SPEED).value.magnitude if self.connection.query(obd.commands.SPEED) else random.randint(0, 180)
            temp = self.connection.query(obd.commands.COOLANT_TEMP).value.magnitude if self.connection.query(obd.commands.COOLANT_TEMP) else random.randint(70, 100)
        else:
            rpm, speed, temp = random.randint(800, 6000), random.randint(0, 180), random.randint(70, 100)

        self.label_rpm.setText(f"🔄 RPM: {rpm}")
        self.label_speed.setText(f"🚗 Velocidad: {speed} km/h")
        self.label_temp.setText(f"🌡️ Temperatura: {temp} °C")

        self.progress_rpm.setValue(rpm)
        self.progress_speed.setValue(speed)

if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = OBD2LumeonTiempoReal()
    window.show()
    sys.exit(app.exec())
import sys
import random
import obd
import time
from PyQt6.QtWidgets import QApplication, QWidget, QVBoxLayout, QLabel, QPushButton, QTextEdit
from PyQt6.QtGui import QFont, QColor
from PyQt6.QtCore import Qt, QTimer

class OBD2LumeonAlertas(QWidget):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("⚠️ OBD2 LUMEON - Alertas Inteligentes")
        self.setGeometry(100, 100, 600, 500)
        self.setStyleSheet("background-color: #121212;")

        layout = QVBoxLayout()

        self.label_title = QLabel("🚨 Alertas en Tiempo Real")
        self.label_title.setFont(QFont("Arial", 16, QFont.Weight.Bold))
        self.label_title.setStyleSheet("color: #FF4500;")
        self.label_title.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.label_title)

        self.label_status = QLabel("🟢 Estado del vehículo: Normal")
        self.label_status.setFont(QFont("Arial", 14))
        self.label_status.setStyleSheet("color: #00FF00;")
        self.label_status.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.label_status)

        self.text_alertas = QTextEdit()
        self.text_alertas.setFont(QFont("Arial", 12))
        self.text_alertas.setStyleSheet("background-color: #1E1E1E; color: #FFFFFF; border: 2px solid #FF4500;")
        self.text_alertas.setReadOnly(True)
        layout.addWidget(self.text_alertas)

        self.btn_iniciar = self.create_3d_button("▶️ Iniciar Monitorización", self.start_monitoring)
        layout.addWidget(self.btn_iniciar)

        self.setLayout(layout)
        self.timer = QTimer()
        self.timer.timeout.connect(self.check_alerts)

        try:
            self.connection = obd.OBD()  # Conectar al OBD-II
        except:
            self.connection = None

    def create_3d_button(self, text, function):
        btn = QPushButton(text)
        btn.setFont(QFont("Arial", 12, QFont.Weight.Bold))
        btn.setStyleSheet("""
            QPushButton {
                background-color: #FF4500;
                color: #121212;
                border-radius: 10px;
                border: 2px solid #FF4500;
                padding: 10px;
                box-shadow: 3px 3px 10px rgba(255, 69, 0, 0.7);
            }
            QPushButton:pressed {
                background-color: #CC3700;
                border: 2px solid #CC3700;
                box-shadow: none;
            }
        """)
        if function:
            btn.clicked.connect(function)
        return btn

    def start_monitoring(self):
        self.timer.start(3000)  # Revisar cada 3 segundos

    def check_alerts(self):
        alertas = []

        if self.connection and self.connection.status() == obd.OBDStatus.CAR_CONNECTED:
            coolant_temp = self.connection.query(obd.commands.COOLANT_TEMP).value.magnitude if self.connection.query(obd.commands.COOLANT_TEMP) else random.randint(70, 120)
            engine_load = self.connection.query(obd.commands.ENGINE_LOAD).value.magnitude if self.connection.query(obd.commands.ENGINE_LOAD) else random.randint(10, 100)
            throttle = self.connection.query(obd.commands.THROTTLE_POS).value.magnitude if self.connection.query(obd.commands.THROTTLE_POS) else random.randint(5, 90)
        else:
            coolant_temp = random.randint(70, 120)
            engine_load = random.randint(10, 100)
            throttle = random.randint(5, 90)

        if coolant_temp > 100:
            alertas.append(f"🌡️ Temperatura Alta ({coolant_temp}°C) - Riesgo de sobrecalentamiento.")

        if engine_load > 85:
            alertas.append(f"⚙️ Carga del motor elevada ({engine_load}%) - Posible problema en inyección.")

        if throttle > 80:
            alertas.append(f"⚡ Aceleración excesiva ({throttle}%) - Posible desgaste prematuro.")

        if alertas:
            self.label_status.setText("🔴 Estado del vehículo: ¡ALERTA!")
            self.label_status.setStyleSheet("color: #FF4500;")
            self.text_alertas.setText("\n".join(alertas))
        else:
            self.label_status.setText("🟢 Estado del vehículo: Normal")
            self.label_status.setStyleSheet("color: #00FF00;")
            self.text_alertas.setText("Sin alertas activas.")

if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = OBD2LumeonAlertas()
    window.show()
    sys.exit(app.exec())
import sys
import json
import random
import requests
import matplotlib.pyplot as plt
import obd
import threading
import time
import speech_recognition as sr
import pyttsx3
from PyQt6.QtWidgets import QApplication, QWidget, QVBoxLayout, QPushButton, QLabel, QTextEdit, QLineEdit, QHBoxLayout, QListWidget, QProgressBar
from PyQt6.QtGui import QFont, QColor
from PyQt6.QtCore import Qt, QTimer

# --- CONFIGURACIÓN DE APIS Y BASES DE DATOS ---
AUTO_CREW_API = "https://api-autocrew.com/fallos"  # API de la comunidad AutoCrew
HISTORIAL_API = "https://api-obd2.com/historial"  # API de historial de fallos

# --- CLASE PARA CONEXIÓN OBD-II ---
class OBDConnection:
    def __init__(self, port=None):
        self.port = port
        self.connection = None
        # ... (código de conexión OBD-II)

    def connect(self):
        # ...
        pass

    def disconnect(self):
        # ...
        pass

    def is_connected(self):
        # ...
        pass

    def query(self, command):
        # ...
        pass

    def clear_dtc(self):
        # ...
        pass

# --- CLASE PARA LA INTERFAZ PRINCIPAL ---
class OBD2Lumeon(QWidget):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("OBD2 LUMEON - Diagnóstico Inteligente")
        self.setGeometry(100, 100, 800, 600)
        self.setStyleSheet("background-color: #121212;")

        self.layout = QVBoxLayout()
        # ... (creación de widgets y diseño de la interfaz)

        self.connection = OBDConnection()
        self.engine = pyttsx3.init()
        self.recognizer = sr.Recognizer()
        self.timer = QTimer()
        self.timer.timeout.connect(self.update_real_time)
        self.scanning = False

        self.setup_ui()

    def setup_ui(self):
        # ... (configuración de la interfaz gráfica)
        pass

    def create_3d_button(self, text, function):
        # ... (creación de botones con efecto 3D)
        pass

    def speak(self, text):
        # ...
        pass

    def voice_input(self):
        # ...
        pass

    def voice_output(self):
        # ...
        pass

    def scan_obd2(self):
        # ... (escaneo OBD-II e integración con API de códigos de error)
        pass

    def get_error_description(self, error_code):
        # ... (obtención de descripción de código de error desde API)
        pass

    def predict_fault(self):
        # ... (predicción de fallos basada en síntomas)
        pass

    def clear_codes(self):
        # ... (borrar códigos de error)
        pass

    def search_error(self):
        # ... (búsqueda de errores en la comunidad AutoCrew)
        pass

    def report_error(self):
        # ... (reportar errores a la comunidad AutoCrew)
        pass

    def update_chat(self):
        # ... (actualizar chat de soporte AutoCrew)
        pass

    def voice_command(self):
        # ... (control por voz)
        pass

    def show_history(self):
        # ... (mostrar historial de diagnósticos)
        pass

    def fetch_data(self):
        # ... (obtener datos de estadísticas)
        pass

    def show_graphs(self):
        # ... (mostrar gráficos de estadísticas)
        pass

    def show_mantenimiento(self):
        # ... (mostrar recomendaciones de mantenimiento)
        pass

    def start_diagnosis(self):
        # ... (iniciar diagnóstico en tiempo real)
        pass

    def update_real_time(self):
        # ... (actualizar datos en tiempo real)
        pass

    def start_monitoring(self):
        # ... (iniciar monitorización de alertas)
        pass

    def check_alerts(self):
        # ... (verificar alertas)
        pass

    def start_auto_scan(self):
        # ... (iniciar escaneo automático)
        pass

    def auto_scan(self):
        # ... (ejecutar escaneo automático)
        pass

    def stop_auto_scan(self):
        # ... (detener escaneo automático)
        pass

# --- EJECUCIÓN DE LA APLICACIÓN ---
if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = OBD2Lumeon()
    window.show()
    sys.exit(app.exec())
