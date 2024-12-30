import numpy as np
import matplotlib.pyplot as plt
from matplotlib.widgets import Slider, Button, CheckButtons
import scipy.signal as signal

# Функція для створення гармоніки з шумом
def harmonic_with_noise(amplitude, frequency, phase, noise_mean, noise_covariance, show_noise):
    t = np.linspace(0, 2 * np.pi, 500)
    pure_signal = amplitude * np.sin(frequency * t + phase)
    noise = np.random.normal(noise_mean, np.sqrt(noise_covariance), len(t))
    if show_noise:
        return t, pure_signal + noise
    return t, pure_signal

# Початкові параметри
initial_amplitude = 1
initial_frequency = 2
initial_phase = 0
initial_noise_mean = 0
initial_noise_covariance = 0.1
show_noise = True

# Створення графічного інтерфейсу
fig, ax = plt.subplots()
plt.subplots_adjust(left=0.25, bottom=0.35)

t, y = harmonic_with_noise(initial_amplitude, initial_frequency, initial_phase, initial_noise_mean, initial_noise_covariance, show_noise)
line, = plt.plot(t, y, lw=2)

# Слайдери
axamp = plt.axes([0.25, 0.2, 0.65, 0.03])
axfreq = plt.axes([0.25, 0.15, 0.65, 0.03])
amp_slider = Slider(axamp, 'Amplitude', 0.1, 10.0, valinit=initial_amplitude)
freq_slider = Slider(axfreq, 'Frequency', 0.1, 5.0, valinit=initial_frequency)

# Чекбокс
axcheck = plt.axes([0.025, 0.5, 0.15, 0.15])
check = CheckButtons(axcheck, ['Show Noise'], [show_noise])

# Кнопка Reset
resetax = plt.axes([0.8, 0.025, 0.1, 0.04])
button = Button(resetax, 'Reset')

# Оновлення графіку
def update(val):
    amp = amp_slider.val
    freq = freq_slider.val
    global show_noise
    t, y = harmonic_with_noise(amp, freq, initial_phase, initial_noise_mean, initial_noise_covariance, show_noise)
    line.set_ydata(y)
    fig.canvas.draw_idle()

amp_slider.on_changed(update)
freq_slider.on_changed(update)

def toggle_noise(label):
    global show_noise
    show_noise = not show_noise
    update(None)

check.on_clicked(toggle_noise)

def reset(event):
    amp_slider.reset()
    freq_slider.reset()

button.on_clicked(reset)

plt.show()
