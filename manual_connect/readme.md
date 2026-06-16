  Step 1: Find your port name

  - Windows: Open Device Manager, expand "Ports (COM & LPT)", and note the COM port (e.g., COM3, COM5).
  - macOS: Run ls /dev/tty.usb* in Terminal (e.g., /dev/tty.usbserial-1420).
  - Linux: Run ls /dev/ttyUSB* in Terminal (e.g., /dev/ttyUSB0).
  - 
## To hardcode port names in Brainflow: 
Inside the test script of your choice, for example lets say you are running filtered_plot.py 

Under the line where it says Params = BrainFlowInputParams()
paste params.serial_port = 'com5' but make surew to substitue the com5 part with the full path the computer assigns to the port number (for example this could look like:    params.serial_port = "/dev/ttyCH341USB0" ) 



## To hardcode port names with the LSL Python test script (for Use with the OpenBCI GUI Fork)

**(Only need to do for the cerelog_lsl.py bridge script):**

By default, the script auto-detects the Cerelog board by scanning all available serial ports. To bypass auto-detection and hardcode a specific port, modify the find_and_open_board() function in tester.py (or cerelog_lsl.py):



  Step 2: Edit the script

  Locate the find_and_open_board() function (line 57 in tester.py, line 59 in cerelog_lsl.py). Replace the entire function with the following:

  def find_and_open_board():
      port_name = "COM3"  # <-- Replace with your actual port name
      print(f"Connecting to hardcoded port: {port_name}")
      ser = serial.Serial(port_name, INITIAL_BAUD_RATE, timeout=2)
      time.sleep(5)
      if ser.in_waiting > 0:
          ser.read(ser.in_waiting)

      print("Sending handshake...")
      current_unix_time = int(time.time())
      checksum_payload = struct.pack('>BI', 0x02, current_unix_time) + bytes([0x01, FIRMWARE_BAUD_RATE_INDEX])
      checksum = sum(checksum_payload) & 0xFF
      handshake_packet = (struct.pack('>BB', HANDSHAKE_START_MARKER_1, 0xBB)
                          + checksum_payload
                          + struct.pack('>B', checksum)
                          + struct.pack('>BB', HANDSHAKE_END_MARKER_1, 0xDD))
      ser.write(handshake_packet)
      time.sleep(0.1)
      ser.baudrate = FINAL_BAUD_RATE
      print(f"Switched to {ser.baudrate} baud...")
      time.sleep(0.5)
      ser.reset_input_buffer()

      bytes_received = ser.read(DATA_PACKET_TOTAL_SIZE * 5)
      if bytes_received and DATA_PACKET_START_MARKER.to_bytes(2, 'big') in bytes_received:
          print(f"SUCCESS on: {port_name}")
          return ser
      else:
          ser.close()
          print(f"FAILED: No valid data received on {port_name}")
          return None

  Step 3: Set your port

  Change the port_name value on the first line of the function to match your system:

  ┌─────────┬───────────────────────────┐
  │   OS    │       Example value       │
  ├─────────┼───────────────────────────┤
  │ Windows │ "COM3"                    │
  ├─────────┼───────────────────────────┤
  │ macOS   │ "/dev/tty.usbserial-1420" │
  ├─────────┼───────────────────────────┤
  │ Linux   │ "/dev/ttyUSB0"            │
  └─────────┴───────────────────────────┘

  Summary of what changed

  The original function scans all serial ports, filters by USB vendor/product ID, and tries each one. The hardcoded version skips all detection logic and connects directly to the specified port, which is useful when the auto-detection picks the wrong device or when running in an
  environment with a known, fixed port assignment.
