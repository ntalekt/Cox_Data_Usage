Cox_Data_Usage
==============
Python web scraper designed to return Cox Communications internet usage data for [Home Assistant](https://home-assistant.io/).

Home Assistant Card Example
---------
![Alt text](/img/HA_Example.JPG?raw=true)

Configuration
---------
Modify the `username` and `password` variables to match your Cox main account. Update the `json_file` variable if required.

Put `cox_usage.py` somewhere inside your Home Assistant configuration directory.

```
cox_user = "username"
cox_pass = "password"
json_file = "/home/homeassistant/.homeassistant/cox_usage.json"
```
Automation
-----
```
automation:
  - alias: Query Cox Data Usage
    trigger:
      platform: time
      minutes: '/60'
      seconds: 00
    action:
      service: shell_command.query_cox_data_usage
```
Shell Command Component
-----
```
shell_command:
  query_cox_data_usage: 'python /home/homeassistant/.homeassistant/cox_usage.py'
```
Sensor Component
-----
```
sensor:
  - platform: command_line
    command: cal -h $(date +"%m %Y") | awk 'NF {DAYS = $NF}; END {print DAYS}'
    name: Days In Current Month
    scan_interval: 3600

  - platform: file
    name: Cox Utilization
    file_path: /home/homeassistant/.homeassistant/cox_usage.json
    value_template: >
      {% if value_json is defined %}
        {% if value_json.dumUsage | int == 0 and value_json.dumLimit | int == 0 and value_json.dumUtilization | int == 0 %}
          stats unavailable
        {% else %}
          {{ value_json.dumUsage | int }} / {{ value_json.dumLimit | int }} GB ({{ value_json.dumUtilization | int }} %)
        {% endif %}
      {% else %}
        undefined
      {% endif %}

  - platform: file
    name: Cox Time Left
    file_path: /home/homeassistant/.homeassistant/cox_usage.json
    value_template: >
      {% if value_json is defined %}
        {% if value_json.dumDaysLeft is defined %}
          {{ value_json.dumDaysLeft | int }} Days
        {% else %}
          unknown
        {% endif %}
      {% else %}
        undefined
      {% endif %}

  - platform: file
    name: Cox Avg GB Current
    file_path: /home/homeassistant/.homeassistant/cox_usage.json
    value_template: >
      {% if value_json is defined %}
        {% if value_json.dumUsage | int == 0 and value_json.dumDaysLeft | int == 0 %}
          stats unavailable
        {% elif states.sensor.days_in_current_month.state is defined %}
          {{ (float(value_json.dumUsage) / (float(states.sensor.days_in_current_month.state) - float(value_json.dumDaysLeft))) | round(1) }} GB per day
        {% else %}
          month_undefined
        {% endif %}
      {% else %}
        undefined
      {% endif %}

  - platform: file
    name: Cox Avg GB Remaining
    file_path: /home/homeassistant/.homeassistant/cox_usage.json
    value_template: >
      {% if value_json is defined %}
        {% if value_json.dumLimit | int == 0 and value_json.dumUsage | int == 0 and value_json.dumDaysLeft | int == 0 %}
          stats unavailable
        {% else %}
          {{ ((float(value_json.dumLimit) - float(value_json.dumUsage)) / float(value_json.dumDaysLeft)) | round(1) }} GB per day
        {% endif %}
      {% else %}
        undefined
      {% endif %}
```
Customize
-----
```
customize:
    sensor.cox_utilization:
      icon: mdi:percent
      friendly_name: Utilization
    sensor.cox_time_left:
      icon: mdi:calendar-clock
      friendly_name: Time Left
    sensor.cox_avg_gb_current:
      icon: mdi:chart-line
      friendly_name: Current Daily Avg.
    sensor.cox_avg_gb_remaining:
      icon: mdi:chart-line-stacked
      friendly_name: Remaining Daily Avg.
```

Required dependencies
-----
```
sudo pip install BeautifulSoup
sudo pip install mechanize
```
