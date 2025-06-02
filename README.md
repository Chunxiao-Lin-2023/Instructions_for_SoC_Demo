# Instructions_for_SoC_Demo

1. **Install the Cable Drivers (Linux)**  
   For programming the device.

2. **Import the project to Vitis**
   Import both the platform project and the application project.
   The main files: TBD
   
4. **Minicom for UART Connection**  
   Used for monitoring the demo via UART port:
   - Run: `sudo dmesg | grep tty` to find the USB port. Here, the port is ttyUSB0.
   - Then: `sudo minicom -D /dev/ttyUSB0 -b 115200` to open Minicom and connect to the correct UART port.

5. **Build Static Ethernet Connection Between PC and FPGA**

   - **Temporary Setup**  
     - Use `ip a` to find the Ethernet interface (e.g., `enp3s0`).
     - Run:
       ```bash
       sudo ip addr add 192.168.1.2/24 dev enp3s0
       sudo ip link set enp3s0 up
       ```
     - Check the connection:
       ```bash
       ping 192.168.1.10
       ```

   - **Permanent Setup using Netplan**  
     - Find the Netplan configuration file at `/etc/netplan/` with a `.yaml` extension.
     - Edit the Netplan configuration file:
       ```bash
       sudo nano /etc/netplan/01-netcfg.yaml
       ```
     - Modify the file to set a static IP address
       ```bash
       network:
        version: 2
        renderer: networkd
        ethernets:
          enp3s0:
            dhcp4: no
            addresses:
              - 192.168.1.2/24
         gateway4: 192.168.1.1
         nameservers:
           addresses:
             - 8.8.8.8
             - 8.8.4.4
     - Apply the Netplan configuration
       ```bash
       sudo netplan apply
       ```

6. **During Demo**

   1. Open Minicom.
   2. Program the board through Vitis.
   3. Activate the Python virtual environment:
      ```bash
      source venv/bin/activate
      ```
   4. Run the Python script within the venv:
      ```bash
      sudo python3 transmit_data.py
      ```
   5. Command Sequence: *(details not provided)*
      Training
      1. turn on the training
      2. Send matrices: W_in and W_x
      3. Send the golden_out_train.txt file
      4. Start the ESN and send the data_in_train.txt file in chunks.
      5. From the minicom, we can see the NMSE being updated.
      Testing
      1. clear the input data only *(for index reset)*
      2. turn off the training
      3. send the golden_out_test.txt file
      4. Start the ESN and send the data_in_test.txt file in chunks.
      5. From the minicom, we can see the testing NMSE.
