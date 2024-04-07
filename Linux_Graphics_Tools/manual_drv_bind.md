```bash
#!/bin/bash 
# Function to check if running as root 

check_root() { 

    if [ "$(id -u)" -ne 0 ]; then 

        echo "This script must be run as root" >&2 

        exit 1 

    fi 

} 

  

# Function to check if sysfs paths exist 

check_paths() { 

    if [ ! -d "/sys/bus/pci/drivers/$2" ] || [ ! -e "/sys/bus/pci/devices/$1/driver/unbind" ]; then 

        echo "Required sysfs paths do not exist, check your device and driver names" >&2 

        exit 1 

    fi 

} 

  

# Function to bind a driver to a device 

bind_driver() { 

    echo "$1" > "/sys/bus/pci/drivers/$2/bind" 

} 

  

# Function to unbind a driver from a device 

unbind_driver() { 

    echo "$1" > "/sys/bus/pci/devices/$1/driver/unbind" 

} 

  

# Main logic to parse arguments and call appropriate functions 

main() { 

    check_root 

     

    ACTION="" 

    DEVICE="" 

    DRIVER="" 

  

    while getopts ":b:u:d:" opt; do 

        case ${opt} in 

            b ) # process option for binding 

                ACTION="bind" 

                DEVICE="${OPTARG}" 

                ;; 

            u ) # process option for unbinding 

                ACTION="unbind" 

                DEVICE="${OPTARG}" 

                ;; 

            d ) # specify driver 

                DRIVER="${OPTARG}" 

                ;; 

            \? ) echo "Usage: cmd [-b device] [-u device] -d driver" 

                ;; 

        esac 

    done 

  

    if [ -z "$DRIVER" ]; then 

        echo "Driver must be specified with -d" >&2 

        exit 1 

    fi 

  

    check_paths "$DEVICE" "$DRIVER" 

  

    if [ "$ACTION" = "bind" ]; then 

        bind_driver "$DEVICE" "$DRIVER" 

    elif [ "$ACTION" = "unbind" ]; then 

        unbind_driver "$DEVICE" 

    else 

        echo "Invalid action. Use -b to bind or -u to unbind." 

        exit 1 

    fi 

} 

  

# Run main function with all arguments 

main "$@" 

 
```
