Here is the complete, consolidated **README.md** for your project. This includes the new JMeter container instructions, the automated provisioning details, and the troubleshooting steps.

---

# 🚀 JMeter-InfluxDB-Grafana Performance Stack

This repository contains a fully automated, containerized performance monitoring environment. It uses **Docker Compose** to orchestrate a load generator (JMeter), a time-series database (InfluxDB), and a visualization layer (Grafana).

---

## 📂 Project Structure

To ensure the automation works, keep your files organized as follows:

```text
.
├── docker-compose.yml         # Container orchestration
├── init_project.ps1           # Setup script (Run this to create folders/files)
├── dashboards/                # Place your .json dashboards here
│   └── jmeter-dashboard.json  # Your pre-configured dashboard
├── scripts/                   # Place your JMeter .jmx files here
├── results/                   # JMeter .jtl and .log files will save here
└── provisioning/              # Auto-configuration files
    ├── datasources/
    │   └── datasource.yml     # Pre-connects InfluxDB to Grafana
    └── dashboards/
        └── dashboard-provider.yml # Auto-loads the JSON dashboards

```

---

## ⚡ Quick Start

### 1. Initialize Project

Run the `init_project.ps1` script (or create the folders manually) to generate the configuration files.

### 2. Start the Stack

In your terminal, run:

```bash
docker-compose up -d

```

### 3. Access the Tools

* **Grafana:** [http://localhost:3000](https://www.google.com/search?q=http://localhost:3000) (User: `admin` | Pass: `admin`)
* **InfluxDB:** [http://localhost:8086](https://www.google.com/search?q=http://localhost:8086) (User: `admin` | Pass: `password12345`)

---

## 🧪 Running a Load Test

### 1. Configure JMeter (.jmx)

Open your `.jmx` file and locate the **Backend Listener**. Set the following parameters:

* **influxdbUrl:** `http://influxdb:8086/api/v2/write?org=my-org&bucket=jmeter_results`
* **influxdbToken:** `my-super-secret-token-123`
* **application:** `my_app` (Matches the dashboard variable)

> **Note:** We use `influxdb:8086` instead of `localhost` because JMeter is running inside the Docker network.

### 2. Execute the Test

Run this command from your host machine to trigger the test inside the container:

```bash
docker exec -it jmeter jmeter -n -t /scripts/your_test.jmx -l /results/run1.jtl

```

### 3. Stopping the Test

To stop the test gracefully:

```bash
docker exec -it jmeter /opt/apache-jmeter/bin/shutdown.sh

```

---

## 📊 Dashboard Management

### Automation

Any JSON dashboard file placed in the `./dashboards` folder is automatically imported into the **"Performance"** folder in Grafana when the container starts.

### Saving Changes Permanently

If you modify a dashboard in the Grafana UI:

1. **Export** the JSON (Share > Export > Save to file).
2. **Verify UID:** Ensure the JSON uses `"uid": "InfluxDB_JMeter"`.
3. **Overwrite** the file in your local `./dashboards` folder.
4. **Reload:** Run `docker-compose restart grafana`.

---

## 🛠️ Essential Commands

| Task | Command |
| --- | --- |
| **Start everything** | `docker-compose up -d` |
| **Stop everything** | `docker-compose down` |
| **Reset (Delete all data)** | `docker-compose down -v` |
| **Check logs** | `docker-compose logs -f grafana` |
| **List Influx Buckets** | `docker exec -it influxdb influx bucket list` |

---

## ⚠️ Troubleshooting

* **No Data in Grafana:** Ensure the **Time Range** (top right) is set to "Last 5 minutes" and the **Bucket** variable matches `jmeter_results`.
* **Connection Refused:** Ensure your `.jmx` points to `http://influxdb:8086`.
* **Dashboard Empty:** Check your JSON file for the correct Data Source UID (`InfluxDB_JMeter`) and ensure the `application` tag in JMeter matches the one in Grafana.