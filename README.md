#  Java Installer Script (Bash-Based)

---
## Author Information

| Created         | Created on         | Version          | Last updated by   | pre Reviewer       | L0 Reviewer     | L1 Reviewer          |    L2 Reviewer    |
|-----------------|--------------------|------------------|-------------------|--------------------|-----------------|----------------------|-------------------|
| Aryan mishra    |17-07-2025          | V.1        |                  |        Siddharth    |  Ram Ratan      |      Gaurav Singla   |   Mahesh Kumar    |
 
 ---
## Table of Contents

- [Introduction](#Introduction)
- [Features](#features)                          
- [Prerequisites](#prerequisites)                      
- [Script Breakdown](#script-breakdown)      
- [Usage](-#Usage)                 
- [Supported Java Versions](#supported-java-versions) 
- [Example](#example)            
- [Troubleshooting](#troubleshooting)      

---
## Introduction

Installing and managing Java environments manually on multiple systems can be time-consuming and error-prone.

This project provides a **lightweight, customizable Bash script** to automate Java installation on Debian/Ubuntu systems. 

To provide a **generic, reusable, and automated Bash script** that simplifies the **installation, configuration, and management** of different Java versions (OpenJDK) on **Linux systems**

---

## Features
- Supports OpenJDK 8, 11, 17+ versions

- Upgrade-safe: skips install if version exists

- Customizable default Java version
 
- Cleans old versions (optional)

- Works on Ubuntu and Debian-based systems

- Sets up JAVA_HOME and PATH automatically

---
## Prerequisites

- Ensure the following before running the script:

- Linux system (Ubuntu/Debian preferred)

- curl and sudo must be available

- Run with appropriate privileges (user in sudoers)

## Script Breakdown
```bash
#!/bin/bash

# ========== CONFIG ========== #
JAVA_VERSION=${1:-11}   # Default is Java 11 if not specified
INSTALL_DIR="/usr/lib/jvm"
UPDATE_ENV=true
CLEAN_OLD=false

# ========== FUNCTIONS ========== #
install_java() {
  echo "[INFO] Installing OpenJDK $JAVA_VERSION..."

  sudo apt update
  sudo apt install -y openjdk-${JAVA_VERSION}-jdk

  if [[ $? -ne 0 ]]; then
    echo "[ERROR] Java $JAVA_VERSION installation failed."
    exit 1
  fi

  echo "[SUCCESS] Java $JAVA_VERSION installed."
}

setup_environment() {
  JAVA_PATH=$(update-java-alternatives -l | grep "java-${JAVA_VERSION}" | awk '{print $3}')

  if [[ -z "$JAVA_PATH" ]]; then
    echo "[ERROR] Could not find JAVA path for version $JAVA_VERSION."
    exit 1
  fi

  echo "[INFO] Setting JAVA_HOME and PATH..."

  sudo tee /etc/profile.d/java.sh > /dev/null <<EOF
export JAVA_HOME=$JAVA_PATH
export PATH=\$JAVA_HOME/bin:\$PATH
EOF

  source /etc/profile.d/java.sh
}

cleanup_old_versions() {
  if [[ "$CLEAN_OLD" == true ]]; then
    echo "[INFO] Removing all other Java versions..."
    installed=$(dpkg -l | grep openjdk | awk '{print $2}' | grep -v "$JAVA_VERSION")
    for pkg in $installed; do
      sudo apt remove -y $pkg
    done
  fi
}

# ========== MAIN ========== #
echo "======================================="
echo " Java Installation Script"
echo " Requested Version: OpenJDK $JAVA_VERSION"
echo "======================================="

install_java
[[ "$UPDATE_ENV" == true ]] && setup_environment
[[ "$CLEAN_OLD" == true ]] && cleanup_old_versions

java -version
echo "[DONE] Java $JAVA_VERSION setup complete."
```
---
##  Basic Usage
```bash
chmod +x install-java.sh
```
```bash
./install-java.sh 17
```
- This installs OpenJDK 17 and sets it as default

---

## Supported Java Versions
| Java Version	|      Status |
|---------------|-------------|
| 8	|   Supported |
| 11	| Supported |
| 17	| Supported |

---
## Example
```bash
./install-java.sh 11
```
#### Output
- [INFO] Installing OpenJDK 11...

- [INFO] Setting JAVA_HOME and PATH...
  
- openjdk version "11.0.22" 2025-01-01
  
-  [DONE] Java 11 setup complete.

---
## Troubleshooting
|  Issue	|  Solution |
|---------|-----------|
| command not found: update-java-alternatives	Install | sudo apt install openjdk-XX-jdk |
| JAVA_HOME not working	Run | source /etc/profile.d/java.sh or re-login |
| Wrong Java version active	Use | sudo update-alternatives --config java |
| Permission denied	Run script as | sudo ./install-java.sh 11 |

---
## References

 | Name                                                    | Resource                                                             |
 |---------------------------------------------------------|----------------------------------------------------------------------|
 | OpenJDK Installation on Ubuntu (Official Guide)        | [Link](https://openjdk.org/install/)                                        |
 |   Ubuntu OpenJDK Packages                              | [Link](https://packages.ubuntu.com/search?keywords=openjdk)                 |
 |    update-alternatives Command Manual                  | [Link](https://man7.org/linux/man-pages/man8/update-alternatives.8.html)    |          
 |     Bash Scripting Best Practices                  |  [Link](https://google.github.io/styleguide/shellguide.html)|

 ---
 | Name          | Email                                |
| ------------- | ------------------------------------ |
| Aryan Mishra  | aryan.mishra@mygurukulam.co          |

---


