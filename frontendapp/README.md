# In order to create first k8s operator I have executed following commands

## Scaffolding an Operator with kubebuilder
Initialized the go module

```
go mod init frontendapp
```

We are using kubebuilder to build Kubernetes Operator a framework which can help us to generate lot of scaffholded code.

```
kubebuilder init --domain stickers.com
```

Now, operator project is created for us to work upon furthur.

`cmd/main.go` is the main entry point for the operator, It contains the initialization code for the kubernetes API server and the logic for starting and running the server.

`Dockerfile` to build the k8s operator image.

`Makefile` is the build automation tool that defines a set of rules for building and compiling the application.

`PROJECT` file contains metadata about the project.


`go.sum` contains the checksums for the Go modules used by the project.

`config` folder contains the configuration files for the kubernetes API resources that will be defined by the project.

`hack` folder contains scripts & tools used during the build and deployment process.



As part of sample project we are creating a kubernetes operator which will manage deployment & service together.
We need to execute following command

```
kubebuilder create api --group <group-name> --version <version> --kind <kind-name>
```

For current sample application we have executed below command.
```
kubebuilder create api --group frontend --version v1 --kind MyPythonApp
```

`api/v1` contains the API definitions for the kubernetes custom resource that we will be defining.

 `bin` folder contains the compiled binary for the application.

 `internal\controller` folder contains the implementation of the kubernetes controllers for the custom resources defined in the `api` folder. Controllers are responsible for implementing the business logic for custom resources


## Creating the Operator's CR Type with Kubebuilder

 Kindly refer file `mypythonapp_types.go` present under `api/vi` folder

#### Defining the structure of the Custom Resource

1. package `v1` line refers to the package name in which the API types for this version of the custom resource are defined.
2. `MyPythonAppSpec` struct used define the specification or desired state of the `MyPythonApp` resource.
3. `MyPythonAppStatus` struct defines the status or observed state of the `MyPythonApp` resource.
4. `MyPythonApp` struct defines the main `MyPythonApp` resource type.
5. markers used to generate kubernetes API objects and enables subresource support.
```
//+kubebuilder:object:root=true
//+kubebuilder:subresource:status
```
6. `MyPythonAppList` struct defines a list type for the `MyPythonApp` resource.
7. `func init()` used to registers the `MyPythonApp` and `MyPythonAppList` types with kubernetes API machinery.



After defining the Custom Resource run the following command to generate some more boiler code as per your CR definition. It generates CRDs, Utility Code, Controller code etc.
```
make generate
```

After this execute following command to generate CRD manifests

```
make manifests
```

Above command will update the CRDs file with the structure we have defined above refer `frontendapp/config/crd/bases/frontend.stickers.com_mypythonapps.yaml`


## Implementing Operator's Business Logic

We need to edit controller file to implement our business logic. Refer `frontendapp/internal/controller/mypythonapp_controller.go`.

Notes about controller.

1. `package` declaration indicates that this code belongs to the `controller` package.
2. Importing required available packages.
3. `MyPythonAppReconciler` struct defines the controller that reconciles the `MyPythonApp` CRD.
4. `+kubebuilder:rbac` derivatives define the role-based access control (RBAC) rules for the controller.
5. `Reconcile` function is the main logic of the controller.
    <ul>
    <li> Purpose is to compare the desired state of the "MyPythonApp" object with the actual state of the kubernetes cluster.
    </li>
    <li>Perform operations to make the two states consistent.</li>
    </ul>
6. `SetupWithManager` function used to sets up the controller with the kubernetes manager, which is responsible for running the controller in the kubernetes cluster.


For updating controller refer `pseudo-code` present under location `frontendapp/pseudo-code`


## Configuring and Deploying the Operator.

1. Make sure kubernetes cluster is up & running. For testing we have used `minikube`
2. Execute `make install run` command.
3. To validate CRD creation in new terminal run `kubectl get crd`
4. Have the sample config ready you can refer file `frontendapp/config/samples/frontend_v1_mypythonapp.yaml`
5. Run `kubectl apply -f config/samples/frontend_v1_mypythonapp.yaml`




