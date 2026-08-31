import tkinter
import math

button_values = [
    ["AC", "+/-", "%", "÷"],
    ["7", "8", "9", "×"],
    ["4", "5", "6", "-"],
    ["1", "2", "3", "+"],
    ["0", ".", "√", "="]
]

right_symbols = ["÷", "×", "-", "+", "="]
top_symbols = ["AC", "+/-", "%"]

row_count = len(button_values)
column_count = len(button_values[0])

color_light_gray = "#D4D4D2"
color_black = "#1C1C1C"
color_dark_grey = "#505050"
color_orange = "#FF9500"
color_white = "white"


# Calculator state
A = None
B = None
operator = None


def remove_zero_decimal(num):
    """Muuttaa esim. 5.0 -> '5', mutta säilyttää muut desimaalit."""
    if float(num).is_integer():
        return str(int(num))
    return str(num)


def clear_all():
    global A, B, operator
    A = None
    B = None
    operator = None


def calculate():
    """Laskee A operator B ja näyttää tuloksen."""
    global A, B, operator

    if A is None or operator is None:
        return

    B = label["text"]

    try:
        numA = float(A)
        numB = float(B)

        if operator == "+":
            result = numA + numB
        elif operator == "-":
            result = numA - numB
        elif operator == "×":
            result = numA * numB
        elif operator == "÷":
            if numB == 0:
                label["text"] = "Error"
                clear_all()
                return
            result = numA / numB
        else:
            return

        label["text"] = remove_zero_decimal(result)
        clear_all()

    except ValueError:
        label["text"] = "Error"
        clear_all()


def button_clicked(value):
    global A, B, operator

    # Operators
    if value in right_symbols:
        if value == "=":
            calculate()

        elif value in "+-×÷":
            # Jos lasku on jo käynnissä, laske ensin nykyinen lasku.
            if operator is not None:
                calculate()

            # Älä aloita uutta laskua virhetilasta.
            if label["text"] == "Error":
                clear_all()
                label["text"] = "0"
                return

            A = label["text"]
            operator = value
            B = None
            label["text"] = "0"

    # Top-row functions
    elif value in top_symbols:
        if value == "AC":
            clear_all()
            label["text"] = "0"

        elif value == "+/-":
            if label["text"] != "Error":
                result = float(label["text"]) * -1
                label["text"] = remove_zero_decimal(result)

        elif value == "%":
            if label["text"] != "Error":
                result = float(label["text"]) / 100
                label["text"] = remove_zero_decimal(result)

    # Square root
    elif value == "√":
        if label["text"] != "Error":
            try:
                num = float(label["text"])
                if num < 0:
                    label["text"] = "Error"
                else:
                    label["text"] = remove_zero_decimal(math.sqrt(num))
            except ValueError:
                label["text"] = "Error"

    # Decimal point
    elif value == ".":
        if label["text"] == "Error":
            label["text"] = "0."

        elif "." not in label["text"]:
            label["text"] += "."

    # Numbers
    elif value in "0123456789":
        if label["text"] == "Error":
            label["text"] = value
        elif label["text"] == "0":
            label["text"] = value
        else:
            label["text"] += value


# Window settings
window = tkinter.Tk()
window.title("Calculator")
window.resizable(False, False)

frame = tkinter.Frame(window, background=color_black)
frame.pack()

label = tkinter.Label(
    frame,
    text="0",
    font=("Arial", 45),
    background=color_black,
    foreground=color_white,
    anchor="e",
    width=column_count
)
label.grid(row=0, column=0, columnspan=column_count, sticky="we")

for row in range(row_count):
    for column in range(column_count):
        value = button_values[row][column]

        button = tkinter.Button(
            frame,
            text=value,
            font=("Arial", 30),
            width=column_count - 1,
            height=1,
            command=lambda value=value: button_clicked(value)
        )

        if value in top_symbols:
            button.config(
                foreground=color_black,
                background=color_light_gray
            )
        elif value in right_symbols:
            button.config(
                foreground=color_white,
                background=color_orange
            )
        else:
            button.config(
                foreground=color_white,
                background=color_dark_grey
            )

        button.grid(row=row + 1, column=column)


# Center the window
window.update()
window_width = window.winfo_width()
window_height = window.winfo_height()
screen_width = window.winfo_screenwidth()
screen_height = window.winfo_screenheight()

window_x = int((screen_width / 2) - (window_width / 2))
window_y = int((screen_height / 2) - (window_height / 2))

window.geometry(
    f"{window_width}x{window_height}+{window_x}+{window_y}"
)

window.mainloop()
