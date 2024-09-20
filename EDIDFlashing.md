To change the primary display for the login screen in GNOME, you'll need to adjust the configuration for the GDM (GNOME Display Manager). Here's how you can do it:

1. **Configure Your Displays**: First, set up your displays the way you want them in your GNOME session using the Display settings.

2. **Update GDM Configuration**:
   - Copy the newly generated `monitors.xml` file from your user directory to the GDM configuration directory. You can do this by opening a terminal and running the following command:
     ```
     sudo cp ~/.config/monitors.xml /var/lib/gdm3/.config/
     ```
   - This command copies your current display configuration to be used by GDM at the login screen.

3. **Set Ownership**:
   - You might need to change the ownership of the copied file to the GDM user to ensure it has the correct permissions to read the file:
     ```
     sudo chown gdm: /var/lib/gdm3/.config/monitors.xml
     ```

4. **Restart GDM**:
   - For the changes to take effect, you may need to restart GDM or reboot your system. To restart GDM, you can use:
     ```
     sudo systemctl restart gdm
     ```

This process should align the GDM login display configuration with your current session settings【25†source】【26†source】【27†source】.

I do not see an existing monitors.xml in that location.

That's fine! If there’s no existing `monitors.xml` file in the GDM folder, it’s likely because GDM hasn't stored any monitor configurations yet. You can create the file and move it manually.

Here’s how to proceed:

1. **Set the Primary Monitor**: If you haven't already set the primary display in your GNOME session:
   - Open a terminal in your session and use `xrandr` to set your preferred display as primary. For example:
     ```bash
     xrandr --output HDMI-1 --primary
     ```

2. **Generate the `monitors.xml` file**: 
   - Open **GNOME Settings** > **Displays**.
   - Adjust your display layout and set the primary monitor from the GUI. This action will automatically create a `monitors.xml` file in your home directory at `~/.config/monitors.xml`.

3. **Copy the file to GDM’s directory**:
   - Now that the `monitors.xml` file is created, you can copy it to GDM’s config folder:
     ```bash
     sudo cp ~/.config/monitors.xml /var/lib/gdm3/.config/
     ```

4. **Set the correct ownership**:
   - Adjust the file ownership so GDM can read it:
     ```bash
     sudo chown gdm:gdm /var/lib/gdm3/.config/monitors.xml
     ```

5. **Restart GDM**:
   - Finally, restart GDM to apply the changes:
     ```bash
     sudo systemctl restart gdm
     ```

This will apply your current monitor setup to the login screen (GDM). If you have further issues, let me know!

Wayland only, no xrandr exists for me.

Since you're using Wayland, `xrandr` isn't available as it's primarily for X11-based systems. Here's how you can handle display configuration on Wayland:

1. **Set Primary Display**:
   - Open **GNOME Settings** > **Displays**.
   - Select the desired monitor and set it as the primary display.
   - This will automatically generate or update the `monitors.xml` file in `~/.config/monitors.xml`.

2. **Copy the `monitors.xml` file to GDM**:
   - Run the following command to copy the display configuration to the GDM configuration:
     ```bash
     sudo cp ~/.config/monitors.xml /var/lib/gdm3/.config/
     ```

3. **Set the correct ownership**:
   - Set the correct ownership to ensure GDM can read the file:
     ```bash
     sudo chown gdm:gdm /var/lib/gdm3/.config/monitors.xml
     ```

4. **Restart GDM**:
   - Restart the GDM service to apply the changes:
     ```bash
     sudo systemctl restart gdm
     ```

These steps should allow GDM to use your preferred monitor for the login screen. Let me know how it goes!