# CineRate Microservices

This repository contains the microservices for the CineRate application, a movie and TV show review platform. Each service is containerized and can be run, tested, and deployed independently. The services are managed as Git submodules, each with its own repository.

## Services
Each service is maintained in its own Git repository and included here as a submodule:

- **api-gateway**: Handles routing, authentication, and rate limiting. Proxies requests to other services.
  - Repository: https://github.com/BasithDev/cinerate-api-gateway.git

- **user-service**: Manages user registration, login, and profile operations.
  - Repository: https://github.com/BasithDev/cinerate-user-service.git

- **review-service**: Handles user reviews for movies and TV shows.
  - Repository: https://github.com/BasithDev/cinerate-review-service.git

- **watchlist-service**: Manages user watchlists.
  - Repository: https://github.com/BasithDev/cinerate-watchlist-service.git

- **Cinerate-Module Federation**: Manages user watchlists.
  - Repository: https://github.com/BasithDev/cine_rate_mfe.git

## Development

### Prerequisites
- Node.js (v18 recommended)
- MongoDB (for user, review, and watchlist services)
- Docker (for building images)

### Running Locally

#### Clone with Submodules
To clone this repository with all submodules:
```sh
git clone --recurse-submodules https://github.com/BasithDev/cinerate_services.git
```

If you've already cloned the repository without submodules, initialize them with:
```sh
git submodule update --init --recursive
```

#### Starting Services
Each service can be started individually:
```sh
cd <service-name>
npm install
npm start
```

Or use the provided `start.sh` script to start all services in new terminals.

### Running Tests
Each service has its own test suite:
```sh
cd <service-name>
npm test
```

## Docker
Each service contains a `Dockerfile` for containerization. Build and run images as follows:
```sh
docker build -t <your-username>/cinerate-<service-name>:latest <service-name>
docker run -p <host-port>:<container-port> <your-username>/cinerate-<service-name>:latest
```

## CI/CD
- **CI**: Automated tests run on every push/pull request via GitHub Actions (`.github/workflows/ci.yml`).
- **CD**: Docker images are built and pushed to Docker Hub on every push to `main` (`.github/workflows/cd.yml`).

## Kubernetes
Kubernetes manifests for each service are in the `k8s/` directory.
Apply all resources:
```sh
kubectl apply -f k8s/
```

## Environment Variables
- Each service may require environment variables (see `.env` or code for details).
- For production, set secrets (e.g., JWT_SECRET) securely.

## Contributing
Pull requests are welcome! For major changes, please open an issue first to discuss what you would like to change.

## License
MIT
