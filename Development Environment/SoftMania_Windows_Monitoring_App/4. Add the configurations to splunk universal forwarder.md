# Configuring Splunk Universal Forwarder

## Edit the `inputs.conf`

1. **Navigate to the directory**:
   - `C:\Program Files\SplunkUniversalForwarder\etc\apps\SplunkUniversalForwarder\local`

2. **Edit the `inputs.conf` file**:
   - Add the following configurations:

   ```ini
   [perfmon://CPU Load]
   counters = % Processor Time;% User Time
   instances = _Total
   interval = 10
   index = softmania_windows_monitoring_idx
   object = Processor

   [perfmon://Available Memory]
   counters = Available Bytes
   interval = 10
   index = softmania_windows_monitoring_idx
   object = Memory

   [perfmon://Free Disk Space]
   counters = Free Megabytes;% Free Space
   instances = _Total
   interval = 60
   index = softmania_windows_monitoring_idx
   object = LogicalDisk
  


## Edit the `outputs.conf`

1. Navigate to the directory:

- **C:\Program Files\SplunkUniversalForwarder\etc\system\local**

2. Edit the `outputs.conf` file:
- Add the following configurations:

```ini
[tcpout]
defaultGroup = default-autolb-group

[tcpout:default-autolb-group]
server = "splunk instance IP address":9997

[tcpout-server://"splunk instance IP address":9997]
```

## Final Steps
1. Restart the Universal Forwarder to apply the changes.

2. Log in to the Splunk Indexer using your credentials.

3. Run the following search query within the **"softmania_windows_monitoring_app"**:

```ini
index="softmania_windows_monitoring_idx"
```
