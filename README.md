# Asynchronous Tkinter Mainloop

[![Python package](https://github.com/insolor/asynctk/actions/workflows/python-package.yml/badge.svg)](https://github.com/insolor/asynctk/actions/workflows/python-package.yml)

Implementation of asynchronous `mainloop` for tkinter, the use of which allows using `async` handler functions.
It is intended to be as simple to use as possible. No fancy unusual syntax or constructions - just use an alternative
function instead of `root.mainloop()` and wrap asynchronous handlers into helper functions.

Based on ideas from:

* my answer on ru.stackoverflow.com: <https://ru.stackoverflow.com/a/1043146>
* answer of [Terry Jan Reedy](https://stackoverflow.com/users/722804) on stackoverflow.com:
  <https://stackoverflow.com/a/47896365>
* answer of [jfs](https://ru.stackoverflow.com/users/23044) on ru.stackoverflow.com:
  <https://ru.stackoverflow.com/a/804609>

## Installation

### Release version

Download `async-tkinter-loop-*.tar.gz` or `async_tkinter_loop-*.whl` file from
[releases](https://github.com/insolor/async-tkinter-loop/releases),
install it with the following command:

```
pip install async-tkinter-loop-*.tar.gz[examples]
```
or
```
pip install async_tkinter_loop-*.whl[examples]
```

- `[examples]` part is needed to install optional dependencies (such as `aiohttp` and `pillow`) to run some of the
  examples. If you're not going to run examples, remove the `[examples]` part from the command
- Insert actual file name instead of `async-tkinter-loop-*.tar.gz` or `async_tkinter_loop-*.whl`
- Use `pip3` instead of `pip` on Linux systems to install the package for python3 (not python2)
- Probably you'll want to create a virtual environment for experiments with this library, but this is optional.
- If you want to try examples, download the entire repository as an archive (green "code" button on this page →
  "Download ZIP"), unpack, run any example (of course, you need to install optional dependencies)

### Development version

1. Install [Poetry](https://python-poetry.org), e.g., with `pip install poetry` command
   (other options see [here](https://python-poetry.org/docs/#installation))
2. Download and unpack or clone the repository.
3. Run the command `poetry install` or `poetry install -E examples` (the later command installs optional dependencies
   needed to run some of the examples). This command will create `.venv` directory with a virtual environment and
   install dependencies into it.
   - Run any example with `poetry run python examples/sparks.py` (insert a file name of an example).
   - Or activate the virtual environment and run an example with `python examples/sparks.py` command. You can also open
     the directory with the project in some IDE (e.g., PyCharm or VS Code) and select Python interpreter from the
     virtual environment as a project interpreter, then run examples directly from the IDE.

## Some examples

Basic example:
```python
import asyncio
import tkinter as tk

from async_tkinter_loop import async_mainloop, async_command


async def counter():
    i = 0
    while True:
        i += 1
        label['text'] = str(i)
        await asyncio.sleep(1.0)


root = tk.Tk()

label = tk.Label(root)
label.pack()

tk.Button(root, text="Start", command=async_command(counter)).pack()

async_mainloop(root)
```

A more practical example, downloading an image from the Internet with [aiohttp](https://github.com/aio-libs/aiohttp)
and displaying it in the Tkinter window:

```python
import tkinter as tk
from io import BytesIO

import aiohttp
from PIL import Image, ImageTk

from async_tkinter_loop import async_mainloop, async_command


async def load_image(url):
    button['state'] = 'disabled'
    label['text'] = 'Loading cat...'

    async with aiohttp.ClientSession() as session:
        response = await session.get(url)
        if response.status != 200:
            label['text'] = f'HTTP error {response.status}'
        else:
            content = await response.content.read()
            pil_image = Image.open(BytesIO(content))
            image = ImageTk.PhotoImage(pil_image)
            label.config(image=image, text='')
            label.image = image
            button['state'] = 'normal'


url = "http://thecatapi.com/api/images/get?format=src&type=jpg"


root = tk.Tk()

button = tk.Button(root, text='Load a cat', command=async_command(load_image, url))
button.pack()

label = tk.Label(root)
label.pack()

if __name__ == "__main__":
    async_mainloop(root)
```

![black cat](img/black_cat.png)


More examples see in the [`examples`](https://github.com/insolor/async-tkinter-loop/tree/main/examples) directory.


## Similar projects

* [Starwort/asynctk](https://github.com/Starwort/asynctk) ([on PyPi](https://pypi.org/project/asynctk/))
* [gottadiveintopython/asynctkinter](https://github.com/gottadiveintopython/asynctkinter) ([on PyPi](https://pypi.org/project/asynctkinter/))
* [Lucretiel/tkinter-async](https://github.com/Lucretiel/tkinter-async)
* [fluentpython/asyncio-tkinter](https://github.com/fluentpython/asyncio-tkinter)
