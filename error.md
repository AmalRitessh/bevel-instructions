## Troubleshooting

### Test Kubectl installation

<p align="center"><img src="./errors/1.jpg"> </p>

- This error is due to wrong configuration of `config` file in `project/bevel/build/config`. The `server:` IP address and port number might be incorrect, or the paths to `certificate-authority:`, `client-certificate:` and `client-key:` might be incorrect. Follow 6th steps in [Deploying a DLT network on Minikube using Bevel](https://github.com/AmalRitessh/bevel-instructions/blob/main/README.md#deploying-a-dlt-network-on-minikube-using-bevel) to resolve the problem.
