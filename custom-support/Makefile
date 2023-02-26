# Define Variables
DOCKER_REGISTRY := "kangdroid"
MONGODB_OPERATOR_VERSION := "mongodb-kubernetes-operator:v0.7.8"
MONGODB_AGENT_VERSION := "mongodb-agent:12.0.15.7646-1"
MONGODB_READINESS_PROBE_VERSION := "mongodb-kubernetes-readinessprobe:1.0.12"
MONGODB_VERSION_HOOK := "mongodb-kubernetes-operator-version-upgrade-post-start-hook:1.0.6"

operator-image: ## Build and push the operator image
	docker buildx build --platform linux/arm64,linux/amd64 -t $(DOCKER_REGISTRY)/$(MONGODB_OPERATOR_VERSION) --push ./operator -f operator/Dockerfile --no-cache

agent-image: ## Build and push agent image
	docker buildx build --platform linux/arm64,linux/amd64 -t $(DOCKER_REGISTRY)/$(MONGODB_AGENT_VERSION) --push ./agent -f agent/Dockerfile

readiness-probe-image: ## Build and push readiness probe image
	docker buildx build --platform linux/arm64,linux/amd64 -t $(DOCKER_REGISTRY)/$(MONGODB_READINESS_PROBE_VERSION) --push ./readiness-probe -f readiness-probe/Dockerfile

version-upgrade-post-start-hook-image: ## Build and push version upgrade post start hook image
	docker buildx build --platform linux/arm64,linux/amd64 -t $(DOCKER_REGISTRY)/$(MONGODB_VERSION_HOOK) --push ./version-post-start-hook -f version-post-start-hook/Dockerfile

all-images: operator-image agent-image readiness-probe-image version-upgrade-post-start-hook-image