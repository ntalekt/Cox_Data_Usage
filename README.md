Cox_Data_Usage
==============
Script designed to get Cox Communications data usage into a JSON format for Home Assisant.

Configuration
---------
Modify the username and password variables to match your Cox main account.

```
cox_user = "username"
cox_pass = "password"
```

Usage Examples
-----
```
sensor:
  - platform: command_line
    name: Cox Usage
    command: "python /home/pi/cox_usage.py"
    unit_of_measurement: "GB"
    value_template: '{{value_json.dumUsage}}'
    scan_interval: 3600
    
  - platform: command_line
    name: Cox Limit
    command: "python /home/pi/cox_usage.py"
    unit_of_measurement: "GB"
    value_template: '{{value_json.dumLimit}}'
    scan_interval: 3600
    
  - platform: command_line
    name: Cox Utilization
    command: "python /home/pi/cox_usage.py"
    unit_of_measurement: "%"
    value_template: '{{value_json.dumUtilization}}'
    scan_interval: 3600
    
  - platform: command_line
    name: Cox Days Left
    command: "python /home/pi/cox_usage.py"
    unit_of_measurement: "Days"
    value_template: '{{value_json.dumDaysLeft}}'
    scan_interval: 3600
```
![Alt text](/img/HA_Example.JPG?raw=true)

Required dependencies
-----
```
sudo pip install BeautifulSoup
sudo pip install mechanize
```
