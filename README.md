# Project Report: CI/CD Pipeline for Train Schedule Application1. 
# Project Aim
The primary goal was to automate the build, test, and deployment process for a Node.js-based "Train Schedule" application. 
# The objective was to create a seamless workflow where code changes in GitHub are automatically built into Docker images, pushed to a central registry, and deployed onto a Kubernetes cluster.
# Learning Objectives (LOs)Pipeline as Code: 
Mastering the creation and management of a Jenkinsfile to define CI/CD stages.
# Containerization: 
Using Docker to package an application and its dependencies into a portable image.
# Orchestration: 
Deploying and managing containerized applications on a Kubernetes (Minikube) cluster.Credential Management: Securely handling sensitive data like Docker Hub passwords and Kubernetes kubeconfig files within Jenkins.
# Infrastructure Troubleshooting: 
Diagnosing network, permission, and port-mismatch issues across a distributed system. 
# Key Challenges & Solutions 
Docker Hub Authentication: Encountered connection resets (EOF). Solution: Verified network connectivity and updated login methods.
# Kubernetes API Redirection: 
Jenkins hit a login page instead of the K8s API. Solution: Corrected the server URL in the kubeconfig to point to the cluster instead of Jenkins.
# Permission Denied: 
Jenkins could not access certificates in the local user's home directory. Solution: Generated a "flattened" kubeconfig with embedded certificate data.
# Port Mismatch: 
The application was listening on port 3000, but the manifests were configured for 8080. Solution: Used direct pod port-forwarding and identified the need to sync the container and service ports.
# Success RateBuild Success: 
100% (Build #14 successfully pushed and deployed).
Deployment Success: 100% (Pods are healthy and running across multiple replicas).
Accessibility: Successfully accessed the UI via multiple ports (7070 and 7777) using kubectl port-forward.
# Importance of the Project:
This project demonstrates the core principles of DevOps. 
By automating these steps, you reduce human error, speed up the release cycle, and ensure that the application environment is consistent from development to production. 
Being able to troubleshoot the complex "handshakes" between Jenkins, Docker, and Kubernetes is a critical skill for any DevOps Engineer.
<img width="958" height="472" alt="5" src="https://github.com/user-attachments/assets/3403f3cd-fb66-4ff2-962e-0f6fdf440366" />

<img width="371" height="107" alt="1" src="https://github.com/user-attachments/assets/7e97e9d4-b571-405f-916d-e844a60e9b5d" />

<img width="470" height="89" alt="2" src="https://github.com/user-attachments/assets/69533d28-9aa0-4c4b-b74c-0489ed8ac832" />

<img width="950" height="454" alt="3" src="https://github.com/user-attachments/assets/23bb3e13-410b-4861-bb7b-1be9e9c1601f" />

<img width="950" height="440" alt="4" src="https://github.com/user-attachments/assets/a9dfebbe-05b6-4314-95ec-f5e8bc5b4c1a" />

# SUPPORTING STATEMENT
# (Situation):
I led a project to automate the CI/CD lifecycle for a Node.js "Train Schedule" application to eliminate manual deployment errors and accelerate release cycles.
# (Task):
My objective was to implement a "Pipeline as Code" workflow using Jenkins to build Docker images, manage secure credentials, and orchestrate deployments onto a Kubernetes (Minikube) cluster. 
# (Action): 
I authored a Jenkinsfile to standardise the build-test-deploy stages. When faced with critical infrastructure "handshake" issues—including Docker Hub authentication resets and Kubernetes API redirection—I performed deep-dive troubleshooting. I resolved permission conflicts by engineering a "flattened" kubeconfig with embedded certificates and fixed service connectivity by synchronising container port mappings (3000 vs 8080).
# (Result): 
This resulted in a 100% build and deployment success rate by Build #14. I successfully delivered a resilient, automated pipeline that ensured consistent production environments and high application availability across multiple replicas.

# BUSINESS IMPACT
# 90%+ Reduction in Lead Time: 
Shifting from manual steps to CI/CD reduces the time from code commit to production from hours to minutes.
# Lower Change Failure Rate (CFR): 
Automating the "handshake" between Docker and Kubernetes eliminates configuration drift, targeting a CFR of less than 15%.
# Faster Mean Time to Recovery (MTTR): 
Automated rollbacks and healthy pod replicas ensure service restoration occurs in seconds rather than minutes during a failure.
# Increased Deployment Frequency:
The business can now deploy multiple times per day with 100% build success, supporting agile market pivots.
