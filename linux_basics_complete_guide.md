# GUIDE AND HANDS-ON EXERCISES: LINUX SYSTEM ADMINISTRATOR

This document brings together the key concepts, the essential commands, explanations of their arguments (options) and commented hands-on exercises to validate your Linux administration skills.

---

## A NOTE ON TERMINOLOGY: FLAGS / OPTIONS (the "-...")
Items that start with a dash (e.g. `-la`, `-p`, `-9`) are called **options**, **arguments** or **flags**.
* They change the behavior of a basic command.
* A single dash (`-`) usually introduces short options (a single letter, e.g. `-f`). They can often be combined: `-la` is the same as `-l -a`.
* Two dashes (`--`) introduce long options (a full word, e.g. `--purge`, `--help`).

---

## SECTION 1: NAVIGATING AND HANDLING FILES AND DIRECTORIES

### Basic commands and arguments
* `cd` (Change Directory): moves between directories.
* `mkdir` (Make Directory): creates directories.
  * `-p` (--parents): creates the parent directories if they do not exist (avoids an error when the whole structure has to be created at once).
* `touch`: creates an empty file or updates its modification date.
* `cp` (Copy): copies files or directories.
  * `-r` or `-R` (--recursive): recursively copies the entire contents of a directory (files and subdirectories).
* `ls` (List): lists the contents of a directory.
  * `-l`: uses the long format (shows permissions, size, owner, date).
  * `-a` (--all): shows all files, including hidden files (those starting with a dot `.`).

### Commented hands-on exercise
```bash
# 1. Go to the current user's home directory (tilde symbol ~)
cd ~

# 2. Create a complete directory tree. The -p option creates 'project' and then 'src' inside it in a single command.
mkdir -p project/src

# 3. Enter the newly created 'src' directory
cd project/src

# 4. Create an empty file named 'app.js'
touch app.js

# 5. Go back to the parent directory (.. means the directory just above, here 'project')
cd ..

# 6. Copy the file 'app.js' located in 'src/' to the current directory (written '.') under a new name
cp src/app.js ./app_backup.js

# 7. List the contents in long format (-l), including hidden files (-a)
ls -la
```

---

## SECTION 2: UNDERSTANDING AND CHANGING PERMISSIONS (chmod/chown)

### Basic commands and arguments
* `chmod` (Change Mode): changes the read (r=4), write (w=2) and execute (x=1) permissions.
  * `u+x`: `u` (user/owner), `+` (add), `x` (execute). Gives the owner the right to execute the file.
  * `700`: numeric notation. 7 (4+2+1 = rwx for the owner), 0 (no rights for the group), 0 (no rights for others).
* `chown` (Change Owner): changes the owner and/or the group of a file.
  * `user:group` syntax: changes both at the same time.

### Commented hands-on exercise
```bash
# Before starting, create a test file
touch script.sh

# 1. Add the execute permission (+x) for the owner (u) only
chmod u+x script.sh

# Strict numeric equivalent: owner gets everything (7), group gets nothing (0), others get nothing (0)
chmod 700 script.sh

# 2. Change the owner to 'alice' and the group to 'dev'
# (Requires 'sudo', because only the administrator can give ownership of a file to someone else)
sudo chown alice:dev script.sh
```

---

## SECTION 3: LISTING, MONITORING AND KILLING A PROCESS

### Basic commands and arguments
* `ps` (Process Status): lists the running processes.
  * `aux`: combined option (without a dash, in its classic BSD form):
    * `a`: shows the processes of all users.
    * `u`: shows the owner's user name and resource usage details (CPU, memory).
    * `x`: shows processes that are not attached to a terminal (background services).
* `top`: shows a live, dynamic list of the most resource-hungry processes.
* `kill`: sends a signal to a process using its unique identifier (PID).
  * `-9`: sends the SIGKILL signal. It is an immediate, forced stop that the program cannot ignore.

### Commented hands-on exercise
```bash
# 1. Look for the Firefox process among all the processes on the system
# The '|' symbol (pipe) sends the output of 'ps aux' to the 'grep' command, which keeps the lines containing 'firefox'
ps aux | grep firefox

# 2. Start live monitoring of the system
top
# NOTE: Once in top, look at the PID, %CPU and %MEM columns. Quit by pressing the 'q' key.

# 3. Force the stubborn process to stop. Replace 1234 with the real PID found in step 1
kill -9 1234
```

---

## SECTION 4: INSTALLING / REMOVING A PACKAGE (apt)

### Basic commands and arguments
* `apt` (Advanced Package Tool): Ubuntu's package manager.
  * `update`: downloads the updated list of software available on the official servers. Run it before installing anything.
  * `install`: downloads and installs a piece of software and its dependencies.
    * `-y` (--yes): automatically answers "Yes" to every question during installation (avoids interruptions).
  * `purge`: removes the software AND all of its system-wide configuration files.
  * `autoremove`: removes packages and dependencies that were installed automatically but are no longer needed.

### Commented hands-on exercise
```bash
# 1. Update the local database of available software
sudo apt update

# 2. Install the 'curl' network tool automatically, without manual confirmation
sudo apt install -y curl

# 3. Cleanly remove 'curl' along with all of its leftover configuration
sudo apt purge curl

# 4. Clean up the system by removing software dependencies that are now orphaned and useless
sudo apt autoremove
```

---

## SECTION 5: CREATING A USER AND MANAGING GROUPS

### Basic commands and arguments
* `useradd`: creates a new user (low-level command).
  * `-m` (--create-home): automatically creates the user's home directory in `/home/username`.
* `passwd`: changes a user's password.
* `groupadd`: creates a new user group on the system.
* `usermod` (User Modify): changes the settings of an existing user.
  * `-aG`: a crucial combined option:
    * `-a` (--append): adds the user to new groups *without* removing them from their current groups.
    * `-G` (--groups): specifies the list of additional groups.

### Commented hands-on exercise
```bash
# 1. Create a user called 'bob' and create his home directory (/home/bob)
sudo useradd -m bob

# 2. Give bob a secure password (the system asks you to type it twice, without displaying it)
sudo passwd bob

# 3. Create a work group named 'sysadmin'
sudo groupadd sysadmin

# 4. Add bob to the 'sysadmin' group AND to the 'sudo' group (which grants administrator rights on Ubuntu)
sudo usermod -aG sysadmin,sudo bob
```

---

## SECTION 6: CONNECTING OVER SSH AND CHECKING OPEN PORTS

### Basic commands and arguments
* `ssh` (Secure Shell): tool for encrypted remote connections.
  * `username@ip_address` syntax: specifies which account to log in with on the target machine.
* `ss` (Socket Statistics): modern tool for inspecting network connections (replaces the old netstat command).
  * `-tulpn`: an essential combined option:
    * `-t`: shows TCP sockets.
    * `-u`: shows UDP sockets.
    * `-l`: shows only the ports currently listening.
    * `-p`: shows the name and PID of the process using the port.
    * `-n`: shows port numbers and addresses in numeric form (e.g. 22 instead of 'ssh'), which makes the command faster.

### Commented hands-on exercise
```bash
# 1. Open a remote terminal session to the machine 192.168.1.50 as the 'ubuntu' user
ssh ubuntu@192.168.1.50
# NOTE: Type 'exit' to close the SSH session once your tests are done.

# 2. Check which programs are listening on the local network or the Internet, with process details (-tulpn)
sudo ss -tulpn
```

---

## SECTION 7: MANAGING A SERVICE WITH systemctl AND READING ITS LOGS

### Basic commands and arguments
* `systemctl`: controls `systemd`, the system and service manager.
  * `start`: starts a service immediately.
  * `status`: shows the current detailed state of a service (active, stopped, failed, latest logs).
  * `enable`: configures the service to start automatically every time the machine boots.
* `journalctl`: queries the system logs centralized by systemd.
  * `-u` (--unit): shows only the logs of a specific service (e.g. nginx).
  * `-f` (--follow): "live" mode. The terminal stays open and displays new log lines as they are written.

### Commented hands-on exercise
```bash
# (Assumption: the Nginx web server is installed on the machine)

# 1. Start the Nginx web server right away and check that it is running correctly
sudo systemctl start nginx
sudo systemctl status nginx

# 2. Set the service to start automatically after every system reboot
sudo systemctl enable nginx

# 3. Follow live (-f) the connections and messages of the nginx service (-u)
sudo journalctl -u nginx -f
# NOTE: Press 'Ctrl + C' to stop the live log display.
```

---

## SECTION 8: FILTERING LOGS AND WRITING A SIMPLE BASH SCRIPT

### Basic commands and arguments
* `cat` (Concatenate): prints the entire contents of a text file in the terminal.
* `grep`: searches for and prints the lines that contain a specific word.
* `awk`: a complete command-line programming language for processing text data organized in columns.
  * `'{print $11}'`: tells awk to split each incoming line and print only the content of the 11th column.

### Commented hands-on filtering exercise
```bash
# Extract the denied login attempts from the security logs
# Read the file, grep keeps only the failures ("Failed"), and awk isolates the 11th column (often the attacker's IP address)
sudo cat /var/log/auth.log | grep "Failed" | awk '{print $11}'
```

### Commented hands-on scripting exercise
Here is the complete code of an automation script. It starts with the mandatory `#!/bin/bash` header, called a **shebang**. It tells the system that this file must be interpreted by the Bash shell.

```bash
#!/bin/bash
# ==============================================================================
# SCRIPT: backup.sh
# DESCRIPTION: Automates the backup of the project directory to a target directory
# ==============================================================================

# Path variables (cleaner and reusable)
SOURCE="/home/ubuntu/project"
TARGET="/home/ubuntu/project_backup"

# Step 1: Print an information message in the terminal
echo "Starting automatic backup..."

# Step 2: Create the target directory if it does not exist yet (-p avoids an error)
mkdir -p "$TARGET"

# Step 3: Recursively copy (-r) all files (*) from the source directory to the target
cp -r "$SOURCE"/* "$TARGET"

# Step 4: Confirmation including the system date and time, using the $(date) command
echo "Backup completed successfully on $(date)"
```

### How to run this script:
1. Create the file: `touch backup.sh`
2. Open an editor (e.g. `nano backup.sh`), paste the script above and save.
3. Give yourself the right to execute it: `chmod u+x backup.sh`
4. Run the script: `./backup.sh`

---

## SECTION 9: PRACTICAL SURVIVAL GUIDE TO THE VIM EDITOR

**Vim** is an extremely fast modal text editor built into the terminal. It has two main states: **Normal mode** (to navigate and run commands) and **Insert mode** (to type text).

### The 4 essential steps

1.  **Open or create a file:**
    ```bash
    vim exercise.txt
    ```
2.  **Switch to writing mode:** press the **`i`** key (`-- INSERT --` appears at the bottom). You can now type your text or code.
3.  **Leave writing mode:** press the **`Esc`** key. The `-- INSERT --` indicator disappears and you are back in Normal mode.
4.  **Save and close:** in Normal mode, type **`:wq`** and press `Enter`.

### Essential shortcuts from the Vim cheat sheet

All of these commands must be typed in **Normal mode** (after pressing `Esc`).

| Command | Action |
| :--- | :--- |
| **`i`** | Switch to writing mode (Insert) |
| **`Esc`** | Leave writing mode to run commands |
| **`:wq`** | Save the changes and close the file (*write and quit*) |
| **`:q!`** | Close the file immediately **without saving** (discard everything) |
| **`:w`** | Save the file without closing it |
| **`dd`** | Delete (cut) the whole line under the cursor |
| **`x`** | Delete the single character under the cursor |
| **`u`** | Undo the last action |
| **`Ctrl + r`** | Redo the action that was just undone |
| **`gg`** | Jump instantly to the very first line of the file |
| **`G`** | Jump instantly to the very last line of the file |
| **`0`** | Move the cursor to the beginning of the current line |
| **`$`** | Move the cursor to the end of the current line |
