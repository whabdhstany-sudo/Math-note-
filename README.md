# Math-note-
Math note Android app 
from kivy.app import App
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.widget import Widget
from kivy.uix.button import Button
from kivy.uix.label import Label
from kivy.graphics import Color, Line
from kivy.core.window import Window
from sympy import sympify, SympifyError

Window.clearcolor = (0.08, 0.08, 0.08, 1)


class DrawingArea(Widget):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.lines = []
        self.expression = ""

    def on_touch_down(self, touch):
        if self.collide_point(*touch.pos):
            with self.canvas:
                Color(1, 1, 1, 1)
                line = Line(points=[touch.x, touch.y], width=2.5)
                self.lines.append(line)
            return True
        return super().on_touch_down(touch)

    def on_touch_move(self, touch):
        if self.collide_point(*touch.pos) and self.lines:
            self.lines[-1].points += [touch.x, touch.y]
            return True
        return super().on_touch_move(touch)

    def clear(self):
        self.canvas.clear()
        self.lines.clear()


class MathNotesApp(App):
    def build(self):
        self.root_layout = BoxLayout(
            orientation="vertical",
            padding=15,
            spacing=10
        )

        title = Label(
            text="Math Notes",
            font_size=28,
            size_hint_y=0.1
        )
        self.root_layout.add_widget(title)

        self.drawing = DrawingArea()
        self.root_layout.add_widget(self.drawing)

        self.result = Label(
            text="عبارت ریاضی را وارد کن",
            font_size=24,
            size_hint_y=0.15
        )
        self.root_layout.add_widget(self.result)

        buttons = BoxLayout(size_hint_y=0.15, spacing=10)

        solve_button = Button(
            text="محاسبه",
            background_color=(1, 0.6, 0, 1)
        )
        solve_button.bind(on_press=self.calculate)
        buttons.add_widget(solve_button)

        clear_button = Button(
            text="پاک کردن",
            background_color=(0.3, 0.3, 0.3, 1)
        )
        clear_button.bind(on_press=self.clear)
        buttons.add_widget(clear_button)

        self.root_layout.add_widget(buttons)

        return self.root_layout

    def calculate(self, instance):
        try:
            if not self.drawing.expression:
                self.result.text = (
                    "برای محاسبه، فعلاً از نسخه متنی استفاده کن"
                )
                return

            expression = sympify(
                self.drawing.expression,
                evaluate=True
            )

            self.result.text = str(expression)

        except Exception:
            self.result.text = "عبارت قابل محاسبه نیست"

    def clear(self, instance):
        self.drawing.clear()
        self.drawing.expression = ""
        self.result.text = "صفحه پاک شد"


if __name__ == "__main__":
    MathNotesApp().run()
