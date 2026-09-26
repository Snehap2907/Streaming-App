StreamingApp/
├── .github/
├── frontend/
│   ├── Dockerfile
│   └── ...
├── backend/
│   ├── Dockerfile
│   └── ...
├── helm/
│   └── mern-app/
│       ├── Chart.yaml
│       ├── values.yaml
│       └── templates/
│           ├── deployment-frontend.yaml
│           ├── deployment-backend.yaml
│           ├── service-frontend.yaml
│           └── service-backend.yaml
├── Jenkinsfile
└── README.md

System Architecture Diagram:
+------------------+         +-------------------+         +--------------------+
  |  GitHub Commit   | ------> |  Jenkins CI/CD    | ------> |     AWS ECR        |
  |  (StreamingApp)  |         |  Pipeline Build   |         | (Frontend/Backend) |
  +------------------+         +-------------------+         +--------------------+
                                                                        |
                                                                        v
  +-----------------------------------------------------------------------------------+
  |                                 Amazon EKS Cluster                                |
  |                                                                                   |
  |   +----------------------------------+     +----------------------------------+   |
  |   | Frontend Pods (Nginx / React)    |     | Backend Pods (Node.js API)       |   |
  |   | ReplicaCount: 2                  |     | ReplicaCount: 2                  |   |
  |   +----------------------------------+     +----------------------------------+   |
  |                    ^                                        ^                     |
  |                    |                                        |                     |
  |           [ LoadBalancer Service ]                  [ ClusterIP Service ]         |
  +-----------------------------------------------------------------------------------+
                                       |
                                       v
                        +----------------------------+
                        | AWS CloudWatch Metrics &   |
                        | ContainerInsights Logging  |
                        +----------------------------+

Screenshot as below:


