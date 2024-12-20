# Setting Up Apache Airflow on a GCE VM

## Step 1: Create a GCE VM named `airflow`

1. Navigate to the Google Cloud Console.
2. Go to the **Compute Engine** section.
3. Click on **Create Instance**.
4. Configure the VM:
   - **Name**: `airflow`
   - **Region**: Choose your preferred region (e.g., `us-central1`).
   - **Machine Type**: Choose a machine type (e.g., `e2-medium`).
   - **Boot Disk**: Select a Linux distribution (e.g., Ubuntu 20.04 LTS).
   - **Allow HTTP Traffic**: Check the box.
   - **Access Scopes**: Set to **Allow full access to all Cloud APIs** (required for integrations like BigQuery).
5. Click **Create** to launch the VM.

## Step 2: Create a Storage Bucket named `airflow-bucket`

1. Go to the **Cloud Storage** section in the Google Cloud Console.
2. Click on **Create Bucket**.
3. Configure the bucket:
   - **Name**: `airflow-bucket`
   - **Location Type**: Regional (choose the same region as your VM).
   - **Storage Class**: Standard.
4. Click **Create** to finalize.

## Step 3: Connect to the VM and Set Up Python Virtual Environment

1. SSH into the VM:
   ```bash
   gcloud compute ssh airflow
   ```
2. Update the package list and install necessary tools:
   ```bash
   sudo apt update && sudo apt upgrade -y
   sudo apt install python3-pip python3-venv -y
   ```
3. Create a virtual environment:
   ```bash
   python3 -m venv airflow-venv
   source airflow-venv/bin/activate
   ```
4. Upgrade `pip`:
   ```bash
   pip install --upgrade pip
   ```

## Step 4: Install Apache Airflow

1. Install Apache Airflow using `pip`:
   ```bash
   pip install apache-airflow
   ```
2. Initialize the Airflow database:
   ```bash
   airflow db init
   ```

## Step 5: Configure Airflow to Run on Port 8080

1. Edit the Airflow configuration file:
   ```bash
   nano ~/airflow/airflow.cfg
   ```
2. Locate the `web_server_port` setting and set it to `8080`:
   ```ini
   web_server_port = 8080
   ```
3. Save and exit the file.

## Step 6: Start Airflow Web Server

1. Start the Airflow web server:
   ```bash
   airflow webserver -p 8080
   ```
2. Start the Airflow scheduler in a separate terminal session:
   ```bash
   airflow scheduler
   ```

## Step 7: Access Airflow Web UI

1. Get the external IP of your VM:
   ```bash
   gcloud compute instances list
   ```
   Note the `EXTERNAL_IP` for the `airflow` VM.
2. Open a web browser and navigate to `http://<EXTERNAL_IP>:8080`.

## Step 8: Log in to Airflow

1. Airflow generates a default username and password during initialization. To retrieve it, look for the generated credentials in the terminal logs or in the `airflow.cfg` file.
2. Use the credentials to log in to the Airflow Web UI.

## Additional Configuration (Optional)

### Setting Up Airflow Environment Variables
To customize your Airflow setup, add environment variables in the `airflow.cfg` file.

### Enable Auto-Start for Airflow on VM Boot
Create a systemd service file to auto-start Airflow:

1. Create a systemd service for the Airflow web server:
   ```bash
   sudo nano /etc/systemd/system/airflow-webserver.service
   ```
   Add the following content:
   ```ini
   [Unit]
   Description=Airflow Webserver
   After=network.target

   [Service]
   User=<your-username>
   Group=<your-group>
   Environment="AIRFLOW_HOME=/home/<your-username>/airflow"
   ExecStart=/home/<your-username>/airflow-venv/bin/airflow webserver -p 8080
   Restart=always

   [Install]
   WantedBy=multi-user.target
   ```

2. Enable and start the service:
   ```bash
   sudo systemctl enable airflow-webserver
   sudo systemctl start airflow-webserver
   ```

### Firewall Rules
Ensure that the firewall rules in the GCP console allow inbound traffic on port `8080` for external access to the Airflow web UI.

### Install Additional Dependencies
Depending on your Airflow use case, install provider packages. For example, to integrate with BigQuery:
```bash
pip install apache-airflow-providers-google
```

### Set Up Environment Variables for BigQuery
If using BigQuery or other integrations, set the necessary environment variables (e.g., `GOOGLE_APPLICATION_CREDENTIALS`) for your service account:
```bash
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/your/service-account-key.json"
```

### Persistent Storage for Logs and DAGs
To persist logs and DAG files across VM restarts, consider storing them in the `airflow-bucket` or attaching a persistent disk to the VM.

### Static External IP Address
To avoid IP changes upon VM restarts, reserve a static external IP in the GCP console and assign it to the VM.
