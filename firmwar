import time
import board
import digitalio
import rotaryio
import usb_hid

from adafruit_hid.keyboard import Keyboard
from adafruit_hid.keycode import Keycode
from adafruit_hid.consumer_control import ConsumerControl
from adafruit_hid.consumer_control_code import ConsumerControlCode

# =========================
# CONFIGURATION DES PINS
# =========================
# Adapte ces pins selon ton câblage réel.
# Ici on suppose 9 boutons individuels reliés à GND,
# avec pull-up interne activée.

BUTTON_PINS = [
    board.GP0,   # Bouton 1
    board.GP1,   # Bouton 2
    board.GP2,   # Bouton 3
    board.GP3,   # Bouton 4
    board.GP4,   # Bouton 5
    board.GP5,   # Bouton 6
    board.GP6,   # Bouton 7
    board.GP7,   # Bouton 8
    board.GP8,   # Bouton 9
]

ENCODER_A = board.GP9
ENCODER_B = board.GP10
ENCODER_SW = board.GP11   # bouton de l'encodeur

DEBOUNCE_TIME = 0.03

# =========================
# INIT HID
# =========================
kbd = Keyboard(usb_hid.devices)
cc = ConsumerControl(usb_hid.devices)

# =========================
# INIT BOUTONS
# =========================
buttons = []

for pin in BUTTON_PINS:
    btn = digitalio.DigitalInOut(pin)
    btn.direction = digitalio.Direction.INPUT
    btn.pull = digitalio.Pull.UP
    buttons.append(btn)

encoder_button = digitalio.DigitalInOut(ENCODER_SW)
encoder_button.direction = digitalio.Direction.INPUT
encoder_button.pull = digitalio.Pull.UP

# =========================
# INIT ENCODEUR
# =========================
encoder = rotaryio.IncrementalEncoder(ENCODER_A, ENCODER_B)
last_encoder_position = encoder.position

# États précédents pour détecter les appuis
last_button_states = [btn.value for btn in buttons]
last_encoder_button_state = encoder_button.value

# =========================
# FONCTIONS RACCOURCIS
# =========================
def send_keys(*keys):
    kbd.send(*keys)
    time.sleep(0.05)

def desktop_left():
    # Windows/Linux souvent: CTRL + WIN + LEFT
    send_keys(Keycode.CONTROL, Keycode.GUI, Keycode.LEFT_ARROW)

def desktop_right():
    # Windows/Linux souvent: CTRL + WIN + RIGHT
    send_keys(Keycode.CONTROL, Keycode.GUI, Keycode.RIGHT_ARROW)

def screenshot():
    # Windows: WIN + SHIFT + S
    send_keys(Keycode.GUI, Keycode.SHIFT, Keycode.S)

def copy():
    send_keys(Keycode.CONTROL, Keycode.C)

def paste():
    send_keys(Keycode.CONTROL, Keycode.V)

def mute():
    cc.send(ConsumerControlCode.MUTE)

def play_pause():
    cc.send(ConsumerControlCode.PLAY_PAUSE)

def alt_tab():
    send_keys(Keycode.ALT, Keycode.TAB)

def open_calculator():
    # Windows Run: WIN+R puis "calc"
    send_keys(Keycode.GUI, Keycode.R)
    time.sleep(0.2)

    # tape "calc" à la main sans KeyboardLayout
    send_keys(Keycode.C)
    send_keys(Keycode.A)
    send_keys(Keycode.L)
    send_keys(Keycode.C)
    send_keys(Keycode.ENTER)

def volume_up():
    cc.send(ConsumerControlCode.VOLUME_INCREMENT)

def volume_down():
    cc.send(ConsumerControlCode.VOLUME_DECREMENT)

# =========================
# MAPPAGE DES 9 TOUCHES
# =========================
def handle_button(index):
    if index == 0:
        desktop_left()
    elif index == 1:
        desktop_right()
    elif index == 2:
        screenshot()
    elif index == 3:
        copy()
    elif index == 4:
        paste()
    elif index == 5:
        mute()
    elif index == 6:
        play_pause()
    elif index == 7:
        alt_tab()
    elif index == 8:
        open_calculator()

# =========================
# BOUCLE PRINCIPALE
# =========================
while True:
    # ---- gestion des 9 boutons ----
    for i, btn in enumerate(buttons):
        current_state = btn.value

        # Bouton pressé = passe de True à False (pull-up)
        if last_button_states[i] and not current_state:
            time.sleep(DEBOUNCE_TIME)
            if not btn.value:
                handle_button(i)

        last_button_states[i] = current_state

    # ---- gestion encodeur rotation ----
    current_position = encoder.position

    if current_position != last_encoder_position:
        if current_position > last_encoder_position:
            volume_up()
        else:
            volume_down()

        last_encoder_position = current_position
        time.sleep(0.01)

    # ---- gestion bouton encodeur ----
    current_encoder_button_state = encoder_button.value

    if last_encoder
