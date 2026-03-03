## README: JMeter-InfluxDB-Grafana Performance Monitoring Stack

This project provides a fully automated, containerized environment for real-time performance testing results. It integrates **JMeter**, **InfluxDB 2.x**, and **Grafana** with automated provisioning to ensure your dashboards and data sources are ready out-of-the-box.

---

### ## 1. Project Structure

To ensure the automation works, keep your files organized as follows:

```text
.
├── docker-compose.yml         # Container orchestration
├── commands.txt               # Quick reference for Docker commands
├── dashboards/                # JSON dashboard storage
│   └── jmeter-dashboard.json  # Your pre-configured dashboard file
└── provisioning/              # Grafana auto-configuration
    ├── datasources/
    │   └── datasource.yml     # Auto-links InfluxDB to Grafana
    └── dashboards/
        └── dashboard-provider.yml # Auto-loads the JSON above

```

---

### ## 2. Quick Start

1. **Launch the stack:**
```bash
docker-compose up -d

```


2. **Access the interfaces:**
* **Grafana:** `http://localhost:3000` (Default: `admin` / `admin`)
* **InfluxDB:** `http://localhost:8086` (Default: `admin` / `password12345`)



---

### ## 3. Connection Settings

#### **InfluxDB 2.x Configuration**

* **Organization:** `my-org`
* **Bucket:** `jmeter_results`
* **Admin Token:** `my-super-secret-token-123`

#### **JMeter Backend Listener Configuration**

In your JMeter Test Plan, add a **Backend Listener** and set these parameters:

* **influxdbUrl:** `http://localhost:8086/api/v2/write?org=my-org&bucket=jmeter_results`
* **influxdbToken:** `my-super-secret-token-123`
* **application:** `my_app` (Matches the dashboard variable)

---

### ## 4. Essential Docker Commands

| Task | Command |
| --- | --- |
| **Start everything** | `docker-compose up -d` |
| **Stop everything** | `docker-compose down` |
| **Reset (Delete all data)** | `docker-compose down -v` |
| **Apply JSON changes** | `docker-compose restart grafana` |
| **Debug connection** | `docker-compose logs -f influxdb` |
| **Check for JSON errors** | `docker-compose logs -f grafana` |

---

### ## 5. Saving Dashboard Changes

By default, changes made in the Grafana UI are lost if the volume is deleted. To make them permanent:

1. **Export:** In the Grafana UI, go to Share > Export > Save to file.
2. **Clean:** Open the JSON file and ensure `"uid"` is set to `"InfluxDB_JMeter"`.
3. **Replace:** Overwrite the file in `./dashboards/jmeter-dashboard.json`.
4. **Reload:** Run `docker-compose restart grafana`.

---

### ## 6. Troubleshooting "No Data"

* **Bucket dropdown:** Ensure the dropdown at the top of the dashboard is set to `jmeter_results`.
* **Time Range:** Ensure the time picker (top right) is set to a window where the test was actually running (e.g., "Last 5 minutes").
* **JMeter Logs:** Click the yellow triangle in the top right of JMeter to check for `401` (Token error) or `404` (Bucket/Org error).