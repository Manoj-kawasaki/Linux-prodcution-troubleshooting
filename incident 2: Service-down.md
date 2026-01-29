# Service Down – Nginx / SSH Won’t Start 

- Category: Service Availability | Port Conflict | Configuration Error
- Environment: Linux (WSL2 / Ubuntu)

# Objective
Simulate a real-world service outage where Nginx or SSH fails to start, investigate the failure using system-level tools identify the root cause (configuration errors or port conflicts), and restore service availability using safe, production-style remediation techniques.

# Impact (Production Context)
* Nginx downtime makes the application unreachable, causing failed user requests and potential business impact.
SSH downtime prevents administrators from accessing the server, blocking emergency recovery and maintenance tasks.
- Step 1: Simulate the Failure
* Option A: Stop the Service
- sudo systemctl stop nginx
* Option B: Break Configuration (Syntax Error Simulation)
- sudo nano /etc/nginx/nginx.conf
- Introduce an invalid directive or typo
* Attempt to start the service:
- sudo systemctl start nginx
- Expected result:
Service enters failed state.
# Step 2: Detect the Issue
- Check service status:
- sudo systemctl status nginx
- This shows whether the service is active, inactive, or failed, along with recent error messages.
- Check service logs:
- sudo journalctl -u nginx --since "5 minutes ago"
- Example output:
- invalid directive in /etc/nginx/nginx.conf
* Step 3: Investigate
- Validate Configuration Syntax
- sudo nginx -t
- Identifies syntax or configuration errors before restarting the service.
- Check for Port Conflicts
- sudo lsof -i :80
- sudo ss -tuln | grep 80
- Determines if another process is already using the required port.
- SSH Investigation (Optional)
- sudo systemctl status ssh
- sudo journalctl -u ssh
# Root Cause Analysis
The service failure was caused by one of the following:
- Invalid syntax in the Nginx configuration file
- Another process already binding to port 80, preventing Nginx from listening on the required port
* Step 4: Resolve
- Fix Configuration Errors
- sudo nano /etc/nginx/nginx.conf
- sudo nginx -t
- sudo systemctl restart nginx
- Free a Blocked Port
- sudo kill -9 <PID>
- sudo systemctl restart nginx
- Enable Service on Boot
- sudo systemctl enable nginx

# Alternate Resolution: Port Switch (Temporary Mitigation)
- If port 80 remains unavailable, switch Nginx to port 8080 to restore service quickly.
- Edit config:
sudo nano /etc/nginx/sites-enabled/default
- Update listen directive:
Nginx
server {
    listen 8080;
    server_name localhost;
}
- Test and restart:
- sudo nginx -t
- sudo systemctl restart nginx
- Verify:
sudo lsof -i :8080
curl -I http://localhost:8080
- Expected:
HTTP/1.1 200 OK
- Step 5: Verification
- Confirm service status:
- sudo systemctl status nginx
Functional test:
curl -I http://localhost
For SSH:
ssh localhost

# Key Learnings
- systemctl → Service management and status
- journalctl → Service-level logs
- nginx -t → Configuration validation
- lsof / ss → Port conflict detection
- Port switching → Temporary mitigation strategy to restore availability

# Prevention & Best Practices
- Always validate config before restart:
- sudo nginx -t
- Monitor service health using uptime checks or systemd alerts
- Document port assignments to avoid conflicts
- Enable log rotation to prevent disk-related service failures 

# Professional Summary
- This incident demonstrates my ability to:
- Diagnose service failures using system-level tools and logs
- Identify root causes such as configuration errors and port conflicts
- Apply safe remediation strategies to restore availability
Validate recovery through real functional testing
- Think in terms of both technical resolution and business impact
# This Shows That I:
- Troubleshoot systematically rather than guessing
- Learn from operational mistakes
- Document clearly for team knowledge sharing
- Communicate technical issues in professional, simple terms
