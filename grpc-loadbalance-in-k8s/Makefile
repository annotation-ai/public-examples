PROFILE_NAME=grpc

cluster:
	minikube start driver=docker --profile=$(PROFILE_NAME)
	eval $(minikube docker-env)  # for using host docker images

tunnel:
	minikube tunnel -p $(PROFILE_NAME)

.PHONY: grpc-server
grpc-server:
	minikube image build -t grpc-server:latest -f grpc-server/Dockerfile . -p $(PROFILE_NAME)
	kubectl apply -f charts/grpc-server/grpc_server.yaml

traefik:
	helm repo add traefik https://helm.traefik.io/traefik
	helm dependency build charts/traefik
	helm install traefik charts/traefik

request-to-l7:
	cd grpc-client && go run main.go -addr localhost:50051

request-to-l4:
	cd grpc-client && go run grpc-client/main.go -addr localhost:50052

