# Simple Flask App for WSL Ubuntu Practice

- [Basic commands for WSL](https://learn.microsoft.com/en-us/windows/wsl/basic-commands)
- [WSL Flask Production Setup - Private](https://chatgpt.com/c/680f757c-387c-8002-9d82-30558ba364c6)
- [WSL Flask Production Setup - Shared](https://chatgpt.com/share/680f853e-fbbc-8002-ad26-d0a4445cdcb4)

## Request

I have a wsl Ubuntu vm. How can I host a production-grade flask app on it to test how to set up production-grade servers?

Please show, provide code for, and explain how:

1. Create a simple flask app for demonstration purposes.
2. Install an appropriate http or request server (or whatever it is called) to serve up the flask app.
3. Please provide any other best-practice information or recommendations.

## Response

Alright, Bruce — great project idea!  
You want to practice setting up a **production-grade Flask server** inside a **WSL Ubuntu VM**.  
I'll walk you through it **clearly and step-by-step** — we'll cover:

---

# 1. Create a simple Flask app

Inside your WSL Ubuntu:

```bash
mkdir myflaskapp
cd myflaskapp
python3 -m venv venv
source venv/bin/activate
pip install flask
```

Then create a file called `app.py`:

```python
# app.py
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello, this is a production-ready test Flask app!"
```

Test it real quick:

```bash
python app.py
```

You should see it running, but **this is NOT production-grade yet** (this is Flask’s built-in dev server).

---

# 2. Install a Production-Grade Server (Gunicorn)

In the same virtual environment:

```bash
pip install gunicorn
```

Now run the app using **gunicorn**:

```bash
gunicorn --bind 0.0.0.0:8000 app:app
```

**Breakdown**:
- `gunicorn` = the server.
- `--bind 0.0.0.0:8000` = listen on all IPs, port 8000 (important inside WSL).
- `app:app` = `[file_name]:[Flask app object name]`.

**You just launched your Flask app on a real HTTP server!**

---

# 3. (Optional but Recommended) Use a Process Manager (like systemd or supervisord)

**In production**, you don't just manually run `gunicorn` — you want your server to **restart if it crashes**.

Since WSL might not have full `systemd`, let's install **supervisor**:

```bash
sudo apt update
sudo apt install supervisor
```

Create a Supervisor config:

```bash
sudo nano /etc/supervisor/conf.d/myflaskapp.conf
```

Paste this:

```
[program:myflaskapp]
directory=/home/YOUR-USERNAME/myflaskapp
command=/home/YOUR-USERNAME/myflaskapp/venv/bin/gunicorn --workers 3 --bind 0.0.0.0:8000 app:app
autostart=true
autorestart=true
stderr_logfile=/var/log/myflaskapp.err.log
stdout_logfile=/var/log/myflaskapp.out.log
environment=PATH="/home/YOUR-USERNAME/myflaskapp/venv/bin",VIRTUAL_ENV="/home/YOUR-USERNAME/myflaskapp/venv"
user=YOUR-USERNAME
```

Then run:

```bash
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl start myflaskapp
```

✅ Now your Flask app is *managed properly*.

---

# 4. Optional: Nginx Reverse Proxy (recommended for real production)

In a real cloud server, you don't want to expose Gunicorn directly. You want **Nginx** to sit in front and forward requests.

Install nginx:

```bash
sudo apt install nginx
```

Create a config:

```bash
sudo nano /etc/nginx/sites-available/myflaskapp
```

Paste:

```
server {
    listen 80;
    server_name localhost;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Link it:

```bash
sudo ln -s /etc/nginx/sites-available/myflaskapp /etc/nginx/sites-enabled
sudo nginx -t
sudo systemctl restart nginx
```

✅ Now you can visit `http://localhost/` and it goes through Nginx to Gunicorn to Flask!

---

# 5. Best Practices Summary

| Area                         | Good Practice                                                                 |
|-------------------------------|-------------------------------------------------------------------------------|
| HTTP server                   | Use **Gunicorn** (or uWSGI)                                                   |
| Process Management            | Use **Supervisor** or **systemd**                                             |
| Reverse Proxy                 | Use **Nginx**                                                                |
| Isolation                     | **venv** or even Docker for bigger apps                                       |
| Security                      | Use **TLS/HTTPS** in real production (Nginx with Let's Encrypt, etc.)          |
| Scalability                   | Run **multiple Gunicorn workers** (`--workers 3`)                             |
| Static Files                  | Let **Nginx** serve static files directly instead of Flask handling them     |
| Environment Variables         | Use `.env` files or secure configuration settings                            |
| Logs                          | Separate **access logs** and **error logs**                                   |

---

# 6. Bonus: Quickstart Copy-Paste for You

```bash
# Quickstart for Ubuntu WSL production-style Flask:
sudo apt update
sudo apt install python3-venv supervisor nginx

mkdir myflaskapp
cd myflaskapp
python3 -m venv venv
source venv/bin/activate
pip install flask gunicorn

echo '
from flask import Flask
app = Flask(__name__)
@app.route("/")
def home():
    return "Hello from production Flask!"
' > app.py

gunicorn --bind 0.0.0.0:8000 app:app
```

Then set up supervisor and nginx as shown above ✅

---

Would you like me to also show how to:
- Add SSL certificates to Nginx?
- Or create a simple deploy script to automate all this?  
👀  
Would be pretty cool if you're practicing for real-world deployments!  
Let me know! 🚀

## Request

Help me replace the important parts of:

```ini
[program:myflaskapp]
directory=/home/YOUR-USERNAME/myflaskapp
command=/home/YOUR-USERNAME/myflaskapp/venv/bin/gunicorn --workers 3 --bind 0.0.0.0:8000 app:app
autostart=true
autorestart=true
stderr_logfile=/var/log/myflaskapp.err.log
stdout_logfile=/var/log/myflaskapp.out.log
environment=PATH="/home/YOUR-USERNAME/myflaskapp/venv/bin",VIRTUAL_ENV="/home/YOUR-USERNAME/myflaskapp/venv"
user=YOUR-USERNAME
```

with

```bash
flynntknapp@DELL-DESKTOP:~/Programming$ ls -al ~/.local/share/virtualenvs/simple-flask-app-xmeeDYXD/bin/activate
-rw-r--r-- 1 flynntknapp flynntknapp 2310 Apr 27 22:04 /home/flynntknapp/.local/share/virtualenvs/simple-flask-app-xmeeDYXD/bin/activate
flynntknapp@DELL-DESKTOP:~/Programming$ ls -al ~/Programming/simple-flask-app/app.py
-rw-r--r-- 1 flynntknapp flynntknapp 262 Apr 28 08:17 /home/flynntknapp/Programming/simple-flask-app/app.py
flynntknapp@DELL-DESKTOP:~/Programming$ ls -al ~/.local/share/virtualenvs/simple-flask-app-xmeeDYXD/bin/gunicorn
-rwxr-xr-x 1 flynntknapp flynntknapp 280 Apr 28 08:15 /home/flynntknapp/.local/share/virtualenvs/simple-flask-app-xmeeDYXD/bin/gunicorn
flynntknapp@DELL-DESKTOP:~/Programming$
```

## Response

Got it — thanks for the details.  
Here’s your updated `supervisor` config, filled in for your situation:

```ini
[program:simple-flask-app]
directory=/home/flynntknapp/Programming/simple-flask-app
command=/home/flynntknapp/.local/share/virtualenvs/simple-flask-app-xmeeDYXD/bin/gunicorn --workers 3 --bind 0.0.0.0:8000 app:app
autostart=true
autorestart=true
stderr_logfile=/var/log/simple-flask-app.err.log
stdout_logfile=/var/log/simple-flask-app.out.log
environment=PATH="/home/flynntknapp/.local/share/virtualenvs/simple-flask-app-xmeeDYXD/bin",VIRTUAL_ENV="/home/flynntknapp/.local/share/virtualenvs/simple-flask-app-xmeeDYXD"
user=flynntknapp
```

✅ **Key replacements** I made:
- `directory=` → where your `app.py` lives.
- `command=` → points to your gunicorn binary inside the Pipenv/virtualenv and loads `app:app`.
- `environment=` → points your `PATH` and `VIRTUAL_ENV` to your virtual environment.
- `user=` → your WSL username (`flynntknapp`).

---

# Next Steps After Saving This Config:
```bash
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl start simple-flask-app
```

- `reread` = tell Supervisor to detect the new `.conf` file.
- `update` = actually load the config.
- `start simple-flask-app` = start your Flask app under Supervisor control.

---

Would you also like me to show you how to **tail the logs** in real time with `sudo tail -f /var/log/simple-flask-app.out.log`?  
(very helpful for debugging!) 🚀

## Request

yes

## Response

Perfect — here’s how you can **monitor your Flask app's logs in real time** while it's running under Supervisor:

---

# 1. Tail the Standard Output Log (normal prints, Gunicorn startup messages, etc.)

```bash
sudo tail -f /var/log/simple-flask-app.out.log
```

- `-f` means "follow" — it will **keep showing new lines as they are written** to the log file.
- Press `Ctrl+C` to stop following the log when you're done.

Example output you might see:

```text
[2025-04-28 08:42:12 +0000] [1234] [INFO] Starting gunicorn 21.2.0
[2025-04-28 08:42:12 +0000] [1234] [INFO] Listening at: http://0.0.0.0:8000 (1234)
[2025-04-28 08:42:12 +0000] [1237] [INFO] Booting worker with pid: 1237
```

---

# 2. Tail the Error Log (important if something crashes)

```bash
sudo tail -f /var/log/simple-flask-app.err.log
```

If your app or gunicorn crashes, or Supervisor has a problem, the errors will show up here.

Typical crash examples:
- Wrong `command=`
- Gunicorn can't find `app:app`
- Python import error inside `app.py`
- Port already in use

---

# 3. Pro Tip: Monitor Both Logs at Once!

If you want to watch both logs at the same time (⚡):

```bash
sudo tail -f /var/log/simple-flask-app.out.log /var/log/simple-flask-app.err.log
```

You'll see output from both files merged in real time.

---

# 4. Extra Bonus Tip: If You Modify Code

If you change your Flask code (`app.py`) or your virtualenv setup:
```bash
sudo supervisorctl restart simple-flask-app
```
This cleanly restarts Gunicorn with the new version.

---

Would you also like me to show you a quick `htop` trick to monitor your Gunicorn worker processes and their CPU usage live?  
It’s fun and super useful if you're tuning performance! 🔥

## Request

will supervisor restart teh app when the server is restarted?

## Response

Yes — **Supervisor will automatically restart your Flask app when the server (your WSL VM) is restarted**.

✅ **As long as**:
- You have `autostart=true` in your Supervisor program config (which you do),
- And Supervisor itself is running at system boot.

—

⚡ In WSL Ubuntu, Supervisor **does not always start automatically** when you boot WSL unless you set it up.  
To make sure Supervisor starts itself and your app:

## 1. Check if Supervisor is enabled on boot

Run:

```bash
sudo systemctl status supervisor
```

- If it shows **running**, you're good.
- If it says **inactive** or **disabled**, you need to enable it.

## 2. Enable Supervisor to start on boot (if needed)

```bash
sudo systemctl enable supervisor
sudo systemctl start supervisor
```

- `enable` = make it automatically start at boot.
- `start` = manually start it now.

(You only need to do `enable` once.)

—

✅ After that:
- When you reboot WSL, Supervisor will start automatically.
- Supervisor will automatically launch your `simple-flask-app`.
- If your app crashes, Supervisor will auto-restart it too (because `autorestart=true`).

—

Would you also like me to show you how to **simulate a crash** and **watch Supervisor restart** it, so you can see it in action?  
(Takes about 30 seconds, and it’s really satisfying to watch!) 🚀

## Request

yes

## Response

Awesome — let's do a **real quick crash simulation** so you can actually *see* Supervisor auto-restart your app.

---

# 1. Start by watching your Supervisor logs

Open a second terminal window and run:

```bash
sudo tail -f /var/log/simple-flask-app.out.log
```

This will let you *watch* the output live.

You can also run:

```bash
sudo supervisorctl status simple-flask-app
```

You should see something like:

```text
simple-flask-app       RUNNING   pid 1234, uptime 0:10:12
```

—

# 2. Force the App to Crash

Now, in the first terminal window, **intentionally kill the Gunicorn process**.

First, find it:

```bash
ps aux | grep gunicorn
```

Example output:

```text
flynntknapp  1234  0.0  0.1  123456  1234 ?  S    08:00   0:01 /home/flynntknapp/.local/share/virtualenvs/simple-flask-app-xmeeDYXD/bin/gunicorn --workers 3 --bind 0.0.0.0:8000 app:app
```

Kill the process manually:

```bash
kill -9 1234
```
(replace `1234` with the actual PID you see)

—

# 3. Watch Supervisor Restart It!

- In your `tail -f` terminal, you’ll see Gunicorn **exit** and then **restart itself** automatically.
- In about 1–2 seconds Supervisor should relaunch the Flask app.
  
You might see messages like:

```text
[INFO] Exited: simple-flask-app (terminated by SIGKILL)
[INFO] Restarting crashed process: simple-flask-app
```

And you can verify again with:

```bash
sudo supervisorctl status simple-flask-app
```

It should show `RUNNING` again with a **new PID**.

—

✅ Congratulations — this is exactly what happens during:
- App crashes
- Gunicorn worker failures
- Full server reboots
- Unexpected errors

Your app **self-heals** via Supervisor! 🔥

---

Would you like me to also show you:
- How to crash just **one worker** (instead of the whole server) and watch Gunicorn itself recover it?
- OR how to **limit restart retries** so Supervisor doesn't infinitely loop on broken apps?  
(These are super cool advanced tricks if you want to get fancy!) 🚀

## Request

```
Would you also like me to show you a quick htop trick to monitor your Gunicorn worker processes and their CPU usage live?
```

yes

## Response

Awesome — this is one of the most satisfying parts!  
You’ll be able to **see your Flask app’s Gunicorn master and workers** live, using `htop`.

Here’s the quick setup:

---

# 1. Install `htop` if you haven't yet

```bash
sudo apt update
sudo apt install htop
```

✅ It’s like a supercharged `top` — much easier to read.

—

# 2. Launch `htop`

Just run:

```bash
htop
```

You’ll see something like this:

```
PID   USER        PRI  NI  VIRT    RES    SHR S CPU% MEM% TIME+  Command
1234  flynntknapp  20   0  123M    12M    4M  S  0.0  0.2  0:01.23 /path/to/gunicorn master [app:app]
1237  flynntknapp  20   0  124M    13M    5M  S  0.0  0.3  0:00.89 /path/to/gunicorn worker [app:app]
1238  flynntknapp  20   0  124M    13M    5M  S  0.0  0.3  0:00.85 /path/to/gunicorn worker [app:app]
1239  flynntknapp  20   0  124M    13M    5M  S  0.0  0.3  0:00.87 /path/to/gunicorn worker [app:app]
```

**Interpretation**:
- 🧠 **Master process** (1): Controls and monitors the workers.
- 🛠️ **Worker processes** (3): Actually handle HTTP requests.
- 🖥️ **CPU%**: You can see if your app is working hard.

Gunicorn (by default) forks multiple workers based on your `--workers 3` flag.

—

# 3. Helpful `htop` Tricks:

| Key | What it does                    |
|-----|----------------------------------|
| F3  | Search for "gunicorn" live       |
| F6  | Sort by CPU usage or memory      |
| F5  | Tree view (to see parent/child)  |
| F9  | Kill a specific process manually |
| /   | Quick search (same as F3)         |

**Use F5 (Tree view)** to see the Gunicorn master ➔ worker hierarchy beautifully.

—

# 4. (Optional) Highlight Just Your Flask App Processes

While inside `htop`, you can **filter by your username** (`flynntknapp`) to only show your stuff:

- Press `U`
- Select `flynntknapp`
- Now you only see your user's processes!

---

✅ With `htop` you can now:
- See how much CPU each Gunicorn worker uses
- See if you’re spawning too many or too few workers
- See memory usage grow if your app has leaks

—

Would you also like a real-world pro tip on **choosing how many Gunicorn workers** based on your CPU cores?  
It’s super simple but important if you're scaling apps later! 🚀

## Request



## Response



## Request



## Response



## Request



## Response



## Request



## Response



## Request



## Response



## Request



## Response



## Request



## Response



## Request



## Response
