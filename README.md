# ETL Weather Data Pipeline

A robust, automated data pipeline that extracts real-time weather data from the Open-Meteo API, transforms it into a structured format, and loads it into a PostgreSQL database using Apache Airflow for orchestration.

## 🌟 Features

- **Real-time Data Extraction**: Fetches current weather data from Open-Meteo API
- **Automated Processing**: Transforms raw API data into structured format
- **Persistent Storage**: Stores processed data in PostgreSQL database
- **Workflow Orchestration**: Uses Apache Airflow for scheduling and monitoring
- **Containerized Environment**: Docker-based setup for consistent deployment
- **Cloud-Ready**: Easy transition to AWS RDS for production deployment

## 🏗️ Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Open-Meteo    │───▶│   Apache Airflow │───▶│   PostgreSQL    │
│      API        │    │   (Orchestrator) │    │    Database     │
└─────────────────┘    └──────────────────┘    └─────────────────┘
     Extract                 Transform                 Load
```

## 🛠️ Technology Stack

- **Apache Airflow**: Workflow orchestration and scheduling
- **Astro CLI**: Airflow development and management
- **Docker**: Containerization platform
- **PostgreSQL**: Relational database for data storage
- **Python**: Pipeline logic and data processing
- **AWS RDS**: Production database deployment (optional)

## 📋 Prerequisites

Before setting up the project, ensure you have the following installed:

- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [Visual Studio Code](https://code.visualstudio.com/) (recommended)
- [DBeaver](https://dbeaver.io/) (recommended for database management)
- AWS Account (for production deployment)

## 🚀 Quick Start

### 1. Install Astro CLI

**macOS & Linux:**
```bash
/bin/bash -c "$(curl -sSL https://install.astronomer.io)"
```

**Windows (PowerShell as Administrator):**
```powershell
Invoke-WebRequest -Uri "https://install.astronomer.io" -OutFile "install.ps1"; .\install.ps1
```

### 2. Clone and Setup Project

```bash
git clone https://github.com/yourusername/etl-weather-pipeline.git
cd etl-weather-pipeline
```

### 3. Start the Environment

```bash
astro dev start
```

This command will:
- Build Docker images
- Start Airflow components (webserver, scheduler, triggerer)
- Start PostgreSQL database
- Make Airflow UI available at `http://localhost:8081`

**Default Credentials:**
- Username: `admin`
- Password: `admin`

### 4. Configure Connections

Navigate to **Admin → Connections** in the Airflow UI and create:

**PostgreSQL Connection:**
- Connection Id: `postgres_default`
- Connection Type: `Postgres`
- Host: `postgres_db`
- Schema: `postgres`
- Login: `postgres`
- Password: `postgres`
- Port: `5432`

**API Connection:**
- Connection Id: `open_meteo_api`
- Connection Type: `HTTP`
- Host: `https://api.open-meteo.com`

### 5. Run the Pipeline

1. Navigate to the DAGs page in Airflow UI
2. Find `weather_etl_pipeline`
3. Un-pause the DAG using the toggle switch
4. Click "Trigger DAG" to run manually

## 📊 Data Schema

The pipeline creates a `weather_data` table with the following structure:

| Column        | Type      | Description                    |
|---------------|-----------|--------------------------------|
| latitude      | FLOAT     | Location latitude              |
| longitude     | FLOAT     | Location longitude             |
| temperature   | FLOAT     | Current temperature (°C)       |
| windspeed     | FLOAT     | Wind speed (km/h)              |
| winddirection | FLOAT     | Wind direction (degrees)       |
| weathercode   | INT       | Weather condition code         |
| timestamp     | TIMESTAMP | Data insertion timestamp       |

## 🔍 Verifying Data

Connect to PostgreSQL using DBeaver or any SQL client:

**Connection Settings:**
- Host: `localhost`
- Port: `5433`
- Database: `postgres`
- Username: `postgres`
- Password: `postgres`

**Query to view data:**
```sql
SELECT * FROM weather_data ORDER BY timestamp DESC;
```

## 🌐 Production Deployment (AWS)

### 1. Create AWS RDS Instance

1. Navigate to AWS RDS Console
2. Create a new PostgreSQL instance
3. Note the endpoint URL, username, and password

### 2. Update Airflow Connection

In the Airflow UI, edit the `postgres_default` connection:
- **Host**: Replace `postgres_db` with your RDS endpoint
- **Login**: Update with RDS master username
- **Password**: Update with RDS master password

### 3. Deploy Airflow

Deploy using:
- **Amazon MWAA** (Managed Workflows for Apache Airflow)
- **EC2/EKS** with Astronomer deployment guides

## 🔧 Configuration

### Environment Variables

The pipeline uses the following default configuration:

- **Target Location**: Richardson, Texas (32.7767°N, -96.7970°W)
- **Schedule**: Daily execution (`@daily`)
- **Database**: PostgreSQL 13
- **API**: Open-Meteo (free weather API)

### Customization

To change the location, modify the constants in `dags/etl_weather_dag.py`:

```python
LATITUDE = '32.7767'    # Your latitude
LONGITUDE = '-96.7970'  # Your longitude
```

## 🐛 Troubleshooting

### Common Issues

**Port Conflicts:**
If ports 8081 or 5433 are already in use, modify the port mappings in `docker-compose.override.yml`

**Docker Issues:**
- Ensure Docker Desktop is running
- Check Docker daemon status
- For Windows: Enable WSL 2 or Hyper-V

**Connection Errors:**
- Verify connection configurations in Airflow UI
- Ensure PostgreSQL container is running
- Check network connectivity

### Logs

View logs for debugging:
```bash
astro dev logs
```

## 📈 Monitoring

- **Airflow UI**: Monitor DAG runs, task status, and logs
- **Database**: Query `weather_data` table for data verification
- **Docker**: Use `docker ps` to check container status

## 🔄 Development Workflow

1. **Make Changes**: Modify DAG files in the `dags/` directory
2. **Test Locally**: Changes are automatically picked up by Airflow
3. **Verify**: Check DAG execution in Airflow UI
4. **Deploy**: Push changes to production environment

## 📚 Additional Resources

- [Apache Airflow Documentation](https://airflow.apache.org/docs/)
- [Astronomer Astro CLI](https://docs.astronomer.io/astro/cli/overview)
- [Open-Meteo API Documentation](https://open-meteo.com/en/docs)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 🙏 Acknowledgments

This project was developed following a comprehensive tutorial from YouTube (https://www.youtube.com/watch?v=Y_vQyMljDsE&t=791s&ab_channel=KrishNaik). Special thanks to the tutorial creator for providing clear guidance on building ETL pipelines with Apache Airflow and modern data engineering practices.

---

**Built with ❤️ using Apache Airflow and modern data engineering tools**
