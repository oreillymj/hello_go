# H1 hello_go
A sample hello world Web app written in Go.
The repo also contains a docker file to build lightweight image based on Alpine. 

The repo also has sample Helm charts for deployment in Kubernetes primarily to test ArgoCD.

If you just want to build the go app then

cd into cmd the use **go run** or **go build**

You can also build locally and test using docker desktop as follows.

# H2 Building a docker image to run as a container
If you want to build the go app as a docker image use

docker build -t hello-go .

Run the container, bound to port 8180: docker run --name hello-go --rm -p 8180:8180 hello-go
This will start a webserver on port 8180. In a web browser, access http://localhost:8180/testing. If everything's working, you should receive a 200 OK response, and the content:

Hello, you've requested: /testing

# H2 Deploying into a local Kubernetes install
Docker desktop also installs Kubernetes. Assuming you have an image called hello-go in docker desktop, run the following.

kubectl create deployment hello-go --image=hello-go

kubectl get deployment hello-go

kubectl get pods

You need to edit the imagepullpolicy as follows. Assumming that VS code is installed you can use it as a Kubernetes YAML editor, by running this command..
set KUBE_EDITOR=code -w

Then edit the deployment
kubectl edit deployment hello-go   (Change imagePullPolicy: Always to IfNotPresent)

** Then expose the deployment on an external port **
kubectl expose deployment hello-go --type=LoadBalancer --port=8180

kubectl get service hello-go
NAME       TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
hello-go   LoadBalancer   10.106.196.120   localhost     8180:30389/TCP   4d22h

The go webservice should be accessible as before in a web browser.
http://10.97.246.2:8180/test

The commands below will give you additional information about your Kubernetes deployment.
kubectl logs -l app=hello-go

kubectl scale deployments/hello-go --replicas=4

kubectl describe service hello-go

# H2 Building the image in Google Cloud Console and storing to the cloud repo.
Ensure you have followed the guide to setup up the container registry api in your Google cloud account - https://cloud.google.com/container-registry/docs/quickstart
Then open a **cloud shell**
In the example below, I created a project in Google cloud services called **argo-demo**

git clone https://github.com/oreillymj/hello_go.git
cd hello-go
docker build -t hello-go .
docker inspect hello-go
docker tag hello-go gcr.io/argo-demo-314410/hello-go:latest

docker push gcr.io/argo-demo-314410/hello-go:latest

# H2 Argo CD setup
In this example the docker image is deployed in ArgoCD from here - https://gcr.io/argo-demo-314410/hello-go




# H2 Credits
The original app is based on an example by Jeff Geerling here - https://github.com/geerlingguy/ansible-for-kubernetes/tree/master/hello-go
