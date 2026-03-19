```python
import os
import importlib.util
from pathlib import Path

from textual.app import App, ComposeResult
from textual.widgets import Input, ListView, ListItem, Label
from textual.containers import Container


COMMANDS_DIR = Path.home() / "core" / "commands"


class Command:
    def __init__(self, name, description, run_func):
        self.name = name
        self.description = description
        self.run = run_func

    def __repr__(self):
        return f"{self.name} - {self.description}"


def load_commands():
    commands = []

    for file in COMMANDS_DIR.glob("*.py"):
        spec = importlib.util.spec_from_file_location(file.stem, file)
        module = importlib.util.module_from_spec(spec)

        try:
            spec.loader.exec_module(module)
        except Exception as e:
            print(f"Error loading {file}: {e}")
            continue

        name = getattr(module, "name", file.stem)
        description = getattr(module, "description", "")
        run_func = getattr(module, "run", None)

        if callable(run_func):
            commands.append(Command(name, description, run_func))

    return commands


class Launcher(App):

    CSS = """
    Screen {
        align: center middle;
    }

    #container {
        width: 60%;
        height: 60%;
        border: solid white;
    }

    Input {
        dock: top;
    }

    ListView {
        height: 1fr;
    }
    """

    def __init__(self):
        super().__init__()
        self.all_commands = load_commands()
        self.filtered_commands = self.all_commands

    def compose(self) -> ComposeResult:
        self.input = Input(placeholder="Type a command...")
        self.list_view = ListView()

        yield Container(
            self.input,
            self.list_view,
            id="container"
        )

    def on_mount(self):
        self.refresh_list()

    def refresh_list(self):
        self.list_view.clear()

        for cmd in self.filtered_commands:
            self.list_view.append(
                ListItem(Label(f"{cmd.name} — {cmd.description}"))
            )

    def on_input_changed(self, event: Input.Changed):
        query = event.value.lower()

        self.filtered_commands = [
            cmd for cmd in self.all_commands
            if query in cmd.name.lower()
            or query in cmd.description.lower()
        ]

        self.refresh_list()

    def on_list_view_selected(self, event: ListView.Selected):
        index = event.index

        if index is None or index >= len(self.filtered_commands):
            return

        command = self.filtered_commands[index]

        self.exit()  # close UI before running

        try:
            command.run()
        except Exception as e:
            print(f"Error running command: {e}")
            input("Press Enter...")


if __name__ == "__main__":
    Launcher().run()
```
