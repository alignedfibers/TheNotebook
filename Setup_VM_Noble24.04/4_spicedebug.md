### Spice mouse bounds are not being handled on spice-vdagent in ubuntu correctly 
***(causes mouse stop at border of spiceclient viewer surface & does not move to the external screen)***

***(this issue does not happen in ChromeOS Flex or Windows just Ubuntu)***

---

#### TODO Automatically attachingg the spice-server debug qemu hook script using custom element within the libvirt qemu domain element and external DTD. Its code.
---

#### TODO Redirect debug from vdagent to log on host via serial device under the current vm.
---
#### TODO Create a folder tree for my custom continue.sh, helpers, and logs to live.
---
#### TODO Create a qemu hook that handles the vm start event and creates a custom continue.sh per vm.
```bash
#!/bin/bash
#This is a start. Generating continue.sh requries a custom DTD
VM_NAME="$1"
EVENT="$2"
#SPICE_PORT="5900"  # Default SPICE port, adjust as needed
CUSTOM_DIR="/var/lib/libvirt/customlaunch/$VM_NAME"
#XML_FILE="/etc/libvirt/qemu/$VM_NAME.xml"

# Function to pause the VM
pause_vm() {
    virsh suspend "$VM_NAME"
}

# Function to unpause the VM
unpause_vm() {
    virsh resume "$VM_NAME"
}

# Function to calculate the hash of the domain XML
calculate_hash() {
    md5sum "$XML_FILE" | awk '{print $1}'
}

# Function to start virt-viewer with parameters
launch_virt_viewer() {
    SPICE_PARAMS="spice://127.0.0.1:$SPICE_PORT"
    remote-viewer "$SPICE_PARAMS" --spice-debug --uuid "$(virsh domuuid $VM_NAME)"
}

if [ "$EVENT" == "start" ]; then
    echo "VM $VM_NAME is starting."
    echo "Domain $VM_NAME is flagged for debugging."
    #pause_vm
    echo "Custom launch directory exists for $VM_NAME."
                
    # Check if keyfile exists and matches the hash
    if [ -f "$CUSTOM_DIR/keyfile" ]; then
        SAVED_HASH=$(cat "$CUSTOM_DIR/keyfile")
        CURRENT_HASH=$(calculate_hash)
        if [ "$SAVED_HASH" == "$CURRENT_HASH" ]; then
            echo "Keyfile matches. Proceeding with continue.sh."
            # Run the continue script
            bash "$CUSTOM_DIR/continue.sh"
            unpause_vm
            exit 0
        else
        echo "Hash mismatch. Rebuilding custom launch setup."
        fi
     else
        echo "Keyfile does not exist. Creating new setup."
     fi
     # At this point, the keyfile either doesn't exist or mismatches
     # Read custom element from domain XML
     #CUSTOM_ELEMENT=$(xmlstarlet sel -t -v "//customElement" "$XML_FILE")
     # Set up the debugger flags based on the custom element
     #DEBUG_FLAGS="--flag1 --flag2"  # Adjust based on your setup
     # Write the new keyfile
     # calculate_hash > "$CUSTOM_DIR/keyfile"
fi

```
---
#### TODO Create wrap for virt-viewer used by virtmanager and add the spice-client debug.
#### TODO Create the DTD that will be able to be specified in the xmlns allowing the custom element in the libvirt domain.xml config for each vm.