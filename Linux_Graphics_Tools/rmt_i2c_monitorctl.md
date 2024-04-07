Web API on top of a service and abstract library wrapping ddcutil.  
#Boiler Plate Python – Called into via a local secure cloud function exposure –  
#behind nginx – 6digit auth with known-device and proximity check attempt, and alerts.   
#Monitor mic cannot logmein. Period.. No No No.   
#Alerts are web, multicast local, email, possible txt msg or messenger. Access is lan only.   
#Web connection is norm over https established and held both ways but has a noisy stream    
#which is a packet of data over https every so many ms, packets are always 16bit obj.   
#Thinking the noise ends when the session ends, so at least could listen on network for use.   
#It is an API so any communications are one of what every combinations are possible.  
#Ports for the simple web service to flip values on my remote/clicker app, also throttled.  
#This is small bits and very little data stuff only. Multicast, he swapped his video,  
#certaintly will have an on off for the whole network knowing if my video swapped though.  
#It is a cheap kvm without the mouse and the keyboard.  
#basically a double private device handshake then an auth code for session.  
#It is a changing handshake, security ideas are overdoing it.

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
                    match = re.search(r'Display (\d+): (.+)', line)
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
