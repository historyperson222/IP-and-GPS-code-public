import time
import requests
import platform
import socket
import os
import uuid
import subprocess
import csv

print("\nThese are your GPS coordinates, your IP address, country, city, device type, device model, operating system, IPv6 address, hostname, DNS servers, and public IP address history\n")

time.sleep(0.75)

def get_device_info():
    try:
        # Get the host name
        host_name = socket.gethostname()
        
        # Get the local IP address
        local_ip_address = socket.gethostbyname(host_name)
        
        # Get the public IP address and location details
        response = requests.get("https://ipinfo.io/json")
        data = response.json()
        public_ip_address = data.get('ip', 'Unknown')
        city = data.get('city', 'Unknown')
        region = data.get('region', 'Unknown')
        country = data.get('country', 'Unknown')
        loc = data.get('loc', 'Unknown')  # Latitude and Longitude
        org = data.get('org', 'Unknown')  # ISP information
        
        # Get the device model and system information
        device_model = platform.machine()
        system_info = platform.system() + " " + platform.release()
        
        # Get the MAC address
        mac_address = ':'.join(['{:02x}'.format((uuid.getnode() >> elements) & 0xff) for elements in range(0,2*6,2)][::-1])
        
        return {
            "Host Name": host_name,
            "Local IP Address": local_ip_address,
            "Public IP Address": public_ip_address,
            "Device Model": device_model,
            "System Info": system_info,
            "Region": region,
            "Country": country,
            "Location (Lat, Long)": loc,
            "ISP": org,
            "MAC Address": mac_address
        }
    except Exception as e:
        return {"Error": str(e)}

def get_ipv6_address():
    try:
        hostname = socket.gethostname()
        ipv6_address = socket.getaddrinfo(hostname, None, socket.AF_INET6)
        return ipv6_address[0][4][0]
    except Exception as e:
        return f"Error: {str(e)}"

def get_dns_servers():
    dns_servers = []
    try:
        with open('/etc/resolv.conf', 'r') as file:
            for line in file:
                if line.startswith('nameserver'):
                    dns_servers.append(line.split()[1])
    except FileNotFoundError:
        dns_servers.append("DNS configuration file not found.")
    return dns_servers

def save_ip_history(ip_address):
    history_file = "ip_history.txt"
    try:
        if not os.path.exists(history_file):
            with open(history_file, 'w') as file:
                file.write("Public IP Address History:\n")
        with open(history_file, 'a') as file:
            file.write(f"{time.ctime()}: {ip_address}\n")
    except Exception as e:
        print(f"Error saving IP history: {str(e)}")

def read_ip_history():
    history_file = "ip_history.txt"
    try:
        if os.path.exists(history_file):
            with open(history_file, 'r') as file:
                return file.read()
        return "No IP address history found."
    except Exception as e:
        return f"Error reading IP history: {str(e)}"

def export_ip_history_to_csv():
    history_file = "ip_history.txt"
    csv_file = "book1.csv"
    try:
        if os.path.exists(history_file):
            with open(history_file, 'r') as file:
                lines = file.readlines()
            
            with open(csv_file, 'w', newline='') as csvfile:
                csvwriter = csv.writer(csvfile)
                csvwriter.writerow(["Timestamp", "Public IP Address"])
                
                for line in lines[1:]:  # Skip the header line
                    timestamp, ip_address = line.strip().split(": ")
                    csvwriter.writerow([timestamp, ip_address])
                    
            print(f"IP history has been exported to {csv_file}")
        else:
            print("No IP address history found to export.")
    except Exception as e:
        print(f"Error exporting IP history to CSV: {str(e)}")

def get_uptime():
    try:
        uptime = subprocess.check_output("uptime -p", shell=True).decode().strip()
        return uptime
    except Exception as e:
        return f"Error: {str(e)}"

def get_current_user():
    try:
        user = os.getlogin()
        return user
    except Exception as e:
        return f"Error: {str(e)}"

def get_env_variables():
    try:
        env_vars = {key: os.environ[key] for key in ["PATH", "HOME", "SHELL"]}
        return env_vars
    except Exception as e:
        return f"Error: {str(e)}"

def get_cpu_memory_info():
    try:
        cpu_info = subprocess.check_output("lscpu", shell=True).decode().strip()
        mem_info = subprocess.check_output("free -h", shell=True).decode().strip()
        return {
            "CPU Info": cpu_info,
            "Memory Info": mem_info
        }
    except Exception as e:
        return f"Error: {str(e)}"

def get_wan_details():
    try:
        response = requests.get("https://api64.ipify.org?format=json")
        wan_ip = response.json().get("ip", "Unknown")
        return {"WAN IP Address": wan_ip}
    except Exception as e:
        return {"Error": str(e)}

time.sleep(1)

if __name__ == "__main__": 
    device_info = get_device_info()
    for key, value in device_info.items():
        print(f"\n{key}: {value}\n")
    
    # Save the current public IP address to history
    save_ip_history(device_info.get("Public IP Address", "Unknown"))
    
    time.sleep(1.5)
    
    ipv6_address = get_ipv6_address()
    print(f'\nIPv6 address: {ipv6_address}\n')
    
    time.sleep(1)
    
    dns_servers = get_dns_servers()
    print(f'\nDNS servers: {dns_servers}\n')
    
    time.sleep(1)
    
    current_user = get_current_user()
    print(f'\nCurrent User: {current_user}\n')
    
    time.sleep(1)
    
    ip_history = read_ip_history()
    print(f'\n{ip_history}\n')
    
    time.sleep(1)
    
    uptime = get_uptime()
    print(f'\nUptime: {uptime}\n')
    
    time.sleep(1)
    
    wan_details = get_wan_details()
    for key, value in wan_details.items():
        print(f"\n{key}: {value}\n")
    
    # Export IP history to CSV
    export_ip_history_to_csv()

print('IP Addresses can change, this may lead to discrepancies in the IP history due to the change in WiFi network')
