# Tracks: Containerized GTD™ Web Application

This repository provides an independently deployable, containerized fork of the original TracksApp/tracks project. Designed to eliminate runtime environment dependencies, it offers pre-configured Apptainer (formerly Singularity) and Docker environments. Whether you are deploying on a local workstation, a traditional cloud server, or a High-Performance Computing (HPC) cluster running SLURM, this fork ensures a frictionless setup out of the box. 

The application comes pre-packaged with an embedded SQLite database and a default administrative account (`admin` / `admin`), allowing for immediate use without prior configuration. Furthermore, a fully automated CI/CD pipeline ensures that both `.sif` and `.tar` artifacts are built, tested, and published on GitHub for every new release.

---

## Deployment & Quick Start

You can download the latest pre-built containers directly from the [Releases page](https://github.com/jasonschurawel/tracks-apptainer/releases). Choose the runtime environment that best fits your infrastructure.

### Apptainer / Singularity (Recommended for HPC)
Apptainer is the recommended runtime for HPC and rootless environments, as it requires no background daemon and executes entirely within the user's privilege space. To get started, simply download the latest standalone container and execute it directly. Integrating the application into a SLURM-managed cluster is straightforward. The container can be submitted as a background batch job or executed within an interactive shell session, making it highly suitable for academic and research environments.

```bash
wget [https://github.com/jasonschurawel/tracks-apptainer/releases/latest/download/tracks_apptainer.sif](https://github.com/jasonschurawel/tracks-apptainer/releases/latest/download/tracks_apptainer.sif)
apptainer run tracks_apptainer.sif

# Submit as a SLURM batch job 
sbatch --wrap="apptainer run tracks_apptainer.sif"
```

### Docker Integration
For traditional cloud or local deployments, standard Docker images are provided. You can download the latest image archive, load it into your local Docker daemon, and map the necessary ports to access the web interface.

```bash
wget [https://github.com/jasonschurawel/tracks-apptainer/releases/latest/download/tracks_docker.tar](https://github.com/jasonschurawel/tracks-apptainer/releases/latest/download/tracks_docker.tar)
docker load < tracks_docker.tar
docker run -p 3000:3000 tracks-apptainer_web:latest
```

---

## Data Persistence Strategy

By default, container filesystems are ephemeral, meaning any changes to the database or application state will be lost upon restart. To achieve data persistence across lifecycles, you must bind a local host directory to the container's internal `/tmp` directory, where the SQLite database resides. 

You can manage this automatically using the provided Makefile commands (`make setup-persistent` followed by `make apptainer-run-persistent`), or manually by explicitly passing the bind flag during execution:

```bash
mkdir -p tracks_data
apptainer run --bind ./tracks_data:/tmp tracks_apptainer.sif
```

---

## Management & Command Reference

A comprehensive `Makefile` is included to abstract complex container operations and streamline the administrative workflow. Through this interface, you can initiate interactive builds for both runtime environments using `sudo make build`. 

For lifecycle management, you can spawn or terminate instances via `make apptainer-run`, `make docker-run`, and `make docker-stop`. Should you encounter orphaned Rails or Apptainer processes, `make apptainer-clean` will resolve them. Additionally, a destructive command (`sudo make clean-build`) is available to purge all previous build artifacts, cached layers, and local data when a complete reset is required.

---

## Upstream Project & Credits

For comprehensive documentation, contribution guidelines, and licensing details under the GPL, please refer to the official [getontracks.org](http://www.getontracks.org/) project homepage or the upstream [TracksApp/tracks](https://github.com/TracksApp/tracks) repository on GitHub.
