import time
from pynput.keyboard import Key, Controller, Listener as KeyboardListener
from pynput.mouse import Listener as MouseListener
import threading

keyboard = Controller()
stop_event = threading.Event()
start_event = threading.Event()

# Function to alternate key presses
def alternate_keys():
    start_event.wait()  # Wait until the start event is set
    print("Start event detected. Alternating keys...")
    while not stop_event.is_set():
        print("Pressing '1'")
        keyboard.press('1')
        keyboard.release('1')
        time.sleep(5)
        if stop_event.is_set():
            break
        print("Pressing '2'")
        keyboard.press('2')
        keyboard.release('2')
        time.sleep(5)
    print("Stop event detected. Ending script.")

# Function to stop the script
def on_press(key):
    try:
        if key.char not in ['1', '2']:  # Ignore '1' and '2' key presses
            stop_event.set()
            print("Key press detected. Stop event set.")
    except AttributeError:
        stop_event.set()
        print("Special key press detected. Stop event set.")

def on_click(x, y, button, pressed):
    if pressed:
        if button == button.left:
            start_event.set()
            print("Left click detected. Start event set.")
        elif button == button.right:
            stop_event.set()
            print("Right click detected. Stop event set.")

# Start the key alternating in a separate thread
thread = threading.Thread(target=alternate_keys)
thread.start()

# Listen for keyboard and mouse events
with KeyboardListener(on_press=on_press) as k_listener, MouseListener(on_click=on_click) as m_listener:
    k_listener.join()
    m_listener.join()