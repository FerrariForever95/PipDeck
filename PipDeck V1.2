import os
import sys
import json
import queue
import shutil
import threading
import subprocess
import tkinter as tk
from tkinter import ttk, messagebox
from urllib.request import urlopen
from urllib.parse import quote

APP_TITLE = "PipDeck"
APP_WIDTH = 1200
APP_HEIGHT = 700


class PipManagerApp:

    def __init__(self, root):
        self.root = root
        self.root.title(APP_TITLE)
        self.root.geometry(f"{APP_WIDTH}x{APP_HEIGHT}")

        self.output_queue = queue.Queue()

        self.python_envs = []
        self.current_python = None
        self.package_cache = []

        self.build_ui()

        self.detect_python_installations()
        self.process_output_queue()

    # ------------------------------------------------------------
    # UI
    # ------------------------------------------------------------

    def build_ui(self):

        self.style = ttk.Style()
        try:
            self.style.theme_use("clam")
        except:
            pass

        top_frame = ttk.Frame(self.root)
        top_frame.pack(fill="x", padx=10, pady=10)

        ttk.Label(top_frame, text="Python Interpreter").pack(side="left")

        self.python_combo = ttk.Combobox(top_frame, state="readonly")
        self.python_combo.pack(side="left", fill="x", expand=True, padx=10)
        self.python_combo.bind("<<ComboboxSelected>>", self.on_python_selected)

        self.refresh_btn = ttk.Button(
            top_frame,
            text="Refresh",
            command=self.refresh_packages
        )
        self.refresh_btn.pack(side="left", padx=5)

        self.install_pip_btn = ttk.Button(
            top_frame,
            text="Install pip",
            command=self.install_pip_for_selected
        )
        self.install_pip_btn.pack(side="left")

        search_frame = ttk.Frame(self.root)
        search_frame.pack(fill="x", padx=10)

        self.search_var = tk.StringVar()

        self.search_entry = ttk.Entry(
            search_frame,
            textvariable=self.search_var
        )
        self.search_entry.pack(fill="x")
        self.search_entry.bind("<KeyRelease>", self.filter_packages)

        main_pane = ttk.PanedWindow(self.root, orient="horizontal")
        main_pane.pack(fill="both", expand=True, padx=10, pady=10)

        # LEFT PANEL
        left_frame = ttk.Frame(main_pane)

        self.package_listbox = tk.Listbox(
            left_frame,
            font=("Consolas", 10)
        )
        self.package_listbox.pack(fill="both", expand=True)
        self.package_listbox.bind("<<ListboxSelect>>", self.on_package_selected)

        main_pane.add(left_frame, weight=1)

        # RIGHT PANEL
        right_frame = ttk.Frame(main_pane)

        self.package_title = ttk.Label(
            right_frame,
            text="Select Package",
            font=("Arial", 16, "bold")
        )
        self.package_title.pack(anchor="w", pady=5)

        self.package_version = ttk.Label(
            right_frame,
            text=""
        )
        self.package_version.pack(anchor="w")

        self.package_desc = tk.Text(
            right_frame,
            wrap="word",
            height=20
        )
        self.package_desc.pack(fill="both", expand=True, pady=10)

        action_frame = ttk.Frame(right_frame)
        action_frame.pack(fill="x")

        self.install_entry = ttk.Entry(action_frame)
        self.install_entry.pack(side="left", fill="x", expand=True)

        self.install_btn = ttk.Button(
            action_frame,
            text="Install",
            command=self.install_package
        )
        self.install_btn.pack(side="left", padx=5)

        self.upgrade_btn = ttk.Button(
            action_frame,
            text="Upgrade",
            command=self.upgrade_selected_package
        )
        self.upgrade_btn.pack(side="left", padx=5)

        self.remove_btn = ttk.Button(
            action_frame,
            text="Remove",
            command=self.remove_selected_package
        )
        self.remove_btn.pack(side="left")

        main_pane.add(right_frame, weight=2)

        # CONSOLE
        console_frame = ttk.LabelFrame(self.root, text="Console")
        console_frame.pack(fill="both", padx=10, pady=(0, 10))

        self.console = tk.Text(
            console_frame,
            height=10,
            bg="black",
            fg="lime",
            insertbackground="white"
        )
        self.console.pack(fill="both", expand=True)

    # ------------------------------------------------------------
    # LOGGING
    # ------------------------------------------------------------

    def log(self, text):
        self.console.insert("end", text + "\n")
        self.console.see("end")

    def process_output_queue(self):
        try:
            while True:
                msg = self.output_queue.get_nowait()
                self.log(msg)
        except queue.Empty:
            pass

        self.root.after(100, self.process_output_queue)

    # ------------------------------------------------------------
    # PYTHON DETECTION
    # ------------------------------------------------------------

    def detect_python_installations(self):

        found = set()

        commands = []

        if os.name == "nt":
            commands = [
                ["where", "python"],
                ["where", "python3"],
                ["where", "py"]
            ]
        else:
            commands = [
                ["which", "-a", "python"],
                ["which", "-a", "python3"],
                ["which", "-a", "pip"],
                ["which", "-a", "pip3"]
            ]

        for cmd in commands:
            try:
                result = subprocess.check_output(
                    cmd,
                    stderr=subprocess.DEVNULL,
                    text=True
                )

                for line in result.splitlines():
                    line = line.strip()

                    if not line:
                        continue

                    if os.name != "nt":
                        if "pip" in os.path.basename(line):
                            line = self.convert_pip_to_python(line)

                    if os.path.exists(line):
                        found.add(line)

            except:
                pass

        envs = []

        for exe in found:

            try:
                version = subprocess.check_output(
                    [exe, "--version"],
                    text=True
                ).strip()

                envs.append({
                    "path": exe,
                    "version": version
                })

            except:
                pass

        self.python_envs = envs

        self.python_combo["values"] = [
            f"{e['version']}  [{e['path']}]"
            for e in envs
        ]

        if envs:
            self.python_combo.current(0)
            self.current_python = envs[0]["path"]
            self.refresh_packages()

    def convert_pip_to_python(self, pip_path):

        base = os.path.dirname(pip_path)

        possible = [
            os.path.join(base, "python"),
            os.path.join(base, "python3")
        ]

        for p in possible:
            if os.path.exists(p):
                return p

        return pip_path

    # ------------------------------------------------------------
    # PIP CHECK
    # ------------------------------------------------------------

    def ensure_pip(self):

        if not self.current_python:
            return False

        try:
            subprocess.check_output(
                [self.current_python, "-m", "pip", "--version"],
                stderr=subprocess.DEVNULL
            )

            return True

        except:
            pass

        self.log("pip not found. Attempting ensurepip...")

        try:
            subprocess.check_call(
                [self.current_python, "-m", "ensurepip", "--upgrade"]
            )

            self.log("pip installed successfully using ensurepip")
            return True

        except Exception as e:
            self.log(f"ensurepip failed: {e}")

        return False

    def install_pip_for_selected(self):

        threading.Thread(
            target=self.ensure_pip,
            daemon=True
        ).start()

    # ------------------------------------------------------------
    # PACKAGE LIST
    # ------------------------------------------------------------

    def refresh_packages(self):

        threading.Thread(
            target=self.load_packages,
            daemon=True
        ).start()

    def load_packages(self):

        if not self.ensure_pip():
            self.output_queue.put("pip unavailable")
            return

        self.output_queue.put("Loading packages...")

        code = r'''
import json
import importlib.metadata as m

packages = []

for d in m.distributions():
    meta = d.metadata

    packages.append({
        "name": meta.get("Name", ""),
        "version": meta.get("Version", ""),
        "summary": meta.get("Summary", "")
    })

print(json.dumps(packages))
'''

        try:
            result = subprocess.check_output(
                [self.current_python, "-c", code],
                text=True
            )

            packages = json.loads(result)

            packages.sort(key=lambda x: x["name"].lower())

            self.package_cache = packages

            self.root.after(0, self.update_package_list)

            self.output_queue.put(
                f"Loaded {len(packages)} packages"
            )

        except Exception as e:
            self.output_queue.put(str(e))

    def update_package_list(self):

        self.package_listbox.delete(0, "end")

        for pkg in self.package_cache:
            self.package_listbox.insert(
                "end",
                f"{pkg['name']}  ({pkg['version']})"
            )

    # ------------------------------------------------------------
    # FILTER
    # ------------------------------------------------------------

    def filter_packages(self, event=None):

        query = self.search_var.get().lower()

        self.package_listbox.delete(0, "end")

        for pkg in self.package_cache:

            name = pkg["name"].lower()
            summary = pkg["summary"].lower()

            if query in name or query in summary:
                self.package_listbox.insert(
                    "end",
                    f"{pkg['name']}  ({pkg['version']})"
                )

    # ------------------------------------------------------------
    # PACKAGE DETAILS
    # ------------------------------------------------------------

    def on_package_selected(self, event=None):

        sel = self.package_listbox.curselection()

        if not sel:
            return

        item = self.package_listbox.get(sel[0])

        name = item.split("  (")[0]

        pkg = None

        for p in self.package_cache:
            if p["name"] == name:
                pkg = p
                break

        if not pkg:
            return

        self.package_title.config(text=pkg["name"])
        self.package_version.config(
            text=f"Version: {pkg['version']}"
        )

        self.package_desc.delete("1.0", "end")

        self.package_desc.insert(
            "end",
            pkg["summary"] + "\n\n"
        )

        threading.Thread(
            target=self.fetch_pypi_description,
            args=(pkg["name"],),
            daemon=True
        ).start()

    def fetch_pypi_description(self, package_name):

        try:

            url = f"https://pypi.org/pypi/{quote(package_name)}/json"

            with urlopen(url, timeout=10) as r:
                data = json.loads(r.read().decode())

            info = data.get("info", {})

            desc = info.get("description", "")

            if not desc:
                desc = info.get("summary", "")

            desc = desc[:8000]

            self.root.after(
                0,
                lambda: self.display_description(desc)
            )

        except Exception as e:
            self.output_queue.put(
                f"Failed fetching PyPI info: {e}"
            )

    def display_description(self, text):

        self.package_desc.delete("1.0", "end")
        self.package_desc.insert("end", text)

    # ------------------------------------------------------------
    # PACKAGE OPERATIONS
    # ------------------------------------------------------------

    def install_package(self):

        pkg = self.install_entry.get().strip()

        if not pkg:
            return

        threading.Thread(
            target=self.run_pip_command,
            args=(["install", pkg],),
            daemon=True
        ).start()

    def upgrade_selected_package(self):

        sel = self.package_listbox.curselection()

        if not sel:
            return

        item = self.package_listbox.get(sel[0])

        name = item.split("  (")[0]

        threading.Thread(
            target=self.run_pip_command,
            args=(["install", "--upgrade", name],),
            daemon=True
        ).start()

    def remove_selected_package(self):

        sel = self.package_listbox.curselection()

        if not sel:
            return

        item = self.package_listbox.get(sel[0])

        name = item.split("  (")[0]

        if not messagebox.askyesno(
            "Confirm",
            f"Uninstall {name} ?"
        ):
            return

        threading.Thread(
            target=self.run_pip_command,
            args=(["uninstall", "-y", name],),
            daemon=True
        ).start()

    def run_pip_command(self, pip_args):

        if not self.ensure_pip():
            return

        cmd = [self.current_python, "-m", "pip"] + pip_args

        self.output_queue.put(
            "Running: " + " ".join(cmd)
        )

        try:

            process = subprocess.Popen(
                cmd,
                stdout=subprocess.PIPE,
                stderr=subprocess.STDOUT,
                text=True
            )

            while True:

                line = process.stdout.readline()

                if not line:
                    break

                self.output_queue.put(line.rstrip())

            process.wait()

            self.output_queue.put(
                f"Process exited with code {process.returncode}"
            )

            self.refresh_packages()

        except Exception as e:
            self.output_queue.put(str(e))

    # ------------------------------------------------------------
    # EVENTS
    # ------------------------------------------------------------

    def on_python_selected(self, event=None):

        idx = self.python_combo.current()

        if idx < 0:
            return

        self.current_python = self.python_envs[idx]["path"]

        self.log(f"Selected: {self.current_python}")

        self.refresh_packages()


# ------------------------------------------------------------
# MAIN
# ------------------------------------------------------------

def main():

    root = tk.Tk()

    app = PipManagerApp(root)

    root.mainloop()


if __name__ == "__main__":
    main()
