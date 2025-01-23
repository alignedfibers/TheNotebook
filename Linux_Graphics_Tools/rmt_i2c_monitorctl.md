
# Ran into some funky issues where my monitors were acting like the menus on them were being run remotely
# I updated some things and that went away, but I wondered, is there a way I could detect if my monitors change menu options
# The closest utility I could find to help in this is ddcutil, so I generated some code it was crap.
# So I clarified I wanted threads and wanted to create a listener to poll the ddcutil utility.
# Code generation pointed me to the correct libraries but still was not working, but I cleaned it up.
# I think at least some of the code below works if you call it, but has an issue I have not cleaned up yet.
# The point of this is so I can add an api around ddcutil to make it easy to change menu options, or detect features available.
# Not all features of ddcutil are available depending on the version of display port or hdmi in use on the monitor
# Purpose used to detect if for some reason the monitors changed via a different display connector on the back by listening.
# Purpose used also to change the videa source on monitors that do not come with a remote control by connect a linux computer to a display port.
# Since I can use this api once complete with key mapping of a keyboard or remote on the linux computer for source input change im happy.

```python
"""
Just something to start with.
"""
import subprocess
import re
import time
from functools import partial
import threading

class MonitorController:
    def __init__(self):
        self.monitors = {}
        self.lock = threading.Lock()
        self.running = True
        self._get_connected_monitors()

    def _get_connected_monitors(self):
        try:
            output = subprocess.check_output(['ddcutil', 'detect'], timeout=10).decode('utf-8')
            for line in output.split('\n'):
                if line.startswith('Display'):
                    match = re.search(r'Display (\d+)', line)
                    if match:
                        monitor_id = int(match.group(1))
                        monitor_name = match.group(2)
                        self.monitors[monitor_id] = monitor_name
        except subprocess.TimeoutExpired:
            print("Timeout occurred while trying to detect monitors.")

    def _monitor_listener(self):
        while self.running:
            time.sleep(1)
            connected_monitors = {}
            try:
                output = subprocess.check_output(['ddcutil', 'detect'], timeout=10).decode('utf-8')
                for line in output.split('\n'):
                    if line.startswith('Display'):
                        match = re.search(r'Display (\d+): (.+)', line)
                        if match:
                            monitor_id = int(match.group(1))
                            monitor_name = match.group(2)
                            connected_monitors[monitor_id] = monitor_name
            except subprocess.TimeoutExpired:
                print("Timeout occurred while trying to detect monitors.")

            with self.lock:
                # Check for newly connected monitors
                new_monitors = set(connected_monitors.keys()) - set(self.monitors.keys())
                for monitor_id in new_monitors:
                    self.monitors[monitor_id] = connected_monitors[monitor_id]

                # Check for disconnected monitors
                disconnected_monitors = set(self.monitors.keys()) - set(connected_monitors.keys())
                for monitor_id in disconnected_monitors:
                    del self.monitors[monitor_id]

            # Check if the listener is stuck or not responding
            if not connected_monitors:
                print("Listener stopped responding. Restarting...")
                self.restart()

    def start_listener(self):
        listener = threading.Thread(target=self._monitor_listener)
        listener.daemon = True
        listener.start()

    def restart(self):
        self.running = False
        time.sleep(1)
        self.__init__()
        self.start_listener()

    def __getattr__(self, name):
        if name.startswith('monitor') and len(name) > 7:
            monitor_id = int(name[7])
            if monitor_id in self.monitors:
                return partial(self._execute_command, monitor_id)
            else:
                raise AttributeError(f"No monitor found with ID {monitor_id}")
        else:
            raise AttributeError(f"Method {name} not found")

    def _execute_command(self, monitor_id, command):
        with self.lock:
            command_list = ['ddcutil', '--display', str(monitor_id)]
            command_list.extend(command.split())
            try:
                output = subprocess.check_output(command_list, timeout=10).decode('utf-8')
                return output
            except subprocess.TimeoutExpired:
                print(f"Timeout occurred while executing command for monitor {monitor_id}.")

# Example usage:

# controller = MonitorController()
# controller.start_listener()
# print(controller.monitor1.completeSomeAction())
```

Now, let's create a systemd .service file to run the script as a service.  
```ini
[Unit]
Description=Monitor Controller Service
After=network.target

[Service]
User=<YOUR_USERNAME>
Group=<YOUR_GROUPNAME>
WorkingDirectory=<PATH_TO_YOUR_SCRIPT_DIRECTORY>
ExecStart=/usr/bin/python3 <PATH_TO_YOUR_SCRIPT_FILE>
Restart=always

[Install]
WantedBy=multi-user.target
```
Reload systemd to read the new service file:
```bash
sudo systemctl daemon-reload
```
Enable the service to start on boot:
```bash
sudo systemctl enable monitor_controller.service
```
Start the service:
```bash
sudo systemctl start monitor_controller.service
```
You can check the status of the service using:
```bash
sudo systemctl status monitor_controller.service
```
