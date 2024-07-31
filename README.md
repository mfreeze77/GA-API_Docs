# GA-API_Docs

# Gallagher Command Centre REST API Documentation

## Table of Contents
1. [Introduction](#introduction)
2. [Features](#features)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Usage](#usage)
6. [API Endpoints](#api-endpoints)
7. [Authentication](#authentication)
8. [Examples](#examples)
9. [Use Cases](#use-cases)
   - [Use Case 1: Device Monitoring](#use-case-1-device-monitoring)
   - [Use Case 2: Access Control Management](#use-case-2-access-control-management)
   - [Use Case 3: Alarm Management](#use-case-3-alarm-management)
   - [Use Case 4: Reporting and Analytics](#use-case-4-reporting-and-analytics)
10. [Troubleshooting](#troubleshooting)
11. [Contributing](#contributing)
12. [License](#license)
13. [Contact](#contact)

## Introduction

Welcome to the cc-rest-docs repository. This project contains comprehensive documentation for the REST API provided by Gallagher Security. The API enables integration with various security systems and facilitates seamless communication between client applications and the Gallagher Command Centre.

## Features

- **Comprehensive API Documentation**: Detailed descriptions of all available API endpoints.
- **Authentication Methods**: Guidance on securing API requests using different authentication methods.
- **Error Handling**: Information on common errors and how to handle them.
- **Example Requests and Responses**: Practical examples to help you get started quickly.

## Installation

To install and set up the documentation locally, follow these steps:

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/GallagherSecurity/cc-rest-docs.git
Navigate to the Directory:
bash
Copy code
cd cc-rest-docs
Install Dependencies (if applicable):
bash
Copy code
npm install
Configuration
Depending on your environment, you may need to configure certain settings. Here’s an example configuration file:

json
Copy code
{
  "api_base_url": "https://api.gallagher.com/v1",
  "auth_token": "your_api_token_here",
  "timeout": 5000
}
Replace your_api_token_here with your actual API token.

Usage
The API provides a comprehensive set of endpoints for managing and monitoring security systems. This section outlines how to use the API for different functionalities, including device management, access control, alarm monitoring, and reporting.

Getting Started
To start using the API, you'll need an API key or OAuth 2.0 token. Refer to the Authentication section for more details.

Device Management
List Devices: Retrieve a list of all devices registered in the system.
Add a New Device: Create and configure a new device.
Update Device Information: Modify existing device details.
Delete a Device: Remove a device from the system.
Access Control
Create Access Levels: Define access levels and assign them to users.
Manage User Credentials: Add, update, or delete user credentials.
Monitor Access Logs: Track entry and exit events in real-time.
Alarm Monitoring
Get Alarm Status: Retrieve the current status of all alarms.
Acknowledge Alarms: Acknowledge and silence active alarms.
Retrieve Alarm History: Access a log of past alarms and events.
Reporting
Generate Reports: Create and download various reports, such as device status, access logs, and alarm history.
API Endpoints
GET /devices
Retrieve a list of devices.

Request:

http
Copy code
GET /devices HTTP/1.1
Host: api.gallagher.com
Authorization: Bearer {token}
Response:

json
Copy code
[
  {
    "id": "device1",
    "name": "Main Door Sensor",
    "status": "active"
  },
  ...
]
... (List more endpoints similarly)

Authentication
The API supports multiple authentication methods:

API Key: Use an API key as a query parameter or in the header.
OAuth 2.0: For secure and scalable access.
Example: Using OAuth 2.0
To authenticate using OAuth 2.0, first obtain an access token and then include it in the Authorization header of your requests:

http
Copy code
Authorization: Bearer {access_token}
Examples
Example 1: Retrieving All Devices
bash
Copy code
curl -X GET "https://api.gallagher.com/v1/devices" -H "Authorization: Bearer your_api_token"
Example 2: Creating a New Device
bash
Copy code
curl -X POST "https://api.gallagher.com/v1/devices" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer your_api_token" \
-d '{
  "name": "New Sensor",
  "type": "sensor"
}'
Use Cases
Use Case 1: Device Monitoring
Scenario: A facility manager wants to monitor the status of all security devices in a building to ensure they are functioning correctly.

Steps:

List All Devices: Use the /devices endpoint to retrieve a list of all devices.
Check Device Status: Parse the response to identify devices that are offline or in an error state.
Alert on Issues: Implement an alert system to notify the manager if any devices are not functioning properly.
Example:

bash
Copy code
curl -X GET "https://api.gallagher.com/v1/devices" -H "Authorization: Bearer your_api_token"
Response:

json
Copy code
[
  { "id": "camera1", "name": "Entrance Camera", "status": "active" },
  { "id": "sensor1", "name": "Window Sensor", "status": "offline" }
]
Use Case 2: Access Control Management
Scenario: An HR manager needs to update access levels for a new employee joining the company.

Steps:

Create Access Level: Use the /access-levels endpoint to define the new employee's access permissions.
Assign Credentials: Use the /users/{user_id}/credentials endpoint to assign a new credential (e.g., keycard) to the employee.
Monitor Access Logs: Regularly check /access-logs to ensure the employee is accessing authorized areas.
Example:

curl -X POST "https://api.gallagher.com/v1/access-levels" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer your_api_token" \
-d '{
  "name": "New Employee Level",
  "permissions": ["access_floor_1", "access_floor_2"]
}'
Use Case 3: Alarm Management
Scenario: A security team needs to manage alarms triggered in a building, including acknowledging and resolving them.

Steps:

Retrieve Active Alarms: Use /alarms to get a list of active alarms.
Acknowledge Alarms: Send a request to /alarms/{alarm_id}/acknowledge to acknowledge an alarm.
Resolve Alarms: After addressing the issue, use /alarms/{alarm_id}/resolve to mark the alarm as resolved.
Example:

bash
Copy code
curl -X POST "https://api.gallagher.com/v1/alarms/{alarm_id}/acknowledge" \
-H "Authorization: Bearer your_api_token"
Use Case 4: Reporting and Analytics
Scenario: A facility manager wants to generate a report on access logs for the past month to review security breaches.

Steps:

Specify Report Parameters: Define the time range and types of events to include.
Generate Report: Use the /reports endpoint to generate the report.
Download Report: Access the report URL provided in the response.
Example:

bash
Copy code
curl -X POST "https://api.gallagher.com/v1/reports" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer your_api_token" \
-d '{
  "type": "access_logs",
  "date_range": {
    "start": "2024-01-01T00:00:00Z",
    "end": "2024-01-31T23:59:59Z"
  }
}'
Troubleshooting
Common Errors
401 Unauthorized: Ensure your API token is valid and has not expired.
404 Not Found: Check the endpoint URL for typos.
Debugging Tips
Use a tool like Postman or cURL to test API requests.
Check the API logs for detailed error messages.

Use Case 1: Device Monitoring
Scenario: A facility manager is responsible for overseeing a building's security system, which includes cameras, sensors, and alarm systems. They need to monitor the status of these devices in real-time to ensure the system's integrity and functionality.

Step-by-Step Process:
Retrieve Device List:

Objective: Obtain a comprehensive list of all security devices in the system.
API Endpoint: GET /devices
Request:
http
Copy code
GET /devices HTTP/1.1
Host: api.gallagher.com
Authorization: Bearer {token}
Response:
json
Copy code
[
  {
    "id": "device1",
    "name": "Main Door Sensor",
    "type": "sensor",
    "status": "active",
    "last_checked": "2024-07-30T12:45:00Z"
  },
  {
    "id": "device2",
    "name": "Entrance Camera",
    "type": "camera",
    "status": "offline",
    "last_checked": "2024-07-30T12:50:00Z"
  }
]
Parse Device Status:

Objective: Determine the current operational status of each device.
Considerations: Devices may have statuses such as "active," "offline," "maintenance," or "error."
Example Code:
python
Copy code
def check_device_status(devices):
    for device in devices:
        if device['status'] != 'active':
            print(f"Alert: {device['name']} is {device['status']}")
Set Up Alert System:

Objective: Notify the facility manager if any device is offline or in an error state.
Implementation: Integrate with an alerting system (e.g., email, SMS, or a monitoring dashboard).
Example:
python
Copy code
def send_alert(device_name, status):
    message = f"Device {device_name} is currently {status}."
    # Code to send an email or SMS alert
    print(message)

for device in devices:
    if device['status'] != 'active':
        send_alert(device['name'], device['status'])
Automate Regular Checks:

Objective: Schedule regular checks to monitor device status.
Tools: Use cron jobs or a scheduling library like schedule in Python.
Example:
python
Copy code
import schedule
import time

def job():
    # Retrieve and check device status
    devices = get_devices()
    check_device_status(devices)

schedule.every(10).minutes.do(job)

while True:
    schedule.run_pending()
    time.sleep(1)
Log and Report:

Objective: Maintain a log of device statuses for auditing and analysis.
Implementation: Store logs in a database or log management system.
Example:
python
Copy code
def log_device_status(device):
    log_entry = {
        "device_id": device['id'],
        "status": device['status'],
        "timestamp": device['last_checked']
    }
    # Code to save log_entry to a database
Use Case 2: Access Control Management
Scenario: An HR manager needs to manage employee access to different building areas. This includes creating access levels, assigning credentials, and monitoring access logs.

Step-by-Step Process:
Create Access Levels:

Objective: Define different levels of access permissions based on roles.
API Endpoint: POST /access-levels
Request:
http
Copy code
POST /access-levels HTTP/1.1
Host: api.gallagher.com
Content-Type: application/json
Authorization: Bearer {token}

{
  "name": "Admin Level",
  "permissions": ["access_server_room", "access_office_areas"]
}
Response:
json
Copy code
{
  "id": "accesslevel123",
  "name": "Admin Level",
  "status": "created"
}
Assign Credentials to Users:

Objective: Link physical credentials (e.g., keycards) or digital tokens to user accounts.
API Endpoint: POST /users/{user_id}/credentials
Request:
http
Copy code
POST /users/123/credentials HTTP/1.1
Host: api.gallagher.com
Content-Type: application/json
Authorization: Bearer {token}

{
  "credential_type": "keycard",
  "credential_id": "card123"
}
Response:
json
Copy code
{
  "status": "success",
  "message": "Credential assigned"
}
Monitor Access Logs:

Objective: Track user entry and exit events.
API Endpoint: GET /access-logs
Request:
http
Copy code
GET /access-logs HTTP/1.1
Host: api.gallagher.com
Authorization: Bearer {token}
Response:
json
Copy code
[
  {
    "timestamp": "2024-07-30T09:00:00Z",
    "user_id": "123",
    "event": "entry",
    "location": "Main Office"
  },
  {
    "timestamp": "2024-07-30T17:00:00Z",
    "user_id": "123",
    "event": "exit",
    "location": "Main Office"
  }
]
Audit and Compliance:

Objective: Ensure that access control policies comply with security protocols and regulations.
Considerations: Regularly review and update access levels and permissions.
Example:
python
Copy code
def audit_access_logs(logs):
    for log in logs:
        if not is_compliant(log):
            print(f"Compliance issue detected: {log}")
Use Case 3: Alarm Management
Scenario: The security team needs to respond to triggered alarms, acknowledge them, and take necessary actions.

Step-by-Step Process:
Retrieve Active Alarms:

Objective: Get a list of all currently active alarms.
API Endpoint: GET /alarms
Request:
http
Copy code
GET /alarms HTTP/1.1
Host: api.gallagher.com
Authorization: Bearer {token}
Response:
json
Copy code
[
  {
    "id": "alarm1",
    "type": "door_forced",
    "location": "Main Entrance",
    "timestamp": "2024-07-30T10:00:00Z",
    "status": "active"
  }
]
Acknowledge Alarms:

Objective: Confirm that the alarm has been noticed and is being addressed.
API Endpoint: POST /alarms/{alarm_id}/acknowledge
Request:
http
Copy code
POST /alarms/alarm1/acknowledge HTTP/1.1
Host: api.gallagher.com
Authorization: Bearer {token}
Response:
json
Copy code
{
  "status": "acknowledged",
  "timestamp": "2024-07-30T10:05:00Z"
}
Investigate and Resolve:

Objective: Determine the cause of the alarm and resolve the issue.
Procedure: Security personnel should investigate the location and report findings.
API Endpoint: POST /alarms/{alarm_id}/resolve
Request:
http
Copy code
POST /alarms/alarm1/resolve HTTP/1.1
Host: api.gallagher.com
Authorization: Bearer {token}
Response:
json
Copy code
{
  "status": "resolved",
  "timestamp": "2024-07-30T10:30:00Z"
}
Document and Report:

Objective: Log the incident and actions taken for future reference.
Considerations: Include details about the alarm, investigation, and resolution.
Example:
python
Copy code
def log_alarm_resolution(alarm_id, status):
    log_entry = {
        "alarm_id": alarm_id,
        "status": status,
        "resolved_at": "2024-07-30T10:30:00Z"
    }
    # Code to save log_entry to a database
Use Case 4: Reporting and Analytics
Scenario: A facility manager wants to analyze security data, such as access logs and alarm events, to identify patterns and improve security measures.

Step-by-Step Process:
Define Report Criteria:

Objective: Specify the data to be included in the report, such as date ranges and types of events.
API Endpoint: POST /reports
Request:
http
Copy code
POST /reports HTTP/1.1
Host: api.gallagher.com
Content-Type: application/json
Authorization: Bearer {token}

{
  "type": "access_logs",
  "date_range": {
    "start": "2024-07-01T00:00:00Z",
    "end": "2024-07-31T23:59:59Z"
  },
  "filters": {
    "locations": ["Main Office", "Server Room"]
  }
}
Response:
json
Copy code
{
  "report_id": "report123",
  "status": "generating",
  "estimated_completion": "2024-07-31T12:00:00Z"
}
Generate Report:

Objective: Compile the requested data into a report.
Procedure: The API processes the request and generates the report.
Considerations: Depending on the data volume, report generation may take some time.
Download Report:

Objective: Access the generated report.
API Endpoint: GET /reports/{report_id}
Request:
http
Copy code
GET /reports/report123 HTTP/1.1
Host: api.gallagher.com
Authorization: Bearer {token}
Response:
json
Copy code
{
  "report_id": "report123",
  "status": "completed",
  "download_url": "https://api.gallagher.com/reports/report123/download"
}
Analyze Report Data:

Objective: Review the report data to identify trends, anomalies, and potential security gaps.
Tools: Use data analysis tools or software (e.g., Excel, Tableau) to visualize and analyze the data.
Example:
python
Copy code
def analyze_report_data(report_data):
    # Code to analyze data and generate insights
    insights = extract_insights(report_data)
    return insights
Implement Improvements:

Objective: Based on the analysis, implement changes to improve security measures.
Considerations: This may include updating access levels, improving alarm response protocols, or enhancing monitoring capabilities.
