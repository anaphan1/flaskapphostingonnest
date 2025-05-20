# 🚀 Hosting a Flask App with Gunicorn, Caddy, and Tmux

This guide helps you host a Flask web app using `gunicorn` (Python WSGI server), `Caddy` (reverse proxy with automatic HTTPS), and `tmux` (terminal multiplexer) on Hack Club's Nest server. Visit their website https://guides.hackclub.app/index.php/Main_Page, or their GitHub repository https://github.com/hackclub/nest for more information.

---

## ✅ Prerequisites

Make sure your project has:
- A `requirements.txt` file with:  

    ```requirements.txt
    Flask
    gunicorn
    ```
    There may be more in this file depending on what you're trying to do (e.g. `pytorch` for neural network apps) but make sure you have at least these two

- A Python file that exposes your Flask app as `app` (e.g., `run.py`).

---

## 1. Open a Tmux Session

Start `tmux` and split the terminal vertically:

```bash
tmux
```

Then press:

`Ctrl + B`, then `%`

This gives you two side-by-side panes.

---

## 2. Set Up Your Flask Project (Left Pane)

To access your first pane, type in:
`Ctrl + B` then `0`

In the first pane, run:

```bash
cd ~/pub
```  

Clone your repo:

```bash
git clone https://github.com/yourusername/yourproject.git
```  
```bash
cd yourproject
```

Set up a Python virtual environment:

```bash
python3 -m venv .venv
``` 
```bash
source .venv/bin/activate
```

Now you are in the virtual environment. You should see `(.venv)` before your current working directory in the command prompt. Type in:  

```bash
pip install -r requirements.txt
```

---

## 3. Configure Caddy (Right Pane)

To access your second pane, type in:
`Ctrl + B` then `1`

In the second pane, register your subdomain:

```bash
nest caddy add <subdomain>.<yourusername>.hackclub.app
```

Copy the output (it includes the Unix socket path you’ll need).

See an open port to host your website on:

```bash
nest get_port
```

Remember the open port, you will need it shortly.

Edit your `Caddyfile`:

```bash
nano Caddyfile
```

Add the following:

```caddyfile
http://<subdomain>.<yourusername>.hackclub.app {
    bind unix/<paste_unix_socket_path_here>.sock|777
    reverse_proxy :<your_port_that_you_remembered>
}
```

---

## 4. Start Your App (Left Pane)

To access your first pane, type in:
`Ctrl + B` then `0`

Still in the first pane, run your app using `gunicorn`:

```bash
gunicorn -b :<your_port_above_10000> run:app
```

Make sure `run.py` looks like:

```python
from app import app
```

The `app` module can also be a package. The `app` that is imported is the instance of your app. You can also initialize an app factory by doing the following code:

```python
from app import create_app

app = create_app()  # Assuming `create_app` is your app factory
```

---

## 5. Launch Caddy (Right Pane)

Kill any running Caddy process and start a new one:

```bash
pkill caddy
```  
```bash
caddy run
```

---

## ✅ You're Live!

Visit your site at:

`https://<subdomain>.<yourusername>.hackclub.app`

Your Flask app is now up and running! 🎉
