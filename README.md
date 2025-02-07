# phy-122
# Import necessary libraries
%matplotlib notebook
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
from ipywidgets import *

# Example setup and plotting data
x = np.arange(10)
y = np.arange(10) + np.random.random(10) - 0.5
plt.figure()
plt.errorbar(x, y, np.ones(10) * 0.2, fmt='.')
plt.plot(x, x, 'r', label="f(x)=x")  # Red line for f(x)=x
plt.plot(x, 0.5 * x, 'g-.', label="f(x)=0.5x")  # Green line for f(x)=0.5x
plt.title("y vs x")
plt.legend()
plt.show()

# Define chi-squared function
def chiSquared(x, y, dy, f, args):
    '''Calculates chi-squared to assess the goodness of fit'''
    return 1 / (len(x) - len(args)) * np.sum((f(x, args) - y) ** 2 / dy ** 2)

# Linear function for fitting
def linear(x, args):
    '''Linear function of the form y = mx + b'''
    return args[0] + x * args[1]

# Example data for regression
y = np.array([1.36, 3.36, 3.92, 4.11, 3.43, 5.22, 8.29, 8.22, 11.15, 10.86])
uncertainty = np.ones(10)
uncertainty[4] = 4  # Higher uncertainty for point 4
x = np.linspace(1, 8, 10)

# Plot with residuals
fig, ax = plt.subplots(1, 2, figsize=(8, 4))
ax[0].set_title("force vs extension")
line, = ax[0].plot(x, linear(x, [0, 1]))  # Initial linear fit
data = ax[0].errorbar(x, y, uncertainty, fmt='.k')
ax[1].set_title("residuals")
residuals = linear(x, [0, 1]) - y
res = ax[1].errorbar(x, residuals, uncertainty, fmt='.k')
ax[1].grid(True, which='both')
plt.show()

# Interactive sliders to adjust slope and intercept
def update(intercept=0, slope=1):
    '''Updates plot and residuals based on user input for intercept and slope'''
    fx = linear(x, [intercept, slope])
    line.set_ydata(fx)
    residuals = fx - y
    ax[1].cla()
    res = ax[1].errorbar(x, residuals, uncertainty, fmt='.k')
    ax[1].grid(True, which='both')
    ax[1].set_title("Residuals")
    fig.canvas.draw_idle()
    print("chi-squared value: ")
    print(chiSquared(x, y, uncertainty, linear, [intercept, slope]).round(3))

# Create interactive sliders
interact(update, intercept=(-5, 20, 0.1), slope=(-1, 10, 0.1))
